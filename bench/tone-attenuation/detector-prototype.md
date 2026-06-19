# Central I/Q Detector — Breadboard Prototype (Optional)

**Optional** end-to-end firmware path when validating PR 13 before PCB fab. **Not required** for PR 2b if attenuation and crosstalk are measured on the **scope path** ([test-procedure.md](test-procedure.md) S1–S6).

Default Phase 0 evidence: passive central AFE + oscilloscope Vpp margin (maps to F5 the same way as `amplitude_proxy` ≥ 3× noise floor).

---

## When to use this path

| Use MCU breadboard | Use scope only |
|--------------------|----------------|
| First-time I/Q implementation | Algorithm already proven (commercial / PR 13 confidence) |
| Tuning debounce + relay F1 on bench | F1 deferred to PR 4b / PR 13 |
| UART logging of `amplitude_proxy` | Vpp + spectrum sufficient for fab gate |

Firmware path (production): `central-board/firmware/` (PR 13).

## Target MCU

| Parameter | Value |
|-----------|-------|
| MCU class | STM32G071 or similar |
| ADC | 12-bit, ≥1 Msps |
| Channels | 1 for attenuation; 6 for EMI bench |

## Sampling configuration

| Parameter | Value |
|-----------|-------|
| Sample rate | **80 kHz** per active channel |
| Burst length | **8 ms** (640 samples) |
| Scan period | **60 ms** round-robin (6-ch) |

## I/Q demodulation (reference)

```text
I += sample × cos_ref[n]   # cos_ref ∈ {+1, 0, -1, 0}
Q += sample × sin_ref[n]   # sin_ref ∈ {0, +1, 0, -1}
amplitude_proxy = I² + Q²
```

## Detection logic

```text
signal_present = (amplitude_proxy > threshold[channel])
threshold ≈ 3 × noise_floor   # from T1
```

**Watchdog:** No successful scan in **5 s** → `signal_present = false`.

## UART debug (optional)

```text
ch=0 I=1234 Q=567 amp=1876543 nf=50000 present=1
```

## Bring-up order

1. Same passive AFE as scope path — verify ADC samples match scope Vpp trend.
2. Tone on 10 ft — `amplitude_proxy` rises.
3. Run MCU tests T1–T5 in [test-procedure.md](test-procedure.md).
4. Record in `bench/reports/phase0-validation.md`.