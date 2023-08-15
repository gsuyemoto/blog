---
title: Implementing RTSP from scratch using Rust
slug: rust-rtsp
date: 2023-08-15
draft: true
categories:
  - Rust
  - RTSP
  - Video
---

# Implementing RTSP from scratch using Rust
Looking into streaming video from a cheap camera I bought off of Amazon (a Topodoome, maybe model TD-J10A?). Anyways, after trying all of the current crates for Rust to get a simple RTSP connection ended in failure...

<!-- more -->

I decided to look into implementing the RTSP protocol myself. RTSP seems pretty similar to HTTP, and I was able to get a connection to the test camera using a simple TCP socket stream. I had tried using the 'reqwest' crate at first, but ran into problems. It appeared to me that 'reqwest' didn't provide enough low level controls to use with RTSP. This was immediately evident when I was looking around to change the scheme from 'HTTP' to 'RTSP' and didn't find a solution. Not sure if it was because I just missed something in the docs.

After moving on to std::io::tcpstream, things moved pretty quickly after that. You just need to find the correct commands/methods to send to the camera. Oh, all of this is assuming you know the correct IP and port for the camera to even start the RTSP communication. With the camera I was using, it provided ONVIF which in many cases, I think, use a form of ws-discovery for device enumeration. In one of my other blogs, I provide information of how to implement your own very basic ONVIF and device discovery using Rust.

The first command I sent to the camera was **OPTIONS**, which provides all of th available RTSP commands in the response. After **OPTIONS**, I sent **DESCRIBE** to the camera which provides full detail about the camera and available streams and codecs using SDP (Session Description Protocol). 

Using the information provided in the **DESCRIBE** command, I then send **SETUP** with an additional header called **Transport**. In the **Transport** header, I provide the request packet transport that I wish to receive packets via RTP, whether TCP or UDP.

I then send **PLAY** and will immediately start receiving RTP packets from the camera. When I tested this using a simple tokio::net::UdpSocket (as I had already used Tokio for my async main), I was able to see that my client was receiving UDP packets from the camera. These packets, according to the **DESCRIBE** response (SDP), would be using H264 in order to encode the stream. My next task, would be to figure out how to decode and display the packets.

In order to stream and decode, I tried at least 3 different FFMPEG crates. Of course, 'ffmpeg' crate was the first one to try, I think, and this one proved to be a big pain in the ass for me. Having little experience with FFMPEG (I haven't used it extensively in years, but at least way more so than gstreamer), I was quickly overwhelmed by the complexity needed to just receive the stream and decode the packets. I looked into 'ffmpeg-next', next, and this crate, IMHO, was worse. The API was kind of wonky for me (and maybe just me). I liked how the API was provided for the original ffmpeg crate, but there was just waaay too much boilerplate needed to get a stream going.

On to 'ac-ffmpeg' crate. This seem to be something that was simpler to set up a stream and therefore more appropriate to my initial testing. I just want to get some code banged out and see a picture! Alas, my very simple and naive implementation using this crate failed. I'm pretty sure it's because I haven't used the API correctly. In my early thoughts, I'm looking to use UDP for the RTP stream and will probably pursue this avenue instead of TCP. As a note, it appears that the UDP packets have at least a 12 byte header:
```
 0               1               2               3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|V=2|P|X|  CC   |M|     PT      |       sequence number         |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                           timestamp                           |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|           synchronization source (SSRC) identifier            |
+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+
|            contributing source (CSRC) identifiers             |
|                             ....                              |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```
Within the 12 byte required header, there is a 4 bit CC flag which indicates if there are additional CSRC headers. Each additional CSRC header would be 4 bytes and so if the CC flag indicates that there are 2 addtional CSRC headers, then you would be sent 4x2 additional header information in the packet. After the header info is the payload. In my basic testing with the Topodome camera and using Wireshark, I was able to see that all of the UDP packets received via RTP were only 12 bytes in length.

More to come... 
