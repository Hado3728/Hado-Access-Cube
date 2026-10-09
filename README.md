# HADO Edge Access Cube

A modular, 3D-printable enclosure and hardware architecture for a touchless, AI-powered access control terminal. Built for the IIITB-Qualcomm-Arduino COMET Hackathon.

\---

## Overview

The **HADO Edge Access Cube** combines dual-brain compute (Qualcomm QRB2210 Linux MPU + STM32U585 Real-Time MCU) with multi-modal sensing (MIPI Camera, Time-of-Flight depth sensing, and PN532 NFC) inside a compact $115 \\times 115 \\times 42\\text{ mm}$ 3-piece modular shell.

This repository contains the CAD models, AI prompt generation assets, bill of materials (BOM), and mechanical specifications required to manufacture, assemble, and deploy the physical device.




**WEBSITE
https://thunderous-marzipan-0d5d81.netlify.app/**
---

\---

## Mechanical Architecture

The enclosure uses a 3-piece snap-and-screw modular structure designed for FDM/SLA 3D printing and brass heat-set inserts.

```text
+-----------------------------------------------------------------+
| Part 1: Front Bezel \& Display Faceplate                         |
|   - Holds 4.3" COF Display, Camera, ToF Sensor, Mic, \& Speaker  |
+-----------------------------------------------------------------+
                                |  (Interlocking Lip + M2.5 Screws)
                                v
+-----------------------------------------------------------------+
| Part 2: Main Body Housing \& Chassis                             |
|   - Houses Qualcomm Arduino UNO Q MPU/MCU Stack \& NFC Rail      |
+-----------------------------------------------------------------+
                                |  (Flush Shoulder + M2.5 Screws)
                                v
+-----------------------------------------------------------------+
| Part 3: Rear Wall-Mounting Backplate                            |
|   - Features Keyhole Wall Mounts \& Cable Pass-Through Window    |
+-----------------------------------------------------------------+
```

### Key Enclosure Specs

* **Footprint:** $115\\text{ mm} \\times 115\\text{ mm} \\times 42\\text{ mm}$
* **Wall Thickness:** $2.5\\text{ mm}$ minimum structural wall thickness
* **Fasteners:** M2.5 and M3 brass heat-set inserts with countersunk machine screws
* **Materials Recommended:** PETG or ABS/ASA (25–30% Gyroid infill, 0.2mm layer height)

\---

## Hardware Stack \& Bill of Materials (BOM)

|Subsystem|Component|Interface / Notes|
|-|-|-|
|**Compute**|Qualcomm® Arduino® UNO™ Q|QRB2210 MPU + STM32U585 MCU|
|**Display**|Evelta 4.3" COF Non-Touch LCD (*DMG48270F043\_01WN*)|UART / TTL ($105.5 \\times 67.2 \\times 3.0\\text{ mm}$)|
|**Perception**|1080p MIPI-CSI RGB Camera Module|MIPI-CSI Ribbon Cable|
|**Depth / Proximity**|VL53L1X Time-of-Flight (ToF) Sensor|$\\text{I}^2\\text{C}$ Bus|
|**Contactless**|PN532 NFC Reader Board|$\\text{I}^2\\text{C}$ / SPI (Internal slide rail)|
|**Audio**|INMP441 MEMS Mic + 3W Speaker \& PAM8403 Amp|$\\text{I}^2\\text{S}$ \& PWM|
|**Power**|12V to 5V/3A DC-DC Buck Converter PMU|Terminal Block / USB-C|

\---

## Repository Structure

```text
├── cad/
│   ├── step/                 # STEP files for SolidWorks/Fusion 360 editing
│   ├── stl/                  # 3D printable STL files for slicers
│   └── prompts/              # Coordinate-mapped AI Text-to-CAD prompts (Parts 1-3)
├── docs/
│   ├── schematics/           # Wiring and interconnect diagrams
│   └── mechanics/            # Exploded assembly blueprints \& clearances
├── BOM                       # Complete hardware component list
├── LICENSE                   # MIT License
└── README.md                 # Project documentation
```

\---

## Assembly Instructions

1. **Insert Brass Heat-Sets:**

   * Press four **M2.5 brass heat-set inserts** into the corner bosses of **Part 1 (Front Bezel)** using a soldering iron.
   * Press four **M3 brass heat-set inserts** into the mainboard standoff pillars of **Part 2 (Main Housing)**.
2. **Mount Front Sensors \& Screen:**

   * Snap the Evelta 4.3" screen into the internal rear seat of Part 1.
   * Press-fit the MIPI camera and ToF sensor into their respective alignment sleeves.
3. **Install Core Compute \& NFC:**

   * Slide the PN532 NFC module into the top ceiling rail channel of Part 2 until the detent locks.
   * Secure the Qualcomm Arduino UNO Q board onto the M3 standoffs using M3x6mm screws.
4. **Final Closure:**

   * Join Part 1 and Part 2 via the perimeter interlocking lip.
   * Attach Part 3 to the rear shoulder and secure the entire sandwich using four M2.5x35mm countersunk machine screws driven from the backplate straight through to Part 1's brass inserts.

\---

## License

This project is open-source under the [MIT License](LICENSE).

