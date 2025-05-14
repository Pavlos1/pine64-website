---
title: "Closed case UART"
draft: false
menu:
  docs:
    title:
    parent: "PineNote/Further_information"
    identifier: "PineNote/Further_information/Closed_Case_UART"
    weight: 5
aliases:
  - /wiki/PineNote/Hardware_Changes/Closed_Case_UART
---

Closed case debugging was brought up during a week of community schematic review of the PineNote. This page documents the discussion of this proposed issue and its resolution.

Dates on this page are relative to UTC-5.

## Background

When debugging bootloader and kernel initialization on a new hardware platform, a hardware UART is indispensable. The PineNote 1.1 required taking the device apart to access a hardware UART broken out on the motherboard. This did not seem tenable long-term, as developers usually don’t like taking their devices apart to debug bootloaders. Therefore, it is important to provide resources to debug the PineNote’s boot process such as a hardware UART.

Recent PINE64 devices have included a hardware UART connected to their 3.5mm TRRS jacks through a hardware switch. However, the PineNote doesn’t have an audio jack. It also doesn’t have a convenient place to put a hardware switch which is accessible without taking the device apart. The case only has the affordance for a single USB-C port.

## Discussion

### August 15, 2021

We decided to ask the PineNote product team to explore USB-C Debug Accessory Mode, where the product changes the USB-C port’s personality when both CC1 and CC2 are pulled high. In normal usage, either CC1 or CC2 will be floating since these are the connector rotation pins. When both are detected, there is a very good chance that a debug harness is connected.

This decision requires the PineNote motherboard to be able to detect an AND condition between CC1 and CC2 and connect one of the SOC’s UARTs to pins on the USB-C connector. In all other cases, the UART should be disconnected. It also requires the PINE64 Store ship a simple one-sided (no magical flipping here, sorry) connector board which breaks out USB 2.0 and UART.

### August 16-18, 2021

There were concerns that cheap USB-C cables have both CC1 and CC2 shorted together to save a wire. This may cause the PineNote to output 3.3v UART to a device that isn’t expecting it, assuming the two are plugged together with a nonstandard cable. This seems unfounded (or not enough of a problem to worry about on a large scale), since the USB-C specification states in section B.2.3.1, "The general concept for setting up a valid connection between a DTS and TS is based on being able to detect the typical USB Type-C termination resistances. However, detecting a Debug Accessory Mode connection requires that both CC pins must detect a pull-up (Rp) or pull-down (Rd) termination. A USB Type-C Cable does not pass both CC wires so a receptacle to receptacle Debug Accessory Mode connection cannot be detected."

There were concerns that checking CC1 and CC2 being pulled high was not strictly to USB-C standard, as detecting them being pulled low is mentioned in the standard. However, detecting a pull-up condition is all that is required. According to the USB-C spec, 'B.2.4.1.5.1 ("UnattachedDeb.SRC Requirements"), a Debug and Test System (DTS) that is a power source must pull CC1/CC2 up, while the Target System (TS) in Unattached.SNK is supposed to pull them low.' In English, this means that we’d only need to detect a pull-up condition on CC1 and CC2, meaning a logical AND between them is a sane solution.

It should be possible to make the UART connection and breakout cable magically flippable by connecting the UART multiple times to the USB-C port.

## Resolution

{{< figure src="/documentation/images/PineNote_USB-C_DAM_Schematic_Addition.png" caption="This proposed circuitry is added to the PineNote v1.1 schematic" >}}

On August 19, 2021, TL posted the results of the hardware team’s efforts. This includes "PineNote USB-C DAM Schematic Addition.png", shown on this page, as well as the following additional documentation:

* [Datasheet for ON Semiconductor NS5S1153 USB-C USB 2.0 and Audio Switch.](https://wiki.pine64.org/wiki/File:NS5S1153-D.PDF)

Additionally, the hardware team proposed designing a breakout board with pull-up resistors on CC1 and CC2 and the appropriate UART signals routed to headers on the board. Then a developer can bring their own USB TTL adapter. They almost definitely have at least one, or could access one for a low price.

dsimic commented that this proposed layout meant that a Google-designed SuzyQable would **not** work for debugging the PineNote. This was deemed acceptable, as long as it is clear to developers that the combination of PineNote and SuzyQable will not result in any useful debugging abilities.

Caleb commented that the Google Pixel series of devices bring out UART over the SB1 and SB2 pins on the USB-C connector. It was surmised that copying this layout would allow reusing hardware that developers already have, however there are no harnesses on the market that directly break out this interface. Instead, it seems that most developers use a USB-C breakout board and stuff wires in the appropriate place when needed[https://github.com/Peter-Easton/android-debug-cable-howto]. A similar approach could be taken with the proposed PineNote solution if someone did not wish to purchase the breakout board.
