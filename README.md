# Damn Vulnerable Raspberry Pi (DVRPi)

## Overview

Damn Vulnerable Raspberry Pi (DVRPi) is a Raspberry Pi 4B firmware designed to teach hardware hacking through intentional vulnerabilities. The Unsecured UART Console challenge introduces learners to serial communication and physical interface exploitation, a common entry point for attacking embedded devices like IoT systems, routers, and industrial controllers.

Warning: DVHA is for educational use only. Do not deploy on production or internet-facing systems, as it contains intentional vulnerabilities that will lead to compromise.

## Challenge Details

- Name: Unsecured UART Console
- Difficulty: Low
- Objective: Gain root access to the Raspberry Pi 4B via the serial console and retrieve a hidden flag.
- Flag: DVHA_FLAG_UART:UART_ROOT_ACCESS
- Tools Needed:
  - USB-to-TTL serial adapter (e.g., CP2102 or FT232R, ~$5).
  - Serial terminal software (e.g., minicom or screen, free).
  - Jumper wires (optional, for connecting to GPIO pins).
