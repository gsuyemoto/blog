---
title: Custom door chime using a Raspberry Pi
slug: custom-door-chime-using-a-raspberry-pi
date_published: 1970-01-01T00:00:00.000Z
date_updated: 2021-09-30T18:51:02.000Z
tags: #Import 2022-12-26 21:35
draft: true
---

 Using a magnetic (reed) door sensor and a(n) PIRultrasonic IR distance sensor to detect and entering and exiting status

A friend of mine asked me to hack a door chime that he found on Amazon to play a custom sound file. I decided to build one from scratch (almost) using a Raspberry Pi and some sensors. The sensors part of that statement is explicitly vague as it took me a bit of trial and error to determine what sensor really worked for my purpose.

Goal: make a door chime that plays a random selection from a list of sound files that are loaded on to a USB drive. Catch: the door sound is different depending on whether someone is entering or exiting.

 A quick search brought me to [this great tutorial on Adafruit](https://learn.adafruit.com/sitcom-sfx-door-trigger/code), but a.) it was coded using Python and b.) as I found out later, not entirely performant in my case with the PIR. I wanted this thing coded using Rust and so began my little journey...

To be continued...
