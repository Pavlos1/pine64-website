---
title: "PinePhone 1.2b R1318 backlight Hardware Fix"
draft: false
menu:
  docs:
    title:
    parent: "PinePhone/Repairs"
    identifier: "PinePhone/Repairs/PinePhone_1.2b_R1318_backlight_Hardware_Fix"
    weight:
aliases:
  - /documentation/PinePhone/Hardware_fixes_and_mods/PinePhone_1.2b_R1318_backlight_Hardware_Fix/
  - /wiki/PinePhone_1.2b_R1318_backlight_Hardware_Fix
---

This page details a hardware fix for an issue that was found on early [PinePhone](/documentation/PinePhone) hardware revisions (see [PinePhone](/documentation/PinePhone/Revisions/) for an overview of the different revisions) and has been fixed since the 1.2b hardware revision.

## Affected Units

1. PinePhone v1.2 - UBports Community Edition
2. PinePhone v1.2a - postmarketOS Community Edition

## Disclaimer

This fix requires desoldering a tiny resistor, therefore some experience with soldering is highly recommended.

## Issue description

When connecting a VBUS powered device to the affected devices, like USB-C docks or hubs, the brightness changes significantly. The cause of the issue was a VBUS_CTRL signal provided by ANX7688 to control VBUS power direction. The signal is connected to AXP803 to tell it to not trying to charge from VBUS when the VBUS is generated from the battery. The signal is connected to the PL9 pin of the SoC, withut there being a reason for that. VBUS_CTRL is a 3.3V signal and the PL port is a 1.8V port. The issue was introduced in the 1.2 board revision.

## Fix

The fix of the issue is to remove the R1318 resistor.

A detailed instruction will follow here.

{{< figure src="/documentation/images/Pinephone-pcb-r1318.png" caption="R1318 location on the board" width="400" >}}

## Sources and tutorials

* [Megi’s blog: Backlight changes when swithcing between USB-C power modes](https://xnux.eu/log/#022)
