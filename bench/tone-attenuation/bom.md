# Tone Attenuation Bench — BOM

Minimum parts to run PR 2 tone-attenuation tests before PCB fab.

## Cable & Power

| Qty | Part | Notes |
|-----|------|-------|
| 1 | 20/2 thermostat cable, **60 ft** | Unshielded; actual installed wire type if known |
| 1 | 20/2 thermostat cable, **10 ft** | Short reference run |
| 1 | 12 V DC bench supply, **≥2 A** | Current-limited; 12–18 V adjustable for margin tests |
| 1 | DMM | Bus voltage, loop drop |
| 2 | Banana-to-wire leads | R/W connections |

## Tone Injection (Thermostat End — Simulated)

Use either **Option A** (function generator, fastest) or **Option B** (breadboard oscillator, matches production topology).

### Option A — Function Generator (recommended for PR 2b)

| Qty | Part | Notes |
|-----|------|-------|
| 1 | Function generator | Sine, **100 kHz**, adjustable amplitude |
| 1 | Coupling cap, **100 nF** ceramic | In series with gen output |
| 1 | Resistor, **100 Ω** | Series limit / Z match (tune on bench) |
| 1 | N-channel MOSFET (e.g. 2N7002) | Optional: switch tone on/off for F1 timing |
| 1 | 10 kΩ resistor | Gate pull-down, default-off |

### Option B — Sine Oscillator Breadboard

| Qty | Part | Notes |
|-----|------|-------|
| 1 | Wein or Colpitts 100 kHz oscillator | KD15 — sine only |
| 1 | Coupling cap, **100 nF** | AC injection to bus |
| 1 | N-FET bus driver | Same as production concept |
| — | Passive set per calculated Wein/Colpitts | Trim to 100 kHz ±2% |

## Central Detector Prototype (Far / Central End)

| Qty | Part | Notes |
|-----|------|-------|
| 1 | MCU dev board | STM32 Nucleo-G071RB or equivalent (12-bit ADC, ≥1 Msps) |
| 1 | Inductor, **1.0 mH** | Feed inductor (470 µH–2.2 mH swap kit optional) |
| 1 | Cap, **100 nF** | AC coupling |
| 2 | Resistors, **10 kΩ + 3.3 kΩ** | Divider starting point (tune) |
| 1 | TVS or Schottky clamp | 3.3 V ADC protection |
| 1 | 8:1 analog mux | ADG708 or CD4051 (single channel OK for PR 2) |
| 1 | SPST relay module or LED+driver | Visual relay-close proxy for F1 |
| 1 | Oscilloscope, **≥100 MHz** | Verify sine, amplitude, tone on/off |

## Optional

| Qty | Part | Notes |
|-----|------|-------|
| 1 | Second 60 ft spool | Spare if first cable damaged |
| 1 | Cable reel / tape | Keep 60 ft run neat; document routing |