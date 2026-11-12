# LTspice simulation procedure

This guide explains how to run the baseline buck-converter model and how to collect useful results.

## 1. Open the circuit

Open LTspice and load:

`ltspice/buck_converter.cir`

## 2. Check the node names

The output node is named `out2` in the current netlist. The default plot should therefore use:

- `V(out2)`
- `I(L1)`
- `V(sw)`

## 3. Run the transient analysis

The file already contains:

`.tran 0 5m 0 100n`

This will simulate 5 ms with a 100 ns maximum time step.

## 4. Plot the key signals

Plot the following:

- output voltage: `V(out2)`
- inductor current: `I(L1)`
- switch node: `V(sw)`

These plots will show:

- startup behaviour
- switching waveform
- current ripple
- output ripple
- settling behaviour

## 5. Measure ripple

Use LTspice measurement tools to determine:

- output ripple voltage: `PP` of `V(out2)`
- inductor current ripple: `PP` of `I(L1)`

Typical markers are:

- `V(out2)`
- `I(L1)`

## 6. Run a simple input sweep

The file includes commented sweep commands:

```
* .step param Vin list 10 12 14
* .step param Rload list 10 5
```

To test line variation, uncomment the first line and rerun. To test load variation, change the load and rerun.

## 7. Record the result for each condition

For each operating point, record:

- input voltage
- output voltage
- output ripple
- inductor current ripple
- load current

## 8. Observe transient response

If you want to test load-step behaviour, edit the load condition and measure how the output responds to the change. Typical quantities to record:

- maximum output voltage dip
- maximum overshoot
- settling time

## 9. Interpretation

The output should be near 5 V under nominal conditions. The exact result depends on:

- duty cycle
- ESR and DCR values
- diode forward drop
- switch resistance
- output capacitor value

This is a baseline simulation and does not yet contain a closed control loop.

## 10. Next step

For a fully regulated converter, the next stage is to add:

- output sensing
- reference voltage
- comparator or error amplifier
- PWM generation based on the error signal

This would permit regulation under changing load and input conditions.
