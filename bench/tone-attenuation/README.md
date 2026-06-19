# Tone Attenuation Bench

Validate **100 kHz sine** over **60 ft 20/2** — PR 2b criterion **F5** (signal margin) and DC bus margin **T6**.

Phase 0 lab work focuses on **physical plant** measurements: tone attenuation and detector-input signal-to-noise. I/Q firmware validation is **optional** (see paths below).

| Artifact | Purpose |
|----------|---------|
| [bom.md](bom.md) | Parts list |
| [setup.md](setup.md) | Physical bench wiring |
| [test-procedure.md](test-procedure.md) | **Scope path (default)** + MCU path (optional) |
| [detector-prototype.md](detector-prototype.md) | Optional MCU + I/Q breadboard |

## Validation paths

| Path | Equipment | Proves |
|------|-----------|--------|
| **Scope (default)** | DMM + oscilloscope (≥100 MHz) + passive central AFE | Attenuation margin at detector input; maps to F5 |
| **MCU (optional)** | Above + STM32 Nucleo-G071 | End-to-end I/Q `amplitude_proxy` and debounce (F1/T5) |

Use the **scope path** when detector algorithm experience is already established in firmware design (PR 13). Record results in `bench/reports/phase0-validation.md` (PR 2b).