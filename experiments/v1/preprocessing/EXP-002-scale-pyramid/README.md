# Experiment: [Experiment Name]

> **ChandraMap Experiment Template**
>
> This document is a reusable template for experiments under `experiments/`.
> Copy this file into the appropriate experiment directory and replace all
> placeholders with measured, reproducible information.
>
> **Do not report fictional results.** Use `TBD`, `N/A`, `Not measured`,
> `Not available`, `[Not implemented]`, or `[Planned]` where information or
> functionality is not yet available.

---

## Experiment Metadata

| Field                    | Value                                                                                                |
| ------------------------ | ---------------------------------------------------------------------------------------------------- |
| **Experiment ID**        | `[EXP-XXX]`                                                                                          |
| **Experiment Name**      | `[Experiment Name]`                                                                                  |
| **Experiment Type**      | `[Baseline / Ablation / Stress Test / Comparison / Preprocessing / Geometry / Registration / Other]` |
| **Status**               | `[Planned / In Progress / Completed / Blocked / Archived]`                                           |
| **Author**               | `[Name]`                                                                                             |
| **Date**                 | `[YYYY-MM-DD]`                                                                                       |
| **Git Commit**           | `[Commit SHA / TBD]`                                                                                 |
| **Branch / Tag**         | `[Branch / Tag / TBD]`                                                                               |
| **Dataset Version**      | `[Dataset Version / TBD]`                                                                            |
| **Benchmark Version**    | `[Benchmark Version / TBD]`                                                                          |
| **Configuration File**   | `[Path / TBD]`                                                                                       |
| **Random Seed**          | `[Value / Not applicable / TBD]`                                                                     |
| **Hardware**             | `[CPU / GPU / RAM / TBD]`                                                                            |
| **Software Environment** | `[Environment / Python version / package lock / TBD]`                                                |
| **Execution Command**    | `[Command / TBD]`                                                                                    |
| **Related Issue / PR**   | `[Issue / PR / N/A]`                                                                                 |
| **Related Experiment**   | `[Experiment ID / N/A]`                                                                              |
| **Parent Experiment**    | `[Experiment ID / N/A]`                                                                              |
| **Experiment Group**     | `[Group / TBD]`                                                                                      |

### Implementation Status

| Component                  | Status                          | Evidence / Reference |
| -------------------------- | ------------------------------- | -------------------- |
| Input preparation          | `[Implemented / Planned / TBD]` | `[TBD]`              |
| Sensor-aware preprocessing | `[Implemented / Planned / TBD]` | `[TBD]`              |
| Multi-scale representation | `[Implemented / Planned / TBD]` | `[TBD]`              |
| Global retrieval           | `[Implemented / Planned / N/A]` | `[TBD]`              |
| Candidate correspondence   | `[Implemented / Planned / TBD]` | `[TBD]`              |
| Geometric verification     | `[Implemented / Planned / TBD]` | `[TBD]`              |
| Sub-pixel refinement       | `[Implemented / Planned / N/A]` | `[TBD]`              |
| Registration               | `[Implemented / Planned / TBD]` | `[TBD]`              |
| Independent evaluation     | `[Implemented / Planned / TBD]` | `[TBD]`              |
| Metrics generation         | `[Implemented / Planned / TBD]` | `[TBD]`              |

---

## 1. Objective

### What Is Being Tested?

`[Describe the specific method, preprocessing step, representation, model,
parameter, or pipeline change being evaluated.]`

### Why Is This Experiment Necessary?

`[Explain the scientific or engineering reason for running this experiment.]`

### Hypothesis

`[State the hypothesis being tested.]`

### Expected Outcome

`[Describe the expected direction or behavior before running the experiment.]`

### Measured Outcome

`[Populate only after execution with actual measurements.]`

> **Important:** Expected outcomes must not be presented as measured results.

### Experiment Objective Statement

> This experiment evaluates whether **[method/change]** affects
> **[correspondence / geometric verification / registration metric]**
> under **[specific condition]**, while controlling **[relevant variables]**.

---

## 2. Research Question

> **Does [method/change] improve [metric or measurable property] under
> [specific condition] compared with [baseline/control]?**

### Secondary Questions

1. `[Secondary research question]`
2. `[Secondary research question]`
3. `[Secondary research question]`

### Scope

**Included:**

- `[Scope item]`
- `[Scope item]`
- `[Scope item]`

**Excluded:**

- `[Excluded item]`
- `[Excluded item]`
- `[Excluded item]`

---

## 3. Hypothesis

### Primary Hypothesis

> `[Testable primary hypothesis.]`

### Secondary Hypotheses

- `[Secondary hypothesis]`
- `[Secondary hypothesis]`
- `[Secondary hypothesis]`

### Expected Direction of Change

| Metric                | Expected Direction                                | Reason     |
| --------------------- | ------------------------------------------------- | ---------- |
| Candidate match count | `[Increase / Decrease / No predefined direction]` | `[Reason]` |
| Verified inlier count | `[Increase / Decrease / No predefined direction]` | `[Reason]` |
| Inlier ratio          | `[Increase / Decrease / No predefined direction]` | `[Reason]` |
| Spatial coverage      | `[Increase / Decrease / No predefined direction]` | `[Reason]` |
| Check-point RMSE      | `[Decrease / Increase / No predefined direction]` | `[Reason]` |
| Runtime               | `[Increase / Decrease / No predefined direction]` | `[Reason]` |
| Failure rate          | `[Decrease / Increase / No predefined direction]` | `[Reason]` |

### Conditions Under Which the Hypothesis May Fail

- `[Failure condition]`
- `[Failure condition]`
- `[Failure condition]`

> The experiment must be capable of falsifying the hypothesis. Do not
> redefine success after observing the results.

---

## 4. Experimental Variables

### Variable Table

| Variable                   | Type                  | Value   | Notes |
| -------------------------- | --------------------- | ------- | ----- |
| Sensor                     | Independent / Control | `[TBD]` |       |
| Image pair                 | Control               | `[TBD]` |       |
| Source GSD                 | Control               | `[TBD]` |       |
| Reference GSD              | Control               | `[TBD]` |       |
| Scale factor               | Independent           | `[TBD]` |       |
| Effective comparison scale | Independent           | `[TBD]` |       |
| Sun angle                  | Independent / Control | `[TBD]` |       |
| Illumination condition     | Independent / Control | `[TBD]` |       |
| Viewpoint                  | Control / Independent | `[TBD]` |       |
| Preprocessing              | Independent           | `[TBD]` |       |
| Image representation       | Independent           | `[TBD]` |       |
| Matcher                    | Control / Independent | `[TBD]` |       |
| Feature extractor          | Control / Independent | `[TBD]` |       |
| Descriptor                 | Control / Independent | `[TBD]` |       |
| Retrieval method           | Independent / N/A     | `[TBD]` |       |
| Geometric model            | Control / Independent | `[TBD]` |       |
| RANSAC threshold           | Control / Independent | `[TBD]` |       |
| Sub-pixel refinement       | Independent / N/A     | `[TBD]` |       |
| Pyramid level              | Independent           | `[TBD]` |       |
| Image dimensions           | Control / Derived     | `[TBD]` |       |
| Search window              | Control / Independent | `[TBD]` |       |
| Random seed                | Control               | `[TBD]` |       |

### Independent Variables

- `[Variable]`
- `[Variable]`

### Dependent Variables

- Candidate match count
- Verified inlier count
- Inlier ratio
- Spatial coverage
- Independent check-point error
- Runtime
- Failure status / failure rate
- `[Additional measured metric]`

### Control Variables

- `[Control variable]`
- `[Control variable]`
- `[Control variable]`

### Potential Confounding Factors

- Sensor differences
- GSD differences
- Illumination differences
- Viewpoint differences
- Projection differences
- Preprocessing differences
- Image quality
- Terrain type
- Feature density
- Repetitive terrain
- Incorrect or incomplete metadata
- `[Experiment-specific confounder]`

---

## 5. Dataset

### Dataset Identification

| Field                             | Value   |
| --------------------------------- | ------- |
| Dataset Name                      | `[TBD]` |
| Dataset Version                   | `[TBD]` |
| Source                            | `[TBD]` |
| Sensor                            | `[TBD]` |
| Product Type                      | `[TBD]` |
| Number of Image Pairs             | `[TBD]` |
| Spatial Region                    | `[TBD]` |
| Geographic Coverage               | `[TBD]` |
| GSD / Pixel Scale                 | `[TBD]` |
| Projection                        | `[TBD]` |
| Ground-Truth / Check-Point Source | `[TBD]` |
| Illumination Metadata             | `[TBD]` |
| Viewpoint Metadata                | `[TBD]` |
| Preprocessing Already Applied     | `[TBD]` |

### Image Pair Inventory

| Pair ID      | Source Image | Reference Image | Source Sensor | Reference Sensor | Source GSD | Reference GSD | Condition |
| ------------ | ------------ | --------------- | ------------- | ---------------- | ---------: | ------------: | --------- |
| `[PAIR-001]` | `[TBD]`      | `[TBD]`         | `[TBD]`       | `[TBD]`          |    `[TBD]` |       `[TBD]` | `[TBD]`   |
| `[PAIR-002]` | `[TBD]`      | `[TBD]`         | `[TBD]`       | `[TBD]`          |    `[TBD]` |       `[TBD]` | `[TBD]`   |

### Dataset Selection Rationale

`[Explain why these image pairs or regions were selected.]`

Consider:

- Sensor combination
- GSD difference
- Geographic overlap
- Terrain diversity
- Illumination difference
- Viewpoint difference
- Feature availability
- Ground-truth availability
- Relevance to the benchmark question

### Dataset Integrity

Confirm:

- [ ] Dataset version recorded
- [ ] Image identifiers recorded
- [ ] Source/reference direction recorded
- [ ] GSD recorded
- [ ] Projection recorded
- [ ] Relevant metadata preserved
- [ ] Ground-truth/check-point version recorded
- [ ] Preprocessing version recorded
- [ ] No untracked dataset substitution
- [ ] No silent mixing of incompatible dataset versions

> Do not silently mix different dataset versions, image products, projections,
> or preprocessing pipelines.

---

## 6. Sensor Configuration

The experiment must use sensor-aware processing and must not assume that
different lunar instruments have identical image characteristics.

### Sensor Summary

| Field                         | Source  | Reference |
| ----------------------------- | ------- | --------- |
| Sensor                        | `[TBD]` | `[TBD]`   |
| Modality                      | `[TBD]` | `[TBD]`   |
| Product Type                  | `[TBD]` | `[TBD]`   |
| Spatial Resolution            | `[TBD]` | `[TBD]`   |
| GSD                           | `[TBD]` | `[TBD]`   |
| Spectral Representation       | `[TBD]` | `[TBD]`   |
| Structural Information        | `[TBD]` | `[TBD]`   |
| Sensor-Specific Preprocessing | `[TBD]` | `[TBD]`   |
| Metadata Availability         | `[TBD]` | `[TBD]`   |

### Supported Sensor Context

The template can support experiments involving:

- OHRC
- TMC-2
- IIRS
- LRO NAC
- LRO WAC
- Kaguya/SELENE TC where explicitly supported
- Synthetic lunar data where explicitly supported

Only identify a sensor as an actual experiment input when the selected
dataset confirms it.

> **Sensor-specific processing requirement:** Do not force OHRC, TMC-2 and
> IIRS through an identical preprocessing pipeline without experimental
> justification.

### Sensor-Specific Notes

`[Document characteristics that materially affect this experiment.]`

---

## 7. Preprocessing

Document every operation applied before correspondence.

| Step | Method  | Parameters | Applied To                    | Purpose |
| ---- | ------- | ---------- | ----------------------------- | ------- |
| 1    | `[TBD]` | `[TBD]`    | `[Source / Reference / Both]` | `[TBD]` |
| 2    | `[TBD]` | `[TBD]`    | `[Source / Reference / Both]` | `[TBD]` |
| 3    | `[TBD]` | `[TBD]`    | `[Source / Reference / Both]` | `[TBD]` |

### Supported Operation Categories

Use only operations actually performed:

- Calibration
- Radiometric normalization
- Denoising
- Contrast normalization
- Histogram processing
- Grayscale conversion
- Edge extraction
- Gradient representation
- Phase-based representation
- Band selection
- PCA
- Composite generation
- Spectral-to-structural conversion
- Map projection
- Orthorectification
- Resampling
- Cropping
- Masking
- Normalization

### Dimension and Metadata Changes

| Property          |  Before |   After | Notes |
| ----------------- | ------: | ------: | ----- |
| Width             | `[TBD]` | `[TBD]` |       |
| Height            | `[TBD]` | `[TBD]` |       |
| GSD               | `[TBD]` | `[TBD]` |       |
| Projection        | `[TBD]` | `[TBD]` |       |
| Coordinate system | `[TBD]` | `[TBD]` |       |
| Metadata changes  | `[TBD]` | `[TBD]` |       |

### Preprocessing Rationale

`[Explain why each preprocessing operation was necessary and whether it
changes the information available for correspondence.]`

> Preprocessing may make images more comparable, but must not be described
> as recovering spatial information that was never measured.

---

## 8. Scale / Multi-Resolution Strategy

### Scale Definitions

| Term                           | Definition                                                                                                                  |
| ------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| **Pixel size**                 | Physical or image-space size represented by an individual pixel, as defined by the product metadata and coordinate context. |
| **Image dimensions**           | Number of pixels along image width and height.                                                                              |
| **GSD**                        | Ground sampling distance associated with the image/product under its applicable metadata and geometric assumptions.         |
| **Scale factor**               | Ratio used to change image sampling or representation between levels.                                                       |
| **Effective comparison scale** | Physical scale at which source and reference information are compared.                                                      |
| **Pyramid level**              | A specific representation within a multi-resolution image hierarchy.                                                        |
| **Interpolation**              | Numerical method used to estimate pixel values during resampling.                                                           |
| **Spatial information**        | Terrain/image structure actually supported by the source measurement.                                                       |

### Scale Configuration

| Parameter                        | Value              |
| -------------------------------- | ------------------ |
| Source GSD                       | `[TBD]`            |
| Reference GSD                    | `[TBD]`            |
| Approximate physical scale ratio | `[TBD]`            |
| Effective comparison GSD         | `[TBD]`            |
| Pyramid levels                   | `[TBD]`            |
| Pyramid scale factor             | `[TBD]`            |
| Downsampling method              | `[TBD]`            |
| Upsampling used                  | `[Yes / No / N/A]` |
| Interpolation                    | `[TBD]`            |

> ## ⚠️ Scientific Warning
>
> **Upsampling changes pixel count; it does not recover spatial information
> that was absent from the source sensor.**
>
> Do not claim that resizing an approximately coarse-resolution product
> creates fine terrain detail. Pixel density after interpolation is not
> equivalent to the spatial information originally measured by the sensor.

### Physical Scale Rationale

`[Explain why the selected comparison scale is physically meaningful.]`

### Multi-Resolution Representation

`[Describe the actual pyramid or multi-scale representation.]`

---

## 9. Illumination / Sun-Angle Conditions

| Parameter                  | Source  | Reference |
| -------------------------- | ------- | --------- |
| Illumination condition     | `[TBD]` | `[TBD]`   |
| Sun incidence angle        | `[TBD]` | `[TBD]`   |
| Emission / view angle      | `[TBD]` | `[TBD]`   |
| Phase angle                | `[TBD]` | `[TBD]`   |
| Illumination normalization | `[TBD]` | `[TBD]`   |
| Shadow handling            | `[TBD]` | `[TBD]`   |

### Illumination Category

- [ ] Similar lighting
- [ ] Moderately different lighting
- [ ] Strongly different lighting
- [ ] Unknown
- [ ] Not available

### Illumination Representation

`[Describe whether grayscale, gradient, edge, phase-based, or another
representation is used.]`

> Brightness or contrast normalization does not recreate terrain shadows
> that changed because of illumination geometry.

### Illumination Stress Test

| Condition                     | Description | Status                                    |
| ----------------------------- | ----------- | ----------------------------------------- |
| Similar lighting              | `[TBD]`     | `[Not implemented / Planned / Completed]` |
| Moderately different lighting | `[TBD]`     | `[Not implemented / Planned / Completed]` |
| Strongly different lighting   | `[TBD]`     | `[Not implemented / Planned / Completed]` |

---

## 10. Matching Method

### Method Configuration

| Method  | Extractor | Matcher | Parameters | Version |
| ------- | --------- | ------- | ---------- | ------- |
| `[TBD]` | `[TBD]`   | `[TBD]` | `[TBD]`    | `[TBD]` |

### Baseline / Classical Methods

Potential methods include:

- SIFT
- Descriptor matching
- Ratio test
- Cross-check

### Learned / Advanced Methods

Potential methods include:

- ALIKED + LightGlue
- LoFTR

### Research Directions

Potential methods include:

- RIFT
- CFOG
- Other documented methods

Only list methods actually evaluated.

> A more advanced method must not be assumed to perform better without
> measurement under the same evaluation protocol.

### Why This Method Was Selected

`[TBD]`

### Expected Strengths

- `[TBD]`
- `[TBD]`

### Expected Failure Modes

- `[TBD]`
- `[TBD]`

---

## 11. Global Retrieval

> **Optional section. Complete only when global retrieval is actually part of
> the experiment.**

Global retrieval is relevant when the corresponding lunar region is not
already known through reliable metadata, footprint information, map
projection, or another justified constraint.

### Retrieval Configuration

| Parameter           | Value   |
| ------------------- | ------- |
| Reference tile set  | `[TBD]` |
| Tile size           | `[TBD]` |
| Pyramid level       | `[TBD]` |
| Global descriptor   | `[TBD]` |
| Embedding dimension | `[TBD]` |
| FAISS index type    | `[TBD]` |
| Index parameters    | `[TBD]` |
| Candidate count K   | `[TBD]` |
| Metadata filtering  | `[TBD]` |

### Retrieval Metrics

| Metric                 |  Result |
| ---------------------- | ------: |
| Recall@1               | `[TBD]` |
| Recall@5               | `[TBD]` |
| Other retrieval metric | `[TBD]` |

### Retrieval vs Local Correspondence

**Global retrieval features:**

`[Describe features used to identify likely reference regions.]`

**Local correspondence features:**

`[Describe features used to establish detailed source/reference
correspondences.]`

> Global retrieval and local correspondence are separate pipeline stages
> and must not be reported as though they measure the same capability.

---

## 12. Candidate Matches

Candidate matches are raw or filtered correspondences before geometric
verification.

### Candidate-Match Configuration

| Parameter            | Value   |
| -------------------- | ------- |
| Matching threshold   | `[TBD]` |
| Confidence threshold | `[TBD]` |
| Descriptor distance  | `[TBD]` |
| Ratio test           | `[TBD]` |
| Cross-check          | `[TBD]` |
| Additional filtering | `[TBD]` |

### Candidate Match Results

| Condition / Level | Source Keypoints | Reference Keypoints | Candidate Matches | Median Distance | Notes |
| ----------------- | ---------------: | ------------------: | ----------------: | --------------: | ----- |
| `[L0 / Control]`  |          `[TBD]` |             `[TBD]` |           `[TBD]` |         `[TBD]` |       |
| `[L1]`            |          `[TBD]` |             `[TBD]` |           `[TBD]` |         `[TBD]` |       |
| `[L2]`            |          `[TBD]` |             `[TBD]` |           `[TBD]` |         `[TBD]` |       |

> **Candidate matches are not equivalent to geometrically verified
> inliers.**

---

## 13. Geometric Verification

### Verification Configuration

| Parameter              | Value                                 |
| ---------------------- | ------------------------------------- |
| Geometric model        | `[Affine / Homography / Other / TBD]` |
| RANSAC implementation  | `[TBD]`                               |
| Reprojection threshold | `[TBD]`                               |
| Confidence             | `[TBD]`                               |
| Maximum iterations     | `[TBD]`                               |
| Minimum inliers        | `[TBD]`                               |
| Random seed            | `[TBD]`                               |

### Verification Results

| Condition / Level | Candidate Matches | Verified Inliers | Inlier Ratio | Residual Statistics | Spatial Coverage |
| ----------------- | ----------------: | ---------------: | -----------: | ------------------- | ---------------: |
| `[Control]`       |           `[TBD]` |          `[TBD]` |      `[TBD]` | `[TBD]`             |          `[TBD]` |
| `[L1]`            |           `[TBD]` |          `[TBD]` |      `[TBD]` | `[TBD]`             |          `[TBD]` |
| `[L2]`            |           `[TBD]` |          `[TBD]` |      `[TBD]` | `[TBD]`             |          `[TBD]` |

### Why This Geometric Model Was Chosen

`[TBD]`

### Model Assumptions

- `[TBD]`
- `[TBD]`

### Failure Conditions

- `[TBD]`
- `[TBD]`

> A larger number of candidate matches does not necessarily indicate better
> registration. Incorrect or spatially clustered matches may increase the
> match count while reducing geometric reliability.

---

## 14. Sub-Pixel Refinement

### Status

`[Implemented / Not implemented / Planned / N/A]`

### Refinement Configuration

| Parameter                  | Value   |
| -------------------------- | ------- |
| Refinement method          | `[TBD]` |
| Patch size                 | `[TBD]` |
| Interpolation              | `[TBD]` |
| Correlation / phase method | `[TBD]` |
| Points refined             | `[TBD]` |
| Convergence criteria       | `[TBD]` |
| Maximum iterations         | `[TBD]` |

### Required Processing Sequence

When implemented, preserve the following sequence:

```text
Candidate Matches
        ↓
RANSAC / Geometric Verification
        ↓
Verified Inliers / Control Points
        ↓
Sub-Pixel Refinement
        ↓
Final Transformation Refit
```

### Before / After Refinement

| Metric           | Before Refinement | After Refinement | Difference |
| ---------------- | ----------------: | ---------------: | ---------: |
| Point error      |           `[TBD]` |          `[TBD]` |    `[TBD]` |
| Check-point RMSE |           `[TBD]` |          `[TBD]` |    `[TBD]` |

> If sub-pixel refinement is not implemented, do not imply that the
> experiment demonstrates sub-pixel performance.

---

## 15. Final Transformation

### Transformation Configuration

| Field                 | Value   |
| --------------------- | ------- |
| Transformation type   | `[TBD]` |
| Source direction      | `[TBD]` |
| Reference direction   | `[TBD]` |
| Coordinate convention | `[TBD]` |
| Fitting points        | `[TBD]` |
| Final refit method    | `[TBD]` |
| Final matrix          | `[TBD]` |

### Transformation Matrix

```text
[TBD]
```

### Transformation Parameters

```text
[TBD]
```

### Residual Summary

| Statistic           |   Value |
| ------------------- | ------: |
| Mean residual       | `[TBD]` |
| Median residual     | `[TBD]` |
| RMSE                | `[TBD]` |
| Maximum residual    | `[TBD]` |
| Percentile residual | `[TBD]` |

> Do not assume that a single global transformation is sufficient for every
> lunar image pair. Terrain relief, viewing geometry, projection, and raw
> sensor geometry may require additional investigation.

---

## 16. Control vs Experimental Conditions

This experiment must distinguish the control configuration from the
experimental configuration.

### Control Condition

The control condition should represent the baseline configuration without
the experimental change.

**Configuration:**

`[Describe the control configuration.]`

### Experimental Condition

The experimental condition includes:

`[Describe the new method or configuration.]`

### Controlled Variables

Where possible, keep the following unchanged:

- Image pair
- Dataset version
- Evaluation points
- Matcher
- Feature extractor
- Feature parameters
- RANSAC configuration
- Transformation model
- Evaluation protocol
- Hardware
- Software environment
- Random seed

### Primary Experimental Difference

> **Primary variable changed:** `[TBD]`

### Confounding Changes

| Change  | Control | Experimental | Justification |
| ------- | ------- | ------------ | ------------- |
| `[TBD]` | `[TBD]` | `[TBD]`      | `[TBD]`       |

> If multiple important variables change, the experiment cannot attribute
> observed differences solely to the primary experimental change without
> additional controls or ablations.

---

## 17. Experiment Matrix

| Condition | Strategy    |   Level | Effective GSD | Feature Method       | Matcher  | RANSAC   | Purpose                 |
| --------- | ----------- | ------: | ------------: | -------------------- | -------- | -------- | ----------------------- |
| Control   | Direct      |     N/A |       `[TBD]` | `[Same as baseline]` | `[Same]` | `[Same]` | Baseline                |
| P1        | `[Pyramid]` | `[TBD]` |       `[TBD]` | `[Same]`             | `[Same]` | `[Same]` | Coarse comparison       |
| P2        | `[Pyramid]` | `[TBD]` |       `[TBD]` | `[Same]`             | `[Same]` | `[Same]` | Intermediate comparison |
| P3        | `[Pyramid]` | `[TBD]` |       `[TBD]` | `[Same]`             | `[Same]` | `[Same]` | Fine comparison         |

Add or remove rows according to the actual experiment.

---

## 18. Metrics

The experiment must use measurable metrics rather than decorative
confidence or quality scores.

### Matching Metrics

- Candidate match count
- Verified inlier count
- Inlier ratio

### Spatial Metrics

- Grid coverage
- Convex-hull coverage where implemented
- Bounding-box coverage where implemented
- Point density
- Clustering indicators where implemented

### Registration Metrics

- Independent check-point RMSE
- Median error
- Maximum error
- Percentile error where available

### Geospatial Metrics

Ground error in metres should only be reported when:

- GSD is known;
- projection/coordinate context is meaningful; and
- reference ground truth supports the conversion.

### System Metrics

- Runtime
- Failure rate

### Primary Results Table

| Condition        | Candidate Matches | Inliers | Inlier Ratio | Coverage | Check-Point RMSE | Runtime | Failure |
| ---------------- | ----------------: | ------: | -----------: | -------: | ---------------: | ------: | ------- |
| `[Control]`      |           `[TBD]` | `[TBD]` |      `[TBD]` |  `[TBD]` |          `[TBD]` | `[TBD]` | `[TBD]` |
| `[Experimental]` |           `[TBD]` | `[TBD]` |      `[TBD]` |  `[TBD]` |          `[TBD]` | `[TBD]` | `[TBD]` |

---

## 19. Independent Check Points

Independent evaluation is required whenever appropriate ground truth or
check-point data is available.

### Evaluation Principle

```text
Control / Fit Points
        ↓
Transformation Estimation
        ↓
Independent Check Points
        ↓
Registration / Geometric Error Evaluation
```

### Check-Point Configuration

| Field                             | Value                  |
| --------------------------------- | ---------------------- |
| Check-point source                | `[TBD]`                |
| Check-point dataset version       | `[TBD]`                |
| Number of check points            | `[TBD]`                |
| Points used for fitting           | `[TBD]`                |
| Points used for evaluation        | `[TBD]`                |
| Independent from fitting points?  | `[Yes / No / Unknown]` |
| Check-point coordinate convention | `[TBD]`                |
| Validation method                 | `[TBD]`                |

> **Do not fit and judge on exactly the same points when independent
> check points are available.**

### Leakage Check

- [ ] Check points were not used to estimate the transformation
- [ ] Check points were not used to tune thresholds
- [ ] Check points were not used to select the best method after execution
- [ ] Check-point identities are recorded
- [ ] Check-point version is recorded
- [ ] Evaluation protocol was defined before final result selection

If independent check points are unavailable:

> **Independent check-point evaluation:** `[Not available]`

Do not describe fitting-point RMSE as independent registration accuracy.

---

## 20. Error Units

### Primary Error Unit

> **Source-image pixels**

Registration error should be reported in source-image pixels first where
appropriate to the experiment.

### Ground-Distance Conversion

Convert error to metres only when:

- source GSD is known;
- the coordinate/projection context is meaningful; and
- the reference truth supports the conversion.

| Error Metric | Source Pixels | Ground Distance | Conversion Basis |
| ------------ | ------------: | --------------: | ---------------- |
| RMSE         |       `[TBD]` |   `[TBD / N/A]` | `[TBD]`          |
| Median       |       `[TBD]` |   `[TBD / N/A]` | `[TBD]`          |
| Maximum      |       `[TBD]` |   `[TBD / N/A]` | `[TBD]`          |

> Equal pixel error across different sensors does not necessarily represent
> equal physical ground error.

Do not report a conversion such as `0.2 px = X metres` without actual GSD
and coordinate context.

---

## 21. Spatial Coverage

The experiment must determine whether successful correspondences are
distributed across the relevant image region.

### Coverage Metrics

| Metric                |   Value | Method  |
| --------------------- | ------: | ------- |
| Grid coverage         | `[TBD]` | `[TBD]` |
| Convex-hull coverage  | `[TBD]` | `[TBD]` |
| Bounding-box coverage | `[TBD]` | `[TBD]` |
| Point density         | `[TBD]` | `[TBD]` |
| Clustering            | `[TBD]` | `[TBD]` |

### Spatial Distribution Assessment

`[Describe where verified correspondences occur.]`

### Coverage Interpretation

`[Describe whether correspondences are distributed across the useful
registration area or concentrated in a small region.]`

> A large number of matches concentrated around one small crater or
> localized feature must not automatically be treated as strong global
> registration evidence.

---

## 22. Results

> **Results must contain measured observations only.**

### 22.1 Scale-Level Results

| Pyramid Level | Effective GSD | Candidate Matches | Inliers | Inlier Ratio | Coverage | Check-Point RMSE |
| ------------- | ------------: | ----------------: | ------: | -----------: | -------: | ---------------: |
| L0            |       `[TBD]` |           `[TBD]` | `[TBD]` |      `[TBD]` |  `[TBD]` |          `[TBD]` |
| L1            |       `[TBD]` |           `[TBD]` | `[TBD]` |      `[TBD]` |  `[TBD]` |          `[TBD]` |
| L2            |       `[TBD]` |           `[TBD]` | `[TBD]` |      `[TBD]` |  `[TBD]` |          `[TBD]` |
| L3            |       `[TBD]` |           `[TBD]` | `[TBD]` |      `[TBD]` |  `[TBD]` |          `[TBD]` |

Adapt the number of levels to the actual implementation.

### 22.2 Baseline Comparison

| Metric            | Baseline | Experimental | Difference | Notes     |
| ----------------- | -------: | -----------: | ---------: | --------- |
| Candidate matches |  `[TBD]` |      `[TBD]` |    `[TBD]` |           |
| Verified inliers  |  `[TBD]` |      `[TBD]` |    `[TBD]` |           |
| Inlier ratio      |  `[TBD]` |      `[TBD]` |    `[TBD]` |           |
| Spatial coverage  |  `[TBD]` |      `[TBD]` |    `[TBD]` |           |
| Check-point RMSE  |  `[TBD]` |      `[TBD]` |    `[TBD]` | Source px |
| Runtime           |  `[TBD]` |      `[TBD]` |    `[TBD]` | Seconds   |
| Failure           |  `[TBD]` |      `[TBD]` |    `[TBD]` |           |

### 22.3 Quantitative Results

`[TBD]`

### 22.4 Qualitative Results

`[TBD]`

### 22.5 Visualization

`[Visualization path: TBD]`

### 22.6 Error Analysis

`[TBD]`

### 22.7 Failure Cases

`[TBD]`

> Do not fabricate RMSE, inlier ratio, coverage, runtime, success rate,
> confidence scores, or improvement percentages.

---

## 23. Expected Results vs Measured Results

### Expected Results

Before execution:

- `[Expected observation]`
- `[Expected observation]`
- `[Expected observation]`

### Measured Results

After execution:

- `[Measured observation]`
- `[Measured observation]`
- `[Measured observation]`

### Interpretation

`[Interpret the measured results without converting hypotheses into facts.]`

---

## 24. Scale-Level Comparison

| Pyramid Level | Effective GSD | Candidate Matches | Inliers | Inlier Ratio | Coverage |    RMSE |
| ------------- | ------------: | ----------------: | ------: | -----------: | -------: | ------: |
| L0            |       `[TBD]` |           `[TBD]` | `[TBD]` |      `[TBD]` |  `[TBD]` | `[TBD]` |
| L1            |       `[TBD]` |           `[TBD]` | `[TBD]` |      `[TBD]` |  `[TBD]` | `[TBD]` |
| L2            |       `[TBD]` |           `[TBD]` | `[TBD]` |      `[TBD]` |  `[TBD]` | `[TBD]` |
| L3            |       `[TBD]` |           `[TBD]` | `[TBD]` |      `[TBD]` |  `[TBD]` | `[TBD]` |

### Selected Scale

`[TBD]`

### Selection Rule

`[Document the predefined or implemented rule used to select a scale.]`

> Do not select a pyramid level solely because it produced a visually
> attractive overlay. Selection must be tied to the documented evaluation
> protocol.

---

## 25. Failure Analysis

Failures are first-class experimental evidence and must not be hidden.

### Failure Categories

Potential categories include:

- Source/reference scale mismatch
- Inappropriate pyramid level
- Over-downsampling
- Loss of useful fine structure
- Interpolation artifacts
- Insufficient keypoints
- Repetitive terrain
- Candidate-match degradation
- Clustered inliers
- Illumination mismatch
- Geometric-model mismatch
- Metadata error
- Incorrect GSD
- Registration failure
- `[Other]`

### Failure Record

#### Failure Condition

`[TBD]`

#### Observed Behavior

`[TBD]`

#### Evidence

`[Path / metric / visualization / log / TBD]`

#### Suspected Cause

`[TBD]`

#### Impact

`[TBD]`

#### Reproduction

`[Command / configuration / TBD]`

#### Next Experiment

`[TBD]`

### Failure Summary

| Failure Category | Occurrences | Evidence | Impact  | Follow-Up |
| ---------------- | ----------: | -------- | ------- | --------- |
| `[TBD]`          |     `[TBD]` | `[TBD]`  | `[TBD]` | `[TBD]`   |

> Do not remove or omit failures because they make the experimental
> approach appear less effective.

---

## 26. Ablation / Controlled Comparison

### A0 — Direct Matching

**Status:** `[Planned / Completed / Not implemented]`

**Description:**

`No reference pyramid or experimental scale-handling component.`

**Configuration:**

`[TBD]`

### A1 — Pyramid

**Status:** `[Planned / Completed / Not implemented]`

**Description:**

`Reference pyramid enabled.`

**Configuration:**

`[TBD]`

### A2 — Pyramid + Structural Representation

**Status:** `[Planned / Completed / Not implemented / N/A]`

**Description:**

`Reference pyramid combined with a documented structural representation.`

**Configuration:**

`[TBD]`

> Do not add A2 merely to make the experiment appear more sophisticated.
> Include it only if it is actually implemented and scientifically relevant.

### Ablation Results

| Ablation | Candidate Matches | Inliers | Inlier Ratio | Coverage | Check-Point RMSE | Runtime |
| -------- | ----------------: | ------: | -----------: | -------: | ---------------: | ------: |
| A0       |           `[TBD]` | `[TBD]` |      `[TBD]` |  `[TBD]` |          `[TBD]` | `[TBD]` |
| A1       |           `[TBD]` | `[TBD]` |      `[TBD]` |  `[TBD]` |          `[TBD]` | `[TBD]` |
| A2       |           `[TBD]` | `[TBD]` |      `[TBD]` |  `[TBD]` |          `[TBD]` | `[TBD]` |

---

## 27. Illumination Stress Test

**Status:** `[Implemented / Planned / Not implemented]`

### Test Conditions

| Condition            | Source Lighting | Reference Lighting | Purpose           | Status  |
| -------------------- | --------------- | ------------------ | ----------------- | ------- |
| Similar              | `[TBD]`         | `[TBD]`            | Baseline lighting | `[TBD]` |
| Moderately different | `[TBD]`         | `[TBD]`            | Moderate stress   | `[TBD]` |
| Strongly different   | `[TBD]`         | `[TBD]`            | High stress       | `[TBD]` |

### Results

| Condition            | Inlier Ratio | Coverage | Check-Point RMSE | Failure Rate | Runtime |
| -------------------- | -----------: | -------: | ---------------: | -----------: | ------: |
| Similar              |      `[TBD]` |  `[TBD]` |          `[TBD]` |      `[TBD]` | `[TBD]` |
| Moderately different |      `[TBD]` |  `[TBD]` |          `[TBD]` |      `[TBD]` | `[TBD]` |
| Strongly different   |      `[TBD]` |  `[TBD]` |          `[TBD]` |      `[TBD]` | `[TBD]` |

### Interpretation

`[TBD]`

> This stress test evaluates whether the method remains useful under
> illumination changes. It does not by itself establish illumination
> invariance.

If not implemented:

> **Illumination stress test:** `[Not implemented]`

---

## 28. Output Artifacts

Document every artifact generated by the experiment.

| Artifact              | Path    | Required    | Status  | Description |
| --------------------- | ------- | ----------- | ------- | ----------- |
| Source image          | `[TBD]` | Yes         | `[TBD]` |             |
| Reference image       | `[TBD]` | Yes         | `[TBD]` |             |
| Pyramid levels        | `[TBD]` | Yes         | `[TBD]` |             |
| Scale metadata        | `[TBD]` | Yes         | `[TBD]` |             |
| Candidate matches     | `[TBD]` | Yes         | `[TBD]` |             |
| RANSAC inliers        | `[TBD]` | Yes         | `[TBD]` |             |
| Registered image      | `[TBD]` | Yes         | `[TBD]` |             |
| Metrics               | `[TBD]` | Yes         | `[TBD]` |             |
| Configuration         | `[TBD]` | Yes         | `[TBD]` |             |
| Logs                  | `[TBD]` | Recommended | `[TBD]` |             |
| Transformation matrix | `[TBD]` | Yes         | `[TBD]` |             |
| Check-point results   | `[TBD]` | Recommended | `[TBD]` |             |
| Failure report        | `[TBD]` | Recommended | `[TBD]` |             |

Use actual repository paths when known.

---

## 29. Visualizations

Visual evidence should support the quantitative evaluation.

### Recommended Visualizations

1. Original source image
2. Original reference image
3. Reference image pyramid
4. Effective-GSD comparison
5. Candidate matches at each scale
6. RANSAC-verified inliers at each scale
7. Spatial distribution of inliers
8. Registered overlay
9. Residual/error visualization
10. Baseline vs experimental comparison

### Visualization Inventory

| Visualization        | Path    | Status  | Purpose |
| -------------------- | ------- | ------- | ------- |
| Source image         | `[TBD]` | `[TBD]` |         |
| Reference image      | `[TBD]` | `[TBD]` |         |
| Pyramid              | `[TBD]` | `[TBD]` |         |
| Candidate matches    | `[TBD]` | `[TBD]` |         |
| Verified inliers     | `[TBD]` | `[TBD]` |         |
| Spatial coverage     | `[TBD]` | `[TBD]` |         |
| Registration overlay | `[TBD]` | `[TBD]` |         |
| Residuals            | `[TBD]` | `[TBD]` |         |
| Baseline comparison  | `[TBD]` | `[TBD]` |         |

> A visually attractive overlay is supporting evidence, not sufficient
> evidence of accurate registration.

---

## 30. Reproducibility

### Environment

| Item                  | Value       |
| --------------------- | ----------- |
| Experiment ID         | `[EXP-XXX]` |
| Git Commit            | `[TBD]`     |
| Dataset Version       | `[TBD]`     |
| Source Image          | `[TBD]`     |
| Reference Image       | `[TBD]`     |
| Source GSD            | `[TBD]`     |
| Reference GSD         | `[TBD]`     |
| Pyramid Configuration | `[TBD]`     |
| Feature Configuration | `[TBD]`     |
| Matcher Configuration | `[TBD]`     |
| RANSAC Configuration  | `[TBD]`     |
| Random Seed           | `[TBD]`     |
| Python Version        | `[TBD]`     |
| OpenCV Version        | `[TBD]`     |
| OS                    | `[TBD]`     |
| CPU                   | `[TBD]`     |
| GPU                   | `[TBD]`     |
| CUDA                  | `[TBD]`     |

### Execution Command

```bash
# Experiment execution command
[TBD]
```

### Configuration Snapshot

```text
[TBD]
```

### Reproducibility Checklist

- [ ] Experiment ID recorded
- [ ] Git commit recorded
- [ ] Dataset version recorded
- [ ] Source image recorded
- [ ] Reference image recorded
- [ ] Source/reference direction recorded
- [ ] GSD values recorded
- [ ] Pyramid configuration recorded
- [ ] Preprocessing recorded
- [ ] Feature configuration recorded
- [ ] Matcher configuration recorded
- [ ] Geometric verification configuration recorded
- [ ] Random seed recorded
- [ ] Hardware recorded
- [ ] Software versions recorded
- [ ] Execution command recorded
- [ ] Generated artifacts recorded
- [ ] Evaluation points/check points recorded
- [ ] Failures recorded

---

## 31. SIFT Configuration

> Complete this section when SIFT is used. For a controlled follow-up to a
> SIFT baseline, keep these values unchanged unless the experiment
> explicitly studies SIFT parameter changes.

| Parameter           | Baseline | Experiment | Changed?     |
| ------------------- | -------: | ---------: | ------------ |
| `nfeatures`         |  `[TBD]` |    `[TBD]` | `[No / Yes]` |
| `nOctaveLayers`     |  `[TBD]` |    `[TBD]` | `[No / Yes]` |
| `contrastThreshold` |  `[TBD]` |    `[TBD]` | `[No / Yes]` |
| `edgeThreshold`     |  `[TBD]` |    `[TBD]` | `[No / Yes]` |
| `sigma`             |  `[TBD]` |    `[TBD]` | `[No / Yes]` |

### SIFT Configuration Rationale

`[TBD]`

> If SIFT configuration changes together with the experimental variable,
> document the change as a potential confounder.

---

## 32. Matching Configuration

| Parameter              | Baseline | Experiment |
| ---------------------- | -------- | ---------- |
| Matcher                | `[TBD]`  | `[TBD]`    |
| Distance metric        | `[TBD]`  | `[TBD]`    |
| Ratio threshold        | `[TBD]`  | `[TBD]`    |
| Cross-check            | `[TBD]`  | `[TBD]`    |
| Additional filtering   | `[TBD]`  | `[TBD]`    |
| Matching scale / level | `[TBD]`  | `[TBD]`    |

### Matching Configuration Rationale

`[TBD]`

---

## 33. Reference Image Pyramid

> Complete this section when a multi-resolution reference representation is
> part of the experiment.

### Pyramid Definition

| Parameter                   | Value   |
| --------------------------- | ------- |
| Number of levels            | `[TBD]` |
| Scale factor                | `[TBD]` |
| Starting resolution         | `[TBD]` |
| Ending resolution           | `[TBD]` |
| Interpolation method        | `[TBD]` |
| Pyramid construction method | `[TBD]` |

### Pyramid Levels

| Level | Scale Factor |   Width |  Height | Effective GSD | Notes |
| ----- | -----------: | ------: | ------: | ------------: | ----- |
| L0    |      `[TBD]` | `[TBD]` | `[TBD]` |       `[TBD]` |       |
| L1    |      `[TBD]` | `[TBD]` | `[TBD]` |       `[TBD]` |       |
| L2    |      `[TBD]` | `[TBD]` | `[TBD]` |       `[TBD]` |       |
| L3    |      `[TBD]` | `[TBD]` | `[TBD]` |       `[TBD]` |       |

### Level Selection Procedure

1. Record source GSD.
2. Record reference GSD.
3. Determine the applicable physical scale relationship.
4. Construct the reference multi-resolution representation.
5. Identify candidate comparable scales.
6. Evaluate correspondence at the selected level(s).
7. Perform geometric verification.
8. Evaluate using independent check points where available.
9. Refine only where the source information supports it.

### Selected Level

`[TBD]`

### Selection Evidence

`[TBD]`

---

## 34. Coarse-to-Fine Strategy

### Coarse Stage

**Goal:**

> Find structural correspondence at a physically compatible scale.

**Configuration:**

`[TBD]`

**Results:**

`[TBD]`

### Fine Stage

**Goal:**

> Refine alignment only where the source sensor contains sufficient
> information to support the refinement.

**Configuration:**

`[TBD]`

**Results:**

`[TBD]`

### Coarse-to-Fine Flow

```text
Source Image
     │
     ▼
Sensor-Aware Preprocessing
     │
     ▼
Determine Source / Reference GSD
     │
     ▼
Construct Multi-Resolution Representation
     │
     ▼
Select Physically Comparable Scale
     │
     ▼
Coarse Correspondence
     │
     ▼
Local / Fine Correspondence
     │
     ▼
Geometric Verification
     │
     ▼
Registration
     │
     ▼
Independent Evaluation
```

> Search coarse first; refine only where the source sensor actually has
> enough information.

---

## 35. Transformation and Registration Evaluation

### Registration Direction

```text
Source → Reference
```

or:

```text
Reference → Source
```

**Actual direction:**

`[TBD]`

### Registration Configuration

| Parameter                   | Value   |
| --------------------------- | ------- |
| Transformation model        | `[TBD]` |
| Coordinate convention       | `[TBD]` |
| Source coordinate system    | `[TBD]` |
| Reference coordinate system | `[TBD]` |
| Fitting point set           | `[TBD]` |
| Check-point set             | `[TBD]` |
| Final refit                 | `[TBD]` |

### Registration Result

`[TBD]`

### Registration Evidence

`[TBD]`

---

## 36. Baseline Comparison

### Baseline

**Experiment:**

`[Baseline Experiment ID]`

**Configuration:**

`[TBD]`

### Comparison Rules

The baseline comparison should use the same:

- Image pair
- Dataset version
- Ground-truth/check-point version
- Evaluation protocol
- Primary metrics
- Hardware where practical
- Software environment where practical
- Random seed where applicable

### Comparison

| Property          | Baseline | Experiment |
| ----------------- | -------- | ---------- |
| Input pair        | `[TBD]`  | `[TBD]`    |
| Preprocessing     | `[TBD]`  | `[TBD]`    |
| Scale handling    | `[TBD]`  | `[TBD]`    |
| Feature extractor | `[TBD]`  | `[TBD]`    |
| Matcher           | `[TBD]`  | `[TBD]`    |
| RANSAC            | `[TBD]`  | `[TBD]`    |
| Transformation    | `[TBD]`  | `[TBD]`    |
| Evaluation points | `[TBD]`  | `[TBD]`    |
| Metrics           | `[TBD]`  | `[TBD]`    |

### Interpretation

`[TBD]`

---

## 37. Statistical / Repeated Evaluation

> Complete when multiple image pairs, repeated runs, or repeated trials are
> evaluated.

### Number of Runs

`[TBD]`

### Number of Image Pairs

`[TBD]`

### Aggregation Method

`[Mean / Median / Distribution / Per-pair reporting / Other / TBD]`

### Variability

| Metric            |    Mean |  Median | Std. Dev. |     Min |     Max |
| ----------------- | ------: | ------: | --------: | ------: | ------: |
| Candidate matches | `[TBD]` | `[TBD]` |   `[TBD]` | `[TBD]` | `[TBD]` |
| Inlier ratio      | `[TBD]` | `[TBD]` |   `[TBD]` | `[TBD]` | `[TBD]` |
| Coverage          | `[TBD]` | `[TBD]` |   `[TBD]` | `[TBD]` | `[TBD]` |
| Check-point RMSE  | `[TBD]` | `[TBD]` |   `[TBD]` | `[TBD]` | `[TBD]` |
| Runtime           | `[TBD]` | `[TBD]` |   `[TBD]` | `[TBD]` | `[TBD]` |

Do not report aggregate statistics without recording the underlying
evaluation population.

---

## 38. Quality Control

### Input QC

- [ ] Correct image pair confirmed
- [ ] Correct sensor metadata confirmed
- [ ] GSD confirmed from authoritative metadata
- [ ] Projection confirmed
- [ ] Geographic overlap confirmed
- [ ] Illumination metadata checked
- [ ] No corrupted inputs

### Processing QC

- [ ] Preprocessing recorded
- [ ] Dimensions recorded
- [ ] Resampling recorded
- [ ] Interpolation recorded
- [ ] Scale levels recorded
- [ ] Effective GSD recorded
- [ ] Configuration version recorded

### Correspondence QC

- [ ] Candidate matches recorded
- [ ] Candidate matches separated from verified inliers
- [ ] Geometric verification performed
- [ ] Inlier ratio recorded
- [ ] Spatial coverage recorded

### Evaluation QC

- [ ] Check points identified
- [ ] Fit/evaluation separation verified
- [ ] RMSE reported in source pixels
- [ ] Ground conversion justified if used
- [ ] Runtime recorded
- [ ] Failures recorded

---

## 39. Scientific Integrity Checks

Before accepting the experiment report, verify:

- [ ] No invented measurements
- [ ] No invented image identifiers
- [ ] No invented GSD values
- [ ] No unsupported success claims
- [ ] Expected results separated from measured results
- [ ] Candidate matches separated from verified inliers
- [ ] Control points separated from independent check points
- [ ] Fitting error not incorrectly called independent registration error
- [ ] Upsampling not described as recovering missing detail
- [ ] Physical scale considered rather than pixel count alone
- [ ] Sensor-specific preprocessing documented
- [ ] IIRS representation documented if applicable
- [ ] Illumination differences documented
- [ ] Spatial coverage evaluated
- [ ] Visual overlay not used as the only evidence
- [ ] Flexible warping does not conceal weak correspondences
- [ ] Failures reported
- [ ] Reproducibility information recorded

---

## 40. Known Limitations

Document known limitations before interpreting results.

### Data Limitations

- `[TBD]`

### Sensor Limitations

- `[TBD]`

### Scale Limitations

- `[TBD]`

### Illumination Limitations

- `[TBD]`

### Geometric Limitations

- `[TBD]`

### Ground-Truth Limitations

- `[TBD]`

### Computational Limitations

- `[TBD]`

### Evaluation Limitations

- `[TBD]`

---

## 41. Known Gaps

| Gap     | Status                       | Impact  | Planned Resolution |
| ------- | ---------------------------- | ------- | ------------------ |
| `[TBD]` | `[Open / Planned / Blocked]` | `[TBD]` | `[TBD]`            |
| `[TBD]` | `[Open / Planned / Blocked]` | `[TBD]` | `[TBD]`            |

---

## 42. Interpretation

### Main Observation

`[TBD]`

### Evidence Supporting the Observation

- `[Metric / result]`
- `[Metric / result]`
- `[Visualization / artifact]`

### Evidence Against the Observation

- `[Metric / result]`
- `[Failure case]`
- `[Limitation]`

### Interpretation Boundaries

`[State what the experiment does and does not establish.]`

> Do not claim general superiority from a single image pair or a single
> experimental condition unless the benchmark design supports that
> conclusion.

---

## 43. Conclusion

### Research Question Answer

`[TBD]`

### Hypothesis Status

`[Supported / Not supported / Inconclusive / Partially supported / Not yet tested]`

### Main Finding

`[TBD]`

### Quantitative Evidence

`[TBD]`

### Main Failure Mode

`[TBD]`

### Practical Implication for ChandraMap

`[TBD]`

### What This Experiment Does Not Establish

`[TBD]`

---

## 44. Next Experiment

### Proposed Follow-Up

`[TBD]`

### Motivation

`[TBD]`

### Variables to Change

- `[TBD]`

### Variables to Keep Fixed

- `[TBD]`

### Expected Question

> `[TBD]`

---

## 45. Reproducibility Record

Complete this section after the experiment has been executed.

```text
Experiment ID:
Experiment Name:
Status:
Date:
Author:

Git Commit:
Branch / Tag:

Dataset:
Dataset Version:

Source Image:
Reference Image:

Source Sensor:
Reference Sensor:

Source GSD:
Reference GSD:
Effective Comparison GSD:

Preprocessing:
Scale Configuration:
Feature Configuration:
Matcher Configuration:
Geometric Verification:
Transformation:

Random Seed:

Python Version:
OpenCV Version:
Operating System:

CPU:
GPU:
CUDA:

Execution Command:

Output Artifact Root:

Independent Check-Point Source:

Notes:
```

---

## 46. Experiment Sign-Off

| Review Item                          | Status                | Reviewer / Evidence |
| ------------------------------------ | --------------------- | ------------------- |
| Objective clearly defined            | `[PASS / FAIL / TBD]` | `[TBD]`             |
| Research question defined            | `[PASS / FAIL / TBD]` | `[TBD]`             |
| Hypothesis defined                   | `[PASS / FAIL / TBD]` | `[TBD]`             |
| Dataset identified                   | `[PASS / FAIL / TBD]` | `[TBD]`             |
| Sensor configuration documented      | `[PASS / FAIL / TBD]` | `[TBD]`             |
| Preprocessing documented             | `[PASS / FAIL / TBD]` | `[TBD]`             |
| Scale strategy documented            | `[PASS / FAIL / TBD]` | `[TBD]`             |
| Candidate matches documented         | `[PASS / FAIL / TBD]` | `[TBD]`             |
| Geometric verification documented    | `[PASS / FAIL / TBD]` | `[TBD]`             |
| Independent evaluation documented    | `[PASS / FAIL / TBD]` | `[TBD]`             |
| Metrics recorded                     | `[PASS / FAIL / TBD]` | `[TBD]`             |
| Failure cases documented             | `[PASS / FAIL / TBD]` | `[TBD]`             |
| Reproducibility information recorded | `[PASS / FAIL / TBD]` | `[TBD]`             |
| No fabricated results                | `[PASS / FAIL / TBD]` | `[TBD]`             |
| Scientific integrity checks passed   | `[PASS / FAIL / TBD]` | `[TBD]`             |

---

## 47. Status Definitions

Use the following status values consistently.

| Status              | Meaning                                                                                |
| ------------------- | -------------------------------------------------------------------------------------- |
| `Planned`           | Experiment is defined but execution has not started.                                   |
| `In Progress`       | Experiment implementation or execution is underway.                                    |
| `Completed`         | Experiment has been executed and documented with measured results.                     |
| `Blocked`           | Experiment cannot currently be completed because a required dependency is unavailable. |
| `Archived`          | Experiment is retained for historical or reproducibility purposes.                     |
| `[Not implemented]` | Required functionality does not currently exist in the implementation.                 |
| `[Planned]`         | Functionality is intended but has not been implemented.                                |
| `[TBD]`             | Value has not yet been determined.                                                     |
| `Not measured`      | The quantity exists conceptually but was not measured.                                 |
| `N/A`               | The quantity is not applicable to this experiment.                                     |
| `Not available`     | Required source information is unavailable.                                            |

---

## 48. Scientific Reporting Rules

The following rules apply to every experiment using this template.

### 48.1 Do Not Fabricate Measurements

Use:

- `TBD`
- `N/A`
- `Not measured`
- `Not available`
- `[Not implemented]`
- `[Planned]`

instead of invented values.

### 48.2 Candidate Matches Are Not Verified Inliers

Use the terminology:

```text
Candidate Matches
        ↓
Geometric Verification
        ↓
Verified Inliers
```

Do not report candidate-match count as geometric correctness.

### 48.3 Independent Evaluation Matters

Use:

```text
Control / Fit Points
        ↓
Transformation Estimation
        ↓
Independent Check Points
        ↓
Registration Error
```

Do not use the same points for both transformation fitting and independent
evaluation when independent check points are available.

### 48.4 Pixel Error Comes First

Report registration accuracy in source-image pixels first where applicable.

Only convert to metres when the GSD, projection, coordinate system, and
reference truth support the conversion.

### 48.5 More Matches Are Not Automatically Better

A method producing more candidate matches may still perform worse if:

- the matches are incorrect;
- the inlier ratio decreases;
- the spatial distribution is poor;
- the independent RMSE increases;
- the matches cluster in one small region.

### 48.6 Visual Alignment Is Supporting Evidence

Overlay, mosaic, and visualization results should accompany quantitative
evaluation rather than replace it.

### 48.7 Flexible Warps Must Not Hide Weak Correspondences

A flexible transformation should not be used to make poor candidate
correspondences appear successful.

### 48.8 Sensor Differences Must Remain Explicit

OHRC, TMC-2, IIRS, LRO NAC, LRO WAC, and other products must not be treated
as interchangeable without documenting the relevant sensor assumptions.

### 48.9 Upsampling Does Not Recover Missing Information

> **Upsampling changes pixel count; it does not recover spatial information
> that was absent from the source sensor.**

### 48.10 Illumination Normalization Is Not Illumination Invariance

Brightness, contrast, or histogram normalization does not recreate terrain
shadows that changed because of Sun-angle geometry.

### 48.11 Report Failures Honestly

Failed registrations, low inlier ratios, poor spatial coverage, large
check-point errors, and stress-test degradation are valid experimental
results and must be retained.

---

## 49. Experiment Completion Checklist

### Definition

- [ ] Experiment ID assigned
- [ ] Experiment name assigned
- [ ] Objective documented
- [ ] Research question documented
- [ ] Hypothesis documented
- [ ] Variables documented

### Data

- [ ] Dataset identified
- [ ] Dataset version recorded
- [ ] Image pair recorded
- [ ] Sensor metadata recorded
- [ ] GSD recorded
- [ ] Projection recorded
- [ ] Illumination metadata recorded
- [ ] Ground-truth/check-point source recorded

### Processing

- [ ] Preprocessing documented
- [ ] Scale handling documented
- [ ] Effective comparison scale documented
- [ ] Interpolation documented
- [ ] Feature extraction documented
- [ ] Matching documented
- [ ] Geometric verification documented
- [ ] Transformation documented
- [ ] Sub-pixel refinement status documented

### Evaluation

- [ ] Candidate matches measured
- [ ] Verified inliers measured
- [ ] Inlier ratio measured
- [ ] Spatial coverage measured
- [ ] Independent check-point evaluation performed or explicitly marked unavailable
- [ ] RMSE measured where applicable
- [ ] Ground error conversion justified where applicable
- [ ] Runtime measured
- [ ] Failure rate recorded

### Scientific Integrity

- [ ] No fabricated results
- [ ] No unsupported claims
- [ ] Control and experimental conditions documented
- [ ] Confounding changes documented
- [ ] Fit points separated from check points
- [ ] Physical scale considered
- [ ] Upsampling limitation acknowledged
- [ ] Sensor-specific assumptions documented
- [ ] Illumination limitations documented
- [ ] Failures retained

### Reproducibility

- [ ] Git commit recorded
- [ ] Dataset version recorded
- [ ] Configuration recorded
- [ ] Random seed recorded
- [ ] Software versions recorded
- [ ] Hardware recorded
- [ ] Execution command recorded
- [ ] Output artifacts recorded
- [ ] Visualizations recorded

---

## 50. References Within ChandraMap

Use only repository documents that are confirmed to exist in the project
when adding experiment-specific references.

Known project documentation relevant to experiment reporting includes:

- `experiments/templates/EXPERIMENT_TEMPLATE.md`
- `experiments/v1/README.md`
- `benchmarks/README.md`
- `benchmarks/v1/README.md`
- `benchmarks/v1/BENCHMARK_SPEC.md`
- `benchmarks/v1/GROUND_TRUTH_PROTOCOL.md`
- `benchmarks/v1/STRESS_TESTS.md`
- `benchmarks/v1/ACCEPTANCE_CRITERIA.md`
- `benchmarks/v1/REPRODUCIBILITY.md`
- `benchmarks/baselines/README.md`
- `benchmarks/baselines/ground_truth/README.md`
- `benchmarks/baselines/expected/README.md`
- `data/README.md`
- `data/raw/README.md`
- `data/external/README.md`
- `data/interim/README.md`
- `data/processed/README.md`
- `data/ground_truth/README.md`
- `data/ground_truth/CONTROL_POINTS.md`

If a referenced file is not present in the actual repository, remove the
reference rather than creating an implied dependency.

---

## 51. Template Usage Notes

When creating a new experiment from this template:

1. Copy `EXPERIMENT_TEMPLATE.md` into the appropriate experiment directory.
2. Assign the experiment ID defined by the repository experiment structure.
3. Replace `[TBD]` values only with verified information.
4. Record the exact dataset and image identifiers.
5. Record the exact Git commit used for execution.
6. Record preprocessing and configuration changes.
7. Separate candidate matches from verified inliers.
8. Separate transformation-fitting points from independent check points.
9. Report source-pixel registration error before any justified ground-distance conversion.
10. Record spatial coverage.
11. Record runtime and failures.
12. Preserve visual evidence alongside quantitative evidence.
13. Record the execution command and environment.
14. Do not convert planned functionality into an implementation claim.
15. Do not remove failed experiments simply because the result is unfavorable.

---

## 52. Final Experiment Record

```text
Experiment ID:
Experiment Name:

Research Question:

Primary Hypothesis:

Dataset:

Source Sensor:
Reference Sensor:

Source Image:
Reference Image:

Source GSD:
Reference GSD:
Effective Comparison Scale:

Preprocessing:

Scale Strategy:

Feature Extractor:

Matcher:

Candidate Matches:

Geometric Model:

Verified Inliers:

Inlier Ratio:

Spatial Coverage:

Independent Check-Point RMSE:

Ground Error:
[Only when scientifically justified]

Runtime:

Failure Rate:

Main Finding:

Main Failure:

Hypothesis Status:

Reproducibility Status:

Next Experiment:
```

> **Final reporting principle:** ChandraMap experiments should make it
> possible for another researcher to determine **what changed, what was
> measured, under which conditions it was measured, how the transformation
> was verified, whether the evaluation was independent, what failed, and
> whether the result can be reproduced.**
