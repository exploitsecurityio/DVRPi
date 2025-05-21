# Damn Vulnerable Raspberry Pi (DVRPi)

## Overview

Damn Vulnerable Raspberry Pi (DVRPi) is a Raspberry Pi 4B firmware designed to teach hardware hacking through intentional vulnerabilities. The Unsecured UART Console challenge introduces learners to serial communication and physical interface exploitation, a common entry point for attacking embedded devices like IoT systems, routers, and industrial controllers.

Warning: DVRPi is for educational use only. Do not deploy on production or internet-facing systems, as it contains intentional vulnerabilities that will lead to compromise.

### Getting Started

Currently there is a single challenge (Low).
Step 1. Download the firmware
Step 2. Check Image Integrity

```
sha256sum rpi4-debian-firmware.img.xz
Check against **rpi4-debian-firmware.img.xz.checksum.txt**
```
 
Step 3. Place the SDCARD into your reader on host machine
- To find your SDCARD
   ```
   lsblk
   ```

Step 4. Umount the SDCARD
 ```
 umount /<mountpoint>/rootfs
 umount /<mountpoint>/bootfs
 ```

Step 5. Flash the image onto the SDCARD (using DD)

  ```
  xz -dc rpi4-debian-firmware.img.xz | sudo dd of=/dev/sdX bs=4M status=progress
  ```

Step 6. Insert SDCARD into RPi and Follow challenge steps

Produced by: 
exploitsecurity.io

<a href="https://x.com/3xploit5ecurit7">Exploit Security on X</a>
