---
title: Port Scanning
slug: port-scanning
date_published: 1970-01-01T00:00:00.000Z
date_updated: 2023-05-10T19:06:54.000Z
draft: true
---

Nmap, arp-scan, opscan, and more

Lately, I have been testing out different methods of port scanning and host discovery. Of course, nmap is among the first to be mentioned in a lot of circles. Especially as it is convenient in providing host discovery and port scanning in one tool. 

Arp-scan is nice for it's simplicity and speed.

And then there are a couple of Rust scanning apps that I tried out, also. One was a port scanner called opscan. This one provides the ability to set the amount of concurrent threads so as to scan 65000 ports way faster than nmap.

Last, I stumbled upon arp-scan which is another Rust project. Which again, is also very fast.

After some testing it seems that the Rust app opscan is the best for performance, although at it's fastest speed it will tend to lose arp packets.

OK, so opscan has some issues. I feel like it has some false-positives on the higher port numbers. I have all ports closed using nftables and opscan will still show high random port numbers as being open. Each scan will produce a different port as being open. When I scan the same port with nmap, it will show the port as being closed.

Also, when testing the opscan on an external IP address it seems to fail with false-negatives. I know a certain external IP had a certain port open, but opscan failed to detect it. Again, using nmap, I was able to detect the port as being open.

Not sure what's going on with opscan, but this seems to indicate that it's not very reliable. Other tests I have made with the app have shown that when setting the 'concurrency' anywhere over 250 will definitely give you poor results.
