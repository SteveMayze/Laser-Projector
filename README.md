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

The chassis is designed for 3D printing and holds the two stepper motors and mirrors in alignment so that the laser beam can be deflected in both the X and Y axes. Design files are located in `3d-models/chassis/`.

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

## Getting Started

> This project is in its initial stage. Design files and firmware will be added as the project progresses.

### Prerequisites

- [KiCad](https://www.kicad.org/) for viewing and editing hardware schematics and PCB layouts
- A 3D printer and slicer software (e.g., [PrusaSlicer](https://www.prusa3d.com/prusaslicer/) or [Cura](https://ultimaker.com/software/ultimaker-cura/)) for printing chassis parts
- A suitable embedded toolchain for building and flashing the firmware

## License

See [LICENSE](LICENSE).
