---
title: SSH Attacks on My Server
slug: ssh-attacks
date: 2023-05-31
draft: true
categories:
  - Blue Team
---

# SSH Attacks on My Server

Recently, I was doing some configuration changes to my personal web server that I host as a VM on Digital Ocean. I was having some issues and was going through my logs to do some debugging when I happened to peruse my auth.log. Upon a quick look, I immediately saw that the log was filled with "Invalid user" lines...
<!-- more -->

Obviously, this piqued my curiosity and I spent the next hour or so picking through the log and archived logs to see what was going on. It didn't take me long to see that someone(s) was launching multiple brute force dictionary attacks on my open SSH port. Here is some info that I gleaned:

````bash title="Checking total Invalid User attempts"
cat auth.log.1 auth.log.3 auth.log.4 | grep "Invalid user" | wc -l 
56683
````

!!! note 
    Those archived logs span from May 6 until May 31.

After changing the ownership of the archived files to make it easier for me to get the data out. I created a file called 'invalid-users.tx'

````bash title="chown and invalid-users.txt"
sudo touch invalid-users.txt
sudo chown user:user invalid-users.txt
cat auth.log.1 auth.log.3 auth.log.4 | grep "Invalid user" > invalid-users.txt
````

The dictionary file has many default usernames included as indicated by the attempted usernames listed in my log file:

````bash title="username --> admin"
cat invalid-users.txt | grep " admin " | wc -l 
4492
````
````bash title="username --> postgres"
cat invalid-users.txt | grep " postgres " | wc -l 
862
````
````bash title="username --> pi"
cat invalid-users.txt | grep " pi " | wc -l 
758
````
````bash title="username --> orangepi"
cat invalid-users.txt | grep " orangepi " | wc -l 
266
````
````bash title="uniq usernames"
cat invalid-users.txt | pcregrep -o1 "user (.+) from" | sort | uniq | wc -l
6330
````
!!! note
    pcregrep is handy for easily capturing and displaying the groups from the regex  
    install with:
    ````bash
    sudo apt-get install pcregrep
    ````
````bash title="uniq ips"
cat invalid-users.txt | pcregrep -o1 "from (.+) port" | sort | uniq | wc -l
3527
````
