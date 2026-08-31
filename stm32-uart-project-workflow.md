# Project Workflow

## Define the Project

### Overview

UART (Universal Asynchronous Receiver/Transmitter) is a serial communication interface used for asynchronous data communication between devices.

Asynchronous communication does not use a shared clock signal; instead, the transmitter and receiver agree on communication settings such as the baud rate.

Each UART data frame typically contains:

- **Start bit:** Indicates the beginning of a data frame.
- **Data bits:** Carry the actual data being transmitted.
- **Parity bit:** An optional bit used for basic error detection.
- **Stop bit(s):** Indicate the end of the data frame.

### UART Frame

| Idle    | Start Bit      | Data Bits   | Parity (Optional) | Stop Bit(s)  | Idle    |
| ------- | -------------- | ----------- | ----------------- | ------------ | ------- |
| Waiting | Start of frame | Actual data | Error detection   | End of frame | Waiting |

This project explores UART communication on the STM32 NUCLEO-L476RG, including data transmission, reception, interrupt-driven communication, and testing.

### Objective

Develop, test, and validate UART communication on the STM32 NUCLEO-L476RG, progressing from basic data transmission and reception to interrupt-driven communication.

### Hardware

- STM32 NUCLEO-L476RG development board
- USB cable
- PC

### Development Tools

- STM32CubeIDE
- STM32CubeMX
- KiCad
- Git
- GitHub
- Visual Studio Code
- Serial terminal

### Project Scope

- Configure the UART peripheral on the STM32 NUCLEO-L476RG.
- Transmit data from the STM32 to a PC.
- Receive data from a PC on the STM32.
- Implement UART echo functionality.
- Implement interrupt-driven UART communication.
- Test and validate UART transmission and reception.
- Document the implementation, configuration, and test results.

---

## Create the GitHub Repository

### Repository Name

`stm32-uart-communication`

### Description

STM32 UART communication project covering transmission, reception, interrupt-driven communication, testing and validation.

### Visibility

Public

### Initialization

- README: Not initialized
- `.gitignore`: Not initialized
- License: None

---

## Clone the Repository

### Clone Method

HTTPS using Git from the VS Code integrated terminal (PowerShell).

### Local Parent Directory

`C:\peyman\work\embedded-systems-engineering\projects`

### Local Repository Directory

`C:\peyman\work\embedded-systems-engineering\projects\stm32-uart-communication`

### Project Workflow File

Moved the project-specific workflow file into the root of the cloned repository:

`stm32-uart-project-workflow.md`

---

## Create the STM32CubeIDE Project

### Workspace

`C:\Users\pkash\STM32CubeIDE\workspace_1.19.0`

Existing STM32CubeIDE workspace reused for this project. The workspace is kept separate from the Git repository.

### Project Name

`stm32-uart-communication`

### Microcontroller / Board

- Board: `NUCLEO-L476RG`
- MCU: `STM32L476RGT6`

### Project Location

`C:\peyman\work\embedded-systems-engineering\projects\stm32-uart-communication\firmware`

The STM32CubeIDE project is stored in the `firmware` subdirectory of the Git repository.

### Configuration

- Language: C
- Binary Type: Executable
- Project Type: STM32Cube
- Firmware Package: `STM32Cube FW_L4 V1.18.2`
- Code Generator: Copy only the necessary library files

---

## Set Up the Project Structure

### Directory Structure

Created:

- `docs/`
- `docs/reference/`
- `hardware/`
- `hardware/schematic/`

### Documentation Structure

Added:

- `docs/reference/um1724-stm32-nucleo64-boards-mb1136-stmicroelectronics.pdf`
- `docs/reference/mb1136-c05-board-schematic.pdf`

---

## Review Hardware Documentation

### Required Information

Determine how the `NUCLEO-L476RG` provides serial communication with a PC.

Identify:

- The STM32 peripheral used for serial communication
- The associated MCU pins
- The hardware connection between the target STM32 and ST-LINK
- The purpose of the connection
- Any required board-level configuration

### Documents Used

- `UM1724` — STM32 Nucleo-64 Boards (MB1136) User Manual
  - Revision: `17`
  - Relevant section: `7.10 USART communication`
  - Local file: `docs/reference/um1724-stm32-nucleo64-boards-mb1136-stmicroelectronics.pdf`

- `MB1136-DEFAULT-C05` — Board Schematic
  - Board revision: `MB1136-L476RG C-05`
  - Relevant circuit: USART2 / ST-LINK Virtual COM Port connection
  - Local file: `docs/reference/mb1136-c05-board-schematic.pdf`

### Findings

The `NUCLEO-L476RG` provides serial communication between the target STM32 and the ST-LINK MCU through `USART2`.

- Peripheral: `USART2`
- Pins:
  - `PA2` — USART2
  - `PA3` — USART2
- Default connection: Target STM32 ↔ ST-LINK MCU
- Purpose: ST-LINK Virtual COM Port
- Default solder bridge configuration:
  - `SB13`: ON
  - `SB14`: ON
  - `SB62`: OFF
  - `SB63`: OFF
- No solder bridge modification is required when using the default Virtual COM Port connection.
- The board schematic was reviewed to verify the USART2 signal path between the target STM32 and the ST-LINK circuitry.

---

## Configure the STM32 Project

### Peripheral Configuration

Configuration path:

`.ioc` → **Pinout & Configuration** → **Connectivity** → **USART2**

Configured:

- Peripheral: `USART2`
- Mode: `Asynchronous`
- Data Direction: `Receive and Transmit`

### Pin Configuration

CubeMX assigned:

- `PA2` → `USART2_TX`
- `PA3` → `USART2_RX`

### Parameter Configuration

Configured:

- Baud Rate: `115200 Bits/s`
- Word Length: `8 Bits (including Parity)`
- Parity: `None`
- Stop Bits: `1`
- Over Sampling: `16 Samples`
- Single Sample: `Disable`

UART format:

`115200 8N1`

### Clock Configuration

Verified in the `.ioc` **Clock Configuration**:

- USART2 Clock Source: `PCLK1`
- USART2 Clock Frequency: `4 MHz`

No USART2 clock configuration changes were required.

---

## Create the Schematic

### Schematic Tool

- KiCad 10.0

### Schematic Project

- Project name: `uart-hardware`
- Location: `hardware/schematic/`
- Main schematic file: `uart-hardware.kicad_sch`
- Purpose: Educational schematic showing the UART-to-USB VCP signal path on the NUCLEO-L476RG.

### Hardware Connections

#### Target MCU to ST-LINK MCU

Transmit path:

`STM32L476 PA2 (USART2_TX) → USART_TX → Top-Level connection → STLK_RX → SB13 (0R) → STLINK_RX → STM32F103 PA3 (RX)`

Receive path:

`STM32F103 PA2 (TX) → STLINK_TX → SB14 (0R) → STLK_TX → Top-Level connection → USART_RX → STM32L476 PA3 (USART2_RX)`

#### ST-LINK MCU to USB Connector

USB data connections:

`STM32F103 PA11 → USB_DM → R18 (0R) → CN1 D−`

`STM32F103 PA12 → USB_DP → R17 (0R) → CN1 D+`

Additional connection:

`CN1 GND → GND`

The USB connector provides the physical connection from the ST-LINK interface to the PC, allowing the UART communication to appear as a Virtual COM Port (VCP).

#### Power Connections

For the educational schematic:

- Main MCU VDD pins → `+3V3`
- Main MCU VSS pins → `GND`
- ST-LINK MCU VDD pins → `+3V3`
- ST-LINK MCU VSS pins → `GND`

Detailed board power circuitry is outside the scope of this schematic.

### Schematic Scope

This is not a complete reproduction of the NUCLEO-L476RG board schematic.

It documents only the hardware path relevant to the UART / ST-LINK Virtual COM Port communication used in this project.

Board circuitry unrelated to this signal path is intentionally omitted.

### Schematic Export

- Format: PDF
- Source schematic: `hardware/schematic/uart-hardware.kicad_sch`
- Exported file: `hardware/schematic/uart-hardware.pdf`
- Drawing sheet and title block included.
- All schematic pages exported using **Plot All Pages**.

---

## Add `.gitignore`

### `.gitignore` File

### Ignored Files and Directories

---

## Make the Initial Commit

### Files Included

### Commit Message

---

## Develop in Small Milestones

### Milestones

### Implementation Notes

---

## Test and Record Results

### Test Environment

### Test Cases

### Test Results

### Issues Found

---

## Update Documentation

### README

### Technical Documentation

### Test Documentation

---

## Push Changes to GitHub

### Branch

### Push Result

---

## Final Review

### Functionality

### Testing

### Documentation

### Repository

### Final Status
