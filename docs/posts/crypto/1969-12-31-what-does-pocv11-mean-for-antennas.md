---
title: What does PoCv11 Mean for Antennas??
slug: what-does-pocv11-mean-for-antennas
date_published: 1970-01-01T00:00:00.000Z
date_updated: 2021-10-04T20:33:52.000Z
tags: #Import 2022-12-26 21:35
draft: true
---

An update to the Helium Proof-of-Coverage is being pushed to production and here is what you need to know if you are a hotspot owner or future hotspot owner (like many who are sitting on some pre-order list).

If you are like me and searched around the internet to get some information and reactions about the PoCv11 then you'll know that there is some conflicts of what the new PoC actually means.

The information for this article was obtained from mainly 2 sources: the [Helium Reddit](https://www.reddit.com/r/HeliumNetwork/comments/pwhobc/please_explain_poc11_coming_out_oct_4th_like_i_am/) and the [Helium Blog](https://blog.helium.com/pocv11-explained-call-to-action-4add36c75a1d).

According to the Helium blog:

> The team hasn’t finalized the date of activation, but it should be on or after October 4th

> in the US, the maximum EIRP is 36 dBm and the hardware is able to transmit at 27 dBm. If you’re operating a 9 dBi antenna, you can assert it without any transmission power loss

AND...

> transmit power higher than regional specific EIRP) will be considered invalid and therefore not earn any rewards. This is driven via the calculation of FSPL

One of the main takeaways for me was the mention of the EIRP or Effective Isotropic Radiated Power. Having gone through the [specs for all of the approved hotspots](__GHOST_URL__/breakdown-of-helium-hotspots/) I know that max output is 27dBi for all of them. If you combine losses (cables, connectors, etc.) and add your gain (antenna) to this number you will have your EIRP.

So, basically, you can use an antenna with 9dBi gain or less without having the Helium software throttle your hotspot's output power to ensure it does not exceed the EIRP of 36dBm. Although, if you have a long cable to connect your antenna to your hotspot, this will reduce the overall EIRP and therefore you will be able to use a higher gain antenna to make up for it (e.g. 10dBi).

I'm not sure why the information from Helium seems to conflict with say, TTN (The Things Network), where you can find this bit of [information](https://www.thethingsnetwork.org/docs/lorawan/regional-parameters/):

> The maximum radiated output power allowed in the USA is EIRP = +30 dBm but for most devices +20 dBm is sufficient. Under the Federal Communications Commission (FCC) there are no duty cycle limitations but there is a 400 ms maximum dwell time per channel. Dwell time is the amount of time needed for a transmission.

If anyone has a clue to why this is, please let me (and therefore us) know!
