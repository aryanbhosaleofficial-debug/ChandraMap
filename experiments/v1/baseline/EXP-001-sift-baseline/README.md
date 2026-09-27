# EXP-001 — SIFT Baseline

**Experiment ID:** `EXP-001`
**Version:** `V1`
**Category:** Baseline
**Method:** SIFT + descriptor matching + geometric verification
**Primary Goal:** Establish a reproducible classical-computer-vision baseline for lunar image correspondence and registration.
**Primary Evaluation:** Geometrically verified correspondences and independent registration error.
**Status:** `[TBD]`

---

## 1. Experiment Overview

`EXP-001` is the first concrete baseline experiment in the ChandraMap V1 experiment series.

The purpose of this experiment is to establish a **simple, measurable, reproducible end-to-end registration baseline** using a known overlapping lunar source/reference image pair.

The intended pipeline is:

```text
Source Image
    ↓
Preprocessing
    ↓
SIFT Feature Extraction
    ↓
Descriptor Matching
    ↓
Candidate Matches
    ↓
RANSAC Geometric Verification
    ↓
Verified Inliers
    ↓
Transformation
    ↓
Registered Image / Overlay
    ↓
Independent Evaluation
```

This experiment is deliberately narrow.

It is **not** intended to demonstrate the complete ChandraMap architecture, solve whole-Moon retrieval, or establish robustness across every sensor and illumination condition.

The project feedback identifies the first practical milestone as one known source/reference pair passing through SIFT, RANSAC, transformation, registration, and numerical evaluation on independent check points.

---

## 2. Experiment Hypothesis

### Core Hypothesis

> A standard SIFT-based local correspondence pipeline can establish a measurable baseline for registering a known overlapping pair of lunar images before more advanced sensor-aware, multi-scale, learned, or retrieval-based methods are introduced.

The experiment is intended to answer:

1. Can SIFT find repeatable candidate correspondences on the selected lunar image pair?
2. How many candidate matches survive geometric verification?
3. Are the verified inliers spatially distributed across the overlap?
4. Can a simple geometric transformation register the pair?
5. What is the registration error on independent check points?
6. What failure modes appear under the selected image conditions?
7. What baseline measurements can later experiments compare against?

### What This Experiment Does Not Hypothesize

EXP-001 does **not** hypothesize that SIFT:

- solves all lunar image registration conditions;
- is invariant to arbitrary illumination changes;
- is robust to every cross-sensor combination;
- is the final ChandraMap matching method;
- provides universal sub-pixel accuracy;
- outperforms learned or multimodal approaches.

SIFT is being used as a **baseline**, not as a final-method claim.

The technical feedback explicitly recommends SIFT as the first serious accuracy baseline because it is simple, explainable, and provides a measurable reference for later methods.

---

## 3. Experiment Philosophy

EXP-001 follows the ChandraMap principle:

> **Build small. Measure honestly. Keep the failures.**

The experiment prioritizes:

- reproducibility;
- measurable evidence;
- interpretable behavior;
- controlled inputs;
- explicit assumptions;
- independent evaluation;
- honest failure analysis.

It does **not** prioritize:

- algorithm count;
- visual polish;
- premature optimization;
- unsupported performance claims;
- decorative confidence percentages;
- arbitrary benchmark scores.

The project feedback recommends proving one real end-to-end result before expanding toward the whole Moon.

---

## 4. Scope

### 4.1 In Scope

The intended scope of EXP-001 is:

- one known overlapping source/reference image pair;
- basic sensor and metadata inspection;
- preprocessing appropriate to the selected data;
- SIFT keypoint extraction;
- SIFT descriptor generation;
- descriptor matching;
- candidate-match filtering;
- RANSAC geometric verification;
- transformation estimation;
- registered output;
- quantitative evaluation;
- artifact generation;
- reproducibility information;
- failure recording.

### 4.2 Out of Scope

Unless explicitly implemented and documented elsewhere, EXP-001 does **not** attempt to solve:

- full-Moon global retrieval;
- large-scale FAISS indexing;
- Top-K candidate retrieval;
- learned local matching;
- ALIKED + LightGlue;
- LoFTR;
- RIFT;
- CFOG;
- universal cross-sensor robustness;
- complete IIRS hyperspectral matching;
- universal illumination invariance;
- advanced local/piecewise warping;
- complete sub-pixel refinement;
- full benchmark evaluation;
- production deployment;
- final lunar mosaic generation.

These are separate experimental directions.

---

## 5. Experiment Identity

| Field                  | Value                                                             |
| ---------------------- | ----------------------------------------------------------------- |
| Experiment ID          | `EXP-001`                                                         |
| Experiment name        | `SIFT Baseline`                                                   |
| Version                | `V1`                                                              |
| Category               | `Baseline`                                                        |
| Method                 | SIFT + descriptor matching + geometric verification               |
| Primary objective      | Establish a reproducible classical-CV lunar registration baseline |
| Input                  | One known overlapping source/reference pair                       |
| Geometric verification | RANSAC                                                            |
| Transformation         | `[TBD]`                                                           |
| Independent evaluation | `[TBD]`                                                           |
| Status                 | `[TBD]`                                                           |

---

## 6. Expected Evidence

A successful EXP-001 run should produce enough evidence to determine whether the baseline actually works.

The expected evidence includes:

- raw/candidate match visualization;
- rejected-outlier visualization;
- registered overlay;
- verified-inlier statistics;
- spatial distribution evidence;
- independent check-point error;
- runtime;
- configuration/provenance information.

The project feedback explicitly identifies the following evidence for the first milestone:

> match plot, rejected outliers, registered overlay, inlier statistics, and check-point error.

### Evidence status

| Evidence                       | Status  |
| ------------------------------ | ------- |
| Candidate-match plot           | `[TBD]` |
| Rejected-outlier visualization | `[TBD]` |
| Registered overlay             | `[TBD]` |
| Inlier statistics              | `[TBD]` |
| Spatial coverage               | `[TBD]` |
| Independent check-point error  | `[TBD]` |
| Runtime                        | `[TBD]` |
| Reproducibility metadata       | `[TBD]` |

---

# 7. Input Data

The exact source/reference pair must be recorded before treating the experiment as a completed measurement.

| Field                    | Value   |
| ------------------------ | ------- |
| Source image             | `[TBD]` |
| Reference image          | `[TBD]` |
| Source sensor            | `[TBD]` |
| Reference sensor         | `[TBD]` |
| Source product           | `[TBD]` |
| Reference product        | `[TBD]` |
| Source dimensions        | `[TBD]` |
| Reference dimensions     | `[TBD]` |
| Source GSD               | `[TBD]` |
| Reference GSD            | `[TBD]` |
| Effective comparison GSD | `[TBD]` |
| Projection               | `[TBD]` |
| Geographic region        | `[TBD]` |
| Illumination conditions  | `[TBD]` |
| Viewing geometry         | `[TBD]` |
| Metadata availability    | `[TBD]` |
| Ground truth             | `[TBD]` |
| Independent check points | `[TBD]` |

No real image identifier, coordinate, dimension, GSD, or product identifier should be added here until it has been confirmed from the actual experiment input.

---

# 8. Dataset Selection

## 8.1 Selection Criteria

The selected source/reference pair should ideally:

- represent the same lunar region;
- have known or demonstrable spatial overlap;
- contain sufficient terrain structure for local correspondence;
- have usable metadata;
- provide a defensible evaluation/reference mechanism;
- be accessible to another contributor;
- have reproducible provenance.

The first milestone should remain small enough to diagnose the actual failure mode.

The project feedback recommends starting with one or two real source/reference pairs rather than beginning with a whole-Moon system.

## 8.2 Selected Pair

**Selected image pair:** `[TBD]`

## 8.3 Selection Rationale

`[TBD]`

The rationale should explain:

- why the pair has sufficient overlap;
- why it is suitable for a first baseline;
- what metadata is available;
- what evaluation mechanism is available;
- what known limitations exist.

## 8.4 Known Data Limitations

`[TBD]`

Examples that may need to be documented if applicable:

- uncertain overlap;
- missing GSD;
- missing projection;
- missing illumination metadata;
- insufficient ground truth;
- incomplete sensor metadata;
- unknown orthorectification status.

---

# 9. Sensor and Physical Context

Lunar sensors should not be treated as interchangeable image sources.

The project materials distinguish the Chandrayaan-2 instruments by spatial scale, sensing modality, and registration implications.

## 9.1 Sensor Used in EXP-001

**Source sensor:** `[TBD]`
**Reference sensor:** `[TBD]`

Only sensors actually used by this experiment should be marked as active.

| Sensor                  | Relevance to EXP-001                                                   | Status  |
| ----------------------- | ---------------------------------------------------------------------- | ------- |
| Chandrayaan-2 OHRC      | High-detail visible/panchromatic lunar imagery                         | `[TBD]` |
| Chandrayaan-2 TMC-2     | Meter-scale panchromatic terrain imagery                               | `[TBD]` |
| Chandrayaan-2 IIRS      | Coarser hyperspectral/IR imagery requiring a defined 2D representation | `[TBD]` |
| LRO NAC                 | Potential high-resolution lunar reference                              | `[TBD]` |
| Other reference product | `[TBD]`                                                                | `[TBD]` |

### Important

The approximate sensor characteristics documented in project feedback are context, not substitutes for actual benchmark-product metadata.

For example:

- OHRC is described as very high-detail visible/panchromatic imagery;
- TMC-2 as approximately meter-scale panchromatic terrain imagery;
- IIRS as substantially coarser hyperspectral/IR data;
- LRO NAC as a possible high-resolution reference.

The actual experiment should use the product-specific metadata as the final authority.

---

## 9.2 IIRS Handling

If EXP-001 uses IIRS, the experiment **must explicitly define the conversion from spectral data to a registration-friendly 2D representation**.

Examples that may be evaluated include:

- selected band;
- PCA representation;
- spectral composite;
- structural representation.

The project feedback explicitly warns against treating a hyperspectral cube as an ordinary 2D image without a defined representation step.

If IIRS is not used:

**IIRS in EXP-001:** `[N/A]`

---

# 10. Preprocessing

Preprocessing must be documented because it can materially affect correspondence results.

Only operations actually performed in EXP-001 should be recorded as implemented.

| Step                            | Method                    | Parameters | Input   | Output  | Reason  |
| ------------------------------- | ------------------------- | ---------- | ------- | ------- | ------- |
| Calibration                     | `[TBD / Not implemented]` | `[TBD]`    | `[TBD]` | `[TBD]` | `[TBD]` |
| Projection / orthorectification | `[TBD / Not implemented]` | `[TBD]`    | `[TBD]` | `[TBD]` | `[TBD]` |
| Grayscale conversion            | `[TBD]`                   | `[TBD]`    | `[TBD]` | `[TBD]` | `[TBD]` |
| Denoising                       | `[TBD / Not implemented]` | `[TBD]`    | `[TBD]` | `[TBD]` | `[TBD]` |
| Contrast normalization          | `[TBD / Not implemented]` | `[TBD]`    | `[TBD]` | `[TBD]` | `[TBD]` |
| Structural representation       | `[TBD / Not implemented]` | `[TBD]`    | `[TBD]` | `[TBD]` | `[TBD]` |
| Masking                         | `[TBD / Not implemented]` | `[TBD]`    | `[TBD]` | `[TBD]` | `[TBD]` |
| Cropping                        | `[TBD / Not implemented]` | `[TBD]`    | `[TBD]` | `[TBD]` | `[TBD]` |
| Resampling                      | `[TBD / Not implemented]` | `[TBD]`    | `[TBD]` | `[TBD]` | `[TBD]` |

### Preprocessing principle

The purpose of preprocessing is to make the images more comparable without pretending that unavailable information exists.

The project feedback recommends preserving relevant geometry metadata such as:

- footprint;
- pixel scale;
- map projection;
- viewing geometry;
- lighting geometry.

---

# 11. Scale Handling

Scale handling is a required part of the experiment record.

| Field                    | Value                     |
| ------------------------ | ------------------------- |
| Source GSD               | `[TBD]`                   |
| Reference GSD            | `[TBD]`                   |
| Effective comparison GSD | `[TBD]`                   |
| Scale ratio              | `[TBD]`                   |
| Resizing                 | `[TBD]`                   |
| Downsampling             | `[TBD]`                   |
| Upsampling               | `[TBD]`                   |
| Interpolation            | `[TBD]`                   |
| Reference pyramid        | `[TBD / Not implemented]` |

## 11.1 Physical-Scale Principle

> **Upsampling changes pixel count; it does not recover spatial information that was absent from the source sensor.**

Therefore, EXP-001 must not claim additional physical terrain detail merely because an image has been resized to a finer pixel grid.

The project feedback recommends comparing imagery at physically meaningful effective scales and using a reference pyramid or downsampling the higher-resolution side where appropriate.

## 11.2 Direct Matching

If EXP-001 performs direct matching without explicit scale normalization:

**Scale normalization:** `[Not implemented]`

This should be recorded as a baseline limitation.

It must not be described as full scale invariance.

---

# 12. Illumination

The experiment must record illumination information when available.

| Field                     | Value   |
| ------------------------- | ------- |
| Source illumination       | `[TBD]` |
| Reference illumination    | `[TBD]` |
| Source Sun angle          | `[TBD]` |
| Reference Sun angle       | `[TBD]` |
| Viewing geometry          | `[TBD]` |
| Photometric normalization | `[TBD]` |
| Illumination category     | `[TBD]` |

## 12.1 Illumination Limitation

Changing the lunar Sun angle changes terrain shadow geometry, not only image brightness.

Brightness or contrast normalization can change intensity but cannot move a physical shadow into the same geometric position.

The project feedback explicitly identifies this distinction and recommends treating illumination handling as a measurable experiment rather than assuming normalization solves it.

Therefore:

> **Illumination robustness is not claimed by EXP-001.**

Unless specifically implemented and measured, EXP-001 should not describe SIFT as illumination-invariant.

---

# 13. SIFT Configuration

SIFT is the primary local feature baseline for this experiment.

| Parameter           |   Value | Notes |
| ------------------- | ------: | ----- |
| `nfeatures`         | `[TBD]` |       |
| `nOctaveLayers`     | `[TBD]` |       |
| `contrastThreshold` | `[TBD]` |       |
| `edgeThreshold`     | `[TBD]` |       |
| `sigma`             | `[TBD]` |       |
| Implementation      | `[TBD]` |       |
| Library version     | `[TBD]` |       |

If the implementation uses OpenCV's defaults, record:

```text
OpenCV defaults
```

Do not replace this with numerical values unless the actual implementation confirms them.

## 13.1 Why SIFT?

SIFT provides a useful initial baseline because it offers:

- classical computer-vision behavior;
- scale-aware keypoints;
- rotation-aware descriptors;
- interpretable feature extraction;
- a relatively simple implementation;
- a measurable reference for later methods.

The project feedback specifically recommends SIFT as the first serious accuracy baseline.

This does **not** establish SIFT as the best method for lunar imagery.

---

# 14. Descriptor Matching

This stage matches SIFT descriptors between the source and reference images.

The exact implementation must be recorded.

| Parameter            | Value   |
| -------------------- | ------- |
| Matcher              | `[TBD]` |
| Distance metric      | `[TBD]` |
| `k`                  | `[TBD]` |
| Ratio threshold      | `[TBD]` |
| Cross-check          | `[TBD]` |
| Additional filtering | `[TBD]` |

Possible implementations include:

- brute-force descriptor matching;
- FLANN;
- L2 distance;
- ratio filtering;
- cross-check filtering.

These are possibilities, not assumptions about the implementation.

---

# 15. Candidate Matches

The descriptor-matching stage produces:

> **Candidate Matches**

Candidate matches are proposed correspondences that have not yet been established as geometrically correct.

The terminology is intentional.

Candidate matches should **not** be called:

- high-confidence matches;
- verified matches;
- true matches.

A matcher score or descriptor distance alone does not establish geometric correctness.

The project feedback explicitly recommends the term **Candidate Matches** and reserves geometric verification for the RANSAC stage.

## 15.1 Candidate-Match Measurements

| Metric                      |   Value | Unit      |
| --------------------------- | ------: | --------- |
| Source keypoints            | `[TBD]` | keypoints |
| Reference keypoints         | `[TBD]` | keypoints |
| Raw descriptor matches      | `[TBD]` | matches   |
| Filtered candidate matches  | `[TBD]` | matches   |
| Minimum descriptor distance | `[TBD]` | distance  |
| Median descriptor distance  | `[TBD]` | distance  |
| Maximum descriptor distance | `[TBD]` | distance  |

## 15.2 Candidate-Match Artifact

**Candidate-match visualization:** `[TBD]`

The visualization should make it possible to inspect whether proposed correspondences are:

- broadly distributed;
- concentrated in a small area;
- visually plausible;
- dominated by obvious false matches.

---

# 16. RANSAC Geometric Verification

RANSAC is a core component of EXP-001.

The conceptual transition is:

```text
Candidate Matches
        ↓
RANSAC
        ↓
Verified Inliers
```

The project feedback recommends RANSAC as the initial geometric verification step and warns against assuming that one global model is universally valid for lunar imagery.

## 16.1 Configuration

| Parameter              | Value   |
| ---------------------- | ------- |
| Geometric model        | `[TBD]` |
| RANSAC implementation  | `[TBD]` |
| Reprojection threshold | `[TBD]` |
| Confidence             | `[TBD]` |
| Maximum iterations     | `[TBD]` |
| Minimum inliers        | `[TBD]` |
| Random seed            | `[TBD]` |
| Coordinate direction   | `[TBD]` |

Possible geometric models include:

- affine transformation;
- homography.

The actual experiment configuration must determine which model is used.

---

## 16.2 Geometry Caution

Affine or homography models can be reasonable first models for local, already map-projected image pairs.

However:

> **The Moon is not a flat poster.**

Terrain relief, sensor geometry, viewpoint differences, and projection effects can cause spatially varying residuals.

The benchmark should therefore inspect residual behavior rather than assuming a single global transform is always sufficient.

---

# 17. Verified Inliers

After geometric verification, the surviving correspondences are referred to as:

> **Verified Inliers**

These are candidate correspondences that are geometrically consistent with the selected model under the configured verification procedure.

## 17.1 Inlier Measurements

| Metric            |   Value | Unit    |
| ----------------- | ------: | ------- |
| Candidate matches | `[TBD]` | matches |
| Verified inliers  | `[TBD]` | matches |
| Rejected outliers | `[TBD]` | matches |
| Inlier ratio      | `[TBD]` | %       |
| Median residual   | `[TBD]` | px      |
| Residual RMSE     | `[TBD]` | px      |

### Inlier ratio

Conceptually:

$$
\text{Inlier Ratio}
=
\frac{\text{Verified Inliers}}
{\text{Candidate Matches}}
$$

The exact reporting convention must remain consistent across experiments.

## 17.2 Residual Distribution

**Residual statistics:** `[TBD]`

**Residual visualization:** `[TBD]`

The experiment should inspect whether residuals are:

- approximately uniform;
- spatially structured;
- concentrated in a particular region;
- increasing toward an image boundary;
- affected by terrain relief.

---

# 18. Spatial Distribution

Match quantity alone is insufficient.

A large number of verified points concentrated around one crater may provide weaker geometric control than fewer points distributed across the overlap.

The project feedback explicitly recommends grid or convex-hull coverage to determine whether good points are distributed across the overlap.

## 18.1 Spatial Metrics

| Metric                |   Value | Status  |
| --------------------- | ------: | ------- |
| Grid coverage         | `[TBD]` | `[TBD]` |
| Convex-hull coverage  | `[TBD]` | `[TBD]` |
| Bounding-box coverage | `[TBD]` | `[TBD]` |
| Spatial clustering    | `[TBD]` | `[TBD]` |

### Grid coverage

If implemented:

```text
Grid coverage = covered eligible cells / total eligible cells
```

The exact grid dimensions and cell-qualification rule must be recorded with the experiment configuration.

### If not implemented

**Spatial coverage:** `[Not implemented]`

This should be identified as a follow-up rather than silently omitted.

---

# 19. Transformation Estimation

The transformation maps coordinates between the source and reference image coordinate systems.

## 19.1 Transformation Configuration

| Field                        | Value                     |
| ---------------------------- | ------------------------- |
| Model type                   | `[TBD]`                   |
| Source → reference direction | `[TBD]`                   |
| Fitting method               | `[TBD]`                   |
| Points used for fitting      | `[TBD]`                   |
| Number of fitting points     | `[TBD]`                   |
| Final refit after refinement | `[Not implemented / TBD]` |

## 19.2 Transformation Matrix

```text
Transformation matrix:

[TBD]
```

Do not commit a fabricated matrix.

## 19.3 Coordinate Convention

**Source coordinate convention:** `[TBD]`
**Reference coordinate convention:** `[TBD]`
**Pixel indexing convention:** `[TBD]`

The experiment should explicitly document whether coordinates represent:

- pixel centers;
- array indices;
- image coordinates;
- projected coordinates.

No convention should be assumed without confirmation from the implementation.

---

# 20. Registration Output

The estimated transformation is applied to produce the registered output.

## 20.1 Registered Artifact

**Registered image:** `[TBD]`

**Overlay:** `[TBD]`

**Registration visualization:** `[TBD]`

The registered result should be used for visual inspection, but visual quality must not replace numerical evaluation.

---

# 21. Independent Evaluation

Independent evaluation is a central requirement of EXP-001.

If transformation parameters are estimated from a set of inlier/control points, those exact points should not be the only points used to measure registration error.

The project feedback explicitly warns that fitting and evaluating on the same points can make RMSE appear better than the actual registration quality.

## 21.1 Evaluation Points

| Field                            | Value          |
| -------------------------------- | -------------- |
| Ground-truth source              | `[TBD]`        |
| Check-point source               | `[TBD]`        |
| Number of check points           | `[TBD]`        |
| Used for transformation fitting? | `No` / `[TBD]` |
| Coordinate system                | `[TBD]`        |
| Verification method              | `[TBD]`        |

## 21.2 Evaluation Rule

> **Do not fit and evaluate on exactly the same points.**

Preferred order:

```text
Candidate Matches
        ↓
RANSAC
        ↓
Verified Inliers
        ↓
Transformation Fitting
        ↓
Independent Check Points
        ↓
Registration Error
```

If independent check points are unavailable:

**Independent evaluation:** `[Not available]`

The limitation must be stated clearly rather than presenting fitting residuals as independent accuracy.

---

# 22. Registration Metrics

## 22.1 Check-Point RMSE

For independent check points:

$$
RMSE =
\sqrt{
\frac{1}{N}
\sum_{i=1}^{N}
e_i^2
}
$$

where \(e_i\) is the registration error at check point \(i\).

**Check-point RMSE:** `[Not measured]`

**Unit:** `[TBD]`

---

## 22.2 Source-Pixel Error

The primary sub-pixel reporting unit should be the source-image pixel.

For a reference check point \((x_i,y_i)\) and predicted position \((\hat{x}\_i,\hat{y}\_i)\):

$$
e_i =
\sqrt{
(x_i-\hat{x}_i)^2 +
(y_i-\hat{y}_i)^2
}
$$

**Source-pixel RMSE:** `[Not measured]`

The project feedback explicitly recommends reporting sub-pixel accuracy in source-image pixels first.

---

## 22.3 Ground Error

Ground error in metres should only be reported when:

- the source/reference GSD is known;
- the projection is appropriate;
- the reference geometry is understood;
- the available ground truth supports physical-distance interpretation.

**Ground error:** `[Not measured]`

**Unit:** `m`

If these conditions are not satisfied:

**Ground error:** `[N/A]`

A source-pixel error should not be converted to metres using an arbitrary assumed scale.

---

# 23. Sub-Pixel Refinement

Sub-pixel refinement is **not automatically part of the initial SIFT baseline**.

The project architecture recommends:

```text
Candidate Matches
        ↓
RANSAC
        ↓
Verified Inliers
        ↓
Sub-Pixel Refinement
        ↓
Final Transform Refit
        ↓
Registration
```

The refinement step should therefore occur after reliable inliers have been established.

## EXP-001 Status

**Sub-pixel refinement:** `[Not implemented / TBD]`

If not implemented:

> EXP-001 does not claim complete sub-pixel refinement. Source-pixel registration error may still be measured using independently evaluated points, but this should not be described as evidence that the SIFT pipeline itself performs explicit sub-pixel tie-point refinement.

A dedicated later experiment should measure:

```text
RMSE before refinement
        vs.
RMSE after refinement
```

---

# 24. Failure Criteria

EXP-001 should define failures explicitly rather than silently dropping unsuccessful runs.

Potential failure states include:

| Failure                | Meaning                                                      |
| ---------------------- | ------------------------------------------------------------ |
| `NO_KEYPOINTS`         | Insufficient detected features                               |
| `NO_CANDIDATE_MATCHES` | Matching produced no usable candidates                       |
| `RANSAC_FAILED`        | Geometric verification did not produce a valid model         |
| `INSUFFICIENT_INLIERS` | Not enough verified correspondences for the configured model |
| `TRANSFORM_FAILED`     | Final transformation could not be estimated                  |
| `REGISTRATION_FAILED`  | Registered output could not be produced                      |
| `NO_CHECKPOINTS`       | Independent evaluation unavailable                           |
| `EVALUATION_FAILED`    | Registration was produced but could not be evaluated         |
| `SUCCESS`              | Required stages completed and evaluation was possible        |

The exact implementation of these states is:

**Failure-state implementation:** `[TBD]`

---

# 25. Runtime and Resource Measurement

Runtime should be measured separately from accuracy.

| Metric                      |            Value | Unit    |
| --------------------------- | ---------------: | ------- |
| Preprocessing runtime       | `[Not measured]` | s       |
| SIFT extraction runtime     | `[Not measured]` | s       |
| Descriptor matching runtime | `[Not measured]` | s       |
| RANSAC runtime              | `[Not measured]` | s       |
| Registration runtime        | `[Not measured]` | s       |
| End-to-end runtime          | `[Not measured]` | s       |
| Peak memory                 | `[Not measured]` | `[TBD]` |
| GPU usage                   |          `[N/A]` |         |
| Hardware                    |          `[TBD]` |         |

Runtime comparisons are meaningful only when the execution environment and timing scope are documented.

---

# 26. Experiment Results

## 26.1 Summary

| Metric               |  Result | Unit      | Status  |
| -------------------- | ------: | --------- | ------- |
| Source keypoints     | `[TBD]` | keypoints | `[TBD]` |
| Reference keypoints  | `[TBD]` | keypoints | `[TBD]` |
| Candidate matches    | `[TBD]` | matches   | `[TBD]` |
| Verified inliers     | `[TBD]` | matches   | `[TBD]` |
| Inlier ratio         | `[TBD]` | %         | `[TBD]` |
| Grid coverage        | `[TBD]` | ratio / % | `[TBD]` |
| Convex-hull coverage | `[TBD]` | ratio / % | `[TBD]` |
| Check-point RMSE     | `[TBD]` | source px | `[TBD]` |
| Ground error         | `[TBD]` | m         | `[TBD]` |
| End-to-end runtime   | `[TBD]` | s         | `[TBD]` |
| Failure state        | `[TBD]` | —         | `[TBD]` |

No result in this table should be replaced with an illustrative or estimated value.

---

# 27. Result Interpretation

Results should be interpreted as evidence about the tested image pair and protocol.

### Candidate matches

A large candidate-match count does not establish correctness.

### Verified inliers

A larger verified-inlier count can indicate more geometrically consistent points, but should be considered together with:

- inlier ratio;
- spatial coverage;
- independent check-point error.

### Inlier ratio

A high inlier ratio with only a few clustered points may not provide sufficient geometric control.

### Registration error

Independent check-point error is more informative about generalization of the fitted transformation than residual error on the same points used for fitting.

### Visual overlay

A visually convincing overlay is useful for qualitative inspection but is not sufficient evidence of accurate registration.

---

# 28. Limitations

EXP-001 is intentionally limited.

Known or expected limitations include:

1. **Single-pair scope**

   One image pair cannot establish general lunar robustness.

2. **Classical feature dependence**

   SIFT may struggle under strong modality and illumination changes.

3. **Scale limitations**

   Direct SIFT matching without physically meaningful scale handling may degrade when source/reference resolution differs substantially.

4. **Illumination limitations**

   SIFT should not be described as universally illumination-invariant.

5. **Geometry limitations**

   A single affine or homography model may not explain all terrain/viewing conditions.

6. **Sensor limitations**

   Results from one sensor pair should not automatically be generalized to OHRC, TMC-2, IIRS, and all reference products.

7. **Ground-truth limitations**

   Without independent check points, reported fitting residuals cannot be treated as independent registration accuracy.

8. **Spatial-coverage limitations**

   Match count without spatial coverage does not fully characterize geometric control.

9. **Sub-pixel limitations**

   Explicit sub-pixel tie-point refinement is separate from ordinary SIFT correspondence.

10. **Retrieval limitations**

    EXP-001 does not establish whole-Moon retrieval performance.

11. **Generalization limitations**

    A successful result on one pair is not evidence of universal lunar registration robustness.

The project feedback specifically identifies strong modality/illumination changes, extreme scale differences, terrain geometry, and insufficient spatial distribution as important limitations to test in later experiments.

---

# 29. Expected Failure Modes

The following failure modes should be investigated if they occur.

### 29.1 Too few keypoints

Possible causes:

- low-feature terrain;
- preprocessing removing useful structure;
- weak image overlap;
- unsuitable scale.

### 29.2 Many candidate matches but few inliers

Possible causes:

- descriptor ambiguity;
- repetitive terrain;
- illumination differences;
- modality differences;
- incorrect scale relationship.

### 29.3 High inlier ratio but poor registration

Possible causes:

- clustered correspondences;
- insufficient spatial coverage;
- incorrect transformation model;
- local geometric distortion.

### 29.4 Good fitting residual but poor check-point error

Possible causes:

- overfitting;
- evaluation leakage;
- spatially varying geometry;
- weak distribution of fitting points.

### 29.5 Good overlay but poor numerical evaluation

Possible causes:

- visual inspection hiding local residuals;
- inappropriate visualization scale;
- transformation being locally good but globally inaccurate.

### 29.6 Registration failure under illumination change

This should not automatically be interpreted as a software defect.

A significant Sun-angle difference can alter shadow geometry and therefore change local image appearance in ways that ordinary intensity normalization cannot remove.

---

# 30. Reproducibility Record

A completed EXP-001 run should preserve the following information.

| Field                       | Value         |
| --------------------------- | ------------- |
| Experiment ID               | `EXP-001`     |
| Experiment version          | `V1`          |
| Dataset version             | `[TBD]`       |
| Source image ID             | `[TBD]`       |
| Reference image ID          | `[TBD]`       |
| Source sensor               | `[TBD]`       |
| Reference sensor            | `[TBD]`       |
| Preprocessing configuration | `[TBD]`       |
| Scale configuration         | `[TBD]`       |
| SIFT configuration          | `[TBD]`       |
| Matcher configuration       | `[TBD]`       |
| RANSAC configuration        | `[TBD]`       |
| Transformation model        | `[TBD]`       |
| Refinement configuration    | `[TBD]`       |
| Evaluation configuration    | `[TBD]`       |
| Random seed                 | `[TBD / N/A]` |
| Hardware                    | `[TBD]`       |
| Software environment        | `[TBD]`       |
| Repository commit           | `[TBD]`       |
| Run timestamp               | `[TBD]`       |

A result should not be considered fully reproducible if its input pair, configuration, or evaluation protocol cannot be reconstructed.

---

# 31. Artifact Checklist

A completed experiment should attempt to preserve:

### Input

- [ ] Source image reference
- [ ] Reference image reference
- [ ] Product metadata
- [ ] GSD/pixel scale
- [ ] Projection information
- [ ] Illumination/viewing metadata where available

### Matching

- [ ] Source keypoint visualization
- [ ] Reference keypoint visualization
- [ ] Candidate-match visualization
- [ ] Descriptor-match statistics

### Geometric verification

- [ ] Verified-inlier visualization
- [ ] Rejected-outlier visualization
- [ ] Transformation parameters
- [ ] Residual statistics

### Evaluation

- [ ] Independent check-point definition
- [ ] Check-point error
- [ ] Source-pixel RMSE
- [ ] Ground error where meaningful
- [ ] Spatial coverage
- [ ] Runtime
- [ ] Failure state

### Reproducibility

- [ ] Configuration
- [ ] Dataset version
- [ ] Software environment
- [ ] Hardware
- [ ] Repository commit
- [ ] Random seed where applicable

---

# 32. Experiment Completion Criteria

EXP-001 should not be considered complete merely because an overlay can be generated.

A completed experiment should satisfy the following:

- [ ] A real source/reference pair has been selected.
- [ ] The pair has documented provenance.
- [ ] Sensor information is recorded.
- [ ] GSD/scale information is recorded where available.
- [ ] Preprocessing is documented.
- [ ] SIFT configuration is recorded.
- [ ] Descriptor matching configuration is recorded.
- [ ] Candidate matches are recorded.
- [ ] RANSAC configuration is recorded.
- [ ] Verified inliers are recorded.
- [ ] Transformation is recorded.
- [ ] Registered output is generated.
- [ ] Independent evaluation points are defined where available.
- [ ] Check-point error is measured where possible.
- [ ] Spatial coverage is measured or explicitly marked as not implemented.
- [ ] Runtime is measured or explicitly marked as not measured.
- [ ] Failures are recorded.
- [ ] Reproducibility metadata is preserved.
- [ ] Results contain actual measurements rather than placeholders.

---

# 33. Comparison with Future Experiments

EXP-001 establishes the reference point for later V1 experiments.

The intended progression is:

```text
EXP-001
SIFT Baseline
      ↓
Scale / Illumination Experiments
      ↓
Retrieval Experiments
      ↓
Stronger Local Matcher
      ↓
Sub-Pixel Refinement
      ↓
Additional Sensors
```

The same benchmark image pairs should be reused where scientifically appropriate.

The project feedback recommends comparing the SIFT baseline, stronger local matching, and the full sensor-aware/multi-scale pipeline on the same test pairs.

---

# 34. Future Improvements

Potential follow-up work includes:

### Scale-aware matching

Introduce:

- reference pyramids;
- effective-ground-scale matching;
- coarse-to-fine search.

### Illumination-aware representations

Evaluate:

- gradients;
- edges;
- phase-based representations;
- terrain-structure features;
- other documented structural representations.

### Stronger local matchers

Evaluate:

- ALIKED + LightGlue;
- LoFTR.

These should be tested against the same baseline image pairs.

### Multimodal matching

Investigate:

- RIFT-style approaches;
- CFOG-style approaches;
- other structural cross-modal methods.

### Sub-pixel refinement

Refine verified tie points and refit the final transformation.

### Spatial coverage

Add:

- grid coverage;
- convex-hull coverage;
- residual-vector analysis.

### Additional sensors

Expand from the initial sensor path to:

- OHRC;
- TMC-2;
- IIRS as a dedicated experiment.

### Retrieval

Only after the known-overlap pipeline is stable:

```text
Reference Images
→ Tiles + Scales
→ Global Descriptors
→ FAISS
→ Top-K
→ Local Matching
```

The project roadmap explicitly recommends establishing the one-pair baseline before adding retrieval, stronger matchers, refinement, and additional sensors.

---

# 35. Scientific Interpretation Rules

The following rules apply when interpreting EXP-001.

### Rule 1 — One pair is not a general benchmark

A successful registration on one pair demonstrates an end-to-end result for that tested condition.

It does not establish general lunar robustness.

### Rule 2 — More matches are not automatically better

Match quality and distribution matter.

### Rule 3 — Candidate matches are not ground truth

Candidate matches require geometric verification and/or independent evaluation.

### Rule 4 — RANSAC inliers are not automatically independent ground truth

RANSAC establishes consistency with the fitted geometric model.

Independent check points are still needed to evaluate registration accuracy.

### Rule 5 — Visual alignment is not numerical accuracy

An overlay is evidence for qualitative inspection only.

### Rule 6 — Pixel count is not physical resolution

Upsampling cannot create missing spatial information.

### Rule 7 — SIFT is not an invariance claim

EXP-001 does not establish universal scale, illumination, modality, or viewpoint invariance.

### Rule 8 — Fitting error is not independent accuracy

If points were used to fit the transform, they should not be the sole basis for the accuracy claim.

### Rule 9 — A single transformation is an initial model

Residual vectors must be inspected before concluding that the selected geometric model adequately explains the image relationship.

### Rule 10 — Failures remain part of the result

Failed cases should be documented rather than silently removed.

---

# 36. Current Experiment Status

| Component                          | Status                    |
| ---------------------------------- | ------------------------- |
| Experiment definition              | **Specified**             |
| SIFT baseline concept              | **Specified**             |
| Known-pair workflow                | **Specified**             |
| Actual source/reference pair       | `[TBD]`                   |
| Preprocessing implementation       | `[TBD]`                   |
| SIFT implementation                | `[TBD]`                   |
| Descriptor matching implementation | `[TBD]`                   |
| Candidate-match filtering          | `[TBD]`                   |
| RANSAC implementation              | `[TBD]`                   |
| Transformation model               | `[TBD]`                   |
| Registration output                | `[TBD]`                   |
| Independent check points           | `[TBD]`                   |
| Spatial coverage                   | `[TBD]`                   |
| Sub-pixel refinement               | `[Not implemented / TBD]` |
| Runtime measurement                | `[TBD]`                   |
| Measured benchmark results         | `[Not measured]`          |
| Failure analysis                   | `[TBD]`                   |
| Reproducible run                   | `[TBD]`                   |

This status table should be updated when implementation and measurement become available.

---

# 37. No Unsupported Results

This experiment documentation intentionally does **not** include fabricated:

- inlier counts;
- inlier ratios;
- RMSE values;
- coverage percentages;
- runtime values;
- image identifiers;
- GSD values;
- transformation matrices;
- SIFT thresholds;
- RANSAC thresholds;
- benchmark rankings.

Values should be added only after they have been measured from the actual experiment.

Decorative percentages or confidence ratings should not be used as substitutes for measured results. The project feedback specifically recommends removing unmeasured percentage/star ratings and replacing them with actual RMSE, inlier ratio, coverage, and runtime measurements.

---

# 38. Recommended Experiment Record

Once EXP-001 has been executed, the final record should be concise enough to answer the following:

```text
What images were matched?
        ↓
What sensors produced them?
        ↓
What preprocessing was applied?
        ↓
What scale relationship existed?
        ↓
How many SIFT keypoints were detected?
        ↓
How many candidate matches were produced?
        ↓
How many survived RANSAC?
        ↓
Were the inliers spatially distributed?
        ↓
What transformation was estimated?
        ↓
What was the independent check-point error?
        ↓
What was the runtime?
        ↓
Did the experiment fail anywhere?
        ↓
Can another contributor reproduce it?
```

If any of these questions cannot be answered, the missing information should be marked explicitly rather than inferred.

---

# 39. Source Basis

This experiment definition is grounded in the ChandraMap project materials supplied for SIH 26166.

The project feedback identifies the first milestone as:

```text
Known overlapping source/reference pair
→ SIFT
→ RANSAC
→ transform
→ registered overlay
→ numerical check-point error
```

and identifies match plots, rejected outliers, registered overlays, inlier statistics, and check-point error as evidence to preserve.

The technical feedback recommends SIFT as the first serious accuracy baseline and distinguishes it from later ALIKED + LightGlue, LoFTR, and RIFT/CFOG research directions.

The evaluation guidance establishes the separation between candidate matches, RANSAC inliers, spatial coverage, independent check-point RMSE, ground error where meaningful, runtime, and failure rate.

The geometry guidance establishes the order:

```text
Candidate Matches
→ RANSAC
→ Inliers
→ Sub-Pixel Tie Points
→ Final Model
→ Registered Image
```

and recommends inspecting residual vectors because lunar terrain is not inherently planar.

The sensor guidance establishes that OHRC, TMC-2, and IIRS require different consideration and that IIRS should first be converted into a suitable 2D representation for ordinary image-registration experiments.

---

# 40. Final Definition

**EXP-001 — SIFT Baseline** is the first controlled ChandraMap V1 experiment for establishing whether a simple classical local-feature pipeline can produce a measurable lunar image registration result on a known overlapping source/reference pair.

Its scientific value comes from establishing a reproducible reference point:

```text
SIFT
  ↓
Candidate Matches
  ↓
RANSAC
  ↓
Verified Inliers
  ↓
Transformation
  ↓
Registration
  ↓
Independent Evaluation
```

Later methods should be judged against this baseline using the same image pairs and evaluation protocol wherever appropriate.

The purpose is not to make SIFT appear successful.

The purpose is to determine, with measurable evidence, **what the simplest defensible baseline can actually achieve, where it fails, and which later changes genuinely improve the ChandraMap system.**

> **Build small. Measure honestly. Keep the failures.**
