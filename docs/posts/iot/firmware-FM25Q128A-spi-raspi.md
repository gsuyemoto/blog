---
title: Getting firmware from FM25Q128A using SPI and Raspberry Pi
slug: fm25q128a-spi-raspi
date: 2023-11-04
---
  [Flashrom report](https://mail.coreboot.org/hyperkitty/list/flashrom@flashrom.org/thread/MZNTGUPEQYJQ5OZWG43IWTOQQ43LTUQU/)
  [FM25Q128A Datasheet](https://datasheet.lcsc.com/lcsc/2109132030_FUDAN-MICRO-FM25Q128A-SOB-T-G_C908405.pdf)
  [SPI new signal names](https://www.sparkfun.com/spi_signal_names)
  [Additional Raspi pinout info](https://www.studiopieters.nl/raspberry-pi-4-model-b-pinout/)
  [Raspi pinouts for SPI](https://www.raspberrypi.com/documentation/computers/raspberry-pi.html)
  [Reading and writing using Flashrom](https://libreboot.org/docs/install/spi.html)
  [Detailed pinout for Raspi](https://www.tomshardware.com/reviews/raspberry-pi-gpio-pinout,6122.html)
  [Ultra small test clips](https://www.amazon.com/dp/B0B4DHVDRV?psc=1&ref=ppx_yo2ov_dt_b_product_details)
  [Waveshare USB to UART/I2C/SPI/JTAG docs](https://www.waveshare.com/wiki/USB_TO_UART/I2C/SPI/JTAG)
  [Update firmware Bus Pirate](https://github.com/BusPirate/Bus_Pirate/blob/master/Documentation/building-and-flashing-firmware.md)
  
# How I dumped the firmware from a FM25Q128A EEPROM using SPI and a Raspberry Pi 4

I've been taking a look at the internals for a security camera I purchased from Amazon for my home. The camera was a relatively unknown brand and had Onvif, which was my main prerogative. I had written my own Onvif client using Rust and wanted to interface to the Onvif server in the camera in order to implement my own computer vision functions (namely object detection).

<!-- more -->

After writing the client and doing some tests with a naive CV implementation of YoloV8, I started to notice some weird network traffic from the camera. Further investigation using Wireshark led me to some calls being made by the camera to several direct IPs located in the most likely in China.

Also, the camera was attempting constant DNS client calls in order to obtain the IP for a peer-to-peer service.

All of this did not sit well with me, and I immediately disconnecting both cameras (I had purchased 2) from my network until I looked into what was going on.

Tools:
1. [Ultra small test clips] from Amazon which were indispensible and made big difference in being able to get to the small legs on the EEPROM
2. Extra hands soldering tool. You really need a light and magnifier to make life easier for the EEPROM.


Notes:
1. flashrom was already installed on Raspberry Pi 4
2. even though, ROM model not detected, still able to read and write
3. Connections: 
  * SPI0 CE0 (CS)
  * VSS (Ground)
  * VCC (3.3V)
  * SPI0 MOSI (DI) -> OUT from Raspi to IN of camera
  * SPI0 MISO (DO) -> OUT from camera to IN of Raspi
  * SPI0 SCLK
4. Dumped flash ROM contents as .bin

````bash
flashromm -p linux_spi:dev=/dev/spidev0.0,spispeed=32768 -r dump.bin
````


