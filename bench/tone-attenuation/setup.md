# Tone Attenuation Bench — Setup

## Topology

```text
[12 V PSU] ── R ─────────────────────────────────────── R ── [Central passive AFE]
              │         60 ft 20/2 (or 10 ft reference)      │      │
              W ───────────────────────────────────────────── W      └── Scope CH (AC)
              │
         [Tone injector @ thermostat end]
              100 kHz sine, AC-coupled onto R/W
```

- **R** = +12 V, **W** = return
- Tone injector at **far (thermostat) end**
- **Central passive AFE** at boiler-room end — scope probes the AFE output (production ADC node)
- MCU + mux are **optional** (replace scope with ADC when using MCU path)

## Wiring steps

### 1. DC bus

1. Bench PSU **+** → cable **R**, PSU **−** → cable **W** at central end.
2. Set **12.0 V**, current limit **200 mA** initially.
3. DMM at central and far ends; record drop (expect ≤188 mV @ 150 mA).

### 2. Tone injection (far end)

```text
Gen OUT ──[100 nF]──┬── R wire (far end)
                    │
                   [100 Ω]── W
```

1. **100 kHz sine**; start ~1 Vpp at source, trim in S2.
2. Confirm **200–500 mVpp** on bus at far end (scope, AC coupling).
3. Verify sine with FFT/SA — not square (KD15).

**Instruments:** External FG or Wein board required on MSO1104Z (non-`-S`). SA-DS1000Z helps analyze spectrum; it does not generate tone.

### 3. Central passive AFE (scope probe point)

```text
R ──[1.0 mH]──┬──[100 nF]──[divider]──[clamp]──┬── Scope CH (high-Z, AC)
              │                                 └── (optional) MUX → MCU ADC
             W
```

1. Feed inductor on **R**; return at **W**.
2. Scope on divider output; **AC coupling** for S1/S3.
3. Adjust divider so tone **on** does not clip clamp; tone **off** shows low idle Vpp.

### 4. Cable routing

- Unspool **60 ft**; avoid tight coils.
- Document routing for PR 2b report.

### 5. Safety

- Current-limited supply; ≤**150 mA** sustained per zone.
- No live boiler wiring in Phase 0.

## Quick sanity check

| Check | Expected |
|-------|----------|
| DC on bus | 12 V ±5% at central |
| Tone frequency | 100 kHz ±2% |
| Waveform | Sine (FFT fundamental dominates) |
| Tone off | Low, stable AFE Vpp (noise floor) |
| Tone on (10 ft) | AFE Vpp clearly above noise floor |