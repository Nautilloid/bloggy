---
title: Zipper
date: 2026-04-04T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - proving-grounds
  - RDP
  - 7z
  - zip
  - revshells
  - php
  - file-upload
  - cron
  - base64
categories:
  - Pentesting
  - Offsec
  - Windows
description: "This guide provides readers with the solution to the Proving Grounds Lab: Zipper"
---

“The soul becomes dyed with the colour of its thoughts.”  
― Marcus Aurelius

![](Screenshot%202026-04-08%20at%2006.38.13.png)

![](Screenshot%202026-04-08%20at%2010.06.52.png)
The http site seems to compress files for you.

![](Screenshot%202026-04-08%20at%2014.58.52.png)

Create a php reverse shell and upload it to the site:

![](Screenshot%202026-04-08%20at%2015.25.09.png)

To see the source code we can use a php base64 filter, which will be parsed first.

![](Screenshot%202026-04-10%20at%2010.12.16.png)

![](Screenshot%202026-04-10%20at%2010.12.38.png)

![](Screenshot%202026-04-10%20at%2010.13.03.png)

![](Screenshot%202026-04-10%20at%2010.13.20.png)

![](Screenshot%202026-04-10%20at%2010.16.51.png)


https://rioasmara.com/2021/07/25/php-zip-wrapper-for-rce/?source=post_page-----b49a52ed8e38---------------------------------------

![](Screenshot%202026-04-10%20at%2009.44.10.png)

Once uploaded download the file and grab the filename.

I used Caido to and Foxy Proxy to grab the file and folder name.

![](Screenshot%202026-04-10%20at%2009.46.39.png)


zip://uploads/upload_1775622633.zip%23payload

- zip://: PHP realises this is a compressed stream and triggers a 'ZipStream handler'
- uploads/upload_1775622633.zip: It locates the archive on the hard drive.
- '%23' OR '#': It stops looking for the archive and starts looking inside it.
- payload.php: It finds this file inside the ZIP and treats its contents as raw PHP code, executing whatever commands you wrote in there (like your reverse shell).


![](Screenshot%202026-04-08%20at%2015.24.21.png)

`python3 -c 'import pty; pty.spawn("/bin/bash")'`

![](Screenshot%202026-04-08%20at%2014.57.56.png)

The crontab leads to the backup script, the backup script points towards the backup.log

![](Screenshot%202026-04-09%20at%2013.36.12.png)

![](Screenshot%202026-04-09%20at%2013.36.53.png)

![](Screenshot%202026-04-09%20at%2013.35.12.png)
 ![](Screenshot%202026-04-09%20at%2013.38.38.png)