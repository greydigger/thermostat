# Bus-Powered Smart Hydronic Zone Thermostat

DIY prototype for a 6-zone hydronic heating system using a 2-wire (R/W) bus with DC power and OOK tone encoding. See [DESIGN.md](DESIGN.md) for the full system specification.

## Repository Layout

```
central-board/     Central power & relay board (KiCad HW, MCU firmware)
thermostat/        Per-zone smart thermostat (KiCad HW, ESPHome firmware)
bench/             Bench test fixtures, reports, and bring-up logs
docs/              Protocol specs, runbooks, and design references
toolchain/         Pinned tool versions for reproducible builds
.github/workflows/ CI: ESPHome compile-only, KiCad ERC/DRC
```

## Prerequisites

| Tool | Version | Notes |
|------|---------|-------|
| KiCad | 10.0.3 | Schematic and PCB layout |
| ESPHome | 2025.5.x | Thermostat firmware (`toolchain/esphome-version.txt`) |
| ESP-IDF | 5.3.x | Via PlatformIO for `esp32-c6-devkitc-1` |
| PlatformIO espressif32 | 6.9.x | Central board firmware (when added) |

See [toolchain/versions.md](toolchain/versions.md) for full version pins and install notes.

## Quick Start

### ESPHome (thermostat firmware)

```bash
pip install "esphome==$(cat toolchain/esphome-version.txt)"
esphome config thermostat/firmware/thermostat-zone.yaml
esphome compile thermostat/firmware/thermostat-zone.yaml
```

### KiCad (hardware)

Open the project file when present:

```bash
kicad-cli sch erc central-board/hw/central-board.kicad_pro   # when added
kicad-cli pcb drc central-board/hw/central-board.kicad_pro    # when added
```

## Development Workflow

1. Feature branches for all work (`feature/pr-N-description`).
2. Bench validation gate (PR 2b) must pass before PCB Gerber orders.
3. CI runs on every push: ESPHome `compile_only` and KiCad ERC/DRC on `*.kicad_pro` files.

## Status

| Phase | Status |
|-------|--------|
| PR 1 — Scaffold & tooling | Complete |
| PR 2 — Bench harness | In progress |
| PR 2b — Go/no-go gate | Blocks PCB fab |