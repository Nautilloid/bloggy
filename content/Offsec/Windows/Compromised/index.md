---
title: Compromised
date: 2026-01-27T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - windows
  - proving-grounds
  - smb
  - winrm
categories:
  - Pentesting
  - Offsec
  - Windows
description: "This guide provides readers with the solution to the Proving Grounds Lab: Compromised"
---
![](Screenshot%202025-12-16%20at%2008.01.14.png)![](Screenshot%202025-12-16%20at%2008.57.35.png)

![](Screenshot%202025-12-16%20at%2010.12.43.png)

smbclient \\\\192.168.184.152\\Users$

![](Screenshot%202025-12-16%20at%2010.34.27.png)
looks like scripting might be the username

![](Screenshot%202025-12-16%20at%2010.36.12.png)

I couldn't get the local.txt, but inside the readme.txt file it points to >

![](Screenshot%202025-12-16%20at%2010.37.38.png)

![](Screenshot%202025-12-16%20at%2010.41.02.png)

![](Screenshot%202025-12-16%20at%2010.38.20.png)

![](Screenshot%202025-12-16%20at%2010.38.50.png)


`evil-winrm -i 192.168.184.152 -u scripting -p FriendsDontLetFriendsBase64Passwords
`
![](Screenshot%202025-12-16%20at%2010.21.44.png)

Trying to find a way to decode this:

![](Screenshot%202025-12-16%20at%2014.28.55.png)



![](Screenshot%202025-12-16%20at%2014.39.59.png)

![](Screenshot%202025-12-16%20at%2015.24.47.png)


![](Screenshot%202025-12-16%20at%2014.40.32.png)

Linux:
	find / -iname local*

Pwershell:
	Get-ChildItem -Path C:\ -Recurse -Filter "local*" -ErrorAction SilentlyContinue

![](Screenshot%202025-12-16%20at%2015.28.25.png)