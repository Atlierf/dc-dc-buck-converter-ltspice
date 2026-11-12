# 12 V to 5 V Buck Converter LTspice Project

This project develops and characterises a 12 V-to-5 V buck-converter simulation. It includes a fixed-duty power-stage example and a separate behavioral closed-loop testbench for regulation and load-transient investigation.

## Baseline specification

- 12 V nominal input; 10 V, 12 V, and 14 V line test points
- 5 V output target, up to 1 A load
- 100 kHz switching frequency
- 100 µH inductor, selected from an approximately 0.3 A peak-to-peak ripple target
- 220 µF output capacitor
- DCR and ESR included as assumed parasitic values

## Files

- `ltspice/buck_converter.cir` — fixed-duty baseline power stage; not a regulated converter.
- `ltspice/buck_converter_closed_loop.cir` — behavioral PI/PWM closed-loop testbench, stepped input, and 0.5 A-to-1 A load step.
- `docs/design_notes.md` — operating principle and component calculations.
- `docs/project_report.md` — report draft with method, equations, limitations, and result fields to complete after running LTspice.
- `docs/simulation_procedure.md` — instructions for running the closed-loop tests and interpreting results.
- `docs/results_template.md` — blank table for actual simulation measurements.

## Quick start

Open `ltspice/buck_converter_closed_loop.cir` in LTspice and run the transient simulation. Plot `V(out)`, `I(L1)`, `V(sw)`, and `V(duty)`. The simulation runs 10 V, 12 V, and 14 V input cases and steps the load from 0.5 A to 1 A at 2 ms. See `docs/simulation_procedure.md` for details.

## Important limitations

The closed-loop controller is a behavioral educational model with initial, unverified PI gains; it is not a commercial controller or a stability-validated design. Semiconductor and passive models are simplified, and ESR/DCR values are assumptions. Run the simulation, inspect its waveforms and error log, tune the controller if needed, and replace assumed models with actual datasheet models before drawing design conclusions. No simulation results are claimed until the blank results template is filled from an LTspice run.
