---
title: "OpenVAS installation && troubleshooting"
date: "2024-12-17T17:58:51+10:00"
draft: "false"
description: "Installing OpenVAS vulnerabilty scanner on Kali"
tags: ["Vulnerablilty Scanner", "Kali", "OpenVAS", "Greenbone", "Tools"]
categories: ["Tool Setup"]
description: "A step-by-step guide to installing and troubleshooting OpenVAS on Kali Linux. Learn how to set up the Greenbone vulnerability scanner, resolve common issues like PostgreSQL upgrades, and configure the service for remote access. Includes practical tips and screenshots for a smooth installation process."
---

Installing OpenVAS on Kali for free vulnerability scanner

Follow the instructions in this link:

https://greenbone.github.io/docs/latest/22.4/kali/index.html

**Troubleshooting:**

![](5.png)		

Before running the gvm-setup command from the tutorial above you need to upgrade PostgreSQL to version 17

https://secburg.com/posts/howto-upgrade-postgresql-16-to-17/

![](1.png)

![](2.png)

![](3.png)
Add: ``` ExecStart=/usr/sbin/gsad --listen=x.x.x.x --port 9392 ```

To: ```/usr/lib/systemd/system/gsad.service```

![](4.png)

```
sudo systemctl daemon-reload && sudo gvm-start
```