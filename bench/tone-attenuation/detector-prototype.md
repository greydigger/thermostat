# Central I/Q Detector — Breadboard Prototype

Software-first prototype for PR 2b **F5** and **F1** before PR 3 schematic and PR 13 production firmware.

## Target MCU

| Parameter | Value |
|-----------|-------|
| MCU class | STM32G071 or similar |
| ADC | 12-bit, ≥1 Msps |
| Channels | 1 for PR 2 (expand to 6 for EMI bench) |

Firmware path (production): `central-board/firmware/` (PR 13).

## Sampling Configuration

| Parameter | Value |
|-----------|-------|
| Sample rate | **80 kHz** per active channel |
| Burst length | **8 ms** (640 samples) |
| Scan period | **60 ms** (single-ch) / **60 ms** round-robin (6-ch) |

## I/Q Reference Tables

Precompute 20 kHz quadrature at 80 kHz sample rate (repeat every 4 samples):

```text
n:     0   1   2   3   0   1   2   3  ...
cos:  +1   0  -1   0  +1   0  -1   0  ...
sin:   0  +1   0  -1   0  +1   0  -1  ...
```

Per burst:

```c
int32_t I = 0, Q = 0;
for (int i = 0; i < N; i++) {
    int16_t s = adc_buf[i];
    I += s * cos_ref[i & 3];
    Q += s * sin_ref[i & 3];
}
uint32_t amplitude_proxy = (uint32_t)(I * I + Q * Q);
```

## Detection Logic

```text
signal_present = (amplitude_proxy > threshold[channel])
```

| Parameter | Starting value | Calibrate in |
|-----------|----------------|--------------|
| `threshold` | 3 × measured noise floor | T1/T3 |
| Output debounce | 1000 ms assert/release | T5 |

**Watchdog:** If no successful scan in **5 s**, force `signal_present = false` (fail-safe).

## Relay Proxy

- GPIO drives relay module or LED.
- **Assert:** `signal_present` TRUE for full debounce period.
- **Release:** `signal_present` FALSE for full debounce period.

## UART Debug (recommended)

Print per burst (115200 baud):

```text
ch=0 I=1234 Q=567 amp=1876543 nf=50000 present=1
```

Enables threshold tuning without re-flashing during T3.

## Bring-Up Order

1. ADC DMA sanity — DC bus only, verify samples in range.
2. Tone on 10 ft — verify `amplitude_proxy` rises.
3. Run [test-procedure.md](test-procedure.md) T1–T5.
4. Port thresholds to `bench/reports/phase0-validation.md`.