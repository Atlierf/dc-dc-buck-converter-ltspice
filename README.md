# 12 V to 5 V Buck Converter 

This repository documents a complete engineering study of a 12 V-to-5 V DC-DC buck converter designed and simulated in LTspice.

## Objective

The project aims to:

- design a 12 V-to-5 V buck converter based on practical switching-power theory
- calculate the required component values from selected design constraints
- simulate the converter in LTspice under nominal, line-variation, and load-variation conditions
- study non-ideal behaviour due to inductor resistance, capacitor ESR, diode voltage drop, and finite switch conduction losses
- evaluate output regulation, ripple, transient response, and efficiency trends

## Design specification

| Parameter | Target |
|---|---:|
| Input voltage | 12 V nominal |
| Output voltage | 5 V regulated |
| Maximum load current | 1 A |
| Switching frequency | 100 kHz |
| Inductor ripple target | 0.3 A peak-to-peak |
| Output ripple target | low ripple, typically < 50 mVpp |
| Operating input range | 10–14 V |

## Design approach

The converter is designed using the standard buck-converter relationship:

Vout ≈ D × Vin

For a 12 V input and 5 V output, the ideal duty cycle is approximately:

D ≈ 5 / 12 ≈ 0.417

The initial inductor value is estimated as:

L ≈ ((Vin - Vout) × D) / (fs × ΔIL)

Using Vin = 12 V, Vout = 5 V, fs = 100 kHz, and ΔIL = 0.3 A gives:

L ≈ 97 µH

A practical starting value of 100 µH is therefore selected.

## Repository structure

- `README.md` — overview and project summary
- `docs/design_notes.md` — design equations and engineering notes
- `docs/project_report.md` — complete engineering report with methodology and discussion
- `ltspice/buck_converter.cir` — LTspice transient simulation netlist

## Simulation scope

The LTspice file includes:

- a 12 V supply
- ideal PWM-controlled switch
- Schottky freewheel diode
- 100 µH inductor and output capacitor
- ESR and DCR modelling
- nominal 1 A resistive load
- parameter sweeps for input and load variation

The project evaluates:

- startup behaviour
- steady-state output voltage
- inductor current ripple
- output ripple
- line regulation
- load regulation
- transient response under load steps

## How to use

1. Open LTspice.
2. Open `ltspice/buck_converter.cir`.
3. Run the transient analysis.
4. Plot desired signals such as:
   - V(out)
   - I(L1)
   - V(sw)
   - output ripple

## Expected findings

At nominal conditions, the output should settle close to 5 V. The real implementation will show deviation from the ideal model because of active losses, component parasitics, and switching dynamics. These effects are explored in the report and design notes.

## Future work

Possible extensions include:

- closed-loop regulation with feedback and compensation
- synchronous buck converter design
- MOSFET and diode loss estimation
- thermal analysis and efficiency mapping
- PCB implementation and prototype validation

## Contribution

This project is intended for engineering study and simulation-based analysis. It can be used as a basis for coursework, design review, or a prototype development project.
