# Heat Pixel Matrix Driver PCB — 16×16 Design

## Overview

Driver board for a **16×16 resistive heat pixel textile matrix** (256 pixels).  
Supply: **12V (3S LiPo / wall adapter)**.  
Three operating modes supported — multiplexed scan, full-row direct, and column burst.

---

## Operating Modes

### Why this matters for heating speed

| Mode | Rows active | Cols active | Avg power/pixel | Time to ~45°C |
|------|-------------|-------------|-----------------|---------------|
| **Scan** (default) | 1 of 16, cycled | any pattern | ~0.72 W (6.25% duty) | ~90–180 s |
| **Full-row** (all cols on, 1 row) | 1 sustained | all 16 | 11.5 W (100% duty) | ~5–15 s |
| **Full-column** (all rows on, 1 col) | all 16 | 1 sustained | 11.5 W per pixel | ~5–15 s |
| **Burst** (up to N rows simultaneous) | N (1–4) | any | N × 0.72 W | scales ~1/N |
| **Full-field** (all 256 on) | all 16 | all 16 | 11.5 W each | ~5–10 s — **needs beefy supply** |

**Key insight:** In full-row mode (1 row × 16 columns), each row MOSFET must carry **15.4 A** continuous
at 12V. This drives the sizing decision below — row drivers upgraded to dual-MOSFET packages.

---

## Multiplexing Architecture

```
                COL1..COL16 (N-MOSFET sinks via 74HC595 chain)
                 │   │   │       │
ROW1  ─[Q_R1]──┼───┼───┼── ... ┼──┐
ROW2  ─[Q_R2]──┼───┼───┼── ... ┼──┤  16 rows × 16 cols
...                                  │  = 256 heater pixels
ROW16 ─[Q_R16]─┴───┴───┴── ... ┴──┘
```

**Row drivers (high-side, P-ch):**  
- 2× 74HC595 shift registers → gate resistors → P-MOSFET  
- Firmware can activate **any subset of rows simultaneously** (multi-bit set in SR)  
- For full-row mode: assert one row bit, set all 16 column bits → sustain indefinitely  
- For full-field: all 32 bits = 1 → all 256 pixels on (limited by supply current)

**Column drivers (low-side, N-ch):**  
- 2× 74HC595 → gate resistors → N-MOSFET per column  
- PWM blanking via `COL_OE#` (GPIO5 LEDC) applies to all active columns uniformly  
- For per-pixel PWM in scan mode: duty set per row slot  
- For full-column mode: set 1 column bit, enable all rows → column stripe sustained

**Control signals (unchanged — 6 GPIOs):**
```
ESP32 → SPI → 74HC595×2 (row select) → AO3401A gates (P-ch rows)
ESP32 → SPI → 74HC595×2 (col select) → AO3400A gates (N-ch cols)
ESP32 GPIO5 (LEDC PWM) → COL_OE# blanking
```

---

## Power Budget — 16×16 @ 12V

| Parameter                       | Value                        |
|---------------------------------|------------------------------|
| Heater element R                | 12.5 Ω (5cm spiral)          |
| VHEAT                           | 12V (3S LiPo)                |
| Current per pixel               | 960 mA                       |
| Power per pixel                 | 11.5 W                       |
| **Full-row current (16 pixels)**  | **15.4 A**                  |
| Full-column current (16 pixels) | 15.4 A (same — 16 col sinks) |
| Full-field current (256 pixels) | 246 A (impractical — use PWM duty) |
| Recommended max simultaneous    | 1 full row = 15.4 A          |
| Practical jacket supply         | 12V / 20A (240W) LiPo pack   |
| With 30% PWM in scan mode       | 0.72 W avg / pixel, ~92W total field |

**Full-field at 30% duty (burst mode):**  
246 A × 30% = ~74 A peak → not realistic for wearable.  
**Practical maximum:** 2 rows simultaneous at 50% PWM = 15.4 A peak = 92W → manageable with 12V/10A supply.

### Recommended supply sizing:
- **Comfort (scan) mode:** 12V / 3A → covers 1 row at a time, 30% duty  
- **Fast warmup mode:** 12V / 20A → full-row direct drive, 1 row at 100%  
- **Full jacket blast:** 12V / 20A LiPo → 2 rows simultaneous, 50% duty

---

## Component List (BOM) — 16×16

| Ref            | Value / Part          | Pkg      | LCSC #   | Qty | Notes                            |
|----------------|-----------------------|----------|----------|-----|----------------------------------|
| U1             | ESP32-C3-MINI-1-H4    | module   | C2934560 | 1   | MCU                              |
| U2             | AMS1117-3.3           | SOT-223  | C6186    | 1   | 3.3V LDO                         |
| U3, U4         | 74HC595D              | SOIC-16  | C5947    | 4   | Row shift register (2×) + Col (2×)|
| U5 (opt)       | MCP9808T-E/MC         | DFN-8    | C194710  | 2   | I2C temp sensors (sample subset) |
| Q_R1–Q_R16     | **SI2333DS** (P-ch, 20A) | SOT-23   | C191553  | 16  | Row high-side; Id=-20A for full-row mode |
| Q_C1–Q_C16     | **SI2302DS** (N-ch, 20A) | SOT-23   | C191554  | 16  | Col low-side; Id=20A for full-col mode   |
| LED1–LED16     | SK6805-EC20           | 2×2mm    | C2890364 | 16  | Status LEDs (1 per row, optional)|
| J1             | USB-C 16-pin          | SMD      | C2765186 | 1   | Programming + 5V power           |
| J2             | 32-pin FPC (0.5mm)    | SMD      | C160391  | 1   | 16 row + 16 col to textile       |
| J3             | XT30 or DC-barrel     | TH       | C601330  | 1   | 12V/3A external power            |
| R_PU_ROW 1–16  | 10 kΩ × 16            | 0402     | C25744   | 16  | P-MOSFET gate pull-up to VHEAT   |
| R_PD_COL 1–16  | 10 kΩ × 16            | 0402     | C25744   | 16  | N-MOSFET gate pull-down to GND   |
| R_G 1–32       | 100 Ω × 32            | 0402     | C25076   | 32  | Gate series resistors (damp)     |
| R_I2C 1–2      | 4.7 kΩ                | 0402     | C25900   | 2   | I2C pull-ups                     |
| R_LED          | 10 Ω                  | 0402     | C25076   | 1   | SK6805 data line                 |
| C_BULK         | 470 µF / 16V electro  | D8×12    | C178593  | 4   | Bulk on VHEAT (one per row bank) |
| C_3V3          | 10 µF                 | 0805     | C15850   | 4   | 3.3V decoupling                  |
| C_BYP          | 100 nF                | 0402     | C14663   | 40  | Local bypass per IC + MOSFET     |
| SW1            | Tactile 4-pin         | SMD      | C318884  | 1   | BOOT                             |
| D1             | SS14 Schottky         | SMA      | C2480    | 1   | 5V / 12V reverse protection      |
| F1             | 5A polyfuse           | 1812     | C70068   | 1   | Overcurrent on VHEAT             |

**Total component count:** ~150 parts  
**Estimated PCB size:** 80 × 60 mm (2-layer)

---

## ESP32-C3 GPIO Assignment

| GPIO | Signal              | Notes                               |
|------|---------------------|-------------------------------------|
| 0    | SPI_CLK             | Shared row + col 74HC595 clock      |
| 1    | SPI_MOSI            | Data to row shift register chain    |
| 2    | ROW_LATCH (RCLK)    | Latch row register output           |
| 3    | COL_MOSI            | Data to col shift register chain    |
| 4    | COL_LATCH (RCLK)    | Latch col register output           |
| 5    | COL_OE#             | PWM blanking (active-low output en) |
| 8    | SK6805 LED data     | Status LED chain                    |
| 9    | BOOT                | Strapping, pull-up                  |
| 18   | USB D-              |                                     |
| 19   | USB D+              |                                     |
| 20   | I2C SDA             | MCP9808 sensors                     |
| 21   | I2C SCL             | MCP9808 sensors                     |

**Only 12 GPIOs used** — well within ESP32-C3's 13 available.

---

## Shift Register Topology

### Row chain (74HC595 × 2):
```
ESP32.GPIO1 → U3.SER → U3.QH' → U4.SER → U4.QH'(NC)
ESP32.GPIO0 → U3.SRCLK, U4.SRCLK  (shared clock)
ESP32.GPIO2 → U3.RCLK,  U4.RCLK   (shared latch)
+3V3        → U3.OE# (always enabled; row MOSFETs have their own enable)
+3V3        → U3.SRCLR#, U4.SRCLR# (no clear)

U3.QA..QH → R_G[1..8] → Q_R1..R8 gates   (active LOW turns on P-ch)
U4.QA..QH → R_G[9..16] → Q_R9..R16 gates
```

### Column chain (74HC595 × 2):
```
ESP32.GPIO3 → U5.SER → U5.QH' → U6.SER → U6.QH'(NC)
ESP32.GPIO0 → U5.SRCLK, U6.SRCLK  (shared clock with row chain)
ESP32.GPIO4 → U5.RCLK,  U6.RCLK   (separate latch)
ESP32.GPIO5 → U5.OE#,   U6.OE#    (PWM blanking, active-LOW)
GND         → U5.SRCLR#, U6.SRCLR#

U5.QA..QH → R_G[17..24] → Q_C1..C8 gates  (active HIGH turns on N-ch)
U6.QA..QH → R_G[25..32] → Q_C9..C16 gates
```

**Note:** Row and column 74HC595 share SRCLK but have separate RCLK signals,
so both chains can be loaded simultaneously in one SPI burst:
send 32 bits (16 col + 16 row) in one transaction, then assert both latches.

---

## Firmware Scan Loop

```c
#define ROWS 16
#define COLS 16
#define SCAN_HZ 100

uint16_t pixel_duty[ROWS][COLS];  // 0–255 per pixel
volatile uint8_t active_row = 0;

typedef enum {
    MODE_SCAN,        // 1 row at a time, multiplexed (default)
    MODE_FULL_ROW,    // 1 row sustained, all cols on — fast warmup
    MODE_FULL_COL,    // 1 col sustained, all rows on — column stripe
    MODE_BURST,       // N rows simultaneous, N <= 4 recommended
} drive_mode_t;

drive_mode_t current_mode = MODE_SCAN;
uint8_t  burst_rows;  // bitmask for MODE_BURST / MODE_FULL_ROW
uint16_t burst_cols;  // bitmask for MODE_FULL_COL / MODE_BURST

void matrix_update(uint16_t row_sr, uint16_t col_sr) {
    uint8_t buf[4] = {col_sr & 0xFF, col_sr >> 8, row_sr & 0xFF, row_sr >> 8};
    spi_transaction_t t = {.length = 32, .tx_buffer = buf};
    spi_device_transmit(spi_handle, &t);
    gpio_set_level(ROW_LATCH, 1); gpio_set_level(COL_LATCH, 1);
    gpio_set_level(ROW_LATCH, 0); gpio_set_level(COL_LATCH, 0);
}

void matrix_scan_task(void *arg) {
    const TickType_t row_ticks = pdMS_TO_TICKS(1000 / (SCAN_HZ * ROWS));
    while (1) {
        switch (current_mode) {

        case MODE_SCAN: {
            // One-hot row (active = LOW for P-ch), columns from duty map
            uint16_t row_sr = ~(uint16_t)(1 << active_row);
            uint16_t col_sr = 0;
            for (int c = 0; c < 16; c++)
                if (pixel_duty[active_row][c] > 0) col_sr |= (1 << c);
            matrix_update(row_sr, col_sr);
            ledc_set_duty(LEDC_HIGH_SPEED_MODE, 0, max_duty_in_row(active_row));
            ledc_update_duty(LEDC_HIGH_SPEED_MODE, 0);
            active_row = (active_row + 1) % ROWS;
            vTaskDelay(row_ticks);
            break;
        }

        case MODE_FULL_ROW: {
            // Single row, all columns on, 100% duty (fastest heat)
            // burst_rows = which row index (0–15)
            uint16_t row_sr = ~(uint16_t)(1 << burst_rows);
            uint16_t col_sr = 0xFFFF;  // all 16 cols
            matrix_update(row_sr, col_sr);
            ledc_set_duty(LEDC_HIGH_SPEED_MODE, 0, 255); // 100% OE# (always enabled)
            ledc_update_duty(LEDC_HIGH_SPEED_MODE, 0);
            vTaskDelay(pdMS_TO_TICKS(10));  // sustain, check mode change
            break;
        }

        case MODE_FULL_COL: {
            // All rows on, single column — column stripe direct drive
            // burst_cols = which col index (0–15)
            uint16_t row_sr = 0x0000;  // all rows active (all LOW for P-ch)
            uint16_t col_sr = (uint16_t)(1 << burst_cols);
            matrix_update(row_sr, col_sr);
            ledc_set_duty(LEDC_HIGH_SPEED_MODE, 0, 255);
            ledc_update_duty(LEDC_HIGH_SPEED_MODE, 0);
            vTaskDelay(pdMS_TO_TICKS(10));
            break;
        }

        case MODE_BURST: {
            // Multiple rows simultaneously (burst_rows = bitmask, max 4 bits set)
            // Columns from burst_cols mask, PWM duty controlled via OE#
            uint16_t row_sr = ~(uint16_t)burst_rows;
            matrix_update(row_sr, burst_cols);
            // PWM duty reduces average current to prevent trace/MOSFET overload
            ledc_set_duty(LEDC_HIGH_SPEED_MODE, 0, 64);  // 25% default burst duty
            ledc_update_duty(LEDC_HIGH_SPEED_MODE, 0);
            vTaskDelay(pdMS_TO_TICKS(10));
            break;
        }
        }
    }
}
```

---

## PCB Layout Guidelines

### Layer stack (2-layer):
- **Top**: signal routing, all SMD components
- **Bottom**: VHEAT power plane (poured copper)
- **Top**: GND pour (stitched with vias to bottom)

### Trace widths (updated for full-row 15.4A @ 12V):
| Net          | Width   | Rationale                                          |
|--------------|---------|----------------------------------------------------|
| VHEAT        | plane   | Bottom copper pour, 2oz Cu                         |
| Row traces   | **3.5 mm** | 15.4A (full-row mode), 1oz Cu, 30°C rise        |
| Col traces   | **3.5 mm** | 15.4A (full-col mode), same                     |
| MOSFET drain pads | 4mm wide copper fill | Thermal relief + current |
| Gate signals | 0.2 mm  | Low current                                        |
| SPI signals  | 0.15 mm | Short, <50mm                                       |
| I2C          | 0.2 mm  | With pull-ups                                      |

> **Note:** For full-row mode 3.5mm trace handles ~12–15A on 1oz Cu (IPC-2221).  
> If 2oz Cu is used (JLCPCB option +cost), 2mm traces suffice.

### Component placement:
```
┌─────────────────────────────────────────────┐
│  J1(USB)  J3(12V)   F1   D1   C_BULK×4      │
│  ─────────────────────────────────────────  │
│  U2(LDO)  U1(ESP32-C3)  SW1               │
│  ─────────────────────────────────────────  │
│  U3  U4  (row 74HC595)                      │
│  Q_R1..Q_R8  [row MOSFETs left bank]         │
│  Q_R9..Q_R16 [row MOSFETs right bank]        │
│  ─────────────────────────────────────────  │
│  U5  U6  (col 74HC595)                      │
│  Q_C1..Q_C8  [col MOSFETs left bank]         │
│  Q_C9..Q_C16 [col MOSFETs right bank]        │
│  ─────────────────────────────────────────  │
│  J2 (32-pin FPC connector, bottom edge)     │
│  LED1..LED16  [status row, top edge]        │
│  U5,U6 (MCP9808 temp sensors)              │
└─────────────────────────────────────────────┘
        80mm × 60mm
```

### J2 pinout (32-pin FPC, 0.5mm pitch):
```
Pin 1–16:  ROW1–ROW16  (to textile row copper bands)
Pin 17–32: COL1–COL16  (to textile col copper bands)
```

### Thermal:
- Row MOSFETs (AO3401A): Rds=50mΩ, I=2A → P=200mW each → no heatsink needed.
- Add 2oz Cu option for row/col power pads if using 12V @ 1A/pixel.
- Place C_BULK caps immediately behind J2 connector.

---

## Safety Features

1. **F1 polyfuse (20A)** on VHEAT — sized for full-row mode (15.4A + 30% margin).
2. **D1 Schottky SS34** (3A) on 5V USB path; main 12V input bypasses D1 via solder bridge.
3. **MCP9808 over-temp**: firmware switches to MODE_SCAN (lowest duty) if T > 50°C, full off at 60°C.
4. **Watchdog**: ESP32-C3 WDT resets scan loop; all 74HC595 outputs go LOW on reset (MOSFETs off via pull resistors).
5. **Burst mode current cap**: firmware enforces `__builtin_popcount(burst_rows) <= 4` — max 4 rows simultaneous = 61.6A theoretical, but with COL_OE# at 25% duty = 15.4A avg — same as full-row at 100%.
6. **Full-field guard**: firmware blocks `row_sr=0x0000 + col_sr=0xFFFF` (all 256 pixels) unless explicitly unlocked via a two-step API call.
7. **Textile fuse wire**: recommend 20A rated copper band sewn into each row/col channel in textile.

## Mode Summary Table

| Mode | row_sr | col_sr | OE# duty | Peak I | Avg I | Use case |
|------|--------|--------|----------|--------|-------|----------|
| Scan | one-hot (inverted) | pattern | per-row | 960 mA | 60 mA/pixel | Comfort warmth |
| Full-row | one bit LOW | 0xFFFF | 100% | **15.4 A** | 15.4 A | Fast warmup stripe |
| Full-col | 0x0000 | one bit | 100% | **15.4 A** | 15.4 A | Column stripe |
| Burst N=2 | 2 bits LOW | pattern | 50% | 30.8 A pk | 15.4 A | Faster area heat |
| Burst N=4 | 4 bits LOW | pattern | 25% | 61.6 A pk | 15.4 A | Max recommended |

---

## Schematic Net List (abbreviated)

```
# Power
VHEAT  : J3.+, J1.VBUS → D1.A → F1 → VHEAT_BUS
GND    : J3.-, J1.GND, all GND symbols
+3V3   : U2.OUT

# 3.3V LDO
U2.IN  : VHEAT_BUS (5V mode) or +5V rail
U2.ADJ : GND
U2.OUT : +3V3

# Row shift register chain
U3.VCC  : +3V3 ; U3.GND : GND
U3.SRCLK: ESP32.GPIO0
U3.RCLK : ESP32.GPIO2
U3.SER  : ESP32.GPIO1
U3.OE#  : +3V3 (always enabled)
U3.SRCLR# : +3V3
U3.QA..QH : R_G1..8 (100Ω) → Q_R1..8.Gate
U3.QH'  : U4.SER
U4.VCC  : +3V3 ; U4.GND : GND ; (clocks shared with U3)
U4.QA..QH : R_G9..16 (100Ω) → Q_R9..16.Gate

# Row MOSFET (P-ch, one of 16)
Q_Rx.Source : VHEAT_BUS
Q_Rx.Gate   : R_G_ROWx → {74HC595 Qx output}
Q_Rx.Gate   : R_PU_ROWx (10kΩ) → VHEAT_BUS  (pull-up: default OFF)
Q_Rx.Drain  : ROWx → J2.pin[x]

# Col shift register chain  
U5.VCC  : +3V3 ; U5.GND : GND
U5.SRCLK: ESP32.GPIO0  (shared)
U5.RCLK : ESP32.GPIO4
U5.SER  : ESP32.GPIO3
U5.OE#  : ESP32.GPIO5  (PWM blanking)
U5.SRCLR# : +3V3
U5.QA..QH : R_G17..24 (100Ω) → Q_C1..8.Gate
U5.QH'  : U6.SER
U6.VCC  : +3V3 ; U6.GND : GND ; (clocks/latch shared with U5)
U6.QA..QH : R_G25..32 (100Ω) → Q_C9..16.Gate

# Col MOSFET (N-ch, one of 16)
Q_Cx.Drain  : COLx → J2.pin[16+x]
Q_Cx.Gate   : R_G_COLx → {74HC595 Qx output}
Q_Cx.Gate   : R_PD_COLx (10kΩ) → GND   (pull-down: default OFF)
Q_Cx.Source : GND

# Temp sensors
U5.VDD : +3V3 ; U5.GND : GND
U5.SDA : I2C_SDA ; U5.SCL : I2C_SCL
U5.A0,A1,A2 : GND  (addr 0x18)
U6.VDD : +3V3 ; U6.GND : GND
U6.SDA : I2C_SDA ; U6.SCL : I2C_SCL
U6.A0 : +3V3 ; U6.A1,A2 : GND  (addr 0x19)
I2C_SDA : ESP32.GPIO20 + R_I2C1(4.7kΩ) → +3V3
I2C_SCL : ESP32.GPIO21 + R_I2C2(4.7kΩ) → +3V3

# LED chain
+3V3 → LED1..16 VDD
GND  → LED1..16 GND
ESP32.GPIO8 → R_LED(10Ω) → LED1.DI → LED1.DO → ... → LED16.DO

# USB / programming
J1.D+ : ESP32.GPIO19
J1.D- : ESP32.GPIO18
ESP32.GPIO9 : SW1 → GND (BOOT button)
```
