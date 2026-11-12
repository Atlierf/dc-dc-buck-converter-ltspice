# 12 V to 5 V Buck Converter(LTspice)

This repository contains a simulation of a 12 V-to-5 V buck converter. It includes a fixed-duty power-stage example, a feedback-controlled switching model, first-pass design calculations, component-model notes, LTspice output files, and a report based on the committed simulation log.

## Design point

- Input test cases: 10 V, 12 V, and 14 V (each is a separate stepped simulation case)
- Output target: 5 V, maximum nominal load 1 A
- Switching frequency: 100 kHz
- Inductor: 100 µH; modeled DCR 180 mΩ
- Output capacitor: 220 µF; modeled ESR 130 mΩ
- Load transition in the recorded run: 0.5 A-equivalent to 1 A-equivalent at 2 ms

The 180 mΩ DCR and 130 mΩ ESR are maximum/assumed datasheet-based values documented in `docs/component_models.md`. The high-side switch remains an idealized voltage-controlled switch, not a selected MOSFET model.

## Files

- `ltspice/buck_converter.cir` — fixed-duty baseline; it does not regulate output.
- `ltspice/buck_converter_closed_loop.cir` — behavioral PID/PWM switching testbench with input steps and load transition.
- `docs/design_notes.md` — operating principle and calculations.
- `docs/component_models.md` — component identities, model provenance, and limitations.
- `docs/simulation_procedure.md` — how to reproduce and interpret the test.
- `docs/project_report.md` — report with measured values and caveats.
- `docs/results_template.md` — detailed recorded measurement table and run metadata.
- `docs/final_quality_checklist.md` — completion and remaining-work checklist.
- `results/` — committed LTspice log and rendered waveform plots.

## Recorded simulation summary

The committed LTspice 17.2.4 run reports post-step average output values of 4.9269 V, 4.9887 V, and 5.0144 V for 10 V, 12 V, and 14 V input, respectively. These averages are within ±2% of 5 V over the specified measurement window. The output-ripple target of <50 mVpp is met at 12 V (45.88 mVpp), but not at 10 V (72.71 mVpp) or 14 V (53.84 mVpp).

## Run the simulation

Open `ltspice/buck_converter_closed_loop.cir` in LTspice and run the transient analysis. Plot `V(out)`, `I(L1)`, `V(sw)`, and `V(duty)`. See `docs/simulation_procedure.md` for measurement windows and important caveats. The checked-in result log and plots correspond to the parameter set and netlist revision described in `results/README.md`.
