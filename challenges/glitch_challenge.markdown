# Glitch Attack (GPIO)

- **Challenge ID**: 9
- **Difficulty**: Low
- **Category**: Hardware Hacking
- **Skills Tested**: GPIO manipulation, fault injection

## Description
The Glitch Attack (GPIO) challenge simulates a fault injection attack on a Raspberry Pi 4B. A binary (`/usr/bin/glitch_challenge`) monitors GPIO 21 (pin 40), pre-configured as an input with a pull-up resistor at boot, for a LOW signal triggered by shorting it to GND (pin 39). When detected, the binary outputs a flag to the UART console (`/dev/ttyS0`), pauses for 10 seconds, and exits. This challenge mimics real-world hardware vulnerabilities where physical manipulation bypasses security checks.

**Note**: Persistent GPIO issues (pins 18, 16, 20 reading ~0V) suggest a damaged Pi. Test with a new Pi if GPIO 21 fails.

## Objective
Short GPIO 21 (pin 40) to GND (pin 39) to trigger a LOW signal, capture the flag (`flag{DVRPi_FLAG_GLITCH:TIMING_BYPASS}`) from the UART console using `minicom`, and submit it via the DVHA dashboard.

## Requirements
- **Hardware**:
  - Raspberry Pi 4B with DVHA firmware (v1.0 or later).
  - USB-to-TTL serial adapter (e.g., CP2102, 3.3V) for UART output.
  - Jumper wire or push-button to short GPIO 21 to GND.
  - Multimeter to verify GPIO 21 voltage (~3.3V at boot).
  - 5V, 2.5A+ power supply to avoid undervoltage.
- **Software**:
  - Terminal emulator (e.g., `minicom` or PuTTY) for UART output.
  - SSH client for system access.
  - Python 3 with `RPi.GPIO` for scripted short simulation (optional).
- **Access**:
  - DVHA firmware image (`dvha-firmware.img.xz`) from [SourceForge](https://sourceforge.net/projects/dvha/).
  - Root access via SSH (UART console login disabled).

## Steps
1. **Set Up UART Monitoring**:
   - Connect the USB-to-TTL adapter:
     - TXD (adapter) → GPIO 15 (pin 10, RXD)
     - RXD (adapter) → GPIO 14 (pin 8, TXD)
     - GND (adapter) → GND (pin 6)
   - Open `minicom`:
     ```bash
     minicom -b 115200 -o -D /dev/ttyUSB0
     ```

2. **Access the Pi via SSH**:
   - Find the Pi’s IP using network scanning or DHCP logs.
   - Connect:
     ```bash
     ssh root@<pi-ip>
     ```

3. **Run the Binary**:
   - Execute:
     ```bash
     sudo /usr/bin/glitch_challenge
     ```
   - In `minicom`, expect:
     ```
     Glitch challenge started
     Monitoring GPIO 21 (input with pull-up set by pinctrl)
     Initial GPIO 21 state: HIGH
     ```

4. **Trigger the Glitch**:
   - **Manual**: Briefly (1–2 seconds) connect a jumper wire or press a push-button between GPIO 21 (pin 40) and GND (pin 39).
   - **Programmatic** (on a second Pi):
     - Save and run `glitch_script.py` (see Source Code):
       ```bash
       python3 glitch_script.py
       ```

5. **Capture the Flag**:
   - In `minicom`, expect:
     ```
     LOW trigger detected (short to GND)
     Success! Flag: flag{DVRPi_FLAG_GLITCH:TIMING_BYPASS}
     Pausing for 10 seconds to capture flag...
     Exiting after pause
     Exiting glitch challenge
     ```
   - Record the flag during the 10-second pause.

6. **Submit the Flag**:
   - Access the DVHA Flask dashboard (http://<pi-ip>:5000).
   - Submit the flag: `flag{DVRPi_FLAG_GLITCH:TIMING_BYPASS}`.

## Solution
**Spoiler Warning**: This section reveals the solution.

1. Flash the DVHA firmware to an SD card:
   ```bash
   xz -dc dvha-firmware.img.xz | sudo dd of=/dev/sdX bs=4M status=progress
   sync
   ```
2. Boot the Pi and verify GPIO 21 voltage (~3.3V) with a multimeter.
3. Set up UART with a USB-to-TTL adapter and open `minicom`:
   ```bash
   minicom -b 115200 -o -D /dev/ttyUSB0
   ```
4. SSH into the Pi:
   ```bash
   ssh root@<pi-ip>
   ```
5. Run the binary:
   ```bash
   sudo /usr/bin/glitch_challenge
   ```
6. Short GPIO 21 (pin 40) to GND (pin 39) for 1–2 seconds using a jumper wire.
7. Capture the flag from `minicom` output and submit it via the dashboard.

## Hints
- Ensure GPIO 21 reads ~3.3V at boot; if LOW, test with a new Pi.
- Use `minicom` correctly configured for 115200 baud to view UART output.
- The short must be brief (1–2 seconds) to avoid false triggers.
- Run the binary with `sudo` to access `/dev/mem`.

## Troubleshooting
- **No UART Output**:
  - Verify UART connections (GPIO 14/15, GND, 3.3V logic).
  - Check `/boot/cmdline.txt`:
    ```bash
    cat /boot/cmdline.txt
    ```
    - Ensure: `console=serial0,115200 console=ttyS0,115200 earlycon=pl011,0x3f201000`.
  - Test UART:
    ```bash
    echo "Test" > /dev/ttyS0
    ```
- **Binary Fails to Run**:
  - Verify permissions:
    ```bash
    ls -l /usr/bin/glitch_challenge
    ```
    - Expect: `-rwxr-xr-x`
  - Check errors:
    ```bash
    sudo /usr/bin/glitch_challenge
    ```
    - Look for `Cannot open /dev/mem`; ensure `sudo`.
- **Initial GPIO 21 State Reads LOW**:
  - If `minicom` shows:
    ```
    Initial GPIO 21 state: LOW
    Warning: GPIO 21 is LOW after retries; check pinctrl, shorts, or hardware issues
    ```
    - **Check Hardware**:
      - Disconnect all wires from GPIO 21 (pin 40).
      - Measure voltage between GPIO 21 and GND (pin 39); expect ~3.3V.
      - Inspect for solder bridges or damage.
      - Test with a new Pi (GPIO 18, 16, 20 failures suggest board damage).
    - **Verify pinctrl**:
      ```bash
      pinctrl get 21
      ```
      - Expect: `input pull-up`.
      - Check `/boot/overlays/gpio21-pullup.dtbo` and `/boot/config.txt` (`dtoverlay=gpio21-pullup`).
    - **Test GPIO State**:
      ```bash
      gpio mode 21 in; gpio read 21
      ```
      - Expect `1` (HIGH); if `0`, suspect hardware issue.
    - **Power Supply**:
      - Use 5V, 2.5A+ power supply.
      - Check:
        ```bash
        dmesg | grep voltage
        ```
- **Incorrect Flag Output**:
  - If garbled, recompile:
    ```bash
    ~/x-tools/aarch64-rpi4-linux-gnu/bin/aarch64-rpi4-linux-gnu-gcc -o glitch_challenge glitch_challenge.c
    sudo cp glitch_challenge /mnt/rootfs/usr/bin/glitch_challenge
    ```

## Resources
- **Hardware Hacking Handbook**: Fault injection techniques.
- **Raspberry Pi GPIO Documentation**: Pin configuration.
- **Linux pinctrl Documentation**: Device Tree and pinctrl usage.
- **DVHA Documentation**: `/usr/share/dvha/docs/challenges/glitch.md`.

## Submission
Submit the flag via the DVHA Flask dashboard (http://<pi-ip>:5000). Share solutions on DVHA GitHub discussions.

---

**Author**: DVHA Team  
**License**: MIT  
**Repository**: [DVHA GitHub](https://github.com/dvha/dvha)  
**Last Updated**: May 23, 2025