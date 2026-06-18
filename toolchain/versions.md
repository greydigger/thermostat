# Toolchain Versions

Pinned versions for reproducible builds across hardware and firmware workstreams.

## Hardware

| Tool | Version | Install |
|------|---------|---------|
| KiCad | **9.0** | [kicad.org/download](https://www.kicad.org/download/) |

## Thermostat Firmware (ESPHome)

| Tool | Version | Pin file |
|------|---------|----------|
| ESPHome | **2025.5.x** | `toolchain/esphome-version.txt` |
| ESP-IDF (via ESPHome) | **5.3.x** | Bundled with ESPHome for `esp32-c6-devkitc-1` |
| Target board | `esp32-c6-devkitc-1` | — |

```bash
pip install "esphome==$(cat toolchain/esphome-version.txt)"
```

## Central Board Firmware (PlatformIO)

| Tool | Version | Notes |
|------|---------|-------|
| PlatformIO | latest stable | `pip install platformio` |
| espressif32 platform | **6.9.x** | Selected in PR 3 |
| ESP-IDF | **5.3.x** | For `esp32-c6-devkitc-1` or STM32 (TBD in PR 3) |

## CI

| Check | Trigger | Scope |
|-------|---------|-------|
| ESPHome `compile_only` | Push / PR | `thermostat/firmware/thermostat-zone.yaml` |
| KiCad ERC | Push / PR | All `*.kicad_pro` files (when present) |
| KiCad DRC | Push / PR | All `*.kicad_pro` files (when present) |