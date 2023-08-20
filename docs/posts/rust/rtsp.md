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

  [rtp protocol spec]: https://www.rfc-editor.org/rfc/rfc3550
  [rtp h264 payload spec]: https://www.rfc-editor.org/rfc/rfc6184
  [rtp protocol spec wiki]: https://en.wikipedia.org/wiki/Real-time_Transport_Protocol
  [NAL (Network Abstraction Layer)]: https://en.wikipedia.org//wiki/Network_Abstraction_Layer
  [NAL Types]: https://yumichan.net/video-processing/video-compression/introduction-to-h264-nal-unit/
  [non-interleaved mode]: https://www.rfc-editor.org/rfc/rfc6184#section-6.3
  [rtp h264 payload spec section 7.1]: https://www.rfc-editor.org/rfc/rfc6184#section-7.1
  [rtp h264 payload spec section 5.4]: https://www.rfc-editor.org/rfc/rfc6184#section-5.4
  [RFC 6184 section 5.8]: https://www.rfc-editor.org/rfc/rfc6184#section-5.8
  [OpenH264 doesn't support yuvj420p]: https://github.com/cisco/openh264/issues/3511
  [ITU H.264 specification]: https://www.itu.int/rec/T-REC-H.264

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

When receiving the RTP packets, the codec provided in the SDP after the **Describe** command is sent, will determine how you handle the payload. With my test camera, I was informed by the camera RTP server that the RTP packets would enclose H264 encoded data. Using this information and also the 'packetization-mode=1' which means [non-interleaved mode] also provided in the SDP informed how I would proceed in decoding and displaying the video stream. Here are the 3 types of 'packetization-mode' keys:

packetization-mode=0: This is the Non-Interleaved Mode where only one slice of encoded video data is put into each RTP packet. This mode is primarily used for backwards compatibility with older systems.

packetization-mode=1: This is the Single NAL Unit Mode. In this mode, each RTP packet contains a full NAL unit, but unlike mode 0, it allows for more modern H.264 features like Constrained Baseline Profile and others. It's the most common mode used in many applications and systems today.

packetization-mode=2: This is the Interleaved Mode. In this mode, a single NAL unit can be split over multiple RTP packets. This mode is rarely used in practice.

Here is a NAL unit data scheme from the spec:

     0                   1                   2                   3
     0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
    |F|NRI|  Type   |                                               |
    +-+-+-+-+-+-+-+-+                                               |
    |                                                               |
    |               Bytes 2..n of a single NAL unit                 |
    |                                                               |
    |                               +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
    |                               :...OPTIONAL RTP padding        |
    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

!!! Note
	Informative note: The first byte of a NAL unit co-serves as the
      	RTP payload header.

In section 7.1 of the [rtp h264 payload spec section 7.1], the spec provides how to de-packetize the RTP and subsequently the NAL unit. According to the spec, after de-packetization, the NAL unit is passed directly to the decoder.

In section 8.2 of the [rtp h264 payload spec], the parameters for SDP as pertaining to the H264 video codec are outlined.

After some investigation using a program I wrote in Rust to capture IP camera streams, I was able to determine that the packets sent using the test camera were using NAL type 28 which is determined from the NAL unit header (the first byte), see [rtp h264 payload spec section 5.4] for NAL unit types, and which is designated as type FU-A, which are fragments.

NAL type unit FU-A packets can be empty. In order to decode, I will need to check for empty FU packets and discard if found. FU packets consist of the following:

     0                   1                   2                   3
     0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
    | FU indicator  |   FU header   |                               |
    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+                               |
    |                                                               |
    |                         FU payload                            |
    |                                                               |
    |                               +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
    |                               :...OPTIONAL RTP padding        |
    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

Which is found in [RFC 6184 section 5.8].

The FU header has the following format:

      +---------------+
      |0|1|2|3|4|5|6|7|
      +-+-+-+-+-+-+-+-+
      |S|E|R|  Type   |
      +---------------+

   S:     1 bit
          When set to one, the Start bit indicates the start of a
          fragmented NAL unit.  When the following FU payload is not the
          start of a fragmented NAL unit payload, the Start bit is set
          to zero.





Wang, et al.                 Standards Track                   [Page 31]

RFC 6184           RTP Payload Format for H.264 Video           May 2011


   E:     1 bit
          When set to one, the End bit indicates the end of a fragmented
          NAL unit, i.e., the last byte of the payload is also the last
          byte of the fragmented NAL unit.  When the following FU
          payload is not the last fragment of a fragmented NAL unit, the
          End bit is set to zero.

   R:     1 bit
          The Reserved bit MUST be equal to 0 and MUST be ignored by the
          receiver.

   Type:  5 bits

!!! Note
	The NAL Unit Type values are the same as those found in the regular NAL unit headers. 

OpenH264 from Cisco follows the Annex B bytestream format as reference at [ITU H.264 specification]. This is for encoding and decoding. When receiving the RTP packets from the camera server, each packet is it's only delineation for all H264 NAL units (which are the basic building blocks for the H264 stream). So, when taking these RTP packets and then attempting to feed the subsequent NAL units to the OpenH264 decoder, you need to preprocess the NALUs (NAL Units). Preprocessing the NALUs is actually fairly involved, for instance because of having to gather fragments (e.g. FU-A) and adding the NALUs delineators called start code prefixes (3 or 4 byte sequence of bytes, either 0x00 0x00 0x01 or 0x00 0x00 0x00 0x01). 

Start code prefixes: With start code prefixes, it depends on whether you are dealing with SPS/PPS packets. For SPS (NAL type 7) and PPS packets (NAL type 8), the prefix will be a 4 byte (0x00 0x00 0x00 0x01) start code and for all other NALUs the prefix start codes will be 3 bytes (0x00 0x00 0x01). As far as I can tell, the start of the entire bytestream will be a 4 byte prefix code, which I would think in almost every case would be a SPS unit anyways...

VCL and non-VCL: VCL units contain image data and non-VCL units contain metadata usually for the entire stream or in the case of AUD units are just another form of NALUs delineators.

