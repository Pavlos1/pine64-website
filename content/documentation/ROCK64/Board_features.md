---
title: "Board features"
draft: false
menu:
  docs:
    title:
    parent: "ROCK64"
    identifier: "ROCK64/Board_features"
    weight: 1
---

{{< figure src="/documentation/ROCK64/images/ROCK64_sideimg.jpg" caption="The ROCK64 and a size comparison" width="400" >}}

## Video

* Digital Video output up to 4K@60Hz
* 4K HDR @ 60fps
* H.264/AVC Base/Main/High/High10 profile @ level 5.1; up to 4Kx2K @ 60fps
* H.265/HEVC Main/Main10 profile @ level 5.1 High-tier; up to 4Kx2K @ 60fps
* VP9, up to 4Kx2K @ 60fps
* MPEG-1, ISO/IEC 11172-2, up to 1080P @ 60fps
* MPEG-2, ISO/IEC 13818-2, SP@ML, MP@HL, up to 1080P @ 60fps
* MPEG-4, ISO/IEC 14496-2, SP@L0-3, ASP@L0-5, up to 1080P @ 60fps
* VC-1, SP@ML, MP@HL, AP@L0-3, up to 1080P @ 60fps
* MVC is supported based on H.264 or H.265, up to 1080P @ 60fps

## Audio

* 3.5mm A/V Jack (Composite Video Output and RCA Stereo support using conversion cable)

## Network

* 10/100/1000Mbps Ethernet
* WiFi 802.11 b/g/n with Bluetooth 4.0 (optional USB dongle)

## Storage

* microSD - bootable, supports SDHC and SDXC and storage up to 256GB
* eMMC - bootable (optional eMMC Module)
* 128Mbit (16MB) on-board SPI flash memory (empty by default) - bootable? Usually accessible as a [Linux MTD](http://linux-mtd.infradead.org/doc/general.html) device at `/dev/mtd0`
* 1 USB3.0 Dedicated Host port
* 2 USB2.0 Dedicated Host port (top one is USB-OTG)

{{< figure src="/documentation/ROCK64/images/Rock64-emmc-disable-jumper.png" caption="Position of the two-pin header for disabling the optionally installed eMMC module (highlighted in red)" width="400" >}} 

Position of the two-pin header for disabling the optionally installed eMMC module (highlighted in red)

Optionally installed eMMC module can be disabled by placing a jumper onto the two pins on the separate two-pin header, located near one of the mounting holes and two side-mounted buttons. See the picture in this section, in which this two-pin header is highlighted in red.

## Expansion Ports

All GPIO pins, including UART, operate at 3.3V. (See VCCIO5 in the schematics.)

* 2x20 pins "Pi2" GPIO Header
* 2x11 pins "Pi P5+" GPIO Header (with 2nd 10/100Mbps Ethernet pins)
