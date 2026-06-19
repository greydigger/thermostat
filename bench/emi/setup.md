# EMI Bench — Setup

## Goal

Simulate **6 zone home runs** in close proximity (worst-case routing) and verify **no false heat calls** on idle channels when one or all channels carry tone.

## Minimum Physical Layout

```text
Central PSU + detector mux (6 ch)
    │
    ├── Cable 1 (60 ft or 20 ft folded) ── Tone injector 1
    ├── Cable 2 ── Tone injector 2
    ├── ...
    └── Cable 6 ── Tone injector 6

Cables bundled parallel for ≥30 ft (tape together — mimics crowded joists)
```

| Parameter | Recommendation |
|-----------|----------------|
| Cable type | Same 20/2 as production |
| Bundle length | **≥30 ft** parallel; 60 ft if space allows |
| Spacing | **≤1 in** center-to-center in bundle |
| Ground reference | All W returns tied at central star point |

## Tone Sources

- **6×** independent **100 kHz sine** sources (generators or oscillators).
- Phase uncorrelated between channels (acceptable).
- Amplitude per channel: same setting validated in [tone-attenuation T2](../tone-attenuation/test-procedure.md).

## Central Detector

- Full **6-channel mux** round-robin per [detector-prototype.md](../tone-attenuation/detector-prototype.md).
- Per-channel `threshold[]` from T3 (can share nominal value initially).

## What Is *Not* Required for PR 2

- Anechoic chamber
- Certified EMC lab
- FCC filing

This is **DIY characterization** per DESIGN scope (KD11).

## Safety

- Same 12 V bench limits as tone-attenuation setup.
- No connection to live hydronic or line voltage.