# Documentation Audit

Prepared 23 October 2026 from the actual local workspace. This is a publication
candidate, with unresolved code and asset distribution rights.

## Files and Scope

- `README.md`: entry point, workflow, actual commands, structure and limits.
- `Indoor_Localization_Report.ipynb`: renamed preferred adaptive notebook;
  all implementation cells preserved, outputs cleared, selected narrative
  corrected and author emails removed.
- `requirements.txt`: explicit execution dependencies; NumPy floor raised to 2
  because the implementation uses NumPy 2 APIs.
- `.gitignore`: excludes local raw inputs, metadata, calibration, maps,
  processed outputs, executed notebooks, caches, archives and secrets.
- `CONTRIBUTORS.md`: team attribution, research sources, unresolved licensing.
- `data/README.md`: explains required but excluded local inputs.
- `docs/ARCHITECTURE.md`, `SETUP.md`, `DATA.md`, `EXPERIMENTS.md`: execution,
  design rationale, provenance, evaluation evidence and limitations.
- `docs/validation_evidence.json`: aggregate execution evidence and input hashes.

The preparation scripts and validation copies are kept outside the proposed
Git content or under ignored `.local-validation/`. No implementation refactor,
new algorithm or new scientific benchmark was introduced.

## Checks

The preferred source was compared with the existing executed notebook and
processed files. A fresh isolated run reproduced the preferred README's
seed-42 values. All ten code cells executed sequentially without errors.
Code-cell source equality, unchanged source-input hashes, cleared public
outputs, finite errors, chronological trajectories and local Markdown links
were checked. Git's proposed file list and ignored validation data are reviewed
before any publication.

The targeted candidate scan checks common token/private-key patterns, absolute
home paths, email addresses and MAC-address literals without printing matches.
It is a preparation check, not a security certification of historical archives.
The original repository has unrelated modifications, which are preserved.

## Unresolved Information

1. Team approval and a project-code license have not been established. No
   `LICENSE` file has been invented.
2. Institutional permission to publish recordings, maps, beacon positions and
   calibration data has not been established. These assets remain excluded.
3. Device descriptions conflict between notebook and field notes. Confirm the
   acquisition log before documenting precise phone models for R1/R2/R4.
4. The source `runs_metadata.csv` describes July 2 exports rather than the
   configured July 18-19 recordings. Use actual filenames and hashes.
5. Scalar map scales and the PF's anisotropic conversion disagree. Physical
   calibration remains an unresolved measurement issue.
6. Calibration labels have prose provenance but no standalone reproducible
   label-generation tool in this package.
7. There is no existing unit-test suite, held-out validation set, packaged
   application, latency benchmark or recorded video-writer verification.

## Suggested GitHub Metadata

Repository name: `emi-indoor-localization`

Description: `Offline smartphone indoor localization using IMU pedestrian dead reckoning, BLE RSSI, and a map-constrained particle filter.`

Topics: `indoor-localization`, `sensor-fusion`, `particle-filter`, `pedestrian-dead-reckoning`,
`bluetooth-low-energy`, `imu`, `python`, `jupyter-notebook`.

Use a separate preparation branch for review. The connected GitHub account is
`dvkkishore`; no existing EMI repository was found in its returned repository
list before preparation. A private `dvkkishore/emi-indoor-localization`
repository was created for review. The existing workspace `origin` is the university GitLab repository and
must not be replaced with GitHub. A draft PR requires a created destination
repository and base branch. Public distribution remains a separate decision
until the rights above are resolved.

## Future Documentation

After permissions are established, add approved floor-specific output images,
a downloadable versioned input bundle with a data license, a reproducible
calibration-label generator, and a team-confirmed contribution record. Record
these as future work rather than completed repository features.
