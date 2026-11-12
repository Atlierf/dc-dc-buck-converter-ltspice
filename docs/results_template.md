# Results Template

Fill this table with values from an actual LTspice run. Do not enter analytical estimates as simulation measurements. Record the LTspice version and save waveforms and the SPICE error log alongside the report if required.

| Input (V) | Pre-step Vout avg (V) | Post-step Vout avg (V) | Post-step Vout ripple (mVpp) | Post-step IL ripple (App) | Load-step minimum (V) | Settling time (ms) | Meets target? |
|---:|---:|---:|---:|---:|---:|---:|---|
| 10 |  |  |  |  |  |  |  |
| 12 |  |  |  |  |  |  |  |
| 14 |  |  |  |  |  |  |  |

## Other records

- LTspice version: [enter version]
- Netlist filename / revision: [enter]
- Regulation band used for settling time: [enter]
- Controller gains tested: [enter KP and KI]
- Model limitations or convergence notes: [enter]
- Waveform and error-log filenames: [enter]

## Interpretation checklist

- Is post-step average output close to 5 V at every input point?
- Is output ripple below the 50 mVpp project target?
- Does the output recover from the load increase without sustained oscillation?
- Is inductor peak current within the selected inductor's rating?
- Are results consistent with the controller and component model assumptions?
