# Data and Provenance

## Required Local Layout

```text
Floormap_0.png
floormap_1.png
data/
|-- raw/
|   |-- pickup_export_20260718_113424.csv  # R1
|   |-- pickup_export_20260718_115144.csv  # R2
|   |-- pickup_export_20260719_151451.csv  # R3
|   `-- pickup_export_20260718_121428.csv  # R4
|-- metadata/
|   |-- beacon_locations.csv
|   |-- reference_points.csv
|   `-- map_config.json
`-- calibration/
    `-- step_length_training.csv         # Optional; needed for adaptive model
```

The original recordings and building assets are not distributed with this
repository. If you have authorized access to the project inputs, arrange them
in the layout above; no public download is provided.
Do not use the older `R1_old.csv`-`R4_old.csv` names as replacements for the
configured July 18-19 recordings.

| Run | Raw rows | Recording span | Intended protocol |
| --- | ---: | ---: | --- |
| R1 | 37,591 | 117.469 s | Ground-floor forward zig-zag |
| R2 | 34,021 | 57.589 s | Ground-floor reverse route |
| R3 | 69,756 | 110.737 s | Ground to first floor through stairs |
| R4 | 52,206 | 80.091 s | First-floor straight route and U-turn |

Spans are maximum minus minimum raw `timestamp_ms`; they are not walking-only
durations. Protocols come from the submission field notes. The old
`runs_metadata.csv` lists July 2 files and must not be treated as the manifest
for these recordings. It is not required by the main notebook computation.

## Schemas

Raw exports use these columns:

```text
timestamp_ms,timestamp_iso,source,id,cardinality,address,rssi,x,y,z,notes
```

IMU rows have `source=imu` and sensor IDs `accel`, `gyro`, `mag`, or
`imu_processed`. BLE rows use `beacon`, `ble_rssi`, or `live_ble_snapshot`.
Accepted beacon IDs match `arrive_emi` followed by digits. Preserve these IDs
consistently if replacing addresses with pseudonyms for an approved release.

Beacon metadata contains `beacon_id,address,x,y,floor,notes`; x/y are map pixels.
The PF uses a fallback transmit power when a tx-power field is absent.
Reference metadata contains
`run_id,ref_id,time_sec,timestamp_iso,location_name,x,y,floor,notes`.
The source reference file has 27 rows: 22 for R1-R4 and five for an unused S1
run. The configured pipeline evaluates only R1-R4.

Map JSON supplies `floors`, each with image dimensions and `walkable_polygons`,
plus `stairs.floor0_polygon` and `stairs.floor1_polygon`. Verify scales against
the PF constants described in [Architecture](ARCHITECTURE.md).

Calibration has 277 rows and columns:

```text
source_session,source_run,segment_index,weinberg_feature,cadence_hz,
dynamic_peak,abs_gyro_delta,abs_orientation_delta,gyro_activity,
dynamic_activity,target_horizontal_step_m
```

The calibration README says earlier-session bouts were matched in order to map
route segments; horizontal distance divided by event count supplies labels.
The staircase was excluded. This is derived supervision, not independent
per-step ground truth. Its label-generation process is described in prose, but
a standalone executable label-generation tool was not found in this package.

## Distribution

Raw logs include absolute timestamps, Bluetooth addresses, device identifiers
and free-text notes. Metadata and images describe a real university building.
Notebook outputs can embed these fields and images. The source notebooks also
contained author email addresses; the published notebook retains names without emails.

No dataset, map or project-code publication license was established in the
preparation record. The public repository excludes local raw data, metadata,
calibration files, floor plans, processed data and executed notebook outputs. Aggregate metrics may be reviewed
without disclosing raw traces, but still describe a team experiment. Obtain
team and institutional permissions before distributing any excluded assets.

No credentials were identified in the published files during the preparation
scan. This does not constitute a security audit of the private project archive.
