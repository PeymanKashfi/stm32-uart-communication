# STM32 UART Communication

A hands-on STM32 project demonstrating UART communication on the NUCLEO-L476RG, progressing from basic polling-based transmission and reception to interrupt-driven UART communication.

## Overview

This project explores UART communication between an STM32L476RG microcontroller and a PC using the NUCLEO-L476RG board's onboard ST-LINK Virtual COM Port.

The project includes:

- UART transmission using polling/blocking HAL functions
- UART reception using polling
- UART echo
- Interrupt-driven UART reception
- Interrupt-driven UART echo
- UART-to-USB Virtual COM Port hardware-path analysis
- Testing and validation using Tera Term

## Hardware and Tools

### Hardware

- NUCLEO-L476RG development board
- STM32L476RGT6 microcontroller
- Onboard ST-LINK debugger/programmer
- USB connection between the Nucleo board and PC

### Software

- STM32CubeIDE
- STM32CubeMX integrated with STM32CubeIDE
- STM32CubeL4 firmware package V1.18.2
- Tera Term
- KiCad
- Visual Studio Code
- Git and GitHub

## UART Configuration

USART2 is configured for asynchronous communication with the following settings:

- USART: USART2
- TX pin: PA2
- RX pin: PA3
- Baud rate: 115200
- Data bits: 8
- Parity: None
- Stop bits: 1
- Flow control: None
- Oversampling: 16
- USART2 clock source: PCLK1
- USART2 clock frequency: 4 MHz

The PA2 and PA3 signals are connected to the onboard ST-LINK circuitry, allowing USART2 communication with the PC through the ST-LINK Virtual COM Port without requiring an external USB-to-UART adapter.

## Hardware Communication Path

The UART communication path was traced using the official NUCLEO-L476RG (MB1136 C-05) schematic.

### STM32 to PC

```text
STM32L476 PA2 (USART2_TX)
        ↓
      USART_TX
        ↓
       STLK_RX
        ↓
       SB13
        ↓
      STLINK_RX
        ↓
STM32F103 PA3 (ST-LINK)
        ↓
       USB
        ↓
ST-LINK Virtual COM Port
        ↓
        PC
```

### PC to STM32

```text
PC
 ↓
ST-LINK Virtual COM Port
 ↓
USB
 ↓
STM32F103 PA2 (ST-LINK)
 ↓
STLINK_TX
 ↓
SB14
 ↓
STLK_TX
 ↓
USART_RX
 ↓
STM32L476 PA3 (USART2_RX)
```

The onboard ST-LINK MCU provides the bridge between the target STM32 UART signals and the USB Virtual COM Port. The UART signals are therefore not converted directly to USB by the STM32L476.

## Project Milestones

### 1. UART Transmission

Implemented basic UART transmission using the STM32 HAL blocking transmit function.

The STM32 periodically transmitted `Hello from STM32!` once per second.

The output was verified on the PC using Tera Term through the ST-LINK Virtual COM Port.

**Result:** PASS

### 2. Polling-Based UART Reception and Echo

Implemented single-byte UART reception using the blocking HAL receive function.

Each received byte was transmitted back to the PC, creating a simple UART echo.

This milestone demonstrated the behavior of blocking UART reception and the use of a one-byte receive buffer.

**Result:** PASS

### 3. Interrupt-Driven UART Reception and Echo

USART2 receive interrupts were enabled through the NVIC.

The receive operation is initially armed using `HAL_UART_Receive_IT(&huart2, &rxData, 1);`.

When a byte is received, the HAL receive-complete callback echoes the byte and re-arms interrupt-driven reception for the next byte.

This allows the main program to continue executing instead of blocking while waiting for incoming UART data.

**Result:** PASS

## Testing and Validation

UART communication was tested using Tera Term with the following terminal settings:

- Port: STMicroelectronics STLink Virtual COM Port
- Baud rate: 115200
- Data bits: 8
- Parity: None
- Stop bits: 1
- Flow control: None
- Local Echo: Off

Final validation included:

1. **Mixed text and numeric data** — A test string containing letters and numbers was transmitted from the PC and echoed correctly by the STM32.
   - Result: PASS

2. **Continuous character reception** — A longer sequence of characters was entered continuously and echoed without missing or duplicated characters.
   - Result: PASS

3. **Reset recovery** — The NUCLEO board was reset while Tera Term remained connected. After reset, interrupt-driven UART reception resumed correctly without reconnecting the COM port or reprogramming the board.
   - Result: PASS

All final UART validation tests passed successfully.

## Project Structure

- `firmware/` — STM32CubeIDE project, application source code, HAL configuration, and STM32CubeMX `.ioc` file
- `hardware/` — Hardware design files and KiCad schematic
- `hardware/schematic/` — KiCad source files and exported UART hardware-path schematic
- `docs/` — Project documentation
- `docs/reference/` — Official hardware reference documents used during the project
- `stm32-uart-project-workflow.md` — Detailed development, testing, and validation workflow
- `README.md` — Project overview and results

## References

The following official STMicroelectronics documentation was used during the project:

- **UM1724 — STM32 Nucleo-64 Boards User Manual, Rev 17**
  - Used to verify the NUCLEO-L476RG USART2 and ST-LINK Virtual COM Port configuration.
  - Relevant section: USART communication.

- **MB1136-DEFAULT-C05 — NUCLEO-L476RG Board Schematic**
  - Used to trace the UART signal path between the STM32L476RG target MCU and the onboard ST-LINK circuitry.
  - Used to verify the USART2 TX/RX connections through SB13 and SB14.

Local copies of the reference documents are stored in `docs/reference/`.

## Key Learnings

This project provided practical experience with:

- Configuring USART peripherals using STM32CubeMX
- Implementing blocking UART transmission and reception using STM32 HAL
- Understanding the difference between polling and interrupt-driven UART reception
- Configuring USART interrupts through the NVIC
- Using HAL callbacks for interrupt-driven communication
- Re-arming single-byte interrupt reception after each received byte
- Testing serial communication using a Virtual COM Port and Tera Term
- Reading official board documentation and tracing UART signals through a hardware schematic
- Creating a simplified KiCad schematic to document the communication path
- Using Git to separate, review, commit, and document development milestones
