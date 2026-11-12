# LTspice Simulation Procedure

The repository contains two netlists:

- `buck_converter.cir`: fixed-duty baseline power-stage model.
- `buck_converter_closed_loop.cir`: educational behavioral-PI closed-loop version with an input sweep and load step. Use this file for regulation and transient studies.

## Run the closed-loop study

1. Open `ltspice/buck_converter_closed_loop.cir` in LTspice.
2. Run the simulation. The `.step` directive runs the test at 10 V, 12 V, and 14 V input.
3. Plot `V(out)` (output), `I(L1)` (inductor current), `V(sw)` (switch node), `V(duty)` (controller duty command), and `V(err)` (feedback error).
4. The load changes from 0.5 A nominal to 1 A at 2 ms. Inspect the output response around that time.
5. Open **View → SPICE Error Log** to see `.meas` outputs: pre-step average output, post-step average output, post-step output peak-to-peak ripple, and post-step inductor-current ripple. Each measurement is repeated for each stepped input value.
6. Enter actual results in `results_template.md`. Save waveform screenshots and the LTspice error log with the project if required by your course.

## Interpret the model carefully

- The switching period is 10 µs (100 kHz); the carrier ramps from 0 to 1 each period.
- A behavioral PI controller changes duty based on the difference between 5 V reference and output voltage.
- The PI gains are initial educational values, not verified compensation. If the output oscillates, saturates, or does not settle, tune the gains and report what changed.
- The switch, diode, DCR, and ESR are simplified models. Replace them with selected-part datasheet models for a component-specific design.
- No numerical results are pre-filled because the netlist must be run in LTspice to obtain them.

## Measure load-step settling time

Use the waveform cursor to find the point when output voltage returns to the chosen regulation band after 2 ms. State the regulation band used (for example, 4.9 V to 5.1 V) and the settling-time definition in the report. The provided average and ripple measurements do not calculate settling time automatically.

## Fixed-duty baseline

The file `buck_converter.cir` is useful for observing power-stage switching at a fixed duty cycle. It cannot demonstrate closed-loop regulation because it does not sense or correct output-voltage error.
