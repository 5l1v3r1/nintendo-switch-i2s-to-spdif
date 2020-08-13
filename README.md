# I2S to S/PDIF

I2S to S/PDIF conversion on **SiPeed Tang Nano** (GOWIN GW1N-LV1), mainly aims to convert Nintendo Switch's internal sound signal.


## Motivation

According to Nintendo, Switch supports USB DACs. However, it doesn't seem to support UAC 2 and 3 which are somewhat high-end. I've tried to connect all DACs I have but only cheap DACs worked nicely while high-ends didn't. I wonder if Nintendo knows why USB DACs are needed and how Switch's headphone output sounds like.

Full-digital sound output is easily achieved in TV mode. S/PDIF splitter from HDMI signal does it well. But how about non-TV (portable) mode? Only things Switch has are the terrible headphone output and incomplete UAC support.

Now it's the time to steal the digital sound signal directly from Switch.


## Overview

The spec of I2S signal:

 - Sampling rate: 48000 [Hz]
 - Bit depth: 16 [bit]
 - Channels: 2 [ch]
 - Bit clock: 48000 * 16 * 2 * 4 = 6.144 [MHz]

<img alt="Oscilloscope visualized the I2S signal" src="/img/osc.png" width="400px">

 - There is a Realtek ALC5639 (smart amp with I2C controls) in Switch.
 - The SoC (≒ NVIDIA Tegra X1) transmits the sound signal to it in I2S format.

(Off-topic: [NVIDIA Jetson TK1](https://github.com/torvalds/linux/blob/d4db4e553249eda9016fab2e363c26e52c47926f/arch/arm/boot/dts/tegra124-jetson-tk1.dts) has ALC5640 (RT5640) in it. It is close to ALC5639 and has identical footprint. Perhaps Nintendo imitated the design of a evaluation board of NVIDIA.)

The spec of the FPGA board:

 - Board: SiPeed Tang Nano
 - FPGA: GOWIN GW1N-LV1 (LittleBee series)

 - This repository has an FPGA design to convert I2S format to S/PDIF format.
 - Download GOWIN EDA (free) to synthesize and program the design.
 - It supports both TOSLINK (optical) and coaxial cable as a PHY.
 - **The RGB LED on Tang Nano is capable of transmitting S/PDIF signal.** Connect a cable to a DAC and press the another side to the LED. Sound should come out. How interesting!! :nerd:


### Step-by-step

1. Disassembly your Switch. [-> iFixit teardown](https://www.ifixit.com/Teardown/Nintendo+Switch+Teardown/78263)

1. Find the chip.

 - Prepare longer wires for convenience and extra length to guide the wires nearby the battery connector.

    <img alt="ALC5639" src="/img/alc5639.jpg" width="400px">

1. Solder wires for BCLK (bit clock), LRCLK (left-right channel clock), and SDATA (serialized data)

 - Solder them VERY CAREFULLY or Switch lose its voice parmanently.
 - Microscope is strongly suggested.

    <img alt="ALC5639 with soldered wires" src="/img/soldered.jpg" width="400px">

1. Guide the wires and connect with Tang Nano somehow

 - There is a tiny free space around the battery connector. Recommend you to guide wires here.
 - Mind your wires not to interfere with other structures.
 - Choose the pin to connect with Tang Nano as you like as most of the pins are available to use.

1. Build the circuitry

 - Schematic is TBA
 - [Generic TTL-to-SPDIF level converter](https://sound-au.com/project85.htm) uses logic ICs for driver but we can build without the IC.
    - Remove DC offset with a capacitor (0.1uF = 100nF is recommended)
    - Lower the voltage with a voltage divider
        - I've adjusted it with volumes to achieve 0.5Vpp.
        - I'm not sure about output impedance; it works anyways!
        - No problem with shorter cable out there but perhaps longer cable causes problem.
 - It'll be so simple with optical transmission; even the RGB LED on Tang Nano can handle it.

1. Open this repository with GOWIN EDA and synthesize the logic design

1. BOOM!

