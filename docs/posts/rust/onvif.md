---
title: Implementing ONVIF device discovery and streaming using Rust
slug: rust-onvif-camera-streaming
date: 2023-08-12
draft: true
categories:
  - Rust
  - ONVIF
  - Video
---

  [Topodome from Amazon]: https://www.amazon.com/dp/B08RXV5SGN?psc=1&ref=ppx_yo2ov_dt_b_product_details
  [WS-Discovery]: https://en.wikipedia.org/wiki/WS-Discovery
  [ONVIF's different implementations]: https://www.onvif.org/resources/
  [ONVIF application programmer's guide]: https://www.onvif.org/wp-content/uploads/2016/12/ONVIF_WG-APG-Application_Programmers_Guide-1.pdf

# Implementing ONVIF device discovery and streaming using Rust
I've always wanted to build my own NVR for a while. Fed up with having to pay monthly fees just so you can have a working security cam?? I am...
<!-- more -->

For those of you coming across this post and don't particularly want to build the whole ONVIF client from scratch can go to [ONVIF's different implementations] to find open source code written for you.

The man don't make it easy to set up your own decent security camera system for the home. By decent, I imply that the camera has face detection, camera multiplexing, and recording for over a day. These days I don't think that this is asking much, but in probably almost all cases with any of the cameras you can buy now, all of these features (except multiplexing) are "premium" features and require a monthly subscription.

I'm pretty convinced that manufactures implement purposely broken movement detection in their camera systems where such a "feature" is more annoying than anything close to useful. Face detection in the computer vision world is a solved problem and relatively easy to provide and paying a monthly fee to have it for my security cameras really irks me.

A big plus for me would be to implement extra stuff like object recognition (give a count of how many squirrels have been in my backyard today). Really, it's just for curiosity sake, but it might be kind of cool to have an inventory of what animals have been prowling around.

For my setup, I went online and purchased a security camera that uses the ONVIF protocol:
[Topodome from Amazon]

I'm a big Rust fan and so I wanted to implement my NVR using Rust only. Right off the bat, I assumed (hopefully not incorrectly) that it might be easier to implement the NVR using the ONVIF protocol as this provides a known protocol and implementations. After perusing the Rust crates available, I noticed on crates.io that there were 4 crates under "onvif" search results. Pretty slim.

Unfortunately, none of the crates worked for me. Not sure if it's because the camera that I purchased doesn't really implement the ONVIF protocol correctly, or I was just fat fingering some code. Anyways, on to writing some code to talk to this camera.

After banging out some code, I realized that "device discovery" and actually sending ONVIF commands to the camera were two pretty distinct concepts. As far as I can tell, device discovery is conducted via a SOAP UDP protocol following [WS-Discovery]. *After* you obtain the xaddrs URL from the device responses, you will switch from low level UDP requests to HTTP requests, but still using SOAP messages.

Here are some of the ONVIF commands:

GetDeviceInformation: This operation retrieves basic information about the device, such as its manufacturer, model, firmware version, serial number, and more.

GetCapabilities: This operation retrieves the capabilities of the device, including supported services, versions, and other device-specific features.

GetHostname: This operation retrieves the device's hostname.

GetNetworkInterfaces: This operation retrieves information about the network interfaces on the device, including IP addresses, MAC addresses, and network configurations.

GetSystemDateAndTime: This operation retrieves the current date and time settings of the device.

GetUsers: This operation retrieves information about the users configured on the device.

SetSystemDateAndTime: This operation allows you to set the date and time settings of the device.

SetUser: This operation allows you to create, modify, or delete user accounts on the device.

Reboot: This operation initiates a reboot of the device.


