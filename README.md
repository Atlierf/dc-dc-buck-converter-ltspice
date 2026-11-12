# 12 V to 5 V Buck Converter LTspice Project

This project develops and characterizes a 12 V-to-5 V buck-converter simulation. It includes a fixed-duty power-stage baseline and a separate tuned behavioral PID/PWM closed-loop testbench for line-regulation, load-transient, and efficiency investigation.

## Baseline specification

- 12 V nominal input; 10 V, 12 V, and 14 V line test points
- 5 V output target, up to 1 A load (`< 50 mVpp` ripple target)
- 100 kHz switching frequency
- 100 µH inductor (`Bourns SRR1260-101M`, `180 mΩ` datasheet max DCR in closed-loop netlist)
- 220 µF output capacitor (`Panasonic EEU-FR1C221`, `130 mΩ` datasheet max ESR at `100 kHz, 20°C` in closed-loop netlist)
- Freewheel diode: `onsemi MBRS340` (`40 V`, `3 A` Schottky from LTspice `standard.dio` in closed-loop netlist)
- High-side switch (`S1`): assumed idealized `30 mΩ` switch (`SWMOD`)

## Files

- `ltspice/buck_converter.cir` — fixed-duty baseline power stage (`D = 0.417`, assumed `70 mΩ` DCR / `50 mΩ` ESR); not a regulated converter.
- `ltspice/buck_converter_closed_loop.cir` — tuned behavioral PID/PWM closed-loop testbench (`KP=0.015`, `KI=65`, `KD=1.2`), stepped input (`10 V`, `12 V`, `14 V`), `0.5 A`-to-`1 A` load step at `2 ms`, and `.meas` directives.
- `docs/design_notes.md` — operating principle and first-pass component calculations.
- `docs/component_models.md` — part numbers, ratings, model sources, and modeling simplifications.
- `docs/simulation_procedure.md` — instructions for running the closed-loop tests and interpreting `.meas` outputs.
- `docs/results_template.md` — measured LTspice 17.2.4 results table and checklist.
- `docs/project_report.md` — full engineering report with measured results, efficiency analysis, and limitations.
- `results/` — saved LTspice error log (`buck_converter_closed_loop_10V_12V_14V.log.txt`), per-case overview and load-step close-up waveform SVGs, and requirements compliance matrix (`results/README.md`).

## Quick start

Open `ltspice/buck_converter_closed_loop.cir` in LTspice (or run `/Applications/LTspice.app/Contents/MacOS/LTspice -b ltspice/buck_converter_closed_loop.cir`) and run the transient simulation. Plot `V(out)`, `I(L1)`, `V(sw)`, and `V(duty)`, and check **View → SPICE Error Log** for `.meas` results across `10 V`, `12 V`, and `14 V`. See `docs/simulation_procedure.md` for details.

## Important limitations

The closed-loop controller is a tuned behavioral PID/PWM model (`KP=0.015`, `KI=65`, `KD=1.2`), not a manufacturer-specific controller IC. The high-side switch (`SWMOD`, `30 mΩ`) is an assumed idealized switch without a floating gate driver or switching losses, and `L1`/`C1` omit core loss, saturation nonlinearity, ESL, and temperature/tolerance variation. Under the `130 mΩ` datasheet max ESR, the `< 50 mVpp` ripple target is met at `12 V` (`45.88 mVpp`) but not at `14 V` (`53.84 mVpp`) or `10 V` (`72.71 mVpp`).
