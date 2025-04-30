
Use the following guide to install the package:
https://docs.caido.io/guides/#installing-on-linux

Once you've installed Caido in Kali and you have used it in your on your self hosted website. But, you get a certificate error when you go to a lab on Portswigger.

![[Screenshot 2025-02-07 at 4.06.51 pm.png]]![[Screenshot 2025-02-07 at 4.07.14 pm.png]]
When you installed Caido you have been prompted to set up a proxy server in the network settings in firefox. 

As part of the install a certificate should have been added to your 'trusted certificates'. This is fine to use for local machines eg.127.0.01. 
	what is needed is to go into the certificate and tick the box that allows firefox to trust it.

Firefox url:
about:preferences#privacy

![[Screenshot 2025-02-07 at 5.20.39 pm.png]]

Click on View Certificates: 
	Authorities tab:
		Caido:
			Edit Trust button:

![[Screenshot 2025-02-07 at 5.21.23 pm.png]]

![[Screenshot 2025-02-07 at 5.22.21 pm.png]]


Now start Caido using by typing ``` caido ``` into the terminal


