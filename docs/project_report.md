# 12 V to 5 V Buck Converter: Design and LTspice Study

## Abstract

This project develops a first-pass, closed-loop buck-converter model for converting a nominal 12 V DC input to a 5 V output at up to 1 A. Component values are estimated from switching frequency and inductor-ripple requirements. The LTspice netlist includes a behavioral PWM controller, a diode freewheel path, finite switch resistance, inductor DCR, and output-capacitor ESR. The planned study covers input-voltage variation, steady-state ripple, and a 0.5 A-to-1 A load step.

**Important:** The netlist and report are a simulation-ready project package, but no LTspice run has been performed in this environment. Numerical simulation results must be generated in LTspice and entered in `docs/results_template.md`; no measured or simulated values are claimed here.

## Project specification

| Quantity | Design target / assumption |
|---|---:|
| Nominal input voltage | 12 V DC |
| Input test points | 10 V, 12 V, 14 V |
| Output voltage | 5 V |
| Maximum output current | 1 A |
| Switching frequency | 100 kHz |
| Inductor ripple target at nominal input | 0.3 A peak-to-peak |
| Inductor | 100 µH, modeled with 70 mΩ DCR |
| Output capacitor | 220 µF, modeled with 50 mΩ ESR |
| Load-step test | 0.5 A to 1 A at 2 ms |
| Output-ripple goal | <50 mV peak-to-peak (project target to verify) |

These component parasitics are initial modeling assumptions, not tied to a particular purchased part. Substitute manufacturer datasheet values for a component-specific study.

## 1. Introduction

A buck converter is a switching power converter that produces a lower DC voltage from a higher DC voltage. It is used in power supplies because switching conversion can be more efficient than dissipating the excess voltage in a linear regulator. The project studies a 12 V-to-5 V converter in LTspice and focuses on component sizing, non-ideal behavior, regulation, and transient response.

## 2. Operating principle

The switch applies the input voltage to the inductor for part of each switching period. When the switch turns off, the inductor current continues through the freewheel diode. The inductor and output capacitor smooth the switched energy into a DC output.

For an ideal buck converter operating in continuous conduction, the conversion ratio is approximately:

\[
V_{out}=D V_{in}, \qquad D=\frac{V_{out}}{V_{in}}
\]

At 12 V input and 5 V output, the ideal duty ratio is:

\[
D=\frac{5}{12}=0.4167
\]

A practical closed-loop converter adjusts its duty ratio as input voltage, load, and component losses change. The LTspice model uses a behavioral PI controller and PWM comparator to demonstrate this feedback action. It is an educational averaged-control approximation around a switching power stage, not a validated commercial controller model.

## 3. Design requirements and assumptions

The design target is 5 V at loads up to 1 A, with a nominal input of 12 V and line tests at 10 V and 14 V. The selected switching frequency is 100 kHz. Inductor ripple is targeted at approximately 30% of full-load current, or 0.3 A peak-to-peak.

The model includes finite switch on-resistance, diode characteristics, inductor DCR, and capacitor ESR. It does not include a manufacturer-specific controller, a detailed MOSFET gate driver, PCB parasitics, thermal coupling, or component tolerances.

## 4. Component calculations

### 4.1 Switching period and ideal duty ratio

\[
f_s=100\,\text{kHz}, \qquad T_s=\frac{1}{f_s}=10\,\mu\text{s}
\]

\[
D=\frac{5}{12}=0.4167
\]

### 4.2 Inductor

The first-pass buck-inductor equation is:

\[
L=\frac{(V_{in}-V_{out})D}{f_s\Delta I_L}
\]

Using the nominal operating point and a 0.3 A peak-to-peak ripple target:

\[
L=\frac{(12-5)(0.4167)}{(100\,000)(0.3)}\approx97.2\,\mu\text{H}
\]

The nearest convenient selected value is 100 µH. With that selected value, the ideal nominal ripple estimate is:

\[
\Delta I_L\approx\frac{(12-5)(0.4167)}{(100\,000)(100\,\mu\text{H})}\approx0.292\,\text{A}_{pp}
\]

At a 1 A average output current, the approximate peak inductor current is:

\[
I_{L,peak}\approx I_{out}+\frac{\Delta I_L}{2}\approx1.146\,\text{A}
\]

A real component should have adequate saturation-current and thermal margin above this estimate. The simulated 70 mΩ DCR is an assumed value and should be replaced with a selected inductor's datasheet value.

### 4.3 Output capacitor

Ignoring ESR, the approximate capacitive ripple is:

\[
\Delta V_C\approx\frac{\Delta I_L}{8f_sC}
\]

For 0.3 A ripple, 100 kHz, and 220 µF:

\[
\Delta V_C\approx\frac{0.3}{8(100\,000)(220\,\mu\text{F})}\approx1.7\,\text{mV}_{pp}
\]

This is only the ideal capacitive contribution. With 50 mΩ ESR, the simple ESR ripple estimate is:

\[
\Delta V_{ESR}\approx\Delta I_L\,ESR\approx0.3(0.05)=15\,\text{mV}_{pp}
\]

Actual total ripple must be measured from the switching simulation; transient and model effects are not captured by these simple estimates.

### 4.4 Load

A 5 Ω resistive load draws 1 A at 5 V:

\[
R=\frac{V}{I}=\frac{5}{1}=5\,\Omega
\]

The LTspice testbench uses a behavioral load that changes from 10 Ω (0.5 A nominal at 5 V) to 5 Ω (1 A) at 2 ms.

## 5. Circuit and model implementation

The simulation file is `ltspice/buck_converter_closed_loop.cir`. It contains:

- a parameterized DC input and 100 kHz timing reference
- a PWM comparator and bounded behavioral PI control command
- an idealized voltage-controlled switch with finite on-resistance
- a Schottky-style freewheel diode model
- a 100 µH inductor with 70 mΩ series resistance
- a 220 µF output capacitor with 50 mΩ ESR
- a 0.5 A-to-1 A behavioral load step
- transient measurement directives and an input-voltage parameter sweep

The PI gains and semiconductor models are educational starting assumptions. The loop must be simulated and, if necessary, tuned before its transient performance can be considered acceptable. This model does not establish hardware safety, stability margins, or a particular real component's performance.

## 6. Simulation procedure

1. Open `ltspice/buck_converter_closed_loop.cir` in LTspice.
2. Run the transient analysis. The netlist steps the input through 10 V, 12 V, and 14 V.
3. Plot `V(out)` for output voltage, `I(L1)` for inductor current, `V(sw)` for switch-node voltage, and `V(duty)` for the control command.
4. Check whether output voltage settles near 5 V before and after the load step at 2 ms.
5. Review the `.meas` results in the LTspice error log; they report pre-step and post-step average output, post-step output ripple, and inductor-current ripple.
6. Record results for each input-voltage step in `docs/results_template.md`.
7. If the run reports convergence errors, inspect the model and solver settings rather than treating missing measurements as valid results.

## 7. Results

### 7.1 Status of results

No numerical LTspice results are included in this report yet. This repository update provides a closed-loop testbench and a reproducible procedure, but the netlist has not been executed here. The following result statements must be completed from actual LTspice output; do not replace them with predicted values.

### 7.2 Steady-state regulation

At each input step (10 V, 12 V, and 14 V), record the average output voltage during the post-load-step interval. Compare each value with the 5 V target and state the percentage error:

\[
\text{Output error (\%)}=\frac{V_{out,avg}-5}{5}\times100\%
\]

**Result:** [Enter measured LTspice values and whether they meet the selected output tolerance.]

### 7.3 Output and inductor ripple

Use the LTspice measurements over the specified post-step interval. Compare measured output ripple with the project target of 50 mV peak-to-peak and compare inductor ripple with the approximately 0.292 A nominal ideal estimate.

**Result:** [Enter measured output ripple and inductor-current ripple for each input condition.]

### 7.4 Load transient

The behavioral load changes from 10 Ω to 5 Ω at 2 ms. Record the minimum output voltage after the load increase, any overshoot, and the time required to return to the chosen regulation band. The provided `.meas` statements give interval averages and peak-to-peak values; use the waveform cursors or add measurements to determine minimum voltage and settling time.

**Result:** [Enter measured dip/overshoot and settling time.]

### 7.5 Input-voltage variation

Compare post-step average output across the 10 V, 12 V, and 14 V stepped runs. A functioning closed loop should adjust duty cycle to reduce output variation, but this must be verified by the simulation.

**Result:** [Enter the measured line-regulation comparison.]

## 8. Discussion of non-ideal behavior

Inductor DCR causes conduction loss and a load-dependent voltage drop. Capacitor ESR contributes switching ripple and an immediate voltage change during load transients. The diode has a forward drop while carrying freewheel current, and the switch's finite on-resistance causes conduction loss. Together, these effects make the required duty ratio differ from the ideal 0.4167 value.

The behavioral feedback controller is intended to compensate for steady-state changes, but its gains are not validated. If the output oscillates, overshoots excessively, or fails to recover, the controller needs tuning and the result should be reported as a limitation rather than presented as successful regulation. The model's fixed parasitic values also do not capture temperature dependence or manufacturing variation.

## 9. Efficiency method

Efficiency can be calculated from steady-state average input and output power:

\[
P_{in}=\operatorname{avg}(V_{in}I_{in}), \qquad P_{out}=\operatorname{avg}(V_{out}I_{out})
\]

\[
\eta=\frac{P_{out}}{P_{in}}\times100\%
\]

For the input voltage source, LTspice current polarity is conventionally positive into the source; use the negative of source current when calculating power delivered by the source. Report the averaging interval and ensure it excludes startup and the load transition. Efficiency results should not be claimed until input power is measured consistently.

## 10. Conclusion

The first-pass component calculations give a 100 µH inductor and 220 µF output capacitor for the selected 100 kHz, 12 V-to-5 V, 1 A design assumptions. A closed-loop behavioral PWM testbench has been added so the project can investigate line variation and a load step rather than relying only on a fixed duty ratio.

The engineering conclusions remain conditional until the netlist is run and results are entered. The completed study should state whether the 5 V target, ripple target, and transient requirements are met based on measured LTspice data. The behavioral controller is an educational model, not a substitute for a controller IC datasheet design or hardware validation.

## 11. Further work

- Run the netlist in LTspice and preserve the log and waveform screenshots.
- Complete the results table and revise this report with measured data.
- Tune the feedback controller and document the tuning method.
- Replace assumed diode, switch, inductor, and capacitor models with selected parts and datasheet parameters.
- Add efficiency and loss breakdown measurements.
- Investigate tolerances and temperature effects.
- If hardware is built, review PCB layout, component ratings, thermal behavior, and safe test procedures before applying power.

## References

1. LTspice Help, behavioral sources, switches, transient analysis, and `.meas` directives (consult the documentation installed with the LTspice version used).
2. Manufacturer datasheets for the actual diode, switch/MOSFET, inductor, and output capacitor used in any component-specific design (to be added when parts are selected).
3. The equations in this report are standard ideal buck-converter relationships; they are first-pass estimates and should be checked against a selected controller's datasheet and design guidance.
