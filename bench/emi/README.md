# EMI Bench — Multi-Zone Crosstalk

Characterize coupling between **6 parallel 20/2 runs** — PR 2b criterion **F6**.

Default validation: **scope + passive AFE per channel** (idle-channel Vpp or 100 kHz spectral peak). MCU `signal_present` logging is optional.

| Artifact | Purpose |
|----------|---------|
| [setup.md](setup.md) | Multi-cable bench layout |
| [test-procedure.md](test-procedure.md) | Crosstalk tests (scope default) |

Sine-only injection (KD15). Square-wave sources are **not** valid.

**Outcome:** `bench/reports/phase0-validation.md` and `go-no-go.md` (PR 2b).