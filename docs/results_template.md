# Results Template

Values below are from an actual LTspice 17.2.4 for MacOS batch run of `ltspice/buck_converter_closed_loop.cir` (see `docs/evidence/buck_converter_closed_loop.log.txt` and `docs/evidence/closed_loop_waveforms.svg`).

| Input (V) | Pre-step Vout avg (V) | Post-step Vout avg (V) | Post-step Vout ripple (mVpp) | Post-step IL ripple (App) | Load-step minimum (V) | Settling time (ms) | Meets target? |
|---:|---:|---:|---:|---:|---:|---:|---|
| 10 | 4.6720 | 4.9254 | 45.95 | 0.3070 | 4.5548 | 1.731 | Yes (ripple < 50 mVpp; slow startup pre-step) |
| 12 | 4.9643 | 5.0027 | 18.66 | 0.3285 | 4.7897 | 0.410 | Yes |
| 14 | 5.1697 | 5.0193 | 33.60 | 0.3694 | 4.9391 | 0.008 | Yes |

## Other records

- LTspice version: LTspice 17.2.4 for MacOS
- Netlist filename / revision: `ltspice/buck_converter_closed_loop.cir` (tuned PID)
- Regulation band used for settling time: 4.9 V to 5.1 V (±2% around 5 V, measured from t = 2 ms)
- Controller gains tested: Initial `KP=0.08, KI=500` (oscillated 1.46–2.62 Vpp); tuned `KP=0.015, KI=65, KD=1.2` with `Cdiff=1n, Rdiff=8k`
- Model limitations or convergence notes: Pseudo-transient `.op` succeeded; simplified switch/diode/ESR/DCR models
- Waveform and error-log filenames: `docs/evidence/closed_loop_waveforms.svg`, `docs/evidence/buck_converter_closed_loop.log.txt`

## Interpretation checklist

- Is post-step average output close to 5 V at every input point?
- Is output ripple below the 50 mVpp project target?
- Does the output recover from the load increase without sustained oscillation?
- Is inductor peak current within the selected inductor's rating?
- Are results consistent with the controller and component model assumptions?
