---
title: Iptables
slug: iptables
date: 2023-05-24
draft: true
categories:
  - Firewall
---

# iptables-legacy vs. iptables-nft

When running UFW and Docker on Ubuntu, things can get a bit confusing. If you create rules using UFW directly, then you will find that sometimes these rules conflict with those of Docker which loves to set it's own bunch of iptables rules upon startup (which is every time you restart). I found this out the hard way when trying to block certain ports using UFW and finding that these ports were subsequently not being blocked. After a quick look in iptables:

<!-- more -->

````bash
sudo iptables -L
````

I quickly found a dizzying array of rules set by Docker.

On top of this, if you are using UFW on Ubuntu 20 and 22, you might actually have 2 underlying systems that come into play to inplement the rules for UFW. You see, when you set the rules in UFW, these rules are *actually* carried out by it's soldier, iptables. But! With the advent of nftables, this is even more complicated. 

Starting with Ubuntu 20.04, nftables became the default backend for UFW, yet iptables was kept around. So, now, if you have said version of Ubuntu, a quick examination of your system using:

````bash
update-alternatives --config iptables
````

Will show the following 2 subsystems that can come into play, iptables-nft and iptables-legacy. The newer nftables is actually masquerading as iptables-nft and the iptables-legacy is the older system.

What does this all mean? Well, if you have an app that is still using the iptables-legacy system, you can actually have 2 sets of iptables rules. If you know which apps are using the legacy, then you can decide whether you want to remove the legacy. I did so, with the following commands, noted [here](https://askubuntu.com/questions/1295021/warning-iptables-legacy-tables-present):

````bash
sudo rmmod iptable_filter
sudo rmmod iptable_nat
sudo rmmod iptable_mangle
sudo rmmod iptable_raw
````

So, now, lesson learned. Use iptables directly. Maybe. On my system I decided to disable UFW:

`sudo systemctl disable --now ufw`

And if all that wasn't confusing enough, you can access nftables either with 'iptables' or with:

`sudo nft list ruleset`

Docker seems to be the main culprit in reactivating iptables-legacy. I have disabled the Docker services:
`
sudo systemctl disable docker.service
`
And even then, the rules in iptables-legacy are back after reboot. I can tell Docker is adding them as they are all named after Docker service.

Next, I try to blacklist the iptables-legacy modules, by adding the following in the /etc/modprobe.d/blacklist.conf file. OK, nevermind the blacklist file. Didn't work.

In the end, I removed iptables via

`sudo apt-get purge iptables`

and moved on... To nftables. Before purging iptables, I believe you need to remove all the rules and flush and more. Check out a good article [here](https://www.cyberciti.biz/tips/linux-iptables-how-to-flush-all-rules.html) for that.
