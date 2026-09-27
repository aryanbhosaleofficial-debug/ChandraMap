# EXP-004 — Affine vs Homography

> **Experiment ID:** `EXP-004`
> **Version:** `V1`
> **Category:** Geometry / Registration
> **Primary Methods:** Affine Transformation, Homography / Projective Transformation
> **Status:** `[TBD]`

---

## 1. Experiment Overview

EXP-004 evaluates two geometric transformation models for local lunar image registration:

1. **Affine transformation**
2. **Homography / projective transformation**

The comparison uses the same source/reference image pair, correspondence pipeline, geometric verification protocol, and independent evaluation points wherever possible.

The purpose is **not** to declare one transformation universally superior.

The purpose is to measure how the two models behave under the specific lunar imaging and registration conditions represented by the controlled V1 experiment.

The central research principle is:

> **The geometric model used for registration should be selected based on measurable registration accuracy, residual structure, spatial coverage, stability, and physical validity rather than visual alignment alone.**

A homography has greater geometric flexibility than an affine transform, but greater flexibility does not automatically mean greater physical correctness or better registration accuracy.

An affine transformation can be sufficient for a small local region when the effective geometric distortion is approximately affine.

A homography may better model projective effects in some image pairs, but a homography does not model arbitrary lunar surface relief, orthorectification errors, or full 3D sensor geometry.

---

## 2. Scientific Context

ChandraMap is concerned with reliable correspondence and registration between lunar imagery acquired under potentially different:

- spatial resolutions
- viewing geometries
- illumination conditions
- sensor modalities
- map projections
- image scales
- terrain conditions

The project feedback recommends beginning with a simple, measurable registration pipeline:

```text
Local Matches
    ↓
RANSAC
    ↓
Verified Inliers
    ↓
Sub-pixel / Tie-Point Refinement
    ↓
Final Transformation
    ↓
Registered Image
```

For a local, already map-projected image pair, affine or homography is a reasonable first geometric model. However, the Moon is not a flat planar surface, and raw sensor imagery may contain additional viewing and sensor geometry that cannot be represented by a single global 2D transformation.

The project therefore treats affine and homography as **controlled geometric approximations**, not as complete physical models of lunar image formation.

---

## 3. Research Question

### Primary Research Question

> **Which geometric transformation model, affine or homography, produces more accurate and stable local lunar image registration under the controlled conditions of the ChandraMap V1 benchmark?**

### Secondary Questions

1. How many candidate correspondences survive geometric verification under each model?
2. How does the verified inlier ratio differ?
3. Are verified correspondences spatially distributed across the overlap?
4. Does either model reduce independent check-point error?
5. Do residuals reveal systematic spatial distortion?
6. Does either model become unstable under clustered or poorly distributed correspondences?
7. Does either model produce extrapolation or edge-region problems?
8. Are observed differences attributable to transformation geometry rather than changes elsewhere in the correspondence pipeline?
9. Does the model remain physically meaningful over the evaluated region?
10. Are both models inadequate because the dominant error originates from correspondence quality, terrain relief, projection, scale, or sensor geometry?

---

## 4. Hypothesis

### Working Hypothesis

> The appropriate transformation model depends on the effective local geometry of the tested lunar image pair. An affine model may adequately represent small local regions with approximately affine distortion, while a homography may provide additional flexibility when projective effects are present.

This experiment does **not** assume:

```text
Homography > Affine
```

or:

```text
Affine > Homography
```

before measurement.

The experiment is designed to determine whether the additional projective flexibility of a homography produces measurable improvement under independent evaluation.

---

## 5. Important Scientific Principle

> **A homography has greater geometric flexibility than an affine transform, but greater flexibility does not automatically mean greater physical correctness or better registration accuracy.**

### Affine Model

An affine transformation can be sufficient when the effective local distortion is approximately affine.

It can represent:

- translation
- rotation
- uniform scaling
- anisotropic scaling
- shear

but cannot represent general projective perspective effects.

### Homography Model

A homography provides a projective mapping and can represent transformations that cannot be represented by an affine model.

However:

> **A homography does not represent arbitrary 3D lunar terrain deformation.**

Terrain relief, orthorectification errors, sensor geometry, and spatially varying displacement may remain even when a homography produces a visually convincing overlay.

---

# 6. Scope

## 6.1 In Scope

EXP-004 includes:

- controlled source/reference image comparison
- verified local correspondences
- affine transformation estimation
- homography estimation
- RANSAC-based geometric verification
- spatial distribution analysis
- transformation residual analysis
- independent check-point evaluation
- pixel-space registration error
- optional physically meaningful ground-space error
- transformation matrix recording
- runtime measurement
- failure-case documentation
- qualitative registration visualization
- reproducibility metadata

## 6.2 Out of Scope

Unless explicitly added to the experiment configuration, EXP-004 does not establish:

- a universal lunar transformation model
- complete orbital sensor geometry correction
- DEM-based orthorectification
- full 3D photogrammetric reconstruction
- arbitrary piecewise deformation
- universal Sun-angle invariance
- universal cross-sensor robustness
- global lunar retrieval
- full-Moon registration
- production deployment
- a universal ranking of affine and homography
- sub-pixel refinement unless separately implemented and documented

A flexible warp should not be used to hide poor correspondence quality. The project feedback specifically recommends accurate and well-distributed control points before flexible/local warping is introduced.

---

# 7. V1 Pipeline Position

EXP-004 follows the initial V1 correspondence and representation experiments.

```text
EXP-001
SIFT Baseline
      │
      ▼
EXP-002
Scale Pyramid
      │
      ▼
EXP-003
Gradient Representation
      │
      ▼
EXP-004
Affine vs Homography
      │
      ▼
Future:
Sub-pixel Refinement / Stronger Matchers / Sensor-aware Experiments
```

The project build order recommends establishing one measurable end-to-end result before expanding to retrieval, stronger matchers, additional sensors, or more advanced refinement.

---

# 8. Relationship to Previous Experiments

## EXP-001 — SIFT Baseline

EXP-001 establishes the classical feature correspondence baseline:

```text
SIFT
→ Descriptor Matching
→ Candidate Matches
→ RANSAC
→ Transformation
→ Registration
→ Evaluation
```

Its purpose is to establish measurable baseline correspondence and registration behavior.

---

## EXP-002 — Scale Pyramid

EXP-002 addresses differences in effective image scale.

The experiment is based on the principle that:

> Upsampling changes pixel count; it does not recover spatial information that was absent from the source sensor.

The project feedback recommends bringing imagery to comparable effective ground scale rather than simply enlarging low-resolution imagery.

---

## EXP-003 — Gradient Representation

EXP-003 investigates whether structural representations such as gradients or edges can improve correspondence under difficult illumination conditions.

This is important because changing Sun angle can change lunar shadow geometry rather than merely image brightness. Brightness normalization therefore cannot be treated as a complete illumination solution.

---

## EXP-004 — Affine vs Homography

EXP-004 primarily changes:

> **Transformation Model**

while keeping the upstream correspondence pipeline controlled.

The conceptual V1 pipeline is:

```text
Input Images
    ↓
Preprocessing
    ↓
Scale Handling
    ↓
Representation
    ↓
SIFT
    ↓
Candidate Matches
    ↓
Geometric Verification
    ↓
Verified Inliers
    ↓
Transformation Model
    ↓
Independent Check-Point Evaluation
```

---

# 9. Transformation Models

## 9.1 Affine Transformation

An affine transformation can be represented as:

$$
x' = a_1x + a_2y + a_3
$$

$$
y' = a_4x + a_5y + a_6
$$

or in homogeneous matrix form:

$$
\begin{bmatrix}
x'\\
y'\\
1
\end{bmatrix}
=
\begin{bmatrix}
a_1 & a_2 & a_3\\
a_4 & a_5 & a_6\\
0 & 0 & 1
\end{bmatrix}
\begin{bmatrix}
x\\
y\\
1
\end{bmatrix}
$$

### Affine Capabilities

An affine transformation can represent:

- translation
- rotation
- scaling
- anisotropic scaling
- shear

It preserves:

- straight lines
- parallelism

It does not represent general projective perspective distortion.

### Degrees of Freedom

An affine transformation has:

> **6 independent parameters**

The theoretical minimum for solving an affine transformation is:

> **3 non-collinear point correspondences**

The three-point minimum is a mathematical requirement, not a recommended operating point for reliable lunar registration.

The actual experiment should use more correspondences than the theoretical minimum whenever sufficient verified correspondences exist.

---

# 10. Homography / Projective Transformation

A homography can be represented as:

$$
s
\begin{bmatrix}
x'\\
y'\\
1
\end{bmatrix}
=
\begin{bmatrix}
h_1 & h_2 & h_3\\
h_4 & h_5 & h_6\\
h_7 & h_8 & 1
\end{bmatrix}
\begin{bmatrix}
x\\
y\\
1
\end{bmatrix}
$$

where \(s\) is a projective scale factor.

The final matrix is normally normalized because a homography is defined only up to an arbitrary scalar.

### Homography Capabilities

A homography provides a projective mapping that can represent effects that an affine transformation cannot.

It is therefore more geometrically flexible.

However:

> **Homography flexibility should not be interpreted as a general model of lunar terrain.**

A homography cannot independently represent arbitrary 3D relief-induced displacement across a large lunar region.

### Degrees of Freedom

A homography has:

> **8 independent parameters under scale normalization**

The theoretical minimum for solving a homography is:

> **4 non-collinear point correspondences**

Again, four points are a mathematical minimum, not a recommended operating point.

---

# 11. Affine vs Homography Summary

| Property                       | Affine          | Homography                  |
| ------------------------------ | --------------- | --------------------------- |
| Independent parameters         | 6               | 8                           |
| Minimum correspondences        | 3 non-collinear | 4 non-collinear             |
| Translation                    | Yes             | Yes                         |
| Rotation                       | Yes             | Yes                         |
| Scaling                        | Yes             | Yes                         |
| Anisotropic scaling            | Yes             | Yes                         |
| Shear                          | Yes             | Yes                         |
| General projective mapping     | No              | Yes                         |
| Preserves parallel lines       | Yes             | Not generally               |
| Represents arbitrary 3D relief | No              | No                          |
| Main role in EXP-004           | Reference model | Projective comparison model |

The table describes model properties, not expected experimental performance.

---

# 12. Why Lunar Registration Is Different

Lunar registration is not equivalent to aligning two photographs of a flat planar poster.

Potential factors include:

- lunar surface relief
- craters
- crater rims
- ridges
- slopes
- shadows
- Sun-angle differences
- viewing geometry
- sensor projection
- map-projection differences
- orthorectification quality
- scale differences
- local terrain-induced displacement
- imperfect metadata

The project feedback explicitly cautions that the Moon is not flat and that one global transformation may not be sufficient when residuals vary spatially.

---

# 13. Planar / Local Image Geometry

Affine and homography can approximate image-to-image relationships when the relevant region behaves approximately like a planar 2D mapping.

For a sufficiently small local region, some physical effects may appear approximately affine even when the underlying surface is not perfectly planar.

A homography can provide additional projective flexibility when projective effects are present.

However, the experiment must record the spatial domain over which the model is being evaluated.

---

# 14. Surface / 3D Geometry

Neither affine nor homography fully models:

- arbitrary lunar relief
- depth-dependent displacement
- complete camera geometry
- arbitrary terrain-induced parallax
- imperfect orthorectification
- all sensor/viewing geometry
- arbitrary local deformation

Therefore:

```text
Low image-space error
        ≠
Universal physical correctness
```

A transformation can perform well in one local region while failing elsewhere.

---

# 15. Local Registration Assumption

EXP-004 is primarily intended for:

- local overlapping regions
- controlled source/reference pairs
- moderate geometric differences
- already map-projected products where appropriate
- image pairs for which a 2D transformation is a reasonable first approximation

It is not intended to replace raw orbital sensor geometry processing.

If raw sensor imagery is used, the following must be explicitly documented:

| Requirement                     | Status  |
| ------------------------------- | ------- |
| Raw sensor geometry             | `[TBD]` |
| Camera/viewing metadata         | `[TBD]` |
| Map projection                  | `[TBD]` |
| Orthorectification status       | `[TBD]` |
| DEM availability                | `[TBD]` |
| Additional geometric correction | `[TBD]` |

---

# 16. Input Data

The exact image pair must be recorded before results are considered reproducible.

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
| Scale ratio             | `[TBD]` |
| Projection              | `[TBD]` |
| Geographic overlap      | `[TBD]` |
| Illumination conditions | `[TBD]` |
| Viewing geometry        | `[TBD]` |
| Image pair difficulty   | `[TBD]` |

No input value should be fabricated.

---

# 17. Sensor Context

The project documentation identifies several possible lunar data sources, including Chandrayaan-2 OHRC, TMC-2, IIRS, LRO NAC, LRO WAC, and optional Kaguya/SELENE TC data.

However, EXP-004 must only report sensors actually used in the experiment.

Potential sensor-specific considerations include:

| Sensor  | Registration consideration                                                |
| ------- | ------------------------------------------------------------------------- |
| OHRC    | High-detail visible/panchromatic imagery; use actual product metadata     |
| TMC-2   | Coarser panchromatic terrain imagery; scale and projection matter         |
| IIRS    | Hyperspectral/IR data requiring a registration-friendly 2D representation |
| LRO NAC | High-resolution lunar reference imagery when used                         |
| LRO WAC | Additional lunar-scale/reference imagery when used                        |

Different sensor pairs should not automatically be combined into one aggregate result.

---

# 18. Preprocessing

The preprocessing configuration must remain fixed between the affine and homography branches.

| Step           | Method  | Parameters | Input     | Output  | Reason  |
| -------------- | ------- | ---------- | --------- | ------- | ------- |
| Preprocessing  | `[TBD]` | `[TBD]`    | Source    | `[TBD]` | `[TBD]` |
| Preprocessing  | `[TBD]` | `[TBD]`    | Reference | `[TBD]` | `[TBD]` |
| Representation | `[TBD]` | `[TBD]`    | Both      | `[TBD]` | `[TBD]` |
| Resampling     | `[TBD]` | `[TBD]`    | Both      | `[TBD]` | `[TBD]` |

The experiment should preserve relevant geometry metadata including:

- footprint
- pixel scale
- map projection
- viewing geometry
- lighting geometry

where available.

The project feedback specifically recommends preserving these metadata during preprocessing.

---

# 19. Scale Handling

Scale must be documented independently from geometric transformation.

| Property                   | Value   |
| -------------------------- | ------- |
| Source GSD                 | `[TBD]` |
| Reference GSD              | `[TBD]` |
| Effective comparison scale | `[TBD]` |
| Scale ratio                | `[TBD]` |
| Resizing/downsampling      | `[TBD]` |
| Reference pyramid          | `[TBD]` |

Important principle:

> **Upsampling changes pixel count; it does not recover spatial information that was absent from the source sensor.**

Where resolution differences are significant, the higher-resolution side should be brought toward a comparable effective ground scale before fine matching rather than treating interpolation as recovered detail.

If no scale normalization is used:

```text
Scale normalization: [Not implemented / TBD]
```

and the limitation must be recorded.

---

# 20. Illumination Conditions

Illumination can affect the upstream correspondence quality.

| Property                         | Value   |
| -------------------------------- | ------- |
| Source illumination              | `[TBD]` |
| Reference illumination           | `[TBD]` |
| Sun angle                        | `[TBD]` |
| Viewing geometry                 | `[TBD]` |
| Photometric normalization        | `[TBD]` |
| Structure-focused representation | `[TBD]` |

A change in Sun angle can change shadow geometry around lunar terrain.

Brightness normalization can alter image intensity, but it cannot reconstruct a shadow boundary that moved because illumination geometry changed.

Therefore:

> **EXP-004 does not claim illumination invariance.**

Illumination robustness remains partly dependent on the correspondence representation established by earlier experiments.

---

# 21. Controlled Experiment Design

The central controlled variable is:

> **Transformation Model**

### Reference Condition

```text
Affine Transformation
```

### Experimental Comparison

```text
Homography Transformation
```

Calling affine the reference condition does **not** imply that affine is physically correct.

It simply provides a controlled comparison model.

---

## 21.1 Variables to Keep Fixed

Where possible, keep the following identical:

- source image
- reference image
- image crop
- preprocessing
- scale strategy
- representation
- feature detector
- descriptor
- descriptor matcher
- ratio threshold
- cross-check configuration
- candidate filtering
- RANSAC threshold
- RANSAC confidence
- random seed
- evaluation checkpoints
- evaluation coordinate system

The only intended model change is:

```text
Affine
vs
Homography
```

Any additional change must be explicitly documented.

---

# 22. Correspondence Pipeline

The experiment uses:

```text
Source Image
      │
      ▼
Preprocessing
      │
      ▼
Representation
      │
      ▼
SIFT Detection
      │
      ▼
SIFT Description
      │
      ▼
Descriptor Matching
      │
      ▼
Candidate Matches
      │
      ▼
Geometric Verification
      │
      ├──────────────┐
      ▼              ▼
Affine RANSAC    Homography RANSAC
      │              │
      ▼              ▼
Affine Inliers  Homography Inliers
      │              │
      ▼              ▼
Affine Model    Homography Model
      │              │
      └──────┬───────┘
             ▼
Independent Check Points
             │
             ▼
Registration Metrics
```

The correspondence pipeline should otherwise remain controlled.

---

# 23. Candidate Matches vs Verified Inliers

This distinction is mandatory.

## Candidate Matches

Candidate matches are descriptor-level correspondences produced before geometric verification.

They are **not automatically correct correspondences**.

## Verified Inliers

Verified inliers are candidate correspondences that satisfy the geometric consistency criterion under the selected RANSAC model.

The project feedback explicitly recommends calling these **Candidate Matches** rather than implying that matcher confidence proves geometric correctness.

---

## 23.1 Correspondence Counts

| Stage                      |   Count |
| -------------------------- | ------: |
| Source keypoints           | `[TBD]` |
| Reference keypoints        | `[TBD]` |
| Raw descriptor matches     | `[TBD]` |
| Filtered candidate matches | `[TBD]` |
| Affine inliers             | `[TBD]` |
| Homography inliers         | `[TBD]` |

---

# 24. RANSAC

RANSAC is used to estimate and verify the geometric model while rejecting correspondence outliers.

The project feedback recommends:

```text
Candidate Matches
        ↓
RANSAC + Initial Model
        ↓
Inliers
        ↓
Sub-pixel Refinement
        ↓
Final Transform
```

when refinement is implemented.

For EXP-004, the affine and homography branches must use controlled RANSAC configurations.

---

## 24.1 Affine RANSAC Configuration

| Parameter              | Value   |
| ---------------------- | ------- |
| Model                  | Affine  |
| Estimator              | `[TBD]` |
| Reprojection threshold | `[TBD]` |
| Confidence             | `[TBD]` |
| Maximum iterations     | `[TBD]` |
| Minimum inliers        | `[TBD]` |
| Random seed            | `[TBD]` |

---

## 24.2 Homography RANSAC Configuration

| Parameter              | Value      |
| ---------------------- | ---------- |
| Model                  | Homography |
| Estimator              | `[TBD]`    |
| Reprojection threshold | `[TBD]`    |
| Confidence             | `[TBD]`    |
| Maximum iterations     | `[TBD]`    |
| Minimum inliers        | `[TBD]`    |
| Random seed            | `[TBD]`    |

Where physically comparable, the same reprojection threshold should be used.

If thresholds differ, the reason must be documented.

A model should not be aggressively tuned while the other remains at unrelated defaults and then be presented as an unbiased comparison.

---

# 25. RANSAC Model Fitting

## Affine

The mathematical minimum is:

> **3 non-collinear correspondences**

## Homography

The mathematical minimum is:

> **4 non-collinear correspondences**

These minimums do not constitute sufficient evidence of reliable registration.

The experiment should use the full reliable verified correspondence set where appropriate.

---

# 26. Degeneracy and Numerical Stability

Geometric stability depends on both correspondence count and correspondence geometry.

## 26.1 Affine Degeneracy

Affine estimation becomes unstable when points are:

- collinear
- nearly collinear
- excessively clustered
- insufficiently distributed

Three points may mathematically define an affine transform, but poor spatial geometry can make the resulting solution unreliable.

---

## 26.2 Homography Degeneracy

Homography estimation is particularly sensitive to:

- collinear points
- nearly collinear points
- clustered points
- poor spatial coverage
- ill-conditioned configurations
- excessive extrapolation

The experiment must therefore record spatial distribution rather than relying only on the number of inliers.

---

# 27. Spatial Coverage

A large number of inliers is not sufficient if they are concentrated in one small part of the image.

The following metrics may be used where implemented:

- 4×4 grid coverage
- convex-hull area ratio
- bounding-box coverage
- point density
- spatial entropy

The project evaluation guidance specifically identifies grid coverage and convex-hull coverage as useful measures of whether good points are distributed across the overlap.

---

## 27.1 Spatial Coverage Table

| Model      | Inliers | Grid Coverage | Convex Hull Coverage | Distribution |
| ---------- | ------: | ------------: | -------------------: | ------------ |
| Affine     | `[TBD]` |       `[TBD]` |              `[TBD]` | `[TBD]`      |
| Homography | `[TBD]` |       `[TBD]` |              `[TBD]` | `[TBD]`      |

---

# 28. Spatial Distribution Interpretation

### Good Distribution

Correspondences are spread across the evaluation region.

### Clustered Distribution

Most correspondences occur in one small region.

### Edge Concentration

Correspondences are concentrated near image boundaries.

### Nearly Collinear Distribution

Correspondences occupy an approximately one-dimensional structure.

None of these should automatically be labelled a failure without examining the actual geometry and evaluation region.

---

# 29. Transformation Quality

Transformation quality must not be reduced to inlier count.

For each model, measure where available:

- candidate match count
- verified inlier count
- inlier ratio
- residual error
- spatial coverage
- independent check-point RMSE
- median error
- maximum error
- percentile errors
- runtime
- failure rate

The project feedback explicitly emphasizes inlier count, inlier ratio, spatial coverage, independent check-point RMSE, runtime, and failure rate as separate evaluation dimensions.

---

# 30. Inlier Ratio

The inlier ratio is:

$$
\text{Inlier Ratio}
=
\frac{N_{\text{inliers}}}
{N_{\text{candidate}}}
\times 100
$$

where:

- \(N\_{\text{inliers}}\) = geometrically verified correspondences
- \(N\_{\text{candidate}}\) = candidate correspondences entering geometric verification

This metric must not be interpreted independently of spatial coverage or independent registration error.

---

# 31. Independent Check Points

Independent evaluation is a required part of this experiment whenever available.

The transformation should be estimated from control/inlier points.

The resulting transformation should then be evaluated using separate check points.

> **Do not fit and judge on exactly the same points.**

The project feedback explicitly warns that reporting RMSE on the same points used to estimate the transformation can make registration quality appear better than it actually is.

---

## 31.1 Control Points

Control points are used to estimate the transformation.

```text
Control / Inlier Points
        ↓
Model Estimation
```

---

## 31.2 Check Points

Check points are withheld from transformation fitting.

```text
Estimated Transformation
        ↓
Independent Check Points
        ↓
Predicted Positions
        ↓
Registration Error
```

If independent check points are unavailable:

```text
Independent check-point evaluation: [Not available]
```

Fitting residuals must not be reported as independent registration accuracy.

---

# 32. Error Metrics

## 32.1 Pixel Error

Registration error should first be reported in image pixels.

For check point \(i\):

$$
e_i =
\left\|
p_i^{predicted}
-
p_i^{reference}
\right\|_2
$$

---

## 32.2 RMSE

For \(N\) independent check points:

$$
RMSE =
\sqrt{
\frac{1}{N}
\sum_{i=1}^{N}
\left\|
p_i^{predicted}
-
p_i^{reference}
\right\|_2^2
}
$$

---

## 32.3 Median Error

The median of independent check-point errors provides a robust central error measure.

---

## 32.4 Maximum Error

The maximum check-point error identifies the worst observed independent evaluation point.

---

## 32.5 Percentile Error

Percentile errors may be reported where sufficient independent check points exist.

Recommended examples include:

- P50
- P75
- P90
- P95

The actual percentile set must be recorded if implemented.

---

## 32.6 Ground Error

Ground error should only be reported when:

- GSD is known
- projection information is available
- coordinate systems are understood
- the conversion is physically meaningful
- appropriate reference truth exists

The project feedback specifically recommends source-image pixels first and conversion to metres only when GSD and projection make the conversion meaningful.

---

# 33. Expected Error Table

| Model      | RMSE (px) | Median (px) | Max (px) | Ground RMSE | Notes |
| ---------- | --------: | ----------: | -------: | ----------: | ----- |
| Affine     |   `[TBD]` |     `[TBD]` |  `[TBD]` |     `[TBD]` |       |
| Homography |   `[TBD]` |     `[TBD]` |  `[TBD]` |     `[TBD]` |       |

`[TBD]` does not mean zero.

---

# 34. Error Units

Primary registration accuracy:

> **Source/reference image pixels**

Ground-space accuracy:

> **Metres only when physically meaningful**

The same pixel error does not necessarily represent the same ground error across sensors with different GSDs.

Therefore:

```text
Pixel Error
    ↓
Primary reported metric

Ground Error
    ↓
Optional derived metric
only when physically justified
```

---

# 35. Residual Analysis

A single RMSE value is insufficient to characterize geometric model behaviour.

Residuals should be inspected spatially.

For each transformation model, analyze:

- residual magnitude
- residual direction
- residual clustering
- systematic drift
- edge effects
- local deformation
- spatially varying error

---

## 35.1 Recommended Visualization

```text
Reference Image
      +
Independent Check Points
      +
Predicted Check Points
      +
Residual Vectors
```

A representative visualization should show:

- measured point
- predicted point
- vector between them
- error magnitude
- spatial location

---

# 36. Residual Pattern Interpretation

Residual patterns should be treated as diagnostic evidence rather than automatic explanations.

## Uniform Residual Pattern

May indicate:

- translation
- systematic offset
- coordinate registration error

---

## Rotation-Like Pattern

May indicate:

- orientation mismatch
- rotation component
- coordinate-frame issue

---

## Radial Pattern

May indicate:

- scale mismatch
- projective/model mismatch
- spatially varying distortion

---

## Spatially Varying Pattern

May indicate:

- terrain relief
- orthorectification error
- viewing geometry
- insufficient transformation model
- local deformation

These are possible interpretations, not automatic diagnoses.

---

# 37. Local vs Global Validity

A transformation can:

- fit the central region well
- fit one crater well
- fail near the edges
- fail over relief-rich terrain
- behave differently outside the control-point distribution

Therefore distinguish:

### Local Fit

How well the model explains the points used for estimation.

### Independent Regional Fit

How well the model predicts withheld points across the evaluation area.

Do not describe a model as globally correct based on a single local region.

---

# 38. Model Complexity

## Affine

Characteristics:

- 6 parameters
- more constrained
- lower geometric flexibility
- suitable when effective local geometry is approximately affine

## Homography

Characteristics:

- 8 independent parameters
- more flexible
- projective mapping
- potentially more sensitive to correspondence geometry

The experiment must not claim that homography necessarily overfits.

Instead:

> **Greater model flexibility can increase sensitivity to noisy or poorly distributed correspondences, so independent validation is required.**

---

# 39. Illumination and Terrain Interaction

Transformation-model performance is partly dependent on the quality of upstream correspondences.

Different illumination conditions can alter:

- crater-rim visibility
- shadow boundaries
- local feature strength
- apparent structural patterns
- descriptor similarity

Therefore EXP-004 must be interpreted together with EXP-003 when the same image pair and representation are used.

The project feedback recommends testing structural representations because Sun-angle changes can alter shadows rather than only brightness.

---

# 40. Sensor-Specific Evaluation

If multiple sensor pairs are tested, report them separately.

Do not combine different sensor pairs into one number unless the benchmark specification explicitly defines such aggregation.

| Sensor Pair | Model      | Inlier Ratio | Coverage | Check-Point RMSE | Failure Rate |
| ----------- | ---------- | -----------: | -------: | ---------------: | -----------: |
| `[TBD]`     | Affine     |      `[TBD]` |  `[TBD]` |          `[TBD]` |      `[TBD]` |
| `[TBD]`     | Homography |      `[TBD]` |  `[TBD]` |          `[TBD]` |      `[TBD]` |

Potential sensor-specific differences include spatial scale, modality, illumination response, and geometric properties.

The project feedback specifically recommends separate sensor paths and separate sensor results rather than treating OHRC, TMC-2, and IIRS as identical imagery.

---

# 41. Experiment Matrix

The minimum controlled comparison is:

| Experiment | Representation | Scale | Model      | RANSAC | Check Points | Purpose          |
| ---------- | -------------- | ----- | ---------- | ------ | ------------ | ---------------- |
| G0         | Fixed          | Fixed | Affine     | Same   | Same         | Reference model  |
| G1         | Fixed          | Fixed | Homography | Same   | Same         | Model comparison |

If EXP-003 representations are intentionally tested as an additional factor:

| Experiment | Representation | Scale | Model      | RANSAC | Check Points | Purpose                   |
| ---------- | -------------- | ----- | ---------- | ------ | ------------ | ------------------------- |
| G2         | Grayscale      | Fixed | Affine     | Same   | Same         | Representation + geometry |
| G3         | Grayscale      | Fixed | Homography | Same   | Same         | Representation + geometry |
| G4         | Gradient       | Fixed | Affine     | Same   | Same         | Representation + geometry |
| G5         | Gradient       | Fixed | Homography | Same   | Same         | Representation + geometry |

These additional combinations should only be executed if representation is explicitly part of the experiment.

Otherwise, G0/G1 is the primary controlled comparison.

---

# 42. Geometry-Only Ablation

The cleanest geometry ablation is:

```text
Fixed Image Pair
Fixed Preprocessing
Fixed Scale
Fixed Representation
Fixed SIFT
Fixed Matcher
Fixed Candidate Filtering
Fixed RANSAC Configuration
Fixed Check Points

             │
       ┌─────┴─────┐
       ▼           ▼
    Affine      Homography
       │           │
       └─────┬─────┘
             ▼
    Independent Evaluation
```

This isolates transformation model as the intended experimental variable.

---

# 43. Representation + Geometry Ablation

If supported by the experiment design, geometry can also be studied jointly with representation.

Example:

```text
                    Affine     Homography
                    -------    ----------
Grayscale             G2          G3
Gradient              G4          G5
```

However, changes between rows and columns must not be conflated.

If both representation and transformation change, the resulting comparison measures an interaction rather than a pure geometry effect.

---

# 44. Baseline Comparison

Comparisons with previous experiments are valid only when the pipelines are sufficiently controlled.

| Metric            | EXP-001 | EXP-002 | EXP-003 | EXP-004 Affine | EXP-004 Homography |
| ----------------- | ------: | ------: | ------: | -------------: | -----------------: |
| Candidate Matches | `[TBD]` | `[TBD]` | `[TBD]` |        `[TBD]` |            `[TBD]` |
| Inliers           | `[TBD]` | `[TBD]` | `[TBD]` |        `[TBD]` |            `[TBD]` |
| Inlier Ratio      | `[TBD]` | `[TBD]` | `[TBD]` |        `[TBD]` |            `[TBD]` |
| Coverage          | `[TBD]` | `[TBD]` | `[TBD]` |        `[TBD]` |            `[TBD]` |
| Check-Point RMSE  | `[TBD]` | `[TBD]` | `[TBD]` |        `[TBD]` |            `[TBD]` |
| Runtime           | `[TBD]` | `[TBD]` | `[TBD]` |        `[TBD]` |            `[TBD]` |

A geometry experiment does not inherently change candidate-match quality because candidate matches are generated upstream.

Any observed difference in candidate matches must therefore be explained by an explicit feedback mechanism or a change elsewhere in the pipeline.

---

# 45. Results

> **Results: [TBD]**

Results must be recorded only after the experiment has been executed and measured.

No placeholder should be interpreted as a measured value.

---

## 45.1 Quantitative Results

| Metric            |  Affine | Homography | Difference | Notes |
| ----------------- | ------: | ---------: | ---------: | ----- |
| Candidate matches | `[TBD]` |    `[TBD]` |    `[TBD]` |       |
| Verified inliers  | `[TBD]` |    `[TBD]` |    `[TBD]` |       |
| Inlier ratio      | `[TBD]` |    `[TBD]` |    `[TBD]` |       |
| Spatial coverage  | `[TBD]` |    `[TBD]` |    `[TBD]` |       |
| Check-point RMSE  | `[TBD]` |    `[TBD]` |    `[TBD]` | px    |
| Median error      | `[TBD]` |    `[TBD]` |    `[TBD]` | px    |
| Maximum error     | `[TBD]` |    `[TBD]` |    `[TBD]` | px    |
| Runtime           | `[TBD]` |    `[TBD]` |    `[TBD]` | s     |

---

## 45.2 Residual Analysis

```text
Affine residual analysis:
[TBD]

Homography residual analysis:
[TBD]
```

---

## 45.3 Spatial Coverage

```text
Affine spatial coverage:
[TBD]

Homography spatial coverage:
[TBD]
```

---

## 45.4 Independent Check-Point Evaluation

```text
Independent check-point evaluation:
[TBD]
```

---

## 45.5 Runtime

```text
Affine runtime:
[TBD]

Homography runtime:
[TBD]
```

---

## 45.6 Failure Cases

```text
Observed failure cases:
[TBD]
```

---

## 45.7 Qualitative Visualization

Required visualization outputs:

- source image
- reference image
- candidate matches
- affine RANSAC inliers
- homography RANSAC inliers
- affine registered image
- homography registered image
- independent check-point residuals
- residual vector fields
- difference/overlay images

A visually convincing overlay is not sufficient evidence of correct registration.

---

# 46. Qualitative Analysis

The visual comparison should include the following sequence.

## 46.1 Original Images

```text
Source Image
Reference Image
```

## 46.2 Candidate Correspondences

```text
Candidate Matches
```

These are pre-verification correspondences.

## 46.3 Affine Inliers

```text
Affine RANSAC Inliers
```

## 46.4 Homography Inliers

```text
Homography RANSAC Inliers
```

## 46.5 Registered Outputs

```text
Affine Registered Image
Homography Registered Image
```

## 46.6 Independent Residuals

```text
Affine Check-Point Residuals
Homography Check-Point Residuals
```

## 46.7 Difference / Overlay

```text
Affine Difference / Overlay
Homography Difference / Overlay
```

Visualizations should support the quantitative evaluation rather than replace it.

---

# 47. Failure Analysis

Failures are first-class experimental results.

Potential failure modes include:

- insufficient inliers
- collinear correspondences
- clustered correspondences
- unstable affine model
- unstable homography
- numerical instability
- extrapolation artifacts
- edge-region distortion
- terrain-relief-induced displacement
- orthorectification error
- incorrect projection assumptions
- scale mismatch
- repeated crater patterns
- shadow-derived false correspondences
- local model valid but regional model invalid
- excessive projective distortion
- insufficient spatial coverage

---

## 47.1 Failure Record

For each actual failure, record:

### Condition

`[TBD]`

### Model

`[TBD]`

### Observed Behavior

`[TBD]`

### Evidence

`[TBD]`

### Suspected Cause

`[TBD]`

### Impact

`[TBD]`

### Reproduction

`[TBD]`

### Follow-Up Experiment

`[TBD]`

Failures must not be silently removed from the benchmark.

---

# 48. Model Failure Interpretation

The results must not be reduced to simplistic conclusions.

## Case: Affine Has Lower RMSE

Possible interpretation:

> The tested local geometry may be adequately described by an affine model.

This does **not** establish that affine is universally superior.

---

## Case: Homography Has Lower RMSE

Possible interpretation:

> The tested image pair may contain projective effects that the affine model does not capture.

This does **not** establish that homography is universally superior.

---

## Case: Homography Has More Inliers but Higher Check-Point Error

Possible interpretation:

> The additional flexibility may fit the correspondence set without providing better independent registration.

This demonstrates why independent validation is necessary.

---

## Case: Both Perform Poorly

The dominant problem may not be the transformation model.

Possible causes include:

- incorrect correspondences
- insufficient overlap
- scale mismatch
- terrain relief
- projection error
- sensor geometry
- illumination differences
- poor representation
- inadequate image quality
- insufficient spatial coverage

The experiment should not automatically attribute poor registration to affine or homography.

---

# 49. Geometric Validity

A transformation can have low image-space error while remaining physically inappropriate outside the evaluated region.

The final experiment report must therefore document:

- spatial domain of validity
- evaluation region
- control-point distribution
- check-point distribution
- extrapolation risk
- terrain-relief limitations
- projection assumptions
- sensor geometry assumptions

---

# 50. Extrapolation Risk

A model estimated from points within one region should not automatically be trusted far outside that region.

This is especially important for homography because projective mappings can produce substantial changes when evaluated outside the spatial support of the correspondences.

The experiment should identify whether the registered output is:

```text
Interpolation
```

or:

```text
Extrapolation
```

where this distinction is relevant.

---

# 51. Transformation Matrix Output

The experiment must save the estimated transformation matrices.

## Affine

An affine transform may be stored as:

- a 2×3 matrix, or
- an equivalent homogeneous 3×3 matrix.

## Homography

A homography should be stored as a:

- 3×3 projective matrix

with its normalization convention documented.

---

## 51.1 Matrix Output Table

| Model      | Matrix Path | Format  | Normalization |
| ---------- | ----------- | ------- | ------------- |
| Affine     | `[TBD]`     | `[TBD]` | `[TBD]`       |
| Homography | `[TBD]`     | `[TBD]` | `[TBD]`       |

---

## 51.2 Transformation Direction

The experiment must explicitly state the transformation direction.

```text
Source → Reference
```

or:

```text
Reference → Source
```

Current configuration:

```text
Transformation direction: [TBD]
```

The direction must be consistent across:

- matrix storage
- registration
- residual calculation
- visualization
- check-point evaluation

---

# 52. Matrix Quality Checks

Where implemented, record:

- matrix values
- normalization convention
- determinant where meaningful
- conditioning diagnostics where available
- model-estimation success/failure
- valid image domain
- extrapolation behaviour

Do not add numerical diagnostics that are not actually computed.

---

# 53. Independent Evaluation Protocol

The recommended evaluation sequence is:

```text
1. Generate candidate correspondences
2. Estimate initial geometric model with RANSAC
3. Identify verified inliers
4. Optionally refine verified tie-point coordinates
5. Refit final transformation if refinement is implemented
6. Apply transformation to independent check points
7. Calculate residuals
8. Calculate RMSE
9. Calculate median and maximum error
10. Inspect spatial residual structure
11. Compare affine and homography
```

The project feedback explicitly places sub-pixel refinement after reliable inliers and before final transformation refitting.

If refinement is not implemented in EXP-004:

```text
Sub-pixel refinement: [Not implemented]
```

and the limitation should be recorded.

---

# 54. Spatial Residual Diagnostics

For each check point:

| ID      | X Reference | Y Reference | X Predicted | Y Predicted | Error (px) | Direction | Model      |
| ------- | ----------: | ----------: | ----------: | ----------: | ---------: | --------- | ---------- |
| `[TBD]` |     `[TBD]` |     `[TBD]` |     `[TBD]` |     `[TBD]` |    `[TBD]` | `[TBD]`   | Affine     |
| `[TBD]` |     `[TBD]` |     `[TBD]` |     `[TBD]` |     `[TBD]` |    `[TBD]` | `[TBD]`   | Homography |

This table should be populated from the actual evaluation artifact.

---

# 55. Registration Output

Required output information:

| Output                      | Affine  | Homography |
| --------------------------- | ------- | ---------- |
| Registered image            | `[TBD]` | `[TBD]`    |
| Transformation matrix       | `[TBD]` | `[TBD]`    |
| Verified inlier coordinates | `[TBD]` | `[TBD]`    |
| Check-point predictions     | `[TBD]` | `[TBD]`    |
| Residual vectors            | `[TBD]` | `[TBD]`    |
| Metrics                     | `[TBD]` | `[TBD]`    |
| Visualization               | `[TBD]` | `[TBD]`    |

---

# 56. Expected Experimental Artifacts

The experiment should produce, where implemented:

```text
Source/reference metadata
Candidate match visualization
Affine inlier visualization
Homography inlier visualization
Affine transformation matrix
Homography transformation matrix
Affine registered image
Homography registered image
Independent check-point results
Residual vector visualization
Spatial coverage statistics
Quantitative metrics
Runtime measurements
Failure records
Experiment configuration
```

The project feedback identifies match plots, rejected outliers, registered overlays, inlier statistics, and check-point error as useful evidence for the first end-to-end milestone.

---

# 57. Reproducibility

Every executed EXP-004 run should record enough information to reproduce the comparison.

| Item                     | Value     |
| ------------------------ | --------- |
| Experiment ID            | `EXP-004` |
| Git commit               | `[TBD]`   |
| Dataset version          | `[TBD]`   |
| Source image             | `[TBD]`   |
| Reference image          | `[TBD]`   |
| Sensor pair              | `[TBD]`   |
| Projection               | `[TBD]`   |
| Representation           | `[TBD]`   |
| Scale strategy           | `[TBD]`   |
| SIFT configuration       | `[TBD]`   |
| Matcher configuration    | `[TBD]`   |
| RANSAC configuration     | `[TBD]`   |
| Affine configuration     | `[TBD]`   |
| Homography configuration | `[TBD]`   |
| Random seed              | `[TBD]`   |
| Check-point set          | `[TBD]`   |
| Python version           | `[TBD]`   |
| OpenCV version           | `[TBD]`   |
| OS                       | `[TBD]`   |
| CPU                      | `[TBD]`   |
| GPU                      | `[TBD]`   |
| CUDA                     | `[TBD]`   |

---

# 58. Execution Command

If the actual command is known, record it here.

```bash
# EXP-004 execution command
[TBD]
```

Do not replace `[TBD]` with an invented command.

---

# 59. Configuration Record

The executed configuration should be preserved separately when supported.

```yaml
experiment:
  id: EXP-004
  name: affine-vs-homography
  version: V1
  status: "[TBD]"

data:
  source_image: "[TBD]"
  reference_image: "[TBD]"
  dataset_version: "[TBD]"

preprocessing:
  method: "[TBD]"

scale:
  strategy: "[TBD]"

representation:
  method: "[TBD]"

features:
  detector: SIFT
  configuration: "[TBD]"

matching:
  method: "[TBD]"
  configuration: "[TBD]"

ransac:
  threshold: "[TBD]"
  confidence: "[TBD]"
  iterations: "[TBD]"
  random_seed: "[TBD]"

models:
  affine:
    enabled: true
    configuration: "[TBD]"

  homography:
    enabled: true
    configuration: "[TBD]"

evaluation:
  checkpoint_set: "[TBD]"
  pixel_rmse: true
  ground_error: "[TBD]"
  spatial_coverage: "[TBD]"

environment:
  python: "[TBD]"
  opencv: "[TBD]"
  os: "[TBD]"
  cpu: "[TBD]"
  gpu: "[TBD]"
  cuda: "[TBD]"
```

> This is a documentation template for the executed configuration. It is not evidence that the fields have already been implemented.

---

# 60. Reproducibility Checklist

Before considering the experiment reproducible, verify:

- [ ] Source image identified
- [ ] Reference image identified
- [ ] Dataset version recorded
- [ ] Image dimensions recorded
- [ ] GSD recorded
- [ ] Projection recorded
- [ ] Scale strategy recorded
- [ ] Representation recorded
- [ ] SIFT configuration recorded
- [ ] Matcher configuration recorded
- [ ] Candidate filtering recorded
- [ ] RANSAC configuration recorded
- [ ] Random seed recorded
- [ ] Affine configuration recorded
- [ ] Homography configuration recorded
- [ ] Check-point set recorded
- [ ] Git commit recorded
- [ ] Software versions recorded
- [ ] Hardware recorded
- [ ] Execution command recorded
- [ ] Transformation matrices saved
- [ ] Quantitative results saved
- [ ] Failure cases retained

---

# 61. Fairness and Experimental Controls

The comparison is considered controlled only if the two model branches receive equivalent upstream evidence.

### Same

```text
Image Pair
Preprocessing
Scale
Representation
Keypoints
Descriptors
Candidate Matches
Check Points
Evaluation Region
```

### Intended Difference

```text
Transformation Model
```

If any other component changes, record it.

---

# 62. Avoiding Hidden Tuning Bias

The following practices should be avoided:

```text
Affine:
default parameters

Homography:
heavily tuned parameters
```

followed by a claim that the models were directly compared.

Instead:

1. define the configuration before measurement where possible
2. use comparable RANSAC criteria
3. record all changes
4. retain failed runs
5. distinguish exploratory tuning from final controlled evaluation

---

# 63. Failure Rate

If multiple controlled pairs are evaluated, define:

$$
Failure\ Rate =
\frac{N_{failed}}
{N_{attempted}}
$$

The definition of failure must be recorded before aggregation.

Possible failure criteria may include:

- transformation estimation failure
- insufficient verified inliers
- invalid matrix
- inability to produce registered output
- missing independent evaluation
- evaluation error exceeding a predefined benchmark criterion

The actual benchmark criterion must not be invented here.

---

# 64. Multi-Pair Evaluation

A single pair is useful for establishing the pipeline but cannot establish broad generality.

If multiple pairs become available, retain pair-level results.

| Pair    | Model      | Inliers | Inlier Ratio | Coverage | RMSE (px) | Runtime | Failure |
| ------- | ---------- | ------: | -----------: | -------: | --------: | ------: | ------- |
| `[TBD]` | Affine     | `[TBD]` |      `[TBD]` |  `[TBD]` |   `[TBD]` | `[TBD]` | `[TBD]` |
| `[TBD]` | Homography | `[TBD]` |      `[TBD]` |  `[TBD]` |   `[TBD]` | `[TBD]` | `[TBD]` |

Do not hide individual failures inside an average.

---

# 65. Statistical Summary for Multiple Pairs

If enough independent pairs are available, report:

- mean
- median
- standard deviation where appropriate
- percentile values
- failure rate
- per-pair results

Do not reduce the experiment to a single aggregate score unless the benchmark specification explicitly requires it.

---

# 66. Runtime

Runtime should be measured under a documented hardware/software environment.

Record:

| Stage                      |  Affine | Homography |
| -------------------------- | ------: | ---------: |
| Model estimation           | `[TBD]` |    `[TBD]` |
| Transformation application | `[TBD]` |    `[TBD]` |
| Evaluation                 | `[TBD]` |    `[TBD]` |
| Total experiment runtime   | `[TBD]` |    `[TBD]` |

If the upstream pipeline is identical, separate geometry runtime from total pipeline runtime where possible.

---

# 67. What Counts as Evidence?

### Stronger Evidence

- independent check-point error
- distributed check points
- stable transformation matrix
- consistent results across controlled pairs
- residual-vector analysis
- retained failure cases
- reproducible configuration

### Weaker Evidence

- visually pleasing overlay
- more raw matches
- more inliers without spatial coverage
- lower fitting residual only
- a single successful example
- subjective visual preference

---

# 68. Interpretation Rules

The experiment should follow these rules:

### Rule 1

Do not equate more inliers with better registration.

### Rule 2

Do not equate lower fitting residual with better independent accuracy.

### Rule 3

Do not evaluate a model only on the points used to estimate it.

### Rule 4

Do not ignore spatial distribution.

### Rule 5

Do not claim physical correctness from image-space alignment alone.

### Rule 6

Do not generalize from one lunar region to all lunar terrain.

### Rule 7

Do not treat homography as a substitute for 3D geometry.

### Rule 8

Do not treat affine as universally sufficient.

### Rule 9

Do not use a flexible warp to hide poor correspondences.

### Rule 10

Do not report unmeasured performance.

---

# 69. Decision Framework

EXP-004 should not produce a universal model ranking.

Instead, the experiment should document the measured behaviour of each model.

The final interpretation should consider:

```text
Independent Accuracy
        +
Residual Structure
        +
Spatial Coverage
        +
Numerical Stability
        +
Failure Rate
        +
Runtime
        +
Physical Validity
```

A model should only be considered suitable for the tested condition when its behaviour is supported by the measured evidence.

---

# 70. Example Interpretation Template

After execution, the final interpretation can be documented using:

```text
Under the tested conditions:

Affine:
[TBD]

Homography:
[TBD]

Independent check-point evaluation:
[TBD]

Residual structure:
[TBD]

Spatial coverage:
[TBD]

Observed failure modes:
[TBD]

Physical/geometric limitations:
[TBD]

Conclusion for this experimental condition:
[TBD]
```

The conclusion must remain specific to the tested data and conditions.

---

# 71. Limitations

Known limitations of this experiment include:

1. A 2D transformation is an approximation of potentially 3D lunar geometry.
2. Terrain relief can produce spatially varying displacement.
3. A single global model may not describe an entire image.
4. Correspondence errors can dominate transformation-model performance.
5. Illumination differences can affect feature correspondence before geometric estimation.
6. Different GSDs make direct pixel-error comparison across sensors potentially misleading.
7. Map-projection and orthorectification quality can affect observed residuals.
8. Poor spatial distribution can make either model unstable.
9. A single successful image pair does not establish generality.
10. A homography does not provide arbitrary terrain deformation modelling.
11. An affine model does not represent general projective distortion.
12. Visual alignment alone cannot establish registration accuracy.

---

# 72. Expected Follow-Up Experiments

Depending on the observed residual structure, EXP-004 may motivate:

### EXP-005 — Residual Analysis

Detailed analysis of spatially varying registration errors.

Potential focus:

- residual vectors
- local drift
- edge effects
- terrain-related patterns
- model inadequacy

### Sub-pixel Refinement

Refine verified control points and refit the final transformation.

### Local / Piecewise Refinement

Consider only after:

- correspondence quality is established
- control points are accurate
- points are well distributed
- residual structure justifies additional flexibility

### Sensor Geometry / DEM-Based Correction

Consider when residuals indicate physical geometry rather than a simple 2D transformation problem.

---

# 73. Connection to EXP-005

EXP-004 should establish which geometric models produce measurable residual behaviour.

EXP-005 can then investigate:

```text
Transformation
      ↓
Residuals
      ↓
Spatial Pattern
      ↓
Possible Cause
      ↓
Model / Geometry Follow-Up
```

This preserves the V1 principle of measuring the simple model before introducing more complex correction.

---

# 74. Benchmark Integrity

The experiment should preserve:

- unsuccessful runs
- insufficient-inlier cases
- unstable transformations
- poor spatial coverage
- high-error check points
- invalid outputs
- failed registrations

Do not silently remove difficult cases simply because they make the average worse.

The project guidance explicitly emphasizes showing what works, where it fails, and what the pipeline improves.

---

# 75. Results Status

| Result Category          | Status  |
| ------------------------ | ------- |
| Input pair               | `[TBD]` |
| Affine model             | `[TBD]` |
| Homography model         | `[TBD]` |
| Candidate matches        | `[TBD]` |
| Verified inliers         | `[TBD]` |
| Spatial coverage         | `[TBD]` |
| Independent check points | `[TBD]` |
| RMSE                     | `[TBD]` |
| Residual analysis        | `[TBD]` |
| Runtime                  | `[TBD]` |
| Failure analysis         | `[TBD]` |
| Transformation matrices  | `[TBD]` |
| Reproducibility record   | `[TBD]` |

---

# 76. Experiment Completion Checklist

## Data

- [ ] Source image identified
- [ ] Reference image identified
- [ ] Sensor information recorded
- [ ] Product information recorded
- [ ] Image dimensions recorded
- [ ] GSD recorded
- [ ] Projection recorded
- [ ] Overlap documented
- [ ] Illumination documented
- [ ] Viewing geometry documented

## Pipeline

- [ ] Preprocessing fixed
- [ ] Scale strategy fixed
- [ ] Representation fixed
- [ ] SIFT configuration fixed
- [ ] Matcher configuration fixed
- [ ] Candidate filtering fixed
- [ ] RANSAC configuration fixed
- [ ] Random seed recorded

## Geometry

- [ ] Affine model estimated
- [ ] Homography estimated
- [ ] Matrix direction recorded
- [ ] Matrix format recorded
- [ ] Matrix outputs saved
- [ ] Degeneracy checked
- [ ] Spatial distribution measured

## Evaluation

- [ ] Independent check points available
- [ ] Check points withheld from fitting
- [ ] Pixel RMSE measured
- [ ] Median error measured
- [ ] Maximum error measured
- [ ] Percentile error measured where appropriate
- [ ] Ground error calculated only if meaningful
- [ ] Residual vectors inspected
- [ ] Spatial coverage measured
- [ ] Runtime measured
- [ ] Failure cases recorded

## Reproducibility

- [ ] Git commit recorded
- [ ] Dataset version recorded
- [ ] Configuration recorded
- [ ] Environment recorded
- [ ] Execution command recorded
- [ ] Artifacts saved

---

# 77. Final Experiment Record

```yaml
experiment:
  id: EXP-004
  name: Affine vs Homography
  version: V1
  category: Geometry / Registration
  status: "[TBD]"

research_question: >
  Which geometric transformation model, affine or homography,
  produces more accurate and stable local lunar image registration
  under the controlled conditions of the ChandraMap V1 benchmark?

input:
  source_image: "[TBD]"
  reference_image: "[TBD]"
  source_sensor: "[TBD]"
  reference_sensor: "[TBD]"
  source_product: "[TBD]"
  reference_product: "[TBD]"
  source_gsd: "[TBD]"
  reference_gsd: "[TBD]"
  projection: "[TBD]"
  overlap: "[TBD]"
  illumination: "[TBD]"
  viewing_geometry: "[TBD]"

correspondence:
  detector: SIFT
  descriptor: "[TBD]"
  matcher: "[TBD]"
  candidate_filtering: "[TBD]"

geometry:
  affine:
    enabled: true
    matrix: "[TBD]"
    inliers: "[TBD]"
    inlier_ratio: "[TBD]"

  homography:
    enabled: true
    matrix: "[TBD]"
    inliers: "[TBD]"
    inlier_ratio: "[TBD]"

evaluation:
  check_points: "[TBD]"
  affine_rmse_px: "[TBD]"
  homography_rmse_px: "[TBD]"
  affine_median_px: "[TBD]"
  homography_median_px: "[TBD]"
  affine_max_px: "[TBD]"
  homography_max_px: "[TBD]"
  affine_coverage: "[TBD]"
  homography_coverage: "[TBD]"

runtime:
  affine_seconds: "[TBD]"
  homography_seconds: "[TBD]"

reproducibility:
  git_commit: "[TBD]"
  dataset_version: "[TBD]"
  random_seed: "[TBD]"
  python_version: "[TBD]"
  opencv_version: "[TBD]"
  os: "[TBD]"
  cpu: "[TBD]"
  gpu: "[TBD]"
  cuda: "[TBD]"
```

---

# 78. Scientific Conclusion Template

The final conclusion must be completed only after the experiment has been executed.

```text
## Conclusion

EXP-004 compared affine and homography transformation models while
holding the upstream correspondence pipeline and independent evaluation
protocol controlled.

Under the tested lunar image-pair conditions:

- Affine registration result: [TBD]
- Homography registration result: [TBD]
- Independent check-point RMSE: [TBD]
- Spatial coverage: [TBD]
- Residual structure: [TBD]
- Stability/failure behaviour: [TBD]
- Runtime: [TBD]

The observed result is specific to the evaluated image pair(s),
representation, scale, sensor configuration, and geometric conditions.

It should not be interpreted as evidence that either affine or homography
is universally correct for lunar image registration.

The dominant limitations identified in this experiment are:

[TBD]
```

---

# 79. Project-Level Interpretation Boundary

EXP-004 is one controlled layer of the ChandraMap V1 pipeline.

It does not independently establish:

```text
Universal lunar registration
```

It establishes evidence about:

```text
Affine vs Homography
under controlled experimental conditions
```

The project should continue to distinguish:

```text
Correspondence Quality
        ↓
Geometric Verification
        ↓
Transformation Model
        ↓
Independent Accuracy
        ↓
Physical Validity
```

rather than collapsing all registration behaviour into a single visual overlay or confidence score.

---

# 80. Source and Project Basis

This experiment documentation is based on the project materials and technical guidance supplied for ChandraMap, including:

- `SIH26166 Silarlar PS.pdf`
- `Aryan_SIH_26166_Lunar_Image_Correspondence_Feedback.pptx`
- `Aryan_Lunar_Image_Registration_Feedback.pdf`
- ChandraMap V1 experiment structure
- V1 benchmark and ground-truth documentation
- EXP-001 SIFT Baseline
- EXP-002 Scale Pyramid
- EXP-003 Gradient Representation
- project guidance on independent check points
- project guidance on RANSAC, spatial coverage, residual analysis, scale handling, illumination, and transformation modelling

The supplied technical feedback specifically recommends a simple baseline of:

```text
SIFT
→ Descriptor Matching
→ RANSAC
→ Affine/Homography
→ Residual Error
```

while emphasizing that lunar terrain is not a flat planar surface and that residual vectors should be inspected across the image.

The project milestone definition further requires a measurable end-to-end result consisting of:

```text
Input
→ Candidate Matches
→ Verified Inliers
→ Final Transform
→ Registered Overlay
→ Numerical Error on Independent Check Points
```

before treating the architecture as a demonstrated system.

---

# 81. Core Principle

> **Build small. Measure honestly. Keep the failures.**

EXP-004 should therefore produce evidence about how affine and homography behave on the tested lunar registration problem rather than forcing a predetermined answer.
