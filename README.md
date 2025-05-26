# Damn Vulnerable Raspberry Pi (DVRPi)

<img src="https://github.com/user-attachments/assets/18a4a5ad-4386-4c8f-b7f5-1d3423fda4c4" height=50% width=50%>

## Overview

Damn Vulnerable Raspberry Pi (DVRPi) is a Raspberry Pi 4B firmware designed to teach hardware hacking through intentional vulnerabilities. The Unsecured UART Console challenge introduces learners to serial communication and physical interface exploitation, a common entry point for attacking embedded devices like IoT systems, routers, and industrial controllers.

Warning: DVRPi is for educational use only. Do not deploy on production or internet-facing systems, as it contains intentional vulnerabilities that will lead to compromise.

### Getting Started

- Multiple Challenges to attempt.
- Write-ups available.

Step 1. Download the firmware

Step 2. Check Image Integrity

```
sha256sum rpi4-debian-firmware.img.xz
Validate against checksum.txt
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

Step 5. Sync Writes

 ```
 sync
 ```

Step 6. Uncompress and Flash the image onto the SDCARD (using DD)

  ```
  xz -dc rpi4-debian-firmware.img.xz | sudo dd of=/dev/sdX bs=4M status=progress
  ```

Step 7. Insert SDCARD into RPi and Follow challenge steps

**NOTE: Do NOT power on until all challenge details have been read**

---

[![Explore Now](https://img.shields.io/badge/Explore-Our_Site-blueviolet?logo=googlechrome&logoColor=white)](https://www.exploitsecurity.io)

[![Follow me on X](https://img.shields.io/badge/Follow-X-1DA1F2?logo=twitter&logoColor=white)](https://x.com/3xploit5ecurit7)

[![Connect](https://img.shields.io/badge/Connect-LinkdIn-0A66C2?logo=linkedin&logoColor=white)](https://www.linkedin.com/company/exploitsecurityio)

[![Join us on Discord](https://img.shields.io/badge/Join-Discord-5865F2?logo=discord&logoColor=white)](https://discord.gg/FvvRWhmr)

---

**Author**: [Exploit Security Team](https://www.exploitsecurity.io)  
**License**: GPL V3.0  
**Repository**: [DVRPi GitHub](https://github.com/exploitsecurityio/DVRPi)  
**Last Updated**: May 23, 2025
