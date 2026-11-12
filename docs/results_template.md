# Results Record

This file records the values from the LTspice 17.2.4 for macOS run whose log is `results/buck_converter_closed_loop_10V_12V_14V.log.txt`. These values are simulation outputs from the behavioral model, not hardware measurements. If the netlist or parameters change, rerun and preserve a separate log before replacing these values.

## Conditions and windows

- Switching frequency: 100 kHz; transient stop time: 5 ms; maximum timestep: 100 ns.
- Inductor: 100 µH with 180 mΩ modeled DCR.
- Output capacitor: 220 µF with 130 mΩ modeled ESR.
- Controller: `KP=0.015`, `KI=65`, `KD=1.2`; switch model `Ron=30 mΩ`; diode model MBRS340.
- Input cases: 10 V, 12 V, and 14 V, stepped as separate runs.
- Load transition: 10 Ω to 5 Ω at 2 ms.
- `VPRE`: 1.5–1.9 ms; `VPOST`, ripple, and power measurements: 4.0–4.9 ms.
- Load excursion and last boundary crossing: 2.0–4.99 ms; boundary band is 4.9–5.1 V.

## Output and transient measurements

| Input (V) | VPRE avg (V) | VPOST avg (V) | VPOST error (%) | Vout ripple (mVpp) | IL ripple (App) | Vmin after 2 ms (V) | Vmax after 2 ms (V) | Last boundary-crossing time after event (ms) |
|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| 10 | 4.7137 | 4.9269 | -1.46 | 72.71 | 0.2597 | 4.5814 | 4.9644 | 2.200 |
| 12 | 5.0228 | 4.9887 | -0.23 | 45.88 | 0.3049 | 4.8149 | 5.0116 | 0.460 |
| 14 | 5.2457 | 5.0144 | +0.29 | 53.84 | 0.3401 | 4.9661 | 5.1436 | 0.034 |

**Caveat:** At 10 V and 14 V, `VPRE` is outside the 4.9–5.1 V band. The 2 ms event therefore does not start from a demonstrably settled regulated operating point in these cases. The last-boundary-crossing value is a netlist measurement, but it is not a clean conventional load-step settling-time result for those cases. The 10 V output also has a minimum of 4.5814 V in the measured post-event interval.

## Modeled power and efficiency

| Input (V) | PIN_POST (W) | POUT_POST (W) | EFF_POST (%) |
|---:|---:|---:|---:|
| 10 | 5.2651 | 4.8550 | 92.21 |
| 12 | 5.3766 | 4.9774 | 92.58 |
| 14 | 5.4259 | 5.0288 | 92.68 |

These values use the netlist's average power measurements over 4.0–4.9 ms. They characterize this model only; they are not hardware efficiency measurements and omit losses not represented by the idealized switch.

## Run metadata

- LTspice: 17.2.4 for macOS.
- Netlist: `ltspice/buck_converter_closed_loop.cir` with `KP=0.015`, `KI=65`, `KD=1.2`, `DCR=180m`, `CESR=130m`, MBRS340 diode model, and 30 mΩ idealized switch resistance.
- Controller tuning history: the log notes an earlier `KP=0.08`, `KI=500` setting oscillated; those are not the final run settings.
- Evidence: log and SVG plots listed in `results/README.md`.
