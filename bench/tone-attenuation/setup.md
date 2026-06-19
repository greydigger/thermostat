# Tone Attenuation Bench — Setup

## Topology

```text
[12 V PSU]──[L_dc_block]── R ═══════ 20/2 ═══════ R ── [Far-end driver emulator + load]
                │                                      │
                W ═════════════════════════════════════ W
                │
         [Central AFE per zone] ── scope @ divider output
```

- **R** = +12 V, **W** = return
- **Impedance fidelity required** — see [impedance-model.md](impedance-model.md)
- Do **not** use bare function-generator output on open R/W

---

## Wiring steps

### 1. DC bus + PSU HF isolation

1. Insert **`L_dc_block`** (1.0 mH recommended) in series: PSU **+** → `L_dc_block` → cable **R**.
2. PSU **−** → cable **W** at central end.
3. Set **12.0 V**, current limit **200 mA**.
4. DMM at central and far ends (DC).

Without `L_dc_block`, PSU bulk capacitance dominates HF termination and invalidates attenuation data.

### 2. Far-end driver emulator + load (thermostat)

Replace “FG → 100 nF → R/W” with a loaded emulator:

```text
[100 kHz sine]──[R_s]──[C_inj 100 nF]──┬── R (far)
                                       │
              [C_bulk 10–47 µF]────────┤  (thermostat buck emulator)
              [R_bleed 1 kΩ optional]──┴── W
```

| Part | Starting value |
|------|----------------|
| `R_s` | 470 Ω–2.2 kΩ (tune under load; finalize vs PR 7) |
| `C_inj` | 100 nF |
| `C_bulk` | 10–47 µF ceramic + electrolytic to W |

1. Enable **100 kHz sine** (external FG or Wein — MSO1104Z non-`-S` has no AWG).
2. Trim **with central AFE connected** until **200–500 mVpp** on R/W at far end (AC, S2).
3. FFT/SA: sine fundamental dominates (KD15).

### 3. Central passive AFE (mandatory termination)

**Per zone port** under test (all six for EMI):

```text
R ──[L_feed 1.0 mH]──┬──[C_ac 100 nF]──[R_div]──[clamp]──┬── Scope (AC, high-Z)
                     │                                    └── (optional) MUX → ADC
                    W
```

1. `L_feed` in series on **R** before any tap to W.
2. Scope **only** at divider output (ADC node), not on raw bus.
3. Use production-target `R_div` (e.g. 10 kΩ + 3.3 kΩ starting point).

### 4. Cable routing

- Unspool **60 ft**; avoid tight coils.
- Document routing for PR 2b report.

### 5. Impedance sanity ([impedance-model.md](impedance-model.md) checklist)

Complete verification table before S3.

### 6. Safety

- Current-limited supply; ≤**150 mA** sustained per zone.
- No live boiler wiring in Phase 0.

---

## Quick sanity check

| Check | Expected |
|-------|----------|
| DC on bus | 12 V ±5% at central |
| PSU HF isolation | Little AC on PSU leads during tone |
| Tone @ far (loaded) | 200–500 mVpp on R/W with central AFE on |
| Tone frequency | 100 kHz ±2% |
| Waveform | Sine (FFT) |
| Central ADC node, tone off | Low stable Vpp (noise floor) |
| Central ADC node, tone on (10 ft) | Vpp clearly above noise floor |