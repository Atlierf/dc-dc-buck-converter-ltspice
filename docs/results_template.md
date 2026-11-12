# Results Template

Values below are from an actual LTspice 17.2.4 for MacOS batch run of `ltspice/buck_converter_closed_loop.cir` (see `docs/evidence/buck_converter_closed_loop.log.txt` and `docs/evidence/closed_loop_waveforms.svg`).

| Input (V) | Pre-step Vout avg (V) | Post-step Vout avg (V) | Post-step Vout ripple (mVpp) | Post-step IL ripple (App) | Load-step minimum (V) | Settling time (ms) | Meets target? |
|---:|---:|---:|---:|---:|---:|---:|---|
| 10 | 4.7137 | 4.9269 | 72.71 | 0.2597 | 4.5814 | 2.200 | Ripple > 50 mVpp (still settling) |
| 12 | 5.0228 | 4.9887 | 45.88 | 0.3049 | 4.8149 | 0.460 | Yes |
| 14 | 5.2457 | 5.0144 | 53.84 | 0.3401 | 4.9661 | 0.034 | Ripple slightly > 50 mVpp (130 mΩ max ESR) |

## Other records

- LTspice version: LTspice 17.2.4 for MacOS
- Netlist filename / revision: `ltspice/buck_converter_closed_loop.cir` (tuned PID + `MBRS340`, `DCR=180m`, `CESR=130m`)
- Regulation band used for settling time: 4.9 V to 5.1 V (±2% around 5 V, measured from t = 2 ms)
- Controller gains tested: Initial `KP=0.08, KI=500` (oscillated 1.46–2.62 Vpp); tuned `KP=0.015, KI=65, KD=1.2` with `Cdiff=1n, Rdiff=8k`
- Model limitations or convergence notes: Pseudo-transient `.op` succeeded; `D1` uses LTspice `MBRS340`, `L1`/`C1` use Bourns `SRR1260-101M` / Panasonic `EEU-FR1C221` datasheet max `DCR`/`ESR`, `S1` remains an assumed idealized switch (see `docs/component_models.md`)
- Waveform and error-log filenames: `docs/evidence/closed_loop_waveforms.svg`, `docs/evidence/buck_converter_closed_loop.log.txt`

## Interpretation checklist

- Is post-step average output close to 5 V at every input point?
- Is output ripple below the 50 mVpp project target?
- Does the output recover from the load increase without sustained oscillation?
- Is inductor peak current within the selected inductor's rating?
- Are results consistent with the controller and component model assumptions?
