# Design Notes for the 12 V-to-5 V Buck Converter

## 1. System objective

The purpose of the converter is to step down a 12 V DC input to a regulated 5 V output suitable for low-voltage electronics. The project evaluates the converter’s electrical performance using LTspice and studies non-ideal component and semiconductor behaviour.

## 2. Buck-converter operating principle

A buck converter reduces voltage by switching the input supply on and off at a high frequency and then filtering the switched waveform using an inductor-capacitor network. In ideal operation, the average output is:

Vout = D × Vin

where D is the duty cycle.

For Vin = 12 V and Vout = 5 V:

D ≈ 5 / 12 ≈ 0.417

This indicates that the high-side switch conducts for approximately 42% of each PWM period in the ideal model.

## 3. Baseline design assumptions

- Input voltage: 12 V nominal
- Output voltage: 5 V
- Maximum output current: 1 A
- Switching frequency: 100 kHz
- Inductor ripple target: 0.3 A
- Output capacitor: 220 µF
- Output capacitor ESR: modelled in simulation
- Diode: Schottky, low forward drop

## 4. Inductor design

A standard buck-regulator approximation for the inductor is:

L = ((Vin - Vout) × D) / (fs × ΔIL)

Substituting the baseline values:

L ≈ ((12 - 5) × 0.417) / (100000 × 0.3)

L ≈ 97 µH

A practical standard value is 100 µH. This provides a first-pass design with realistic ripple current and acceptable average current stress.

The peak inductor current at 1 A output is approximately:

IL,peak ≈ Iout + ΔIL / 2

IL,peak ≈ 1 + 0.15 = 1.15 A

Therefore, the selected inductor should have a saturation current comfortably above 1.15 A.

## 5. Output capacitor selection

The output capacitor filters the switched current and reduces ripple. A rough estimate for voltage ripple is:

ΔVout ≈ ΔIL / (8 × fs × C)

For ΔIL = 0.3 A, fs = 100 kHz, and C = 220 µF:

ΔV ≈ 0.3 / (8 × 100000 × 220e-6)

ΔV ≈ 1.7 mV

This is the ideal ripple contribution. In reality, ESR adds a significant ripple component and must be modelled explicitly because it can dominate the total ripple at moderate-to-high load currents.

## 6. Diode and switch selection

A Schottky diode is preferred because it has:

- lower forward drop than a silicon diode
- lower reverse recovery charge
- lower switching loss

The freewheel diode and the switching element should both be selected with ratings above:

- output current requirement
- maximum reverse voltage
- thermal operating limits

## 7. Non-ideal behaviour to investigate

Real components introduce losses and dynamic effects. In this project, the following items are explicitly included in the LTspice model where practical:

- inductor DC resistance (DCR)
- capacitor ESR
- diode forward voltage and junction characteristics
- MOSFET on-resistance and finite switching behaviour
- transient effects during startup and load steps

These effects are important because they modify:

- output regulation
- ripple amplitude
- efficiency
- thermal stress
- dynamic response

## 8. Simulation strategy

The simulation is set up to study:

- steady-state output at nominal input
- line regulation at 10 V, 12 V, and 14 V
- load regulation at 0.5 A and 1 A
- output ripple under steady-state operation
- startup transient behaviour
- transient response to sudden load changes

## 9. Evaluation metrics

The performance of the converter is judged according to:

- output voltage accuracy
- output ripple
- load regulation
- line regulation
- transient settling time
- observed losses and thermal implications

The standard performance equations used are:

Pin = Vin × Iin

Pout = Vout × Iout

η = (Pout / Pin) × 100%

## 10. Practical engineering interpretation

If the LTspice model shows a near-constant 5 V output under the selected conditions, the initial design is valid. If the output drifts with input voltage or load current, the design must be refined by changing:

- duty cycle
- inductor value
- output capacitance and ESR
- diode type and rating
- switching frequency

## 11. Recommended next steps

The next engineering development would be to add a closed-loop feedback network so the converter regulates the output automatically rather than relying on a fixed duty cycle. This can be done by introducing:

- reference voltage
- error amplifier
- feedback divider
- compensation network
- MOSFET gate-driver model

This would allow more realistic evaluation of stability and transient performance.

## 12. Summary

This project uses a practical design approach to transform a 12 V source into a 5 V regulated output. The LTspice model is intentionally simple enough to understand but realistic enough to include essential non-ideal behaviours. It provides a solid basis for more advanced controller design and hardware prototyping.
