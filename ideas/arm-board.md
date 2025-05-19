---
title: Ideas - arm-board
date: March 10, 2025
tags: []
---

# ARM SOC will have following interface
- Multiple Cores
- Onchip PLL ( Phase Loop Lock) takes clock source from external source.
   The on-chip PLL typically receives an input clock from an external source (such as a crystal oscillator or 
   an external clock generator) and generates an output clock with a frequency that can be multiplied or divided by a specific factor.
- Flash Chip
- DMA Contoller
- GPIO
  - Simple Control of External Devices: LEDs 
  - Switches and Buttons: GPIO pins can detect when a button is pressed or a switch is toggled.
  - GPIO pins can be set to trigger interrupts, which allow the microcontroller to react immediately to events like a button press or sensor activation without constantly polling the pin.
- Peripherals
    UARTs
    SPI controllers
    I2C controllers

- SPI interface 
   - Full Duplex ( I2C is Half duplex ), High Speed, Multiple device support, master-slave architecture.
   - CPU act as master. Set up the SPI bus: The master device initializes the SPI bus by configuring its SPI hardware. 
     The communication parameters such as clock polarity, clock phase, bit order, and data rate are set up.
   - Used to interface 
        - Memory Devices : Flash Memory, EEPROM
        - Sensors - Temperature
        - Display 
        - RTC 

