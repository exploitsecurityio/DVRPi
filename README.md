# Damn Vulnerable Raspberry Pi (DVRPi)

## Overview

Damn Vulnerable Raspberry Pi (DVRPi) is a Raspberry Pi 4B firmware designed to teach hardware hacking through intentional vulnerabilities. The Unsecured UART Console challenge introduces learners to serial communication and physical interface exploitation, a common entry point for attacking embedded devices like IoT systems, routers, and industrial controllers.

Warning: DVRPi is for educational use only. Do not deploy on production or internet-facing systems, as it contains intentional vulnerabilities that will lead to compromise.

### Getting Started

> Currently there is a single challenge (Low).
> Step 1. Download the firmware
> Step 2. Check Image Integrity
> - sha256sum rpi4-debian-firmware.img.xz and check against sha256sum rpi4-debian-firmware.img.xz.checksum.txt
> Step 3. Place the SDCARD into your reader on host machine
  > To find your SDCARD
  > ```
  > lsblk
  > ```
> Step 4. Umount the SDCARD
> ```
> umount /<mountpoint>/rootfs
> umount /<mountpoint>/bootfs
> ```
> Step 5. Flash the image onto the SDCARD (using DD)
>
> Step 6. Verify 

Produced by: exploitsecurity.io
-<a href="https://twitter.com/b1ack0wl">b1ack0wl</a>
