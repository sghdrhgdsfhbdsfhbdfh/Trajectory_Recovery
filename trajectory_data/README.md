# CID-remapped release

All CI/CID observation tokens in the calibration CSV, trajectory XLSX files, and map HTML files have been replaced consistently with integers 1 through 911. Original CID values were sorted numerically in ascending order before assigning these integers. Equal original CI values remain pooled, including across LAC values. The mapping table is maintained separately and is not included in this release directory. Coordinates, LAC, timestamps, road graph, filenames, and trajectory splits are unchanged. This version performs CID pseudonymization only and does not anonymize location. References to raw CI below describe the source data; released CI fields contain remapped tokens.

---

# Region A Cellular Trajectory Dataset

## Overview

This package contains the Region A data used in the paper *Tower-Coordinate-Free Road-Level Trajectory Recovery from Cell-ID Sequences: Device-Side Calibration and Policy-Guided Constrained Decoding* and its supplementary material.

Region A is an urban road-network study area. The experimental protocol separates field acquisition, policy-training data generation, online recovery, and evaluation. The package therefore contains three data roles that must not be conflated:

1. **Field-collected regional calibration data** used to construct the road--Cell-ID evidence artifact.
2. **1,000 generated policy-training trajectories** constructed offline from the frozen road graph and regional calibration data.
3. **250 held-out real vehicle test trajectories** used only for final evaluation.

The method is tower-coordinate-free, not GPS-free. Regional calibration and reference construction use GPS recorded by a researcher-controlled probe device, but no carrier-provided tower-coordinate inventory or operator-maintained tower registry is used.

## Region A collection protocol

The calibration and test records were obtained through planned drives using a researcher-controlled probe rather than through passive subscriber mobility traces exported by a mobile operator. The probe recorded synchronized GPS position, raw Cell-ID and timestamp observations. Records are grouped by acquisition session or trajectory.

For the cellular sequence, each retained record corresponds to the `Index=1` cell reported by one logical scan channel. This is the channel's first-ranked scan result and is not asserted to be the serving or camped cell. Equal-time records retain their deterministic device-output order and equal timestamps.

The model token is the normalized raw Cell-ID (`CI`). Records sharing the same raw `CI` are intentionally pooled into one empirical regional support, even when that value occurs under multiple network namespaces. Consequently, a `CI` value in this package must not be interpreted as a verified physical tower identity, operator identity, or globally unique cell.

## Dataset scale reported in the paper

| Asset | Region A scale |
|---|---:|
| Calibration records | 92,232 |
| Unique normalized raw-CID values | 911 |
| Raw-CID values used for scored evidence | 901 |
| Generated policy-training trajectories | 1,000 |
| Held-out real vehicle test trajectories | 250 |
| Road-graph nodes | 6,277 |
| Directed road edges | 12,777 |

Ten singleton raw-CID values are excluded from scored road--Cell-ID evidence, leaving 92,222 records supporting 901 scored raw-CID values. They may remain in the acquisition data for auditability.

## Directory structure

```text
trajectory_data/
├── README.md
├── 基础数据/
│   ├── Region_A_路网填充CID_WGS84_911.csv
│   └── Region_A.graphml
├── 训练轨迹1000/
│   ├── xlsx/
│   ├── html/
│   ├── Region_A训练轨迹1000_manifest.csv
│   └── Region_A训练轨迹1000_summary.json
└── 250条预测轨迹/
    └── 真实轨迹/
        ├── region_a_route1_with_coords.xlsx
        ├── ...
        └── region_a_route250_with_coords.xlsx
```

### `基础数据`

- `Region_A_路网填充CID_WGS84_911.csv` contains the regional calibration observations used for empirical road--Cell-ID support construction.
- `Region_A.graphml` is the frozen directed Region A road graph used by the reported experiments.

### `训练轨迹1000`

These are **generated training trajectories**, not additional field drives. The pipeline samples 1,000 origin--destination coordinate pairs from the calibration data, generates a route and longitude--latitude sequence on the frozen road graph, and assigns each generated position a raw-CID token using the calibration records. Only these generated trajectories are used for policy fitting and development-time hyperparameter selection.

### `250条预测轨迹/真实轨迹`

These files contain the 250 held-out real vehicle trajectories. They are kept in fixed route-number order. According to the experimental protocol, these trajectories do not enter calibration, training-data generation, policy training, or hyperparameter selection.

## Unified test-workbook schema

Every test XLSX file contains one worksheet named `Sheet1`, one header row and the following six columns in a fixed order:

| Column | Type | Description |
|---|---|---|
| `开始时间` | Excel date-time | Timestamp of the retained cellular observation, displayed as `yyyy-mm-dd hh:mm:ss`. |
| `LAC` | Integer | Acquisition/audit identifier retained with the observation; it is not the model token. |
| `CI` | Integer | Normalized raw Cell-ID used as the cellular observation token. |
| `lat` | Decimal degrees | Probe latitude in WGS84. |
| `lon` | Decimal degrees | Probe longitude in WGS84. |
| `时间差(秒)` | Seconds | Elapsed time from the preceding retained row; the first row is normally zero and equal-time observations may also have zero. |

The `lat` and `lon` columns are included for reference construction, visualization and evaluation. Intermediate query GPS coordinates are not online decoder inputs.

## Licensing and release notice

This README documents provenance and experimental roles; it does not itself grant redistribution rights. Before public release, the distributor must confirm the applicable license for the probe data and include the required OpenStreetMap attribution and Open Database License notice for any OSM-derived road graph.
