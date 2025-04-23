---
title: "Bootloader improvements"
draft: false
menu:
  docs:
    title:
    parent: "PineTime/Discussions"
    identifier: "PineTime/Discussions/Bootloader_improvements"
    weight:
aliases:
  - /wiki/PineTime_bootloader_improvements
  - /wiki/Bootloader_improvements
---

Discussion by the user JF:

## Request for comments

In this page, I explain the changes and improvements I would like to implement to the MCUBoot bootloader to make it more reliable, and to allow bootloader upgrade and switch (mcuboot <-> wasp-bootloader, for example).
I try to explain why I made these choices too.

Do not hesitate to edit this page and add a new section if you want to add any comment to these points (if you agree, disagree, want to try something else).

## Introduction

This pages describes the improvements I propose to make to the bootloader and OTA to make the whole process more reliable.

My goals are:

* KISS: Keep It Stupid Simple. Simple code contains less bugs than complicated/complex code.
* As reliable as possible. 100% reliability is not feasible with the current hardware (no physical reset button, no bootloader in ROM code), but let’s try to reach 99.9% reliability
* Provide a read-only factory image we’ll be able to restore in case of unrecoverable errors during OTA
* Provide a way to upgrade the bootloader
* Provide a way to switch from MCUBoot to NRF bootloaders

## Current memory map

Pinetime Devkit0 is using a nRF52832-QFAA MCU with 512kB flash and a [XT25F32B SPI NOR external flash](https://datasheet.lcsc.com/szlcsc/2005251035_XTX-XT25F32BSOIGU-S_C558851.pdf) with 4096kB.

{{< figure src="/documentation/images/MemoryMap.png" >}}

Internal flash (512Kb):

* Page size is 4Kb.
* Bootloader contains the MCUBoot bootloader.
* Boot log is currently not used. Can be use to provide reboot logs to the application
* App is the PRIMARY SLOT for MCUBoot: the firmware that is currently running.
* Scratch is used by MCUBoot as temporary storage for swap operation

External flash (4096Kb):

* Page size is 256 bytes.
* Bootloader assets: boot logo
* OTA: SECONDARY SLOT for MCUBoot. During OTA, this is where the new version of the firmware is stored.
* FS: space available for the application firmware.

This memory map allows a strong boundary between the bootloader and the application :

* The bootloader is ran by the MCU at reset time and loads its boot logo from the SPI flash
* The bootloader (MCUBoot) swaps images from primary and secondary slots if necessary
* The bootloader starts the watchdog
* The bootloader jumps to 0x8000 to run the application

The application don’t know anything about the bootloader except that:

* it must be linked @0x8000
* it must refresh the watchdog periodically
* it must store the image for OTA at a specific location in the external SPI flash memory

In this configuration, the boot logo is part of the bootloader, not of the application: the same logo will be displayed whatever application firmware will be loaded.

## Changes

### Factory image

The factory image is a 'light' build of InfiniTime that contains the bare minimum code to provide OTA to the user: basic UI, BLE and OTA. No apps, no fancy functionalities.

This factory image will be stored in the "bootloader assets" area of the external flash memory.

This factory image will be restored either automatically, when the bootloader detect it cannot boot from primary slot (App in internal memory) and secondary slot (OTA from exeternal).
The user can also request to restore this factory image if necessary (using the button?).

### Bootloader assets

Graphics can be compressed using a simple RLE encoding. RLE encoding on 1 bits (2 colors images) is very efficient (115200KB -> 1-5KB).

In order to simplify the code, the graphics will be stored into the bootloader code instead of storing them into the external flash:
 - Less code.
 - Easier to document: 1 specific logo = bootloader mode
 - Do not have to worry about the dynamic size of the logo (the binary size of the graphics will vary with the content when they are RLE encoded).

### Bootloader

* Display the bootloader version on the screen and expose the version to the application firmware
* Provide a way to revert the firmware to the last working version AND to the factory firmware
* Display status and progression (progress bar similar to wasp-reloader, color code)
* Test as much as possible

### Bootloader workflow

{{< figure src="/documentation/images/PineTimeBootloaderWorkflow.png" >}}

### Upgrade & Reloader

The reloader is a tool that allows to upgrade the current version of the bootloader AND to switch from MCUBoot to NRF and vice versa.

#### How does it work?

* Upgrade
  * The reloader is first OTAed like any firmware upgrade.
  * When the system resets, MCUBoot swaps the application firmware with the reloader. The reloader upgrade the current bootloader and resets the system. It does NOT flag the image as validated. It resets the MCU.
  * The new version of MCUBoot notice that the last upgrade is not validated and reverts to the firmware that was running just before.
  * Voilà, you’re running your firmware and a new version of the bootloader
* Switch bootloader
  * The reloader is first OTAed like any firmware upgrade.
  * When the system resets, MCUBoot swaps the application firmware with the reloader. The reloader overwrite the current bootloader with a new one and reset.
  * The new bootloader is running.
* Switch
  * From InfiniTime to wasp-os: the reloader contains the NRF Bootloader and Softdevice. This bootloader provides the OTA mecanism out of the box. Wasp-os is downloaded when the NRF bootloader is running
  * From wasp-os to InfiniTime: the reloader contains the factory image (infinitime-factory). The complete version of InfiniTime will be OTAed when this factory image is running.

## Discussions

### Boot Logo: embedded into the bootloader binary vs stored in the external SPI flash
Embedding (and compressing) the boot logo inside the bootloader binary brings many advantages:

* All the data are available in memory at runtime. No need to load them & check them, and no need to handle errors and invalid corrupted data.
* The data is available and can be sent directly to the display controller
* 1 unique logo for the bootloader: easier to document and explain to the user that this specific logo is the logo from the bootloader mode.

But it also has some disadvantages:

* 1-Bit RLE encoding (very effective compression) allows only 2 colors (background/foreground)
* The boot logo cannot be customized (unless you recompile and flash this new build of the bootloader)
* The size of the boot logo is limited (depending on the compression ratio)

My (JF) point is that the bootloader must be as reliable as possible. I would like to remove all part of the code than can fail. If we read the boot logo from the SPI flash, we will write something like this:

```
 int ret;
boot_logo_info info;
ret = SpiNor_read(infoOffset, &info, sizeof(boot_logo_info));
if(ret != 0) {
  // Something went wrong while reading image info
  panic(); // ? reset ?
}

if(check_boot_logo_info(info) == false) {
  // image info are invalid (ex: size > 240*240), we cannot use them
  panic(); // ? reset ? Display nothing?
}
```

We could find invalid image info if a firmware did not respect the memory map and erased/overwrote the external memory map. In this case, the bootloader couldn’t run properly. Of course, we can implement something smart in panic() (retry, use failover values), but again, this adds complexity and bug probability.

All these if’s that call panic() can be avoided by using hard-coded values at build-time.

If the image is hard-coded, you won’t be able to easily (not that easy, actually) customize the boot logo. But remember that this logo is only display for a short time only when the device reset (manually or during an OTA).

### Why not add OTA functionality to the bootloader?

This is exactly how the NRF bootloader/SoftDevice works: the bootloader is a standalone firmware that provides OTA functionality thanks to the SoftDevice. The downside is that the BLE stack needed to provide OTA is quite big and uses a lot of space in flash memory (~124kB according to the documentation). This is roughly 1/4 of the available space in the internal flash memory.

Firmware based on the NRF SoftDevice share the BLE stack with the bootloader, it is mutualised between both entities.
The downside of this design is that firmwares developers are somewhat forced to use the NRF BLE stack. If they want to integrate another BLE stacak (NimBLE for example), these 120kB used by the SoftDevice would be wasted.

That’s why we decided to make the MCUBoot bootloader a simple bootloader without OTA functionality. It’s very lighweight (less than 20kB) and leaves the developers the right to choose the BLE stack they want.

### Fixed vs dynamic memory map

A dynamic memory map, using a partition stored in a fixed place (at the beginning of the external flash, for example) would allow different firmware to customize the partition table, image sizes and use the memory for efficiently.

But it has the downside to add complexity and code that could fail.

See [External flash partitioning](/documentation/PineTime/Flashing/External_flash_partitioning) proposal.
