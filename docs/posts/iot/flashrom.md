Installed 1.4-dev from Github main branch, but unable to get flashrom to recognize or enable ch341a. Upon removing all 
of flashrom 1.4-dev and then reinstalling using apt-get (version 1.2 of flashrom), I was then provided double the amount of enabled programmers, including the ch341a.

When using the ch341a to read SPI EEPROM chips using the supplied adapter PCB pin breakout board and ribbon cable, you need to ensure that the board is placed closest to the main CH341A chip with pin1 on the same side as the clamp handle.

I've read in an article online that aluminum wiring should not exceed around 10-15cm and that you can get up to around 20cm using copper, so I'm not sure if I encounter problems with long ribbon whether I'll need to cut and splice it using copper wires.

Problems with CH341A:
* Overheats. CH341A gets REALLY hot.
* Recognizes chip at one point of CLI execution and then not the next.
