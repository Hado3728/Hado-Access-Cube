# Hado Edge Access Cube — Electrical Architecture & Wiring

**Revision:** A · **Target:** IIITB-Qualcomm-Arduino COMET Hackathon · **Logic level:** 3.3 V throughout

> Items marked **[VERIFY]** depend on the exact UNO Q pin-mux / board revision. Confirm against the official UNO Q pinout before ordering a harness.

## 1. System Overview

```
12 V DC jack ─► PTC 3 A ─► Buck 12→5 V / 3 A ─┬─► UNO Q (5 V in) ─► on-board 3V3 rail ─┬─► VL53L1X
                                              ├─► 4.3" LCD (5 V)                      ├─► PN532 (I2C)
                                              ├─► PAM8403 (5 V) ─► 3 W speaker         └─► INMP441
                                              └─► MIPI-CSI camera (via UNO Q connector)
```

| Block | Part | Role |
|---|---|---|
| Compute | Arduino UNO Q (QRB2210 MPU + STM32U585 MCU) | MPU: vision / UI / network. MCU: real-time I/O (NFC, ToF, relay) |
| Display | Evelta 4.3" COF, DMG48270F043_01WN | UART/TTL command display, non-touch |
| Vision | MIPI-CSI 1080p RGB camera | Face / presence capture on the MPU |
| Range | VL53L1X ToF | Wake-on-approach, distance gating |
| Contactless | PN532 | 13.56 MHz credential read, top rail |
| Audio | INMP441 + PAM8403 + 3 W speaker | Voice capture + feedback tones |

## 2. Power Distribution

| Rail | Source | Loads | Est. peak |
|---|---|---|---|
| 12 V | Barrel jack (5.5/2.1 mm), PTC 3 A, SS34 reverse diode | Buck input; door-relay coil (external) | — |
| 5 V | Buck module, 3 A | UNO Q, LCD, PAM8403, camera | ≈ 2.3 A |
| 3V3 | UNO Q on-board regulator | VL53L1X, PN532 logic, INMP441 | ≈ 0.25 A |

Budget (design estimates, measure on bench): UNO Q ≈ 1.0 A, LCD ≈ 0.35 A, camera ≈ 0.25 A, PN532 ≈ 0.15 A, PAM8403 at 3 W ≈ 0.55 A → **≈ 2.3 A, ~25 % headroom**.

- Star-ground at the buck output. Add **470 µF** bulk + **100 nF** at the PAM8403 supply pins, **220 µF** at the LCD.
- Set the buck to **5.10 V** under load; verify before connecting the UNO Q.
- Feed the UNO Q through the supply input specified in its datasheet **[VERIFY]**; never back-feed from USB-C while the buck is live.
- Door relay switches on an **external** 12 V line via the recessed terminal block; keep it galvanically separate from logic ground (opto-isolated relay module recommended).

## 3. Bus Allocation

### 3.1 I²C (single bus, 400 kHz)

| Device | Address | Notes |
|---|---|---|
| VL53L1X | `0x29` | XSHUT on D4 for boot-time address control |
| PN532 | `0x24` | I²C mode: SEL0 = H, SEL1 = L (check board DIP/solder pads) |

- SDA/SCL on the UNO Q I²C header (A4/A5 / Qwiic) **[VERIFY]**. One set of **4.7 kΩ** pull-ups to 3V3 (disable duplicates on breakouts).
- PN532 `IRQ` → D2, `RSTPD_N` → D3. VL53L1X `GPIO1` → D5 (interrupt).
- Keep the PN532 cable ≤ 150 mm and routed away from the PAM8403 output pair.

### 3.2 UART / TTL (LCD bridge)

| UNO Q | LCD (DMG48270F043_01WN) | Notes |
|---|---|---|
| D1 (TX, MCU UART) | RX | 115200 8N1 default; 3.3 V logic |
| D0 (RX, MCU UART) | TX | |
| 5 V / GND | VCC / GND | Add 220 µF at the LCD connector |

### 3.3 I²S (INMP441)

| INMP441 | UNO Q | Notes |
|---|---|---|
| SCK (BCLK) | I²S/SAI BCLK pin **[VERIFY]** | |
| WS (LRCLK) | I²S/SAI WS pin **[VERIFY]** | |
| SD | I²S/SAI data-in **[VERIFY]** | |
| L/R | GND | Left-channel slot |
| VDD / GND | 3V3 / GND | Decouple 100 nF + 10 µF |

Mic port is a Ø2.4 mm acoustic hole in Part 1; keep the breakout ≤ 3 mm behind it, with a foam gasket.

### 3.4 Audio out

UNO Q **DAC pin (A0) [VERIFY]** → 10 µF + 10 kΩ divider → PAM8403 `L-IN` → 3 W / 4 Ω speaker. Tie `R-IN` to GND. Keep speaker leads twisted and short.

### 3.5 Camera

MIPI-CSI ribbon to the UNO Q camera connector **[VERIFY connector availability]**; if not exposed on your revision, substitute a USB UVC 1080p module (same 10×10 mm sleeve footprint).

## 4. Consolidated Pin Map

| Signal | UNO Q pin | Peer |
|---|---|---|
| I²C SDA / SCL | A4 / A5 **[VERIFY]** | VL53L1X, PN532 |
| LCD TX / RX | D1 / D0 | LCD RX / TX |
| PN532 IRQ / RST | D2 / D3 | PN532 |
| VL53L1X XSHUT / INT | D4 / D5 | VL53L1X |
| Relay drive | D6 | Opto relay IN |
| Tamper switch | D7 (INPUT_PULLUP) | Reed / microswitch to GND |
| I²S BCLK / WS / SD | **[VERIFY]** | INMP441 |
| DAC out | A0 **[VERIFY]** | PAM8403 L-IN |

## 5. Bring-up Checklist

1. Buck output at 5.10 V, no load. 2. Power UNO Q alone; confirm 3V3. 3. I²C scan shows `0x24`, `0x29`. 4. LCD echo test over UART. 5. Mic capture at 16 kHz; speaker tone sweep. 6. NFC read, then ToF-triggered wake. 7. Relay under load, with enclosure closed, 30 min thermal soak (buck case ≤ 70 °C).
