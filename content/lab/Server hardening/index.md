---
title: "Server Hardening"
date: 2025-05-27T16:06:04+01:00
draft: false
tags: ["Lab", "Security", "Hardening", "Cloudflare", "nginx", "CSP"]
categories: ["lab"]
description: "A practical guide to hardening your self-hosted blog server. Learn layered security techniques including VLAN isolation, UFW, HTTPS, Cloudflare Tunnel, and strict Content Security Policy (CSP) configuration. Includes troubleshooting tips, real-world lessons, and screenshots from a live deployment"
---

After creating a blog and releasing it into the wild I instantly became paranoid that my network would be exploited. 

I wanted to be sure that I had done everything in my power to secure the site and isolate it from my home network. 

Initially these were the techniques I used to layer seucutiy. 

- Up-to-date software, automate updates using cron
- Content-delivery-policies
-  Fail2ban(unnecessary, ssh disabled)
- UFW
- Isolated VLAN/DMZ
- http redirected to https 
- Disabled ssh

With all of this done I was still very paranoid because my public IP was exposed. Knowing whats crawling around out there I was quite uncomfortable. My site was also intermittently down  every 5 minutes- my ISP(98% sure) is using a CGNAT and self-hosting breaks that terms and services agreement. 

To further improve my sites security I am going to use a Cloudflare tunnel to hide my IP address. With this free service I can also:

- Enable 'use HTTPS only'
- Enable website analytics
- Run vulnerability scans through the Cloudflare portal
- Block countries 
- Enable HSTS 
- Minimum TLS Version


![](1.png)

![](2.png)

![](3.png)

![](4.png)

I added the new CSP to the /etc/nginx/site-availible/your-site

When I set the CSP I got some errors a the headers were too rigid and sacrificed functionaliity for security.

![](5.png)


I've altered the Policy to allow specific scripts from known URI's, I've removed Cloudflares analytics because its still gathering information from the tunnel. But there are still issues with the Subresource Integrity (SRI).

After updating the SRI hashes I was still getting a few errors.

![](6.png)

After working for at least an hour I realised ublock was still on! 

Still some work to be done.

![](7.png)

used this CSP: 
*needs to be on the same line for nginx
```
add_header Content-Security-Policy "default-src 'self'; img-src 'self'; script-src 'self' https://cdn.jsdelivr.net; style-src 'self' https://cdn.jsdelivr.net https://fonts.googleapis.com; font-src 'self' https://fonts.gstatic.com; object-src 'none'; frame-ancestors 'none'; base-uri 'self'; form-action 'self';" always;
```

Now: 

![](8.png)