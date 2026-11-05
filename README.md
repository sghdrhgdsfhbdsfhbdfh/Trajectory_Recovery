# Tower-Coordinate-Free Road-Level Trajectory Recovery

This repository provides the supplementary material and the publicly releasable Region A data for the manuscript:

> **Tower-Coordinate-Free Road-Level Trajectory Recovery from Cell-ID Sequences: Device-Side Calibration and Policy-Guided Constrained Decoding**

The release supports inspection of the data protocol, regional calibration inputs, generated policy-training trajectories, and the held-out test set described in the paper. It contains no carrier-provided tower-coordinate inventory.

## Repository contents

```text
.
├── README.md
├── Supplementary_Tower_Coordinate_Free_Road_Level_Trajectory_Recovery_from_Cell_ID_Sequences__Device_Side_Calibration_and_Policy_Guided_Constrained_Decoding.pdf
└── trajectory_data/
    ├── README.md
    ├── Basic Data/
    │   ├── regionACID_WGS84_911.csv
    │   └── regionA.graphml
    ├── Training trajectory 1000/
    │   └── xlsx/
    │       ├── regionA_0001.xlsx
    │       ├── ...
    │       └── regionA_1000.xlsx
    └── 250 predicted tracks/
        └── Real track/
            ├── shenzhen_nanshan_route1_with_coords.xlsx
            ├── ...
            └── shenzhen_nanshan_route250_with_coords.xlsx
```

The PDF contains the detailed algorithm specification, additional experiments, ablations, reproducibility information, and Region B results. The released data directory contains Region A only.

## Region A data summary

| Asset | Quantity |
| --- | ---: |
| Regional calibration records | 92,232 |
| Released remapped CI tokens | 911 |
| CI tokens used for scored road-CI evidence | 901 |
| Generated policy-training trajectories | 1,000 |
| Held-out real vehicle test trajectories | 250 |
| Road-graph nodes | 6,277 |
| Directed road edges | 12,777 |

Ten singleton CI tokens are retained in the calibration data for auditability but excluded from scored road-CI evidence, leaving 92,222 supporting records for 901 scored tokens.

## Data roles and split policy

The three data roles are intentionally separated and must not be mixed.

1. **Regional calibration data** were collected during planned drives with a researcher-controlled probe. These observations are used to construct the empirical road-CI evidence for Region A.
2. **Policy-training trajectories** are generated offline from the frozen Region A road graph and regional calibration data. They are not additional field-collected trajectories.
3. **Held-out test trajectories** are real vehicle trajectories used only for final evaluation. They do not enter regional calibration, training-trajectory generation, policy fitting, or hyperparameter selection.

The method is tower-coordinate-free, not GPS-free. Probe GPS-CI observations are used during regional calibration, and test GPS coordinates are retained for reference construction, visualization, and evaluation. During online decoding, intermediate query GPS coordinates and reference road labels are not used.

## CID remapping and privacy

All CI values in the released calibration CSV and trajectory workbooks have been pseudonymized consistently. Original CI values were sorted numerically and replaced with integer tokens from 1 to 911. Equal original CI values remain pooled, including occurrences associated with different LAC values. The original-to-released mapping is maintained separately and is not included.

This transformation pseudonymizes CI values only. Coordinates, timestamps, LAC values, road geometry, filenames, and dataset splits are unchanged. The release is therefore not location-anonymized. A released CI token must not be interpreted as a verified physical tower identity, operator identity, or globally unique cell identifier.

## File descriptions

### Regional calibration data

`trajectory_data/Basic Data/regionACID_WGS84_911.csv` contains the Region A probe observations used to build empirical road-CI evidence.

| Column | Description |
| --- | --- |
| `lac` | Acquisition and audit field. It is not used as the model token. |
| `ci` | Consistently remapped CI token in the range 1-911. |
| `lon` | Probe longitude in WGS84 decimal degrees. |
| `lat` | Probe latitude in WGS84 decimal degrees. |
| `toDateTime(env_time)` | Observation timestamp. |

`trajectory_data/Basic Data/regionA.graphml` is the frozen directed Region A road graph used in the reported experiments.

### Generated policy-training trajectories

`trajectory_data/Training trajectory 1000/xlsx/` contains 1,000 workbooks named `regionA_0001.xlsx` through `regionA_1000.xlsx`. Every workbook contains one worksheet named `trajectory` with the following columns:

| Column | Description |
| --- | --- |
| `lat` | Generated route latitude in WGS84 decimal degrees. |
| `lon` | Generated route longitude in WGS84 decimal degrees. |
| `CI` | Remapped CI token assigned from the regional calibration evidence. |

These files contain 31,474 trajectory points in total. Individual trajectories contain 18-82 points.

### Held-out real test trajectories

`trajectory_data/250 predicted tracks/Real track/` contains 250 fixed, held-out real vehicle trajectories. The directory name is retained from the experiment package; the files in `Real track` are reference/test trajectories rather than predicted outputs.

Every workbook contains one worksheet named `Sheet1` with the following columns:

| Column | Description |
| --- | --- |
| `开始时间` | Timestamp of the retained cellular observation. |
| `LAC` | Acquisition and audit field; not the model token. |
| `CI` | Remapped CI token used as the cellular observation token. |
| `lat` | Probe latitude in WGS84 decimal degrees. |
| `lon` | Probe longitude in WGS84 decimal degrees. |
| `时间差(秒)` | Elapsed time in seconds from the preceding retained row. The first row is normally zero, and equal-time observations may also have zero. |

The 250 test workbooks contain 17,330 observations in total. Intermediate `lat` and `lon` values are evaluation references and are not online decoder inputs.

## Scope of this release

This repository snapshot contains the supplementary PDF and the publicly releasable Region A data. It does not contain Region B data, source code, trained model weights, or an executable training and decoding pipeline. It therefore supports data inspection and protocol auditing but is not, by itself, a complete executable reproduction package.

## Citation

If you use these materials, please cite the manuscript named above. Complete bibliographic information will be added after publication.

## License and attribution

No separate license file is currently included in this release. Unless a license is added, the materials are provided for scholarly review and inspection, and this README does not grant permission for redistribution or reuse.

The road graph contains data derived from OpenStreetMap. OpenStreetMap data are copyright OpenStreetMap contributors and are available under the [Open Database License](https://www.openstreetmap.org/copyright).

For additional protocol details and limitations, see `trajectory_data/README.md` and the supplementary PDF.
