# Packages for ALARM on Orange Pi 4/4B

Based on the great work by Nadia Holmquist Pedersen here: https://github.com/nadiaholmquist/pbp-packages

The packages work, I'm using my DTB on two Orange Pis and they work fine for the most part,
I'll try to fix any issues I can fix and once everything is working, evaluate sending packages
back to upstream for mainlining.

99% of the DTB comes from Armbian, with some changes to fix some issues. No warranties on code quality or
correctness, "it works on my machine". **Not enough effort was put into this**, otherwise you wouldn't find
it here but rather in the upstream kernel.

## Packages

### ap6256-firmware

Firmware for the Broadcom SDIO Wi-Fi+Bluetooth module.


### uboot-orangepi4

Mainline UBoot with minimal patches. Boots from both SD card and internal eMMC,

Everything seems to work except the recovery button. See below for a recovery method that works regardless
of the firmware that is flashed.

### uboot-orangepi4-nonfree

Mainline U-Boot with minimal patches, but using proprietary blobs for RAM init, first stage loader and trusted firmware.
RAM gets set up at ~1800MHz versus ~1600MHz with the FOSS ram init, not benchmarked.

~~It crashes when booting the firmware from eMMC and then the OS from the SD card, I haven't troubleshooted it.~~

**Update:** It only seems to crash when loading Armbian since they use non-standard stuff. Use their bootloader
or simply don't use that pile of duct-tape.

If you need to boot from SD card after flashing a broken firmware, you can flash a known-good U-Boot image to an SD card,
then using a needle you can short the test point `TP50265` to ground at the very first boot stages (look at the
board from the back, vertically, with Ethernet port at the bottom: the testpad is approx. 1cm above the right edge
of the SD card slot). Remove short as soon as you see UART output. The board will boot the firmware from the SD
card.

##### Why does this work?

RK3399's bootrom happily boots from the first valid-looking boot device, supporting SPI, eMMC, SD card and probably
others. `TP50265` is hooked up to the eMMC flash's clock line, shorting it to ground will glitch it, make it fail to
read temporarily so that the bootrom proceeds with the boot order to the SD card.

At that point you need to remove the short, otherwise U-Boot will not be able to probe it and it will hang.

### linux-orangepi4-dtbs

Builds the DTBs for Orange Pi 4 and Orange Pi 4B (the use the same DTB).

You should use it together with the official `linux-aarch64` package, the device is fully
supported by mainline Linux, just the DTB is missing.

#### eMMC HS400 Enhanced Strobe

The on-board eMMC supports "enhanced strobe" mode, which in my tests gives around extra 50MB/s for the built-in
eMMC card.

However, while it works consistently fine on my Orange Pi 4B, on my Orange Pi 4 it randomly fails to initialize
occasionally. Therefore it is disabled by default.

If you want to forcefully enable it on your device, add the following to your `boot.scr` script right after `fdt resize`:

```bash
fdt set /sdhci@fe330000 mmc-hs400-enhanced-strobe
```

If you instead have a board that is unstable with HS400, you may want to downgrade to HS200. In that case, add:

```bash
fdt rm /sdhci@fe330000 mmc-hs400-1_8v
```

to disable HS400.
