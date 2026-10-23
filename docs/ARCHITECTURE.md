# Architecture

## Execution Model

The implementation is one Jupyter notebook with ten code cells. Run it from the
repository root in order. Stages share `GLOBAL_IMU_DATA`, `GLOBAL_BLE_DATA`,
`GLOBAL_STEPS_DATA`, `GLOBAL_PDR_DATA`, `GLOBAL_PF_POSITIONS`,
`GLOBAL_PF_CLOUD` and `GLOBAL_PF_ERRORS`; later stages also reload generated
CSVs. There is no imported project package or service API.

| Notebook section | Responsibilities | Important functions |
| --- | --- | --- |
| Data loading | Parse, clean, deduplicate, separate streams | `generate_run`, `extract_arrive_beacon_id` |
| Steps and PDR | Detect peaks, estimate step length, integrate gyro, accumulate relative motion | `detect_steps`, `apply_generalized_heading`, `fit_step_length_regressor`, `write_positions` |
| Sensor plots | Plot acceleration, step inputs and RSSI | Notebook plotting cells |
| Map visualization | Display walkable and stair polygons | Map plotting cell |
| Particle filter | Initialize, predict, weight, constrain, transition, resample | `run_particle_filter`, `run_autonomous_particle_filter` |
| Evaluation | Match references and summarize checkpoint errors | `calculate_errors`, `calculate_overall_errors` |
| Visualization | Separate trajectories by floor, optionally animate clouds | Final plotting and animation cells |

Section numbers follow notebook headings. Source cell numbers are recorded in
the local audit evidence, but should not be used as a stable public API.

## Motion and Learning

Step detection applies a centered moving-average baseline to acceleration
magnitude, an adaptive peak threshold and a 0.35-second minimum event spacing.
The physical distance estimate uses a Weinberg coefficient of 0.70. A
standardized Ridge model (`alpha=10`) predicts horizontal event distance from
seven acceleration, cadence, gyro and orientation features. The result is
75% physical estimate and 25% regression prediction, bounded to 0.35-1.35 m.
If the calibration file is absent or has fewer than 20 usable rows, the code
uses the physical estimate.

Yaw is represented relative to its first filtered value. Gravity-projected gyro
is integrated with actual timestamps and corrects supported large turns.
Map-heading alignment is a particle state initialized from candidate directions
around the known Start. A per-recording logistic model uses sensor-derived
pseudo-labels; its probability is diagnostic and does not replace the physical
heading decision.

## Particle Filter

Each particle stores metric position and map-heading alignment. Defaults are
1,000 particles, seed 42, 0.2 m step noise and heading noise that varies with
motion state. The PF retains up to two nearby beacons and applies their
power-weighted Gaussian RSSI likelihoods every second step. The path-loss
exponent is 2 and RSSI standard deviation is 8 dB, with -59 dBm fallback
transmit power.

Endpoint map validity sets invalid particle weights to zero. If a prediction
loses all valid particles, the previous state is restored. A weighted mean
outside valid geometry falls back to a valid particle. Systematic resampling
occurs when `N_eff < 0.5 * N`. This is endpoint containment, not continuous
collision detection along a step.

The transition detector identifies pause-bounded movement bouts from the
recording, combines IMU features with floor-specific BLE evidence, and proposes
stair entry/exit. The PF conditions on stair geometry and releases the cloud
near the destination exit. Reference transition times do not drive this
detector. Its full-recording statistics and post-bout BLE windows make it offline.

## Coordinates and Time

Images use top-left origin with x right and y down. The PF uses:

```text
x_m = x_px / 12.88
y_m = (490 - y_px) / 18.01
```

The inverse conversion is used for map checks and plotting. The constants imply
an anisotropic scale; a scalar `meters_per_pixel` in map JSON does not control
these PF conversions. Physical accuracy therefore depends on confirming this
calibration.

Cleaning subtracts the first raw-row timestamp. Evaluation additionally
subtracts the first cleaned IMU `time_sec`. The nearest estimate on the
reference's floor is selected, without a maximum time gap. Starts are included,
and references for floors never estimated are skipped. Review per-checkpoint
`time_error_sec` and coverage before interpreting the mean.

## Extending the Project

Add new recordings by updating the loading cell's `RUN_FILES`, the motion/PF
`RUN_IDS`, reference inputs and map/beacon metadata consistently. A new building
also needs coordinate-scale verification and new walkable/stair polygons.
Preserve a source snapshot and record input hashes, parameter choices, kernel
versions and checkpoint coverage with each experiment. Changes to timestamp
alignment, calibration labels or floor matching require fresh evaluation rather
than comparisons with an old CSV summary.
