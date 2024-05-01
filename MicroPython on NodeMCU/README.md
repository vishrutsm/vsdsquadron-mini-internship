# MicroPython on NodeMCU
## Components Required
- NodeMCU
- Micro USB Data Cable
- Computer
## Softwares Used
- [Visual Studio Code](https://code.visualstudio.com/)
- [ESP8266 Flasher](https://github.com/nodemcu/nodemcu-flasher/tree/master/Win64/Release)
- (optional) [Thonny](https://thonny.org/)
## Obtaining the Firmware
The latest stable generic version for ESP8266 can be obtained directly from [micropython.org](https://micropython.org/download/ESP8266_GENERIC/).
## Flashing the Firmware
- Connect NodeMCU to Computer
- Open ESP8266 Flasher
- In the _Config_ window, select the firmware .bin file downloaded from micropython.org
- Ensure that the port to which NodeMCU is connected is correctly chosen in the _Operation_ window. If the port cannot be detected, download the required drivers ([CH340C](https://www.wch-ic.com/downloads/CH341SER_EXE.html) in this case) and ensure that a data cable (not merely a power cable) is being used for connection.
- Click on _Flash_. The LED on NodeMCU starts blinking indicating that ESP8266 is being Flashed.
## Running MicroPython
- Open Visual Studio Code and install the [PyMakr](https://marketplace.visualstudio.com/items?itemName=pycom.Pymakr) extension
- Create a new project file from PyMakr and connect to NodeMCU port under the _DEVICES_ tab. If the port is not visible, click on _ADD DEVICES_ under the _PROJECTS_ tab and select the required port.
- Two files _boot.py_ and _main.py_ can be observed in the workspace.

The code in _boot.py_ is executed everytime the board is powered up, followed by the code in _main.py_, which is also executed everytime the board resets.
Alternatively, Thonny can be used to Flash the board and write Python codes in the IDE.

A snippet of implementation of MicroPython on NodeMCU can be seen in the video:


https://github.com/vishrutsm/vsdsquadron-mini-internship/assets/167020131/270e3105-5fc7-4b41-be06-03c43e656ff2

