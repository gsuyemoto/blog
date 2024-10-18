---
title: Raspberry Pi, ADC, ADS1115 and Rust. Oh my!
slug: raspberry-pi-adc-ads1115-and-rust-oh-my
date_published: 1970-01-01T00:00:00.000Z
date_updated: 2021-09-30T18:48:43.000Z
tags: #Import 2022-12-26 21:35
draft: true
---

Or, how to get some readings from an IR distance sensor (ADS1115) with a Raspberry Pi using good ol' Rust.

**Parts needed:**

---

**Crates needed:**

[embedded-hal](https://crates.io/crates/embedded-hal)

[ads1x1x](https://crates.io/crates/ads1x1x)

---

[Here](https://github.com/gsuyemoto/rust-pi-adc-ir-distance) is the complete code on Github.

So, related to my other project creating a custom door chime that detects whether a person is entering or exiting, I decided to try out an IR distance sensor. Still not quite satisfied with the performance of PIR nor the ultrasonic, here's hoping that this sensor will do the job.

Keeping in mind that the IR distance sensor that I chose only works with analog, I chose to add an ADC (Analog to Digital Converter) for the Raspberry Pi as it is unable to process analog inputs directly. I chose this particular ADC as there are Rust crates available to interface with it.

First, I need to add the crate dependencies to the Cargo.toml file:

    [dependencies]
    ads1x1x = "0.2"
    linux-embedded-hal = "0.3"
    embedded-hal = "0.2.4"
    nb = "1.0.0"

After a first 'cargo check' of the draft code, I came upon an error:

> thread 'main' panicked at 'Error setting i2c: No such file or directory (os error 2)'

After a bit of digging on Google, I found the [solution](https://www.raspberrypi.org/forums/viewtopic.php?t=115080). Basically, you need to do the following:

    sudo modprobe i2c-dev

After getting the dependencies set up, you can wire everything up. Here is a diagram of the wiring that I used:

After you get everything wired. You are ready to try the Rust code. I noticed upon using the ADC with the IR sensor that my raw readings from the ads1x1x crate was not what I was expecting. I had expected voltage readings, but it was apparent that the measurements returned were not voltages.

There does not seem to be much information online about getting the voltage reading from the IR sensor via an ADC using a Raspi and Rust. So, after coming across a discussion on [Sparkfun.com](https://www.sparkfun.com/products/8958#comment-4f864d34ce395f9161000000), especially this comment:
![](https://scratchthecurious.com/content/images/2021/04/Screen-Shot-2021-04-10-at-4.20.07-PM.png)
I decided to do something similar myself. Using 5 measurements from my setup at known distances of 10cm, 15cm, 20cm, 25cm, and 30cm, I then visited these sites:

[Logarithmic Regression Calculator on casio.com](https://keisan.casio.com/exec/system/14059930226691)

[Logarithmic Regression Calculator on stats.blue](https://stats.blue/Stats_Suite/logarithmic_regression_calculator.html)

Plugged in the measurements I took with the sensor in the 'x,y' tables and had it plot a best fit logarithmic line for me. Each site gave me the corresponding logarithmic function in the form of:

y = A + B * ln(X)

Using the constants for A & B that the calculators provided for me, I then added these to my Rust code to have it convert the readings provided by the ADC/IR sensor to distance in centimeters.

It works OK, but I think I need to implement a Kalman filter (maybe using [this](https://crates.io/crates/filter) Rust crate) in order to get the best results. I'll have to update this blog when I have done so. In order to use the Kalman filter, you really need to be able to provide it with the correct initialization AFAIK (which is not much! sadly). Or maybe I'll look into the OpenCV port of the Kalman filter in[ crates.io](https://crates.io/crates/kalman_filter) as there is probably much better documentation for the OpenCV version around. [Here](https://github.com/rlabbe/Kalman-and-Bayesian-Filters-in-Python) is a free book on Kalman and Bayesian filters.

Anyways, with the log regression function it works just OK, especially for what I'm using it for and especially if you're using the middle portion of it's spec'd range (this sensor is spec'd for 10-80cm and I'm going to keep to the 30-50cm range for detection).

---

Ok, after an initial draft of this blog, I decided to try the GP2Y0A02YK0F which as a spec'd sensing distance of between 20cm to 150cm. Switching to the A02 gave me a better sweet spot for the particular application I was going to use it for while basically using the same wiring and code.

They are not much different and size and form. Here is a picture:
![](https://scratchthecurious.com/content/images/2021/04/IMG_0984.jpg)[GP2Y0A02YK0F](https://www.mouser.com/datasheet/2/365/GP2Y0A02YK0F_13May05_Spec_ED-05G127-1652666.pdf) on the left and [GP2Y0A21YK0F](https://global.sharp/products/device/lineup/data/pdf/datasheet/gp2y0a21yk_e.pdf) on the right.
Also, instead of taking 5 of my own measurements as I did with the GP2Y0A21YK0F, I just used values from the datasheet (see link below). This might not be as tailored to the actual GP2Y0A02YK0F in my possession, but was much quicker and worked well for me.

---

Here are some additional references that can help:

1. Official datasheet for the [GP2Y0A21YK0F](https://global.sharp/products/device/lineup/data/pdf/datasheet/gp2y0a21yk_e.pdf)
2. Official datasheet for the [GP2Y0A02YK0F](https://www.mouser.com/datasheet/2/365/GP2Y0A02YK0F_13May05_Spec_ED-05G127-1652666.pdf)

---

Some pics:
![](https://scratchthecurious.com/content/images/2021/03/IMG_0946.jpg)![](https://scratchthecurious.com/content/images/2021/03/IMG_0947.JPG)![](https://scratchthecurious.com/content/images/2021/03/IMG_0948.jpg)![](https://scratchthecurious.com/content/images/2021/03/IMG_0949.jpg)![](https://scratchthecurious.com/content/images/2021/03/IMG_0945.jpg)
