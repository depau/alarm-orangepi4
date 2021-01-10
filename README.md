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

Everything seems to work except the recovery button.

### uboot-orangepi4-nonfree

More recent U-Boot but using proprietary blobs for RAM init, first stage loader and trusted firmware.
RAM gets set up at ~1800MHz versus ~1600MHz with the FOSS ram init, not benchmarked.

It crashes when booting the firmware from eMMC and then the OS from the SD card, I haven't troubleshooted it.

If you need to boot from SD card after flashing this, you can flash a known-good U-Boot image to an SD card,
then using a needle you can short the test point `TP50265` to ground at the very first boot stages (look at the
board from the back, vertically, with Ethernet port at the bottom: the testpad is approx. 1cm above the right edge
of the SD card slot). Remove short as soon as you see UART output. The board will boot the firmware from the SD
card.

##### Why does this work?

RK3399's bootrom happily boots from the first valid-looking boot device, supporting SPI, eMMC, SD card and probably
others. `TP50265` is hooked up to the eMMC flash's clock line, shorting it to ground will glitch it, make it fail to
read temporarily so that the bootrom proceeds with the boot order to the SD card.

At that point you need to remove the short, otherwise U-Boot will not be able to probe it and it will hang.`

### linux-orangepi4-dtbs

Builds the DTBs for Orange Pi 4 and Orange Pi 4B (the use the same DTB).

You should use it together with the official `linux-aarch64` package, the device is fully
supported by mainline Linux, just the DTB is missing.

