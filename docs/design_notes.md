# Design Notes — 12 V to 5 V Buck Converter

## Objective and modeled operating point

The project studies conversion from a 12 V nominal DC input to a 5 V target at up to 1 A. The closed-loop LTspice testbench separately steps the input parameter through 10 V, 12 V, and 14 V. It switches the modeled load from 10 Ω (0.5 A nominal at 5 V) to 5 Ω (1 A nominal at 5 V) at 2 ms.

The main model uses 100 kHz switching, 100 µH inductance, 220 µF output capacitance, 180 mΩ inductor DCR, and 130 mΩ capacitor ESR. The latter values and model provenance are recorded in `component_models.md`.

## How a buck converter works

The switch connects the input to the inductor for part of each cycle. When it turns off, the inductor current continues through the freewheel diode. The inductor and capacitor smooth the switched energy into a DC output.

For an ideal buck converter in continuous conduction:

\[
V_{out}\approx D V_{in}
\]

At 12 V input and 5 V output, the ideal duty ratio is:

\[
D\approx\frac{5}{12}=0.4167
\]

The closed-loop model changes duty in response to output error; its behavioral PID values are `KP=0.015`, `KI=65`, and `KD=1.2`. The separate baseline netlist uses a fixed duty ratio and is not a regulator.

## Inductor estimate

For a buck converter, an approximate inductor sizing relation is:

\[
L=\frac{(V_{in}-V_{out})D}{f_s\Delta I_L}
\]

Using 12 V, 5 V, 100 kHz, and a 0.3 A peak-to-peak ripple target gives:

\[
L\approx\frac{(12-5)(0.4167)}{100\,000(0.3)}\approx97.2\,\mu H
\]

The selected model value is 100 µH. Its ideal nominal ripple estimate is approximately 0.292 App. At 1 A average output current, estimated peak inductor current is about 1.146 A. This is below the documented 1.70 A inductor saturation-current rating, though the model does not simulate inductance roll-off near saturation.

## Output capacitor estimate

Ignoring ESR, the capacitive ripple estimate is:

\[
\Delta V_C\approx\frac{\Delta I_L}{8f_sC}
\]

For 0.3 A, 100 kHz, and 220 µF, this is about 1.7 mVpp. The simple ESR contribution at 130 mΩ is approximately:

\[
\Delta V_{ESR}\approx\Delta I_L\times ESR\approx0.3(0.13)=39\,mV_{pp}
\]

These estimates do not replace measurements of the switching waveform. In the committed simulation, measured post-step output ripple was 72.71 mVpp at 10 V, 45.88 mVpp at 12 V, and 53.84 mVpp at 14 V.

## Non-ideal models and limits

The closed-loop testbench models the MBRS340 Schottky diode using the LTspice library model, finite switch on-resistance of 30 mΩ, inductor DCR of 180 mΩ, and output-capacitor ESR of 130 mΩ. The switch is not a real MOSFET model and is driven by a ground-referenced behavioral PWM source. Switching losses, gate charge, thermal rise, PCB parasitics, inductor core loss, ESR variation, and component tolerances are not fully represented. Refer to `docs/component_models.md` before interpreting results as representative of hardware.

## Recorded simulation outcomes

The committed run's post-step average output values are within ±2% of 5 V in the 4.0–4.9 ms measurement window. The ripple goal of <50 mVpp is met only at the 12 V input case. At 10 V and 14 V the measured ripple exceeds the goal.

The 10 V and 14 V cases were still outside the ±2% regulation band in the 1.5–1.9 ms pre-step window. Consequently, the 2 ms transition is not a clean load-step test from established steady state for those cases. Repeat the test with a longer pre-step settling interval before claiming load-step performance.

## Evaluation equations

For power and efficiency, average input and output power over the same steady-state interval:

\[
P_{in}=\operatorname{avg}(-V(in)I(VBus)),\quad
P_{out}=\operatorname{avg}(V(out)I(Bload)),\quad
\eta=100\frac{P_{out}}{P_{in}}\%
\]

The negative sign for input power follows the LTspice voltage-source current convention in this netlist. The reported efficiencies are model-dependent estimates, not measured hardware efficiencies.
