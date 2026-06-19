# Tone Attenuation — Test Procedure

Execute during **Phase 0**. Record all results in `bench/reports/phase0-validation.md` (PR 2b).

## Validation paths

| Path | When to use | F5 evidence |
|------|-------------|-------------|
| **Scope (default)** | Detector I/Q algorithm already validated; measure plant attenuation | Central AFE **Vpp** margin ≥ **3×** idle noise |
| **MCU (optional)** | End-to-end firmware bring-up before PR 13 | `amplitude_proxy` margin ≥ **3×** noise floor |

Both paths use the same injector amplitude (200–500 mVpp at far end) and passive central AFE.

---

## Prerequisites — scope path

- [setup.md](setup.md) complete (passive AFE + scope probe point)
- 12 V bench supply and DMM
- Oscilloscope **≥100 MHz**, AC coupling (e.g. Rigol MSO1104Z + SA spectrum option)
- External **100 kHz sine** source (function generator or Wein oscillator — MSO1104Z without `-S` has **no built-in AWG**)
- Tone **off** for noise measurements; tone **on** for signal measurements

## Prerequisites — MCU path (optional)

- Scope path prerequisites
- [detector-prototype.md](detector-prototype.md) firmware flashed on STM32 Nucleo-G071

---

## Scope path

Probe the **central AFE output** (after divider/clamp) — the node that feeds the production ADC.

### S1 — Noise floor (tone off)

**Goal:** Baseline idle level at detector input.

1. PSU on at **12.0 V**; all tone sources **disabled**.
2. Scope CH on AFE output, **AC coupling**, adequate vertical scale.
3. Capture **≥10 s**; measure **Vpp** (or RMS × 2√2 for sine-dominated residual).
4. Record **`noise_Vpp`** = max Vpp observed (conservative) or stable typical value.

**Pass (informational):** Stable trace; no sporadic bursts >2× median Vpp.

---

### S2 — Injector trim (10 ft reference)

**Goal:** Establish tone amplitude for **200–500 mVpp** at far end.

1. Use **10 ft** cable.
2. Enable **100 kHz sine** at far-end injector.
3. Scope on bus at **far end** (AC): adjust source until **200–500 mVpp**.
4. Record generator settings and far-end mVpp.
5. Measure **central AFE Vpp** with tone on — confirm well above `noise_Vpp`.

---

### S3 — 60 ft attenuation (F5)

**Goal:** PR 2b pass — central signal ≥ **3×** idle noise at 60 ft.

1. Swap to **60 ft** cable; **same injector settings** as S2.
2. Tone **on**; measure **`signal_Vpp`** at central AFE output.
3. Margin: **`M = signal_Vpp / noise_Vpp`**.

| Result | Criterion |
|--------|-----------|
| **PASS** | M ≥ **3.0** |
| FAIL | M < 3.0 → increase tone amplitude, tune inductor/divider, or revisit KD14 |

4. **Spectrum (optional):** SA/FFT — dominant peak at **100 kHz** on active run.

Record: far-end mVpp, central AFE mVpp, `noise_Vpp`, `signal_Vpp`, M, cable routing notes.

---

### S4 — Frequency offset (scope check)

**Goal:** Confirm margin holds within tone tolerance.

1. At 60 ft, detune source to **99.9 kHz** and **100.1 kHz**; re-check M ≥ 3.
2. Detune to **98 kHz** and **102 kHz** (±2% spec); note marginal/fail points.

---

### S5 — Fail-safe release (F1, optional for scope path)

**Goal:** Tone stop → relay open **< 2 s** (validates central debounce + relay, not I/Q math).

Defer to **PR 4b / PR 13** if no relay prototype on bench. If testing:

1. Assert tone; close relay proxy (MCU or manual).
2. Disable tone; scope timing from tone off → relay/LED open.

| Result | Criterion |
|--------|-----------|
| **PASS** | < **2.0 s** |

---

### S6 — DC load margin

**Goal:** Bus drop within design at 150 mA.

1. On **60 ft** bus, apply **150 mA** load at far end.
2. DMM `V_far` on R–W.

| Result | Criterion |
|--------|-----------|
| **PASS** | V_far ≥ **11.81 V** (12 V − 188 mV) |

---

## MCU path (optional)

Use when validating PR 13 firmware on breadboard. Replaces S1/S3 numeric readout with `amplitude_proxy = I²+Q²`.

### T1 — Noise floor

1. PSU on, tone **off**.
2. Log 100 bursts; **`noise_floor`** = max `amplitude_proxy`.

### T2 — Injector trim (10 ft)

Same as S2; confirm `amplitude_proxy` rises above noise floor.

### T3 — 60 ft detection (F5)

1. **60 ft** cable; tone on.
2. **`signal_max`** = max `amplitude_proxy`; **M = signal_max / noise_floor** ≥ 3.

### T4 — Frequency offset

As S4, using M from `amplitude_proxy`.

### T5 — Fail-safe release (F1)

As S5 with MCU debounce + relay proxy GPIO.

---

## Data sheet (copy to PR 2b report)

```text
Validation path: Scope / MCU
Date:
Cable: 20/2, length ___ ft, routing notes:
Inductor: ___ mH
Divider ratio:
Scope: model ___, probe ___, coupling AC

--- Scope path ---
noise_Vpp (tone off):
signal_Vpp @ 60 ft (tone on):
Margin M (signal/noise):
Far-end tone mVpp:
100 kHz spectrum OK? Y/N

--- MCU path (if used) ---
noise_floor:
signal_max @ 60 ft:
Margin M (amp proxy):

--- Common ---
F1 release time (if measured): ___ ms
V_far @ 150 mA: ___ V
Notes:
```