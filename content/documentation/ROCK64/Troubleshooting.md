---
title: "Troubleshooting"
draft: false
menu:
  docs:
    title:
    parent: "ROCK64"
    identifier: "ROCK64/Troubleshooting"
    weight: 4
---

## HDMI output disconnects as soon as it connects

Some older monitors seemingly can get into a weird state wherein the ROCK64 is unable to establish a proper connection with them. User CounterPillow has seen this happen on an iiyama ProLite G2773HS connected over HDMI, and an Acer P225HQL connected over an HDMI to DVI adapter. The symptoms usually are that you see the monitor briefly turn on its backlight without displaying a picture, but then immediately either shutting off again or showing a "No Signal" message.

The solution is to completely power down your ROCK64, also removing its power source. Then plug in the monitor, and start up the ROCK64 afterwards. You should now be getting a picture again.

## Power button doesn't do anything on a short press (Linux)

Make sure the `rk805_pwrkey` module is loaded, e.g. with `lsmod | grep rk805_pwrkey`. The module auto loading of `rk805_pwrkey` was fixed in the following upstream kernels: v6.0/v5.15.66/v5.10.142/v5.4.213/v4.19.258/v4.14.293 so there should be no requirement for fixes with those kernels or newer. If it doesn’t show up, do a `modprobe rk805_pwrkey` as root. To make this persistent, create a `99-rk805_pwrkey.conf` in `/etc/modules-load.d/` with the content `rk805_pwrkey`

If it still doesn’t work, make sure your init system is actually listening to the button press. With systemd, check `/etc/systemd/logind.conf` and make sure it’s either all commented out (uses defaults) or contains something like `HandlePowerKey=poweroff`. You can change the short press action by setting `HandlePowerKey` to one of "ignore", "poweroff", "reboot", "halt", "kexec", "suspend", "hibernate", "hybrid-sleep", "suspend-then-hibernate", or "lock".

## Video output is glitchy during activity

If your video output glitches out while there is memory bandwidth pressure, the likely reason is that the video output (VOP) quality-of-service (QoS) registers aren’t set to high priority mode.

User CounterPillow submitted [a patch](https://overviewer.org/~pillow/up/c5179dcb67/0001-rockchip-rk3328-Set-VOP-QoS-to-high-priority.patch) to u-boot to fix this, but someone still needs to write a kernel patch to save/restore the QoS registers from the power domain driver.
