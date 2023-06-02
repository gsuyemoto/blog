---
title: web and goats part 2
slug: web-and-goats-p2
date: 2023-06-02
categories:
  - Broken Access Control
  - Web App Pen Testing
---

  [Ubuntu VM]: ./it-hums-it-roars.md
  [BurpSuite]: https://portswigger.net/burp/communitydownload
  [OWASP WebGoat]: https://owasp.org/www-project-webgoat/
  [WebGoat Github]: https://github.com/WebGoat/WebGoat/releases
  [OWASP ZAP]: https://owasp.org/www-project-zap
  [Part 1]: ../web-and-goats-p1

# Web Goats Part 2

Ok, so this is part 2 of the my adventures with WebGoat. Please check out [Part 1] if you want to see my solutions for the SQL Injection part of WebGoat. Spoiler alert though, that article covers most of the solutions I came up for the SQL Injection section. I will be going over my solutions for some of the Broken Access Control section of WebGoat here. I like to have a historical document for these exercises as a nice to have for future reference.

If you want to learn or practice your cybersecurity skills, especially against the top ten OWASP web vulnerabilities, then the [OWASP WebGoat] is a great tool. Allowing for you to practice a range of different techniques that would be otherwise be time consuming to set up in a lab setting, such as fuzzing and SQL Injection, WebGoat also provides great insight and guidance.

<!-- more -->

!!! info "Get the WebGoat Distro"
    Go to the [WebGoat Github]{target=_blank} for the latest distro. Just download it and run it:
    ````java
    java -jar webgoat.jar
    ````

Default server settings serve the WebGoat on localhost (127.0.0.1) on port 8080. Just open a browser and go to the URL:
````
https://localhost:8080/WebGoat
````

!!! warning
    It's not a good idea to set up WebGoat and provide it access to the WAN/internet and therefore OTHERS to access your WebGoat.
    
Some of the lessons require some kind of proxy and the recommendation is for [OWASP ZAP] or [BurpSuite]. In many case, I found that you could just use the browser's dev tools. I used all of the choices, ZAP, Burp and the dev tools. Burp requires that you get a trial license for scanning. ZAP was incredibly handy for fuzzing, albeit in many cases it was just brute force.

### Broken Access Control

I found challenges 2-4 pretty straightforward. Challenge 5 was a bit more involved and for me, involved use of ZAP in order to fuzz the answer.

From the previous lessons, it was discovered that in order to obtain your profile data is was necessary to make the following HTTP call:

````
GET http://localhost:8080/WebGoat/IDOR/profile/userid HTTP/1.1
````

With *userid* being your profile id number ascertained in previous challenges. Using ZAP, I decided that I would just fuzz around my profile id (before and after) and see if another user's profile came up:

![type:video](videos/fuzzing-other-users-profile.webm)
