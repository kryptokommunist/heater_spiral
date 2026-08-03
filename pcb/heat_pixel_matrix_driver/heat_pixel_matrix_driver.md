# Heat Pixel Matrix Driver PCB — Design Document

## Overview

Driver board for the **4×4 heat pixel textile matrix** (expandable to NxM).  
Derived from the existing `heater base 2 ESP32-C3` design.  
Row/column multiplexing: only one row is energized at a time; column MOSFETs
select which pixels in that row sink current.  

Matrix size: **4 rows × 4 columns = 16 pixels**  
(Matches 16 SK6805 LED count on the base board — one indicator LED per pixel.)

---

## Architecture

```
           COL1   COL2   COL3   COL4
            │      │      │      │
          [Q_C1] [Q_C2] [Q_C3] [Q_C4]   ← N-MOSFET column sinks (AO3400A)
            │      │      │      │
ROW1 ─[Q_R1]─┼──────┼──────┼──────┤     ← P-MOSFET row sources (AO3401A)
ROW2 ─[Q_R2]─┼──────┼──────┼──────┤       (high-side switch, VHEAT rail)
ROW3 ─[Q_R3]─┼──────┼──────┼──────┤
ROW4 ─[Q_R4]─┴──────┴──────┴──────┘
              │
             GND
```

**Multiplexing scheme:**
- 4 row drivers: P-MOSFETs pulling each row to VHEAT (5–12V)
- 4 column drivers: N-MOSFETs pulling each column to GND
- Only 1 row active at a time → max 4 pixels lit per cycle
- PWM on column gates for per-pixel power control
- Scan rate ≥ 100 Hz (imperceptible flicker), duty per row = 25%

**Connector to textile:**  
- 8-pin FFC/FPC or JST-PH connector: 4 row lines + 4 col lines
- Each row/col trace rated for heater current

---

## Power Budget

| Parameter            | Value            |
|----------------------|------------------|
| Heater element R     | ~12.5 Ω (5cm spiral) |
| VHEAT rail           | 5V (USB-C) or 12V (LiPo) |
| Peak pixel current   | 400 mA @ 5V / 960 mA @ 12V |
| Max simultaneous     | 4 pixels (one full row) |
| Peak row current     | 1.6 A @ 5V / 3.84 A @ 12V |
| PWM duty typical     | 20–50% → avg ~300–800 mA/row |

Row MOSFETs must handle ≥2A continuous.  
AO3401A (P-ch): Vds=-30V, Id=-4A, Rds=50mΩ → OK.  
AO3400A (N-ch): Vds=30V, Id=5.8A, Rds=60mΩ → OK.

---

## Component List (BOM)

| Ref       | Value / Part           | Package      | LCSC #   | Notes                          |
|-----------|------------------------|--------------|----------|--------------------------------|
| U1        | ESP32-C3-MINI-1-H4     | SMD module   | C2934560 | MCU, WiFi/BLE, 13 GPIOs used  |
| U2        | AMS1117-3.3            | SOT-223      | C6186    | 3.3V LDO from 5V rail          |
| U3        | MCP9808T-E/MC          | DFN-8        | C194710  | I2C temp sensor, A0=GND        |
| U4        | MCP9808T-E/MC          | DFN-8        | C194710  | I2C temp sensor, A0=VDD        |
| Q_R1–R4   | AO3401A (×4)           | SOT-23       | C15127   | P-ch MOSFET, row high-side     |
| Q_C1–C4   | AO3400A (×4)           | SOT-23       | C700233  | N-ch MOSFET, col low-side      |
| LED1–16   | SK6805-EC20            | 2×2mm        | C2890364 | Addressable RGB, 1 per pixel   |
| J1        | USB-C 16-pin 2MD(073)  | SMD          | C2765186 | Power + serial programming     |
| J2        | JST-PH 8-pin           | Through-hole | C131337  | Textile matrix connector       |
| J3        | JST-PH 2-pin           | Through-hole | C131337  | External LiPo/12V input        |
| R_G1–R4   | 10 kΩ (×4)             | 0402         | C25744   | Row MOSFET gate pull-up (P-ch) |
| R_G5–C8   | 10 kΩ (×4)             | 0402         | C25744   | Col MOSFET gate pull-down      |
| R_G_drv   | 100 Ω (×8)             | 0402         | C25076   | Gate resistors, damp ringing   |
| R_LED     | 10 Ω                   | 0402         | C25076   | SK6805 data line impedance     |
| C_bulk    | 100 µF / 16V (×2)      | D6.3×7.7     | C16780   | Bulk decoupling on VHEAT       |
| C_3V3     | 10 µF (×2)             | 0805         | C15850   | 3.3V rail decoupling           |
| C_byp     | 100 nF (×10)           | 0402         | C14663   | Local bypass per IC/MOSFET     |
| SW1       | Tactile switch         | 4-pin SMD    | C318884  | BOOT/RESET for ESP32-C3        |
| TP1–TP8   | Test points            | Through-hole | —        | Row1-4, Col1-4 measurement     |

---

## ESP32-C3 GPIO Assignment

| GPIO | Function            | Direction |
|------|---------------------|-----------|
| 0    | ROW1 (via R_G+inv)  | OUT       |
| 1    | ROW2                | OUT       |
| 2    | ROW3                | OUT       |
| 3    | ROW4                | OUT       |
| 4    | COL1 PWM            | OUT / PWM |
| 5    | COL2 PWM            | OUT / PWM |
| 6    | COL3 PWM            | OUT / PWM |
| 7    | COL4 PWM            | OUT / PWM |
| 8    | SK6805 LED data     | OUT       |
| 9    | BOOT (strapping)    | IN / PU   |
| 18   | USB D-              | USB       |
| 19   | USB D+              | USB       |
| 20   | I2C SDA (MCP9808)   | I/O       |
| 21   | I2C SCL (MCP9808)   | OUT       |

Row lines drive P-MOSFET gates through an inverting driver (10kΩ pull-up to
VHEAT + GPIO drives LOW to turn on → standard P-ch gate drive).

---

## Multiplexing Firmware (pseudocode)

```c
// 100 Hz frame rate, 4 rows → 2.5 ms per row
void IRAM_ATTR matrix_isr() {
    static uint8_t row = 0;
    // Disable all rows (high = P-ch off)
    gpio_set_level(ROW1, 1); gpio_set_level(ROW2, 1);
    gpio_set_level(ROW3, 1); gpio_set_level(ROW4, 1);
    // Set column PWM duty for this row
    for (int c = 0; c < 4; c++)
        ledc_set_duty(LEDC_LOW_SPEED_MODE, c, pixel_duty[row][c]);
    // Enable active row
    gpio_set_level(row_pin[row], 0);  // P-ch: LOW = on
    row = (row + 1) % 4;
}
```

---

## PCB Layout Guidelines

1. **VHEAT plane** on bottom copper, stitched with vias.
2. Row/column traces: 1.5 mm wide (rated for 2A on 1oz Cu).
3. Gate traces: 0.2 mm, keep short (<10mm from GPIO to gate resistor).
4. Place bulk caps (C_bulk) directly at J2 connector pads.
5. MCP9808 sensors: place one near center of PCB, one near edge, for
   gradient measurement.
6. SK6805 LEDs arranged in 4×4 grid matching heater pixel layout.
7. Board outline: 60 × 50 mm (matches existing base board footprint).
8. Mounting holes: M2.5 × 4 corners.

---

## Net List (key nets)

```
VHEAT    : J1.VBUS, J3.+, C_bulk[1..2]+, Q_R[1..4].Source
GND      : J1.GND, U1.GND, U2.GND, Q_C[1..4].Source, C_bulk[1..2]-
+3V3     : U2.OUT, U1.3V3, U3.VDD, U4.VDD, R_G1..R4 (pull-up for cols)
ROW1_DRV : U1.GPIO0 → R_G_drv1 → Q_R1.Gate; R_G1: VHEAT→Q_R1.Gate
ROW2_DRV : U1.GPIO1 → R_G_drv2 → Q_R2.Gate; R_G2: VHEAT→Q_R2.Gate
ROW3_DRV : U1.GPIO2 → R_G_drv3 → Q_R3.Gate; R_G3: VHEAT→Q_R3.Gate
ROW4_DRV : U1.GPIO3 → R_G_drv4 → Q_R4.Gate; R_G4: VHEAT→Q_R4.Gate
COL1_PWM : U1.GPIO4 → R_G_drv5 → Q_C1.Gate; R_G5: GND→Q_C1.Gate
COL2_PWM : U1.GPIO5 → R_G_drv6 → Q_C2.Gate; R_G6: GND→Q_C2.Gate
COL3_PWM : U1.GPIO6 → R_G_drv7 → Q_C3.Gate; R_G7: GND→Q_C3.Gate
COL4_PWM : U1.GPIO7 → R_G_drv8 → Q_C4.Gate; R_G8: GND→Q_C4.Gate
ROW1     : Q_R1.Drain → J2.pin1
ROW2     : Q_R2.Drain → J2.pin2
ROW3     : Q_R3.Drain → J2.pin3
ROW4     : Q_R4.Drain → J2.pin4
COL1     : J2.pin5 → Q_C1.Drain
COL2     : J2.pin6 → Q_C2.Drain
COL3     : J2.pin7 → Q_C3.Drain
COL4     : J2.pin8 → Q_C4.Drain
LED_DAT  : U1.GPIO8 → R_LED → LED1.DI; LED1.DO → LED2.DI → ... → LED16.DO
I2C_SDA  : U1.GPIO20 ↔ U3.SDA ↔ U4.SDA (4.7kΩ pull-up to +3V3)
I2C_SCL  : U1.GPIO21 → U3.SCL, U4.SCL (4.7kΩ pull-up to +3V3)
```

---

## Schematic Blocks

### Block 1 — Power Input
```
J1 (USB-C) ──VBUS──► C_bulk ──► VHEAT (5V)
                   └──► U2 (AMS1117-3.3) ──► +3V3 ──► C_3V3
J3 (ext 12V)──────► VHEAT (solder bridge selects 5V or 12V)
```

### Block 2 — Row Drivers (×4, shown for ROW1)
```
VHEAT ──── R_G1 (10kΩ) ──┬── Q_R1.Gate
                          │
U1.GPIO0 ─ R_G_drv1(100Ω)┘
                          
Q_R1.Source ─── VHEAT
Q_R1.Drain  ─── ROW1 ─── J2.pin1
```

### Block 3 — Column Drivers (×4, shown for COL1)
```
U1.GPIO4 ─ R_G_drv5(100Ω) ─┬── Q_C1.Gate
                             │
GND ────── R_G5 (10kΩ) ─────┘

Q_C1.Drain  ─── COL1 ─── J2.pin5
Q_C1.Source ─── GND
```

### Block 4 — Temperature Sensors
```
+3V3 ─── U3.VDD;  U3.GND ─── GND
         U3.A0  ─── GND  (addr=0x18)
         U3.A1  ─── GND
         U3.A2  ─── GND
         U3.SDA ──┬── I2C_SDA ── U1.GPIO20
         U3.SCL ──┤── I2C_SCL ── U1.GPIO21
                  │
+3V3 ─── U4.VDD;  U4.GND ─── GND
         U4.A0  ─── +3V3 (addr=0x19)
         U4.A1  ─── GND
         U4.A2  ─── GND
         U4.SDA ──┘
         U4.SCL ──┘
4.7kΩ pull-ups on SDA and SCL to +3V3
```

### Block 5 — ESP32-C3 Module
```
J1.D+/D- ──────── U1.USB_D+/D-
+3V3 ──────────── U1.3V3 (+ 100nF bypass)
GND  ──────────── U1.GND
SW1 ─── U1.GPIO9 (BOOT, active-low)
```

### Block 6 — LED Chain
```
+3V3 ─── LED1..16 VDD (+ 100nF each)
GND  ─── LED1..16 GND
U1.GPIO8 ─ R_LED(10Ω) ─ LED1.DI
LED1.DO ─ LED2.DI ─ ... ─ LED16.DO (no termination)
```
