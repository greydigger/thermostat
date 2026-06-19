# Tone Attenuation Bench — Setup

## Topology

```text
[12 V PSU] ── R ─────────────────────────────────────── R ── [Central AFE + MCU]
              │         60 ft 20/2 (or 10 ft reference)      │
              W ───────────────────────────────────────────── W
              │
         [Tone injector @ thermostat end]
              100 kHz sine, AC-coupled onto R/W
```

- **R** = +12 V
- **W** = return (common)
- Tone injector sits at the **thermostat (far) end** to mimic production.
- Central **detector prototype** sits at the **boiler-room (near) end**.

## Wiring Steps

### 1. DC Bus

1. Connect bench PSU **+** to cable **R**, PSU **−** to cable **W** at the central end.
2. Set **12.0 V**, current limit **200 mA** initially.
3. Measure `V_bus` at central and far ends. Record drop (expect ≤188 mV @ 150 mA).

### 2. Tone Injection (Far End)

**Function-generator path:**

```text
Gen OUT ──[100 nF]──┬── R wire (thermostat end)
                    │
                   [100 Ω] to W (return)
```

1. Configure generator: **100 kHz sine**, 1 Vpp starting amplitude (adjust later).
2. Enable output; confirm **200–500 mVpp** tone at far end on scope (AC coupling).
3. For OOK / F1 tests: gate generator with MOSFET on `TONE_EN` proxy, default-off.

### 3. Central Detector AFE

Per channel (single channel sufficient for PR 2):

```text
R wire ──[1.0 mH]──┬──[100 nF]──[divider]──[clamp]── MUX ── ADC
                   │
                  W
```

1. Feed inductor on **R** leg; return at **W**.
2. AC-couple and attenuate to **0–3.3 V** peak at ADC pin.
3. Verify no ADC clipping at max tone amplitude.

### 4. Cable Routing

For the **60 ft** run:

- Unspool full length; avoid tight coils (adds stray L/C).
- Keep parallel to typical install: along floor/wall if possible.
- Record routing notes in PR 2b report (affects parasitic C).

### 5. Safety

- Fuse or current-limited supply; do not exceed **150 mA** per-zone design limit during sustained tests.
- Disconnect before changing divider/clamp components.
- Central relay proxy is **not** connected to live boiler wiring in Phase 0.

## Quick Sanity Check

| Check | Expected |
|-------|----------|
| DC on bus | 12 V ±5% at central |
| Tone frequency | 100 kHz ±2% |
| Waveform | Sine (not square) |
| Tone off | ADC noise floor stable, relay open |