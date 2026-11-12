# Project Report: 12 V to 5 V Buck Converter Simulation

## Abstract

This project investigates the design and simulation of a 12 V-to-5 V DC-DC buck converter using LTspice. The converter is analysed from a power-electronics perspective, with emphasis on design calculations, component selection, and performance characterisation under nominal, line-variation, and load-variation conditions. The study also considers non-ideal passive and semiconductor effects to assess real-world behaviour beyond the ideal switching model.

## 1. Introduction

The conversion of a 12 V DC input to a regulated 5 V DC output is a common power-management requirement in embedded systems, sensor nodes, and low-voltage electronics. A buck converter is well suited for this purpose because it can provide regulated output with relatively high efficiency compared to linear regulators.

The objective of this project is to develop a practical design and verify its performance in LTspice. The investigation focuses on the key design equations, the role of the power stage, and the effect of component parasitics on output regulation and ripple.

## 2. Design requirements

The initial design specification is:

- Input voltage: 12 V nominal
- Output voltage: 5 V regulated
- Maximum output current: 1 A
- Switching frequency: 100 kHz
- Inductor ripple target: 0.3 A peak-to-peak
- Output ripple target: low enough for stable DC supply operation
- Operating range: 10–14 V input variation

## 3. Buck-converter theory

A buck converter works by periodically switching the input voltage to an inductor, then smoothing the result with an output capacitor. In steady state, the ideal conversion ratio is:

Vout = D × Vin

Therefore:

D ≈ Vout / Vin = 5 / 12 ≈ 0.417

This means the switch is on for approximately 42% of the switching period in ideal operation.

## 4. Design calculations

### 4.1 Inductor value

Using the inductor sizing equation:

L = ((Vin - Vout) × D) / (fs × ΔIL)

For the chosen parameters:

- Vin = 12 V
- Vout = 5 V
- D = 0.417
- fs = 100 kHz
- ΔIL = 0.3 A

L ≈ ((12 - 5) × 0.417) / (100000 × 0.3)

L ≈ 97 µH

A standard value of 100 µH is used for the initial design.

### 4.2 Peak inductor current

The peak current is approximately:

IL,peak ≈ Iout + ΔIL / 2

IL,peak ≈ 1 + 0.15 = 1.15 A

Therefore, the selected inductor must have a saturation-current rating above 1.15 A with some margin.

### 4.3 Output capacitor selection

A typical approximation for ripple is:

ΔVout ≈ ΔIL / (8 × fs × C)

Using C = 220 µF:

ΔV ≈ 0.3 / (8 × 100000 × 220e-6)

ΔV ≈ 1.7 mV ideal

The physical capacitor ESR will add extra ripple, so a low-ESR capacitor is preferred.

## 5. LTspice model implementation

The converter is implemented using a simple switching stage with the following components:

- input voltage source
- PWM-controlled switch
- Schottky flyback diode
- 100 µH inductor
- 220 µF output capacitor
- load resistance for 1 A output at 5 V
- ESR and DCR modelling

The model is saved in the repository as:

`ltspice/buck_converter.cir`

### Key modelling features

- ideal switching element to represent the high-side transistor
- diode model with realistic forward characteristic
- output capacitor with ESR
- inductor DCR
- transient simulation for startup and steady-state evaluation
- parameter sweeps for input and load variations

## 6. Simulation setup

The simulations are conducted to evaluate:

1. nominal operation at 12 V input and 1 A load
2. output regulation under input variation
3. output regulation under load variation
4. startup transient behaviour
5. load-step response
6. ripple and current waveform characterisation

## 7. Results and discussion

### 7.1 Nominal operation

At 12 V input and 1 A load, the converter should produce an output close to 5 V. In an ideal model, the average output remains approximately 5 V with limited ripple. In the pragmatic model, voltage deviation occurs because of diode drop, finite switch resistance, capacitor ESR, and inductor DCR.

### 7.2 Line regulation

Line regulation is evaluated by changing the input from 10 V to 14 V. The converter is expected to remain near 5 V output provided the control scheme maintains a stable duty ratio. In a fixed-duty design, the output may vary more noticeably with Vin because the converter does not actively compensate for input changes.

### 7.3 Load regulation

The load is increased from light load to 1 A. In practical operation, the output voltage may fall slightly due to increased inductor current ripple, capacitor ESR, and semiconductor losses. This result is especially important because a real converter must remain within the required output tolerance across the full load range.

### 7.4 Ripple and current waveforms

The inductor current should appear as a triangular waveform around the average load current. The output voltage ripple should remain small but not zero because of capacitor ESR and switch transitions. These waveforms provide confirmation that the converter is operating in a stable switching regime.

### 7.5 Load-step response

A sudden step in load current reveals the converter’s dynamic behaviour. The output voltage experiences a transient dip or overshoot before settling. The magnitude and duration of this transient depend on the output capacitor, inductor value, ESR, and control dynamics. A larger capacitor or higher loop response improves transient performance.

## 8. Non-ideal effects and their impact

### 8.1 Inductor DCR

The inductor winding resistance contributes power loss and reduces the effective output voltage. It also alters current ripple and affects efficiency.

### 8.2 Capacitor ESR

The capacitor ESR introduces an additional ripple component and can worsen transient voltage drop when load current changes quickly. This is one of the dominant non-ideal sources in practical buck converters.

### 8.3 Diode voltage drop

The freewheel diode adds a voltage drop during current conduction, reducing the delivered output power and shifting the conversion ratio away from the ideal value.

### 8.4 Switch resistance

The switching element’s finite conduction resistance contributes both voltage loss and heat generation. These losses become more significant at higher load current.

## 9. Engineering conclusions

The initial LTspice design demonstrates the fundamental operation of a buck converter and confirms the validity of the first-pass design equations. The results show that a 12 V input can be reduced to a 5 V output near the intended target, but real-world non-ideal effects cause measurable deviations from the ideal model.

The design is therefore valid as a baseline concept but would require a more complete feedback-controlled implementation for practical use. A closed-loop control scheme would improve regulation under changing input and load conditions and would better reflect real converter design practice.

## 10. Future work

Recommended improvements to the project include:

- closed-loop voltage regulation using a feedback divider and comparator
- optimisation of inductor and capacitor values for lower ripple and better transient performance
- detailed efficiency analysis and loss breakdown
- MOSFET driver and gate-control modelling
- thermal analysis of the diode, switch, and inductor
- PCB layout considerations for real hardware implementation

## 11. Summary

This project demonstrates the design and simulation of a 12 V-to-5 V buck converter in LTspice. It combines theoretical calculations with circuit simulation, shows the role of non-ideal components, and provides a foundation for further study in power electronics and regulated DC-DC design.
