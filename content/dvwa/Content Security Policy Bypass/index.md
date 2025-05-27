---
title: "Content Security Policy"
date: 2025-02-13T16:06:04+01:00
draft: false
tags: ["Caido", "Security", "Web",]
categories: ["Hacking"]
description: "Explore how Content Security Policy (CSP) works to prevent XSS attacks, and learn common pitfalls and bypass techniques using DVWA. This guide covers practical examples of CSP misconfigurations, content-type issues, and nonce-based script execution, with hands-on analysis and screenshots."
---

CSP is a browser security mechanism that aims to mitigate XSS and some other attacks. It works by restricting the resources (such as scripts and images) that a page can load and restricting whether a page can be framed by other pages.

To enable CSP, a response needs to include an HTTP response header called Content-Security-Policy with a value containing the policy. The policy itself consists of one or more directives, separated by semicolons. 

https://portswigger.net/web-security/cross-site-scripting/content-security-policy

**Low:**

![](1.png)

- https://digi.ninja/dvwa/alert.js
- https://digi.ninja/dvwa/alert.txt
- https://digi.ninja/dvwa/cookie.js
- https://digi.ninja/dvwa/forced_download.js
- https://digi.ninja/dvwa/wrong_content_type.js

![](2.png)

Pretend these are on a server like Pastebin and try to work out why some work and some do not work. Check the help for an explanation if you get stuck.

If incorrectly implemented it can lead to CSP vulnerabilities.

The first one works

![](3.png)

The second in the list didn't seem to work
	This one is a txt file

![](4.png)

https://digi.ninja/dvwa/cookie.js

![](5.png)

This one obviously worked


Nothing seemed to happen for this one.

![](6.png)

![](7.png)

![](8.png)

This one puzzled me, I tried to find the file on the server but found nothing. 


This next one also didn't download or execute 

![](9.png)

Results: 

`alert.js` - Will work, this is a normal JavaScript file served with the correct headers.

`alert.txt` - This will not work as it has the wrong content type set by the web server due to its file extension.

`cookie.js` - This will work and will show your cookies

`forced_download.js` - As the name says, the server sets the "Content-Disposition: attachment" header for this to force the browser to download it rather than execute it.

`wrong_content_type.js` - This will not work as the web server ignores the file extension and forces the content type to get set as "plain/text" which prevents the browser executing it.

I tried to get the content_download.js file to download with no luck
Attempts were made on my firefox(macos), firefox(kali), chromium(kali), all failed. If you go to the url you will be forced to download.

![](10.png)

![](11.png)


We can create a file with `alert(1)` and upload it into the database using the file uploads tab. Then we enter the file path as the url

`http://192.168.10.123/dvwa/uploads/2csp`

![](12.png)

**Medium**

![](13.png)

`<script nonce="TmV2ZXIgZ29pbmcgdG8gZ2l2ZSB5b3UgdXA=">alert(1)</script>`
 
They're showing us how to bypass the mitigation. 
 
![](14.png)

Not sure why I didn't need the url in the one above


https://rentry.co/xsdasdasd

![](15.png)

**High**

Simply replace the "solve sum"  JS code with the desired JS and you will get a favourable response.

![](16.png)

![](17.png)

![](18.png)

![](19.png)