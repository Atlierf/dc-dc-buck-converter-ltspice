# Results template for 12 V-to-5 V buck-converter project

Use this table when collecting LTspice simulation data.

| Test case | Vin (V) | Load (A) | Vout average (V) | Vout ripple (mVpp) | Inductor ripple (A) | Observation |
|---|---:|---:|---:|---:|---:|---|
| Nominal | 12 | 1.0 |  |  |  |  |
| Low input | 10 | 1.0 |  |  |  |  |
| High input | 14 | 1.0 |  |  |  |  |
| Light load | 12 | 0.5 |  |  |  |  |
| Full load | 12 | 1.0 |  |  |  |  |
| Load step up | 12 | 0.5 → 1.0 |  |  |  |  |
| Load step down | 12 | 1.0 → 0.5 |  |  |  |  |

## Suggested evaluation points

- Output stays close to 5 V at 12 V input
- Ripple magnitude is acceptable
- Inductor current remains continuous in normal operation
- Output does not severely overshoot or undershoot during load transients

## Example discussion points

- DCR reduces output voltage slightly
- ESR increases ripple
- Diode forward drop affects power efficiency
- Switch resistance adds conduction loss
- The fixed-duty power stage is not a fully compensated regulator
