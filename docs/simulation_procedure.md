# LTspice Simulation Procedure

The repository contains two netlists:

- `ltspice/buck_converter.cir`: fixed-duty baseline power-stage model (`D = 0.417`, `12 V` input, `5 Ω` load).
- `ltspice/buck_converter_closed_loop.cir`: closed-loop PID/PWM testbench with an input-voltage sweep (`10 V`, `12 V`, `14 V`) and a `0.5 A` to `1.0 A` load step at `2 ms`. Use this file for regulation, transient, and efficiency studies.

## 1. Run the closed-loop study

1. Open `ltspice/buck_converter_closed_loop.cir` in LTspice (GUI: **File → Open...**, or CLI batch mode: `/Applications/LTspice.app/Contents/MacOS/LTspice -b ltspice/buck_converter_closed_loop.cir`).
2. Run the transient analysis (`.tran 0 5m 0 100n startup` with `.step param VIN_DC list 10 12 14`).
3. Plot `V(out)` (output voltage), `I(L1)` (inductor current), `V(sw)` (switch node), `V(duty)` (controller duty command), and `V(err)` (feedback error).
4. Inspect the `0.5 A → 1.0 A` load step at `t = 2 ms` (`Bload` switches from `V(out)/10` to `V(out)/5`).
5. Open **View → SPICE Error Log** (or convert the UTF-16LE `.log` file with `iconv -f UTF-16LE -t UTF-8`) to read all `.meas` outputs for each stepped `VIN_DC` (`10 V`, `12 V`, `14 V`).
6. Record the measured values in `docs/results_template.md`.

## 2. Measurement definitions in `ltspice/buck_converter_closed_loop.cir`

All measurements are repeated automatically for `VIN_DC = 10`, `12`, and `14`:

- **Pre-step average output (`VPRE`):** `AVG V(out)` from `1.5 ms` to `1.9 ms` (`10 Ω` load, `0.5 A` nominal at `5 V`).
- **Post-step average output (`VPOST`):** `AVG V(out)` from `4.0 ms` to `4.9 ms` (`5 Ω` load, `1.0 A` nominal at `5 V`).
- **Post-step output ripple (`VPP_POST`):** `PP V(out)` from `4.0 ms` to `4.9 ms`.
- **Post-step inductor-current ripple (`ILPP_POST`):** `PP I(L1)` from `4.0 ms` to `4.9 ms`.
- **Load-step minimum & maximum (`VMIN_STEP`, `VMAX_STEP`):** `MIN` and `MAX` of `V(out)` from `2.0 ms` to `4.99 ms` (avoids exact end-of-simulation boundary `5.0 ms`).
- **Settling time (`TSETTLE_MS`):** `(T_BAND_LAST - 2m) * 1000`, where `T_BAND_LAST` finds the last time `|V(out) - 5| = 0.1` for `t ≥ 2.0 ms` (`4.9 V` to `5.1 V`, `±2%` regulation band).
- **Input power, output power, and efficiency (`PIN_POST`, `POUT_POST`, `EFF_POST`):** Measured from `4.0 ms` to `4.9 ms`:
  - `PIN_POST = AVG(-V(in)*I(VBus))` — negated because LTspice defines `I(VBus)` positive *into* `VBus(+)`, so delivered power is `-V(in)*I(VBus)`.
  - `POUT_POST = AVG(V(out)*I(Bload))` — positive because `Bload out 0` current flows from `out` to ground.
  - `EFF_POST = 100*POUT_POST/PIN_POST` (%).

## 3. Interpret the model carefully

- The switching period is `10 µs` (`100 kHz`); the carrier ramps from `0` to `1` each period.
- The controller uses tuned behavioral PID parameters (`KP = 0.015`, `KI = 65`, `KD = 1.2` with a `Cdiff = 1n`, `Rdiff = 8k` band-limited derivative on `V(out)`).
- `D1` (`onsemi MBRS340`) uses LTspice's built-in `standard.dio` model; `L1` (`180 mΩ` max DCR) and `C1` (`130 mΩ` max ESR) use Bourns `SRR1260-101M` and Panasonic `EEU-FR1C221` datasheet maximums; `S1` (`SWMOD`, `30 mΩ`) remains an assumed idealized switch without gate-drive or switching-loss modeling. See `docs/component_models.md` for details.

## 4. Fixed-duty baseline

`ltspice/buck_converter.cir` is preserved for observing power-stage switching at a fixed duty cycle (`0.417`). It cannot demonstrate closed-loop regulation because it does not sense or correct output-voltage error.
