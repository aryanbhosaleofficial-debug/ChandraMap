# Experiment: [Experiment Name]

> **Template file:** `experiments/templates/EXPERIMENT_TEMPLATE.md`
> **Project:** ChandraMap
> **Purpose:** Reproducible experiment documentation template
> **Status:** Template — replace all applicable `[PLACEHOLDER]` values before treating a copied document as an experiment report.

---

## Experiment Metadata

| Field                | Value                                                                   |
| -------------------- | ----------------------------------------------------------------------- |
| Experiment ID        | `[EXP-XXXX]`                                                            |
| Experiment Name      | `[Experiment Name]`                                                     |
| Experiment Type      | `[Baseline / Ablation / Stress Test / Comparison / Validation / Other]` |
| Status               | `[Planned / Running / Completed / Failed / Aborted]`                    |
| Author               | `[Name]`                                                                |
| Date                 | `[YYYY-MM-DD]`                                                          |
| Git Commit / Version | `[Commit SHA / Version]`                                                |
| Branch / Tag         | `[Branch or Tag]`                                                       |
| Dataset Version      | `[Dataset Version]`                                                     |
| Benchmark Version    | `[Benchmark Version / N/A]`                                             |
| Configuration File   | `[Path / N/A]`                                                          |
| Random Seed          | `[Seed / N/A]`                                                          |
| Hardware             | `[GPU / CPU / RAM / Other]`                                             |
| Software Environment | `[Environment / Container / Python Version / Dependencies]`             |
| Execution Command    | `[Exact Command]`                                                       |
| Related Issue / PR   | `[Issue / PR / N/A]`                                                    |
| Related Experiment   | `[Experiment ID / N/A]`                                                 |
| Parent Experiment    | `[Experiment ID / N/A]`                                                 |
| Experiment Group     | `[Group Name / N/A]`                                                    |

### Experiment Status Definitions

Use one of the following where applicable:

- **Planned** — experiment has been specified but not executed.
- **Running** — experiment is currently being executed.
- **Completed** — experiment completed and results were recorded.
- **Failed** — execution occurred but did not produce a valid experimental result.
- **Aborted** — execution was intentionally stopped before completion.

Do not mark an experiment as **Completed** when metrics, configuration, or reproducibility information are still missing.

---

# 1. Objective

## 1.1 What Is Being Tested?

`[Describe the method, component, parameter, pipeline stage, or scientific condition being evaluated.]`

## 1.2 Why Is This Experiment Necessary?

`[Explain the scientific or engineering motivation.]`

## 1.3 Hypothesis Being Evaluated

`[State the hypothesis in testable form.]`

## 1.4 Specific Question

`[State the precise question this experiment should answer.]`

## 1.5 Scope

### Included

- `[Condition / sensor / dataset / method]`
- `[Condition]`
- `[Condition]`

### Excluded

- `[Condition]`
- `[Condition]`

## 1.6 Expected vs Measured Outcome

| Item             | Description                                  |
| ---------------- | -------------------------------------------- |
| Hypothesis       | `[What is expected to happen?]`              |
| Expected Outcome | `[Expected direction or qualitative result]` |
| Measured Outcome | `[Fill only after execution]`                |
| Interpretation   | `[Evidence-based interpretation]`            |

> **Important:** Do not replace the measured outcome with the expected outcome after execution.

---

# 2. Research Question

> Does `[method / change]` improve `[metric / outcome]` under `[specific condition]` compared with `[baseline / control]`?

### Research Question

`[Write the final experiment-specific research question.]`

### Comparison

| Component      | Experiment          | Baseline / Control       |
| -------------- | ------------------- | ------------------------ |
| Method         | `[Method]`          | `[Baseline]`             |
| Dataset        | `[Dataset]`         | `[Same dataset]`         |
| Image Pairs    | `[Pairs]`           | `[Same pairs]`           |
| Evaluation Set | `[Check-point set]` | `[Same check-point set]` |
| Metrics        | `[Metrics]`         | `[Same metrics]`         |

> Use the same evaluation conditions when comparing methods unless the experiment explicitly investigates a different condition.

---

# 3. Hypothesis

## 3.1 Primary Hypothesis

`[State the primary hypothesis.]`

## 3.2 Secondary Hypotheses

1. `[Secondary hypothesis]`
2. `[Secondary hypothesis]`
3. `[Secondary hypothesis]`

## 3.3 Expected Direction of Change

| Metric            | Expected Direction                                   | Rationale  |
| ----------------- | ---------------------------------------------------- | ---------- |
| Candidate Matches | `[Increase / Decrease / No predetermined direction]` | `[Reason]` |
| Verified Inliers  | `[Direction]`                                        | `[Reason]` |
| Inlier Ratio      | `[Direction]`                                        | `[Reason]` |
| Spatial Coverage  | `[Direction]`                                        | `[Reason]` |
| Check-Point RMSE  | `[Direction]`                                        | `[Reason]` |
| Runtime           | `[Direction / Trade-off expected]`                   | `[Reason]` |
| Failure Rate      | `[Direction]`                                        | `[Reason]` |

## 3.4 Conditions Under Which the Hypothesis May Fail

- `[Scale condition]`
- `[Illumination condition]`
- `[Sensor/modality condition]`
- `[Geometric condition]`
- `[Low-feature terrain condition]`
- `[Other known limitation]`

> Do not assume that a more complex or advanced method will necessarily outperform a baseline. The experiment should measure the difference.

---

# 4. Experimental Variables

| Variable                 | Type                  | Value           | Notes              |
| ------------------------ | --------------------- | --------------- | ------------------ |
| Sensor                   | Independent           | `[Value]`       | `[Notes]`          |
| Image Pair               | Control / Independent | `[Value]`       | `[Notes]`          |
| Source GSD               | Control               | `[Value]`       | `[Units / source]` |
| Reference GSD            | Control               | `[Value]`       | `[Units / source]` |
| Effective Comparison GSD | Independent           | `[Value]`       | `[Rationale]`      |
| Scale Factor             | Independent           | `[Value]`       | `[Notes]`          |
| Sun Angle                | Independent           | `[Value]`       | `[Notes]`          |
| Illumination Condition   | Independent           | `[Value]`       | `[Notes]`          |
| Viewpoint                | Independent / Control | `[Value]`       | `[Notes]`          |
| Preprocessing            | Independent           | `[Value]`       | `[Notes]`          |
| Representation           | Independent           | `[Value]`       | `[Notes]`          |
| Matcher                  | Independent           | `[Value]`       | `[Version]`        |
| Feature Extractor        | Independent           | `[Value]`       | `[Version]`        |
| Descriptor               | Independent           | `[Value]`       | `[Version]`        |
| Retrieval Method         | Independent           | `[Value / N/A]` | `[Notes]`          |
| Geometric Model          | Independent           | `[Value]`       | `[Notes]`          |
| RANSAC Threshold         | Independent           | `[Value]`       | `[Units]`          |
| Sub-Pixel Refinement     | Independent           | `[Value / N/A]` | `[Notes]`          |
| Pyramid Level            | Independent           | `[Value]`       | `[Notes]`          |
| Image Size               | Control               | `[Value]`       | `[Dimensions]`     |
| Search Window            | Independent           | `[Value / N/A]` | `[Notes]`          |
| Random Seed              | Control               | `[Value / N/A]` | `[Notes]`          |

## 4.1 Independent Variables

`[List variables intentionally changed between experimental conditions.]`

## 4.2 Dependent Variables

`[List quantities measured as outcomes.]`

Recommended registration metrics include:

- Candidate match count
- Verified inlier count
- Inlier ratio
- Spatial coverage
- Independent check-point RMSE
- Ground error when physically meaningful
- Runtime
- Failure rate

## 4.3 Control Variables

`[List variables held constant.]`

## 4.4 Confounding Factors

`[Identify factors that could influence the result but are not the primary experimental variable.]`

---

# 5. Dataset

## 5.1 Dataset Identification

| Field                             | Value                |
| --------------------------------- | -------------------- |
| Dataset Name                      | `[Name]`             |
| Dataset Version                   | `[Version]`          |
| Source                            | `[Source]`           |
| Sensor                            | `[Sensor]`           |
| Product Type                      | `[Product Type]`     |
| Number of Image Pairs             | `[Count]`            |
| Spatial Region                    | `[Region]`           |
| Geographic Coverage               | `[Coverage / N/A]`   |
| GSD / Pixel Scale                 | `[Value]`            |
| Projection                        | `[Projection / N/A]` |
| Train Split                       | `[Value / N/A]`      |
| Validation Split                  | `[Value / N/A]`      |
| Test Split                        | `[Value / N/A]`      |
| Ground Truth / Check-Point Source | `[Source / N/A]`     |

## 5.2 Image Pairs

| Pair ID      | Source Image ID | Reference Image ID | Source Sensor | Reference Sensor | Source GSD | Reference GSD | Condition     |
| ------------ | --------------- | ------------------ | ------------- | ---------------- | ---------- | ------------- | ------------- |
| `[PAIR-001]` | `[ID]`          | `[ID]`             | `[Sensor]`    | `[Sensor]`       | `[Value]`  | `[Value]`     | `[Condition]` |
| `[PAIR-002]` | `[ID]`          | `[ID]`             | `[Sensor]`    | `[Sensor]`       | `[Value]`  | `[Value]`     | `[Condition]` |

## 5.3 Metadata Availability

| Metadata                  | Available? | Value / Source | Notes     |
| ------------------------- | ---------- | -------------- | --------- |
| Acquisition time          | `[Yes/No]` | `[Value]`      | `[Notes]` |
| Sun angle                 | `[Yes/No]` | `[Value]`      | `[Notes]` |
| Emission / view angle     | `[Yes/No]` | `[Value]`      | `[Notes]` |
| Phase angle               | `[Yes/No]` | `[Value]`      | `[Notes]` |
| GSD                       | `[Yes/No]` | `[Value]`      | `[Notes]` |
| Projection                | `[Yes/No]` | `[Value]`      | `[Notes]` |
| Sensor metadata           | `[Yes/No]` | `[Value]`      | `[Notes]` |
| Terrain / region metadata | `[Yes/No]` | `[Value]`      | `[Notes]` |

## 5.4 Existing Preprocessing

`[Document any preprocessing already applied before this experiment.]`

## 5.5 Dataset Selection Rationale

`[Explain why these image pairs and datasets were selected.]`

### Selection Criteria

- `[Criterion]`
- `[Criterion]`
- `[Criterion]`

> **Dataset integrity requirement:** Do not silently mix different dataset versions, image versions, coordinate systems, or preprocessing pipelines.

> If a dataset has been modified, record the new dataset version or clearly document the modification.

---

# 6. Sensor Configuration

ChandraMap experiments should treat sensors according to their actual characteristics.

Supported experiment categories may include:

- OHRC
- TMC-2
- IIRS
- LRO NAC
- LRO WAC
- Kaguya/SELENE TC, when explicitly included
- Synthetic lunar data, when explicitly included

Not every experiment needs to use every sensor.

## 6.1 Source Sensor

| Field                           | Value                                              |
| ------------------------------- | -------------------------------------------------- |
| Sensor                          | `[OHRC / TMC-2 / IIRS / Other]`                    |
| Modality                        | `[Visible / Panchromatic / Hyperspectral / Other]` |
| Spatial Resolution              | `[Value]`                                          |
| GSD                             | `[Value]`                                          |
| Spectral Representation         | `[Value]`                                          |
| Expected Structural Information | `[Description]`                                    |
| Sensor-Specific Preprocessing   | `[Description]`                                    |
| Metadata Availability           | `[Description]`                                    |

## 6.2 Reference Sensor

| Field                           | Value                         |
| ------------------------------- | ----------------------------- |
| Sensor                          | `[LRO NAC / LRO WAC / Other]` |
| Modality                        | `[Value]`                     |
| Spatial Resolution              | `[Value]`                     |
| GSD                             | `[Value]`                     |
| Spectral Representation         | `[Value]`                     |
| Expected Structural Information | `[Description]`               |
| Sensor-Specific Preprocessing   | `[Description]`               |
| Metadata Availability           | `[Description]`               |

> **Scientific requirement:** Do not force OHRC, TMC-2, and IIRS through an identical preprocessing pipeline without experimental justification.

### IIRS Representation

If IIRS is used, document how the data are represented for image correspondence:

- `[Selected band]`
- `[Band combination]`
- `[PCA representation]`
- `[Composite]`
- `[Structural representation]`
- `[Other documented representation]`

`[Explain why this representation was selected.]`

---

# 7. Preprocessing

Document every preprocessing operation performed before correspondence or registration.

| Step | Method     | Parameters     | Applied To                | Purpose     |
| ---- | ---------- | -------------- | ------------------------- | ----------- |
| 1    | `[Method]` | `[Parameters]` | `[Source/Reference/Both]` | `[Purpose]` |
| 2    | `[Method]` | `[Parameters]` | `[Source/Reference/Both]` | `[Purpose]` |
| 3    | `[Method]` | `[Parameters]` | `[Source/Reference/Both]` | `[Purpose]` |

## 7.1 Possible Operations

Record only operations actually used.

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
- Spectral-to-structural conversion
- Map projection
- Orthorectification
- Resampling
- Cropping
- Masking
- Normalization
- Other: `[Specify]`

## 7.2 Image Dimensions

| Image     | Before    | After     | Change Reason |
| --------- | --------- | --------- | ------------- |
| Source    | `[W × H]` | `[W × H]` | `[Reason]`    |
| Reference | `[W × H]` | `[W × H]` | `[Reason]`    |

## 7.3 GSD Changes

| Image     | Before GSD | After GSD | Method     | Reason     |
| --------- | ---------- | --------- | ---------- | ---------- |
| Source    | `[Value]`  | `[Value]` | `[Method]` | `[Reason]` |
| Reference | `[Value]`  | `[Value]` | `[Method]` | `[Reason]` |

## 7.4 Coordinate and Metadata Changes

| Item              | Before    | After     | Reason     |
| ----------------- | --------- | --------- | ---------- |
| Coordinate System | `[Value]` | `[Value]` | `[Reason]` |
| Projection        | `[Value]` | `[Value]` | `[Reason]` |
| Image Origin      | `[Value]` | `[Value]` | `[Reason]` |
| Metadata          | `[Value]` | `[Value]` | `[Reason]` |

---

# 8. Scale / Multi-Resolution Strategy

## 8.1 Scale Configuration

| Parameter                | Value            |
| ------------------------ | ---------------- |
| Source GSD               | `[Value]`        |
| Reference GSD            | `[Value]`        |
| Effective Comparison GSD | `[Value]`        |
| Scale Factor             | `[Value]`        |
| Pyramid Levels           | `[Values]`       |
| Downsampling             | `[Method / N/A]` |
| Upsampling               | `[Method / N/A]` |
| Interpolation            | `[Method / N/A]` |

## 8.2 Scale Rationale

`[Explain why the selected comparison scale is physically meaningful.]`

## 8.3 Multi-Resolution Strategy

`[Describe the pyramid or multi-scale representation.]`

### Pyramid

| Level | Scale     | Image Size | Effective GSD | Purpose     |
| ----- | --------- | ---------- | ------------- | ----------- |
| `[0]` | `[1×]`    | `[W × H]`  | `[GSD]`       | `[Purpose]` |
| `[1]` | `[Value]` | `[W × H]`  | `[GSD]`       | `[Purpose]` |

> **Scientific warning:** Upsampling changes pixel count; it does **not** recover spatial information that was absent from the source sensor.

Do not claim that upsampling a coarse product creates missing fine terrain detail.

A high-resolution reference may instead need to be downsampled or represented through a multi-resolution pyramid so that coarse matching occurs at a physically meaningful effective scale.

## 8.4 Scale-Related Limitations

`[Document information loss, aliasing, resolution mismatch, or other scale limitations.]`

---

# 9. Illumination / Sun-Angle Conditions

## 9.1 Illumination Configuration

| Parameter              | Source          | Reference       |
| ---------------------- | --------------- | --------------- |
| Illumination Condition | `[Value]`       | `[Value]`       |
| Sun Incidence Angle    | `[Value / N/A]` | `[Value / N/A]` |
| Emission / View Angle  | `[Value / N/A]` | `[Value / N/A]` |
| Phase Angle            | `[Value / N/A]` | `[Value / N/A]` |
| Acquisition Time       | `[Value / N/A]` | `[Value / N/A]` |

## 9.2 Illumination Category

Select one where applicable:

- `[ ]` Similar lighting
- `[ ]` Moderately different lighting
- `[ ]` Strongly different lighting
- `[ ]` Unknown
- `[ ]` Other: `[Specify]`

## 9.3 Illumination Normalization

| Method     | Parameters     | Applied To | Purpose     |
| ---------- | -------------- | ---------- | ----------- |
| `[Method]` | `[Parameters]` | `[Image]`  | `[Purpose]` |

## 9.4 Structure-Focused Representation

`[Describe edge/gradient/phase/structural representation if used.]`

## 9.5 Shadow Handling

`[Describe whether shadows are retained, masked, normalized, or otherwise handled.]`

> **Scientific warning:** Brightness or contrast normalization is not equivalent to solving Sun-angle differences. It cannot recreate terrain shadows that changed because of illumination geometry.

## 9.6 Stress-Test Result

| Illumination Condition | Metric     |  Result | Change vs Baseline |
| ---------------------- | ---------- | ------: | -----------------: |
| Similar                | `[Metric]` | `[TBD]` |            `[TBD]` |
| Moderate Difference    | `[Metric]` | `[TBD]` |            `[TBD]` |
| Strong Difference      | `[Metric]` | `[TBD]` |            `[TBD]` |

---

# 10. Matching Method

## 10.1 Method Configuration

| Method     | Extractor     | Matcher     | Parameters     | Version     |
| ---------- | ------------- | ----------- | -------------- | ----------- |
| `[Method]` | `[Extractor]` | `[Matcher]` | `[Parameters]` | `[Version]` |

## 10.2 Baseline

Possible baseline components include:

- SIFT
- Descriptor matching
- Ratio test
- Cross-check

### Baseline Configuration

`[Document exact baseline configuration.]`

## 10.3 Learned / Advanced Methods

Possible methods include:

- ALIKED + LightGlue
- LoFTR

`[Document the actual method used.]`

## 10.4 Research Directions

Possible research directions include:

- RIFT
- CFOG
- Other documented methods: `[Specify]`

Only record methods actually evaluated in the experiment.

## 10.5 Why This Method Was Selected

`[Scientific/engineering rationale.]`

## 10.6 Expected Strengths

- `[Expected strength]`
- `[Expected strength]`

## 10.7 Expected Failure Modes

- `[Failure mode]`
- `[Failure mode]`

> Do not assume that a more advanced matcher is automatically better. Compare methods using the same evaluation conditions where possible.

---

# 11. Global Retrieval

> **Optional section:** Complete this section only when global retrieval is used or explicitly evaluated.

Global retrieval is relevant when the corresponding region is not already known through reliable metadata.

If the source and reference region are already constrained by reliable metadata or footprint information, global retrieval may not be necessary.

## 11.1 Retrieval Configuration

| Parameter           | Value                |
| ------------------- | -------------------- |
| Retrieval Used      | `[Yes / No]`         |
| Reference Tiles     | `[Description]`      |
| Tile Size           | `[Value]`            |
| Pyramid Level       | `[Value]`            |
| Global Descriptor   | `[Method]`           |
| Embedding Dimension | `[Value]`            |
| FAISS Index Type    | `[Type / N/A]`       |
| Index Parameters    | `[Parameters / N/A]` |
| Candidate Count K   | `[Value]`            |
| Metadata Filtering  | `[Method / N/A]`     |

## 11.2 Retrieval Metrics

| Metric                 |        Result |
| ---------------------- | ------------: |
| Recall@1               |       `[TBD]` |
| Recall@5               |       `[TBD]` |
| Other Retrieval Metric | `[TBD / N/A]` |

## 11.3 Retrieval vs Local Correspondence

### Global Retrieval Features

`[Describe global descriptors/embeddings used to retrieve candidate regions.]`

### Local Correspondence Features

`[Describe local features/descriptors/matching features used after retrieval.]`

> **Important:** Global retrieval features and local correspondence features represent different pipeline stages and must not be reported as though they are the same feature representation.

---

# 12. Candidate Matches

Candidate matches are raw or preliminary correspondences produced before geometric verification.

## 12.1 Candidate-Match Configuration

| Parameter                   | Value                        |
| --------------------------- | ---------------------------- |
| Candidate Matching Method   | `[Method]`                   |
| Number of Candidate Matches | `[TBD]`                      |
| Matching Threshold          | `[Value]`                    |
| Confidence Threshold        | `[Value / N/A]`              |
| Descriptor Distance         | `[Value / Distribution]`     |
| Cross-Check                 | `[Enabled / Disabled / N/A]` |
| Ratio Test                  | `[Enabled / Disabled / N/A]` |
| Visualization Path          | `[Path / N/A]`               |

## 12.2 Candidate Match Statistics

| Metric                     |  Result |
| -------------------------- | ------: |
| Candidate Matches          | `[TBD]` |
| Rejected Before Geometry   | `[TBD]` |
| Candidate Match Density    | `[TBD]` |
| Candidate Spatial Coverage | `[TBD]` |

## 12.3 Candidate Match Distribution

`[Describe whether candidate matches are spatially distributed or concentrated in particular regions.]`

## 12.4 Candidate Match Visualization

`[Path to visualization / N/A]`

> **Critical distinction:** Candidate matches are **not equivalent to geometrically verified inliers**.

Matcher confidence alone does not establish geometric correctness.

---

# 13. Geometric Verification

## 13.1 Configuration

| Parameter              | Value                           |
| ---------------------- | ------------------------------- |
| Geometric Model        | `[Affine / Homography / Other]` |
| RANSAC Variant         | `[Variant]`                     |
| Reprojection Threshold | `[Value]`                       |
| Confidence             | `[Value]`                       |
| Maximum Iterations     | `[Value]`                       |
| Minimum Inliers        | `[Value]`                       |
| Random Seed            | `[Value / N/A]`                 |

## 13.2 Results

| Metric            |  Result |
| ----------------- | ------: |
| Candidate Matches | `[TBD]` |
| Verified Inliers  | `[TBD]` |
| Inlier Ratio      | `[TBD]` |
| Median Residual   | `[TBD]` |
| Mean Residual     | `[TBD]` |
| Maximum Residual  | `[TBD]` |
| Spatial Coverage  | `[TBD]` |

### Inlier Ratio

`[Document the exact project-defined calculation or reference the applicable benchmark metric definition.]`

## 13.3 Why This Geometric Model Was Chosen

`[Explain the geometric assumptions and why the selected model is appropriate.]`

## 13.4 Assumptions

- `[Assumption]`
- `[Assumption]`

## 13.5 Failure Conditions

- `[Failure condition]`
- `[Failure condition]`

> Do not report candidate-match count as though it were verified-inlier count.

---

# 14. Sub-Pixel Refinement

## 14.1 Configuration

| Parameter                  | Value                   |
| -------------------------- | ----------------------- |
| Refinement Used            | `[Yes / No]`            |
| Refinement Method          | `[Method]`              |
| Patch Size                 | `[Value]`               |
| Interpolation              | `[Method]`              |
| Correlation / Phase Method | `[Method / N/A]`        |
| Points Refined             | `[Count / Description]` |
| Convergence Criteria       | `[Criteria]`            |
| Maximum Iterations         | `[Value]`               |

## 14.2 Required Processing Order

The experiment must preserve the following sequence:

```text
Candidate Matches
        ↓
Geometric Verification / RANSAC
        ↓
Verified Inliers
        ↓
Sub-Pixel Refinement
        ↓
Final Transformation Refitting
        ↓
Independent Check-Point Evaluation
```

Sub-pixel refinement should operate on verified control points/inliers rather than blindly refining every candidate match.

## 14.3 Before / After Measurements

| Metric             | Before Refinement | After Refinement |
| ------------------ | ----------------: | ---------------: |
| Verified Inliers   |           `[TBD]` |          `[TBD]` |
| Residual Statistic |           `[TBD]` |          `[TBD]` |
| Check-Point RMSE   |           `[TBD]` |          `[TBD]` |
| Spatial Coverage   |           `[TBD]` |          `[TBD]` |

> A lower fitting error after refinement does not by itself prove better independent registration. Check-point performance must be evaluated separately.

---

# 15. Final Transformation

## 15.1 Transformation Configuration

| Parameter             | Value                  |
| --------------------- | ---------------------- |
| Transformation Type   | `[Type]`               |
| Source Direction      | `[Source → Reference]` |
| Coordinate Convention | `[Convention]`         |
| Final Refit Method    | `[Method]`             |
| Number of Fit Points  | `[Count]`              |
| Refinement Applied    | `[Yes / No]`           |

## 15.2 Transformation Matrix

Record the actual final transformation here.

```text
[TBD]
```

If the transformation has more parameters than a simple matrix, document the complete parameterization:

```text
Parameters:
[TBD]
```

## 15.3 Transformation Assumptions

`[Document assumptions behind the final transformation.]`

## 15.4 Transformation Limitations

`[Document known limitations such as non-planar terrain, viewpoint differences, raw-image geometry, or other relevant effects.]`

---

# 16. Independent Check-Point Evaluation

Independent check points are used to evaluate the final transformation without fitting the transformation to those same points.

## 16.1 Check-Point Source

| Field                  | Value           |
| ---------------------- | --------------- |
| Check-Point Dataset    | `[Name / Path]` |
| Check-Point Version    | `[Version]`     |
| Source Image Set       | `[Identifier]`  |
| Reference Image Set    | `[Identifier]`  |
| Number of Check Points | `[TBD]`         |
| Verification Status    | `[Status]`      |
| Coordinate Convention  | `[Convention]`  |

## 16.2 Independence Verification

Confirm each requirement:

- `[ ]` Check points were not used during RANSAC fitting.
- `[ ]` Check points were not used during final transformation refitting.
- `[ ]` Check points were not used for parameter tuning.
- `[ ]` Check points were not used for model selection.
- `[ ]` Check-point coordinates were not modified after observing final evaluation results.
- `[ ]` Check-point dataset version is recorded.

## 16.3 Evaluation Metrics

| Metric                           |        Result | Unit          |
| -------------------------------- | ------------: | ------------- |
| Check-Point RMSE                 |       `[TBD]` | Source pixels |
| Mean Check-Point Error           |       `[TBD]` | Source pixels |
| Median Check-Point Error         |       `[TBD]` | Source pixels |
| Maximum Check-Point Error        |       `[TBD]` | Source pixels |
| Number of Check Points Evaluated |       `[TBD]` | Count         |
| Ground Error                     | `[TBD / N/A]` | Metres        |
| Other Metric                     |       `[TBD]` | `[Unit]`      |

> **Primary reporting rule:** Report source-image pixel error first when discussing sub-pixel registration accuracy.

Ground-distance conversion should only be reported when GSD, projection, and reference information make that conversion meaningful.

## 16.4 Residual Distribution

`[Describe or link to residual distribution.]`

## 16.5 Residual Visualization

`[Path to residual-vector plot / overlay / N/A]`

## 16.6 Spatial Coverage

| Coverage Metric          |          Result |
| ------------------------ | --------------: |
| Coverage Metric          |      `[Method]` |
| Coverage Value           |         `[TBD]` |
| Spatial Region Evaluated | `[Description]` |

> A large number of check points concentrated in one small area does not necessarily provide strong spatial evidence of registration quality.

---

# 17. Registration Output

## 17.1 Registered Image

`[Path / Artifact ID / N/A]`

## 17.2 Overlay

`[Path / Artifact ID / N/A]`

## 17.3 Difference / Residual Image

`[Path / Artifact ID / N/A]`

## 17.4 Visual Assessment

`[Describe observable alignment.]`

> **Important:** A visually attractive overlay is supporting evidence, not sufficient evidence of accurate registration.

Numerical evaluation using independent check points must remain the primary evidence for registration accuracy.

---

# 18. Spatial Coverage Analysis

## 18.1 Coverage Method

`[Grid-based / Convex hull / Other documented method]`

## 18.2 Coverage Result

`[TBD]`

## 18.3 Match Distribution

`[Describe the spatial distribution of candidate matches and verified inliers.]`

## 18.4 Check-Point Distribution

`[Describe the spatial distribution of independent check points.]`

## 18.5 Clustering

`[Document whether correspondences are clustered around particular terrain features.]`

> More correspondences are not necessarily better if they are incorrect or spatially concentrated.

---

# 19. Runtime and Computational Performance

## 19.1 Runtime

| Pipeline Stage         |       Runtime |
| ---------------------- | ------------: |
| Data Loading           |       `[TBD]` |
| Preprocessing          |       `[TBD]` |
| Multi-Scale Processing |       `[TBD]` |
| Global Retrieval       | `[TBD / N/A]` |
| Feature Extraction     |       `[TBD]` |
| Matching               |       `[TBD]` |
| Geometric Verification |       `[TBD]` |
| Sub-Pixel Refinement   | `[TBD / N/A]` |
| Final Transformation   |       `[TBD]` |
| Evaluation             |       `[TBD]` |
| Total Runtime          |       `[TBD]` |

## 19.2 Hardware

`[GPU / CPU / RAM / Storage / Other]`

## 19.3 Throughput

`[Images per second / pairs per minute / N/A]`

## 19.4 Runtime Interpretation

`[Explain relevant performance trade-offs.]`

---

# 20. Failure Analysis

## 20.1 Experiment Outcome

- `[ ]` Successful
- `[ ]` Partially successful
- `[ ]` Failed
- `[ ]` Aborted

## 20.2 Failure Rate

| Metric           |  Result |
| ---------------- | ------: |
| Total Cases      | `[TBD]` |
| Successful Cases | `[TBD]` |
| Failed Cases     | `[TBD]` |
| Failure Rate     | `[TBD]` |

## 20.3 Failure Cases

| Pair ID | Failure Stage | Observed Problem | Suspected Cause     | Evidence     | Follow-Up  |
| ------- | ------------- | ---------------- | ------------------- | ------------ | ---------- |
| `[ID]`  | `[Stage]`     | `[Description]`  | `[Cause / Unknown]` | `[Artifact]` | `[Action]` |

## 20.4 Failure Categories

Where applicable, classify failures by:

- Scale mismatch
- Illumination / Sun-angle difference
- Modality difference
- Low-feature terrain
- Viewpoint / geometry
- Poor candidate correspondences
- Insufficient verified inliers
- Poor spatial coverage
- Transformation failure
- Sub-pixel refinement failure
- Retrieval failure
- Runtime / resource failure
- Data / metadata problem
- Other: `[Specify]`

> Failed cases must not be silently removed because they reduce the reported performance.

---

# 21. Stress-Test Configuration

Select applicable stress categories:

- `[ ]` Easy pair
- `[ ]` Sun-angle stress
- `[ ]` Scale stress
- `[ ]` Modality stress
- `[ ]` Geometry stress
- `[ ]` Low-feature terrain
- `[ ]` Other: `[Specify]`

## 21.1 Stress-Test Setup

| Stress Condition | Configuration     | Baseline   | Experiment |
| ---------------- | ----------------- | ---------- | ---------- |
| `[Condition]`    | `[Configuration]` | `[Result]` | `[Result]` |

## 21.2 Degradation Analysis

| Condition     | Baseline Metric | Experiment Metric |  Change | Interpretation     |
| ------------- | --------------: | ----------------: | ------: | ------------------ |
| `[Condition]` |         `[TBD]` |           `[TBD]` | `[TBD]` | `[Interpretation]` |

## 21.3 Stress-Test Conclusion

`[Describe what changed under stress conditions. Do not conceal degradation.]`

---

# 22. Baseline Comparison

## 22.1 Baseline Definition

`[Describe the baseline implementation.]`

## 22.2 Experimental Method

`[Describe the proposed/improved method.]`

## 22.3 Fairness Conditions

Confirm:

- `[ ]` Same image pairs
- `[ ]` Same dataset version
- `[ ]` Same independent check-point set
- `[ ]` Same evaluation metrics
- `[ ]` Same relevant coordinate convention
- `[ ]` Same reporting procedure
- `[ ]` Comparable computational conditions where runtime is compared

## 22.4 Comparison Results

| Metric            | Baseline | Experiment | Difference |
| ----------------- | -------: | ---------: | ---------: |
| Candidate Matches |  `[TBD]` |    `[TBD]` |    `[TBD]` |
| Verified Inliers  |  `[TBD]` |    `[TBD]` |    `[TBD]` |
| Inlier Ratio      |  `[TBD]` |    `[TBD]` |    `[TBD]` |
| Spatial Coverage  |  `[TBD]` |    `[TBD]` |    `[TBD]` |
| Check-Point RMSE  |  `[TBD]` |    `[TBD]` |    `[TBD]` |
| Runtime           |  `[TBD]` |    `[TBD]` |    `[TBD]` |
| Failure Rate      |  `[TBD]` |    `[TBD]` |    `[TBD]` |

> Do not declare a method superior solely because it produces more matches or lower fitting error. Independent registration accuracy and spatial coverage must also be considered.

---

# 23. Ablation Study

> **Optional section:** Complete this section when the experiment removes or changes one component at a time.

## 23.1 Ablation Configuration

| Configuration | Component Removed / Changed |  Result |
| ------------- | --------------------------- | ------: |
| Full Pipeline | `[None]`                    | `[TBD]` |
| Ablation A    | `[Component]`               | `[TBD]` |
| Ablation B    | `[Component]`               | `[TBD]` |
| Ablation C    | `[Component]`               | `[TBD]` |

## 23.2 Controlled Components

`[List components that remain constant across ablations.]`

## 23.3 Interpretation

`[Explain which component affected which metric and under which conditions.]`

---

# 24. Results Summary

## 24.1 Primary Results

| Metric            |        Result | Unit          |
| ----------------- | ------------: | ------------- |
| Candidate Matches |       `[TBD]` | Count         |
| Verified Inliers  |       `[TBD]` | Count         |
| Inlier Ratio      |       `[TBD]` | Ratio / %     |
| Spatial Coverage  |       `[TBD]` | `[Unit]`      |
| Check-Point RMSE  |       `[TBD]` | Source pixels |
| Ground Error      | `[TBD / N/A]` | Metres        |
| Runtime           |       `[TBD]` | `[Unit]`      |
| Failure Rate      |       `[TBD]` | Ratio / %     |

## 24.2 Sensor-Specific Results

| Sensor / Pair Type | Candidate Matches | Inliers | Inlier Ratio | Check-Point RMSE | Coverage | Runtime | Failure |
| ------------------ | ----------------: | ------: | -----------: | ---------------: | -------: | ------: | ------: |
| `[Sensor]`         |           `[TBD]` | `[TBD]` |      `[TBD]` |          `[TBD]` |  `[TBD]` | `[TBD]` | `[TBD]` |

> Where multiple sensors are evaluated, report sensor-specific results rather than hiding substantially different sensor behavior inside one aggregate value.

---

# 25. Interpretation

## 25.1 Observed Findings

`[State only findings supported by measured results.]`

## 25.2 Hypothesis Assessment

`[Describe whether the measured results support, contradict, or do not sufficiently test the hypothesis.]`

## 25.3 Unexpected Findings

`[Document unexpected observations.]`

## 25.4 Trade-Offs

`[Describe accuracy/runtime/coverage/robustness trade-offs.]`

## 25.5 Limitations

`[Document limitations of the experiment.]`

## 25.6 Alternative Explanations

`[Document plausible explanations that cannot be ruled out.]`

---

# 26. Reproducibility

A completed experiment should contain enough information for another project contributor to understand and reproduce the execution.

## 26.1 Reproducibility Checklist

- `[ ]` Experiment ID recorded
- `[ ]` Dataset version recorded
- `[ ]` Benchmark version recorded where applicable
- `[ ]` Git commit recorded
- `[ ]` Configuration recorded
- `[ ]` Random seed recorded or marked N/A
- `[ ]` Hardware recorded
- `[ ]` Software environment recorded
- `[ ]` Exact execution command recorded
- `[ ]` Input image IDs recorded
- `[ ]` Reference image IDs recorded
- `[ ]` Ground-truth/check-point version recorded
- `[ ]` Preprocessing recorded
- `[ ]` Scale strategy recorded
- `[ ]` Illumination conditions recorded
- `[ ]` Matching method recorded
- `[ ]` Geometric verification configuration recorded
- `[ ]` Sub-pixel refinement configuration recorded
- `[ ]` Final transformation configuration recorded
- `[ ]` Evaluation metrics recorded
- `[ ]` Output artifacts recorded
- `[ ]` Failure cases recorded

## 26.2 Environment

```text
Operating System:
[TBD]

Python:
[TBD]

Framework / Runtime:
[TBD]

GPU:
[TBD / N/A]

CUDA / Accelerator Runtime:
[TBD / N/A]

Dependencies:
[TBD]

Container / Environment:
[TBD / N/A]
```

## 26.3 Exact Command

```text
[TBD]
```

---

# 27. Artifacts

Record every artifact necessary to inspect or reproduce the experiment.

| Artifact          | Path / Identifier | Version     | Description     |
| ----------------- | ----------------- | ----------- | --------------- |
| Configuration     | `[Path]`          | `[Version]` | `[Description]` |
| Input Data        | `[Path/ID]`       | `[Version]` | `[Description]` |
| Check Points      | `[Path/ID]`       | `[Version]` | `[Description]` |
| Candidate Matches | `[Path]`          | `[Version]` | `[Description]` |
| Verified Inliers  | `[Path]`          | `[Version]` | `[Description]` |
| Transformation    | `[Path]`          | `[Version]` | `[Description]` |
| Registered Image  | `[Path]`          | `[Version]` | `[Description]` |
| Overlay           | `[Path]`          | `[Version]` | `[Description]` |
| Residual Plot     | `[Path]`          | `[Version]` | `[Description]` |
| Metrics           | `[Path]`          | `[Version]` | `[Description]` |
| Logs              | `[Path]`          | `[Version]` | `[Description]` |

---

# 28. Visualization

## 28.1 Input Images

`[Path / Embedded Image / N/A]`

## 28.2 Candidate Matches

`[Path / Embedded Image / N/A]`

## 28.3 Verified Inliers

`[Path / Embedded Image / N/A]`

## 28.4 Registration Overlay

`[Path / Embedded Image / N/A]`

## 28.5 Check-Point Residuals

`[Path / Embedded Image / N/A]`

## 28.6 Spatial Coverage

`[Path / Embedded Image / N/A]`

## 28.7 Failure Visualization

`[Path / Embedded Image / N/A]`

> Visualizations support the numerical evaluation; they do not replace independent geometric metrics.

---

# 29. Quality-Control Checklist

## Data

- `[ ]` Correct dataset version used
- `[ ]` Correct image pair IDs recorded
- `[ ]` Source/reference identities verified
- `[ ]` Sensor identities recorded
- `[ ]` GSD recorded
- `[ ]` Relevant metadata recorded

## Preprocessing

- `[ ]` Every preprocessing operation documented
- `[ ]` Dimensions recorded before and after processing
- `[ ]` GSD changes recorded
- `[ ]` Resampling method recorded
- `[ ]` Coordinate changes recorded
- `[ ]` Sensor-specific processing justified

## Scale

- `[ ]` Effective comparison scale recorded
- `[ ]` Scale rationale documented
- `[ ]` Upsampling not represented as recovered spatial detail
- `[ ]` Multi-resolution strategy documented where used

## Illumination

- `[ ]` Illumination conditions recorded
- `[ ]` Sun angle recorded where available
- `[ ]` View/emission angle recorded where available
- `[ ]` Phase angle recorded where available
- `[ ]` Illumination normalization documented
- `[ ]` Stress degradation reported where applicable

## Correspondence

- `[ ]` Candidate matches reported
- `[ ]` Candidate matches distinguished from verified inliers
- `[ ]` Matching parameters recorded
- `[ ]` Matching version recorded

## Geometric Verification

- `[ ]` Geometric model recorded
- `[ ]` RANSAC configuration recorded
- `[ ]` Inlier count reported
- `[ ]` Inlier ratio reported
- `[ ]` Spatial coverage reported

## Sub-Pixel Refinement

- `[ ]` Verified points refined rather than unverified candidates
- `[ ]` Refinement method recorded
- `[ ]` Parameters recorded
- `[ ]` Final transformation refitted after refinement

## Independent Evaluation

- `[ ]` Independent check points identified
- `[ ]` Check-point version recorded
- `[ ]` Check points excluded from transformation fitting
- `[ ]` Check points excluded from final refitting
- `[ ]` Check-point RMSE reported
- `[ ]` Residuals inspected
- `[ ]` Spatial coverage evaluated

## Reproducibility

- `[ ]` Git commit recorded
- `[ ]` Configuration recorded
- `[ ]` Dataset version recorded
- `[ ]` Environment recorded
- `[ ]` Random seed recorded
- `[ ]` Exact command recorded
- `[ ]` Artifacts recorded

---

# 30. Scientific Integrity Rules

The following rules apply to every experiment documented using this template.

### Rule 1 — Do Not Fabricate Metrics

Use:

```text
TBD
N/A
Not measured
Not available
```

when a value is not known.

Never invent a numerical result to make an experiment appear complete.

### Rule 2 — Resizing Does Not Recover Missing Information

Upsampling increases pixel dimensions but does not recreate spatial detail absent from the original sensor observation.

### Rule 3 — Use Physically Meaningful Scales

Document effective comparison GSD and explain the selected scale.

### Rule 4 — Use Sensor-Aware Processing

OHRC, TMC-2, and IIRS have different characteristics and should not automatically share identical preprocessing.

### Rule 5 — Treat IIRS Explicitly

If IIRS is used, document the selected band, PCA/composite, or structural representation used for correspondence.

### Rule 6 — Do Not Equate Normalization With Illumination Invariance

Brightness and contrast normalization cannot recreate or remove all physical effects of changed terrain illumination.

### Rule 7 — Visual Alignment Is Not Enough

A visually convincing overlay must be supported by quantitative registration evaluation.

### Rule 8 — Candidate Matches Are Not Verified Inliers

Candidate correspondences must undergo geometric verification before being treated as verified inliers.

### Rule 9 — Refine Verified Points

Sub-pixel refinement should operate on geometrically verified points and be followed by final transformation refitting.

### Rule 10 — Evaluate Independently

Where check points are available, evaluate the final transformation on points that were not used to fit it.

### Rule 11 — Report Pixel Error First

For SIH-style sub-pixel registration accuracy, report source-image pixel error first.

Convert to metres only when the GSD, projection, and reference information make that conversion meaningful.

### Rule 12 — Flexible Warps Must Not Hide Bad Correspondences

If a flexible or local transformation is used, document its assumptions and ensure that it does not conceal poor underlying correspondence quality.

### Rule 13 — Preserve Failures

Failed or difficult image pairs are part of the scientific result and must not be silently removed.

### Rule 14 — More Matches Are Not Automatically Better

Correspondence quality, geometric consistency, independent registration accuracy, and spatial coverage matter in addition to match count.

---

# 31. Experiment Conclusion

## 31.1 Primary Finding

`[TBD — Complete after experiment execution.]`

## 31.2 Research Question Answer

`[TBD — Answer using measured evidence only.]`

## 31.3 Hypothesis Assessment

`[Supported / Not Supported / Inconclusive — with evidence.]`

## 31.4 Key Metrics

| Metric                       |  Result |
| ---------------------------- | ------: |
| Verified Inliers             | `[TBD]` |
| Inlier Ratio                 | `[TBD]` |
| Spatial Coverage             | `[TBD]` |
| Independent Check-Point RMSE | `[TBD]` |
| Runtime                      | `[TBD]` |
| Failure Rate                 | `[TBD]` |

## 31.5 Main Limitation

`[TBD]`

## 31.6 Recommended Follow-Up Experiment

`[TBD]`

---

# 32. Follow-Up Experiments

| Experiment ID | Question     | Motivation | Dependency             |
| ------------- | ------------ | ---------- | ---------------------- |
| `[EXP-XXXX]`  | `[Question]` | `[Reason]` | `[Current experiment]` |
| `[EXP-XXXX]`  | `[Question]` | `[Reason]` | `[Current experiment]` |

Possible follow-up directions include:

- scale stress;
- Sun-angle stress;
- modality stress;
- geometry stress;
- low-feature terrain;
- sensor-specific preprocessing;
- alternative correspondence methods;
- retrieval evaluation;
- sub-pixel refinement;
- transformation-model comparison;
- failure-case analysis.

---

# 33. Experiment Rejection Criteria

The experiment should be flagged for review if any of the following occurred:

- dataset version is unknown;
- input image identity is unknown;
- preprocessing cannot be reconstructed;
- transformation fitting points and check points are mixed;
- check points were used during tuning without being disclosed;
- reported metrics cannot be reproduced from recorded inputs;
- candidate matches are reported as verified inliers;
- visual overlays are presented without numerical evaluation where numerical evaluation is required;
- unsupported physical accuracy claims are made;
- upsampling is described as recovering missing spatial detail;
- ground-distance accuracy is reported without meaningful GSD/projection support;
- failed cases were excluded without documented justification;
- numerical metrics were manually altered or fabricated.

### Review Status

`[Pass / Needs Review / Reject / N/A]`

### Review Notes

`[TBD]`

---

# 34. Final Experiment Record

Complete this section only after the experiment has been executed and reviewed.

| Field             | Final Value                      |
| ----------------- | -------------------------------- |
| Experiment ID     | `[EXP-XXXX]`                     |
| Status            | `[Completed / Failed / Aborted]` |
| Dataset Version   | `[Version]`                      |
| Benchmark Version | `[Version / N/A]`                |
| Git Commit        | `[SHA]`                          |
| Configuration     | `[Path / ID]`                    |
| Primary Method    | `[Method]`                       |
| Baseline          | `[Baseline / N/A]`               |
| Image Pairs       | `[Count]`                        |
| Check Points      | `[Count]`                        |
| Verified Inliers  | `[Count]`                        |
| Inlier Ratio      | `[Value]`                        |
| Spatial Coverage  | `[Value]`                        |
| Check-Point RMSE  | `[Value]`                        |
| Ground Error      | `[Value / N/A]`                  |
| Runtime           | `[Value]`                        |
| Failure Rate      | `[Value]`                        |
| Result Artifact   | `[Path / ID]`                    |
| Review Status     | `[Status]`                       |

---

# 35. Change Log

| Date           | Author   | Change     | Reason     |
| -------------- | -------- | ---------- | ---------- |
| `[YYYY-MM-DD]` | `[Name]` | `[Change]` | `[Reason]` |

---

# 36. Template Usage Notes

When creating a new experiment from this template:

1. Copy this file into the appropriate experiment location.
2. Assign a unique experiment ID.
3. Replace all applicable placeholders.
4. Remove sections explicitly marked as not applicable only when their absence does not remove required scientific information.
5. Record the exact dataset and benchmark versions.
6. Record the exact code version used.
7. Record preprocessing and scale decisions.
8. Record sensor-specific assumptions.
9. Record candidate matches separately from verified inliers.
10. Record the geometric verification configuration.
11. Record sub-pixel refinement and final transformation refitting.
12. Record independent check-point evaluation.
13. Report source-pixel error before any physically meaningful ground-distance conversion.
14. Record runtime and failure cases.
15. Preserve unsuccessful results and limitations.
16. Do not replace `TBD`, `N/A`, or `Not measured` with invented values.
17. Complete the conclusion only after the experiment has actually been executed.

---

# 37. Minimal Completion Checklist

Before considering an experiment report complete:

```text
[ ] Experiment metadata complete
[ ] Objective defined
[ ] Research question defined
[ ] Hypothesis defined
[ ] Variables documented
[ ] Dataset version recorded
[ ] Image pairs identified
[ ] Sensor configuration documented
[ ] Preprocessing documented
[ ] Effective comparison scale documented
[ ] Illumination conditions documented
[ ] Matching method documented
[ ] Candidate matches reported
[ ] Geometric verification documented
[ ] Verified inliers reported
[ ] Inlier ratio reported
[ ] Spatial coverage reported
[ ] Sub-pixel refinement documented
[ ] Final transformation documented
[ ] Independent check-point dataset identified
[ ] Check-point independence verified
[ ] Check-point RMSE reported
[ ] Runtime reported
[ ] Failure cases documented
[ ] Stress-test results documented where applicable
[ ] Baseline comparison documented where applicable
[ ] Artifacts recorded
[ ] Git commit recorded
[ ] Dataset version recorded
[ ] Execution command recorded
[ ] Environment recorded
[ ] Scientific limitations documented
[ ] No fabricated metrics
[ ] Final conclusion completed
```

---

# 38. Final Scientific Principle

Every ChandraMap experiment should preserve the distinction:

```text
                    INPUT
                      │
                      ▼
             Sensor-Aware Processing
                      │
                      ▼
              Multi-Scale Strategy
                      │
                      ▼
           Global Retrieval (Optional)
                      │
                      ▼
             Candidate Matches
                      │
                      ▼
          Geometric Verification
                 / RANSAC
                      │
                      ▼
             Verified Inliers
                      │
                      ▼
             Sub-Pixel Refinement
                      │
                      ▼
          Final Transformation
                      │
                      ▼
                 Registration
                      │
                      ▼
        Independent Check Points
                      │
                      ▼
       Quantitative Registration Metrics
                      │
          ┌───────────┼───────────┐
          ▼           ▼           ▼
        RMSE       Coverage     Runtime
          │
          ▼
     Failure Analysis
```

The experiment report should demonstrate **what was tested, under which conditions, with which data and configuration, what was actually measured, and how independently the result was evaluated**.

The template is not evidence of an experiment having been performed. Every result, metric, artifact, and conclusion must be populated from an actual execution and clearly distinguished from the original hypothesis or expected outcome.
