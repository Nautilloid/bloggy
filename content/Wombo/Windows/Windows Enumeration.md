
### Manual:

##### CMD
systeminfo
set
whoami 
	whoami /all
	whoami /groups
net user
	net user Ray
	net localgroup
	net accounts
icals file.txt

ifconfig
ipconfig /all
route print 
netstat -ao
	netstat -ao -p TCP

hostname
findstr /SIM /C:"pass" *.ini *.cfg *.xml
echo %PATH%
where
help dir
dir /a

###### Powershell

Get-LocalUser
Get-LocalGroupMember *administrator*
dir env:
Get-ChildItem -Path c:\users\  -Include .kdbx -File -Recursive -ErrorAction SilentlyContinue
FindStr blablabla
Get-Process
Get-ItemProperty

  nstalled apps (32 bit)
	Get-ItemProperty "HKLM:\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*" | select displayname
  

  installed apps (64 bit)
	  Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\*" | select displayname