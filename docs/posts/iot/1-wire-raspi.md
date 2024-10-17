---
title: DS2431 EEPROM and Raspberry Pi
slug: ds2431-raspi
date: 2023-11-01
---
  [YouTube Tutorial]: https://www.youtube.com/watch?v=8pt0_so2Vfw

# How to read the DS2431 EEPROM using a Raspberry Pi

In a previous blog post, I had outlined a way to read a DS2431 EEPROM chip using a Bus Pirate. Recently, I came across a [YouTube Tutorial] of a way to read a DS2433 EEPROM using a Raspberry Pi. And boy, was I glad I did...

<!-- more -->

Using the method mentioned in the video was so much easier to read the DS2431 that I had in my possession. It was just overall, writing and reading to the EEPROM a world of difference. The video was great, but there was one thing missing (an easy to read wire diagram -- ala Fritzing) and another simple change.

![DS2431 and Raspberry Pi](images/ds2431-raspberry-pi.png)
### Prerequisites:

1. Linux (e.g. *Ubuntu* as that's what I used)
2. Terminal program (e.g. *Screen* as that's what I used)
3. Midnight Commander (should be installed on Raspi by default)
4. Raspberry Pi
5. DS2431 EEPROM
6. 2.2k resistor
7. breadboard
8. 4 jumper wires (male to female)
9. Alligator clip
10. 1-2 IC wire test lead
11. USB for Raspberry Pi (mine needed a mini-B)

### Steps:

1. Make all connections as shown in the diagram above.
2. Plug in power for Raspi and follow directions of how to turn on 1-Wire shown in the YouTube video
3. Follow the video to use Midnight Commander to read and write to the EEPROM
