---
title: Exploring the Khadas VIM2
slug: exploring-the-khadas-vim2
date_published: 1970-01-01T00:00:00.000Z
date_updated: 2021-09-30T18:49:32.000Z
tags: #Import 2022-12-26 21:35
draft: true
---

In attempt to implement my idea of having a smart router (i.e. a router device that can process AI/ML functions locally without having to reach out to cloud servers), I have purchased a Khadas VIM2. The VIM2 is an interesting device with some great specs and most importantly, it has Wifi on board. Even better, the Wifi is 802.11ac. Even better, the Wifi is AP capable. So, onward!

Here are list of items you will need from Amazon:

Some quick notes before you buy the Khadas:

1. You will need very slim USB-C and HDMI cables in order to use both at the same time. USB-C is necessary for power and HDMI is necessary to display if not booting directly in headless mode. I had to search around numerous cables to find ones that would work. The ones I found were actually sitting around my house (I have PLENTY), so I will not be able to post any from Amazon.
2. You will also need to have a micro SD card IF you wish to either boot from the card or transfer the OS To eMMC via the SD card. I used an SD card and therefore the instructions that follow require the SD card.
3. It's convenient to have a wireless keyboard and mouse to set up and login directly to the VIM2 even in headless mode.

Once I got the VIM2 from Amazon, I opened the package to find a nice looking board that was maybe a tad too perfectly fit into it's compact box. I had a slight difficult getting it out of the box, being careful not to damage the board.

Going to the Khadas site for documentation on how to set up the board (seeing as there was no information provided with the board itself), I found a page on their site that provided some instructions that covered the [VIM2](https://docs.khadas.com/linux/vim2/index.html). The instructions and steps on the Khadas site seemed a bit convoluted to me, so here is my version:

1. 
The Khadas page with all of the VIM2 images are [here](https://docs.khadas.com/linux/firmware/Vim2UbuntuFirmware.html). I chose to use the Ubuntu 20.04 mainline kernel operating in headless/server mode from [here](https://dl.khadas.com/Firmware/VIM2/Ubuntu/SD_USB/VIM2_Ubuntu-server-focal_Linux-5.12_arm64_SD-USB_V1.0.5-210430.img.xz). With the headless mode, you are still able to use the monitor as the VIM2 TTY terminal without a serial cable attached. Just plug the VIM2 in with a wireless keyboard, wireless mouse, HDMI and power via the USB-C and you are good to go!

2. 
After downloading the OS image, flash it to the SD card. I used [Balena Etcher](https://www.balena.io/etcher/). You can also use CLI on Linux systems as detailed on the Khadas site [here](https://docs.khadas.com/linux/vim2/InstallOsIntoSdusb.html).

3. 
Insert the SD card into the VIM2, plug in the USB-C for power and then press the power and reset buttons as described [here](https://docs.khadas.com/linux/vim2/BootIntoUpgradeMode.html) on the Khadas site.!
[VIM2-Pic---Reset-and-Power-Buttons](https://scratchthecurious.com/content/images/2021/08/VIM2-Pic---Reset-and-Power-Buttons.png)

4. 
The VIM2 board will boot up and eventually present you with a login. Default login and pass is "khadas" and "khadas".

5. 
After logging in, it's probably good practice to change the password with `passwd`

6. 
Now, if you want to use the on board wireless to connect to your home Wifi network you can easily set it up with the very convenient nmtui. Just use the command `sudo nmtui` and follow the menu commands to set up the Wifi as a client and add your SSID and passphrase.

7. 
If, on the other hand, you want to set up the VIM2 as an AP, then you should follow the instructions as noted [here](https://docs.khadas.com/linux/vim2/WifiApstaMode.html). Which I modified to the following in order to broadcast 2.4GHz AND 5GHz:
`sudo nmcli con add type wifi ifname wlan0 con-name Hostspot autoconnect yes ssid mywifi

sudo nmcli con modify Hostspot 802-11-wireless.mode ap 802-11-wireless.band bg ipv4.method shared

sudo nmcli con modify Hostspot wifi-sec.key-mgmt wpa-psk

sudo nmcli con modify Hostspot wifi-sec.psk "yourpass"

sudo nmcli con up Hostspot`

sudo nmcli con add type wifi ifname wlan0 con-name Hostspot2 autoconnect yes ssid mywifi-5gz

sudo nmcli c modify Hostspot2 802-11-wireless.mode ap 802-11-wireless.band a 802-11-wireless.channel 149 802-11-wireless.powersave 2 ipv4.method shared

sudo nmcli con modify Hostspot2 wifi-sec.key-mgmt wpa-psk

sudo nmcli con modify Hostspot2 wifi-sec.psk "yourpass"

sudo nmcli con up Hostspot2

8. 
In order for a client to connect to the new AP and have access to the internet via eth0 (this is how my router config is set up, with the eth0 connected to WAN), then you will need to forward some packets.
