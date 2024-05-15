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

## Programming the CH32V003F4U6 MCU on VSDSquadron-Mini
The following C code is built and flashed into VSDSquadron-Mini board using [MounRiver Studio](http://www.mounriver.com/) software.
    
   
```c  
    #include "debug.h"
    u8 Rxfinish1 = 0;
    u8 RxBuffer1[3] = {0};
    
    void GPIO_Config(void)
    {
    GPIO_InitTypeDef GPIO_InitStructure = {0}; //structure variable used for the GPIO configuration
    
    RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOD, ENABLE); // to Enable the clock for Port D
    
    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_0; // Defines which Pin to configure
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP; // Defines Output Type
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz; // Defines speed
    GPIO_Init(GPIOD, &GPIO_InitStructure);
    
    }
    
    void USARTx_CFG(void)
    {
        GPIO_InitTypeDef  GPIO_InitStructure = {0};
            USART_InitTypeDef USART_InitStructure = {0};
            NVIC_InitTypeDef  NVIC_InitStructure = {0};

        RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOD | RCC_APB2Periph_USART1, ENABLE);

        /* USART1 TX-->D.5   RX-->D.6 */
        GPIO_InitStructure.GPIO_Pin = GPIO_Pin_5;
        GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
        GPIO_InitStructure.GPIO_Mode = GPIO_Mode_AF_PP;
        GPIO_Init(GPIOD, &GPIO_InitStructure);

        GPIO_InitStructure.GPIO_Pin = GPIO_Pin_6;
        GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IN_FLOATING;
        GPIO_Init(GPIOD, &GPIO_InitStructure);

        USART_InitStructure.USART_BaudRate = 115200;
        USART_InitStructure.USART_WordLength = USART_WordLength_8b;
        USART_InitStructure.USART_StopBits = USART_StopBits_1;
        USART_InitStructure.USART_Parity = USART_Parity_No;
        USART_InitStructure.USART_HardwareFlowControl = USART_HardwareFlowControl_None;
        USART_InitStructure.USART_Mode = USART_Mode_Tx | USART_Mode_Rx;

        USART_Init(USART1, &USART_InitStructure);
        USART_ITConfig(USART1, USART_IT_RXNE, ENABLE);

        NVIC_InitStructure.NVIC_IRQChannel = USART1_IRQn;
        NVIC_InitStructure.NVIC_IRQChannelPreemptionPriority = 1;
        NVIC_InitStructure.NVIC_IRQChannelSubPriority = 1;
        NVIC_InitStructure.NVIC_IRQChannelCmd = ENABLE;
        NVIC_Init(&NVIC_InitStructure);;

        USART_Cmd(USART1, ENABLE);
    }
    
    void UartBufferSend(uint8_t* buffer, uint16_t length)
    {
        uint16_t tmp = 0;
        for(tmp =0; tmp < length; tmp++)
        {
            while(USART_GetFlagStatus(USART1, USART_FLAG_TXE) == RESET) /* waiting for sending finish */
            {
            }
            USART_SendData(USART1, buffer[tmp]);
        }
    
    }

    void USART1_IRQHandler(void) __attribute__((interrupt("WCH-Interrupt-fast")));
    
    void USART1_IRQHandler(void)
    {
        u8 RxCnt1 = 0;

    if(USART_GetITStatus(USART1, USART_IT_RXNE) != RESET)
    {
        RxBuffer1[RxCnt1++] = USART_ReceiveData(USART1);

        if(RxCnt1 >= 3)
        {
            RxCnt1 = 0;
            Rxfinish1 = 1;
        }
    }
    }
    
    int main(void)
    {
        NVIC_PriorityGroupConfig(NVIC_PriorityGroup_1);
        SystemCoreClockUpdate();
        Delay_Init();

    GPIO_Config();
    USARTx_CFG();
    Delay_Ms(1000);

    GPIO_WriteBit(GPIOD, GPIO_Pin_0, Bit_RESET);
    Delay_Ms(500);
    GPIO_WriteBit(GPIOD, GPIO_Pin_0, Bit_SET);
    Delay_Ms(500);
    GPIO_WriteBit(GPIOD, GPIO_Pin_0, Bit_RESET);
    Delay_Ms(500);
    GPIO_WriteBit(GPIOD, GPIO_Pin_0, Bit_SET);
    Delay_Ms(500);
    GPIO_WriteBit(GPIOD, GPIO_Pin_0, Bit_RESET);
    Delay_Ms(500);
    GPIO_WriteBit(GPIOD, GPIO_Pin_0, Bit_SET);


    while(1)
    {
        if(Rxfinish1)
        {

            if ((RxBuffer1[0] == 0x6f) && (RxBuffer1[1] == 0x66) && (RxBuffer1[2] == 0x66))
            {
                GPIO_WriteBit(GPIOD, GPIO_Pin_0, Bit_RESET);
                UartBufferSend("DONE\r\n", 6);
            }
            else if ((RxBuffer1[0] == 0x6f) && (RxBuffer1[1] == 0x66) && (RxBuffer1[2] == 0x66))
            {
                GPIO_WriteBit(GPIOD, GPIO_Pin_0, Bit_SET);
                UartBufferSend("DONE\r\n", 6);
            }
            else {
                UartBufferSend("FAIL\r\n", 6);
            }

            Rxfinish1 = 0;
        }
        else {
            USART1_IRQHandler();
        }
    }
    }
```
    

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

![voltage_glitching_concept](https://github.com/vishrutsm/vsdsquadron-mini-internship/assets/167020131/9d405898-113a-4c8d-9d73-948ffbb7aa4a)


The Data Sheet for CH32V003F4U6 suggests that, under normal temperature, typically 300us is needed for reset. It can be inferred that power cut(grounding the capacitors) for 300us would cause the MCU to reset.

![image](https://github.com/vishrutsm/vsdsquadron-mini-internship/assets/167020131/633a4e2e-cf55-410f-9a48-87d758f8c2fb)

Hence, to achieve glitching for skipping instructions, grounding must be done for less than 300us. Any particular instruction can be targeted by trial and error.
However, to manage such precise timing conditions, switching can not be manual and has to be controlled by external hardware such as FPGAs or other microcontrollers. (Pins of ESP8299 can be controlled to perform under the required timing conditons. However, the GPIOs could not drive the capacitor to ground, leaving the timing condition yet to be addressed.)

Here is a video demonstrating voltage glitching as per the above method but achieving only reset of the MCU as fault injection due to manual switching of ground:



https://github.com/vishrutsm/vsdsquadron-mini-internship/assets/167020131/1454121b-3d8d-4c32-9e11-430ef504bf03

