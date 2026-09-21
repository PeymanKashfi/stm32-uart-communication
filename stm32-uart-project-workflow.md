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

- STM32CubeIDE (including the integrated CubeMX `.ioc` configuration editor)
- KiCad
- Git
- GitHub
- Visual Studio Code
- Tera Term 5.7.0 x64

A separate standalone STM32CubeMX application is not required for this workflow because the required CubeMX configuration functionality is integrated into STM32CubeIDE.

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
  - `PA2` — `USART2_TX`
  - `PA3` — `USART2_RX`
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

A root-level `.gitignore` file was created to exclude generated, temporary, and machine-specific files from Git.

### Ignored Files and Directories

```gitignore
# KiCad temporary and local files
*.lck
*.kicad_prl
.history/

# STM32CubeIDE build output
Debug/
Release/

# Windows
Thumbs.db
```

Important project source and configuration files remain under version control, including the STM32CubeIDE project files, `.ioc` file, source code, KiCad project and schematic files, project documentation, and reference documents.

### Verification

The KiCad lock-file rule was verified with:

```powershell
git check-ignore -v hardware/schematic/~uart-hardware.kicad_sch.lck
```

Git confirmed that the lock file was ignored by the `*.lck` rule.

---

## Make the Initial Commit

### Files Included

The initial project structure, STM32CubeIDE firmware project, KiCad schematic project, reference documentation, project workflow, and `.gitignore` were staged and committed.

Before staging, `git status` showed the project files as untracked.

The files were staged with:

```powershell
git add .
```

The staged files were reviewed with:

```powershell
git status
```

### Commit Message

The initial commit was created with:

```powershell
git commit -m "Initial project setup"
```

After the commit, the working tree was clean.

### Initial Push

The first HTTPS push initially failed because GitHub password authentication is not supported for Git operations.

Git Credential Manager was configured with:

```powershell
git config --global credential.helper manager
```

The push was then retried:

```powershell
git push -u origin main
```

Browser authentication succeeded, the `main` branch was pushed to GitHub, and the local branch was configured to track `origin/main`.

A repeated push returned:

```text
Everything up-to-date
```

---

## Develop in Small Milestones

### Milestones

Planned implementation sequence:

1. UART TX using polling/blocking HAL transmission
2. UART RX using polling
3. UART echo
4. Interrupt-driven UART communication
5. Final testing and documentation

### Implementation Notes

#### Milestone 1 — UART TX

The first implementation milestone transmits a test message from the STM32 to the PC approximately once every second.

The following header was added inside the user include section:

```c
#include <string.h>
```

The transmit code uses USART2:

```c
char msg[] = "Hello from STM32!\r\n";

HAL_UART_Transmit(
    &huart2,
    (uint8_t *)msg,
    strlen(msg),
    HAL_MAX_DELAY
);

HAL_Delay(1000);
```

The firmware was successfully built in STM32CubeIDE and programmed onto the NUCLEO-L476RG using the onboard ST-LINK debugger/programmer.

During the first build attempt, the UART handle was accidentally written as `&huart`. The build error identified that `huart` was undeclared. The handle was corrected to `&huart2`, after which the project built successfully.

#### Milestone 2 — UART RX Using Polling

A one-byte receive variable was added before the main loop:

```c
uint8_t rxData;
```

Polling-based reception uses USART2:

```c
HAL_UART_Receive(&huart2, &rxData, 1, HAL_MAX_DELAY);
```

This blocking call receives one byte through USART2 and stores it in `rxData`.

#### Milestone 3 — UART Echo

UART echo was implemented with:

```c
HAL_UART_Receive(&huart2, &rxData, 1, HAL_MAX_DELAY);
HAL_UART_Transmit(&huart2, &rxData, 1, HAL_MAX_DELAY);
```

Inside `while (1)`, each received byte is immediately transmitted back to the PC. The firmware built successfully and was programmed onto the NUCLEO-L476RG.


#### Milestone 4 — Interrupt-Driven UART RX / Echo

USART2 interrupt handling was enabled in the `.ioc` configuration through:

`Pinout & Configuration → System Core → NVIC → USART2 global interrupt`

The generated configuration uses Preemption Priority `0` and Sub Priority `0`.

CubeMX generated the USART2 NVIC initialization and interrupt-handler support:

```c
HAL_NVIC_SetPriority(USART2_IRQn, 0, 0);
HAL_NVIC_EnableIRQ(USART2_IRQn);
```

```c
void USART2_IRQHandler(void)
{
    HAL_UART_IRQHandler(&huart2);
}
```

The receive variable was moved to private global scope:

```c
uint8_t rxData;
```

Interrupt-driven reception is initially armed before the main loop:

```c
HAL_UART_Receive_IT(&huart2, &rxData, 1);
```

The previous blocking polling receive/transmit operations were removed from `while (1)`.

The receive-complete callback echoes each byte and then re-arms reception for the next byte:

```c
void HAL_UART_RxCpltCallback(UART_HandleTypeDef *huart)
{
    if (huart->Instance == USART2)
    {
        HAL_UART_Transmit(&huart2, &rxData, 1, HAL_MAX_DELAY);
        HAL_UART_Receive_IT(&huart2, &rxData, 1);
    }
}
```

The `huart->Instance == USART2` check ensures that the callback processing applies to USART2. `HAL_UART_Receive_IT()` returns without blocking the calling code. After one byte is received, the receive operation completes, so the callback re-arms reception for the next byte.

---

## Test and Record Results

### Test Environment

- Board: `NUCLEO-L476RG`
- MCU: `STM32L476RGT6`
- UART peripheral: `USART2`
- UART format: `115200 8N1`
- PC interface: ST-LINK Virtual COM Port
- Windows COM port: `COM3`
- Serial terminal: Tera Term 5.7.0 x64
- Flow control: None

### Serial Communication Path

For the UART TX test, the communication path is:

`Application Code → USART2 → PA2 (TX) → ST-LINK VCP → USB → COM3 → Tera Term`

The onboard ST-LINK Virtual COM Port allows the PC to receive the target STM32 UART data through the board's USB connection. No external USB-to-UART adapter is required for this test.

### Test Cases

#### UART TX — Test Message

Transmit:

```text
Hello from STM32!
```

approximately once every second and verify that the message is received correctly by the PC serial terminal.

### Test Results

#### UART TX

**Result: PASS**

Windows detected:

`STMicroelectronics STLink Virtual COM Port (COM3)`

Tera Term was connected to `COM3` using:

- Speed: `115200`
- Data: `8 bit`
- Parity: `None`
- Stop bits: `1 bit`
- Flow control: `None`

The terminal displayed the expected message repeatedly:

```text
Hello from STM32!
Hello from STM32!
Hello from STM32!
```

This confirms successful UART transmission from the STM32 through the onboard ST-LINK Virtual COM Port to the PC.

#### UART RX / Echo

**Result: PASS**

Tera Term was connected to `COM3` at `115200 8N1` with no flow control. The STM32 successfully received individual characters using polling and echoed each byte back to the PC.

For troubleshooting, Tera Term `Local echo` was temporarily enabled. Typing `A` displayed `AA`: one locally displayed character and one character returned by the STM32. `Local echo` was then disabled, and typing `HELLO` displayed `HELLO` once, confirming the PC → STM32 → PC echo path.

#### Interrupt-Driven UART RX / Echo

**Result: PASS**

USART2 interrupt handling was enabled in NVIC and reception was changed from blocking polling to interrupt-driven one-byte reception using `HAL_UART_Receive_IT()`.

With Tera Term connected to `COM3` at `115200 8N1`, no flow control, and `Local echo` disabled, each typed character was received by the STM32 and echoed back to the terminal.

Continuous character entry was successful because `HAL_UART_RxCpltCallback()` re-armed `HAL_UART_Receive_IT()` after every received byte.

As an additional validation, disconnecting the board from USB stopped characters from appearing in Tera Term. With local echo disabled, this confirmed that the displayed characters were being returned through the STM32 UART echo path rather than displayed locally by the terminal.

### Issues Found

#### Incorrect UART Handle

Initial code used:

```c
&huart
```

This caused a build error because the generated USART2 handle is:

```c
&huart2
```

After correcting the handle, the firmware built successfully.

#### Serial Terminal Baud-Rate Mismatch

Tera Term initially used its default speed of `9600` baud while USART2 was configured for `115200` baud.

The COM port opened successfully, but the received characters were unreadable.

After changing Tera Term to `115200` baud, the text was displayed correctly.

This demonstrated that both ends of an asynchronous UART connection must use matching serial communication settings.

---

## Update Documentation

### README

To be completed after the main UART milestones are implemented and validated.

### Technical Documentation

Current technical documentation includes:

- Project-specific workflow
- Official NUCLEO-L476RG reference documentation
- Educational KiCad schematic
- Exported schematic PDF
- UART configuration, TX, RX polling, echo, and interrupt-driven RX/echo implementation notes in this workflow

### Test Documentation

UART TX configuration, serial connection, troubleshooting, and the successful TX test result are recorded in this workflow.

UART RX polling, echo, and interrupt-driven RX/echo implementation and successful test results are also recorded in this workflow.

---

## Push Changes to GitHub

### Branch

`main`

### Push Result

The initial project setup was pushed successfully to `origin/main`.

The UART TX milestone was committed with:

```text
Implement and validate UART TX
```

Commit:

`0719cbe`

The UART TX milestone was then pushed successfully to `origin/main`.

The UART RX / echo milestone was committed as:

`15d0565` — `Implement and validate UART RX echo`

The milestone was pushed successfully to `origin/main`.

The interrupt-driven UART RX / echo milestone was committed as:

`5047fe2` — `Implement and validate interrupt-driven UART RX echo`

The milestone was pushed successfully to `origin/main`.

After the push, the local `main` branch and `origin/main` were synchronized and the working tree was clean.

---

## Final Review

### Functionality

- UART TX: Complete and validated
- UART RX using polling: Complete and validated
- UART echo: Complete and validated
- Interrupt-driven UART RX / echo: Complete and validated

### Testing

- UART TX test: PASS
- UART RX polling test: PASS
- UART echo test: PASS
- Interrupt-driven UART RX / echo test: PASS

### Documentation

- Hardware documentation review: Complete
- Educational UART-to-USB VCP schematic: Complete
- `.gitignore` documentation: Complete
- UART TX implementation and test documentation: Complete
- UART RX polling and echo implementation/test documentation: Complete
- README: Pending
- Interrupt-driven UART RX / echo implementation/test documentation: Complete

### Repository

- GitHub repository created
- Initial commit completed
- Initial push completed
- UART TX milestone committed as `0719cbe` — `Implement and validate UART TX`
- UART TX milestone pushed successfully to `origin/main`
- UART RX / echo milestone committed as `15d0565` — `Implement and validate UART RX echo`
- UART RX / echo milestone pushed successfully to `origin/main`
- Interrupt-driven UART RX / echo milestone committed as `5047fe2` — `Implement and validate interrupt-driven UART RX echo`
- Interrupt-driven UART RX / echo milestone pushed successfully to `origin/main`
- Local `main` synchronized with `origin/main`
- Working tree clean after push

### Final Status

**Project in progress.**

The UART TX, polling-based UART RX, UART echo, and interrupt-driven UART RX / echo milestones are complete, validated, documented, committed, and pushed to GitHub.

The remaining project work is final testing, README completion, and final project documentation/review.

