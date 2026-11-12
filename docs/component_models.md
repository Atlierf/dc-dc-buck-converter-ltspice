# Component Models and Verification

This document records the component models used in `ltspice/buck_converter_closed_loop.cir`, their ratings, model sources, and modeling simplifications.

> **Note on `ltspice/buck_converter.cir`:** The fixed-duty baseline netlist is preserved with its original assumed parameters (`DCR = 70 mΩ`, `ESR = 50 mΩ`, `SWMOD`, `Dsch`).

## Summary Table

| RefDes | Function | Part / Model | Status | Key Ratings | Project Requirement & Margin | Model Source |
|---|---|---|---|---|---|---|
| `S1` | High-side switch | `SWMOD` (idealized switch) | **Assumed** (not a manufacturer part) | `Ron = 30 mΩ`, `Roff = 1 MΩ`, `Vt = 2.5 V` | `14 V` max input, `~1.17 A` peak | Assumed behavioral switch in netlist |
| `D1` | Freewheel diode | **onsemi MBRS340** | **Verified model** | `VRRM = 40 V`, `IF(AV) = 3.0 A`, `IFSM = 80 A`, `VF ≈ 0.5 V @ 3 A` | `40 V` vs `14 V` max input (`2.86×`); `3 A` vs `1 A` max load | LTspice 17.2.4 `standard.dio` (`mfg=OnSemi`) & onsemi MBRS340 datasheet |
| `L1` / `Rind` | Power inductor | **Bourns SRR1260-101M** | **Datasheet-based** (`L` + max `DCR`) | `100 µH ±20%`, `DCR = 180 mΩ` max, `Isat = 1.70 A`, `Irms = 1.70 A` | `Isat = 1.70 A` vs `~1.17 A` peak (`+45%` margin); `Irms = 1.70 A` vs `~1.0 A` | Bourns SRR1260 series datasheet |
| `C1` / `Rcap` | Output capacitor | **Panasonic EEU-FR1C221** | **Datasheet-based** (`C` + max `ESR`) | `220 µF ±20%`, `16 VDC`, `ESR = 130 mΩ` max (`100 kHz, 20°C`), `455 mA rms` (`100 kHz, 105°C`) | `16 V` vs `5.14 V` max (`3.1×`); `455 mA` vs `~98 mA rms` ripple | Panasonic FR series (`EEU-FR1C221`) datasheet |

## Modeling Simplifications and Limitations

1. **High-side switch (`S1` — `SWMOD`):**
   - Kept as an assumed idealized voltage-controlled switch (`Ron = 30 mΩ`) because the testbench drives `S1` with a ground-referenced `0–5 V` PWM signal (`Bgate`) rather than a floating/bootstrap high-side MOSFET gate driver.
   - Switching losses, gate charge (`Qg`), Miller plateau, and body-diode effects of a real power MOSFET are not modeled.
2. **Freewheel Schottky diode (`D1` — `MBRS340`):**
   - Uses the exact SPICE `.model MBRS340` parameters from LTspice 17.2.4 `standard.dio`.
   - Package parasitics (lead inductance) and self-heating (`Tj` rise) are not modeled.
3. **Inductor (`L1` — `SRR1260-101M`):**
   - Uses the datasheet maximum `DCR = 180 mΩ` (worst-case DC resistance at `20°C`) in series with an ideal `100 µH` inductor.
   - Core losses (AC losses at `100 kHz`), inductance roll-off near saturation, self-resonant frequency (SRF), and tolerance (`±20%`) are not modeled.
4. **Output capacitor (`C1` — `EEU-FR1C221`):**
   - Uses the datasheet maximum `100 kHz, 20°C` impedance/ESR (`130 mΩ`) in series with an ideal `220 µF` capacitor.
   - Equivalent series inductance (ESL), frequency/temperature dependence of ESR, and capacitance tolerance (`±20%`) are not modeled.
