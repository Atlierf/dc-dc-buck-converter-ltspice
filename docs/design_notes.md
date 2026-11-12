# Design Notes — 12 V to 5 V Buck Converter

## Objective and modeled operating point

The project studies conversion from a 12 V nominal DC input to a 5 V target at up to 1 A. The closed-loop LTspice testbench separately steps the input through 10 V, 12 V, and 14 V. It changes the modeled load from 10 ohms (about 0.5 A at 5 V) to 5 ohms (about 1 A at 5 V) at 2 ms.

The main model uses a 100 kHz switching frequency, 100 µH inductance, 220 µF output capacitance, 180 mΩ inductor DCR, and 130 mΩ capacitor ESR. The latter values and model provenance are recorded in `component_models.md`.

## Symbol guide

| Symbol | Meaning | Unit |
|---|---|---|
| `Vin` | Input voltage | V |
| `Vout` | Output voltage | V |
| `D` | Switch duty cycle (fraction of each period that the switch is on) | unitless, 0 to 1 |
| `fs` | Switching frequency | Hz |
| `Ts` | Switching period, equal to `1 / fs` | s |
| `L` | Inductance | H |
| `C` | Capacitance | F |
| `ΔIL` | Peak-to-peak inductor-current ripple | A |
| `ΔVC` | Capacitive part of output-voltage ripple | V |
| `ESR` | Capacitor equivalent series resistance | ohm |

## How a buck converter works

The switch connects the input to the inductor for part of each switching cycle. When it turns off, the inductor current continues through the freewheel diode. The inductor and capacitor smooth the switched energy into a DC output.

For an ideal buck converter operating in continuous-conduction mode, the relationship is:

```text
Vout ≈ D × Vin
```

At 12 V input and 5 V output, the ideal duty cycle is:

```text
D ≈ Vout / Vin
  ≈ 5 V / 12 V
  ≈ 0.4167 (about 41.7%)
```

The closed-loop model changes duty in response to output error. Its behavioral PID values are `KP = 0.015`, `KI = 65`, and `KD = 1.2`. The separate baseline netlist uses a fixed duty cycle and is not a regulator.

## Inductor estimate

A first-pass buck-converter inductor-sizing equation is:

```text
L = ((Vin - Vout) × D) / (fs × ΔIL)
```

Using `Vin = 12 V`, `Vout = 5 V`, `D = 0.4167`, `fs = 100 kHz`, and a target ripple of `ΔIL = 0.3 A`:

```text
L ≈ ((12 V - 5 V) × 0.4167) / (100,000 Hz × 0.3 A)
  ≈ 97.2 µH
```

The selected model value is 100 µH. Its ideal nominal ripple estimate is approximately 0.292 A peak-to-peak. At 1 A average output current, estimated peak inductor current is:

```text
IL_peak ≈ Iout + (ΔIL / 2)
        ≈ 1 A + (0.292 A / 2)
        ≈ 1.146 A
```

This is below the documented 1.70 A inductor saturation-current rating, although the simulation does not model inductance roll-off near saturation.

## Output capacitor estimate

Ignoring ESR, the capacitive part of output ripple can be estimated by:

```text
ΔVC ≈ ΔIL / (8 × fs × C)
```

For `ΔIL = 0.3 A`, `fs = 100 kHz`, and `C = 220 µF`:

```text
ΔVC ≈ 0.3 A / (8 × 100,000 Hz × 220 µF)
    ≈ 1.7 mV peak-to-peak
```

A simple estimate of the ESR contribution is:

```text
ΔVESR ≈ ΔIL × ESR
      ≈ 0.3 A × 0.13 ohm
      ≈ 39 mV peak-to-peak
```

These are approximate estimates, not substitutes for measuring the switching waveform. The committed simulation reports post-step output ripple of 72.71 mVpp at 10 V input, 45.88 mVpp at 12 V, and 53.84 mVpp at 14 V.

## Non-ideal models and limits

The closed-loop testbench models the MBRS340 Schottky diode using the LTspice library model, finite switch on-resistance of 30 mΩ, inductor DCR of 180 mΩ, and output-capacitor ESR of 130 mΩ. The high-side switch is an idealized voltage-controlled switch, not a specific MOSFET. Gate-drive and switching losses, thermal rise, PCB parasitics, inductor core loss, ESR variation, and component tolerances are not fully represented. See `docs/component_models.md` for details.

## Recorded simulation outcomes

The committed run's post-transition average output values, measured from 4.0 ms to 4.9 ms, are within ±2% of 5 V. The output-ripple goal of less than 50 mVpp is met only at the 12 V input case. At 10 V and 14 V, ripple exceeds the target.

The 10 V and 14 V cases were outside the ±2% regulation band during the pre-transition measurement window (1.5 ms to 1.9 ms). Therefore, the transition at 2 ms is not a clean load-step test from an established steady-state operating point for those two cases. The reported settling-time values should be interpreted with this limitation.

## Power and efficiency equations

Average input and output power must be measured over the same steady-state interval:

```text
Pin  = average(-V(in) × I(VBus))
Pout = average( V(out) × I(Bload))
Efficiency (%) = 100 × Pout / Pin
```

The minus sign in the input-power equation follows the LTspice voltage-source current convention used by this netlist. The reported efficiencies are estimates from the behavioral simulation, not hardware efficiency measurements.
