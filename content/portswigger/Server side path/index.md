---
title: "XML external entity injection XXE"
date: 2025-06-1T16:06:04+01:00
draft: false
tags: ["Security", "Apprentice", "web", "Portswigger", "Caido"]
categories: ["web", "Portswigger"]
description: "Another guide through the Apprentice-level Server Side Vulnerabilities pathway on PortSwigger. Covers practical labs and techniques for path traversal, access control, privilege escalation, and brute force attacks, with step-by-step walkthroughs and tips for using tools like Caido."
---

This is a guide for the Apprentice level Server Side Vulnerabilities pathway on Portswigger.  I'm pretty impressed with the amount of free content on this platform. It is well written and it starts out pretty easy, then leads you deeper into more complex concepts. 

Thank you Portswigger people. 

**Path Traversal**

Basic path traversal 
```
GET / image?filename=../../../etc/passwd
```

![](1.png)

![](2.png)

**Access Control**

What is access control?
Access control is the application of constraints on who or what is authorized to perform actions or access resources. In the context of web applications, access control is dependent on authentication and session management:

Authentication confirms that the user is who they say they are.
Session management identifies which subsequent HTTP requests are being made by that same user.
Access control determines whether the user is allowed to carry out the action that they are attempting to perform. -Portswigger-

maybe: 
```https://insecure-website.com/admin```
```https://insecure-website.com/robots.txt```

The robots.txt file gives us a juicy clue

![](3.png)


![](4.png)
![[Screenshot 2025-05-31 at 2.27.42 pm.png]]

![](5.png)


Next Lab:

Navigate to the a product page and then 'inspect'

**Screenshot here** 

I then searched: script

![](6.png)

![](7.png)


![](8.png)

**Parameter-based access control methods**

```https://insecure-website.com/login/home.jsp?admin=true```

```https://insecure-website.com/login/home.jsp?role=1```

This lab has an admin panel at `/admin`, which identifies administrators using a forgeable cookie.
Solve the lab by accessing the admin panel and using it to delete the user `carlos`.
You can log in to your own account using the following credentials: `wiener:peter`

log on as wiener

use Caido to find the myaccount GET request.

![](9.png)

Change false to true, Send then open the Response in a browser window:

![](10.png)

Now you should see an admin menu item.

![](11.png)

![](12.png)

If you see this then open inspect-storage and change the false to true.

![](13.png)

Reload and now you should be able to see the the delete options

![](15.png)

**Horizontal Privilege escalation**

Find a post by Carlos

![](15.png)

Click on Carlos's name 
In the URL you'll find his userID

![](16.png)

Login as wiener:peter

![](17.png)

Change the userID in the URL

![](18.png)

Submit:

![](19.png)

**Horisontal and vertical escalation**

Log in as wiener:peter
change the userID at the end of the URL to `administrator`

![](20.png)

In Caido, find the password in the response.

![](20a.png)

Use the password to login and navigate to the admin panel. 

![](20b.png)

**Brute Force**

Attempt to log in, we need a copy of the request.

![[21.png]]

In Caido, grab the POST request and send it to Automate.

Copy the contents of the username and passwords into seperate files and upload them to Caido 

![[22a.png]]

![[22.png]]

Select the username you've entered on the login page and then hit the '+' icon in the top right to add a payload. Do the same for your password. Select Matrix, this will run through both payload files checking each password for every username.  

![[23.png]]

Your results can be sorted by Status and length. Sometimes its the status's will be the same e.g 200 will be a successful attempt so you wont be able to sort via the status. Notice that a successful attempt will show a different Response length. Play with the results to find the needle in the haystack. 

![[24.png]]

