---
title: Algernon
date: 2026-01-27T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - proving-grounds
  - enum4linux-ng
  - searchsploit
  - python
categories:
  - Pentesting
  - Linux
  - Offsec
description: "This guide provides readers with the solution to the Proving Grounds Lab: Algernon"
---
nmap -A 

You can use metasploit for this but obviously its better not to. 

`searchsploit smarter` 

![](Screenshot%202025-12-15%20at%2007.51.31.png)

`searchsploit smarter` 

![](Screenshot%202025-12-15%20at%2008.43.50.png)

Even though the site is on port 9998 you need the leave the port as 17001 but change the HOST and LHOST

![](Screenshot%202025-12-15%20at%2008.48.17.png)

start your listener 

`rlwrap nc -lvnp 4444`

![](Screenshot%202025-12-15%20at%2008.38.58.png)

![](Screenshot%202025-12-15%20at%2008.40.58.png)

This took me 2 hours. I had the port set to 9998. I found the proof.txt file using metasploit but I knew I should be working from the script. 

