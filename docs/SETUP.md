# Setup and Execution

## Environment

The local verification environment has Python 3.13.2, NumPy 2.3.0, pandas 2.3.0,
Matplotlib 3.10.3, scikit-learn 1.7.0, nbconvert 7.16.6, nbformat 5.10.4 and
ipykernel 6.29.5 on Windows. The execution record identifies the actual kernel
used. Other operating systems and dependency combinations were not tested.

The source requirements allowed NumPy 1.24, but the code imports `numpy.strings`
and calls `numpy.trapezoid`; this repository requires NumPy 2. No implementation
change was needed to correct the dependency manifest.

Follow the environment commands in the [README](../README.md). Always use
`python -m pip` and register/select the environment's kernel explicitly. The
Windows system default currently resolves to a separate Python 3.14 installation
without scikit-learn; that interpreter is not the documented environment.

## Inputs and Working Directory

Supply authorized files in the layout in [Data](DATA.md). Every major stage uses
`Path.cwd()`. Start Jupyter in the repository root; starting in its parent can
create processed directories in the wrong location and fail to find inputs.
Both `Floormap_0.png` and lowercase `floormap_1.png` are used. Keep case exact
on case-sensitive file systems.

The calibration file is optional in the implementation. Omitting it switches
the model to physical Weinberg lengths and produces a different experiment.
For the documented adaptive result, provide the exact calibration table.
No external API, database, credential or environment-variable configuration is
required by the localization algorithm.

## Outputs

The notebook generates `data/processed/<run_id>/` with cleaned BLE and IMU CSVs,
`steps.csv`, `position_estimates.csv`, `particle_filter_positions.csv`,
`particle_filter_errors.csv`, transition events/diagnostics and
`particle_cloud.npz`. Per-floor position files are also written. Some values
also remain only in notebook globals; the combined error summary is not
persisted by the source code, despite a configured output path.

Run All overwrites these local outputs and removes stale floor-specific PF
position CSVs. Preserve a separate execution directory for comparisons.
The default seed is reset to 42 for each recording. To inspect another seed,
change it in a separate experiment copy and record the change.

The final animation cell has `GENERATE_PARTICLE_ANIMATIONS = False`. Enabling
it is optional and can take substantially longer. MP4 generation additionally
depends on a working Matplotlib animation writer such as FFmpeg; video encoding
was not validated during documentation preparation.

## Troubleshooting

- `FileNotFoundError`: check the current directory and exact input filenames.
- `No module named sklearn`: select the registered environment kernel.
- Missing NumPy `strings` or `trapezoid`: install NumPy 2 in that kernel.
- Jupyter ZMQ selector/permission warnings on Windows: inspect whether execution
  actually completed; these warnings alone did not establish a notebook failure.
- Missing floor checkpoints: inspect transition diagnostics and compare expected
  reference coverage; a smaller score table can indicate a failed transition.
- Unexpectedly large errors: check that raw filenames and checkpoint timing are
  from the same recording session before interpreting model performance.
