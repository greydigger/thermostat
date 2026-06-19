# EMI Bench — Test Procedure

Supports PR 2b criterion **F6**: *No cross-talk false triggers on idle channels.*

Use the **scope path** by default. MCU path is optional ([detector-prototype.md](../tone-attenuation/detector-prototype.md)).

**Reference margin:** From [S3](../tone-attenuation/test-procedure.md), `signal_Vpp` on an active channel at 60 ft. An idle channel **passes** if crosstalk Vpp stays **< signal_Vpp / 3** (same 3× rule as F5).

---

## Scope path

Probe each channel's **central AFE output** (AC coupling). Let **`V_act`** = active-channel `signal_Vpp` from S3 (60 ft, same bundle geometry if possible).

### C0 — Baseline (all idle)

1. All tone sources **off**; 12 V energized.
2. Measure Vpp on all 6 AFE outputs for **≥2 min**.
3. Record max idle Vpp per channel.

| Result | Criterion |
|--------|-----------|
| **PASS** | No channel shows sustained Vpp approaching `V_act / 3` |

---

### C1 — Single active channel

For each channel **N** = 1..6:

1. Enable tone **only** on channel N (100 kHz sine).
2. Run **≥5 min**.
3. **Active:** AFE Vpp on N ≈ expected (similar to S3 for that cable length).
4. **Idle:** AFE Vpp on all other channels **< V_act / 3**.
5. **SA/FFT:** Idle channels — no dominant 100 kHz peak near active level.

| Result | Criterion |
|--------|-----------|
| **PASS** | Crosstalk on idle ch **< V_act / 3** each |

---

### C2 — Six simultaneous tones (F6)

1. Enable **100 kHz sine** on **all 6** channels.
2. Run **≥30 min**.
3. Log AFE Vpp per channel (all should be “active” levels).
4. Compare each channel's Vpp to its **C0 idle floor** — must remain well above idle, below clip.
5. Re-check: with all on, no channel that is **muted** mid-test shows false “active” Vpp (see C3).

| Result | Criterion |
|--------|-----------|
| **PASS** | All 6 carry tone; idle-channel crosstalk limits from C1 never exceeded when others active |

---

### C3 — Staggered assert

1. Channels 1–3 on; 4–6 off — **2 min**; measure idle 4–6 Vpp.
2. Swap: 4–6 on; 1–3 off — **2 min**.
3. Toggle one channel at **1 Hz** for 60 s; others off — monitor idle Vpp.

| Result | Criterion |
|--------|-----------|
| **PASS** | Off channels stay **< V_act / 3** during transitions |

---

### C4 — Harmonic check (qualitative)

1. Single active channel; scope FFT/SA at far end.
2. Fundamental **100 kHz** dominates (KD15).

| Result | Criterion |
|--------|-----------|
| **PASS** | Sine-dominated; weak odd-harmonic comb suggests square wave — reject source |

---

## MCU path (optional)

Replace Vpp checks with `signal_present[ch]` and `amplitude_proxy` vs `threshold[ch]`.

### E0 — Baseline

All tones off; 10 min — 0 false `signal_present`.

### E1 — Single active

Tone on channel N only; 5 min — only N asserts.

### E2 — Six simultaneous (F6)

All on; 30 min — all detect, 0 false on idle.

### E3 — Staggered assert

As C3 with `signal_present` logging.

---

## Data sheet (copy to PR 2b report)

```text
Validation path: Scope / MCU
Date:
Bundle: length ___ ft, spacing ___, routing
V_act reference (from S3): ___ mVpp
Crosstalk limit (V_act/3): ___ mVpp

C0 max idle Vpp per ch: [ ... ]
C1 results ch 1-6: PASS/FAIL (idle Vpp values)
C2 30 min notes:
C3 transition worst idle Vpp: ___
C4 spectrum OK? Y/N

MCU path (if used):
E0 false events: ___
E1 results: ___
E2 false events: ___
Notes / mitigations:
```