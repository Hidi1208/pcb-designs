# USB-C Power Delivery Hub

A custom PCB that negotiates USB-C Power Delivery to provide multiple regulated voltage outputs from a single PD charger. Designed from scratch in KiCad 10 as a portfolio project demonstrating power electronics, USB-C PD protocol, and multi-rail voltage regulation.

## What It Does

Plug in any USB-C PD charger (45W+), and the board negotiates 20V/3A over the PD protocol, then steps it down through three independent buck converters to provide:

| Output | Voltage | Max Current | Connector |
|--------|---------|-------------|-----------|
| Rail 1 | 12V | 1.5A | Barrel jack (center-positive) |
| Rail 2 | 5V | 2A | USB-A port |
| Rail 3 | 3.3V | 1A | Screw terminal |
| Fallback | 5V | 900mA | Screw terminal (active only when PD negotiation fails) |

## Block Diagram

```
USB-C PD Charger
      │
      ▼
┌─────────────┐     ┌──────────┐     ┌───────────┐
│  USB-C      │────▶│ CYPD3177 │────▶│ P-FET Q1  │──── 20V Rail
│  Connector  │     │ PD Sink  │     │ Main Gate │       │
└─────────────┘     │ (20V/3A) │     └───────────┘       ├──▶ MP1584 #1 ──▶ 12V (Barrel Jack)
                    │          │     ┌───────────┐       ├──▶ MP1584 #2 ──▶ 5V  (USB-A)
                    │          │────▶│ P-FET Q2  │       └──▶ MP1584 #3 ──▶ 3.3V (Screw Term.)
                    │          │     │ Fallback  │──── 5V Fallback (Screw Term.)
                    └──────────┘     └───────────┘
```

## Key Components

| Reference | Component | Function |
|-----------|-----------|----------|
| U1 | CYPD3177-BCR (QFN-24) | USB-C PD sink controller — negotiates voltage/current via CC pins, no firmware required |
| U2, U3, U4 | MP1584EN (SOIC-8) | Step-down buck converters — 20V input, configurable output via feedback dividers |
| Q1 | AO3401A (SOT-23) | P-channel MOSFET — main power gate, enabled on successful PD negotiation |
| Q2 | AO3401A (SOT-23) | P-channel MOSFET — fallback gate, enabled when PD negotiation fails |
| J1 | USB-C Receptacle (16-pin) | PD charger input |

## Design Highlights

**USB-C PD Negotiation (CYPD3177)**
The CYPD3177 is a hardware-configured PD sink controller. Voltage and current requests are set entirely through resistor dividers on four configuration pins — no microcontroller or firmware needed. VBUS_MIN and VBUS_MAX pins set the acceptable voltage range (both configured for 20V), while ISNK_COARSE and ISNK_FINE set the current request (3A + 0mA). The chip communicates with the PD source over the CC1/CC2 lines and controls two external P-channel MOSFETs via gate driver outputs.

**Dual Power Path**
Two separate P-channel MOSFETs provide mutually exclusive power paths. VBUS_FET_EN drives Q1 for the main 20V rail on successful negotiation. SAFE_PWR_EN drives Q2 for a 5V/900mA fallback if the source can't meet the PD request. Each MOSFET has a 49.9kΩ gate-to-source pull-up (default off) and a 1kΩ series gate resistor.

**Buck Converter Topology (MP1584EN)**
Each output rail uses an identical MP1584EN circuit with only the feedback resistor divider changed per rail. The MP1584EN is a non-synchronous buck converter with an internal high-side MOSFET, requiring an external SS34 Schottky diode for the freewheeling current path. Output voltage is set by: V_OUT = 0.8V × (1 + R_top / R_bottom).

| Rail | R_top | R_bottom | Calculated Output |
|------|-------|----------|-------------------|
| 12V | 115kΩ | 8.2kΩ | 12.02V |
| 5V | 43kΩ | 8.2kΩ | 4.99V |
| 3.3V | 27kΩ | 8.2kΩ | 3.43V |

Shared across all three converters: 10µH inductor, 10µF input cap, 22µF output cap, 100nF bootstrap cap, 100kΩ frequency-setting resistor (~500kHz), and a series RC compensation network (10kΩ + 2.2nF).

## PCB Specifications

| Parameter | Value |
|-----------|-------|
| Dimensions | 80mm × 60mm |
| Layers | 2 (F.Cu + B.Cu) |
| Thickness | 1.6mm FR4 |
| Copper pours | GND on both layers |
| Signal trace width | 0.25mm |
| Power trace width | 0.5mm minimum |
| Design tool | KiCad 10 |

## Skills Demonstrated

- USB-C Power Delivery sink design and CC pin negotiation
- Buck converter design with feedback divider calculations
- Power path management with P-channel MOSFET switching
- Multi-rail voltage regulation from a single input
- 2-layer PCB layout with ground pours and mixed trace widths
- Component selection and datasheet-driven design
- DRC-clean board ready for fabrication

## Files

```
pd-hub/
├── gerbers/              # Fabrication-ready Gerber + drill files
├── images/               # Board renders and schematic screenshots
├── pd-hub-README.md      # This file
└── (KiCad source files kept private)
```

## Charger Requirements

Any USB-C charger advertising Power Delivery support at 20V. Typical examples include 45W, 65W, or 100W laptop chargers. If a non-PD charger is connected, the board falls back gracefully to 5V/900mA on the fallback output.