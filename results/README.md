# Simulation Results (`LTspice 17.2.4 for MacOS`)

All waveform plots and log data in this folder are from an actual batch run of `ltspice/buck_converter_closed_loop.cir` (`.tran 0 5m 0 100n startup`, `100 kHz`, `L1 = 100 µH`, `DCR = 180 mΩ`, `C1 = 220 µF`, `ESR = 130 mΩ`, `D1 = onsemi MBRS340`, `S1 = SWMOD` assumed `30 mΩ`).

*(Note: Waveform SVGs are rendered directly from LTspice's binary `.raw` output rather than manual GUI screenshots.)*

## Files

- `buck_converter_closed_loop_10V_12V_14V.log.txt` — UTF-8 LTspice SPICE error log with `.meas` outputs for `10 V`, `12 V`, and `14 V`
- `vin10v_0.5A_to_1A_overview.svg` — `Vin = 10 V`, `0–5 ms` full transient (`V(out)`, `I(L1)`)
- `vin10v_load_step_closeup.svg` — `Vin = 10 V`, `1.9–3.2 ms` load-step close-up (`0.5 A → 1.0 A` at `2 ms`)
- `vin12v_0.5A_to_1A_overview.svg` — `Vin = 12 V`, `0–5 ms` full transient (`V(out)`, `I(L1)`)
- `vin12v_load_step_closeup.svg` — `Vin = 12 V`, `1.9–3.2 ms` load-step close-up (`0.5 A → 1.0 A` at `2 ms`)
- `vin14v_0.5A_to_1A_overview.svg` — `Vin = 14 V`, `0–5 ms` full transient (`V(out)`, `I(L1)`)
- `vin14v_load_step_closeup.svg` — `Vin = 14 V`, `1.9–3.2 ms` load-step close-up (`0.5 A → 1.0 A` at `2 ms`)

## Requirements Compliance Matrix

| Requirement | Condition | Measured Value (from `.log.txt`) | Status | Evidence / Notes |
|---|---|---|---|---|
| **5 V output regulation** | `10 V` input, `4.0–4.9 ms` (`1 A`) | `VPOST = 4.9269 V` (`-1.46%`) | **Met** (within `±2%`) | `VPRE = 4.7137 V`; still rising slowly (`4.9323 V` at `5 ms`) |
| **5 V output regulation** | `12 V` input, `4.0–4.9 ms` (`1 A`) | `VPOST = 4.9887 V` (`-0.23%`) | **Met** | `VPRE = 5.0228 V`; `TSETTLE_MS = 0.460 ms` |
| **5 V output regulation** | `14 V` input, `4.0–4.9 ms` (`1 A`) | `VPOST = 5.0144 V` (`+0.29%`) | **Met** | `VPRE = 5.2457 V`; `TSETTLE_MS = 0.034 ms` |
| **< 50 mVpp output ripple** | `10 V` input, `4.0–4.9 ms` (`1 A`) | `VPP_POST = 72.71 mVpp` | **Not met** | Exceeds `50 mVpp` due to slow recovery/drift + `130 mΩ` ESR |
| **< 50 mVpp output ripple** | `12 V` input, `4.0–4.9 ms` (`1 A`) | `VPP_POST = 45.88 mVpp` | **Met** | Below `50 mVpp` target |
| **< 50 mVpp output ripple** | `14 V` input, `4.0–4.9 ms` (`1 A`) | `VPP_POST = 53.84 mVpp` | **Not met** | Exceeds `50 mVpp` due to `130 mΩ` max ESR (`ΔIL = 0.3401 App`) |
| **Operation up to 1 A (`10–14 V`)** | `10 V, 12 V, 14 V`, `t ≥ 2 ms` | Stable `5 Ω` (`~1 A`) load; `EFF_POST = 92.21%–92.68%` | **Met (simulation)** | No oscillation or duty saturation (`D = 0.387–0.569`); `SRR1260-101M` `Isat = 1.70 A`, `MBRS340` `IF(AV) = 3.0 A` |
| **MOSFET gate-drive & thermal limits** | Real switching hardware | `S1` is an assumed `30 mΩ` ideal switch | **Not yet verified** | Real MOSFET switching losses, gate driver, and thermal rise require a datasheet MOSFET model or hardware test |
