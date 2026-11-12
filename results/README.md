# Recorded Simulation Results

This folder contains the results reported for a batch transient run of `ltspice/buck_converter_closed_loop.cir` using LTspice 17.2.4 for macOS. Run parameters and model choices are documented in `docs/component_models.md` and `docs/results_template.md`. These are simulation results, not hardware measurements.

The full LTspice measurement log is `buck_converter_closed_loop_10V_12V_14V.log.txt`. SVG plots render waveforms from LTspice raw output for the three stepped input cases.

## Evidence files

- `buck_converter_closed_loop_10V_12V_14V.log.txt` — `.meas` results for input cases 10 V, 12 V, and 14 V.
- `vin10v_0.5A_to_1A_overview.svg` — full transient at 10 V input.
- `vin10v_load_step_closeup.svg` — load-transition detail at 10 V input.
- `vin12v_0.5A_to_1A_overview.svg` — full transient at 12 V input.
- `vin12v_load_step_closeup.svg` — load-transition detail at 12 V input.
- `vin14v_0.5A_to_1A_overview.svg` — full transient at 14 V input.
- `vin14v_load_step_closeup.svg` — load-transition detail at 14 V input.

## Results summary

| Input | VPRE (V) | VPOST (V) | Vout ripple (mVpp) | IL ripple (App) | Efficiency estimate | Interpretation |
|---:|---:|---:|---:|---:|---:|---|
| 10 V | 4.7137 | 4.9269 | 72.71 | 0.2597 | 92.21% | Post-window average is within ±2%; ripple target fails. Pre-transition output is not settled in band. |
| 12 V | 5.0228 | 4.9887 | 45.88 | 0.3049 | 92.58% | Average and ripple goals met in the stated windows. |
| 14 V | 5.2457 | 5.0144 | 53.84 | 0.3401 | 92.68% | Post-window average is within ±2%; ripple target fails. Pre-transition output is not settled in band. |

The efficiency figures are model estimates from the behavioral circuit, not hardware efficiency measurements.

## Requirements status

| Requirement | Result | Status |
|---|---|---|
| Post-window average output within ±2% of 5 V | 4.9269–5.0144 V across cases | Met for the defined post-window average only; this does not establish full-run regulation. |
| Output ripple <50 mVpp | 45.88 mVpp at 12 V; 72.71 mVpp at 10 V; 53.84 mVpp at 14 V | Met only at 12 V. |
| Load step from settled pre-step operating point | 10 V and 14 V pre-step averages are outside ±2% band | Not demonstrated for those cases; repeat with longer pre-step settling. |
| Real MOSFET gate-drive and thermal behavior | Idealized switch used | Not modeled / not verified. |
| Hardware performance | No hardware test evidence | Not tested. |

See `docs/project_report.md` for the detailed interpretation and limitations.
