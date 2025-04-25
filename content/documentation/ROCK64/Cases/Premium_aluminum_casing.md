---
title: "Premium Aluminum Casing"
draft: false
menu:
  docs:
    title:
    parent: "ROCK64/Cases"
    identifier: "ROCK64/Cases/Premium_aluminum_casing"
    weight:
aliases:
  - /wiki/ROCK64_Aluminum_Case
  - /wiki/ROCK64_Premium_Aluminum_Casing
---

{{< figure src="/documentation/ROCK64/images/Rock64-Al-Case1-1.jpg" title="The case, fully assembled with a ROCK64 inside of it" >}}

The **ROCK64 Premium Aluminum Casing** is an aluminum case for the [ROCK64](/documentation/ROCK64) single-board computer sold by Pine64 through their [official store](https://pine64.com/product/model-b-premium-aluminum-casing/?v=0446c16e2e66). It includes an integrated heat spreader with an included thermal pad that allows the SoC to sink its generated heat into the metal case.

## Installation

Make sure the LED light guide and rubber plug are taken out of the case. Drop your ROCK64 into the bottom half of the case with the ports in the correct place. Tighten it with the four included round top screws, using a Phillips #00 screw driver.

Install the light guide and the rubber plug by pushing them in from the outside.

Ideally, clean the SoC of any possible residue with some isopropyl alcohol. Next, remove the plastic foil protecting the thermal pad on the top half of the case, and slot the top half into place, making sure that the HDMI and audio jack ports fall into their respective place in the side section (see [HDMI and Audio Jack Clearance Issues](#hdmi_and_audio_jack_clearance_issues) if they don’t.)

Once the top half is firmly in place, screw in the four flat top screws with a Phillips #00 screw driver.

## Performance

The built in heat spreader does do its job at sinking heat into the case. User:CounterPillow finds that during a lengthy kernel compilation at 22°C ambient temperature, the average SoC temperature measured around 60°C. The case during such sustained loads becomes warm to the touch, which means it is working as a heat sink.

## Tips and Tricks

### HDMI and Audio Jack Clearance Issues

Some cases may not allow the ROCK64 IO to snugly fit into its predestined cutout holes on its long side. If this is the case for you, try loosening the screws on the HDMI, power and audio jack port side, and only tightening the screws on the GPIO side to still make good ground contact. The HDMI and audio jacks should then fit into their holes snugly. If you wish to try, you can also try adjusting the screws holding in the posts on the bottom of the case.

### Plastic Pegs for PWR and RST Buttons

{{< figure src="/documentation/ROCK64/images/Button_peg_cad.png" title="FreeCAD displaying the peg. Bottom part goes inside the case." >}}

If you are tired of using a pen or a toothpick to push the buttons, there are [3d-printable plastic pegs you can download](https://wiki.pine64.org/wiki/File:Button.zip) and print yourself. Recommended material is PLA, excellent results can be obtained using a 0.25mm nozzle, though slightly larger sizes should work fine as well.

To use the pegs, open your case and remove the ROCK64 from within. Be sure to pull out the light guide and rubber plug to remove it, or else it will catch on them. Once it is removed, turn your bottom half of the case such that the holes for PWR and RST are pointing towards the floor, and drop the pegs into the holes from inside the case, so that the lip prevents them from falling through. Hold them in position with your finger, turn the case the right side up again, and reinstall your ROCK64.

{{< figure src="/documentation/ROCK64/images/ROCK64_Aluminum_Casing_Plastic_Peg_Mod_Photo.jpeg" title="Plastic Pegs in their place" >}}

You should now have buttons that can be pressed from outside the case without any utensils.

If you do not own a 3d printer, contact User:CounterPillow. He may mail you some for free in an envelope.

### Remove the black anodised layer from the heat pad

Anodising has a significantly poorer thermal conductivity than aluminium, therefore grinding off the anodising before fitting the blue thermal pad should result in a lower CPU temperature.

### Using the case with the PINE H64

Whilst the case is sold as a ROCK64 accessory, all the connectors and mounting screws on the [PINE H64](/documentation/PINE_H64_Model_B) are in the same spots. The only difficulty is that the H6 CPU is closer to the "Pi2" connector and extends beyond the edge of the heat spreader by about 5mm. User:AlephNull used a piece of copper shim on top of the H6 (with a smear of thermal paste) to spread the heat back to the thermal pad. In this configuration, the CPU stabilised at about 76°C running 4 copies of a CPU stress test. Whilst a lower temperature might be nice, this is still well below the 125°C limit.
