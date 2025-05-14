---
title: "UART adapter"
draft: false
menu:
  docs:
    title:
    parent: "PineTab-V"
    identifier: "PineTab-V/UART_adapter"
    weight: 4
---

The UART serves as a console for early boot and kernel messages, and also offers access to a root shell in the factory test image. 

## Connecting the UART adapter

{{< figure src="/documentation/PineTab-V/images/PineTab2_USB_UARTv2.jpg" caption="UART Adapter" width="450" >}}

The UART adapter allows access to the UART through the USB 2.0 Type-C port:

* Insert the UART adapter face-up in the USB 2.0 Type-C port (the USB port furthest from the power button).
* Connect a USB cable from another computer to the port on the adapter marked "DEBUG". The green LED on the adapter should light up. The other adapter port can be connected to USB power if you don’t want to run the tablet on its battery.
* In a terminal window on the other computer, use _tio_, _screen_, _minicom_, or another application that supports serial port communication to connect via USB serial at 115200 bit/s 8N1 (8 data bits, no parity, 1 stop bit):\
`tio /dev/ttyUSB0`\
`screen /dev/ttyUSB0 115200`\
`minicom -D /dev/ttyUSB0 -b 115200`
  * Install one of the above from your distribution’s package manager if none are already installed.
  * The device may have a different name, particularly if multiple USB serial devices are connected. On macOS it will have a name like `/dev/tty.usbserial-_nnnn_`.
  * If _Permission denied_ is reported, you may need to be added to the group that can access the port with a command like `sudo usermod -a -G dialout $USER`. Then log out and log back in or use a command like `newgrp dialout` to create a new shell in that group.
  * Ubuntu-based distro users may encounter the error, "cannot open /dev/ttyUSB0: No such file or directory". If this occurs, check the output of `sudo dmesg --follow` and unplug/replug the USB to look for any errors. If you see an error like, "usb 1-1: usbfs: interface 0 claimed by ch341 while 'brltty' sets config #1", then the brltty service is likely conflicting with this device. Brltty provides access to blind users who use a braille display; if you do not need this service, try disabling it using these commands:\
  `sudo systemctl stop brltty-udev.service`\
  `sudo systemctl mask brltty-udev.service`\
  `sudo systemctl stop brltty.service`\
  `sudo systemctl mask brltty.service`

The switch on the UART adapter has no function on the PineTab-V; the boot order is controlled by the Vol-/Vol+ switches.
