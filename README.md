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

# Fault Injection into VSDSquadronMini
A fault injection on a microcontroller refers to an intentional disturbance that causes deviation from correct execution of the operations as described by the hex file. The MCU may act abnormally or the result may be incorrect. Fault injection can be performed in several ways such as:
- Voltage Glitching: This involves briefly manipulating the voltage supply to a system or component, causing a temporary fault or error.
- Clock Glitching: This involves manipulating the clock signal to a system or component, causing a temporary fault or error.
- Electromagnetic Glitching: This involves introducing electromagnetic interference (EMI) into a system or component, causing a temporary fault or error.
- Optical Glitching: This involves manipulating the light signal to a system or component, causing a temporary fault or error.
The faults introduced into the components can be transient faults or persistent faults that remain in the system and exploit it repeatedly.

## Voltage Glitching on VSDSquadronMini
If the power supply to the MCU is interrupted for a very short amount of time, the behaviour of the chip may be changed and possibly instructions skipped. If the power supply to the microcontroller is interrupted, the voltage regulator and capacitors ensure that a stable voltage is supplied and hence glitching will not be achieved. To counter this, we can implement the following method where the power supply is cut off by shorting capacitors present at the output of the voltage regulators:

![voltage_glitching_concept](https://github.com/vishrutsm/vsdsquadron-mini-internship/assets/167020131/fc3cc46a-af03-4bcf-a167-871712eebc50)

The Data Sheet for CH32V003F4U6 suggests that, under normal temperature, typically 300us is needed for reset. It can be inferred that power cut(grounding the capacitors) for 300us would cause the MCU to reset.

![image](https://github.com/vishrutsm/vsdsquadron-mini-internship/assets/167020131/633a4e2e-cf55-410f-9a48-87d758f8c2fb)

Hence, to achieve glitching for skipping instructions, grounding must be done for less than 300us. Any particular instruction can be targeted by trial and error.
However, to manage such precise timing conditions, switching can not be manual and has to be controlled by external hardware such as FPGAs or other microcontrollers. (Pins of ESP8299 can be controlled to perform under the required timing conditons. However, the GPIOs could not drive the capacitor to ground, leaving the timing condition yet to be addressed.)

Here is a video demonstrating voltage glitching as per the above method but achieving only reset of the MCU as fault injection due to manual switching of ground:
