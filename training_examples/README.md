# Worked-example sources

The live examples use unmodified renders copied from
`../../tournament_v2/methods/{method}/{prompt_id}/{seed}/` relative to `html/`.
Each directory contains `rgb_0.png`, `rgb_30.png`, `rgb_60.png`, `rgb_90.png`,
and `style.png`. No image filters, cropping, or geometric distortion are applied.

| Directory | Criterion / side | Actual prompt | Style | Method | prompt_id | Seed |
|---|---|---|---|---|---|---|
| oq_boat_artifacts | Object Quality / A | A boat | wave | style_prompt | 14 | 9345 |
| oq_boat_good | Object Quality / B (better) | A boat | wave | g-style | 14 | 9345 |
| sa_car_pattern | Style Alignment / A (better) | A toy car | mosaic | SIC3D | 59 | 32511 |
| sa_car_no_pattern | Style Alignment / B | A toy car | mosaic | styleGS | 59 | 32511 |

Both prompt-style-seed combinations are outside the active 30-item pool.
Object labels are verified against stage-1 seed directories; tournament prompt
object labels are stale. Both pairs use the same prompt, style, seed, and views
within each pair. The worse style example is an actual output for the requested
style, not an output generated for another style.

The quality explanation highlights the large blue halos obscuring A's boat;
B is relatively better, not artifact-free. The style explanation compares
the coloured tile pattern in A with the mottled brown texture in B.
Previous cat/rabbit asset directories are retained as historical files and are
not referenced by the live examples.
