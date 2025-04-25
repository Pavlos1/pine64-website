---
title: "Armbian Notes"
draft: false
menu:
  docs:
    title:
    parent: "PineCube/Software"
    identifier: "PineCube/Software/Armbian_notes"
    weight:
aliases:
  - /wiki/PineCube_Armbian_Notes
---

This article contains notes regarding Armbian on the [PineCube](/documentation/PineCube).

## General notes

[GitHub gist](https://gist.github.com/Icenowy/ff68f6e4ba8231380d3a295226e63fb3) for the user patch which pre-installs and configures the motion (detection) package.

A serial console can be established with 1152008N1 (no hardware flow control). Login credentials are as usual in Armbian (login: root, password: 1234).

Motion daemon can be enabled using systemctl (With root) `systemctl enable motion`. Set the video settings in /etc/motion/motion.conf to 640x480 15fps YU12. Then just reboot. Note that motion currently takes considerable resources on the pinecube, so you’ll want to stop it when doing things like apt upgrade and apt update with `systemctl stop motion` and then `systemctl start motion`

## Serial connection using screen and the Woodpecker USB serial device

First set the Woodpecker’s S1 jumper to 3V3. Then connect the Woodpecker USB serial device to the PineCube. Pin 1 on the PineCube has a small white dot on the PCB - this should be directly next to the micro-USB power connection. Attach the GND pin on the Woodpecker to pin 6 (GND) on the PineCube, TXD from the Woodpecker to pin 10 (UART_RXD) on the PineCube, and RXD from the Woodpecker to pin 8 (UART_TXD) on the PineCube.

On the host system which has the Woodpecker USB serial device attached, it is possible to run screen and to communicate directly with the PineCube:

`screen /dev/ttyUSB0 115200`

## Serial connection using screen and Arduino Uno

You can use the Arduino Uno or other Arduino boards as a USB serial device.

First you must either remove the microcontroller from it’s socket, or if your Arduino board does not allow this, then you can use wires to jump RESET (RST) and GND to isolate the SOC.

After this you can either use the Arduino IDE and it’s Serial monitor after selecting your `/dev/ttyACMx` Arduino device, or screen:

`screen /dev/ttyACM0 115200`

{{< figure src="/documentation/images/ArduinoSerialPinecube.jpg" width="400" >}}

## Serial connection using pinephone/pinebook pro serial debugging cable

You can use the serial console USB cable for PinePhones and Pinebook Pros at the [store](https://pine64.com/product/pinebook-pinephone-pinetab-serial-console/). With a [female terminal block](https://www.amazon.com/3-5mm-Stereo-Female-terminal-connector/dp/B077XPSKQD) wire using breadboard wire into the GPIO block at the following locations in a "null modem" configuration with transmit and receive crossed between your computer and the pinecube:

    S - Ground (to pin 9)
    R - Transmit (to pin 8)
    T - Receive (to pin 10)

From Linux you can access the console of the pinecube using the screen command:

`screen /dev/ttyUSB0 115200`

## Basic bandwidth tests with iperf3

Install armbian-config:
`apt install armbian-config`

Enable iperf3 through the menu in armbian-config:
`armbian-config`

On a test computer on the same network segment run iperf3 as a client:
`iperf3 -c pinecube -t 60`

The same test computer, run iperf3 in the reverse direction:
`iperf3 -c pinecube -t 60 -R`
