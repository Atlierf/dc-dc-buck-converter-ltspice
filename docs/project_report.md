# 12 V to 5 V Buck Converter: Design and LTspice Study

## Abstract

This project develops and characterizes a closed-loop buck-converter model for converting a nominal 12 V DC input (10–14 V range) to a 5 V output at up to 1 A. Component values are sized from switching frequency and inductor-ripple requirements, and the LTspice netlist incorporates a tuned behavioral PID/PWM controller, an onsemi `MBRS340` Schottky freewheel diode model, Bourns `SRR1260-101M` max DCR (`180 mΩ`), Panasonic `EEU-FR1C221` max 100 kHz ESR (`130 mΩ`), and an assumed `30 mΩ` idealized high-side switch.

**Status:** `ltspice/buck_converter_closed_loop.cir` was executed in LTspice 17.2.4 for MacOS across `10 V`, `12 V`, and `14 V` inputs with a `0.5 A → 1.0 A` load step at `2 ms`. The initial unverified PI gains (`KP=0.08`, `KI=500`) oscillated at `1.46–2.62 Vpp` due to the ~1.07 kHz LC resonance. Tuning to a band-limited PID (`KP=0.015`, `KI=65`, `KD=1.2` with `Cdiff=1n`, `Rdiff=8k`, ~19.9 kHz pole) stabilized all three input cases. Measured results, logs, and waveforms are saved in `docs/results_template.md` and `results/`.

## Project specification

| Quantity | Design target / model value |
|---|---:|
| Nominal input voltage | 12 V DC |
| Input test points | 10 V, 12 V, 14 V |
| Output voltage target | 5 V |
| Maximum output current | 1 A |
| Switching frequency | 100 kHz |
| Inductor ripple target at nominal input | 0.3 A peak-to-peak |
| Inductor (`Bourns SRR1260-101M`) | 100 µH, `180 mΩ` datasheet max DCR |
| Output capacitor (`Panasonic EEU-FR1C221`) | 220 µF, `130 mΩ` datasheet max ESR (100 kHz, 20°C) |
| Freewheel diode (`onsemi MBRS340`) | 40 V, 3 A Schottky (LTspice `standard.dio`) |
| High-side switch (`SWMOD`) | Assumed idealized switch (`30 mΩ` `Ron`) |
| Load-step test | 0.5 A to 1 A at 2 ms |
| Output-ripple goal | <50 mV peak-to-peak |

See `docs/component_models.md` for part ratings, model sources, and modeling simplifications.

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

A real component should have adequate saturation-current and thermal margin above this estimate. The selected Bourns `SRR1260-101M` (`100 µH`, `Isat = 1.70 A`, `Irms = 1.70 A`, `180 mΩ` max DCR) provides margin above the estimated `~1.15 A` peak current.

### 4.3 Output capacitor

Ignoring ESR, the approximate capacitive ripple is:

\[
\Delta V_C\approx\frac{\Delta I_L}{8f_sC}
\]

For 0.3 A ripple, 100 kHz, and 220 µF:

\[
\Delta V_C\approx\frac{0.3}{8(100\,000)(220\,\mu\text{F})}\approx1.7\,\text{mV}_{pp}
\]

This is only the ideal capacitive contribution. With the Panasonic `EEU-FR1C221` datasheet maximum 100 kHz ESR of `130 mΩ`, the simple ESR ripple estimate is:

\[
\Delta V_{ESR}\approx\Delta I_L\,ESR\approx0.3(0.13)=39\,\text{mV}_{pp}
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

- a parameterized DC input (`10 V`, `12 V`, `14 V` sweep) and `100 kHz` sawtooth timing reference
- a PWM comparator and bounded behavioral PID control command (`KP=0.015`, `KI=65`, `KD=1.2` with a `Cdiff=1n`, `Rdiff=8k` band-limited derivative on `V(out)` and anti-windup on `Cint`)
- an assumed idealized voltage-controlled high-side switch (`SWMOD`, `Ron=30 mΩ`, ground-referenced `0–5 V` drive without a bootstrap gate driver)
- an `onsemi MBRS340` Schottky freewheel diode model (`40 V`, `3 A` from LTspice 17.2.4 `standard.dio`)
- a `100 µH` inductor with `180 mΩ` series DCR (`Bourns SRR1260-101M` datasheet maximum)
- a `220 µF` output capacitor with `130 mΩ` ESR (`Panasonic EEU-FR1C221` datasheet maximum at `100 kHz, 20°C`)
- a `0.5 A`-to-`1.0 A` behavioral load step at `2 ms`
- `.meas` directives for pre/post-step average output, output and inductor ripple, load-step min/max, settling time (`±2%` band), and input/output power and efficiency

The behavioral controller and idealized switch `SWMOD` are educational abstractions; this model does not capture MOSFET switching/gate-drive losses, inductor core loss, or thermal effects.

## 6. Simulation procedure

1. Open `ltspice/buck_converter_closed_loop.cir` in LTspice (or run `/Applications/LTspice.app/Contents/MacOS/LTspice -b ltspice/buck_converter_closed_loop.cir`).
2. Run the transient analysis (`.tran 0 5m 0 100n startup`), which steps `VIN_DC` through `10 V`, `12 V`, and `14 V`.
3. Plot `V(out)`, `I(L1)`, `V(sw)`, and `V(duty)` (see `results/` for saved per-case overview and load-step close-up waveforms).
4. Review the `.meas` results in the LTspice error log (`results/buck_converter_closed_loop_10V_12V_14V.log.txt`), which report `VPRE`, `VPOST`, `VPP_POST`, `ILPP_POST`, `VMIN_STEP`, `VMAX_STEP`, `TSETTLE_MS`, `PIN_POST`, `POUT_POST`, and `EFF_POST`.
5. Record and verify all measured values in `docs/results_template.md` and `results/README.md`.

## 7. Results

### 7.1 Status of results

`ltspice/buck_converter_closed_loop.cir` was executed in LTspice 17.2.4 for MacOS across all three input steps (`10 V`, `12 V`, `14 V`). The initial PI gains (`KP=0.08`, `KI=500`) caused sustained oscillation (`1.456 Vpp` at 10 V, `1.695 Vpp` at 12 V, `2.623 Vpp` at 14 V). After tuning to `KP=0.015`, `KI=65`, and a band-limited derivative `KD=1.2` (`Cdiff=1n`, `Rdiff=8k`), all three cases stabilized without duty saturation (`V(duty)` post-step ranges for `t ≥ 4 ms`: `0.526–0.569` at 10 V, `0.447–0.494` at 12 V, `0.387–0.438` at 14 V).

### 7.2 Steady-state regulation

**Result:** Post-step average `V(out)` (`4.0–4.9 ms`, with `MBRS340`, `DCR = 180 mΩ`, `CESR = 130 mΩ`):
- `10 V` input: `4.9269 V` (`-1.46%` error; still approaching `5.0 V` at `4.9323 V` by `5 ms`)
- `12 V` input: `4.9887 V` (`-0.23%` error)
- `14 V` input: `5.0144 V` (`+0.29%` error)

### 7.3 Output and inductor ripple

**Result:** Post-step peak-to-peak ripple (`4.0–4.9 ms`):
- `10 V` input: `72.71 mVpp` output ripple (includes slow settling drift), `0.2597 App` inductor ripple
- `12 V` input: `45.88 mVpp` output ripple (`< 50 mVpp` target), `0.3049 App` inductor ripple
- `14 V` input: `53.84 mVpp` output ripple (reflects the `130 mΩ` datasheet max ESR), `0.3401 App` inductor ripple

### 7.4 Load transient

**Result:** After the `0.5 A → 1.0 A` load step at `2 ms` (using a `4.9–5.1 V` regulation band):
- `10 V` input: minimum `4.5814 V`, maximum `4.9644 V`, settling time `2.200 ms`
- `12 V` input: minimum `4.8149 V`, maximum `5.0116 V`, settling time `0.460 ms`
- `14 V` input: minimum `4.9661 V`, maximum `5.1436 V`, settling time `0.034 ms`

### 7.5 Input-voltage variation

**Result:** Across the `10 V → 14 V` line sweep, post-step average `V(out)` varies from `4.9269 V` to `5.0144 V` (`87.5 mV` total spread, `1.75%` of `5 V`), with the controller adjusting duty cycle (`t ≥ 4 ms`) across `0.526–0.569` (10 V), `0.447–0.494` (12 V), and `0.387–0.438` (14 V). See `docs/component_models.md` for verified part ratings and model simplifications.

## 8. Discussion of non-ideal behavior and model limitations

Inductor DCR (`180 mΩ` max), `SWMOD` on-resistance (`30 mΩ`), and `MBRS340` forward drop cause conduction losses that require higher steady-state duty ratios (`~0.54` at `10 V`, `~0.46` at `12 V`, `~0.40` at `14 V`) than the ideal `Vout / Vin` ratio (`0.50`, `0.417`, `0.357`).

The `100 µH` / `220 µF` LC filter has an undamped resonant frequency near `1.07 kHz` (`-180°` phase shift above resonance). With the initial PI gains (`KP=0.08`, `KI=500`), the loop crossed over in the `-180°` region without phase boost, producing `1.46–2.62 Vpp` oscillation. Adding the band-limited derivative (`KD=1.2`, `Cdiff=1n`, `Rdiff=8k`) stabilized the loop across `10–14 V`, at the cost of slower integral recovery at `10 V` (`VPOST = 4.9269 V`, `TSETTLE_MS = 2.200 ms`).

Two ripple targets were **not met** under the `130 mΩ` datasheet max ESR of the `EEU-FR1C221`: `14 V` (`53.84 mVpp`) slightly exceeds `50 mVpp` due to `ΔIL × ESR` (`0.3401 App × 0.13 Ω ≈ 44.2 mVpp` plus capacitive ripple), and `10 V` (`72.71 mVpp`) includes residual settling drift during `4.0–4.9 ms`. Key model limitations remain: `S1` is an assumed ground-referenced `30 mΩ` switch without gate-drive or switching losses, and `L1`/`C1` omit core loss, saturation nonlinearity, ESL, and temperature/tolerance variation.

## 9. Efficiency analysis

Efficiency was measured in LTspice over the post-step window (`4.0–4.9 ms`, `5 Ω` load) using `PIN_POST = AVG(-V(in)*I(VBus))` (negated because LTspice defines `I(VBus)` positive into `VBus(+)`) and `POUT_POST = AVG(V(out)*I(Bload))`:

- `10 V` input: `PIN_POST = 5.2651 W`, `POUT_POST = 4.8550 W`, `EFF_POST = 92.21%`
- `12 V` input: `PIN_POST = 5.3766 W`, `POUT_POST = 4.9774 W`, `EFF_POST = 92.58%`
- `14 V` input: `PIN_POST = 5.4259 W`, `POUT_POST = 5.0288 W`, `EFF_POST = 92.68%`

These values reflect conduction losses of `SWMOD`, `MBRS340`, and `DCR = 180 mΩ` only; real hardware efficiency will be lower once MOSFET switching, gate-drive, and inductor core losses are included.

## 10. Conclusion

The tuned closed-loop simulation (`ltspice/buck_converter_closed_loop.cir`) stabilizes the `12 V`-to-`5 V` buck converter across `10 V`, `12 V`, and `14 V` inputs and a `0.5 A → 1.0 A` load step without duty saturation:

- **5 V output regulation:** **Met** within `±1.5%` post-step (`4.9269 V` at `10 V`, `4.9887 V` at `12 V`, `5.0144 V` at `14 V`).
- **< 50 mVpp output ripple:** **Met** at `12 V` nominal (`45.88 mVpp`), **not met** at `14 V` (`53.84 mVpp`) or `10 V` (`72.71 mVpp`).
- **Operation up to 1 A (`10–14 V`):** **Met in simulation** (`92.21%–92.68%` simulated efficiency; `SRR1260-101M` and `MBRS340` ratings have margin), while real MOSFET gate-drive, switching loss, and thermal behavior remain **not yet verified**.

## 11. Further work

- Lower the output-capacitor ESR (e.g., parallel ceramic capacitor or lower-ESR polymer/electrolytic part) to bring `14 V` ripple below `50 mVpp`.
- Replace the assumed `SWMOD` switch and behavioral PID with a manufacturer-specific MOSFET, floating/bootstrap high-side gate driver, and controller IC model.
- Investigate component tolerances (`±20%` on `L` and `C`) and temperature effects on ESR/DCR.
- Validate thermal behavior, PCB layout, and efficiency on hardware.

## References

1. LTspice 17.2.4 for MacOS built-in component library `standard.dio` (`MBRS340` SPICE model, `mfg=OnSemi`).
2. onsemi `MBRS340` Schottky Power Rectifier datasheet (`40 V`, `3.0 A`).
3. Bourns `SRR1260` series shielded power inductor datasheet (`SRR1260-101M`, `100 µH`, `180 mΩ` max DCR, `Isat = 1.70 A`).
4. Panasonic `FR` series aluminum electrolytic capacitor datasheet (`EEU-FR1C221`, `220 µF`, `16 VDC`, `130 mΩ` max impedance at `100 kHz, 20°C`).
5. Repository documentation: `docs/component_models.md`, `docs/results_template.md`, `docs/simulation_procedure.md`, and `results/README.md`.
