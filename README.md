# WebGoat-XSS-Challenge
#### Using Cross-Site-Scripting (XSS) and Tamper Data on a vulnerable web server to gather login credentials and compromise the data integrity of its website
#### By Glen Abalayan
## Technologies Used
* Foxy Proxy Web Scarab (FireFox Extension)
* Tamper Data for FF Quantum by Pamblam (FireFox Extension)
* CyberChef
## Environment Setup
1. Ensure both OWASP BWA server and Kali Linux machine are running in the Hyper-V manager.
2. Input the IP address of OWASP BWA server into a browser on the Kali Linux machine.
3. Select OWASP WebGoat on the menu
	* ![](Images/0-5-Access-the-OWASP-BWA-server-by-inputting-server-IP-address-in-browser.JPG)
	*Use the login credentials _guest_:_guest_ to continue to WebGoat
4. On the WebGoat website, navigate to the Challenge sub-menu on the left side of the page to begin the activity.
![](Images/1-Navigated-to-WebGoat-Challenge-Page.JPG)
## Task 1: Breaking the authentication scheme
* To break the authentication scheme and gain access to the database, parse through the HTML source code to find clues about the login credentials.
	* To access the website's source code, replace the URL's parameters so that 	 __source?source=true__
	![](Images/2-Appendeds%20UIRL%20to%20read%20source%20code.JPG)
* While on the source code, filter for passwords by running **Cntrl+F** and typing in "Password"
	*  Parsing for "Password" in the browser brings us the login credentails 
	__"youaretheweakestlink":"goodbye"__
	![](Images/3-Cntrl-F-for-password-and-found-user-credentials-on-page.JPG) 
* Now that we have obtained login credentials from the source code, we will now use these credentials to log into the website
	* Log into the website with __"youaretheweakestlink":"goodbye"__
	* ![](Images/4-logged-in-using-hidden-credentials.JPG)
* Congratulations! You have completed Task 1. You have now cracked the website's authentication and can access the site.
## Task 2: Stealing all the credit cards from the database
* Now that you have logged into the website, it is time to steal credit card data from the database.
* Start the Tamper Data extension on your Firefox browser.
	* ![](Images/5-Started-Tamper-Data-Extension.JPG)
* With Tamper Data running, intercept and tamper an HTTP Post request by clicking on the "Buy Now" button and copy the user cookie. Copy everything inside the quotation marks. The cookie is written in Base64.
	* ![](Images/6-Tamper-HTTP-POST-Request-When-Click-Buy-Now-And-Copy-Cookie-Translate-From-Base64.JPG)
* Using CyberChef, input the copied Base64 user string and translate it from Base64 to ASCII.
	* Notice how the string translates to "youaretheweakestlink", the username we extracted in the previous step.
	* ![](Images/6-05-Notice-the-Base64-string-translates-to-username.JPG)
	* Our goal now is to insert malicious script alongside the string so the server responds back with credit card data from the database.
* Using Cyberchef, navigate to the "To Base64" translation tool and input this command to inject a script alongside the user cookie.
	* __youaretheweakestlink' OR '1'='1__
	* Copy the translated Base64 string from the Output.
	* ![](Images/7-Using-Cyber-Chef-Edited-Cookie-To-Include-Script.JPG)
* With Tamper Data open, replace the original username cookie string with the copied Base64 string that includes the injected script from the previous step.
	* ![](Images/8-Copied-Edited-Cookie-And-Transplanted-It-Back-to-POST-Request.JPG)
* Sending the tampered data results to the server results in exposing credit card information from the database.
	* ![](Images/9-Sending-Out-Tampered-Reqeust-Gave-Me-All-Keys.JPG)
* Congratulations! You have completed Task 2. You used Cross-Site-Scripting (XSS) to extract private credit card information from a web server. 
## Task 3: Defacing the website
* Now that you have gained access to the site and retrieved private data from the database it is now time to deface the website!
* Start the FoxyProxy WebScarab extension from your Firefox Browser.
	* ! [](Images/10-Started-FoxyProxy-Web-Scarab.JPG)
* You also need to have the WebScarab application running on your machine. To run the application, input __"webscarab"__ in your Linux terminal.
	* ! [](Images/11-Started-Web-Scarab-In-Terminal.JPG)
* Before we can run any commands, we must first test whether the site is vulnerable to XSS attacks and find where our attak would be located. 
	* With the WebScarab extension and application running, click on the "View Network" button, select tcp, and edit the request so that the Value for the File variable includes __"tcp && whoami && pwd"__
		* ![](Images/12-Clicked-On-View-Network-to-Edit-Request-To-find-working-directory-and-text-for-xss-vuln.JPG)
		* This command forces the server to not only run the original tcp command, but also __whoami__ and __pwd__
			* __whoami__ shows which user you are logged in as 
			* __pwd__ prints the current working directory you are located in.
	* Submitting the edited request results in the server telling us that we are logged in as __root__ and that we are currently located in __/var/lib/tomcat6__
		* ![](Images/13-Verified-Vulnerability-and-working-directory-by-seeing-edited-request-on-page.JPG)
* Now that we have established that the server is vulnerable to XSS attacks we must now locate the webpage in the server
	* Remember, the webpage we need to deface is __webgoat_challenge_guest.jsp__. We need to locate where this file is in order to change its contents.
	* To locate the file, you must first change directories to the root directory and run the find command to look for the webpage.
		* With WebScarab open, click on "View Network Status" again, select tcp, and edit the Value for the File variable to __"tcp && cd / && find . -iname webgoat_challenge_guest.jsp"__.
		* ![](Images/14-Inserted-Script-to-change-to-root-directory-and-find-webpage-to-tamper-file.JPG)
	* The injected script returns with the webpage's location at __./owaspbwa/owaspbwa-svn/var/lib/tomcat6/webapps/WebGoat/webgoat_challenge_guest.jsp__. 
		* ![](Images/15-Injected-Script-Returns-With-File-Location-Of-Webpage.JPG)
* Now that we know which directory the webpage is located in, we can now naviage to the directory and deface the webpage. 
	* With WebScarab running, click on "View Network Status", select tcp, and edit the request so that the Value for the File variable is __"tcp && cd webapps/WebGoat && echo You have been hacked gg > webgoat_challenge_guest.jsp__
		* Remember, we did not have to write the absolute path because we are already in the __/var/lib/tomcat6/__ directory. 
	* ![](Images/16-Changed-Directory-To-WebGoat-And-Edited-Guest-Webpage-With-Text.JPG)
	* Click "Accept Changes" in the WebScarab application and see whether the webpage has been altered.
* To confirm you have defaced the website, scroll to the bottom of the WebGoat Challenge webpage and compare the edited webpage with the original. 
	* ![](Images/17-Confirmation-Of-Defaced-Website.JPG)
* Congratulations you have completed Task 3 and have successfully completed WebGoat's XSS Challenge!
