# Access Control Vulnerability
### Unprotected admin functionality
- Search the `/robots.txt`
- view It have administrator file path .


### Unprotected admin functionality with unpredictable URL
- Review the Home Page with developer tool.
- Read view source and see comment code 
- find --> **admin file path**

### User role controlled by request parameter
- Goal : delete the user **carlos**
- Credentials: wiener:peter
- first login wiener:peter and look at the burpsuite's proxy history `/login` 
- And see `Cookie: Admin=false; session=UkUQgWWMGnzKNI8P2EJ0M3eTlv71dgmE`
- So, change   `Admin=true` not work `401 response`
- then I enter `/admin` and look at burp history
- so I found this `Admin=false; session=AKYkdm0KiTSQhrLlHl8eVGKo9cnWN94B`
- So I also try `Admin=true` it 200(ok),

### User role can be modified in user profile
Goal: accessing the admin panel and using it to delete the user **carlos**.<br>
Credentials: wiener:peter
-  Login as wiener:peter
-  then see the `change email-address` so change 
-  then sent to **repeater** then found **roleid:1**
-  so change the **request** add json data `,"roldid":2` then send
-  and see `302` response so get admin  panel

### User ID controlled by request parameter
Goal : obtain the API key for the user **carlos** and submit it as the solution
Credentials : "wiener":"peter"<br>
This lab has a horizontal privilege escalation vulnerability on the user account page
- first Login as Credentials 
- Then found `https://0a1f00220345a795848b6c3300a0006c.web-security-academy.net/my-account?id=wiener`
- so change `https://0a1f00220345a795848b6c3300a0006c.web-security-academy.net/my-account?id=carlos`
- and found api keys
- Done!

### Lab: User ID controlled by request parameter, with unpredictable user IDs
Goal : ind the GUID for **carlos**, then submit his API key as the solution.
Info : This lab has a horizontal privilege escalation vulnerability on the user account page, but identifies users with GUIDs.<br>
Credentials : wiener: peter
- first Login as Credentials
- see these `https://0ada00f00395ec7f8124d5bb006200f1.web-security-academy.net/my-account?id=204053eb-9343-4cfd-b7ac-b9244058f831`
- and looks the blogs who wirte each post
- I search **carlos** wrote post and i found **carlos** name like a link so I click it
- then I found ==> `https://0ada00f00395ec7f8124d5bb006200f1.web-security-academy.net/blogs?userId=7934ec5f-3827-4f71-88df-a81530d67199`
- So I used **userId** value to here *my-account?id=* and see the GUID value
- Done!

### Lab: User ID controlled by request parameter with data leakage in redirect
Info: This lab contains an access control vulnerability where sensitive information is leaked in the body of a redirect response.
Goal: To solve the lab, obtain the API key for the user carlos and submit it as the solution.<br>
Credentials: wiener:peter
- first login as credentials
- see the path `https://0ac8005a030f4966824147c200c900bd.web-security-academy.net/my-account?id=wiener`
- use the burpsuite and look at the history of proxy tab.
- send to the repeater this link ==> `GET /my-account?id=wiener`
- then change `id=carlos` and response *302* and look the little down
- found it `<p>Your username is: carlos</p><div>Your API Key is: mCw9MV66zZyEklLO7bXcimxNwkXQSfCj</div>`
- Done!

### User ID controlled by request parameter with password disclosure
Info: This lab has user account page that contains the current user's existing password, prefilled in a masked input.
Goal: To solve the lab, retrieve the *administrator's* password, then use it to delete the user *carlos*<br>
Credentials: wiener:peter
- first login as credentials
- then found the email change and password change **already fill password**
- `https://0ab100d90365d3e98182e8ca00b200f5.web-security-academy.net/my-account?id=wiener` change the id value to like this `?id=administrator` and click
- then see the administrator password value but we can't read it.
- use the developer tool and found source code.==>` <input required type=password name=password value='hipwmbbxx7q6ixkwf1uk'/>`
- So login with that password and *delete* the carlos.
- !Done

### Lab: Insecure direct object references
Info: This lab stores user chat logs directly on the server's file system, and retrieves them using static URLs.
Goal: Solve the lab by finding the password for the user carlos, and logging into their account.<br>
Credentials: **None**
- First i see live chat 
- I send blah blah then intercept wiht burp
- and look the history of proxy tab
- found the path `GET /download-transcript/2.txt` then send to repeater
- nothing strange things but when change the `1.txt` and found the password
- so i used that password for carlos and login
- Done!

### Lab: URL-based access control can be circumvented
Info: This website has an unauthenticated admin panel at */admin*, but a front-end system has been configured to block external access to that path. However, *the back-end application* is built on a framework that supports the **X-Original-URL** header<br>
Goal: **access the admin panel and delete the user carlos**.<br>
Credentials: None
- first click the admin-pannel but not allow! `https://0a19004e042a96c583be004000a100f0.web-security-academy.net/admin` ==> **"Access denied"**
- so I use burpsuite
- then `/admin` send to repeater and I add header vale like this ==> `X-Original-URL: /admin` but sitll *Access denied*
- So change instead of `GET /admin` to `GET /` then send and boom!
- I got 200 ok
- found these <u>/admin/delete?username=wiener</u> and <u>/admin/delete?username=carlos"></u>
- so I add the "GET /?username=carlos and X-Original-URL: /admin/delete"
- and back `Home Page`
- Done!

### Lab: Method-based access control can be circumvented
Info: This lab implements access controls based partly on the HTTP method of requests. You can familiarize yourself with the admin panel by logging in using the credentials **administrator:admin**.
Goal: To solve the lab, log in using the credentials wiener:peter and exploit the flawed access controls *to promote yourself to become an administrator*.<br>
Credentials: wiener:peter
- I before start hacking I already open burpsuite and intercept all.
- first I login as administrator:admin and go to the admin-panel and upgrade **carlos** to *admin* and burpsuite send `admin-role` to repeater rename **upgrade**
- and in burpsuite `upgrade` tab change the username to **wiener** and change the request *Method* To GET and send 
- but 401 unauthorized so I login as wiener:peter and intercept and I copy cookie of wiener and paste to *upgrade* tab of cookies value and send it
- boom I got it.

### Lab: Multi-step process with no access control on one step
Info: This lab has an admin panel with a flawed multi-step process for changing a user's role. You can familiarize yourself with the admin panel by logging in using the credentials administrator:admin.
Goal: To solve the lab, log in using the credentials wiener:peter and exploit the flawed access controls to promote yourself to become an administrator.<br>
Credentials: administrator: admin, wiener:peter
- first login wiht admininstrator:admin and upgrade **carlos** to ADMIN role.
- then incognito tab login with wiener:peter and copy cookies
- in Burpsuite `POST /admin-roles` change the username and cookies value and send it, got **302** response
- So got it Done!

### Lab: Referer-based access control
Info: This lab controls access to certain admin functionality based on the Referer header. You can familiarize yourself with the admin panel by logging in using the credentials administrator:admin.
Goal :To solve the lab, log in using the credentials wiener:peter and exploit the flawed access controls to promote yourself to become an administrator.<br>
Credentials: administrator:admin, wiener:peter
- first login with administrator:admin
- then upgrade *carlos* user-role to admin then intercept with burp
- then incognito tab login with wiener:peter and copy sessions cookie value
- and burpsuite repeater change username=wiener and cookie value paste we copy of wiener cookie value
- and send it 
- Got it
  
 
