---
title: Installing NodeJS on Ubuntu
slug: install-nodejs-ubuntu
date: 2023-08-09
categories:
  - NodeJS
---

  [Rust fnm]: https://github.com/Schniz/fnm#using-a-script-macoslinux

# Installing NodeJS on Ubuntu
Installing the LATEST version of NodeJS, or even a recent version, on Ubuntu is not as straightforward as one would hope.
<!-- more -->

I found out the hard way that using apt or apt-get will not get you close to a recent version of NodeJS installed on Ubuntu. The next way, is to update the apt repo location by adding the PPA using Curl. This didn't work and after attempting to install with apt, I received an error. After a quick Google search, I found that there was a NodeJS version management program written in Rust! So, here is the way I was able to successfully and easily install NodeJS:

```bash
cargo install fnm
```

!!! note
	If you don't have Rust installed, you can still use fnm by running Curl to pull and run an install shell script.

Now, not only do I have NodeJS installed, but with handy [Rust fnm], I can have multiple version of NodeJS running on my system at the same time. Nice.
