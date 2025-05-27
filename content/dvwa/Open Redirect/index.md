---
title: "Open Redirect"
date: 2025-03-21T16:06:04+01:00
draft: false
tags: ["Caido", "Redirects", "Web", "DVWA"]
categories: ["Hacking"]
description: "Learn how to identify and exploit open redirect vulnerabilities in DVWA. This guide explains how unvalidated redirects can be abused for phishing and credential theft, demonstrates practical attack techniques across different security levels, and provides tips for detection and prevention with hands-on examples and screenshots."
---

Unvalidated redirects and forwards are possible when a web application accepts untrusted input that could cause the web application to redirect the request to a URL contained within untrusted input. By modifying untrusted URL input to a malicious site, an attacker may successfully launch a phishing scam and steal user credentials.

Using the url we can redirect a user to another site.

![](1.png)

When you click the quote the server redirects you to the right page. There are 2 GET requests, one for the redirect then another one to the address requested by the redirect. 

![](2.png)

The first one is a 302 response.

![](3.png)

The second a 200.

![](4.png)


When you change the redirect address: 

![](5.png)

Take that url and add it to your browsers address bar: 

![](6.png)


![](7.png)

You can see the same thing happening, first a GET request with the redirect, then  the redirection. 

![](8.png)

**Medium:**

This time we get a 500 response. 

![](9.png)

Absolute urls are not allowed so we can change the redirect path to //google.com and it should work 

![](10.png)

![](11.png)


**High:**

We're being told that it will only redirect to the info page.

![](12.png)

It's looking for the text(info.php) in the url, we add: &test=info.php


![](13.png)


If we url encode the: &, we get our redirect response code 302

![](14.png)

![](15.png)


You could also have an malicious website that included info.php:
http://hacktheplanet.com/info.php

