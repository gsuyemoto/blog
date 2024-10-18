---
title: Hacking Canon G5020
slug: hacking-canon-g5020
date_published: 1970-01-01T00:00:00.000Z
date_updated: 2023-04-26T05:44:08.000Z
draft: true
---

After some hacking around on the Mimaki/Sindoh 3D printer I had for a reason (to see if I could add the ability for open material), I have taken additional interest in hacking more printers. Sitting conveniently in my office is a Canon G5020.

I first got the IP of the printer. I had previously done an nmap of my network and so I knew the IP to be 192.x.x.41. Using this IP, I used a Rust port scanner called [opscan](https://github.com/sigoden/opscan) to find the open ports:

`opscan 192.x.x.41 -c 100`

Notes: Make sure you use switch -c for number of concurrent threads. If you go along with the default it will silently fail and give you false results.

Ports open:

- 80 http
- 443 https
- 515 printer
- 631 ipp
- 9100 jetdirect

I already have snmp tools installed on my Linux distro, but you can do so with:

`sudo apt-get install snmp`

then:

`snmpwalk -v 1 -c public 192.x.x.41 > canon-g5020.txt`

After viewing the canon-g5020.txt file I found the serial number at:

`iso.3.6.1.2.1.43.5.1.1.17.1 = STRING: "KMDT08191"`

which you can skip the whole dumping to the .txt file and get the serial with:

`snmpget -v 1 -c public 192.168.86.41 .1.3.6.1.2.1.43.5.1.1.17.1`

Now that we have the serial number, if the printer's password has not been changed, we can open up the print server at http://192.x.x.41 and then login with the serial as the password.
