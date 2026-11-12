# 12 V to 5 V Buck Converter (LTspice)

This repository contains a practical LTspice simulation of a 12 V-to-5 V DC-DC buck converter intended for design and performance characterisation.

## Objective

Design and simulate a buck converter that converts a 12 V DC input to a regulated 5 V DC output, while investigating:

- duty-cycle selection
- inductor and capacitor sizing
- non-ideal semiconductor and passive effects
- line regulation with changing input voltage
- load regulation and transient response with changing load current

## Design assumptions

- Nominal input voltage: 12 V
- Output voltage target: 5 V
- Maximum output current: 1 A
- Switching frequency: 100 kHz
- Inductor ripple target: 0.3 A peak-to-peak
- Output capacitor: 220 µF with ESR modelled

## Design calculations

Ideal duty cycle:

D ≈ Vout / Vin = 5 / 12 ≈ 0.417

Inductor estimate:

L ≈ ((Vin - Vout) * D) / (fs * ΔIL)

For Vin = 12 V, Vout = 5 V, fs = 100 kHz, ΔIL = 0.3 A:

L ≈ ((12 - 5) * 0.417) / (100000 * 0.3) ≈ 97 µH

A practical starting point is therefore:

- L = 100 µH
- output capacitor = 220 µF
- diode = Schottky, current rating above 1.5 A
- ideal MOSFET or switching element model with finite Ron

## LTspice simulation

The file `ltspice/buck_converter.cir` contains a SPICE netlist that models:

- ideal 12 V source
- a high-side switch driven by a 100 kHz PWM signal
- freewheel diode
- 100 µH inductor with DCR
- 220 µF output capacitor with ESR
- 5 Ω load for a nominal 1 A output
- transient analysis for output voltage and inductor-current behaviour

This is a practical baseline testbench for exploring switching behaviour and ripple. The duty cycle is set to 0.417 as a first-pass design point.

## How to run in LTspice

1. Open LTspice.
2. Go to File → Open and select `ltspice/buck_converter.cir`.
3. Run the transient simulation.
4. Plot:
   - output voltage (`V(out)`)
   - inductor current (`I(L1)`)
   - switch node (`V(sw)`)
   - diode current if extra plots are added

## Example investigations

- Input-voltage sweep: 10 V, 12 V, 14 V
- Load sweep: 0.5 A, 1 A
- Load step: 0.5 A → 1 A
- Add ESR / DCR to compare ideal vs. non-ideal behaviour
- Compare waveform ripple and regulation at each operating point

## Notes

This project intentionally starts from a straightforward buck-converter model to make the power-stage behaviour easy to analyse. It is ideal for a first pass before designing a closed-loop regulator with compensation and synchronous switching.

If you want to extend it further, the next practical step is to add:

- a feedback comparator and reference
- a MOSFET gate driver
- a compensation network
- a more realistic MOSFET model
- thermal loss estimation

## Repository contents

- `README.md` — project overview and design summary
- `docs/design_notes.md` — more detailed notes and equations
- `ltspice/buck_converter.cir` — LTspice transient simulation netlist

## Author

This project was created for a DC-DC buck converter design and performance characterisation study.
