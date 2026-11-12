# Project Report: 12 V to 5 V Buck Converter Simulation

## Abstract

This project uses LTspice to study a 12 V-to-5 V buck-converter model at up to a nominal 1 A load. First-pass calculations set a 100 kHz switching frequency, 100 µH inductor, and 220 µF output capacitor. The closed-loop testbench uses a behavioral PID/PWM controller and models a Schottky diode, finite switch resistance, inductor DCR, and output-capacitor ESR. The committed LTspice run includes input cases of 10 V, 12 V, and 14 V and a modeled load change from 0.5 A to 1 A.

Based on the record, average output was 4.927 – 5.014 V over the selected measurement window. Output ripple was below the 50 mVpp project target only at 12 V input. Because the controller was not in the ±2% band before the load transition at 10 V and 14 V, those cases do not represent a clean load-step test from established steady state. Results apply only to the simplified simulation model; they are not hardware measurements.

## 1. Introduction

A buck converter converts a higher DC voltage to a lower DC voltage by switching energy through an inductor and filtering it with a capacitor. This project examines a simulated 12 V-to-5 V converter, focusing on first-pass component calculations, non-ideal model assumptions, output behavior, and the limits of the available simulation evidence.

## 2. Design specification

| Parameter | Value / criterion |
|---|---:|
| Nominal input | 12 V DC |
| Simulated input cases | 10 V, 12 V, 14 V (separate stepped runs) |
| Output target | 5 V |
| Nominal maximum load | 1 A |
| Switching frequency | 100 kHz |
| Inductor ripple target | 0.3 App at nominal input |
| Inductor | 100 µH; modeled DCR 180 mΩ |
| Output capacitor | 220 µF; modeled ESR 130 mΩ |
| Project ripple criterion | <50 mVpp |
| Load transition | 0.5 A-equivalent to 1 A-equivalent at 2 ms |

The target criteria are study requirements, not a claim that every criterion is met.

## 3. Theory and component calculations

For an ideal buck converter in continuous conduction:

\[
V_{out}\approx D V_{in}, \qquad D\approx\frac{V_{out}}{V_{in}}
\]

At 12 V input and 5 V output, the ideal duty ratio is approximately 0.4167. With 100 kHz switching, the period is 10 µs.

The first-pass inductance estimate is:

\[
L=\frac{(V_{in}-V_{out})D}{f_s\Delta I_L}
=\frac{(12-5)(0.4167)}{100\,000(0.3)}\approx97.2\,\mu H
\]

A 100 µH model value was selected. The corresponding ideal nominal ripple estimate is approximately 0.292 App, and estimated peak current at 1 A output is approximately 1.146 A.

The ideal capacitor ripple estimate is:

\[
\Delta V_C\approx\frac{0.3}{8(100\,000)(220\,\mu F)}\approx1.7\,mV_{pp}
\]

A simple ESR contribution estimate using 130 mΩ is about 39 mVpp. These calculations are approximate; actual simulated ripple includes the modeled circuit behavior and must be taken from LTspice measurements.

## 4. Simulation model and method

The closed-loop netlist is `ltspice/buck_converter_closed_loop.cir`. It uses a behavioral PID controller (`KP=0.015`, `KI=65`, `KD=1.2`), a 100 kHz PWM carrier, an idealized voltage-controlled switch with 30 mΩ on-resistance, an MBRS340 diode model, a 100 µH inductor with 180 mΩ series resistance, and a 220 µF capacitor with 130 mΩ ESR. The input parameter is stepped across 10 V, 12 V, and 14 V. In each run the behavioral load changes from 10 Ω to 5 Ω at 2 ms.

The simulation results and waveform plots are stored in `results/`. The reported values below are taken from `results/buck_converter_closed_loop_10V_12V_14V.log.txt`. Run details and model provenance are documented in `results/README.md` and `docs/component_models.md`.

## 5. Recorded simulation results

### 5.1 Output voltage, ripple, and current ripple

| Input | Pre-transition average (V) | Post-transition average (V) | Post-window Vout ripple (mVpp) | Post-window inductor ripple (App) | Average error from 5 V |
|---:|---:|---:|---:|---:|---:|
| 10 V | 4.7137 | 4.9269 | 72.71 | 0.2597 | −1.46% |
| 12 V | 5.0228 | 4.9887 | 45.88 | 0.3049 | −0.23% |
| 14 V | 5.2457 | 5.0144 | 53.84 | 0.3401 | +0.29% |

The post-transition averages in the 4.0–4.9 ms window are within ±2% of 5 V. This does not by itself prove steady-state regulation: at 10 V and 14 V, the pre-transition averages are outside the ±2% band, indicating that the output had not settled before the load change. The 50 mVpp ripple criterion is met at 12 V but exceeded at 10 V and 14 V.

### 5.2 Load-transition excursion and timing

| Input | Minimum output from 2.0–4.99 ms (V) | Maximum output (V) | Last ±0.1 V boundary crossing after 2 ms (ms) |
|---:|---:|---:|---:|
| 10 V | 4.5814 | 4.9644 | 2.200 |
| 12 V | 4.8149 | 5.0116 | 0.460 |
| 14 V | 4.9661 | 5.1436 | 0.034 |

The last-boundary-crossing metric is calculated relative to 4.9–5.1 V and the 2 ms event time. At 10 V and 14 V, the pre-transition operating point was already outside this band, so these numbers should not be treated as conventional load-step settling times. The recorded minimum at 10 V also shows a substantial undershoot during the post-event interval.

### 5.3 Estimated simulation efficiency

| Input | Average input power (W) | Average output power (W) | Model efficiency (%) |
|---:|---:|---:|---:|
| 10 V | 5.2651 | 4.8550 | 92.21 |
| 12 V | 5.3766 | 4.9774 | 92.58 |
| 14 V | 5.4259 | 5.0288 | 92.68 |

These values are calculated from the LTspice model over 4.0–4.9 ms. They are estimates for this behavioral circuit and should not be presented as hardware efficiency or as a prediction for a particular MOSFET implementation.

## 6. Discussion

The post-transition average output is close to 5 V in the chosen measurement window, but the run does not show that the controller was settled before the 2 ms load transition for every input condition. The 10 V pre-transition average is 4.7137 V and the 14 V value is 5.2457 V. A longer preconditioning interval and a later load step are needed to separate startup/line settling from load-step response.

The output-ripple goal is met only at the 12 V input case. The 10 V and 14 V results exceed 50 mVpp. The modeled capacitor ESR is 130 mΩ, and the measured inductor-current ripple increases with input voltage in this run. Further investigation should examine ESR, capacitance, controller response, and measurement windows. Any revised design must be rerun and its new results recorded rather than inferred from these data.

The reported efficiency is model-dependent. The high-side switch is an idealized voltage-controlled switch with finite on-resistance, not a real MOSFET with gate charge and switching losses. The diode and passive models are also simplified. Therefore, the values characterize the netlist assumptions only.

## 7. Conclusion

The project establishes a first-pass 100 kHz buck-converter model with 100 µH and 220 µF energy-storage components and includes a behavioral closed-loop controller. In the committed LTspice run, post-window average output was within ±2% of 5 V for all three input cases, while the <50 mVpp ripple goal was met only at 12 V. The pre-transition measurements at 10 V and 14 V show that those cases were not settled before the 2 ms load transition, limiting interpretation of their load-step results.

The simulation is not a validated hardware design. The next verification step is to extend the pre-step settling interval, repeat the load step from a demonstrably stable operating point, and rerun after any controller or component changes. A real MOSFET model, thermal analysis, and hardware measurements would be required for stronger practical conclusions.

## 8. Model limitations and further work

- The PID/PWM controller is behavioral and its stability is not established by a formal loop-gain analysis.
- The high-side switch is not a real MOSFET model; gate drive, switching loss, and thermal rise are omitted.
- Passive models omit inductor core losses, saturation roll-off, capacitor ESL, tolerance, and temperature dependence.
- The 10 V and 14 V cases need longer pre-step settling before clean load-transient claims can be made.
- The 50 mVpp output-ripple target is not met for two of the three input cases.
- Repeat simulation using a later load step and sufficiently long steady-state windows; record all changed parameters and preserve new logs/plots.
- Hardware testing, if undertaken, requires component-rating, PCB-layout, thermal, and safe-power-up review.
