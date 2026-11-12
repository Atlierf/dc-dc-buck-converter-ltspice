# Design notes for the 12 V to 5 V buck converter

## 1. Operating principle

The buck converter reduces the input DC voltage by chopping it with a high-frequency switch, then filtering the result using an inductor and capacitor. In steady state, the average output voltage is approximately:

Vout ≈ D * Vin

For Vin = 12 V and Vout = 5 V,

D ≈ 5 / 12 ≈ 0.417

This is the ideal duty cycle used for the first-pass design.

## 2. Initial component sizing

### Inductor

Target ripple current: ΔIL = 0.3 A

Switching frequency: fs = 100 kHz

L ≈ ((Vin - Vout) * D) / (fs * ΔIL)

L ≈ ((12 - 5) * 0.417) / (100000 * 0.3)

L ≈ 97 µH

Use a standard value close to 100 µH.

### Output capacitor

Capacitor ripple approximation:

ΔV ≈ ΔIL / (8 * fs * C)

A 220 µF capacitor is a good first choice and produces low ripple in the ideal model. In practice, ESR and load transients can add extra ripple. The LTspice netlist models capacitor ESR directly.

### Diode

Use a Schottky diode because it has a lower forward drop and faster recovery than a standard diode. The diode must be rated for:

- reverse voltage above 12 V
- continuous current above the output current
- adequate thermal handling

## 3. Non-ideal effects to investigate

The following effects are important in real converters and should be captured in simulation:

- inductor DCR (series resistance)
- capacitor ESR
- diode forward voltage and dynamic resistance
- MOSFET Rds(on)
- switching transition loss
- input and output parasitics
- transient response to sudden load changes

## 4. Evaluation criteria

The simulation should be used to assess:

- output voltage regulation under line variations
- output voltage regulation under load changes
- inductor current ripple
- output ripple
- startup behaviour
- response to load steps
- power loss and efficiency trends

## 5. Simulation interpretation

If the output is near 5 V at the nominal operating point, the design is considered viable. If the output drifts substantially with input or load variation, the design should be refined by adjusting:

- duty cycle
- inductor value
- capacitor ESR and value
- diode selection
- switching frequency

## 6. Suggested extensions

The next stage of the project would be to implement a closed-loop regulator with:

- voltage feedback divider
- error amplifier / comparator
- pulse-width modulation control
- compensation network

This would allow a more realistic investigation of transient performance and stability, rather than simply using a fixed duty cycle.
