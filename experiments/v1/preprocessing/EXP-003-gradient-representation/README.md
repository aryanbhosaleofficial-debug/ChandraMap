# EXP-003 — Gradient Representation

**Path:** `experiments/v1/preprocessing/EXP-003-gradient-representation/README.md`
**Experiment ID:** `EXP-003`
**Version:** `V1`
**Category:** Preprocessing / Structural Representation
**Primary Method:** Gradient / edge / structure-focused image representation
**Primary Baseline:** `EXP-001 — SIFT Baseline`
**Preprocessing Predecessor:** `EXP-002 — Scale Pyramid`
**Status:** `[TBD]`

---

## 1. Overview

**EXP-003 — Gradient Representation** investigates whether representing lunar imagery through local terrain structure can improve image correspondence compared with raw grayscale intensity.

The experiment is motivated by a central difficulty in lunar image registration: images of the same terrain can differ because of **Sun angle, shadows, sensor modality, spatial resolution, scale, viewing geometry, and radiometric characteristics**.

The experiment therefore asks whether structural information such as:

- crater rims
- ridge lines
- terrain boundaries
- local intensity transitions
- gradient orientation
- relative geometry

can provide more stable correspondence than raw brightness under selected difficult conditions.

The experiment follows the V1 philosophy:

> **Build small. Measure honestly. Keep the failures.**

The technical feedback specifically recommends comparing raw grayscale with edge/gradient/structural representations under strong Sun-angle differences.

This experiment **does not assume that gradients improve registration**. The purpose is to measure the effect.

---

## 2. Scientific Principle

> **Compare stable terrain structure rather than relying only on raw brightness when lunar illumination or sensor modality changes.**

A gradient representation can reduce dependence on absolute intensity, but it does not make differently illuminated lunar images identical.

A different Sun angle can change the location and appearance of crater and terrain shadows. Brightness or contrast normalization cannot recreate shadow geometry that changed because the illumination geometry changed.

Therefore:

- gradient representations do not guarantee illumination invariance;
- edge maps do not remove shadow geometry;
- structural representations do not recover missing spatial information;
- normalization does not recover terrain that was not measured;
- cross-sensor correspondence is not guaranteed;
- successful matching must still be demonstrated through geometric verification and independent evaluation.

The experiment measures these effects rather than assuming them away.

---

## 3. Research Question

### Primary Research Question

> **Does a gradient or edge-based representation produce more geometrically consistent lunar image correspondences than raw grayscale intensity when source and reference images experience significant illumination and/or modality differences?**

The answer must be determined from measured experimental results.

### Secondary Research Questions

Where supported by the implementation, EXP-003 investigates:

1. Does structural representation change the number of candidate matches?
2. Does it improve the number of verified inliers?
3. Does it improve the inlier ratio?
4. Does it improve spatial distribution of verified correspondences?
5. Does it reduce independent check-point registration error?
6. Does it reduce performance degradation under different Sun angles?
7. Does representation choice behave differently across sensor pairs?
8. Does representation normalization materially affect results?
9. Does the additional preprocessing cost materially change runtime?

---

## 4. Hypothesis

### Primary Hypothesis

> A representation emphasizing local terrain structure may produce more stable correspondence than raw grayscale intensity when brightness and shadow patterns differ between source and reference images.

Possible measurable effects include:

- increased verified inlier count;
- increased inlier ratio;
- improved spatial coverage;
- reduced independent check-point RMSE;
- improved performance under illumination stress;
- improved structural correspondence across selected modalities.

These are **hypotheses, not results**.

Results must be based on measured experiments.

---

## 5. Scientific Warning

> ⚠️ **Gradient representations can reduce dependence on absolute intensity, but they do not make two differently illuminated lunar images identical.**

A gradient or edge representation does **not**:

- remove all illumination effects;
- make shadows disappear;
- move shadows back to their previous positions;
- recover hidden terrain;
- recover missing spatial detail;
- guarantee cross-sensor invariance;
- guarantee successful registration;
- guarantee sub-pixel accuracy.

The project feedback explicitly identifies Sun-angle changes as changes in shadow geometry, not merely brightness, and recommends treating illumination handling as an experiment.

---

# 6. Relationship to V1

EXP-003 is a controlled preprocessing experiment within the V1 experimental pipeline.

The broader V1 progression is:

```text
Known Source / Reference Pair
            ↓
     Sensor / Metadata
            ↓
       Preprocessing
            ↓
    Local Correspondence
            ↓
   Geometric Verification
            ↓
      Transformation
            ↓
      Registration
            ↓
 Independent Evaluation
            ↓
       Measurements
```

EXP-003 changes the **image representation** entering the local matching stage.

It should not simultaneously change unrelated parts of the pipeline unless those changes are explicitly part of the experiment.

---

# 7. Relationship to Previous Experiments

## EXP-001 — SIFT Baseline

Conceptual baseline:

```text
Raw / Basic Representation
        ↓
       SIFT
        ↓
Descriptor Matching
        ↓
Candidate Matches
        ↓
RANSAC / Geometric Verification
        ↓
Transformation
        ↓
Registration
        ↓
Evaluation
```

EXP-001 establishes the reference behavior for the local matching pipeline.

---

## EXP-002 — Scale Pyramid

Conceptual progression:

```text
Physically Comparable Scale
        ↓
       SIFT
        ↓
Descriptor Matching
        ↓
Candidate Matches
        ↓
RANSAC / Geometric Verification
        ↓
Transformation
        ↓
Registration
        ↓
Evaluation
```

EXP-002 addresses the physical-scale problem before correspondence.

The project feedback recommends bringing imagery to a comparable effective ground scale rather than treating upsampling as recovery of missing information.

---

## EXP-003 — Gradient Representation

Conceptual progression:

```text
Comparable Scale Where Applicable
        ↓
Structural / Gradient Representation
        ↓
       SIFT
        ↓
Descriptor Matching
        ↓
Candidate Matches
        ↓
RANSAC / Geometric Verification
        ↓
Transformation
        ↓
Registration
        ↓
Independent Evaluation
```

### Primary Experimental Change

**Image representation**

### Variables That Should Remain Fixed

Where possible:

- source image;
- reference image;
- overlap/crop;
- scale strategy;
- SIFT configuration;
- descriptor matcher;
- candidate filtering;
- RANSAC configuration;
- transformation model;
- evaluation check points;
- random seed;
- software environment.

This isolates the effect of the representation.

---

# 8. Scope

## In Scope

EXP-003 evaluates:

- raw grayscale as the control;
- gradient/edge/structural representations actually implemented;
- correspondence quality;
- geometric verification;
- inlier statistics;
- spatial distribution;
- independent registration error;
- illumination stress where suitable data exists;
- representation preprocessing cost;
- failure behavior.

## Out of Scope

Unless explicitly implemented elsewhere, EXP-003 does not claim to solve:

- global lunar retrieval;
- whole-Moon indexing;
- universal illumination invariance;
- complete multi-sensor registration;
- hyperspectral end-to-end matching;
- learned feature matching;
- universal geometric modeling;
- terrain reconstruction;
- DEM generation;
- production-scale deployment.

The project feedback recommends adding capabilities incrementally and proving one measurable layer at a time.

---

# 9. Input Data

The exact EXP-003 input pair and metadata are not established in the available project documentation.

| Field                   | Value   |
| ----------------------- | ------- |
| Source image            | `[TBD]` |
| Reference image         | `[TBD]` |
| Source sensor           | `[TBD]` |
| Reference sensor        | `[TBD]` |
| Source product          | `[TBD]` |
| Reference product       | `[TBD]` |
| Source dimensions       | `[TBD]` |
| Reference dimensions    | `[TBD]` |
| Source GSD              | `[TBD]` |
| Reference GSD           | `[TBD]` |
| Approximate scale ratio | `[TBD]` |
| Projection              | `[TBD]` |
| Geographic overlap      | `[TBD]` |
| Illumination conditions | `[TBD]` |
| Sun-angle metadata      | `[TBD]` |

No image IDs, dimensions, GSD values, projections, or illumination values should be inferred without source data.

---

# 10. Sensor Context

ChandraMap should not treat all lunar sensors as identical images.

The project feedback distinguishes OHRC, TMC-2, and IIRS by both spatial scale and modality.

## OHRC

OHRC provides high-resolution visible/panchromatic lunar imagery.

The authoritative pixel scale for a particular experiment should come from the actual challenge/product metadata rather than a generic assumed value.

## TMC-2

TMC-2 provides panchromatic terrain imagery at approximately the meter-scale class and can provide useful structural overlap with lunar reference imagery.

## IIRS

IIRS is an imaging infrared hyperspectral sensor with substantially different spatial and spectral characteristics.

It should not automatically be treated as an ordinary 2D grayscale camera image.

If IIRS is included in EXP-003, the experiment must document the conversion from spectral data to a registration-friendly 2D representation.

The feedback recommends starting with simple representations such as a selected band, PCA/composite, or structural map rather than beginning with a complicated hyperspectral network.

## Reference Imagery

Reference imagery may have substantially finer spatial resolution than the source.

The higher-resolution side should be downsampled or represented through a pyramid when necessary to establish a physically meaningful comparison scale.

---

# 11. Scale Handling

Scale handling and representation handling are separate experimental variables.

### Scale Handling

Makes the source and reference information physically comparable.

### Representation Handling

Changes how local terrain structure is represented for correspondence.

EXP-003 should inherit the scale strategy from EXP-002 when EXP-002 is part of the controlled comparison.

If a reference pyramid is used:

- selected pyramid level: `[TBD]`;
- effective source GSD: `[TBD]`;
- effective reference GSD: `[TBD]`;
- scale factor: `[TBD]`.

> **Upsampling changes pixel count; it does not recover spatial information that was absent from the source sensor.**

The project feedback explicitly recommends a reference pyramid or downsampling of the higher-resolution side instead of enlarging coarse imagery and treating the additional pixels as recovered detail.

---

# 12. Representation Definitions

EXP-003 uses **structural representation** as the broader experimental concept.

## R0 — Raw Grayscale

The control representation.

It preserves image intensity information without the gradient conversion being tested by EXP-003.

---

## R1 — Gradient Representation

The primary experimental representation.

The exact gradient operator is:

`[TBD]`

The exact normalization procedure is:

`[TBD]`

The exact output datatype/range is:

`[TBD]`

---

## Gradient Magnitude

When implemented, gradient magnitude measures the strength of local intensity change.

For image intensity \(I(x,y)\), a generic gradient can be expressed as:

$$
G_x = \frac{\partial I}{\partial x}
$$

$$
G_y = \frac{\partial I}{\partial y}
$$

and:

$$
G = \sqrt{G_x^2 + G_y^2}
$$

This equation describes the generic gradient concept.

The exact derivative operator, kernel, normalization, and implementation used by EXP-003 remain:

`[TBD]`

---

## Gradient Orientation

When implemented:

$$
\theta = \operatorname{atan2}(G_y,G_x)
$$

Orientation represents the direction of local intensity change.

Its use in EXP-003 is:

`[TBD]`

---

## Edge Map

An edge representation emphasizes boundaries or local transitions.

The specific edge detector, thresholding procedure, and output representation are:

`[TBD]`

---

## Structural Representation

For this experiment, **structural representation** refers broadly to an image representation intended to preserve terrain geometry and local structure rather than relying only on absolute brightness.

It must not be interpreted as a guarantee of physical terrain invariance.

---

# 13. Implemented Representation

The repository currently does not provide enough confirmed implementation detail to specify the exact EXP-003 gradient operator, kernel, normalization, or output format.

| Representation                        | Status                            |
| ------------------------------------- | --------------------------------- |
| Raw grayscale control                 | Specified as control              |
| Gradient representation               | Specified as experiment objective |
| Gradient magnitude                    | `[TBD]`                           |
| Gradient orientation                  | `[TBD]`                           |
| Edge representation                   | `[TBD]`                           |
| Alternative structural representation | `[TBD]`                           |

Only representations actually implemented should be entered as measured experimental conditions.

---

# 14. Preprocessing Pipeline

Preprocessing must be documented separately from representation conversion.

The project feedback recommends keeping preprocessing simple and requiring each preprocessing step to demonstrate measurable benefit.

| Stage                        | Method  | Parameters | Purpose                             | Status  |
| ---------------------------- | ------- | ---------- | ----------------------------------- | ------- |
| Loading                      | `[TBD]` | `[TBD]`    | Load source/reference               | `[TBD]` |
| Calibration                  | `[TBD]` | `[TBD]`    | Product preparation                 | `[TBD]` |
| Projection                   | `[TBD]` | `[TBD]`    | Geometry handling                   | `[TBD]` |
| Cropping                     | `[TBD]` | `[TBD]`    | Define comparison area              | `[TBD]` |
| Masking                      | `[TBD]` | `[TBD]`    | Remove invalid regions              | `[TBD]` |
| Denoising                    | `[TBD]` | `[TBD]`    | Reduce noise                        | `[TBD]` |
| Intensity normalization      | `[TBD]` | `[TBD]`    | Controlled photometric preparation  | `[TBD]` |
| Gradient computation         | `[TBD]` | `[TBD]`    | Structural representation           | `[TBD]` |
| Representation normalization | `[TBD]` | `[TBD]`    | Prepare representation for matching | `[TBD]` |

No preprocessing step should be considered beneficial solely because it is conventional.

---

# 15. IIRS Representation

If EXP-003 is extended to IIRS, the spectral-to-2D conversion must be documented explicitly.

Possible approaches include:

- selected spectral band;
- PCA component;
- PCA composite;
- derived structural map;
- gradient representation of a selected band;
- gradient representation of a derived 2D product.

The actual EXP-003 implementation is:

`[TBD]`

The project feedback recommends asking which simple 2D representation best preserves stable terrain structure before considering more complicated hyperspectral approaches.

A full hyperspectral cube must not be presented as a conventional 2D SIFT input unless the implementation explicitly supports that representation.

---

# 16. Illumination Stress Test

Illumination is a primary motivation for EXP-003.

The project feedback recommends comparing the same region under similar and substantially different lighting and reporting the performance change honestly.

## Condition A — Similar Illumination

Same or overlapping terrain with relatively similar illumination geometry.

## Condition B — Different Illumination

Same or overlapping terrain with substantially different Sun geometry and shadow appearance.

### Comparison

| Condition     | Representation | Candidate Matches | Inliers | Inlier Ratio | Coverage | Check-Point RMSE |
| ------------- | -------------- | ----------------: | ------: | -----------: | -------: | ---------------: |
| Similar Sun   | Grayscale      |           `[TBD]` | `[TBD]` |      `[TBD]` |  `[TBD]` |          `[TBD]` |
| Similar Sun   | Gradient       |           `[TBD]` | `[TBD]` |      `[TBD]` |  `[TBD]` |          `[TBD]` |
| Different Sun | Grayscale      |           `[TBD]` | `[TBD]` |      `[TBD]` |  `[TBD]` |          `[TBD]` |
| Different Sun | Gradient       |           `[TBD]` | `[TBD]` |      `[TBD]` |  `[TBD]` |          `[TBD]` |

**Illumination stress-test status:** `[TBD]`

If suitable data is unavailable:

`Illumination stress test: [Not implemented]`

---

# 17. Representation Ablation

The primary controlled comparison is:

### R0 — Grayscale

Raw grayscale representation.

### R1 — Gradient

The actual gradient representation implemented by EXP-003.

If additional representations are implemented, they may be added independently.

| Condition | Representation                        | Scale Strategy | SIFT | RANSAC | Purpose            |
| --------- | ------------------------------------- | -------------- | ---- | ------ | ------------------ |
| R0        | Grayscale                             | Fixed          | Same | Same   | Control            |
| R1        | Gradient                              | Fixed          | Same | Same   | Primary experiment |
| R2        | Edge                                  | Fixed          | Same | Same   | `[TBD]`            |
| R3        | Alternative structural representation | Fixed          | Same | Same   | `[TBD]`            |

The primary comparison should change **only the representation**.

---

# 18. SIFT Configuration

EXP-003 should preserve the SIFT configuration established by EXP-001 unless SIFT configuration itself is an explicit experimental variable.

| Parameter           | EXP-001 | EXP-003 | Changed? |
| ------------------- | ------: | ------: | -------- |
| `nfeatures`         | `[TBD]` | `[TBD]` | `[TBD]`  |
| `nOctaveLayers`     | `[TBD]` | `[TBD]` | `[TBD]`  |
| `contrastThreshold` | `[TBD]` | `[TBD]` | `[TBD]`  |
| `edgeThreshold`     | `[TBD]` | `[TBD]` | `[TBD]`  |
| `sigma`             | `[TBD]` | `[TBD]` | `[TBD]`  |

If SIFT configuration changes, the reason and experimental effect must be documented.

A representation improvement cannot be attributed solely to gradients if SIFT configuration changes simultaneously.

---

# 19. Matching Configuration

The matching configuration should remain consistent with EXP-001 unless explicitly changed.

| Parameter           | EXP-001 | EXP-003 |
| ------------------- | ------- | ------- |
| Matcher             | `[TBD]` | `[TBD]` |
| Distance metric     | `[TBD]` | `[TBD]` |
| Ratio threshold     | `[TBD]` | `[TBD]` |
| Cross-check         | `[TBD]` | `[TBD]` |
| Candidate filtering | `[TBD]` | `[TBD]` |

The project feedback identifies SIFT followed by descriptor matching, ratio/cross-check filtering, RANSAC, transformation estimation, and residual evaluation as the baseline path.

---

# 20. Candidate Matches

EXP-003 must distinguish **candidate matches** from **verified inliers**.

```text
Keypoints
    ↓
Descriptor Matching
    ↓
Candidate Matches
    ↓
RANSAC / Geometric Verification
    ↓
Verified Inliers
```

Candidate matches have not yet been proven geometrically correct.

For each representation:

| Representation | Keypoints | Candidate Matches | Median Distance | Notes |
| -------------- | --------: | ----------------: | --------------: | ----- |
| Grayscale      |   `[TBD]` |           `[TBD]` |         `[TBD]` |       |
| Gradient       |   `[TBD]` |           `[TBD]` |         `[TBD]` |       |

The project feedback specifically recommends the terminology **Candidate Matches** before geometric verification and warns against treating matcher confidence as proof of correctness.

---

# 21. RANSAC and Geometric Verification

Geometric verification should use the same protocol as EXP-001 unless EXP-003 explicitly studies a different model.

Document:

- transformation model;
- RANSAC implementation;
- reprojection threshold;
- confidence;
- iteration limit;
- minimum inlier requirement;
- random seed.

| Parameter              | Value   |
| ---------------------- | ------- |
| Transformation model   | `[TBD]` |
| RANSAC implementation  | `[TBD]` |
| Reprojection threshold | `[TBD]` |
| Confidence             | `[TBD]` |
| Iteration limit        | `[TBD]` |
| Minimum inliers        | `[TBD]` |
| Random seed            | `[TBD]` |

For each representation, measure:

- verified inlier count;
- inlier ratio;
- residual error;
- spatial distribution.

A high number of candidate matches is not equivalent to good registration.

---

# 22. Transformation Model

The actual transformation model is:

`[TBD]`

Possible V1 models include:

- affine;
- homography;
- another explicitly implemented local model.

The project feedback describes affine or homography as reasonable first models for local, already map-projected pairs, while emphasizing that lunar terrain is not flat and that a single global transform may be insufficient.

Document:

| Property             | Value                      |
| -------------------- | -------------------------- |
| Model                | `[TBD]`                    |
| Fitting points       | Verified inliers / `[TBD]` |
| Residual calculation | `[TBD]`                    |
| Model assumptions    | `[TBD]`                    |
| Spatial limitations  | `[TBD]`                    |

Residual vectors should be inspected across the image.

Systematic residual changes across the image may indicate that a simple global model is insufficient.

---

# 23. Sub-Pixel Refinement

Sub-pixel refinement is **not automatically part of EXP-003**.

**Status:** `[Not implemented in EXP-003 / TBD]`

If implemented later, the expected conceptual sequence is:

```text
Candidate Matches
        ↓
RANSAC
        ↓
Verified Inliers
        ↓
Sub-Pixel Tie-Point Refinement
        ↓
Final Transformation Refit
        ↓
Registered Image
```

The project feedback specifically recommends refining verified control points and then refitting the final transformation.

---

# 24. Controlled Variables

The primary independent variable is:

> **Image representation**

The following should remain fixed where possible:

- source image;
- reference image;
- crop/overlap;
- effective scale;
- SIFT configuration;
- descriptor matcher;
- ratio threshold;
- cross-check configuration;
- RANSAC model;
- RANSAC threshold;
- evaluation check points;
- random seed;
- hardware/software environment.

A secondary independent variable may be:

> **Illumination condition**

only when EXP-003 explicitly includes an illumination stress experiment.

---

# 25. Experiment Matrix

The final matrix must reflect actual executed experiments.

A recommended structure is:

| Experiment | Representation | Illumination | Scale | SIFT | RANSAC | Purpose              |
| ---------- | -------------- | ------------ | ----- | ---- | ------ | -------------------- |
| R0         | Grayscale      | Similar      | Fixed | Same | Same   | Control              |
| R1         | Gradient       | Similar      | Fixed | Same | Same   | Main experiment      |
| R2         | Grayscale      | Different    | Fixed | Same | Same   | Illumination control |
| R3         | Gradient       | Different    | Fixed | Same | Same   | Illumination stress  |
| R4         | Edge           | Different    | Fixed | Same | Same   | Optional ablation    |

Cases not actually executed must remain marked as planned or `[TBD]`.

---

# 26. Evaluation Metrics

Evaluation is a core component of EXP-003.

## Matching Metrics

- candidate match count;
- verified inlier count;
- inlier ratio.

## Spatial Metrics

- grid coverage;
- convex-hull coverage where implemented;
- point density;
- clustering behavior.

## Registration Metrics

- independent check-point RMSE;
- median error;
- maximum error;
- percentile error where available;
- residual distribution.

## Geospatial Metrics

Ground error in metres should only be reported when:

- GSD is known;
- projection is meaningful;
- reference/ground truth supports the conversion.

## System Metrics

- preprocessing runtime;
- feature extraction runtime;
- matching runtime;
- total runtime;
- failure rate.

The project feedback identifies reprojection RMSE, inlier count, inlier ratio, spatial coverage, meaningful ground error, success rate, and runtime as important measurable outputs.

---

# 27. Independent Check Points

Transformation fitting and final evaluation should use different points whenever independent ground truth/check points are available.

### Transformation Estimation

Use:

- verified inliers;
- control/fitting points;
- refined control points if sub-pixel refinement is implemented.

### Final Evaluation

Use:

- independent check points;
- challenge ground truth where available;
- independently checked tie points where applicable.

> **Do not fit and judge on exactly the same points.**

Using the same points for fitting and evaluation can produce an overly optimistic fitting error. The project feedback explicitly recommends independent check points for final evaluation.

If independent check points are unavailable:

`Independent check-point evaluation: [Not available]`

In that case, fitting error must not be described as independent registration accuracy.

---

# 28. Error Units

Registration error should be reported in:

> **Source-image pixels first.**

Conversion to metres is appropriate only when the relevant:

- source GSD;
- projection;
- reference truth;
- geometric assumptions

support the conversion.

The project feedback emphasizes that equal pixel errors on different sensors do not represent equal ground errors.

Do not invent a pixel-to-metre conversion.

---

# 29. Spatial Coverage

A representation can produce many matches while concentrating them in a small local region.

EXP-003 should therefore evaluate whether verified correspondences are distributed across the useful overlap.

Possible measures include:

- grid coverage;
- convex-hull coverage;
- bounding-box coverage;
- point density;
- clustering.

A possible grid metric is:

> Number or fraction of spatial grid cells containing at least one verified inlier.

The exact grid definition is:

`[TBD]`

The project feedback specifically recommends spatial coverage because good points should be distributed across the overlap rather than concentrated around one crater.

---

# 30. Expected Results Table

No numerical EXP-003 results are established in the available project materials.

| Metric            | EXP-001 | EXP-002 | EXP-003 | Difference vs Control | Notes     |
| ----------------- | ------: | ------: | ------: | --------------------: | --------- |
| Candidate matches | `[TBD]` | `[TBD]` | `[TBD]` |               `[TBD]` |           |
| Verified inliers  | `[TBD]` | `[TBD]` | `[TBD]` |               `[TBD]` |           |
| Inlier ratio      | `[TBD]` | `[TBD]` | `[TBD]` |               `[TBD]` |           |
| Spatial coverage  | `[TBD]` | `[TBD]` | `[TBD]` |               `[TBD]` |           |
| Check-point RMSE  | `[TBD]` | `[TBD]` | `[TBD]` |               `[TBD]` | source px |
| Runtime           | `[TBD]` | `[TBD]` | `[TBD]` |               `[TBD]` | s         |

EXP-002 should only be included as a measured comparison if it is actually part of the controlled evaluation.

EXP-003 must not be described as an improvement merely because it is the next experiment.

---

# 31. Results

## Representation-Level Results

**Status:** `Not yet measured.`

The final report should compare:

- grayscale;
- gradient;
- additional structural representations only when implemented.

---

## Illumination Results

**Status:** `[TBD]`

When available, compare:

- similar illumination;
- different illumination.

The important measurement is the change in performance, not the assumption that gradients eliminate illumination effects.

---

## Baseline Comparison

The appropriate comparison is:

```text
EXP-001
   ↓
EXP-002 where applicable
   ↓
EXP-003
```

The same image pairs and evaluation conditions should be preserved wherever possible.

---

## Quantitative Results

Record:

- candidate matches;
- verified inliers;
- inlier ratio;
- spatial coverage;
- check-point RMSE;
- median error;
- maximum error;
- runtime;
- failure status.

**Current status:** `Not yet measured.`

---

## Qualitative Results

Visual inspection should compare:

1. raw grayscale source;
2. raw grayscale reference;
3. gradient source;
4. gradient reference;
5. edge/structural representations when implemented;
6. candidate matches;
7. RANSAC inliers;
8. inlier distribution;
9. registered overlay;
10. residual vectors.

A visually attractive overlay is not sufficient evidence of correct correspondence.

---

# 32. Gradient-Specific Visualizations

When implemented, the experiment should preserve visual evidence for:

### Gradient Magnitude

Shows locations of strong local intensity transitions.

**Path:** `[TBD]`

### Gradient Orientation

Shows the local direction of intensity change.

**Path:** `[TBD]`

### Edge Map

Shows detected image boundaries.

**Path:** `[TBD]`

### Candidate Match Overlay

Shows proposed correspondences before geometric verification.

**Path:** `[TBD]`

### Verified Inlier Overlay

Shows correspondences accepted by geometric verification.

**Path:** `[TBD]`

### Residual Vector Field

Shows spatially varying registration error.

**Path:** `[TBD]`

---

# 33. Failure Analysis

Failures are first-class experimental evidence.

Potential failure categories include:

- weak gradients;
- excessive noise;
- crater-shadow ambiguity;
- shadow reversal;
- gradient polarity changes;
- edge fragmentation;
- irrelevant boundary detection;
- repetitive terrain;
- low-feature terrain;
- insufficient keypoints;
- cross-sensor modality mismatch;
- scale mismatch;
- interpolation artifacts;
- clustered inliers;
- incorrect geometric model;
- unstable representation normalization.

For every observed failure, record:

### Failure Condition

What image pair and representation were used?

### Observed Behavior

What failed?

### Evidence

Which measurements or visualizations demonstrate the failure?

### Suspected Cause

What mechanism may explain it?

### Impact

How did it affect correspondence or registration?

### Reproduction

What configuration reproduces the failure?

### Next Experiment

What controlled change should be tested next?

The project feedback explicitly recommends exposing difficult and low-feature conditions rather than hiding failures.

---

# 34. Gradient-Specific Limitations

## Shadow Geometry

Gradient edges can correspond to shadows rather than physical terrain boundaries.

## Edge Polarity

A terrain transition can change appearance when illumination changes.

## Noise Amplification

Derivative operations can amplify high-frequency noise.

## Texture Loss

A strong structural representation may discard intensity information that is useful for correspondence.

## Low-Contrast Terrain

Weak terrain boundaries may produce weak gradients.

## Repetitive Terrain

Repeated crater or terrain structures can generate ambiguous correspondences.

## Modality Differences

Different sensors measure different physical quantities. Similar gradients do not guarantee physically equivalent terrain structure.

---

# 35. Normalization

The exact EXP-003 normalization strategy is:

`[TBD]`

Possible approaches should only be documented when actually implemented:

- min-max normalization;
- percentile normalization;
- standardization;
- local normalization;
- no normalization.

| Operation              | Method  | Parameters | Applied To | Status  |
| ---------------------- | ------- | ---------- | ---------- | ------- |
| Gradient normalization | `[TBD]` | `[TBD]`    | `[TBD]`    | `[TBD]` |
| Contrast normalization | `[TBD]` | `[TBD]`    | `[TBD]`    | `[TBD]` |

Normalization must not be added simply because it is common.

If normalization is an experimental variable, its effect must be measured separately.

---

# 36. Control vs Experimental Conditions

## Control Condition

**Raw grayscale representation** using the established baseline pipeline.

## Experimental Condition

**Gradient / edge / structural representation** implemented by EXP-003.

Where possible, maintain:

- same source;
- same reference;
- same crop;
- same scale;
- same SIFT;
- same matcher;
- same RANSAC;
- same check points;
- same random seed;
- same execution environment.

### Primary Difference

> **Image representation**

---

# 37. Ablation Design

If EXP-003 contains multiple preprocessing components, they should be isolated.

A possible structure is:

```text
A0 — Grayscale
        ↓
A1 — Gradient
        ↓
A2 — Gradient + Normalization
        ↓
A3 — Gradient + Denoising
```

Only actual implemented components should be included in the final measured experiment.

The purpose is to determine whether an observed change is attributable to:

- the gradient itself;
- normalization;
- denoising;
- or another preprocessing component.

---

# 38. Sensor-Specific Analysis

If multiple sensor pairs are evaluated, results should initially be reported separately.

| Sensor Pair | Representation | Inlier Ratio | Coverage | Check-Point RMSE | Failure Rate |
| ----------- | -------------- | -----------: | -------: | ---------------: | -----------: |
| `[TBD]`     | Grayscale      |      `[TBD]` |  `[TBD]` |          `[TBD]` |      `[TBD]` |
| `[TBD]`     | Gradient       |      `[TBD]` |  `[TBD]` |          `[TBD]` |      `[TBD]` |

Potential categories include:

### OHRC

`[Not yet evaluated / TBD]`

### TMC-2

`[Not yet evaluated / TBD]`

### IIRS

`[Not yet evaluated / TBD]`

### LRO Reference

`[Not yet evaluated / TBD]`

A mixed average should not be used unless the benchmark protocol explicitly requires it.

The project feedback recommends adding sensors gradually and treating IIRS separately because its modality and spatial resolution differ substantially.

---

# 39. Runtime

The computational cost of representation conversion should be measured rather than assumed.

| Representation | Preprocessing | Feature Extraction | Matching |   Total |
| -------------- | ------------: | -----------------: | -------: | ------: |
| Grayscale      |       `[TBD]` |            `[TBD]` |  `[TBD]` | `[TBD]` |
| Gradient       |       `[TBD]` |            `[TBD]` |  `[TBD]` | `[TBD]` |

Where implemented, also record:

- gradient computation time;
- edge computation time;
- normalization time;
- preprocessing time;
- feature extraction time;
- matching time;
- total experiment runtime.

Do not claim that gradient processing is cheaper or more expensive without measurements.

---

# 40. Output Artifacts

The exact repository artifact paths are not currently confirmed.

| Artifact                 | Path    | Required    | Status  | Description                                |
| ------------------------ | ------- | ----------- | ------- | ------------------------------------------ |
| Source representation    | `[TBD]` | Yes         | `[TBD]` | Representation used for source matching    |
| Reference representation | `[TBD]` | Yes         | `[TBD]` | Representation used for reference matching |
| Gradient image           | `[TBD]` | Yes         | `[TBD]` | Gradient representation                    |
| Edge image               | `[TBD]` | Optional    | `[TBD]` | Edge representation if implemented         |
| Candidate matches        | `[TBD]` | Yes         | `[TBD]` | Pre-verification correspondences           |
| RANSAC inliers           | `[TBD]` | Yes         | `[TBD]` | Geometrically verified correspondences     |
| Transformation           | `[TBD]` | Yes         | `[TBD]` | Estimated geometric model                  |
| Registered image         | `[TBD]` | Yes         | `[TBD]` | Registered result                          |
| Overlay                  | `[TBD]` | Recommended | `[TBD]` | Visual registration result                 |
| Residual visualization   | `[TBD]` | Recommended | `[TBD]` | Spatial error evidence                     |
| Metrics                  | `[TBD]` | Yes         | `[TBD]` | Quantitative measurements                  |
| Configuration            | `[TBD]` | Yes         | `[TBD]` | Experiment configuration                   |
| Logs                     | `[TBD]` | Recommended | `[TBD]` | Execution information                      |

---

# 41. Reproducibility

Every measured EXP-003 result should be traceable to the exact experimental configuration.

| Item                  | Value     |
| --------------------- | --------- |
| Experiment ID         | `EXP-003` |
| Git commit            | `[TBD]`   |
| Dataset version       | `[TBD]`   |
| Source image          | `[TBD]`   |
| Reference image       | `[TBD]`   |
| Sensor pair           | `[TBD]`   |
| Source GSD            | `[TBD]`   |
| Reference GSD         | `[TBD]`   |
| Scale configuration   | `[TBD]`   |
| Representation        | `[TBD]`   |
| Gradient operator     | `[TBD]`   |
| Kernel                | `[TBD]`   |
| Normalization         | `[TBD]`   |
| SIFT configuration    | `[TBD]`   |
| Matcher configuration | `[TBD]`   |
| RANSAC configuration  | `[TBD]`   |
| Random seed           | `[TBD]`   |
| Python version        | `[TBD]`   |
| OpenCV version        | `[TBD]`   |
| Operating system      | `[TBD]`   |
| CPU                   | `[TBD]`   |
| GPU                   | `[TBD]`   |
| CUDA                  | `[TBD]`   |

### Execution Command

```bash
# EXP-003 execution command
[TBD]
```

A result should not be considered fully reproducible if the representation, input pair, scale configuration, matching configuration, geometric-verification configuration, and evaluation data cannot be reconstructed.

---

# 42. Data Leakage Prevention

EXP-003 must preserve the separation between:

```text
Experiment Inputs
        ↓
Candidate Correspondences
        ↓
Geometric Verification
        ↓
Transformation Fitting
        ↓
Independent Evaluation
```

Evaluation check points must not influence the fitted transformation.

Do not:

- fit the transformation using evaluation check points;
- tune representation parameters directly against hidden evaluation points;
- select a representation because it performs best on a point set that was also used to fit the model;
- report training/fitting residuals as independent registration accuracy.

If parameters are tuned using a specific image pair, that dependency should be documented.

---

# 43. Baseline Comparison

Where appropriate, the same image pairs should be processed through:

1. SIFT baseline;
2. scale-aware SIFT pipeline;
3. EXP-003 structural representation.

The comparison should preserve:

- source/reference pair;
- evaluation points;
- geometric model;
- matching protocol;
- scale strategy where controlled;
- measurement definitions.

The purpose is to determine **which component caused the measured change**.

The project feedback recommends running identical test pairs through the baseline and improved paths rather than comparing unrelated datasets.

---

# 44. Stress-Test Matrix

EXP-003 may participate in the V1 stress-test framework.

| Stress Case          | Purpose                               | EXP-003 Status |
| -------------------- | ------------------------------------- | -------------- |
| Easy pair            | Validate end-to-end operation         | `[TBD]`        |
| Sun-angle difference | Measure illumination sensitivity      | `[TBD]`        |
| Scale difference     | Measure scale sensitivity             | `[TBD]`        |
| Modality difference  | Test structural cross-sensor behavior | `[TBD]`        |
| Geometry difference  | Test transformation assumptions       | `[TBD]`        |
| Low-feature terrain  | Expose false-match behavior           | `[TBD]`        |

The project feedback identifies these cases as useful controlled stress conditions.

---

# 45. Success Criteria

EXP-003 is not successful merely because it produces a visually plausible overlay.

A valid experiment must produce measurable evidence.

## Minimum Experimental Evidence

1. A defined source/reference pair.
2. A documented control representation.
3. A documented experimental representation.
4. Candidate matches.
5. Verified inliers.
6. A geometric transformation.
7. Registration output.
8. Quantitative metrics.
9. Independent check-point evaluation where available.
10. Spatial coverage information where implemented.
11. Runtime information.
12. Failure behavior.
13. Reproducible configuration.

## Scientific Success

A scientific improvement should only be claimed if measurements demonstrate it.

Examples of acceptable statements:

> “Gradient representation produced a higher measured inlier ratio on the evaluated pair.”

> “Gradient representation reduced independent check-point RMSE under the evaluated illumination condition.”

> “No measurable improvement was observed.”

Avoid unsupported statements such as:

- “gradients solve illumination”;
- “gradient representation is robust”;
- “gradient matching is universally better”;
- “the method is illumination invariant.”

---

# 46. Interpretation Rules

Results must distinguish three concepts:

### Hypothesis

What the experiment expected to test.

### Measurement

What the experiment actually recorded.

### Interpretation

What the measurements may indicate.

Example:

```text
Hypothesis:
Gradient representation may improve correspondence under strong
illumination differences.

Measurement:
Verified inlier ratio = [measured value].

Interpretation:
The observed result may indicate improved structural correspondence
for this evaluated condition.

Limitation:
The result does not establish general illumination invariance.
```

This distinction prevents a single successful pair from being treated as proof of general robustness.

---

# 47. Failure Reporting Template

For each important failure, record:

```markdown
## Failure: [Short Description]

### Condition

- Source: [TBD]
- Reference: [TBD]
- Representation: [TBD]
- Illumination: [TBD]
- Scale: [TBD]

### Failure Stage

[TBD]

### Observed Behavior

[TBD]

### Measurements

- Candidate matches: [TBD]
- Verified inliers: [TBD]
- Inlier ratio: [TBD]
- Coverage: [TBD]
- Check-point RMSE: [TBD]
- Runtime: [TBD]

### Evidence

[TBD]

### Suspected Cause

[TBD]

### Impact

[TBD]

### Reproduction

[TBD]

### Next Experiment

[TBD]
```

---

# 48. Reproducible Experiment Record

Each completed EXP-003 run should preserve enough information to answer:

> **What was run, on which data, with which representation, under which configuration, and what did it produce?**

At minimum, preserve:

```text
Experiment ID
Git commit
Dataset / data version
Source image ID
Reference image ID
Representation
Scale configuration
SIFT configuration
Matcher configuration
RANSAC configuration
Evaluation points
Random seed
Environment
Runtime
Metrics
Failure status
Output artifacts
```

---

# 49. Current Implementation Status

Based on the available project materials, the **research direction** for structure-focused preprocessing is clearly specified, but the exact EXP-003 implementation details are not established.

| Component                                    | Status                 |
| -------------------------------------------- | ---------------------- |
| EXP-003 identity                             | Specified              |
| Gradient/structural representation objective | Specified              |
| Raw grayscale control                        | Specified              |
| Illumination motivation                      | Specified              |
| Structural representation comparison         | Specified              |
| SIFT baseline relationship                   | Specified              |
| Scale-control relationship with EXP-002      | Specified conceptually |
| Exact input pair                             | `[TBD]`                |
| Exact gradient operator                      | `[TBD]`                |
| Exact kernel                                 | `[TBD]`                |
| Exact normalization                          | `[TBD]`                |
| Exact SIFT configuration                     | `[TBD]`                |
| Exact matcher configuration                  | `[TBD]`                |
| Exact RANSAC configuration                   | `[TBD]`                |
| Exact transformation model                   | `[TBD]`                |
| Independent check-point dataset              | `[TBD]`                |
| Numerical results                            | Not yet measured       |
| Runtime results                              | Not yet measured       |
| Sensor-specific results                      | `[TBD]`                |
| Illumination stress-test results             | `[TBD]`                |
| Final artifact paths                         | `[TBD]`                |

---

# 50. Known Gaps and Limitations

The following items must be resolved before EXP-003 can be treated as a fully reproducible measured experiment:

- exact source/reference pair;
- exact sensor/product metadata;
- exact source/reference GSD;
- exact scale configuration;
- exact gradient operator;
- exact derivative kernel;
- exact output datatype/range;
- exact normalization;
- exact SIFT configuration;
- exact matching configuration;
- exact RANSAC configuration;
- exact transformation model;
- exact independent check-point set;
- exact artifact paths;
- actual measurements;
- actual runtime;
- actual failure rate;
- actual illumination stress-test results.

These are documentation/implementation gaps, not experimental results.

---

# 51. Scientific Limitations

EXP-003 has several fundamental limitations.

### 51.1 Gradient Is Not Physical Terrain

A gradient represents image intensity change, not necessarily physical terrain geometry.

### 51.2 Shadows Can Become Edges

A gradient detector can respond strongly to illumination-induced shadows.

### 51.3 Illumination Geometry Can Change Structure

Different Sun geometry can move or alter shadows.

### 51.4 Sensor Modalities Differ

A gradient extracted from visible imagery does not necessarily represent the same physical quantity as a gradient extracted from infrared or hyperspectral data.

### 51.5 Scale Still Matters

Structural representation cannot recover spatial information absent from a coarse source.

### 51.6 Registration Geometry Still Matters

Better correspondences do not guarantee that a global affine or homography model is sufficient.

### 51.7 One Pair Is Not Generalization

A successful result on one lunar region does not establish general performance across the Moon.

---

# 52. Repository Relationship

EXP-003 belongs under:

```text
experiments/
└── v1/
    └── preprocessing/
        └── EXP-003-gradient-representation/
            └── README.md
```

The experiment should remain connected conceptually to:

```text
experiments/v1/
experiments/v1/baseline/EXP-001-sift-baseline/
experiments/v1/preprocessing/EXP-002-scale-pyramid/
experiments/templates/EXPERIMENT_TEMPLATE.md
```

The exact contents of the EXP-003 directory beyond this README are:

`[TBD]`

---

# 53. Experiment Documentation vs Benchmark Documentation

EXP-003 documentation describes:

- the scientific question;
- experimental conditions;
- controlled variables;
- representation;
- methodology;
- measurements;
- interpretation;
- failures.

Benchmark documentation defines the broader:

- evaluation protocol;
- datasets;
- ground truth;
- metrics;
- acceptance criteria;
- reproducibility requirements.

EXP-003 should not redefine the benchmark protocol.

Where a benchmark protocol exists, EXP-003 should follow it.

---

# 54. Experiment Documentation vs Dataset Documentation

This README documents **how the experiment uses data**.

Dataset documentation should define:

- dataset identity;
- provenance;
- licensing where documented;
- acquisition/source information;
- metadata;
- ground truth;
- data versioning.

EXP-003 should reference the applicable dataset rather than silently redefining it.

---

# 55. Recommended Execution Flow

When the implementation is available, the controlled workflow should be:

```text
1. Select documented source/reference pair
                ↓
2. Inspect sensor and geometry metadata
                ↓
3. Establish comparable physical scale
                ↓
4. Prepare control grayscale representation
                ↓
5. Prepare EXP-003 structural representation
                ↓
6. Extract SIFT features
                ↓
7. Match descriptors
                ↓
8. Filter candidate matches
                ↓
9. Run RANSAC
                ↓
10. Identify verified inliers
                ↓
11. Estimate transformation
                ↓
12. Register / overlay images
                ↓
13. Evaluate independent check points
                ↓
14. Calculate spatial coverage
                ↓
15. Measure runtime
                ↓
16. Save visual and numerical artifacts
                ↓
17. Compare control vs structural representation
                ↓
18. Document failures
```

---

# 56. Recommended Comparison Logic

The experiment should answer one question at a time.

### Step 1 — Establish Control

Run:

```text
Grayscale
→ SIFT
→ Matching
→ RANSAC
→ Transformation
→ Evaluation
```

### Step 2 — Change Representation

Run:

```text
Gradient
→ SIFT
→ Same Matching
→ Same RANSAC
→ Same Transformation Protocol
→ Same Evaluation
```

### Step 3 — Compare Measurements

Compare:

- candidate matches;
- verified inliers;
- inlier ratio;
- spatial coverage;
- check-point RMSE;
- runtime;
- failure behavior.

### Step 4 — Interpret

Determine whether the measured difference supports or does not support the hypothesis.

---

# 57. What Counts as Evidence?

Strong evidence includes:

- independently measured registration error;
- verified inlier statistics;
- spatial coverage;
- consistent results across controlled pairs;
- reproducible execution;
- preserved failure cases;
- identical evaluation conditions.

Weak evidence includes:

- visual appearance alone;
- matcher confidence alone;
- a single arbitrary successful match;
- decorative percentage scores;
- manually selected examples without measurement;
- claims based only on theory.

The project feedback specifically recommends replacing decorative confidence ratings with actual RMSE, inlier ratio, coverage, and runtime.

---

# 58. No-Fabrication Rule

The following values must never be invented:

- RMSE;
- inlier count;
- inlier ratio;
- spatial coverage;
- runtime;
- success rate;
- improvement percentage;
- confidence score;
- number of images;
- image dimensions;
- GSD;
- scale ratio;
- gradient parameters;
- RANSAC parameters;
- check-point error.

Until measured, use:

`[TBD]`

or:

`Not yet measured.`

---

# 59. Final Experimental Interpretation

EXP-003 should ultimately answer:

> **Under the evaluated lunar image conditions, does structural/gradient representation provide measurably better correspondence than raw grayscale representation?**

Possible scientifically valid conclusions include:

- measurable improvement;
- no measurable improvement;
- improvement only under selected illumination conditions;
- improvement in matching but not registration;
- improvement in inlier ratio but reduced spatial coverage;
- improvement in robustness but increased runtime;
- representation-dependent behavior;
- failure under particular sensor or illumination conditions.

The experiment must report what the measurements show rather than forcing a positive result.

---

# 60. Expected Scientific Contribution

If successfully measured, EXP-003 provides a controlled V1 experiment answering whether structural representation is useful as a preprocessing component in ChandraMap.

Its value is not simply producing another image representation.

Its purpose is to establish evidence about the relationship:

```text
Image Representation
        ↓
Candidate Correspondences
        ↓
Geometric Consistency
        ↓
Spatial Distribution
        ↓
Registration Accuracy
```

This makes EXP-003 a measurable research step rather than a cosmetic preprocessing stage.

---

# 61. Design Principle

> **Every preprocessing step should demonstrate a measurable benefit or be removed.**

EXP-003 therefore treats gradient representation as an experimental variable, not as an assumed improvement.

The result may support the hypothesis, weaken it, or show that its usefulness is conditional.

All three outcomes are scientifically useful.

---

# 62. V1 Position

EXP-003 is one component of the V1 progression:

```text
EXP-001
SIFT Baseline
        ↓
EXP-002
Scale Pyramid
        ↓
EXP-003
Gradient Representation
        ↓
Stronger Local Matching
        ↓
Sub-Pixel Refinement
```

The project feedback recommends this incremental build order: establish one known-pair baseline, add scale/illumination experimentation, then move toward stronger matching and refinement.

EXP-003 should therefore remain small, controlled, measurable, and reproducible.

---

# 63. Experiment Status

**Current status:** `[TBD]`

**Results:** `Not yet measured.`

**Independent check-point evaluation:** `[TBD]`

**Illumination stress test:** `[TBD]`

**Sensor-specific evaluation:** `[TBD]`

**Sub-pixel refinement:** `[Not implemented in EXP-003 / TBD]`

**Reproducibility status:** `[TBD]`

**Known limitations:** See [Gradient-Specific Limitations](#51-gradient-specific-limitations).

---

# 64. Final Principle

> **Build small. Measure honestly. Keep the failures.**

EXP-003 should not be considered complete because a gradient image can be generated or because an overlay looks visually plausible.

It is complete as a scientific experiment when the representation can be evaluated against a controlled grayscale baseline using documented inputs, controlled matching and geometric verification, independent evaluation where available, quantitative metrics, reproducible configuration, and explicit failure analysis.
