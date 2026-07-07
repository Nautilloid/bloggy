---
title: Debugging on Arm
date: 2026-05-06T12:00:04+01:00
draft: false
tags:
  - offsec
  - ROPgadget
  - gcc
  - qemu
  - gdb
  - pwndbg
  - amd64
  - x86
categories:
  - Pentesting
  - Offsec
description: Running through how to set up an arm based environment to do x86 binary enumeration.
---

https://wiki.archlinux.org/title/Main_page

I was really struggling with this, it started when I was trying to find windows binary buffer-overflow vulnerabilities. When reading through guides and documentation I would try and follow along and become frustrated. Because of the architecture on my macbook(M1) I was unable to find the offsets. 

This guide helped me though the install process. Additionally, there are some great troubleshooting tips.

https://www.secureideas.com/blog/running-and-debugging-non-native-elf-binaries-locally-using-qemu-binfmt-and-gdb

QEMU

```
sudo apt install qemu-user qemu-user-static -y && sudo apt install binfmt-support && sudo mkdir –p /etc/qemu-binfmt -y
```

Amd64 libraries:

```
sudo dpkg --add-architecture amd64
apt install libc6:amd64
```


Pwndbg:

https://pwndbg.re/stable/setup/#system-install
```
curl --proto '=https' --tlsv1.2 -LsSf 'https://install.pwndbg.re' | sh -s -- -t pwndbg-gdb
```

gdb-multiarch:

```
sudo apt install gdb-multiarch
``` 


Once everything is installed, start the binary using this command:

```
qemu-x86_64 -g 1234  ./your-binary
```

![](Screenshot%202026-05-07%20at%2006.51.10.png)

This starts the binary emulating the x86 architecture, assigns it to port 1234 and then waits. 

In another tab start pwndbg, set the architecture and connect it to the assigned port

```
pwndbg ./your-binary
```

`set architecture i386:x86-64 /`  
`target remote localhost:1234`

![](Screenshot%202026-05-06%20at%2010.57.10.png)

Pwndbg will print a summary of the binary when it starts, this is a nice touch. 

`disass main`

![](Screenshot%202026-05-07%20at%2007.12.28.png)

Pick a function and disassemble further:

`disass system`

![](Screenshot%202026-05-07%20at%2007.14.16.png)

----------------

Compiling binary.c for x86 architecture can be done with the x86_64-... 

gnu - linux standard library  
mingw32 - windows 32/64 bit library

![](Screenshot%202026-05-07%20at%2016.16.31.png)


