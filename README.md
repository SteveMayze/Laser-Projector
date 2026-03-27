# Laser-Projector

A device to project a laser point to create 2 dimensional images and patterns.

## Overview

The Laser Projector is a device that rotates two mirrors, each driven by a stepper motor, to deflect a low-power laser beam and trace two-dimensional images or patterns on a wall or ceiling. While professional laser projectors typically use galvanometers for high-speed mirror deflection, this project repurposes stepper motors salvaged from discarded printers as a practical and accessible alternative.

## Repository Structure

```
Laser-Projector/
├── 3d-models/
│   └── chassis/          # 3D-printable parts for the mechanical chassis
├── hardware/
│   ├── motor-driver/     # KiCad schematic and PCB for the stepper motor driver
│   │   ├── symbols/      # Custom KiCad schematic symbols
│   │   └── footprints/   # Custom KiCad PCB footprints
│   └── microcontroller-board/  # KiCad schematic and PCB for the microcontroller
│       ├── symbols/      # Custom KiCad schematic symbols
│       └── footprints/   # Custom KiCad PCB footprints
└── firmware/             # Firmware for the microcontroller
    ├── src/              # Source files
    └── include/          # Header files
```

## Hardware

### Mechanical

The chassis is designed for 3D printing and forms one integrated unit that holds the two stepper motors and mirrors in alignment so that the laser beam can be deflected in both the X and Y axes. It also provides mounting space for the motor controller board(s) (it is yet to be decided whether one combined board or two separate boards are more practical) and the microcontroller board. Design files are located in `3d-models/chassis/`.

### Electronics

KiCad is used for all schematic capture and PCB layout. Two boards are planned:

- **Motor Driver** (`hardware/motor-driver/`) — drives the two stepper motors that rotate the deflection mirrors.
- **Microcontroller Board** (`hardware/microcontroller-board/`) — hosts the microcontroller responsible for coordinating motor movements, laser control, and communication.

Custom schematic symbols and PCB footprints specific to each board are kept alongside the KiCad project files in their respective `symbols/` and `footprints/` subdirectories.

### Laser

A low-power laser module is used as the light source. The laser is switched on and off by the microcontroller in coordination with mirror positioning to draw images.

## Firmware

Firmware source code is located in `firmware/`. It is responsible for:

- Controlling the stepper motors to position the mirrors
- Switching the laser on and off at the correct positions
- Interpreting image or pattern data and converting it to motor steps

## Drawing Grammar

The projector interprets a simple command language — inspired by turtle graphics but
adapted for a laser projector — to control mirror position and laser state. Commands
are read either from an SD card file or streamed from an ESP32 comms module over UART.

The terminology follows industry-standard laser show conventions: `BLANK` suppresses
the beam during repositioning moves, and `UNBLANK` enables it when drawing.

### Coordinate system

All positions are expressed in **Cartesian coordinates on the XY projection plane**
(e.g. millimetres at a fixed projection distance). A calibration step maps physical
units to motor steps (recorded as a steps-per-unit constant in firmware).

### Command set

| Command                  | Description                                              |
|--------------------------|----------------------------------------------------------|
| `UNBLANK`                | Turn the laser on (beam visible, drawing)                |
| `BLANK`                  | Turn the laser off (beam suppressed, repositioning)      |
| `GOTO x y`               | Move to absolute position (x, y)                        |
| `MOVE dx dy`             | Move relative to current position                        |
| `SPEED v`                | Set traversal speed (units/sec)                          |
| `DWELL t`                | Pause at current position for t milliseconds             |
| `CIRCLE r`               | Draw a circle of radius r centred at current position    |
| `ARC r startDeg endDeg`  | Draw an arc of radius r between two angles               |

Higher-level macros (e.g. `TEXT`, `RECT`) can be built on top of these primitives
in firmware.

### Example program — a 10 mm square

```
BLANK
GOTO 0 0
UNBLANK
MOVE 10 0
MOVE 0 10
MOVE -10 0
MOVE 0 -10
BLANK
```

## Data Input

Two input methods are supported (they can coexist):

### SD Card

A plain-text drawing file (`.ldr` — Laser Drawing Record — or `.txt`) is placed on an SD card connected to
the microcontroller over SPI. On power-up (or on receipt of a trigger), the MCU
reads the file, parses the commands and executes them.

### ESP32 Comms Module

An ESP32 connected to the MCU over UART can stream commands in real time. The ESP32
can receive drawing programs over Wi-Fi or Bluetooth, acting as a wireless front-end.
This enables remote control and dynamic pattern generation without re-flashing the MCU.

### Firmware pipeline

```
Input source (SD / ESP32)
        │  raw text commands
        ▼
  Command Parser        — tokenises and validates the drawing grammar
        │  parsed command structs
        ▼
  Trajectory Planner    — converts commands to timed (X, Y, laser) events,
        │                 applying speed limits and coordinate mapping
        ▼
  Motion Executor (MCU) — issues step pulses to motor drivers and
                          gates the laser in synchronisation
```

## Getting Started

> This project is in its initial stage. Design files and firmware will be added as the project progresses.

### Prerequisites

- [KiCad](https://www.kicad.org/) for viewing and editing hardware schematics and PCB layouts
- A 3D printer and slicer software (e.g., [PrusaSlicer](https://www.prusa3d.com/prusaslicer/) or [Cura](https://ultimaker.com/software/ultimaker-cura/)) for printing chassis parts
- A suitable embedded toolchain for building and flashing the firmware

## License

See [LICENSE](LICENSE).
