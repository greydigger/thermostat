# Tone Attenuation — Test Procedure

Execute during **Phase 0**. Record all results in `bench/reports/phase0-validation.md` (PR 2b).

## Prerequisites

- [setup.md](setup.md) complete
- [detector-prototype.md](detector-prototype.md) firmware flashed
- Oscilloscope and DMM calibrated

---

## T1 — Noise Floor (per channel)

**Goal:** Baseline `amplitude_proxy` with tone **off**.

1. PSU on, tone generator **disabled**.
2. Run one full 60 ms scan; record `I²+Q²` as `noise_floor`.
3. Repeat 100 scans; use **max** observed value as conservative floor.
4. Record: supply voltage, cable length, inductor value.

**Pass (informational):** Stable floor; no sporadic spikes >2× median.

---

## T2 — Tone Amplitude Sweep (10 ft reference)

**Goal:** Establish injector amplitude for 200–500 mVpp at thermostat.

1. Use **10 ft** cable.
2. Sweep generator amplitude until scope shows **200–500 mVpp** on bus (AC).
3. Record generator setting and measured mVpp.
4. Confirm central `amplitude_proxy` well above noise floor.

---

## T3 — 60 ft Detection (F5)

**Goal:** PR 2b pass — `I²+Q² ≥ 3× noise_floor` at 60 ft.

1. Swap to **60 ft** cable; keep same generator setting as T2.
2. Tone **on**; collect 100 bursts.
3. Compute `signal_max` = max `amplitude_proxy`.
4. Margin: `M = signal_max / noise_floor`.

| Result | Criterion |
|--------|-----------|
| **PASS** | M ≥ **3.0** |
| FAIL | M < 3.0 → increase tone amplitude, tune inductor/divider, or revisit KD14 |

Record: mVpp at far end, mVpp at central AFE, `noise_floor`, `signal_max`, margin M.

---

## T4 — Frequency Offset

**Goal:** ±0.1% tone tolerance (100 kHz ±100 Hz).

1. At 60 ft, detune generator to **99.9 kHz** and **100.1 kHz**.
2. Confirm detection still passes (M ≥ 3×).
3. Detune to **98 kHz** and **102 kHz** (±2% limit); note marginal/fail points.

---

## T5 — Fail-Safe Release Timing (F1)

**Goal:** Tone stop → relay open **< 2 s**.

1. Assert tone; wait for relay proxy **closed** (stable ≥5 s).
2. Disable tone (generator off or MOSFET gate low).
3. Measure time from tone off to relay **open** (scope on relay coil or LED).

| Result | Criterion |
|--------|-----------|
| **PASS** | < **2.0 s** |
| FAIL | ≥ 2.0 s → reduce central debounce in prototype firmware |

Record: debounce setting, measured release time.

---

## T6 — DC Load Margin

**Goal:** Confirm bus drop within design at 150 mA.

1. Apply **150 mA** electronic load (or resistor bank) on 60 ft bus.
2. Measure `V_far`; compute drop.

| Result | Criterion |
|--------|-----------|
| **PASS** | V_far ≥ **11.81 V** (12 V − 188 mV) |

---

## Data Sheet (copy to PR 2b report)

```text
Date:
Cable: 20/2, length ___ ft, routing notes:
Inductor: ___ mH
Divider ratio:
noise_floor:
signal_max @ 60 ft:
Margin M:
Tone mVpp @ far / @ central:
F1 release time: ___ ms
V_far @ 150 mA: ___ V
Threshold setting (if any):
```