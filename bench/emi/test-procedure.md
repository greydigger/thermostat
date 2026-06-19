# EMI Bench — Test Procedure

Supports PR 2b criterion **F6**: *No cross-talk false triggers on idle channels.*

Also exercises rollout **Phase 0** item: *EMI 1 vs 6 sources.*

---

## E0 — Baseline (all idle)

1. All 6 tone sources **off**; 12 V bus energized.
2. Run detector for **10 minutes** (600 scans).
3. Record any `signal_present[ch] == TRUE` events.

| Result | Criterion |
|--------|-----------|
| **PASS** | 0 false asserts on all channels |

---

## E1 — Single Active Channel

For each channel **N** = 1..6:

1. Enable tone **only** on channel N.
2. Run **5 minutes**.
3. Verify `signal_present[N] == TRUE` stable.
4. Verify `signal_present[all others] == FALSE`.

| Result | Criterion |
|--------|-----------|
| **PASS** | Active only on N; 0 false on others |

---

## E2 — Six Simultaneous Tones (F6)

1. Enable **100 kHz sine** on **all 6** channels simultaneously.
2. Run **30 minutes**.
3. Log per-channel `amplitude_proxy` min/max/mean.
4. Verify each active channel detects; **no idle channel** falsely asserts.

| Result | Criterion |
|--------|-----------|
| **PASS** | All 6 detect; 0 false asserts on any channel |
| FAIL | Any false assert → reposition bundle, add spacing, or reduce tone amplitude |

---

## E3 — Staggered Assert (burst crosstalk)

1. Channels 1–3 on; 4–6 off for 2 min.
2. Swap: 4–6 on; 1–3 off for 2 min.
3. Rapid toggle one channel at 1 Hz for 60 s while others idle.

| Result | Criterion |
|--------|-----------|
| **PASS** | No false assert on off channels during transitions |

---

## E4 — Harmonic Check (qualitative)

1. On single active channel, inspect far-end tone with scope FFT.
2. Confirm fundamental **100 kHz** dominates; no strong square harmonics (3f, 5f).

| Result | Criterion |
|--------|-----------|
| **PASS** | Sine-dominated spectrum (KD15) |

---

## Data Sheet (copy to PR 2b report)

```text
Date:
Bundle description: length ___ ft, spacing ___
Thresholds per ch: [ ... ]
E0 false events: ___
E1 results (1..6): PASS/FAIL each
E2 duration: 30 min, false events: ___
E3 transition false events: ___
Notes / mitigations:
```