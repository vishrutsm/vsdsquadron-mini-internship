# Controlling VSDSquadronMini using MicroPython
## Overview
[MicroPython](https://micropython.org/) is an efficient implementation of Python3 optimized to run on microcontrollers. It is ideal to have a minimum of 256kb of codespace and 16kb of RAM for meaningful implementation.
It includes a small subset of the Python standard library and strives to be as compatible as possible with CPython(normal Python). This enables the implementation of a selection of Python libraries along with modules
for accessing low level hardware. With the required hardware resources in mind, the firmware is built for ESP8266 microcontroller and VSDSquadronMini is controlled using ESP8266.
## Components Required
- VSDSquadronMini Microcontroller
- ESP8266 Microcontroller
- Power and Connecting cables
## Table for Pin Connection
| VSDSquadron-Mini  | NodeMCU - ESP8266 |
| ----------------- | ----------------- |
| PD6               | TX                |
| PD5               | RX                |
| 3.3V              | 3.3V              |
| GND               | GND               |

## Pin Diagram
![image](https://github.com/vishrutsm/vsdsquadron-mini-internship/assets/167020131/f8c6f3a1-f485-4b10-9265-567244838a5a)

## Video Demonstration
A snippet demonstrating controlling LED on VSDSquadron-Mini using MicroPython


https://github.com/vishrutsm/vsdsquadron-mini-internship/assets/167020131/a519e72f-b789-4ff4-bb70-33d913f211de
