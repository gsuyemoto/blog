---
title: Spoofing or Hiding IP
slug: spoofing-or-hiding-ip
date_published: 1970-01-01T00:00:00.000Z
date_updated: 2023-05-12T05:43:36.000Z
draft: true
---

How to hide your IP address

Probably best to stay away from anonym8. All you need, with Linux machine is to install the tor package:

`sudo apt-get install tor`

Make sure the tor service is up and running:

`sudo service tor status`

If that looks good then first check what your external/WAN IP is using:

`wget -q -O - checkip.amazonaws.com`

This will make a wget call in quiet mode and redirect output to stdout instead of saving to a file which is its default behavior. Or! any of the other ways to get your IP found [here](https://www.cyberciti.biz/faq/how-to-find-my-public-ip-address-from-command-line-on-a-linux/).

Now, hide your IP with a tor relay server:

`source torsocks on`

Check your IP using the method from above and you should be good to go. I did notice in my testing that using curl didn't seem to work and would bypass the tor relay, if you know why this is let me know!
