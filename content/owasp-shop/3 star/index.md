---
title: "OWASP Juice-shop  3star challenges"
date: 2025-09-18T12:00:04+01:00
draft: false
tags: ["owasp", "caido", "sqlmap", "Web", "hacking", "juice-shop", "sqlite", "api"]
categories: ["Pentesting"]
description: "This guide provides step-by-step solutions for the intermediate (3-star) challenges in OWASP Juice Shop. It covers techniques such as SQL injection, XSS, CSRF, privilege escalation, and more, using practical tools like Caido and sqlmap. Each section includes screenshots and explanations to help users understand the exploitation process and learn web application security"
---

**Forged Feedback**

	Post some feedback in another user's name.

Sign into any account and submit some feedback

![](Screenshot%202025-09-10%20at%2010.18.39.png)

Find your POST request and send it to Caido's repeater. 

![](Screenshot%202025-09-10%20at%2010.19.21.png)

Change the UserID to another number. 

![](Screenshot%202025-09-10%20at%2010.21.01.png)
🎉

----------------

**Login Jim**

	Log in with Jim's user account.

Use sql injection to solve this one. 

![](Screenshot%202025-09-10%20at%2015.57.45.png)

![](Screenshot%202025-09-10%20at%2016.02.11.png)



--------------


**Login Bender**

	Log in with Bender's user account.

I just used the same injection `bender@juice-sh.op';`

![](Screenshot%202025-09-10%20at%2017.52.07.png)

![](Screenshot%202025-09-10%20at%2016.34.42.png)

![](Screenshot%202025-09-10%20at%2016.33.54.png)

----------------

**API-only XSS**

	Perform a persisted XSS attack with `<iframe src="javascript:alert(`xss`)">` without using the frontend application at all.

This one had me a little stumped because you need to change the script otherwise you will get  a 500 server error. I tried to encode the script, put it in different sections. 

The first one is uses the java script provided. 

![](Screenshot%202025-09-11%20at%2012.05.07.png)

This one introduces backslashes. 
Backslash is used to 'escape the character, meaning the the following character is treated like a literal character rather than a special character. 

`<iframe src=\"javascript:alert(`xss`)\"> 

![](Screenshot%202025-09-11%20at%2012.04.40.png)

You can also alter the script and use 'single' quotes instead of "double"


![](Screenshot%202025-09-11%20at%2012.07.33.png)

![](Screenshot%202025-09-11%20at%2012.30.33.png)

![](Screenshot%202025-09-11%20at%2012.32.15.png)

---------------

**Admin Registration**

	Register as a user with administrator privileges.


When creating a new account the PUT request is being sent to '/api/users'. The default role is 'customer' but we need to find out what the administrator role is called. 

Inside the JSON web token for admin@juice-sp.op we find: role: admin

![](Screenshot%202025-09-11%20at%2016.19.27.png)

Using Caido's intercept function, when adding a new user we can also add our new role.

![](Screenshot%202025-09-11%20at%2016.17.32.png)

![](Screenshot%202025-09-11%20at%2016.26.51.png)


Bjoern's Favorite Pet

	Reset the password of Bjoern's OWASP account via the Forgot Password mechanism with the original answer to his security question.


First find out his username/email, which you should already have from the admin account. 
bjoern@owasp.org

Now you need to find out his pets name. 
https://www.youtube.com/watch?v=a0k465G8Zkc

Good luck.

![](Screenshot%202025-09-14%20at%2008.45.54.png)

------------

**CAPTCHA Bypass**

	Submit 10 or more customer feedbacks within 20 seconds.

Navigate to the feedback page and submit a comment. 

Find you comment POST in Caido and send it to automate.
![](Screenshot%202025-09-14%20at%2008.53.23.png)

Make a simple list. 

![](Screenshot%202025-09-14%20at%2008.54.48.png)

![](Screenshot%202025-09-14%20at%2008.50.54.png)
 ![](Screenshot%202025-09-14%20at%2008.55.29.png)
 
 ----------------

**CSRF**

*This challenge is NOT possible with up-to-date browsers. This is maybe half of what I tried.*



	Change the name of a user by performing Cross-Site Request Forgery from another origin.

 First I made a POST request to change the username.
![](Screenshot%202025-09-16%20at%2005.57.52.png)


![](Screenshot%202025-09-15%20at%2011.48.59.png)

Then I used a Caido community plugin called CSRF PoC generator. 

![](Screenshot%202025-09-15%20at%2011.45.05.png)

![](Screenshot%202025-09-15%20at%2011.46.22.png)

Pasted this into a pastie.org

![](Screenshot%202025-09-15%20at%2011.43.35.png)

And tried to use this link as my malicious website. 

Importantly, if you read the `hint` it stipulates that the challenge only accepts from this particular website(htmledit.squarefree.com). 

![](Screenshot%202025-09-15%20at%2011.58.00.png)

![](Screenshot%202025-09-15%20at%2011.59.33.png)

I tried to start chromium with flags disabling security and certi errors, but I was still getting the error. 
`chromium --disable-web-security --ignore-certificate-errors --user-data-dir=~/chromium_dev_session`

I give up. I spent a bunch of time combing through the juice-shop's server files for a 'sameSite'. 
https://web.dev/articles/samesite-cookie-recipes


-------------

**Client-side XSS Protection**

	Perform a persisted XSS attack with `<iframe src="javascript:alert(`xss`)">` bypassing a client-side security mechanism.

The code will actually be `<iframe src=\"javascript:alert(`xss`)\">`, used in the previous challenge 'API-only XSS'. 

The input that has persistence 'shown on another screen' is the email registration input field. 

![](Screenshot%202025-09-18%20at%2009.54.14.png)

Fill out the new user registration form, then use Caido's 'Intercept' function to  intercept the POST request.

![](Screenshot%202025-09-18%20at%2009.58.56.png)

![](Screenshot%202025-09-18%20at%2009.55.39.png)

This took a while for me to solve as I was spent a lot of time looking trying all the different input fields but missed this one. 

-----------

**Forged Review **

	Post a product review as another user or edit any user's existing review.

Log in as any user and fill out a review. 

![](Screenshot%202025-09-18%20at%2010.45.50.png)

Use Caido's intercept function to intercept the request.

![](Screenshot%202025-09-18%20at%2010.47.47.png)

Change the author to another user. 

![](Screenshot%202025-09-18%20at%2010.50.18.png)

![](Screenshot%202025-09-18%20at%2010.51.15.png)

----------


**Database Schema**

	Exfiltrate the entire DB schema definition via SQL Injection.

This one took me a while and I needed guidance. More than usual. 

From a previous challenge we know that you an interact with the SQL database from the search query input. 

![](Screenshot%202025-09-18%20at%2014.16.23.png)

Using Caido we find the /rest/product/search  

![](Screenshot%202025-09-18%20at%2014.16.47.png)

![](Screenshot%202025-09-18%20at%2016.25.44.png)

Running sqlmap show that the database is SQLite, common databse names are master, 
	sqlmap -u http://192.168.10.132:3000/rest/products/search?q=

![](Screenshot%202025-09-18%20at%2016.27.28.png)

The payload '), will start our query. 
/rest/products/search?q=
- ')
- UNION SELECT
- *
- FROM sqlite_schema or sqlite_master

https://sqlite.org/schematab.html
sqlite_master and sqlite_schema refer to the "schema table" that stores the schema for that database.

I stated my query as follows: 
	') UNION SELECT * FROM sqlite_schema
It doesn't work.

 After some research(watching youtube guides), I found that I needed another )
	')) UNION SELECT * FROM sqlite_schema

In Caido it looked like this. 
![](Screenshot%202025-09-18%20at%2017.04.05.png)

Still didn't work
	192.168.10.132:3000/rest/products/search?q=')) UNION SELECT * FROM sqlite_schema;

![](Screenshot%202025-09-18%20at%2017.05.22.png)

But, this is the server error we need. 

Url Encoding?

![](Screenshot%202025-09-18%20at%2017.07.17.png)

![](Screenshot%202025-09-18%20at%2017.07.47.png)


Now I need to iterate through the category list starting at the first position, 1. 
	')) UNION SELECT 1 FROM sqlite_schema
	')) UNION SELECT 1,2 FROM sqlite_schema
	')) UNION SELECT 1,2,3 FROM sqlite_schema

Until I get to:
![](Screenshot%202025-09-18%20at%2017.09.15.png)

Answer: 9 

Caido:

`/rest/products/search?q=a%27))UNION%20SELECT%20sql%20,2,3,4,5,6,7,8,9%20FROM%20sqlite_master;`

URL:

`192.168.10.132:3000/rest/products/search?q=a'))UNION SELECT sql ,2,3,4,5,6,7,8,9 FROM sqlite_master;`

