---
title: DIY Router using Jetson Nano & Ubuntu
slug: diy-router-using-jetson-nano-ubuntu
date_published: 1970-01-01T00:00:00.000Z
date_updated: 2022-12-29T00:12:35.000Z
tags: #Import 2022-12-26 21:35
draft: true
---

I've had my Google router for a while now (since 2018) and I'm frankly tired of it. Performance is not good and reliability not so good either. So, it's time to build my own!

Coincidentally, I've also had a Jetson Nano lying around that I've wanted to use to stream some possibly DIY security cameras for local and more importantly PRIVATE computer vision processing. Having [Amazon engineers](https://www.bbc.com/news/technology-51048406) and whoever have access to my security cameras is not altogether comforting. And like the Google router, the performance sucks.

So, why not create my own router with the Jetson!? I thought. Well, why the heck not.

I came across a [great article](https://pythops.com/post/create-your-own-image-for-jetson-nano-board) on how to create a custom image for the Jetson and I decided to use it as a baseline configuration considering it's basically just the bare essentials. Plus! It will allow me to create an image for all of you who want to try the same thing, making things MUCH easier for you!

Some changes are needed to the image and Ansible configuration:

1. Need to install network-manager as netplan is [unable to create AP](https://askubuntu.com/questions/1166619/define-hostapd-configuration-settings-on-netplan) using networkd as renderer. OK, nevermind netplan. Could not get the AP to work in a or g mode using a wireless AC USB adapter. Need to install and use hostapd instead. Also, need to set channel to 40. Tried 36 and would not work.
2. Add hostname to /etc/hostname file.

If you are using the recommended wireless adapter mentioned in the parts list, then you should install the drivers for it from [here](https://github.com/aircrack-ng/rtl8812au). Getting the right wireless adapter to work with Linux is a feat in itself as your choices are quite narrow, but if you want to set up your own AP AND have that AP broadcast on 5GHz then you really have a challenge on your hands. The recommended adapter was the only one I found to work out of 5 that I tried. All of the others might be 5GHz capable, but all ran into the regulatory domain restrictions that didn't allow that particular card to work in AP mode at 5GHz.

Now for the iptables. In order for the packets to stream freely from wlan0 (your AP) back and forth from eth0 (your WAN). First, make sure you set ip forwarding:

`echo 1 | sudo tee /proc/sys/net/ipv4/ip_forward`

To ensure that the ip forwarding is persisted on reboot. Find and remove the commenting for the following line in your /etc/sysctl.conf:

`net.ipv4.ip_forward = 1`

And then add dynamic addressing:

`echo 1 | sudo tee /proc/sys/net/ipv4/ip_dynaddr`

Then you will need to set these iptables rules:

`sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE

sudo iptables -A FORWARD -i eth0 -o wlan0 -m state --state ESTABLISHED,RELATED -j ACCEPT

sudo iptables -A FORWARD -i wlan0 -o eth0 -j ACCEPT
`

Save the rules with iptables-persistent by entering this command:

`sudo iptables-save`

You can check if you are actually connected and using 802.11ac bandwidth with the command:

`sudo iw wlan0 station dump`

Important files:

1. /etc/netplan/netcfg.yaml
2. /etc/networks – for peer-to-peer network link
3. /etc/resolv.conf
4. /etc/wpa_supplicant

Important commands:
`
    resolvectl status
    iwconfig
    ifconfig
    iw status
    sudo ufw status
    lspci
    lshw -C network
    nmcli connection show // great last connected APs
    nmcli dev wifi // great for showing nearby APs
    iwlist
    watch -n 1 cat /proc/net/wireless // continuous update of signal level
`
