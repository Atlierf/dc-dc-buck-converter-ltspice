# LTspice Simulation Procedure

## Which netlist to run

- `ltspice/buck_converter.cir` is the fixed-duty power-stage baseline. It cannot regulate against input or load changes.
- `ltspice/buck_converter_closed_loop.cir` is the behavioral PID/PWM model used for the committed input-step and load-transition results.

## Reproduce the committed run

1. Open `ltspice/buck_converter_closed_loop.cir` in LTspice 17.2.4 or a compatible version.
2. Run the transient analysis. `.step param VIN_DC list 10 12 14` produces separate runs at 10 V, 12 V, and 14 V.
3. Plot `V(out)`, `I(L1)`, `V(sw)`, `V(duty)`, and `V(err)`.
4. Observe the behavioral load change from 10 Ω to 5 Ω at 2 ms.
5. Open **View → SPICE Error Log**. The committed measurement output is in `results/buck_converter_closed_loop_10V_12V_14V.log.txt`.
6. Compare your run with the committed log, allowing for version/solver differences. If the netlist or model parameters change, create a new result log and update the report; do not reuse old values as new results.

## Measurement definitions

The current netlist measures:

- `VPRE`: average `V(out)` from 1.5 to 1.9 ms (before the load transition).
- `VPOST`: average `V(out)` from 4.0 to 4.9 ms.
- `VPP_POST`: peak-to-peak output voltage from 4.0 to 4.9 ms.
- `ILPP_POST`: peak-to-peak inductor current from 4.0 to 4.9 ms.
- `VMIN_STEP` / `VMAX_STEP`: minimum and maximum output voltage from 2.0 to 4.99 ms.
- `TSETTLE_MS`: time from 2 ms to the last ±0.1 V boundary crossing around 5 V, reported in milliseconds.
- `PIN_POST`, `POUT_POST`, `EFF_POST`: modeled average input power, output power, and efficiency from 4.0 to 4.9 ms.

## Critical interpretation caveat

In the committed run, `VPRE` is 4.7137 V at 10 V input and 5.2457 V at 14 V input. Both are outside the 4.9–5.1 V band before the load transition. Thus, for these cases, the 2 ms event does not begin from an established regulated operating point. The reported `TSETTLE_MS` values cannot be treated as clean load-step settling times for those conditions. The 12 V case's pre-step average is within the band, but the load step's waveform should still be inspected.

For a cleaner load-step experiment, move the load change later (for example, after a longer pre-step settling period), extend the transient stop time, and move all pre/post measurement windows accordingly. Then rerun LTspice, save the new log and plots, and update the report and results table. Do not mix measurements from different netlist versions.

## Reading the results

- Compare average output to 5 V and state the tolerance used.
- Compare output ripple with the project target of 50 mVpp.
- Inspect peak/undershoot and whether output remains within the selected regulation band after the step.
- Treat efficiency as a model estimate only; the switch omits real MOSFET switching and gate-drive losses.
- Do not infer thermal safety from this electrical simulation.
