# IPDS Sensor Node — KiCad PCB Project

**KiCad Version:** 7.x  
**Board:** 120 × 90 mm · FR4 · 1.6 mm · 2 layers · HASL · 1 oz copper

---

## Files in This Project

| File | Description |
|---|---|
| `IPDS_SensorNode.kicad_pro` | KiCad project settings & DRC rules |
| `IPDS_SensorNode.kicad_sch` | Full schematic — all components & nets |
| `IPDS_SensorNode.kicad_pcb` | PCB layout — footprints, traces, GND plane |
| `sym-lib-table` | Symbol library references |
| `fp-lib-table` | Footprint library references |

---

## How to Open

1. Install **KiCad 7** (https://www.kicad.org/download/)
2. Open `IPDS_SensorNode.kicad_pro` — KiCad will load the project
3. Open **Schematic Editor** (Eeschema) to view the schematic
4. Open **PCB Editor** (PcbNew) to view and edit the PCB layout

---

## Design Overview

### Components

| Ref | Value | Description | Footprint |
|---|---|---|---|
| J1 | ESP32_Left | ESP32 DevKit V1 Left 19-pin header | PinHeader_1x19_P2.54mm |
| J2 | ESP32_Right | ESP32 DevKit V1 Right 19-pin header | PinHeader_1x19_P2.54mm |
| J3 | MPU6050 | 3-Axis Accel+Gyro I²C module | PinHeader_1x06_P2.54mm |
| J4 | DS3231_RTC | TCXO RTC I²C module w/ CR2032 | PinHeader_1x04_P2.54mm |
| J5 | NEO-6M_GPS | u-blox GPS UART module | PinHeader_1x04_P2.54mm |
| J6 | USB_Micro_B | USB Micro-B power input (5V → VIN) | USB_Micro-B_Molex_47589 |
| R1 | 4.7 kΩ | SDA pull-up (I²C) | R_0402_1005Metric |
| R2 | 4.7 kΩ | SCL pull-up (I²C) | R_0402_1005Metric |
| C1 | 100 nF | 3.3V decoupling capacitor | C_0402_1005Metric |
| C2 | 10 µF | 3.3V bulk capacitor | C_0805_2012Metric |
| H1–H4 | M3 | PCB mounting holes (3.2 mm drill) | MountingHole_3.2mm_M3 |
| TP1–TP4 | — | Test points: +3V3, GND, SDA, SCL | TestPoint_Pad_1.0x1.0mm |

### Net List

| Net | Signal | Connected To |
|---|---|---|
| `+3V3` | 3.3V power | ESP32 3V3 pin → all VCC |
| `GND` | Ground | Common GND + B.Cu pour |
| `SDA` | I²C Data | GPIO21 ↔ MPU6050 SDA ↔ DS3231 SDA + R1 |
| `SCL` | I²C Clock | GPIO22 ↔ MPU6050 SCL ↔ DS3231 SCL + R2 |
| `GPS_TX` | UART2 RX in | NEO-6M TX → GPIO16 (RX2) |
| `GPS_RX` | UART2 TX out | GPIO17 (TX2) → NEO-6M RX |
| `VIN` | 5V from USB | USB VBUS → ESP32 VIN |

### I²C Addresses

| Device | Address |
|---|---|
| MPU6050 | `0x68` (AD0 = GND) |
| DS3231 | `0x57` (fixed) |

### UART Settings

| Parameter | Value |
|---|---|
| Port | UART2 |
| Baud rate | 9600 bps |
| Data bits | 8 |
| Parity | None |
| Stop bits | 1 |

---

## PCB Design Rules

| Rule | Value |
|---|---|
| Min trace width | 0.2 mm |
| Power trace width | 0.5 mm |
| Signal trace width | 0.25 mm |
| Min clearance | 0.2 mm |
| Via drill | 0.4 mm |
| Via diameter | 0.8 mm |
| Min hole-to-hole | 0.25 mm |
| Min copper-to-edge | 0.5 mm |
| Solder mask expansion | 0.05 mm |
| Board thickness | 1.6 mm |

---

## Gerber Export (for fabrication)

In KiCad PCB Editor:

1. **File → Fabrication Outputs → Gerbers (.gbr)**
2. Output directory: `Gerbers/`
3. Enable layers:
   - `F.Cu` (front copper)
   - `B.Cu` (back copper / GND plane)
   - `F.SilkS` (front silkscreen)
   - `B.SilkS` (back silkscreen)
   - `F.Mask` (front solder mask)
   - `B.Mask` (back solder mask)
   - `Edge.Cuts` (board outline)
4. **File → Fabrication Outputs → Drill Files (.drl)**
   - Format: Excellon
   - Origin: Absolute

### Recommended Fabs
- [JLCPCB](https://jlcpcb.com) — upload Gerbers zip
- [PCBWay](https://www.pcbway.com)
- [OSH Park](https://oshpark.com)

---

## BOM — Bill of Materials

| Qty | Ref | Value | MPN / Note |
|---|---|---|---|
| 1 | J1,J2 | ESP32 DevKit V1 | AI-Thinker / Random Nerd |
| 1 | J3 | MPU6050 Module | GY-521 breakout |
| 1 | J4 | DS3231 RTC Module | DS3231 breakout w/ CR2032 |
| 1 | J5 | NEO-6M GPS | u-blox NEO-6M module |
| 1 | J6 | USB Micro-B | Molex 47589-0001 |
| 2 | R1,R2 | 4.7 kΩ 0402 | Vishay CRCW04024K70FKED |
| 1 | C1 | 100 nF 0402 10V | Murata GRM155R71A104KA01D |
| 1 | C2 | 10 µF 0805 10V | Murata GRM21BR61A106KE18L |
| 4 | H1–H4 | M3 standoffs | 10 mm brass hex standoff |

---

## Notes

- **Power:** Feed 5V via USB Micro-B (J6) or directly to ESP32 `VIN`. ESP32 onboard AMS1117 regulates to 3.3V.
- **I²C pull-ups:** R1 and R2 are mandatory. Omitting them will cause unreliable I²C comms.
- **GPS antenna:** The NEO-6M module has an integrated ceramic patch antenna. Ensure no copper pours below it or use an active external antenna.
- **AD0 (MPU6050):** Tied to GND on the module, sets I²C address to `0x68`. If using two MPU6050s, tie AD0 of the second to 3.3V for address `0x69`.
- **After PCB modifications:** Re-run DRC (Design → Design Rules Checker) and re-generate netlist before Gerber export.
