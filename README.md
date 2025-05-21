# Damn Vulnerable Raspberry Pi (DVRPi)

## Overview

Damn Vulnerable Raspberry Pi (DVRPi) is a Raspberry Pi 4B firmware designed to teach hardware hacking through intentional vulnerabilities. The Unsecured UART Console challenge introduces learners to serial communication and physical interface exploitation, a common entry point for attacking embedded devices like IoT systems, routers, and industrial controllers.

Warning: DVHA is for educational use only. Do not deploy on production or internet-facing systems, as it contains intentional vulnerabilities that will lead to compromise.

## Challenge Details

- **Name:** Unsecured UART Console
- **Difficulty:** Low
- **Objective:** Gain root access to the Raspberry Pi 4B via the serial console and retrieve a hidden flag.
- **Flag:** DVHA_FLAG_UART:UART_ROOT_ACCESS
- **Tools Needed:**
  - USB-to-TTL serial adapter (e.g., CP2102 or FT232R, ~$5).
  - Serial terminal software (e.g., minicom or screen, free).
  - Jumper wires (optional, for connecting to GPIO pins).
  - Raspberry Pi 4B

- **Prerequisites:**
  - Basic Linux command-line knowledge.
  - Familiarity with serial communication concepts (baud rate, TX/RX).

- **Location in Firmware:** The UART interface is enabled on GPIO pins 14 (TX) and 15 (RX), with a root shell accessible via **/etc/inittab**.
  
## Background

**UART (Universal Asynchronous Receiver-Transmitter)** is a serial communication protocol used for debugging and interfacing with embedded systems. On the Raspberry Pi 4B, UART is exposed via GPIO pins 14 (TX) and 15 (RX), allowing direct communication with the system’s console. In real-world devices, unsecured UART interfaces often provide attackers with privileged access, as seen in cases like router firmware extractions [].

In this challenge, the DVHA firmware is configured to provide a root shell over UART without requiring authentication, simulating a common misconfiguration in IoT devices. Your task is to connect to the UART interface, access the root shell, and retrieve a flag stored in **/root/flag.txt**.

## Setup Instructions

Before starting, ensure you have the DVHA firmware flashed to an SD card and the Raspberry Pi 4B powered on. Follow these steps to set up your hardware and software:

1. Acquire a USB-to-TTL Adapter:
    - Purchase a 3.3V-compatible adapter (e.g., CP2102, FT232R). Avoid 5V adapters to prevent damaging the Pi’s GPIO pins.
    - Example: Adafruit CP2102.
2. **Install Serial Terminal Software:**
   
   - On Linux:
     
     ```
     sudo apt update
     sudo apt install -y minicom
     ```
   - On MAC:
     ```
     brew install minicom
     ```
   - Alternative: Use screen (pre-installed on most systems).

4. **Identify the USB-to-TTL Adapter:**
   
    - Plug the adapter into your computer’s USB port.
    - Check the device name:
    ```
    ls /dev/ttyUSB*  # Linux
    ls /dev/tty.*     # macOS
    ```
  - Example output: /dev/ttyUSB0 (Linux) or /dev/tty.usbserial-XXXX (macOS).

5. **Connect the Adapter to the Pi:**
   
  - Power off the Raspberry Pi 4B to avoid short circuits:
    ```
    sudo shutdown -h now
    ```
    - Locate GPIO pins 14 (TX, pin 8) and 15 (RX, pin 10) on the Pi’s 40-pin header (Pinout Reference).
    - Connect the adapter to the Pi using jumper wires:
    ```
    Adapter GND → Pi GND (e.g., pin 6).
    Adapter TX → Pi RX (GPIO 15, pin 10).
    Adapter RX → Pi TX (GPIO 14, pin 8).
    ```
    **Note:** Do not connect VCC (3.3V or 5V) from the adapter, as the Pi provides its own power.
    Example wiring:
    ```
    USB-to-TTL Adapter    Raspberry Pi 4B
    ------------------    ---------------
    GND                  GND (pin 6)
    TX                   GPIO 15 (pin 10)
    RX                   GPIO 14 (pin 8)
    ```

6. **Power On the Pi:**
   - Insert the SD card with the DVHA firmware.
   - Power on the Pi using a USB-C power supply.
