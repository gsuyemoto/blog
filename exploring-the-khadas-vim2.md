<h1>Exploring the Khadas VIM2</h1>
<p>In attempt to implement my idea of having a smart router (i.e. a router device that can process AI/ML functions locally without having to reach out to cloud servers), I have purchased a Khadas VIM2. The VIM2 is an interesting device with some great specs and most importantly, it has Wifi on board. Even better, the Wifi is 802.11ac. Even better, the Wifi is AP capable. So, onward!</p><p>Here are list of items you will need from Amazon:</p><!--kg-card-begin: html--><iframe style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//ws-na.amazon-adsystem.com/widgets/q?ServiceVersion=20070822&OneJS=1&Operation=GetAdHtml&MarketPlace=US&source=ss&ref=as_ss_li_til&ad_type=product_link&tracking_id=gsuyemoto-20&language=en_US&marketplace=amazon&region=US&placement=B07CXJQYFB&asins=B07CXJQYFB&linkId=a3ceaac9d06e04356e5e227cdc2bb8f7&show_border=true&link_opens_in_new_window=true"></iframe><!--kg-card-end: html--><!--kg-card-begin: html--><iframe style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//ws-na.amazon-adsystem.com/widgets/q?ServiceVersion=20070822&OneJS=1&Operation=GetAdHtml&MarketPlace=US&source=ss&ref=as_ss_li_til&ad_type=product_link&tracking_id=gsuyemoto-20&language=en_US&marketplace=amazon&region=US&placement=B07FCMKK5X&asins=B07FCMKK5X&linkId=2d74b361423bcd0ed5a48a85fd7dc27e&show_border=true&link_opens_in_new_window=true"></iframe><!--kg-card-end: html--><p>Some quick notes before you buy the Khadas:</p><!--kg-card-begin: markdown--><ol>\
<li>You will need very slim USB-C and HDMI cables in order to use both at the same time. USB-C is necessary for power and HDMI is necessary to display if not booting directly in headless mode. I had to search around numerous cables to find ones that would work. The ones I found were actually sitting around my house (I have PLENTY), so I will not be able to post any from Amazon.</li>\
<li>You will also need to have a micro SD card IF you wish to either boot from the card or transfer the OS To eMMC via the SD card. I used an SD card and therefore the instructions that follow require the SD card.</li>\
<li>It's convenient to have a wireless keyboard and mouse to set up and login directly to the VIM2 even in headless mode.</li>\
</ol>\
<!--kg-card-end: markdown--><p>Once I got the VIM2 from Amazon, I opened the package to find a nice looking board that was maybe a tad too perfectly fit into it's compact box. I had a slight difficult getting it out of the box, being careful not to damage the board.</p><p>Going to the Khadas site for documentation on how to set up the board (seeing as there was no information provided with the board itself), I found a page on their site that provided some instructions that covered the <a href="https://docs.khadas.com/linux/vim2/index.html">VIM2</a>. The instructions and steps on the Khadas site seemed a bit convoluted to me, so here is my version:</p><!--kg-card-begin: markdown--><ol>\
<li>\
<p>The Khadas page with all of the VIM2 images are <a href="https://docs.khadas.com/linux/firmware/Vim2UbuntuFirmware.html">here</a>. I chose to use the Ubuntu 20.04 mainline kernel operating in headless/server mode from <a href="https://dl.khadas.com/Firmware/VIM2/Ubuntu/SD_USB/VIM2_Ubuntu-server-focal_Linux-5.12_arm64_SD-USB_V1.0.5-210430.img.xz">here</a>. With the headless mode, you are still able to use the monitor as the VIM2 TTY terminal without a serial cable attached. Just plug the VIM2 in with a wireless keyboard, wireless mouse, HDMI and power via the USB-C and you are good to go!</p>\
</li>\
<li>\
<p>After downloading the OS image, flash it to the SD card. I used <a href="https://www.balena.io/etcher/">Balena Etcher</a>. You can also use CLI on Linux systems as detailed on the Khadas site <a href="https://docs.khadas.com/linux/vim2/InstallOsIntoSdusb.html">here</a>.</p>\
</li>\
<li>\
<p>Insert the SD card into the VIM2, plug in the USB-C for power and then press the power and reset buttons as described <a href="https://docs.khadas.com/linux/vim2/BootIntoUpgradeMode.html">here</a> on the Khadas site.!<br>\
<a href="https://scratchthecurious.com/content/images/2021/08/VIM2-Pic---Reset-and-Power-Buttons.png">VIM2-Pic---Reset-and-Power-Buttons</a></p>\
</li>\
<li>\
<p>The VIM2 board will boot up and eventually present you with a login. Default login and pass is &quot;khadas&quot; and &quot;khadas&quot;.</p>\
</li>\
<li>\
<p>After logging in, it's probably good practice to change the password with <code>passwd</code></p>\
</li>\
<li>\
<p>Now, if you want to use the on board wireless to connect to your home Wifi network you can easily set it up with the very convenient nmtui. Just use the command <code>sudo nmtui</code> and follow the menu commands to set up the Wifi as a client and add your SSID and passphrase.</p>\
</li>\
<li>\
<p>If, on the other hand, you want to set up the VIM2 as an AP, then you should follow the instructions as noted <a href="https://docs.khadas.com/linux/vim2/WifiApstaMode.html">here</a>. Which I modified to the following in order to broadcast 2.4GHz AND 5GHz:<br>\
<code>sudo nmcli con add type wifi ifname wlan0 con-name Hostspot autoconnect yes ssid mywifi<br>\
sudo nmcli con modify Hostspot 802-11-wireless.mode ap 802-11-wireless.band bg ipv4.method shared<br>\
sudo nmcli con modify Hostspot wifi-sec.key-mgmt wpa-psk<br>\
sudo nmcli con modify Hostspot wifi-sec.psk &quot;yourpass&quot;<br>\
sudo nmcli con up Hostspot</p>\
<p>sudo nmcli con add type wifi ifname wlan0 con-name Hostspot2 autoconnect yes ssid mywifi-5gz<br>\
sudo nmcli c modify Hostspot2 802-11-wireless.mode ap 802-11-wireless.band a 802-11-wireless.channel 149 802-11-wireless.powersave 2 ipv4.method shared<br>\
sudo nmcli con modify Hostspot2 wifi-sec.key-mgmt wpa-psk<br>\
sudo nmcli con modify Hostspot2 wifi-sec.psk &quot;yourpass&quot;<br>\
sudo nmcli con up Hostspot2<br>\
</code></p>\
</li>\
<li>\
<p>In order for a client to connect to the new AP and have access to the internet via eth0 (this is how my router config is set up, with the eth0 connected to WAN), then you will need to forward some packets.</p>\
</li>\
</ol>\
<!--kg-card-end: markdown--><p></p><p></p>
