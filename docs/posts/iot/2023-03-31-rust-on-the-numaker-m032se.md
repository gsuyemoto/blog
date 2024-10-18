---
title: Rust on the NuMaker M032SE
slug: rust-on-the-numaker-m032se
date_published: 2023-04-01T01:05:45.000Z
date_updated: 2023-04-01T01:05:45.000Z
tags: #Import 2022-12-26 21:35
---

After doing some work on the Raspi with Rust, I decided I wanted to try a board that wasn't as ubiquitous. I found a company called Nuvoton that had some IoT boards based on the ARM Cortex chips, which was definitely a requirement.

I settled on the NuMaker M032SE mainly because I could get it easily from Digikey which has historically been very fast and pretty cheap to ship. Normally, I seem to get my orders from Digikey in a couple of days. In the future, if this board works out, I would like to check out some of Nuvoton's newer development boards and sensors.

Things you'll need for this project:

1. NuMaker M032SE Dev Board (I got it from [Digikey](https://www.digikey.com/en/products/detail/nuvoton-technology-corporation-of-america/NK-M032SE/12337437))
2. Rust - You can get the install script [here](https://www.rust-lang.org/tools/install)
3. [OpenOCD](https://github.com/OpenNuvoton/OpenOCD-Nuvoton) (Custom Nuvoton version)
4. GDB (Optional)
5. Micro USB cable that carries data and power (some only do power)
6. Ubuntu (most Debian distros can follow along fine I would think)

Step

We need to make sure Clang is installed in order to use Rust [Bindgen](https://rust-lang.github.io/rust-bindgen/). Bindgen will automatically generate all C bindings for the C libraries needed for this project (specifically, NuMicro.c).

`sudo apt-get install llvm-dev libclang-dev clang`

Step

Install some needed Rust crates:

`cargo install svd2rust form`

Step 1

Make sure you execute openocd as sudo!

`sudo openocd -f interface/nulink.cfg -f target/numicroM0.cfg`

Step 2

If you want to use GDB then you need to have [gdb-multiarch](https://packages.ubuntu.com/bionic/gdb-multiarch) installed. Not to be confused with the standard version of gdb. Although, if you want to just flash the program to the board, this is not needed. See Step 2A.

`gdb-multiarch -q target/thumbv6m-none-eabi/debug/examples/hello

(gdb) target remote :333`

Maybe try arm-none-eabi-gdb.

Step 2A (Flashing only)

If you only want to flash the program to the board, you can just use openocd to do [this](http://openocd.org/doc/html/Flash-Programming.html):

`openocd -f interface/nulink.cfg -f target/numicroM0.cfg -c "program filename.elf verify reset exit"`

NOTE: filename.elf is the name of the release/debug executable built with 'cargo build' and will not have the .elf extension on the file. I left this in the command above, but in the actual command it would just be the filename.

Currently, there are no PAC (Peripheral Access Crate) for the M032SE from Nuvoton – as far as I could tell. Because of this, you will have a good ol' time accessing the GPIOs for instance. Therefore, I used the Rust crate [SVD2Rust](https://crates.io/crates/svd2rust) in order to create a mapping from the corrresponding SVD file. I found the SVD file by going [here](https://developer.arm.com/tools-and-software/embedded/cmsis/cmsis-packs) and downloading the board pack (I think these .packs are for use with the Keil IDE). After downloading the .pack file, I renamed the file to packfile.zip and then just unzipped it. In the zip is a folder named 'SVD.'
