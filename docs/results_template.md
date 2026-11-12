# Results Template

Values below are from an actual **LTspice 17.2.4 for MacOS** run of `ltspice/buck_converter_closed_loop.cir` (`.tran 0 5m 0 100n startup`, `100 kHz`, `L = 100 µH`, `DCR = 180 mΩ`, `C = 220 µF`, `ESR = 130 mΩ`, `D1 = onsemi MBRS340`, `S1 = SWMOD` assumed `30 mΩ`). See `docs/evidence/buck_converter_closed_loop.log.txt` and `docs/evidence/closed_loop_waveforms.svg`.

## 1. Regulation, Ripple, and Transient Results

**Test conditions & measurement windows:**
- **Pre-step steady state (`VPRE`):** `1.5 ms` to `1.9 ms` (`10 Ω` behavioral load, `0.5 A` nominal at `5 V`)
- **Post-step steady state (`VPOST`, `VPP_POST`, `ILPP_POST`):** `4.0 ms` to `4.9 ms` (`5 Ω` behavioral load, `1.0 A` nominal at `5 V`)
- **Load-step excursion (`VMIN_STEP`, `VMAX_STEP`):** `2.0 ms` to `4.99 ms` after `0.5 A → 1.0 A` load step at `2.0 ms`
- **Settling time (`TSETTLE_MS`):** `(T_BAND_LAST - 2 ms) × 1000`, where `T_BAND_LAST` is the last crossing of `|V(out) - 5 V| = 0.1 V` (`4.9 V` to `5.1 V`, `±2%` band) for `t ≥ 2.0 ms`

| Input (V) | Pre-step Vout avg (V) | Post-step Vout avg (V) | Post-step Vout ripple (mVpp) | Post-step IL ripple (App) | Load-step min (V) | Load-step max (V) | Settling time (ms) | Meets target? |
|---:|---:|---:|---:|---:|---:|---:|---:|---|
| 10 | 4.7137 | 4.9269 | 72.71 | 0.2597 | 4.5814 | 4.9644 | 2.200 | Ripple > 50 mVpp (slow recovery from startup/step) |
| 12 | 5.0228 | 4.9887 | 45.88 | 0.3049 | 4.8149 | 5.0116 | 0.460 | Yes |
| 14 | 5.2457 | 5.0144 | 53.84 | 0.3401 | 4.9661 | 5.1436 | 0.034 | Ripple slightly > 50 mVpp (`130 mΩ` max ESR) |

## 2. Input Power, Output Power, and Efficiency (Post-Step `4.0 ms` to `4.9 ms`, `5 Ω` Load)

- **Input power (`PIN_POST`):** `AVG(-V(in)*I(VBus))` (negative sign accounts for LTspice convention where `I(VBus)` is positive into `VBus(+)`)
- **Output power (`POUT_POST`):** `AVG(V(out)*I(Bload))` (`I(Bload)` flows from `out` to `0`)
- **Efficiency (`EFF_POST`):** `100 × POUT_POST / PIN_POST`

| Input (V) | Input power `PIN_POST` (W) | Output power `POUT_POST` (W) | Efficiency `EFF_POST` (%) |
|---:|---:|---:|---:|
| 10 | 5.2651 | 4.8550 | 92.21 |
| 12 | 5.3766 | 4.9774 | 92.58 |
| 14 | 5.4259 | 5.0288 | 92.68 |

## 3. Other Records

- LTspice version: LTspice 17.2.4 for MacOS
- Netlist filename / revision: `ltspice/buck_converter_closed_loop.cir` (PID + `MBRS340`, `DCR=180m`, `CESR=130m`)
- Regulation band used for settling time: `4.9 V` to `5.1 V` (`±2%` around `5 V`, measured from `t = 2 ms`)
- Controller gains tested: Initial `KP=0.08, KI=500` (oscillated `1.46–2.62 Vpp`); tuned `KP=0.015, KI=65, KD=1.2` with `Cdiff=1n, Rdiff=8k`
- Model limitations or convergence notes: Pseudo-transient `.op` succeeded; `D1` uses LTspice `MBRS340`, `L1`/`C1` use Bourns `SRR1260-101M` / Panasonic `EEU-FR1C221` datasheet max `DCR`/`ESR`, `S1` remains an assumed idealized switch (`30 mΩ`) without gate-drive or switching-loss modeling (see `docs/component_models.md`)
- Waveform and error-log filenames: `docs/evidence/closed_loop_waveforms.svg`, `docs/evidence/buck_converter_closed_loop.log.txt`

## 4. Interpretation Checklist

- Is post-step average output close to 5 V at every input point? Yes (`4.9269 V` to `5.0144 V`, within `1.5%` of `5 V`).
- Is output ripple below the 50 mVpp project target? At `12 V` nominal (`45.88 mVpp`), yes; at `14 V` (`53.84 mVpp`) and `10 V` (`72.71 mVpp`), it exceeds `50 mVpp` due to the `130 mΩ` datasheet max ESR and slow `10 V` settling.
- Does the output recover from the load increase without sustained oscillation? Yes, all three input cases settle without sustained oscillation.
- Is inductor peak current within the selected inductor's rating? Yes (`SRR1260-101M` `Isat = 1.70 A` vs `~1.17 A` peak).
- Are results consistent with the controller and component model assumptions? Yes, efficiency (`92.2%–92.7%`) reflects conduction losses of `SWMOD` (`30 mΩ`), `MBRS340`, and `DCR = 180 mΩ` without MOSFET switching/gate-drive or inductor core losses.
