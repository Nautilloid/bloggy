---
title: "OWASP Juice-shop 2 star challenges"
date: 2025-09-03T12:00:04+01:00
draft: false
tags: ["owasp", "caido", "inspect", "Web", "hacking", "juice-shop"]
categories: ["Tool Setup"]
description: "The second in a series of guides showing web application hacking the OWASP Juice-shop. This is beginner/intermediate level and shows basic nagivation of sites, files, looking into source code and an introduction to Caido"
---

**Reflected XSS**

`<iframe src="javascript:alert(`xss`)">`

Go through the whole order process. Add to cart, add credit card and address. Then we can track our order.


![](Screenshot%202025-09-02%20at%2016.47.42.png)

Replace the id string with the javascript. 

![](Screenshot%202025-09-02%20at%2016.47.08.png)

----------


**Exposed Credentials**


	A developer was careless with hardcoding unused, but still valid credentials for a testing account on the client-side.

After a little think, I wondered if the testing account used the @juice-sh.op email.

![](Screenshot%202025-09-03%20at%2016.09.59.png)![](Screenshot%202025-09-03%20at%2016.12.30.png)

-------------

 **Login Admin**

	Log in with the administrator's user account.

Here is some helpful SQL command injection lists
https://github.com/payloadbox/sql-injection-payload-list

' OR 1 = 1 -- -
' OR '2' = '2';

- The ' is breaking out of the string 
- OR is a logic operator and will return a result if the conditions are true
- 1=1 is statement that is always true
 - -- - this will comment out a syntax error
- The ; is a standard SQL terminator

http://owasp.org/www-community/attacks/SQL_Injection

![](Screenshot%202025-09-03%20at%2019.51.30.png)


-------------

**Admin Section**

	Access the administration section of the store.

This one was quire straight forward. I was trying a lot of ways to use admin then decided to search for admin in the inspect-debugger. Then I found the 'path' 'administration' reference. 


![](Screenshot%202025-09-04%20at%2008.55.29.png)


![](Screenshot%202025-09-04%20at%2008.55.03.png)

![](Screenshot%202025-09-04%20at%2008.54.48.png)

![](Screenshot%202025-09-04%20at%2009.55.06.png)

---------

**Password Strength**

	Log in with the administrator's user credentials without previously changing them or applying SQL Injection.

I tried admin@juice-sh.op and a few random easy passwords. Then I decided to use Caido automate to brute force the password. 

Attempt to login

![](Screenshot%202025-09-04%20at%2009.56.42.png)

Then send Post request to Automate in Caido

![](Screenshot%202025-09-04%20at%2009.56.21.png)

Add a placeholder and select the list you'd like to use. 

![](Screenshot%202025-09-04%20at%2010.03.28.png)

![](Screenshot%202025-09-04%20at%2010.05.17.png)

 You don't need to use the rockyou.txt list. 
 ![](Screenshot%202025-09-04%20at%2010.06.37.png)

----------


**View Basket**

	View another user's shopping basket.	

You can do this a couple of ways:

Caido

![](Screenshot%202025-09-04%20at%2012.14.59.png)


![](Screenshot%202025-09-04%20at%2011.40.05.png)![](Screenshot%202025-09-04%20at%2012.12.50.png)

Changing the Get request from GET /rest/basket/1 HTTP/1.1 to GET /rest/basket/2 HTTP/1.1 using Caido-replay 

Or you can do it using inspect-network, right click on the 
![](Screenshot%202025-09-04%20at%2012.22.19.png)





![](Screenshot%202025-09-04%20at%2012.21.23.png)

Have a look in the response tab

![](Screenshot%202025-09-04%20at%2012.24.03.png)

------------

**Deprecated Interface**

	Use a deprecated B2B interface that was not properly shut down. 

![](Screenshot%202025-09-04%20at%2014.06.20.png)
I couldn't do this one. I was look for an old version of the application. 

Turns out it was an deprecated file format. 

If we search for zip in the debugging tap we find that there is a few old file types supported. 
yaml and xml

![](Screenshot%202025-09-04%20at%2014.09.26.png)


![](Screenshot%202025-09-04%20at%2014.11.37.png)

![](Screenshot%202025-09-04%20at%2014.07.40.png)

![](Screenshot%202025-09-04%20at%2014.12.14.png)

----------

**Empty User Registration**

	Register a user with an empty email and password.

I registered a new user. 

![](Screenshot%202025-09-04%20at%2015.19.31.png)

Then I changed the the user and password input to blank, but it said it was invalid.

![](Screenshot%202025-09-04%20at%2015.21.50.png)

So I put in a couple of spaces....


![](Screenshot%202025-09-04%20at%2015.23.12.png)

Not sure really what I did. Thought it had failed. 

![](Screenshot%202025-09-04%20at%2015.24.11.png)
