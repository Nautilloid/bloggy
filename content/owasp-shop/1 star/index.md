---
title: "OWASP Juice-shop 1 star challenges"
date: 2025-09-01T16:06:04+01:00
draft: false
tags: ["owasp", "caido", "inspect", "Web", "hacking", "juice-shop"]
categories: ["Tool Setup"]
description: "The first in a series of guides showing web application hacking the OWASP Juice-shop. This is beginner level and shows basic nagivation of sites, files, looking into source code and an introduction to Caido"
---

We'll start with the easy ones:

**Scoreboard**

Right click on the page and select inspect. Now search through the html code for a reference to "score" or "scoreboard"

![](Screenshot%202025-09-01%20at%2013.49.09.png)

We found a ref to: routerlink="score-board"

if we put that into out url:
192.168.10.132/#/score-board/

![](Screenshot%202025-09-01%20at%2013.24.20.png)


![](Screenshot%202025-09-01%20at%2018.57.51.png)

![](Screenshot%202025-09-01%20at%2019.18.26.png)

---------------


**Bonus Payload**

In this Cross Site Scripting attack(XSS), we need to modify the DOM(document object model) by adding the element(iframe).

`<iframe width="100%" height="166" scrolling="no" frameborder="no" allow="autoplay" src="https://w.soundcloud.com/player/?url=https%3A//api.soundcloud.com/tracks/771984076&color=%23ff5500&auto_play=true&hide_related=false&show_comments=true&show_user=true&show_reposts=false&show_teaser=true"></iframe>`

https://owasp.org/www-community/attacks/DOM_Based_XSS

![](Screenshot%202025-09-01%20at%2014.12.37.png)

![](Screenshot%202025-09-01%20at%2019.36.00.png)

![](Screenshot%202025-09-01%20at%2019.36.40.png)

----------------


DOM XSS

Again, copy and paste the script from the scoreboard into the search bar. 
Using the element(iframe) to interact with the object in the DOM. 
 `<iframe src="javascript:alert(`xss`)">

![](Screenshot%202025-09-01%20at%2017.40.23.png)

![](Screenshot%202025-09-01%20at%2017.39.21.png)

![](Screenshot%202025-09-01%20at%2017.49.11.png)

Here you can see that the javascript has been permanantly added to the file, if you reload the page the alert will be shown. 


![](Screenshot%202025-09-01%20at%2019.23.20.png)

![](Screenshot%202025-09-01%20at%2019.25.39.png)

----------------


**Privacy Policy**

Create an account, under the account menu youll find the policy.

![](Screenshot%202025-09-01%20at%2018.11.46.png)


Bully Chatbot

Very straightforward, just ask for a token

![](Screenshot%202025-09-01%20at%2018.23.01.png)![](Screenshot%202025-09-01%20at%2018.23.38.png)

----------------


**Confidential Document**


![](Screenshot%202025-09-01%20at%2018.37.29.png)

I found this via the http://192.168.10.132:3000/ftp/legal.md page. 

I removed the legal.md which revealed a ftp menu:

![](Screenshot%202025-09-01%20at%2018.38.38.png)

![](Screenshot%202025-09-01%20at%2019.40.10.png)

![](Screenshot%202025-09-01%20at%2019.42.39.png)

-------------


**Error Handling** 

Not sure how this one is solved. 

![](Screenshot%202025-09-01%20at%2018.40.44.png)

---------------


**Sensitive Data Exposure**


I found a reference to support/logs while reading through the coding challenge. 

![](Screenshot%202025-09-01%20at%2019.37.59.png)

![](Screenshot%202025-09-01%20at%2019.57.05.png)

For the coding challenge I was looking for a path/routerlink

![](Screenshot%202025-09-02%20at%2006.35.56.png)

Still need access to the panel for admin

![](Screenshot%202025-09-02%20at%2006.37.09.png)

----------------


**Mass Dispel**

I was looking in the main.js file for keywords: challenge solved, notifications. I came across a closeNotification function, but I ignored it. After looking at a walkthrough I realised I'd missed it. 

![](Screenshot%202025-09-02%20at%2008.54.42.png)

To solve the challenge search "shift" then I held down r and shift and clicked the x button on the Challenge solved notification. 

-------------------


**Missing Encoding**

Open the photo wall page and inspect. There is a cat image not loading with the other images. I tried to open the link shown via assets/public blablabla. 

![](Screenshot%202025-09-02%20at%2006.55.32.png)

I got nowhere so I took the filename and dropped it into a url encoder:
https://www.urlencoder.org/

![](Screenshot%202025-09-02%20at%2006.58.41.png)

![](Screenshot%202025-09-02%20at%2006.59.34.png)

The picture is revealed once you input the encoded filename: 

-----------------


**Outdated Allowlist**

Let us redirect you to one of our crypto currency addresses which are not promoted any longer.
	Redirect is the key here. Looking for a outdated crypto wallet and redirect. 

Search: redirect 

![](Screenshot%202025-09-02%20at%2008.08.08.png)

Bit easier to read:

![](Screenshot%202025-09-02%20at%2008.13.03.png)

 add the url:
	 192.168.10.132:3000/./redirect?to=https://etherscan.io/address/0x0f933ab9fcaaa782d0279c300d73750e1311eae6

![](Screenshot%202025-09-02%20at%2008.09.38.png)

![](Screenshot%202025-09-02%20at%2008.37.39.png)

Removing these 

![](Screenshot%202025-09-02%20at%2008.38.21.png)

---------


**Repetitive Registration**


![](Screenshot%202025-09-02%20at%2009.32.45.png)

Fill out the form. Before you register, change the first password input and it will still register

--------------


**Broken Access Control** 

Serach in the main.js file for: sandbox or web3

![](Screenshot%202025-09-02%20at%2009.37.23.png)

![](Screenshot%202025-09-02%20at%2009.41.01.png)

![](Screenshot%202025-09-02%20at%2009.42.54.png)

![](Screenshot%202025-09-02%20at%2009.43.44.png)

--------------


**Zero Stars**

Give a devastating zero-star feedback to the store.

Fill out the form normally and send it to Caido.

![](Screenshot%202025-09-02%20at%2012.57.17.png)

Send it to `replay` (ctrl:r) 
	Change 5 to 0

![](Screenshot%202025-09-02%20at%2012.59.37.png)

Thats it. 