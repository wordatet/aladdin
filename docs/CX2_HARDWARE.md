# TI-Nspire CX II Hardware Documentation (Aladdin SoC)

The "Aladdin" SoC used in the TI-Nspire CX II is a custom System-on-Chip composed primarily of Faraday Technology IP cores. This document maps emulated peripherals and "mysteries" to their known Faraday counterparts.

## Architecture Overview

- **CPU**: ARM926EJ-S variant.
- **Bus**: AMBA (AHB/APB).

## Peripheral Mapping

### Power Management Unit (PMU)
- **Base Address**: `0x90140000`
- **Faraday IP**: `FTPMU010`
- **Register Map**:
    | Offset | Known Name | Aladdin usage | Status |
    |--------|------------|---------------|--------|
    | 0x00   | IDNMBR0    | Wakeup Reason | Verified |
    | 0x08   | OSCC       | Osc Control   | Researching |
    | 0x0C   | PMODE      | Power Mode    | Researching (Sleep) |
    | 0x20   | PMSR       | Status Reg    | Used as `disable[0]` |
    | 0x24   | PGSR       | Group Status  | Used for `int_state` |
    | 0x30   | PDLLCR0    | PLL Control 0 | Used for `clocks` |

> [!NOTE]
> The registers in the `0x800` range (e.g., `0x808`, `0x80C`, `0x810`) appear to be TI-specific ASIC extensions or "efuse" registers not part of the standard `FTPMU010` core.

### DMA Controller
- **Base Address**: `0x90020000`
- **Faraday IP**: `FTDMAC020`
- **Structure**: 8-channel prioritized DMA controller.
- **Status**: Partially implemented in `cx2.cpp`.

### USB Controller
- **Base Address**: `0x90110000` (EHCI) / `0x90111000` (OTG)
- **Faraday IP**: `FTOTG210` (USB 2.0 OTG)
- **Status**: Verified via U-Boot headers.

### LCD Controller
- **Faraday IP**: Likely `FTLCDC210`.
- **"Magic VRAM"**: The CX II uses a display-list based or windowed memory mapping for the LCD. 
- **Status**: TODO item #9.

## Known Mysteries
- **`0x9014080C`**: ASIC user flags, must match `0x80E0` field in OS image.
- **`0x90140810` / Bit 8**: Cleared when the physical **ON** key is pressed.
