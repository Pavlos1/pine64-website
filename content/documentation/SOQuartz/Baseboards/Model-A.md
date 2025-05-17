---
title: "SOQuartz Model-A Baseboard"
draft: false
menu:
  docs:
    title:
    parent: "SOQuartz/Baseboards"
    identifier: "SOQuartz/Baseboards/Model-A"
    weight:
---

{{< figure src="/documentation/SOQuartz/images/SOQuartz_model-A_baseboard.jpg" caption="SOQuartz Model-A Baseboard" width="400" >}}

* Model "A" Baseboard Dimensions: 133mm x 80mm x 19mm
* Input Power: DC 12V @ 3A 5.5OD/2.1ID (IEC 60130-10 Type A) Barrel DC Jack connector

## Storage

* microSD - bootable, support SDHC and SDXC, storage up to 2TB
* USB -	2 USB2.0 Host port

## Expansion Ports

* MiPi-CSI - 1x 2 lanes, 1x 4 lanes
* MiPi-DSI - 1x 2 lanes, 1x 4 lanes
* 2x20 pins "Pi2" GPIO Header
* PCIe 1x open ended slot
* 1x5 pin USB Expansion Header (J15)

## GPIO Pins

Attention! GPIOs are 3.3V!

Interesting alternate pin configurations are listed in [brackets].

| Assigned To | Pin no. | Pin no. | Assigned To |
| --: | :-: | :-: | --- |
| 3.3 V | 1 | 2 | 5 V |
| _[I2C2_SDA_M1]_ GPIO4_B4_3V3 <sup>a</sup> | 3 | 4 | 5 V |
| _[I2C2_SCL_M1]_ GPIO4_B5_3V3 <sup>a</sup> | 5 | 6 | GND |
| _[GPCLK0]_ GPIO4_A4_3V3 | 7 | 8 | GPIO0_D1_3V3 _[UART2_TX_M0]_ |
| GND | 9 | 10 | GPIO0_D0_3V3 _[UART2_RX_M0]_ |
| _[I2S1_MCLK_M1]_ GPIO3_C6_3V3 | 11 | 12 | GPIO3_C7_3V3 _[I2S1_SCLK_M1]_ |
| GPIO4_A0_3V3 | 13 | 14 | GND |
| GPIO4_A2_3V3 | 15 | 16 | GPIO4_A3_3V3 |
| 3.3V | 17 | 18 | GPIO4_A1_3V3 |
| _[SPI3_MOSI_M0]_ GPIO4_B2_3V3 | 19 | 20 | GND |
| _[SPI3_MISO_M0]_ GPIO4_B0_3V3 | 21 | 22 | GPIO4_B1_3V3 |
| _[SPI3_CLK_M0]_ GPIO4_B3_3V3 | 23 | 24 | GPIO4_A6_3V3 _[SPI3_CS0_M0]_ |
| GND | 25 | 26 | GPIO4_A7_3V3 _[SPI3_CS1_M0]_ |
| _[I2C3_SDA_M0]_ GPIO1_A0_3V3 <sup>a</sup> | 27 | 28 | GPIO1_A1_3V3 _[I2C3_SCL_M0]_ <sup>a</sup> |
| GPIO3_A5_3V3 | 29 | 30 | GND |
| GPIO3_D4_3V3 | 31 | 32 | GPIO3_D6_3V3 |
| GPIO3_D7_3V3 | 33 | 34 | GND |
| _[I2S1_LRCK_M1]_ GPIO3_D0_3V3 | 35 | 36 | GPIO3_D5_3V3 |
| GPIO3_D3_3V3 | 37 | 38 | GPIO3_D2_3V3 _[I2S1_SDI_M1]_ |
| GND | 39 | 40 | GPIO3_D1_3V3 _[I2S1_SDO_M1]_ |

### Notes

<sup>a</sup> pulled high to 3.3V through 2.2kOhm resistor

Source: Page 11 of the board schematics.

## USB Expansion Header

| Pin no | Assigned To |
| --- | --- |
| 1 | USB2_HOST_5V |
| 2 | HOST_DM3 |
| 3 | HOST_DP3 |
| 4 | GND |
| 5 | GND |

Source: Page 10 of the board schematics.
