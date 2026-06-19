# Tone Attenuation Bench — BOM

Minimum parts for PR 2b **before PCB fab**. Default validation uses **scope + passive AFE** (no MCU required).

## Required — scope path

### Cable & power

| Qty | Part | Notes |
|-----|------|-------|
| 1 | 20/2 thermostat cable, **60 ft** | Unshielded; match installed wire if known |
| 1 | 20/2 thermostat cable, **10 ft** | Injector trim (S2) |
| 1 | 12 V DC bench supply, **≥2 A** | Current-limited (e.g. Eventek KPS305D) |
| 1 | DMM | Bus voltage, loop drop |
| 2 | Banana-to-wire leads | R/W connections |

### Tone injection (far end)

Rigol **MSO1104Z** (without `-S`) has **no AWG**; `SA-DS1000Z` is spectrum analysis only. Use an external sine source.

**Option A — Function generator + driver emulator**

| Qty | Part | Notes |
|-----|------|-------|
| 1 | Function generator | Sine, **100 kHz** — drives emulator; see [impedance-model.md](impedance-model.md) |
| 6× | `R_s` resistor kit | **470 Ω–2.2 kΩ** per channel (tune to PR 7 `Z_out`) |
| 6× | `C_inj` **100 nF** | Per-channel injection cap |
| 6× | `C_bulk` **10–47 µF** | Far-end buck input emulator per channel |
| 6× | `R_bleed` **1 kΩ** (optional) | Per-channel load emulator |

**Option B — Wein / Colpitts + production driver topology**

| Qty | Part | Notes |
|-----|------|-------|
| 1–6× | 100 kHz sine oscillator | KD15 — sine only; one per EMI channel or shared + gating |
| 6× | `C_inj` **100 nF** + N-FET driver | Match `tone-driver.kicad_sch` (PR 7) |
| 6× | Far-end load emulator | As Option A |

### Central AFE + measurement (near end)

| Qty | Part | Notes |
|-----|------|-------|
| 1 | `L_dc_block` **1.0 mH** | **Required** — PSU HF isolation on R feed |
| 6× | Inductor, **1.0 mH** | Feed inductor per zone (1 for S3; **6 for EMI**) |
| 6× | Cap, **100 nF** | AC coupling per zone |
| 6× | Resistor pair **10 kΩ + 3.3 kΩ** | Divider per zone (tune) |
| 6× | TVS or Schottky clamp | Per-zone ADC node protection |
| 1 | Oscilloscope, **≥100 MHz** | AC Vpp at AFE output; FFT/SA for 100 kHz check |

## Optional — MCU path

Add only for end-to-end I/Q firmware bring-up (see [detector-prototype.md](detector-prototype.md)).

| Qty | Part | Notes |
|-----|------|-------|
| 1 | STM32 Nucleo-G071RB | 12-bit ADC, ≥1 Msps |
| 1 | 8:1 analog mux | ADG708 or CD4051 (6-ch EMI) |
| 1 | SPST relay module or LED | F1 timing with debounce firmware |
| 1 | N-channel MOSFET + 10 kΩ | Optional tone gate for T5 |

## Optional — general

| Qty | Part | Notes |
|-----|------|-------|
| 1 | Second 60 ft spool | Spare cable |
| 1 | Cable reel / tape | Neat 60 ft run; document routing |