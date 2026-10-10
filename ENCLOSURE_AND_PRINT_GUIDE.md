# Hado Edge Access Cube — Enclosure Design Basis, Component Register & Print Guide (Rev B)

CAD source: `cad/hado_cube.py` (CadQuery, parametric). Exports: `cad/export/*.step|*.stl`.
Frame: origin = centre of front face, **Z = 0 front → Z = 48.5 rear**, viewed from the front +X right, +Y up.

## 1. Component dimension register

Confidence: **A** = manufacturer document/product page, **B** = retailer listing of the exact part, **C** = typical value for the part class (pocket is oversized; **verify with calipers before printing**).

| Part (BOM) | Manufacturer | Dimensions used | Source / conf. |
|---|---|---|---|
| Arduino UNO Q (ABX00162) | Arduino | PCB 68.58 × 53.34 mm; holes Ø3.2 on UNO pattern (13.97,2.54) (15.24,50.8) (66.04,7.62) (66.04,35.56); headers ≈ 8.5 mm tall; underside parts < 2 mm | Arduino datasheet §12 (outline, 4×R1.6) **A**; hole coordinates = UNO form-factor pattern **A/inferred**; header height **C** |
| 4.3" COF LCD DMG48270F043_01WN | DWIN | Outline 105.52 × 67.17 × 3.0; active area 95.04 × 53.86 | Sister part DMG48270F043_02WN listing **B** (same family/outline; your memo agrees 105.5 × 67.2 × 3.0). `_01WN` datasheet drawing not retrievable — **verify AA offset on the FPC side** |
| VL53L1X breakout (Adafruit 3967) | Adafruit | 25.5 × 17.5 × 4.6 | Adafruit product page **A** |
| PN532 breakout (Adafruit 364) | Adafruit | **51 × 117.7 × 1.1 mm** | Adafruit product page **A** → *does not fit; see §2* |
| PN532 V3-class module (substitute) | Elechouse-pattern | 42.7 × 40.4 mm PCB, ≈ 4 mm with header | Retailer listings **B** |
| INMP441 module | module vendors (chip: TDK InvenSense) | Chip 4.72 × 3.76 × 1; module 14 × 14 × 3 | InvenSense datasheet **A** (chip); retailer **B** (module) |
| Mono amp (PAM8302 class) | Adafruit 2130 / PAM8302 | 24 × 15 (≈ 6 tall w/ trimpot, terminal) | Adafruit-class listing **B/C** |
| 3 W enclosed speaker | Adafruit 3351 | 70 × 30 × 17; Ø3.4 holes on 63 × 24 rectangle | Adafruit product page **A** |
| LM2596 buck module | generic | 43.2 × 21 × 14; Ø3 holes 6 mm from ends | Multiple retailer listings **B** |
| MIPI-CSI camera | *unspecified in BOM* | 25 × 24 × 9 pocket (Pi-camera-class) | **C — BOM has no part number** |
| DC jack, relay terminal | *not in BOM* | Ø9 hole; 15 × 10 window | From your memo |

## 2. Findings that changed the design (please read)

1. **Adafruit 364 PN532 is 117.7 mm long** — longer than the whole 115 mm enclosure. The design uses a 42.7 × 40.4 mm PN532 V3-class module instead. Change the BOM link accordingly. Note Adafruit lists I²C address **0x48** (shifted), not 0x24 as in the earlier wiring doc; most V3 modules use 0x24 — check your board.
2. **Depth 42 → 48.5 mm.** A 40.4 mm NFC board lying in the ceiling rail needs ≈ 41 mm of travel; 42 mm total depth leaves only ≈ 32 mm. Footprint stays 115 × 115.
3. **Speaker grille moved to the bottom wall.** The 70 × 30 × 17 speaker cannot sit on the front face (the strip below the display is 8 mm). It mounts on four M3 pads inside the bottom wall and radiates through five 2 × 10 mm slots. Your memo's front grille (y = 8 mm) also overlapped its own display seat.
4. **Display moved down** (centre y = −13 mm) so the camera / ToF / mic row sits above it with a ≥ 3 mm bridge, below the NFC rail.
5. **Screws are M2.5 × 45 mm** (not × 35): head seat to insert gives 45 mm. Inserts: pilot Ø4.0 × **5.5** mm (5.0 insert + 0.5 tip relief). Total M2.5 inserts needed: **4** (BOM lists 8).
6. **Power:** the UNO Q accepts **7–24 V on VIN** and 5 V on 5V_SYS (datasheet §3.1). The LM2596 is therefore optional for the UNO Q itself; it is kept for the display/amp 5 V rail as per BOM.
7. **Audio / camera interfaces:** the UNO Q exposes camera lanes only on the 60-pin **JMEDIA** connector (needs an adapter, a plain CSI ribbon will not plug in), and no I²S pins to the MCU; INMP441 needs a different route (USB audio, or the analog mic/line paths on JMISC). Verify before ordering.
8. **Not modelled / assumed:** display snap-tabs (retain with 1.0 mm VHB tape in the pocket), USB-C vertical position on the UNO Q (assumed centred on the short edge), sensor-chip position on the ToF and camera boards (assumed centred), mic-port offset. The official UNO Q STEP (`ABX00162-step.zip`, docs.arduino.cc) could not be downloaded here — drop it in `cad/ref/` and I'll align the cut-outs exactly.

## 3. Geometry summary

| Feature | Value |
|---|---|
| Envelope | 115 × 115 × 48.5; corner R5; walls ≥ 2.5 (≥ 2.5 everywhere except the 1.15 mm half-lap lands) |
| Part 1 (Z 0–7.5, sensor frames reach Z 17) | 1.0 skin; display pocket 106.5 × 68.2 × 3.2; window 96 × 55; camera Ø8.5 (13.5 sleeve), ToF 6.5 × 4.5 (11.5 × 9.5 sleeve), mic Ø2.4; 4 × M2.5 insert bosses |
| Part 2 (Z 5.5–44.5) | 2.5 floor; M3 standoffs 6.0 tall on UNO pattern; NFC ledge rail (channel 4.2 mm, 0.6 mm detent); USB-C 11 × 6, DC jack Ø9, relay window 15 × 10; speaker pads; LM2596 + amp cradles |
| Part 3 (Z 42–48.5) | 4.0 plate + 2.5 rim; 4 × countersunk Ø3.2 / Ø6.0; keyholes Ø8 + 4.5 × 10 with 2.2 rear recess; wire exit 20 × 15 |
| Half-lap joints | tongue 1.2 × 2.0, 0.15 mm clearance (front and rear) |

Fit check (script output): all components clear the shell (0 mm³) apart from the intentional 0.6 mm NFC detent bump; parts and screws do not interfere.

## 4. Print settings

| Setting | PETG (indoor) | ASA (warm/outdoor) |
|---|---|---|
| Nozzle / layer | 0.4 mm / **0.2 mm** | same |
| Perimeters | **6** (2.7 mm) | 6 |
| Top / bottom | 5 / 5 layers | 5 / 5 |
| Infill | **25–30 % gyroid** | same |
| Nozzle / bed | 235–245 °C / 70–80 °C | 250–260 °C / 100–110 °C, enclosed, fan off |
| Speed | 40 mm/s outer, 60 inner | same |
| Supports | none | none |
| Orientation | Part 1 front-face down · Part 2 floor down (open front up) · Part 3 plate down | same |

Orientation reasons: in these orientations the NFC rails, grille slots and side ports run along the print axis (no bridging), and the show face prints against the bed.

**Calibrate first:** print a 20 mm tall coupon of the tongue/groove and one M2.5 pilot. If the lap is tight, add 0.05 mm clearance per side; if the insert pilot is loose, drop to Ø3.9 mm.

## 5. Inserts, fasteners, assembly

| Item | Qty | Spec | Notes |
|---|---|---|---|
| M2.5 brass insert | 4 | OD 4.0 × 5.0, pilot Ø4.0 × 5.5 | Part 1 corner bosses, tip 220–240 °C (PETG) / 240–260 °C (ASA) |
| M3 brass insert | 4 (+4 for speaker) | OD 4.2 × 6.0 | Part 2 standoffs; speaker pads need M3 × 8 screws |
| M2.5 × 45 countersunk | 4 | 0.20–0.25 N·m | Part 3 → Part 1, through the corner channels |
| M3 × 6 button head | 4 | 0.30–0.40 N·m | UNO Q to standoffs |

1. Install inserts. 2. Slide the NFC module into the ledge rail from the front until it clicks over the detent. 3. Fit UNO Q, LM2596, amp, speaker. 4. Press the display into its pocket with VHB; fit camera / ToF / mic into their frames (a drop of CA glue at two corners). 5. Close Part 1 onto Part 2 (half-lap), seat Part 3, drive four screws in a cross pattern.
