## Challenge Details

- **Name:** Unsecured UART Console
- **Difficulty:** Low
- **Objective:** Gain access to the Raspberry Pi 4B via the serial console and retrieve a hidden flag.
- **Flag:** flag{DVRPi_FLAG_UART:UART_DVRPi_ACCESS}
- **Tools Needed:**
  
  - USB-to-TTL serial adapter (e.g., CP2102 or FT232R, ~$5).
  - Serial terminal software (e.g., minicom or screen, free).
  - Jumper wires (optional, for connecting to GPIO pins).
  - Raspberry Pi 4B (w/SDCARD)

- **Prerequisites:**
  
  - Basic Linux command-line knowledge.
  - Familiarity with serial communication concepts (baud rate, TX/RX).

- **Location in Firmware:** The UART interface is enabled on GPIO pins 14 (TX) and 15 (RX), with a root level shell accessible via **/etc/inittab**.
  
## Background

**UART (Universal Asynchronous Receiver-Transmitter)** is a serial communication protocol used for debugging and interfacing with embedded systems. On the Raspberry Pi 4B, UART is exposed via GPIO pins 14 (TX) and 15 (RX), allowing direct communication with the system’s console. In real-world devices, unsecured UART interfaces often provide attackers with privileged access, as seen in cases like router firmware extractions [].

In this challenge, the DVRPi firmware is configured to provide a root privileged shell over UART without requiring authentication, simulating a common misconfiguration in embedded or IoT devices. Your task is to connect to the UART interface, access the shell, and retrieve a flag stored in **/root/.flag.txt**.

## Setup Instructions

Before starting, ensure you have the DVRPi firmware flashed to an SD card and the Raspberry Pi 4B powered on. Follow these steps to set up your hardware and software:

1. Acquire a USB-to-TTL Adapter:
   
  - Purchase a 3.3V-compatible adapter (e.g., CP2102, FT232R). Avoid 5V adapters to prevent damaging the Pi’s GPIO pins.
  - Example: Adafruit CP2102.
      
3. **Install Serial Terminal Software:**
   
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
    ls /dev/tty.*    # macOS
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
    Adapter TX  → Pi RX (GPIO 15, pin 10).
    Adapter RX  → Pi TX (GPIO 14, pin 8).
    ```
  
  **Note:** Do not connect VCC (3.3V or 5V) from the adapter, as the Pi provides its own power.
  Example wiring:
  
  ```
  USB-to-TTL Adapter    Raspberry Pi 4B
  ------------------    ---------------
  GND                   GND (pin 6)
  TX                    GPIO 15 (pin 10)
  RX                    GPIO 14 (pin 8)
  ```

6. **Power On the Pi:**
   
  - Insert the SD card with the DVRPi firmware.
  - Power on the Pi using a USB-C power supply.

## Exploitation Steps

Follow these steps to exploit the unsecured UART console and retrieve the flag:

1. **Launch the Serial Terminal:**
   
  - Open minicom with the correct device and baud rate (115200, standard for Raspberry Pi):
  
    ```
    minicom -b 115200 -o -D /dev/ttyUSB0
    ```
  
  - Replace /dev/ttyUSB0 with your adapter’s device name.
  - If using screen:
  
    ```
    screen /dev/ttyUSB0 115200
    ```
  
  - Settings (optional, in minicom):
    
    - 8 data bits, no parity, 1 stop bit (8N1).
    - Disable hardware/software flow control.

2. **Access the Console:**

  - Upon connecting, you should see the Pi’s boot output, followed by a automatic login prompt:
  
    ```
    Debian GNU/Linux 12 DVRPi ttyS0
    DVRPi login: (automatic login)
    ```
  
  - The DVRPi firmware is configured to provide a low privilege shell without a password, so you’ll be logged in directly as **dvrpi:**
  
    ```
    root@DVRPi:~#
    ```

3. **Retrieve the Flag:**

  ```
  root@DVRPi:~# cat .flag.txt
  flag{DVRPi_FLAG_UART:UART_DVRPi_ACCESS}
  ```

4. **Exit the Console:**

  - Log out:
  
    ```
    exit
    ```
  
  - Exit minicom: Press Ctrl-A, then X, and confirm.
  - For screen: Press Ctrl-A, then \, and confirm.

## Technical Details

1, **Firmware Configuration:**
   
   - UART is enabled in /boot/config.txt:
     
  ```
  enable_uart=1
  ```
   
   - Permissive service, where a low privileged shell is provided via the autologin service:
  
  ```
  cat /lib/systemd/system/serial-getty@service
  [Service]
  ExecStart=-/sbin/agetty --autologin root --keep-baud 115200,57600,38400,9600 %I $TERM 
  ```
  
   - The flag is stored in /root/.flag.txt, accessible due to the unsecured console).

2. **Why Vulnerable:**

  - The UART interface lacks authentication, a common oversight in embedded devices designed for debugging [].
  - Developers often leave serial consoles enabled in production, assuming physical access is unlikely.

3. **Real-World Relevance:**

  - UART attacks have been used to extract firmware from routers (e.g., Netgear vulnerabilities []) and bypass authentication in industrial systems [].
  - Physical access is often feasible in scenarios like misplaced devices or insider threats.

## Mitigation Strategies

To prevent this vulnerability in production systems:

1. **Disable UART:**

  - Set enable_uart=0 in /boot/config.txt or disable the serial console in /etc/inittab.
  - Example:
  
 ```
 cat /boot/config.txt
 enable_uart=0
 ```

2. **Require Authentication:**

  - Configure a strong password for the serial console:
  
 ```
 passwd root
 ```

3. **Restrict Physical Access:**

  - Use tamper-evident seals or enclosures to deter unauthorized access to GPIO pins.

4. **Monitor Debugging Interfaces:**

  - Log serial console access attempts (requires custom kernel modules or hardware monitoring).

5. **Use Secure Boot:**

  - Implement secure boot to prevent unauthorized firmware modifications, even if UART access is gained.
