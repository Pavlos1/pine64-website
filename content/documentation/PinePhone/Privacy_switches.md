---
title: "Privacy switches"
draft: false
menu:
  docs:
    title:
    parent: "PinePhone"
    identifier: "PinePhone/Privacy_switches"
    weight: 7
---

{{< figure src="/documentation/images/PinePhone_Kill_Interruptors_de_Maquinari_del_PinePhone_4529.jpg" caption="Picture of the privacy switches" width="320" >}}

The PinePhone features six switches that can be used to configure its hardware. They are numbered 1-6, with switch 1 located nearest to the modem. Their "on" position is toward the top of the phone.

| Number | Name | Explanation | Description |
| --- | --- | --- | --- |
| 1 | Modem | Pulls Q1501 gate up (FET killing modem power) | "On" enables 2G/3G/4G communication and GNSS hardware, "off" disables it. |
| 2 | WiFi / Bluetooth | Pulls up CHIP_EN | "On" enables WiFi and Bluetooth communication hardware, "off" disables it. |
| 3 | Microphone | Breaks microphone bias voltage from the SoC | "On" enables audio input from on-board microphones (not 3.5 mm jack), "off" disables it. |
| 4 | Rear camera | Pulls up PWDN on OV5640 | "On" enables the rear camera, "off" disables it. |
| 5 | Front camera | Pulls up PWDN on GC2145 | "On" enables the front camera, "off" disables it. |
| 6 | Headphone | Pulls up IN2 on analog switch BCT4717ETB | "On" enables audio input and output via the 3.5 mm audio jack, "off" switches the jack to hardware UART mode. |
