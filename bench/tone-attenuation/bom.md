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

**Option A — Function generator (fastest)**

| Qty | Part | Notes |
|-----|------|-------|
| 1 | Function generator | Sine, **100 kHz**, adjustable amplitude |
| 1 | Coupling cap, **100 nF** ceramic | Series with output |
| 1 | Resistor, **100 Ω** | Series limit (tune) |

**Option B — Wein / Colpitts breadboard**

| Qty | Part | Notes |
|-----|------|-------|
| 1 | 100 kHz sine oscillator | KD15 — sine only, not square |
| 1 | Coupling cap, **100 nF** | AC injection to bus |
| 1 | N-FET bus driver | Production topology |

### Central AFE + measurement (near end)

| Qty | Part | Notes |
|-----|------|-------|
| 1 | Inductor, **1.0 mH** | Feed inductor (470 µH–2.2 mH swap kit optional) |
| 1 | Cap, **100 nF** | AC coupling |
| 2 | Resistors, **10 kΩ + 3.3 kΩ** | Divider starting point (tune) |
| 1 | TVS or Schottky clamp | Protect scope / future ADC node |
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