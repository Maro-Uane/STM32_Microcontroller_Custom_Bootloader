# STM32 Microcontroller Custom Bootloader

A bare-metal bootloader implementation for STM32F446RE Cortex-M4 microcontroller with UART-based firmware update capability.

## 📋 Overview

This project implements a custom bootloader that enables firmware updates over UART communication. Built using GCC for ARM and the STM32 HAL library, the bootloader provides a robust solution for field firmware updates without requiring external programming tools.

## ✨ Features

- **Automatic Bootloader Entry**: Triggered by user button press on reset
- **UART Communication**: Host-based firmware update protocol
- **Multiple Commands**: Comprehensive bootloader command set
- **Hardware Initialization**: Complete peripheral setup (HAL, GPIO, UART, CRC, Clock)
- **User Application Jump**: Seamless transition to main application
- **Flash Protection Management**: Read/Write protection control
- **Memory Operations**: Flash erase and write capabilities

## 🔧 Hardware & Tools

| Component | Description |
|-----------|-------------|
| **MCU** | STM32F446RE (Cortex-M4) |
| **Development Board** | NUCLEO-STM32F446RE |
| **Debugger/Programmer** | ST-Link |
| **Communication Interface** | UART |
| **Compiler** | GCC for ARM |
| **Libraries** | STM32 HAL |

## 🚀 Bootloader Flow

```
┌─────────────────┐
│   MCU Reset     │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Initialize     │
│  Peripherals    │
│ (HAL/GPIO/UART) │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  User Button    │◄──── Check on Reset
│   Pressed?      │
└────┬────────┬───┘
     │        │
    YES      NO
     │        │
     ▼        ▼
┌─────────┐ ┌──────────────┐
│Bootloader│ │ Jump to User │
│  Mode    │ │ Application  │
│(Wait CMD)│ └──────────────┘
└──────────┘
```

## 📡 Supported Bootloader Commands

| Command | Code | Reply | Description |
|---------|------|-------|-------------|
| **BL_GET_VER** | `0x51` | 1 byte | Read bootloader version |
| **BL_GET_HELP** | `0x52` | 10 bytes | List all supported commands |
| **BL_GET_CID** | `0x53` | 2 bytes | Read MCU chip ID |
| **BL_GET_RDP_STATUS** | `0x54` | 1 byte | Get flash read protection level |
| **BL_GO_TO_ADDR** | `0x55` | 1 byte | Jump to specified address |
| **BL_FLASH_ERASE** | `0x56` | 1 byte | Erase flash sectors |
| **BL_MEM_WRITE** | `0x57` | 1 byte | Write data to memory |
| **BL_EN_RW_PROTECT** | `0x58` | 1 byte | Enable Read/Write protection |
| **BL_READ_SECTOR_STATUS** | `0x5A` | 2 bytes | Read sector protection status |
| **BL_DIS_RW_PROTECT** | `0x5C` | 1 byte | Disable Read/Write protection |

## 📂 Project Structure

```
STM32_Bootloader/
├── Core/
│   ├── Inc/              # Header files
│   └── Src/              # Source files
├── Drivers/
│   ├── STM32F4xx_HAL_Driver/
│   └── CMSIS/
├── Bootloader/
│   ├── bootloader.c      # Main bootloader logic
│   └── bootloader.h      # Bootloader definitions
└── README.md
```

## 🛠️ Getting Started

### Prerequisites

- STM32CubeIDE or equivalent ARM GCC toolchain
- STM32F446RE NUCLEO board
- USB cable for ST-Link connection
- Serial terminal (PuTTY, Tera Term, etc.)

### Building the Project

1. Clone the repository:
```bash
git clone https://github.com/Maro-Uane/STM32_Microcontroller_Custom_Bootloader.git
cd STM32_Microcontroller_Custom_Bootloader
```

2. Open the project in STM32CubeIDE

3. Build the project:
   - Right-click on project → Build Project
   - Or press `Ctrl + B`

4. Flash the bootloader to STM32F446RE

### Usage

#### Entering Bootloader Mode

1. Press and hold the **User Button** (Blue button on NUCLEO board)
2. Press the **Reset Button**
3. Release both buttons
4. Bootloader is now active and waiting for commands

#### Sending Commands via UART

**UART Configuration:**
- Baud Rate: 115200 (configurable)
- Data Bits: 8
- Stop Bits: 1
- Parity: None
- Flow Control: None

**Command Format:**
```
[Command Code] [Length] [Data...] [CRC]
```

**Example - Get Bootloader Version:**
```
Host → MCU: 0x51 0x00 [CRC]
MCU → Host: 0x01 (Version 1)
```

## 🔐 Flash Protection Levels

| Level | Value | Description |
|-------|-------|-------------|
| **Level 0** | `0xAA` | No protection (default) |
| **Level 1** | `0x55` | Read protection enabled |
| **Level 2** | `0xCC` | Debug disabled (permanent) |

⚠️ **Warning:** Level 2 protection is irreversible!

## 📝 Memory Map

```
┌──────────────────────┐ 0x08000000
│   Bootloader         │
│   (16 KB)            │
├──────────────────────┤ 0x08004000
│   User Application   │
│   (Available Flash)  │
│                      │
└──────────────────────┘ 0x08080000
```

## 🧪 Testing

Use the provided host Python script or serial terminal to test bootloader commands:

```python
# Example: Get bootloader version
import serial

ser = serial.Serial('COM3', 115200)
ser.write(bytearray([0x51, 0x00, 0xCRC]))  # Replace CRC with calculated value
response = ser.read(1)
print(f"Bootloader Version: {response[0]}")
```

## 👤 Author

- GitHub: [@Maro-Uane](https://github.com/Maro-Uane)

## 🙏 Acknowledgments

- STMicroelectronics for STM32 HAL library
- ARM for Cortex-M4 documentation
- Community contributors

## 📚 References

- [STM32F446RE Reference Manual](https://www.st.com/resource/en/reference_manual/dm00135183.pdf)
- [STM32F446RE Datasheet](https://www.st.com/resource/en/datasheet/stm32f446re.pdf)
- [AN2606: STM32 microcontroller system memory boot mode](https://www.st.com/resource/en/application_note/cd00167594.pdf)

---

⭐ **Star this repository if you find it helpful!**