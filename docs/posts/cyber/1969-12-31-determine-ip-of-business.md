---
title: Determine IP of Business
slug: determine-ip-of-business
date_published: 1970-01-01T00:00:00.000Z
date_updated: 2023-05-10T18:41:25.000Z
draft: true
---

How to find out the IP of a business?

Nowadays, most businesses won't be hosting their own web server, and the ones that do probably are doing so using cloud or co-loc facilities. Pinging the domain for a business would be pretty useless at that point.

Another avenue of approach would be to use their email. If you receive an email from the business, you can ascertain a possible IP from the email headers. Many businesses still host their own Outlook email servers separately.

In Gmail, you can find the full headers of an email by clicking on the hamburger next to the 'reply' link. In the drop down, you can find the option for 'Show original' and this will open up a new window with the email and its full headers.

In the full headers, you will need to scroll down to the bottom to find the origin of the email and its path as it heads to Google and its servers and then to you. In there, you might find a mention for:

`X-Mailer: Microsoft Office Outlook`

And right under that you might find:

`X-AntiAbuse: Primary Hostname - server.businessdomain.com`

Try pinging that domain and if you are lucky you will find the IP to the servers for the actual business. From there you can use the 'tracepath' command on Linux machines to get even more info. Check out [this](https://pimylifeup.com/ubuntu-traceroute/) article here for additional insight into tracepath and traceroute.

`tracepath server.businessdomain.com`

When I was testing this out, I first pinged the domain server.businessdomain.com and then did 2 separate traceroutes with the IP and then with the domain and received 2 different results. This makes sense, as the trace needs to resolve the DNS when not using the raw IP address.
