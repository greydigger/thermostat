# R/W Impedance Model @ 100 kHz

Bench attenuation and crosstalk results are **only valid** when source and termination impedances match the production thermostat injector and central-board per-zone front-end. A function generator into an open 20/2 pair does not reproduce field attenuation or coupling.

**Status:** Provisional numeric targets — finalize from `thermostat/hw/sheets/tone-driver.kicad_sch` (PR 7) and `central-board/hw/sheets/zone-channel.kicad_sch` (PR 3). Update this file when those values are known.

---

## Why impedance matters

At **100 kHz**, the 2-wire pair is not “DC power + a voltage probe”:

| Effect | Consequence if ignored |
|--------|------------------------|
| Source `Z_out` sets tone current into cable | FG 50 Ω vs FET driver → wrong mVpp and wrong radiated/coupled fields |
| Central `Z_load` (1 mH feed + divider) loads the line | Open-end or scope-only tap → inflated mVpp, wrong S3 margin |
| Far-end thermostat input caps shunt HF | No far-end emulator → optimistic attenuation |
| Bench PSU bulk caps shunt HF at central | Tone shorted to supply → pessimistic or unstable S3 |
| Six central AFE networks in parallel (EMI) | Single-channel tap → wrong crosstalk (F6) |

---

## Reference equivalent circuit (single zone)

```text
CENTRAL (boiler room)                           FAR (thermostat)
───────────────                                 ────────────────

[PSU 12 V]──[L_dc_block]── R ──────── 20/2 pair ─────── R ──[Thermostat HF load]
                │              │                           │
                W ─────────────┴────────────────────────── W
                │
         [Zone AFE × N]
         L_feed 1.0 mH
              └── C_ac 100 nF ── R_div ── C_mux ── GND (W)
```

- **`L_dc_block`:** Bench-only — isolates PSU from HF (see [setup.md](setup.md)).
- **`L_feed`:** Production 1.0 mH per zone (470 µH–2.2 mH tune range).
- **Thermostat HF load:** Buck input network + tone injector `Z_out` (PR 7).

---

## Impedance budget @ 100 kHz (provisional)

| Element | Nominal | @ 100 kHz | Notes |
|---------|---------|-----------|-------|
| `L_feed` | 1.0 mH | **Z ≈ 628 Ω** | `2πfL`; dominates central AC load |
| `C_ac` | 100 nF | **\|Z\| ≈ 16 Ω** | In series with divider branch |
| `R_div` | 10 kΩ \|\| 3.3 kΩ example | **≈ 2.5 kΩ** | Plus scope 10 MΩ \|\| ~15 pF (negligible) |
| 20/2 loop (60 ft) | ~1.25 Ω DC | **L_wire, C_par** distributed | Unshielded pair; measure or model as line |
| Tone driver `Z_out` | TBD (PR 7) | Target **100 Ω–2 kΩ** range | Must bench-match, not 50 Ω FG alone |
| Thermostat buck input | TBD (PR 5) | Often **< 100 Ω** if large `C_in` | Far-end shunt — include emulator |
| Bench PSU (raw) | — | **< 1 Ω** with bulk caps | **Must not** connect directly to R without `L_dc_block` |

**Central AC load (single zone, first-order):**

```text
Z_cent ≈ jωL_feed + 1 / (1/Z_C + 1/R_div)
       ≈ 628 Ω + (16 Ω in series with 2.5 kΩ)  →  dominated by L_feed for bus current
```

Use this to sanity-check: tone current is mA-scale, not what a 50 Ω generator would push into a matched load.

---

## Bench source emulator (thermostat end)

**Requirement:** Present the same `Z_out` and topology as the production tone driver when measured at the R/W terminals.

### Minimum fidelity (until PR 7 schematic)

1. **Do not** connect FG output directly to R/W.
2. Build **driver emulator** on breadboard at far end:

```text
[100 kHz sine]──[R_s]──[C_inj]──┬── R (far)
                                │
                    [Thermostat load emulator] to W
                                │
                               W
```

| Component | Starting value | Role |
|-----------|----------------|------|
| `R_s` | Match PR 7 series limit (start **470 Ω–2.2 kΩ**, tune) | Sets `Z_out` with `C_inj` |
| `C_inj` | **100 nF** (match production coupling) | Blocks DC; pairs with `R_s` for AC Thevenin |
| Load emulator | `C_bulk` 10–47 µF + `R_bleed` 1 kΩ to W | Mimics buck input at HF |

3. Trim **under loaded conditions** to **200–500 mVpp on R/W at far end** (S2) — not unloaded open-circuit at FG terminals.

### After PR 7

Replace starting values with measured `Z_out` from schematic or VNA/heuristic:

```text
Z_out = V_open / I_short   @ 100 kHz, driver enabled, 12 V bus present
```

Record in `phase0-validation.md`.

---

## Bench termination (central end)

### Per active zone under test

**Mandatory** production-representative AFE on **every** zone port used in the test:

```text
R ──[L_feed 1.0 mH]──┬──[C_ac 100 nF]──[R_div]──[clamp]── scope / ADC
                     │
                    W
```

- Use the same `R_div` ratio planned for production (not a bare scope probe on R/W).
- Probe **after** divider/clamp (ADC node), not on raw bus.

### PSU HF isolation (mandatory)

Insert **`L_dc_block`** between bench PSU and zone R bus:

| Option | Part | Purpose |
|--------|------|---------|
| Preferred | **1.0 mH** (same family as `L_feed`) in series on R | Blocks HF from shunting into PSU |
| Alternate | RF choke **100 µH–1 mH** + **100 nF** to W at central feed | HF decoupling |

Verify: with tone on and S2 trim, central PSU terminals show **<<** tone current (AC current mainly flows on R/W pair).

### EMI bench (six zones)

For F6 crosstalk, **all six** central AFE networks must be present on their respective cables — not one scope tap and five open wires. Idle channels need the full `L_feed + C_ac + R_div` load so coupling matches installed wiring.

---

## Verification before S3 / C1

| Check | Method | Pass |
|-------|--------|------|
| PSU isolation | AC current probe on PSU leads during tone | Minimal HF current |
| Loaded source | mVpp at far end **with** central AFE + load emulator connected | 200–500 mVpp |
| `Z_out` documented | FG settings + `R_s`/`C_inj` values recorded | Matches PR 7 when available |
| Central AFE on all ports | Visual / DCR check each zone | All `L_feed` in series on R |
| Scope loading | 10:1 probe at divider output only | Not on raw bus |

---

## Relation to pass criteria

| Test | Impedance-sensitive quantity |
|------|------------------------------|
| **S3 / F5** | `signal_Vpp / noise_Vpp` at **divider output** with full source + load |
| **C1–C3 / F6** | Crosstalk Vpp on idle channels with **6×** central AFE + 6× far-end emulators |

Changing `R_s`, `L_feed`, or omitting far-end load can shift margin M by orders of magnitude — note all emulator values in PR 2b report.

---

## References

- [setup.md](setup.md) — wiring with emulator blocks
- [bom.md](bom.md) — emulator parts list
- [test-procedure.md](test-procedure.md) — S2/S3 with loaded trim
- Design: [DESIGN.md](../../DESIGN.md) tone driver + central AFE sections