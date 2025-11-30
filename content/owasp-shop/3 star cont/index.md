---
title: "OWASP Juice-shop  3star challenges continued"
date: 2025-12-1T00:01:04+01:00
draft: false
tags: ["owasp", "caido", "sqlmap", "Web", "hacking", "juice-shop", "sqlite", "api"]
categories: ["Pentesting"]
description: "This guide provides step-by-step solutions for the intermediate (3-star) challenges in OWASP Juice Shop. It covers techniques such as SQL injection, XSS, CSRF, privilege escalation, and more, using practical tools like Caido and sqlmap. Each section includes screenshots and explanations to help users understand the exploitation process and learn web application security"
---



**Deluxe Fraud**

	Obtain a Deluxe Membership without paying for it.


Okay so, login and find the delux membership purchase page. 

![](Screenshot%202025-09-19%20at%2007.41.02.png)

Select a credit card, turn on intercept and send the POST request to replay.

![](Screenshot%202025-09-19%20at%2007.45.06.png)

I tried a few different cards, then the wallet.

![](Screenshot%202025-09-19%20at%2007.45.33.png)

No paymentId

![](Screenshot%202025-09-19%20at%2007.46.57.png)

Then I went back and looked for other payment types.

![](Screenshot%202025-09-19%20at%2007.47.57.png)

![](Screenshot%202025-09-19%20at%2007.48.20.png)

--------------

**GDPR Data Erasure**

	Log in with Chris' erased user account.

obviously I tried chris@juice-sh.op first. 

sqlmap -u "http://192.168.10.132:3000/rest/products/search?q=" -T Users --dump

![](Screenshot%202025-09-21%20at%2009.20.54.png)


![](Screenshot%202025-09-21%20at%2009.21.57.png)

![](Screenshot%202025-09-21%20at%2009.10.37.png)![](Screenshot%202025-09-21%20at%2013.56.44.png)

I also went back to an old challenge to check if I had the creds. 

`http://192.168.10.132:3000/rest/products/search?q=apple%27))UNION%20SELECT%201%20%20,username,email,password,5,6,7,8,deletedAt%20from%20Users;`

![](Screenshot%202025-09-21%20at%2013.58.52.png)


----------

**Login Amy**

Log in with Amy's original user credentials. (This could take 93.83 billion trillion trillion centuries to brute force, but luckily she did not read the "One Important Final Note")

This little ditty was tougher than expected. I went down a rabbit hole trying to brute force the hash that was found in another challenge. 

The answer came from OSINT, googling 93.83 billion trillion trillion centuries bought you to Mr Gibson's website, a website I am quite familiar with. 

![](Screenshot%202025-09-22%20at%2020.50.34.png)

The challenge blurb refers to "One Important Final Note", which can be found at the bottom of the page and refers to creating your own password padding rules. 

![](Screenshot%202025-09-22%20at%2020.55.03.png)

![](Screenshot%202025-09-22%20at%2020.55.19.png)

Send a login request to Caido's Automator and change the password to D0g.....................

![](Screenshot%202025-09-22%20at%2021.01.26.png)

- Add a simple list of capital letters to 'D'
- Add a pimple list of numbers 0-9 to the zero
- Add a smiple list of lower case letters to the 'g' 

Change the type to: Matrix
This is much like cluster in Burpsuite and will iterate through all the payloads. 

![](Screenshot%202025-09-22%20at%2021.07.40.png)

![](Screenshot%202025-09-22%20at%2021.13.51.png)


 This sneaky cod is both brute force and OSINT 

K1f.....................

![](Screenshot%202025-09-22%20at%2021.15.01.png)

--------------

**Manipulate Basket**

	Put an additional product into another user's shopping basket.

Login into an account and send add a product then up the quantity +

Grab that PUT request in Caido and send it to reply. 


![](Screenshot%202025-09-23%20at%2005.56.42.png)

![](Screenshot%202025-09-23%20at%2006.12.43.png)

Grab that PUT request in Caido and send it to reply. 

![](Screenshot%202025-09-23%20at%2006.13.34.png)

![](Screenshot%202025-09-23%20at%2005.55.06.png)

----------------------


**Mint the Honey Pot**

	Mint the Honey Pot NFT by gathering BEEs from the bee haven.

I'm skipping this as my metamask wont link to the Juice-shop

-------------


**Payback Time**

	Place an order that makes you rich.

After adding an item to my basket, I increased it to 2.

![](Screenshot%202025-09-28%20at%2018.02.55.png)

After modifying the PUT request to -200 I paid for my goods. 

![](Screenshot%202025-09-28%20at%2018.02.01.png)

![](Screenshot%202025-09-28%20at%2018.05.15.png)

![](Screenshot%202025-09-28%20at%2018.05.38.png)

---------------

**Privacy Policy Inspection**

	Prove that you actually read our privacy policy.

Find the privacy policy at http://192.168.10.132:3000/#/privacy-security/privacy-policy
When reading through it, particular words have this hue when you hover the mouse. 

Inspector shows us that its using a element called 'hot'

![](Screenshot%202025-10-01%20at%2007.11.45.png)

Using the search function we find 7 uses of this element throughout the page.

![](Screenshot%202025-10-01%20at%2007.18.33.png)

![](Screenshot%202025-10-01%20at%2007.20.19.png)

![](Screenshot%202025-10-01%20at%2007.21.02.png)

![](Screenshot%202025-10-01%20at%2007.21.21.png)

![](Screenshot%202025-10-01%20at%2007.21.44.png)

I think this is the URL: 
http://192.168.10.132:3000/We/may/also/instruct/you/to/refuse/all/reasonably/necessary/responsibility

I'm 100% sure though, but....

![](Screenshot%202025-10-01%20at%2007.26.51.png)

--------------

**Reset Jim's Password**

	Reset Jim's password via the Forgot Password mechanism with the original answer to his security question.

![](Screenshot%202025-10-01%20at%2020.18.38.png)

Just gonna leave this here. 

-------------------

**Product Tampering**

	Change the href of the link within the OWASP SSL Advanced Forensic Tool (O-Saft) product description into https://owasp.slack.com.





------------

**Security Advisory**

	The Juice Shop is susceptible to a known vulnerability in a library, for which an advisory has already been issued, marking the Juice Shop as known affected. A fix is still pending.  Inform the shop about a suitable checksum as proof that you did your due diligence.

This one really threw me. I wasn't putting everything together properly, no reading diligently. 

I found the security.txt file but it wasn't in the .wellknown folder. 
I read it and found this: .well-known/csaf/provider-metadata.json

![](Screenshot%202025-10-05%20at%2013.10.06.png)

In theory, that should pointed me towards the CSAF folder. 

![](Screenshot%202025-10-05%20at%2013.11.35.png)

I got caught up with the wording and was attempting to enter the latest checksum SHA-512 because of this wording; *Inform the shop about a suitable checksum as proof that you did your due diligence.*

![](Screenshot%202025-10-05%20at%2013.36.25.png)

They want you to find the vulnerable "known affected" library. There are a couple of ways to do this. 

1. Inspect
 
![](Screenshot%202025-10-05%20at%2014.04.05.png)

![](Screenshot%202025-10-05%20at%2014.09.28.png)

I just wanted to see what a normal pages response would look like. 


![](Screenshot%202025-10-05%20at%2014.16.51.png)

![](Screenshot%202025-10-05%20at%2014.13.48.png)

![](Screenshot%202025-10-05%20at%2014.17.54.png)

From here we have to figure out what the .... to do. 

![](Screenshot%202025-10-05%20at%2014.27.47.png)

So what is CSAF?
https://www.csaf.io/

There are 2 specific CVE's and a general advisory specified. 

CVE-2020-36604 
	This is a vulnerable library called hoek.
![](Screenshot%202025-10-05%20at%2022.18.24.png)

The one we're after is: CVE-2020-15084
https://nvd.nist.gov/vuln/detail/CVE-2020-15084
	This vulbnerable library wont enforce authorisation unless specified. It was fixed in version 6.0

![](Screenshot%202025-10-05%20at%2022.19.04.png)

It took me a while to find this but they're asking for the hash of this file:![](Screenshot%202025-10-05%20at%2022.26.05.png)

 ![](Screenshot%202025-10-05%20at%2022.28.14.png)

To be to be input into the feedback section.

![](Screenshot%202025-10-05%20at%2022.29.00.png)

-----------------

**Product Tampering**

	Change the href of the link within the OWASP SSL Advanced Forensic Tool (O-Saft) product description into https://owasp.slack.com.

Be very careful with your syntax comrades. I just spent 3 hours fucking around because I had a extra  ";" where it shouldn't have been.

We have all the necessary information to solve this. In order to change the description we need to interact with the api using a PUT request. We know the table and can easily find the product ID. 

PUT api/Products/9

We need to add:
Content-Type: application/json

Definately not:
Content-Type: application/json**;**
	This I copy/pasted from the Response header. IT IS NOT CORRECT>>>

You can also find the ProductID from making a order.

![](Screenshot%202025-10-17%20at%2006.52.25.png)

![](Screenshot%202025-10-17%20at%2007.06.23.png)

![](Screenshot%202025-10-17%20at%2007.05.32.png)


![](Screenshot%202025-10-17%20at%2007.08.59.png)

![](Screenshot%202025-10-17%20at%2007.10.34.png)

-------------

Upload Size

	Upload a file larger than 100 kB.

I tried to upload a zip file to see what the request looks like.

![](Screenshot%202025-10-17%20at%2020.25.40.png)

I check it out, went down some rabbit holes. Then I tried a pdf. 

![](Screenshot%202025-10-17%20at%2020.26.41.png)

The pdf binary is added between the form boundry.

![](Screenshot%202025-10-17%20at%2020.28.21.png)

The API might be checking the `Content Length:` header.

I simply copied the entire content and pasted it back into the request, doubling the amount of data and bringing the overall content over the 100kb. The headers all stayed the same. Not an elegant solution but effective for passing the challenge. 

![](Screenshot%202025-10-18%20at%2008.17.47.png)

----------

**Upload Type**

	Upload a file that has no .pdf or .zip extension.

You should already have a file upload request for the previous challenge. 



![](Screenshot%202025-10-20%20at%2007.19.34.png)

![](Screenshot%202025-10-20%20at%2007.17.16.png)

----------

**XXE Data Access**

	Retrieve the content of C:\Windows\system.ini or /etc/passwd from the server.




