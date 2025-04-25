---
title: "Sound controls"
draft: false
menu:
  docs:
    title:
    parent: "PineCube/Tuning"
    identifier: "PineCube/Tuning/Sound_controls"
    weight:
---

Note that sound is only currently available with special patches on top of a 5.13.13 or higher kernel with Armbian or NixOS. Once you have a kernel that supports sound you can install alsa-utils to get the alsamixer tool. The following mixer settings have been found to work with both playback and record. Note that you’ll need to press F5 to get the capture controls and space bar to turn on/off capture for a device. The speaker dangles on a wire from the device. The microphone is located about 1cm below the lens on the front facing circuit board.

{{< figure src="/documentation/PineCube/images/Pinecube_sound_mixer.png" width="800" >}}
