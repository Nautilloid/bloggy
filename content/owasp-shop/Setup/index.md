---
title: "OWASP Juice-shop setup guide"
date: 2025-08-08T16:06:04+01:00
draft: false
tags: ["owasp", "Proxmox", "Ubuntu", "Web", "nginx"]
categories: ["Tool Setup"]
description: "A comprehensive guide to deploying OWASP Juice Shop on an Ubuntu VM within a Proxmox environment. It details the entire process from setting a static IP address with Netplan to configuring Nginx as a reverse proxy for seamless network access. The guide also includes a dedicated troubleshooting section for common issues, ensuring readers can get the secure test environment up and running smoothly."
---
**Proxmox | Ubuntu Server | NginX | NodeJS | NPM**



- From inside your Proxmox environment, clone another vm or set up new ubuntu VM/container. 
		
		- ssh into your machine so that you can copy/paste.

- Change the IP address using Netplan and set a static IP 
 
![](Screenshot%202025-08-16%20at%2020.02.07.png)
Use vim or nano based on your network configuration, then `netplan apply` to implement the changes.
 

My one looks like this because I have a DNS setup for the subnet: 192.168.10.0/24 on 192.168.10.1

Sign in as root:
		
		`sudo su`

Update and upgrade
		
		`apt update && upgrade -y`

Install nginx
		
		`apt install nginx`

- Install curl and add the NodeSource repository:
		
		`apt install curl -y
		curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash - ` (this could change depending on the version you need)


- Install nodejs
		
		`apt install nodejs -y`

- Navigate to /var/www
		
		`cd /var/www`

- Clone the Juice-shop repository:
		
		`sudo git clone https://github.com/bkimminich/juice-shop.git`

- Install the repositories dependencies:
		
		`cd juice-shop
		npm install`

- Start the juice-shop
		
		`HOST=0.0.0.0 npm start`

It should look like this:
![](Screenshot%202025-08-16%20at%2021.39.28.png)
- Head to your favourite browser:
		
		`192.168.10.132:3000`

![](Screenshot%202025-08-16%20at%2021.40.59.png)

**Troubleshooting**

- Routing: Add route if you aren't able to connect to your vm 
- Firewall: disable and re-enable ufw to check `ufw disable` |  `ufw enable`
- Correct nodejs repo. First time I installed nodejs I didn't update the repo and it failed. 