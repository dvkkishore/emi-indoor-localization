# Experiments and Verified Results

## Snapshot

The selected implementation is
`Portfolio_3_Group_E_Adaptive_Regression/final_adaptive/Indoor_Localization_Report(5).ipynb`
in the original workspace. Its code was preserved exactly in the repository's
renamed notebook; presentation outputs and author emails were removed, and
documentation cells were corrected. See [validation evidence](validation_evidence.json)
for source, code and input SHA-256 hashes.

The preparation audit on **23 October 2026** executed all ten code cells in order
from a fresh processed-data state, using Python 3.13.2 and seed 42. Animation
generation remained disabled. Execution completed without a notebook error.
The local executed notebook and raw per-checkpoint outputs remain excluded from
Git because they embed building assets and experiment data.

## Protocol and Results

Four configured recordings cover forward/reverse ground-floor routes, a
ground-to-first-floor staircase route, and a first-floor U-turn. The code uses
1,000 particles, known Start position/floor, adaptive Ridge step length, IMU
heading fusion, BLE likelihood weighting and map endpoint constraints.

| Run | Matched / expected checkpoints | Mean error | RMSE | Resampling events |
| --- | ---: | ---: | ---: | ---: |
| R1 | 5 / 5 | 1.135 m | 1.743 m | 16 |
| R2 | 5 / 5 | 3.038 m | 3.275 m | 14 |
| R3 | 7 / 7 | 4.359 m | 6.044 m | 27 |
| R4 | 5 / 5 | 1.296 m | 1.452 m | 21 |
| Pooled | 22 / 22 | 2.630 m | 3.903 m | 78 |

These match the rounded seed-42 figures in the adaptive submission README.
The pooled result is checkpoint-weighted, not an unweighted mean of four run
means. Excluding the four known Starts gives **3.024 m over 18 checkpoints**.
R3 contributes 171 trajectory states (109 on floor 0 and 62 on floor 1) and one
detected transition; R1/R2/R4 have no detected transitions. All runs together
produce 418 trajectory states.

The maximum absolute checkpoint time gaps are R1 0.545 s, R2 1.707 s,
R3 1.981 s and R4 1.441 s. These gaps matter because matching selects the
nearest same-floor PF state and can select a later estimate.

## Interpreting the Score

Error uses anisotropic pixel conversion:

```text
error_m = sqrt((dx_px / 12.88)^2 + (dy_px / 18.01)^2)
```

This is sparse checkpoint localization error. It is not continuous trajectory
accuracy, a surveyed path benchmark, or a standalone floor-classification
metric. Starts are initialization inputs and are included by the default
summary. The source skips a reference if that floor has no estimates; always
report coverage alongside error. Matching by the expected floor also means a
matched row's floor agreement is not independent evidence of correct floor
estimation at the checkpoint time.

Parameters were developed against these routes. This rerun verifies
reproducibility, not unbiased held-out performance. No new five-seed sweep,
cross-building evaluation, participant split, real-time latency benchmark,
video-encoding check, or calibration-label validation was performed here.

## Historical Evidence Conflicts

The saved `Indoor_Localization_Report(5)_Executed.ipynb` in the source folder
does **not** execute the preferred raw-file mapping. Its loading cell substitutes
`R1_old.csv` through `R4_old.csv`. Its errors and `data/processed/` describe that
old-session stress test: approximately 32.92, 9.81, 22.06 and 18.49 m means,
with R3 missing the upper-floor reference rows. These outputs cannot support
the official-session result even though most code cells are otherwise identical.

The fresh run above resolves the source/output ambiguity without changing
algorithm code. Other workspace variants are historical alternatives, not
additional validated releases of this repository:

| Local folder | Interpretation |
| --- | --- |
| `Portfolio_3_Group_E/` | Earlier base submission |
| `Portfolio_3_Group_E_Generalized_Heading/` | Relative-heading variant |
| `Portfolio_3_Group_E_Adaptive_Regression/final_adaptive/` | Selected source |
| `Task_3/` | Earlier scripts, copies and development outputs |
| `Archive/`, `Others/` | Historical material outside this repository candidate |

The five-seed numbers in the selected source README were not rerun or
independently verified in this documentation pass. Earlier paper-guided trial
metrics used different implementations and reference conventions and must not
be combined with this result table.

## Repeating the Verification

Follow [Setup](SETUP.md) and provide the exact authorized [inputs](DATA.md).
Use the SHA-256 list in [validation evidence](validation_evidence.json) to check
identity before running. Execute the root notebook in a new environment and
fresh output directory, then read `particle_filter_errors.csv` for each run.
Calculate the pooled mean and RMSE from all checkpoint rows, and record the
number of expected and matched references. Retain the full executed notebook
locally with the package versions and seed.
