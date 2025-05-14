---
title: "Hardware"
draft: false
menu:
  docs:
    title:
    parent: "QuartzPro64"
    identifier: "QuartzPro64/Hardware"
    weight: 3
---

{{< figure src="/documentation/images/Quartzpro64_soc_and_ram_resized.jpeg" caption="The SoC and RAM packages" width="200" >}}

## General

* RK3588 SoC (8 cores: 4x A76 at 2.4&nbsp;GHz + 4x A55 at 1.8&nbsp;GHz)
* Mali G610MC4 GPU (4x Valhalla cores)
* 16&nbsp;GB of LPDDR4X (SK hynix)
* 64&nbsp;GB eMMC (Foresee, soldered)
* 1x USB-C (with video-alt mode)
* 1x USB-C (FTDI debug UART, FT232RL)
* 1x USB 3.0
* 2x USB 2.0
* 1x HDMI in
* 2x HDMI out
* 1x PCIe 3.0 slot (open-ended)
* 1x SD / TF card slot
* 2x SATA ports
* 2x Gigabit Ethernet (1x from SoC, 1x on PCIe, RTL8211F, RTL8111HS)
* 1x Wi-Fi & Bluetooth module (AMPAK Tech AP6275PR3)
* 2x SMA Antenna
* 2x MIPI DPHY
* 1x MIPI D/C PHY
* 1x MIPI CSI
* 1x PWM Fan header (four pins)
* 1x RTC battery socket (CR1220, 3&nbsp;V, see QuartzPro64 board schematics PDF, page 21)
* 1x MIC (soldered)
* 1x audio output 3.5&nbsp;mm jack
* DC 12&nbsp;V power input

## Cooler

The board comes with two cooler mounts, a 4-hole mount that appears to be spaced 55x55mm apart, and the ~60mm diagonal "northbridge heatsink" mount the ROCKPro64 and Quartz64 Model A uses.

RK3588 is slightly (<1mm?) taller than the DRAM chips, use a thick enough thermal pad instead of thermal compound.

## UART

Plug in the USB-C port labelled "***DEBUG PORT***" on the QP64 board to another computer with a USB-A-to-C cable.

It will show up as a FT232 USB Serial adapter in `lsusb`:

```
$ lsusb
Bus 005 Device 027: ID 0403:6001 Future Technology Devices International, Ltd FT232 Serial (UART) IC
```

Baud rate is 1.5 mbauds or 1500000.

Ensure you have the driver module loaded:

```
. modprobe ftdi_sio
. lsmod | grep ftdi_sio
ftdi_sio               61440  0
usbserial              53248  1 ftdi_sio
usbcore               290816  7 ftdi_sio,usbserial,xhci_hcd,usbhid,usbkbd,usbmouse,xhci_pci
```

If the above is not working, check the required driver is supported by your kernel, using one of the following commands:

```
$ zgrep FTDI_SIO /proc/config.gz
CONFIG_USB_SERIAL_FTDI_SIO=m
$ grep FTDI_SIO "/boot/config-$(uname -r)"
CONFIG_USB_SERIAL_FTDI_SIO=m
```

Using the `dmesg` command, you should see something like the following:

```
[24784.535804] usb 5-3: new full-speed USB device number 3 using xhci_hcd
[24784.710714] usb 5-3: New USB device found, idVendor=0403, idProduct=6001, bcdDevice= 6.00
[24784.710723] usb 5-3: New USB device strings: Mfr=1, Product=2, SerialNumber=3
[24784.710725] usb 5-3: Product: FT232R USB UART
[24784.710727] usb 5-3: Manufacturer: FTDI
[24784.710728] usb 5-3: SerialNumber: A10LLO86
[24784.723858] usbcore: registered new interface driver usbserial_generic
[24784.723865] usbserial: USB Serial support registered for generic
[24784.725286] usbcore: registered new interface driver ftdi_sio
[24784.725295] usbserial: USB Serial support registered for FTDI USB Serial Device
[24784.725348] ftdi_sio 5-3:1.0: FTDI USB Serial Device converter detected
[24784.725368] usb 5-3: Detected FT232RL
[24784.731685] usb 5-3: FTDI USB Serial Device converter now attached to ttyUSB0
```

Then you can get console output from the QP64 with, for example:

```
screen /dev/ttyUSB0 1500000
```

## Mounting Holes

Mounting holes are 3&nbsp;mm in diameter, so standard standoffs can be used, preferably the 2.5&nbsp;mm variant. See also the PCB layout PDF files, linked in the  [documentation section of the Resources page](/documentation/QuartzPro64/Resources#documentation).

The height from the bottom of the PCB to the top of the USB ports as the tallest components is 18&nbsp;mm, which can be used as a data point for selecting the suitable standoff length to place some acrylic top cover over the board. However, that doesn’t account for the heatsink that needs to be mounted on the RK3588 SoC, for which a suitable rectangular hole can be cut in the top cover.

## Storage

{{< figure src="/documentation/images/Quartzpro64_emmc_resized.jpeg" caption="The eMMC on the dev board" width="200" >}}

* Soldered 64&nbsp;GB FORESEE eMMC chip, which comes pre-flashed with some Android build
* One microSD card slot
* Two SATA 3.0 ports (standard Molex power connector is not populated)

## Power

{{< figure src="/documentation/images/Power_and_switch.jpg" caption="Power switch & barrel connector" width="100" >}}

You can provide power to the board via the 12V barrel connector, it’s 5.5mm OD/2.1mm ID barrel 'coaxial' type "M" centre-positive, the ROCKPro64 5A power supply from the PINE64 store will work. (TODO: add alternative ways).

There is a hardware flip switch to power up / down the board.

## PMU

{{< figure src="/documentation/images/Quartzpro64_pmu.jpeg" caption="The PMU" width="100" >}}

2x RK806-2, not RK808 compatible. It’s a dual PMU configuration where one PMU is a subordinate of the other.

Verify this once we have access to SDK sources.

## Ethernet

The RGMII ethernet port (near the SDCARD socket) is working if you use neg2led’s linux-quartz64 repo.

The other port (near the sound jack) is hooked to the SoC via PCIe and is currently reported working (on the matrix channel) with latest neggles kernel.
