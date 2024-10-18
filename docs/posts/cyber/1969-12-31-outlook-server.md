---
title: Outlook Server
slug: outlook-server
date_published: 1970-01-01T00:00:00.000Z
date_updated: 2023-05-12T01:05:23.000Z
draft: true
---

Maybe a MS Server box?

A family acquaintance runs a business and as a way to practice my pen testing skills, will be probing their server for vulnerabilities. I figure this will be a win win as I will be able to practice on a Windoes machine (a dedicated configured out of the box server at that – which I surmise many businesses don't change for a custom configuration) and be able to provide the business with insight into their vulnerabilities and how to mitigate.

First! I need an IP. Their website is hosted via a large cloud provider, which will probably be the norm, especially for SMBs. As I can't ascertain their IP via their website, I chose to use their email. In other cases, I think it would be very feasible to have a rep from a business to email you information, etc., so this seems like a valid approach.

In the email sent to me, I went through its full headers to find that the company is using Outlook server for their internal email, as this was shown in the headers. Further, the headers also provided me with their Outlook server domain.

From here, I can do some quick port scans of their server and find out what ports are open. For my case, their server had several ports open, including 80 and 21. Going through my browser and hitting their open port 80, I was redirected to port 443 and shown a default landing page for their server. The landing page provided me with info that the server was using cPanel for administration.

Checking the default login pages for cPanel, I found that [default](https://www.liquidweb.com/kb/new-user-tutorial-logging-in-to-cpanel-services/) ports and login pages were available. And now the interesting part begins.

Of note, is the trademark for the cpanel at the bottom of the landing pages which denotes the year 2016. Now I can have a general ballpark idea of the cpanel version.

Looking through the page source for all of the public facing pages, I found that their were at least 2 form submissions. One for login to each of the different services, and one form submission to change the locale settings (language).

Ports found to be open:

- 3306 MySQL
- 80 http
- 21 ftp

FOR MYSQL:

- Tried accessing via telnet and was denied with error that DHost 'my-ip' is not allowed to connect
- Maybe can try sending crafted login packets? Spoof the IP?

FOR HTTP:

- Server redirects http to https
- Server runs cpanel from 2016
- Server cpanel logins for cpanel, webmail, and WHM on ports 2083, 2096, and 2087 respectively
- Tried simple SQL injection on all ports
- Tried simple default passwords, including name of company for pass on all ports

FOR FTP:

- Able to connect via ftp CLI
- Tried simple user and pass of 'admin' and 'root' and was denied access

After seeing that I could access the login screens for cpanel at the ports listed above, I was tempted to try a brute force password tool, but had some reservations of using brute force. Anyways, curious to see if it worked, I installed and used [Hydra](https://github.com/vanhauser-thc/thc-hydra).

My reservations were correct, as after a couple of attempts, the server locked me out. Apparently, the server blacklisted my IP. So, here was another opportunity to try some other tools.

First up was [anonym8](https://github.com/HiroshiManRise/anonym8). Which I don't recommend. Installing script was a disaster and was attributable to me being very lazy and at least taking a quick look at the scripts installed. The anonym8 install will create a service which uses a shell script to manipulate iptables and primarily uses the package tor for anonymous traffic. I don't use iptables and instead use nftables and so this didn't work.

After removing all of the junk installed by anonym8, except for tor, I proceeded to use a command with tor like so:

`source torsocks on`

And now all my command line traffic was routed via tor servers:

`wget -q0 - https://api.ipify.org; echo`

Gave me an IP from a tor server. Using my browser bypasses all of this and will show my real IP. My next step was to obfuscate my MAC. Looking around, I see a lot of noise about macchanger, but after my experience with anonym8, I know better. I used [this](https://www.wikihow.com/Change-MAC-Address-on-Ubuntu) method which was incredibly simple and begs the question of why anyone would want to install macchanger for such a thing?
