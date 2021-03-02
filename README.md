# WebGoat-XSS-Challenge
#### Using Cross-Site-Scripting (XSS) and Tamper Data on a vulnerable web server to gather login credentials and compromise the integrity of a website
#### By Glen Abalayan
## Technologies Used
* Foxy Proxy Web Scarab (FireFox Extension)
* Tamper Data for FF Quantum by Pamblam (FireFox Extension)
* CyberChef
## Environment Setup
1. Ensure both OWASP BWA server and Kali Linux machine are running in the Hyper-V manager.
2. Input the IP address of OWASP BWA server into a browser on the Kali Linux machine.
3. Select OWASP WebGoat on the menu
	*Use the login credentials _guest_:_guest_ to continue to WebGoat
4. On the WebGoat website, navigate to the Challenge sub-menu on the left side of the page to begin the activity.
![](Images/1-Navigated-to-WebGoat-Challenge-Page.JPG)
## Task 1: Breaking the authentication scheme
* To break the authentication scheme and gain access to the database, parse through the HTML source code to find clues about the login credentials.
	* To access the website's source code, replace the URL's parameters so that 	* __source?source=true__
	![](Images/2-Appendeds%20UIRL%20to%20read%20source%20code.JPG)
* While on the source code, filter for passwords by running **Cntrl+F** and typing in "Password"
	*  Parsing for "Password" in the browser brings us the login credentails 
	*__"youaretheweakestlink":"goodbye"__
	![](Images/3-Cntrl-F-for-password-and-found-user-credentials-on-page.JPG)
## Task 2: Stealing all the credit cards from teh database
## Task 3: Defacing the website