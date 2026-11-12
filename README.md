# 12 V to 5 V Buck Converter Project

This repository contains a complete engineering study of a 12 V-to-5 V DC-DC buck converter designed and simulated in LTspice.

## Project objective

The project aims to:

- design a 12 V-to-5 V buck converter from first principles
- calculate component values using switching-power equations
- simulate the power stage in LTspice
- evaluate the effect of non-ideal components
- analyse the output voltage, ripple, and transient response

## Design specification

| Parameter | Value |
|---|---:|
| Input voltage | 12 V nominal |
| Output voltage | 5 V regulated |
| Maximum load current | 1 A |
| Switching frequency | 100 kHz |
| Inductor ripple target | 0.3 A peak-to-peak |
| Output ripple target | low ripple, typically < 50 mVpp |
| Input operating range | 10 V to 14 V |

## Baseline design summary

The ideal buck-converter duty cycle is:

D ≈ Vout / Vin = 5 / 12 ≈ 0.417

The initial inductor value is estimated from:

L ≈ ((Vin - Vout) × D) / (fs × ΔIL)

For Vin = 12 V, Vout = 5 V, fs = 100 kHz, and ΔIL = 0.3 A:

L ≈ 97 µH

A practical starting value of 100 µH is selected.

## Repository contents

- `README.md` — overview of the project
- `docs/design_notes.md` — engineering notes and equations
- `docs/project_report.md` — written project report draft
- `docs/simulation_procedure.md` — how to run the LTspice tests
- `docs/results_template.md` — measurement table for collecting results
- `ltspice/buck_converter.cir` — LTspice netlist for the buck-converter power stage

## What the simulation currently models

The LTspice netlist represents:

- a 12 V DC source
- a switching transistor stage
- a Schottky freewheel diode
- 100 µH inductor with series resistance
- 220 µF output capacitor with ESR
- nominal 1 A resistive load

It is suitable for evaluating:

- the switching waveform
- inductor current ripple
- output voltage ripple
- start-up transient behaviour
- line and load effects in the power stage

## Important note

This version is a baseline power-stage simulation. It does not yet include a closed-loop feedback controller that automatically regulates the output under changing load and input conditions. The project therefore represents a solid engineering starting point, but a full regulated converter would require:

- feedback divider
- error amplifier or comparator
- PWM control loop
- compensation network
- a more realistic gate-driver and MOSFET model

## How to run the LTspice simulation

1. Open LTspice.
2. Open `ltspice/buck_converter.cir`.
3. Run the transient analysis.
4. Plot:
   - `V(out2)` for output voltage
   - `I(L1)` for inductor current
   - `V(sw)` for the switch node

## What to record

Use `docs/results_template.md` and record:

- output voltage at each input/load condition
- ripple voltage
- inductor ripple current
- settling time after load step
- thermal or loss observations if available

## Future extension

The next development step is to add a closed-loop regulated design so the output remains at 5 V when the input source or load current changes. This would be a more complete engineering implementation and a stronger final project.

## Summary

The repository is already a credible starter project for a 12 V-to-5 V buck converter and contains the basic theory, calculations, LTspice model, and project documentation. It is now ready for simulation execution and result collection, while remaining open to further extension into a fully regulated converter design.
