---
title: Cobbles
date: 2026-06-23T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - proving-grounds
  - gobuster
  - docker
  - SUID
  - chmod
  - linpeas
categories:
  - Pentesting
  - Offsec
  - Linux
description: "This guide provides readers with the solution to the Proving Grounds Lab: Cobbles"
---
“There are so many different kinds of stupidity, and cleverness is one of the worst.”  
― Thomas Mann

nmap 192.168.182.214 -p- -sS -sC -sV --min-rate 5000

![](Screenshot%202026-06-25%20at%2007.18.21.png)

![](Screenshot%202026-06-25%20at%2006.50.46.png)

![](Screenshot%202026-06-25%20at%2007.17.55.png)

server-status

![](Screenshot%202026-06-25%20at%2007.17.16.png)

![](Screenshot%202026-06-25%20at%2007.49.34.png)

![](Screenshot%202026-06-25%20at%2007.48.50.png)

https://krastanoel.com/cve/2022-29806

![](Screenshot%202026-06-25%20at%2008.44.40.png)

![](Screenshot%202026-06-25%20at%2008.54.15.png)
![](Screenshot%202026-06-25%20at%2008.55.12.png)

I didn't see that there is an exploit script.... oops

https://github.com/krastanoel/exploits/blob/master/zoneminder-1.36.12-remote-code-execution/exploit.py

vim exploit.py

![](Screenshot%202026-06-25%20at%2014.17.56.png)

I had no luck with port 1234.

usage: exploit.py [-h] --rhost [RHOST] --rport [RPORT] [--uri [URI]]  
'python exploit.py --rhost 192.168.182.214 --rport 80 --uri /zm-prod/index.php`

![](Screenshot%202026-06-25%20at%2014.19.25.png)

![](Screenshot%202026-06-25%20at%2014.23.28.png)

![](Screenshot%202026-06-25%20at%2014.23.12.png)

![](Screenshot%202026-06-25%20at%2014.24.37.png)

![](Screenshot%202026-06-25%20at%2014.51.26.png)

![](Screenshot%202026-06-25%20at%2014.53.32.png)

Haproxy feels like something.

![](Screenshot%202026-06-25%20at%2016.00.27.png)

![](Screenshot%202026-06-25%20at%2016.02.02.png)

Okay, this is pretty tough, I had to look at a guide. This cfg file is saying that haproxy will route to 0.0.0.0/80 from 127.0.0.1:8081 instead of 127.0.0.1:8081 when port 80/8080 is in use. When this happens and you reconnect using the python exploit it will connect you to the fallback.

![](Screenshot%202026-06-25%20at%2016.45.55.png)

To make this work you need 2 reverse shells both on port 80. Once you have the first one(www-data) setup, wait 120 seconds and start another listener on port 80 and try again until you connect using the same exploit.py script. 

![](Screenshot%202026-06-25%20at%2016.41.03.png)

Once you have your second connection, you'll see the the zoneminder/www folders have the same files and folders. There is a link between the host and the docker container. The mount command or /proc/mount shows how these folders are linked, and means we can access the host file system from the docker container with root privs.

![](Screenshot%202026-06-26%20at%2007.17.12.png)

The next step is to copy the bash file to the www directory and change the files privs so it can be accessed by the www-data user.

Docker machine:  
`cp /bin/bash .`  
`chmod +s bash`

![](Screenshot%202026-06-26%20at%2007.12.58.png)

./bash -p

![](Screenshot%202026-06-26%20at%2007.23.36.png)

If you don't use the -p it will default to the 'effective user', and you wont get root.

![](Screenshot%202026-06-26%20at%2007.26.55.png)
https://www.man7.org/linux/man-pages//man1/bash.1.html