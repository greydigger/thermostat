# EMI Bench — Setup

## Goal

Simulate **6 zone home runs** in close proximity and verify **idle channels do not pick up enough tone** to exceed the detect margin used in [tone-attenuation S3](../tone-attenuation/test-procedure.md).

## Minimum physical layout

```text
Central PSU + 6× passive AFE (or 6 scope probe points)
    │
    ├── Cable 1 ── Tone injector 1
    ├── Cable 2 ── Tone injector 2
    ├── ...
    └── Cable 6 ── Tone injector 6

Cables bundled parallel ≥30 ft (tape together)
```

| Parameter | Recommendation |
|-----------|----------------|
| Cable type | Same 20/2 as production |
| Bundle length | **≥30 ft** parallel; 60 ft if space allows |
| Spacing | **≤1 in** center-to-center in bundle |
| Ground reference | All W returns tied at central star point |

## Measurement — scope path (default)

- One **passive AFE + scope CH** per channel (or mux + single scope sequentially).
- Record **AC Vpp** at each idle channel's AFE output.
- **SA/FFT:** 100 kHz peak on idle channels should stay **< ⅓** of weakest active-channel `signal_Vpp` from S3 (equivalent to 3× detect margin).

## Tone sources

- **6×** independent **100 kHz sine** sources (generators or oscillators).
- Amplitude per channel: same setting from [tone-attenuation S2](../tone-attenuation/test-procedure.md).

## Central detector — MCU path (optional)

- 6-ch mux + I/Q firmware per [detector-prototype.md](../tone-attenuation/detector-prototype.md).
- Log `signal_present[ch]` and `amplitude_proxy`.

## What is not required

- Anechoic chamber, certified EMC lab, FCC filing (KD11 DIY scope).

## Safety

- Same 12 V limits as tone-attenuation setup.
- No live hydronic wiring.