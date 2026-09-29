# 9-Key Macropad with OLED + Rotary Encoder

A compact USB programmable macropad with a 3×3 switch grid, rotary encoder, and OLED display. Designed in KiCad, QMK firmware compatible, with a 3D-printable case.

![3D Render](./images/3d-render-front.png)

## Features

- **9 mechanical switches** (MX-compatible) in a 3×3 grid
- **Rotary encoder** with push-button (volume, scroll, or custom function)
- **SSD1306 OLED display** (128×64) showing active layer and key assignments
- **Pro Micro ATmega32U4** controller (USB-C)
- **QMK firmware** compatible with full layer and macro support
- **Compact PCB** at 69mm × 93mm — under 100×100mm for low-cost fabrication
- **3D-printable case** with 5° typing angle

## Technical Details

| Parameter | Value |
|-----------|-------|
| Matrix | 4 rows × 3 columns (9 switches + encoder push) |
| Controller | Pro Micro ATmega32U4 (18 GPIO, 11 used) |
| Display | SSD1306 128×64 OLED (I2C) |
| PCB | 2-layer, 1.6mm FR4 |
| Board dimensions | 69mm × 93.4mm |
| Fabrication cost | ~$2–5 (JLCPCB) / ~₹800 (LionCircuits) |

### GPIO Allocation

| Function | Pins | GPIO |
|----------|------|------|
| Matrix rows | 4 | D4, D5, D6, D7 |
| Matrix columns | 3 | D8, D9, D10 |
| Encoder rotation | 2 | D14, D16 |
| I2C (OLED) | 2 | D2 (SDA), D3 (SCL) |
| **Total** | **11** | 7 spare for future use |

### Layout

```
[   OLED   ] [Encoder]
  [1] [2] [3]
  [4] [5] [6]
  [7] [8] [9]
```

## Design Highlights

**Efficient GPIO usage:** A 4×3 matrix handles 9 switches plus the encoder push-button using only 7 GPIO pins. The OLED shares the hardware I2C bus. 11 total pins used out of 18 available, leaving headroom for future features like RGB underglow.

**Sub-100mm PCB:** The entire design fits within a 69×93mm footprint, qualifying for minimum-price tiers at most PCB fabricators. Five boards cost under ₹800 domestically.

**QMK compatibility:** The Pro Micro and matrix layout follow QMK conventions, making firmware configuration straightforward with full support for layers, macros, tap-dance, and encoder mapping.

## File Structure

```
macropad-9key/
├── kicad/
│   ├── Macropad.kicad_sch
│   ├── Macropad.kicad_pcb
│   └── Macropad.kicad_pro
├── gerbers/
│   └── Macropad-gerbers.zip
├── case/
│   └── macropad_case.scad      # Bottom + top plate (OpenSCAD)
├── images/
│   ├── 3d-render-front.png
│   ├── 3d-render-back.png
│   ├── schematic.png
│   └── pcb-layout.png
└── README.md
```

## Bill of Materials

| Component | Quantity | Package |
|-----------|----------|---------|
| Pro Micro ATmega32U4 | 1 | USB-C variant |
| SSD1306 OLED 0.96" | 1 | 4-pin I2C module |
| MX-compatible switches | 9 | Through-hole |
| 1N4148W diodes | 10 | SOD-123 |
| 4.7kΩ resistors | 2 | 0805 SMD |
| EC11 rotary encoder | 1 | Vertical, with switch |
| Tactile reset switch | 1 | SMD/THT |
| M2 hardware | 1 set | Screws + standoffs |

**Estimated total build cost: ₹1,200–1,800**

## Use Cases

- **Coding shortcuts:** Copy, paste, undo, save, run, debug — one-handed
- **Media control:** Play/pause, skip, volume (encoder), mute (encoder push)
- **Streaming:** Scene switching, mute mic, start/stop recording
- **Productivity:** Window management, virtual desktop switching, app launching
- **Gaming:** Ability macros, push-to-talk, quick-buy bindings

All keys are fully reprogrammable via QMK — switch functions anytime by reflashing.

## Fabrication

- **PCB:** Upload `gerbers/Macropad-gerbers.zip` to any fab house. 2-layer, 1.6mm, HASL.
- **Case:** Open `macropad_case.scad` in OpenSCAD, export bottom and top plate as separate STLs.
- **Firmware:** QMK with custom keyboard definition.

## License

Open source hardware — MIT License.