# Packages for ALARM on Orange Pi 4/4B

Based on the great work by Nadia Holmquist Pedersen here: https://github.com/nadiaholmquist/pbp-packages

The packages work, I'm using my DTB on two Orange Pis and they work fine for the most part,
I'll try to fix any issues I can fix and once everything is working, evaluate sending packages
back to upstream for mainlining.

## Packages

### ap6256-firmware

Firmware for the Broadcom SDIO Wi-Fi+Bluetooth module.


### uboot-orangepi4

Mainline UBoot with minimal patches from Armbian. Boots from both SD card and internal eMMC,
rkusb/rkdevelop does not seem to work.

Should you need it you can flash another build to an SD card and press the recovery button,
it should work.


### linux-orangepi4-dtbs

Builds the DTBs for Orange Pi 4 and Orange Pi 4B (the use the same DTB).

You should use it together with the official `linux-aarch64` package, the device is fully
supported by mainline Linux, just the DTB is missing.

