Design and Implementation of Embedded Systems Applications using STM32
This repository contains source code and simulation resources for a set of embedded systems mini‑projects implemented using STM32 microcontrollers (STM32F401RE and STM32F103C6) and STM32CubeIDE. The focus is on GPIO control, display interfacing, ADC data acquisition, and timer/peripheral usage, with all projects verified using Proteus simulations.

Project Overview
The goal of this work is to bridge theoretical concepts of ARM Cortex‑M–based embedded systems with practical, working implementations. Using STM32 HAL drivers, each task demonstrates a specific peripheral or concept: GPIO, seven‑segment displays, character LCDs, ADC with sensors, and timer‑based control.

The projects are structured as independent tasks:

LED sequence pattern on STM32F401RE

Double seven‑segment counter with multiplexing

Custom characters on 16x2 LCD

ADC interface with LM35 temperature sensor and 20x4 LCD
(Tasks 5–7 continue with timers, servo PWM, and UART on STM32F103C6.)

Task 1 – LED Sequence Control (STM32F401RE)
Microcontroller: STM32F401RE (Cortex‑M4)

Toolchain: STM32CubeIDE + HAL

Hardware: 8 LEDs on GPIOA pins PA0–PA7 (active‑high)

Description:

Configures system clock via PLL and initializes GPIOA as push‑pull outputs.

Uses an integer array of bitmasks (0x01 to 0x80) and a loop to drive a “running light” effect from PA0 to PA7 with 200 ms ON/OFF delays.

Verified in Proteus; LEDs light sequentially and repeat continuously.

Key learning: Basic HAL initialization, GPIO configuration, timing with HAL_Delay, and mapping bit patterns to physical pins.

Task 2 – Double Seven‑Segment Display Counter (STM32F401RE)
Microcontroller: STM32F401RE

Display: 2‑digit common‑cathode 7‑segment display (00–99 counter)

GPIO: Segment lines distributed over GPIOC (B0, B1, B6), GPIOA (B2–B5), GPIOB (B7); digit control on PB pins CTL_1 and CTL_2.

Description:

Uses a union (Tyedef_PinConfig) to break a byte (segment code) into individual bits and map them to non‑contiguous GPIO pins.

Segment_HEX[] stores codes for digits 0–9; Display() performs time‑division multiplexing: refreshes ones and tens digits in quick succession.

A small loop prescaler controls counting speed from 00 to 99, then wraps to 00.

Key learning: Practical multiplexing, bit‑level mapping with C unions, and driving multi‑digit 7‑segment displays with limited/fragmented GPIO.

Task 3 – Custom Character Display on 16x2 LCD (STM32F401RE)
Microcontroller: STM32F401RE

Display: 16x2 character LCD (LM016L), 8‑bit interface mode

GPIO:

Data: PA4–PA11 (D0–D7, referenced via K1_Pin–K8_Pin array)

Control: PC1 (RS), PC2 (RW), PC3 (EN)

Description:

Implements low‑level LCD driver: LCD_Command, LCD_Data, and Data_Write that bit‑bangs 8‑bit values over GPIO.

Initializes LCD (clear, home, entry mode, function set, display ON).

Defines 5x8 CGRAM bitmaps for three custom icons: battery, heart, smiley.

LCD_Custom_Char() writes these bitmaps into CGRAM locations 0–2 and displays them at selected DDRAM positions on the first row.

Key learning: HD44780 LCD architecture (DDRAM vs CGRAM), manual 8‑bit parallel interface, and converting 5x8 pixel designs into hex patterns for custom glyphs.

Task 4 – ADC Interface with LM35 and 20x4 LCD (STM32F103C6)
Microcontroller: STM32F103C6 (Cortex‑M3, “Blue Pill”)

Sensor: LM35 temperature sensor on ADC Channel 0 (PA0)

Display: 20x4 character LCD (LM044L), 8‑bit mode

GPIO:

ADC input: PA0 (ADC1_IN0)

LCD data: PB0–PB7

LCD control: PA6 (RS), PA7 (EN)

Description:

Configures ADC1 for single‑channel, continuous conversion with right‑aligned 12‑bit data.

Uses polling (HAL_ADC_PollForConversion) to read raw ADC values (0–4095).

Displays fixed strings “STM32 Tutorials” and “Embedded Systems” on line 1 and line 2; line 3 shows label “ADC VAL:” plus the 4‑digit ADC count using disp_ADC_Val().

Cursor positions use standard 20x4 addresses (0x81, 0xC1, 0x94).

Key learning: STM32 ADC configuration, integer‑to‑ASCII conversion for display, and integrating mixed‑signal input (LM35) with a multi‑line LCD UI.

Microcontrollers and Tools
Aspect	STM32F401RE	STM32F103C6
Core	Cortex‑M4	Cortex‑M3
Used in tasks	Tasks 1–3 (LED, 7‑segment, LCD)	Tasks 4–7 (ADC, timers, servo, UART)
Typical usage here	GPIO, LCD, display logic	ADC, timers, PWM, UART
Simulation platform	Proteus Design Suite	Proteus Design Suite
The split was chosen because the Nucleo‑style F401RE is convenient for GPIO/LCD demos, while the F103C6 (“Blue Pill”) has better tutorial and simulation support for ADC, UART, and timers in Proteus.

Simulation vs Real Hardware
All tasks were developed and tested primarily in Proteus for cost, safety, and rapid experimentation. Timing, noise, and power behavior on real hardware may differ; further work can port these projects to physical STM32 boards for validation and calibration.
