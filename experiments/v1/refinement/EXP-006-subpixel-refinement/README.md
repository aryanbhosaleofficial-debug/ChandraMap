# EXP-006 — Sub-Pixel Refinement

> **Experiment ID:** `EXP-006`
> **Experiment Name:** `Sub-Pixel Refinement`
> **Version:** `V1`
> **Category:** `Refinement / Registration Accuracy`
> **Primary Predecessor:** `EXP-005 — Residual Analysis`
> **Status:** `[TBD]`

---

## 1. Overview

EXP-006 evaluates whether **sub-pixel refinement of already verified correspondences** can improve the geometric accuracy of lunar image registration.

The experiment operates **after** reliable correspondences have been established and an initial geometric transformation has been estimated.

The core pipeline is:

```text
Source Image
    ↓
Correspondence Detection
    ↓
Candidate Matches
    ↓
Geometric Verification
    ↓
Verified Inliers
    ↓
Initial Geometric Transformation
    ↓
Sub-Pixel Refinement
    ↓
Final Transformation
    ↓
Independent Check-Point Evaluation
    ↓
Residual Analysis
```

The experiment does **not** assume that sub-pixel refinement improves registration. Its purpose is to measure whether fractional-pixel localization produces a reproducible improvement in **independent registration accuracy**.

The project feedback specifically recommends refining verified inlier coordinates after RANSAC and then refitting the final transformation.

---

## 2. Scientific Objective

### Primary Objective

Determine whether sub-pixel localization/refinement of verified correspondence points reduces independent registration error after a robust initial geometric transformation has already been established.

### Primary Research Question

> Can sub-pixel localization and/or refinement reduce independent registration error after robust correspondences and an initial geometric transformation have already been established?

### Secondary Questions

1. Does sub-pixel refinement improve check-point RMSE?
2. Does it improve median and high-percentile errors?
3. Does it reduce systematic residual structure?
4. Does it improve both x and y localization accuracy?
5. Does refinement behave differently for affine and homography models?
6. Does refinement improve accuracy without substantially increasing runtime?
7. Does refinement remain beneficial across different image scales?
8. Does refinement remain useful under illumination differences?
9. Is the measured improvement larger than the uncertainty/noise level?
10. Does refinement improve actual independent registration rather than merely reducing fitting residuals?

---

## 3. Scientific Purpose

EXP-006 is a **refinement experiment**, not a replacement for the earlier stages of the registration pipeline.

It does not replace:

- feature detection
- feature description
- descriptor matching
- geometric verification
- transformation estimation
- independent evaluation

Instead, it evaluates whether already reliable correspondence coordinates can be localized more precisely before the final transformation is fitted.

The intended scientific sequence is:

```text
Candidate Matches
        ↓
RANSAC + Initial Model
        ↓
Verified Inliers
        ↓
Local Sub-Pixel Refinement
        ↓
Refined Tie Points
        ↓
Final Transformation Refit
        ↓
Independent Check-Point Evaluation
```

The technical feedback explicitly recommends this ordering: after RANSAC identifies reliable inliers, locally refine the tie-point coordinates and then estimate the final transformation again.

> **Sub-pixel refinement is a precision-improvement experiment, not a replacement for robust correspondence estimation or correct geometric modeling.**

> **Refinement cannot recover information that is absent from the image or compensate for fundamentally incorrect correspondences.**

If residuals are dominated by:

- terrain relief
- projection error
- sensor/viewing geometry
- illumination-induced correspondence changes
- incorrect matches
- inappropriate geometric models

then sub-pixel refinement may provide little or no meaningful improvement.

---

## 4. Critical Scientific Principle

> **A numerical sub-pixel estimate does not automatically imply sub-pixel physical accuracy.**

Sub-pixel refinement can produce a coordinate such as `x = 125.37`, but that does not prove that the physical feature location is known to `0.37` pixel accuracy.

Meaningful fractional-pixel accuracy depends on factors including:

- image resolution
- local texture
- signal quality
- interpolation
- feature localization stability
- illumination
- sensor geometry
- projection
- ground-truth accuracy
- initial correspondence quality
- geometric-model validity

The project feedback specifically requires sub-pixel error to be reported in **source-image pixels first**, with conversion to metres only when GSD and projection make that conversion meaningful.

---

# 5. Relationship to V1 Experiments

EXP-006 is part of the progressive V1 experimental sequence.

```text
EXP-001
Correspondence Baseline
        ↓
EXP-002
Scale Handling
        ↓
EXP-003
Structural Representation
        ↓
EXP-004
Geometric Model
        ↓
EXP-005
Residual Diagnosis
        ↓
EXP-006
Sub-Pixel Refinement
```

## EXP-001 — SIFT Baseline

Establishes the classical correspondence and registration baseline.

Conceptually:

```text
Image Pair
→ SIFT
→ Descriptor Matching
→ Geometric Verification
→ Transformation
→ Registration
→ Evaluation
```

---

## EXP-002 — Scale Pyramid

Addresses differences in effective image scale and GSD through physically meaningful multi-scale comparison.

The project guidance emphasizes comparing information at compatible physical scales rather than attempting to recover missing detail through resizing.

---

## EXP-003 — Gradient Representation

Evaluates structural/gradient representations for correspondence under difficult radiometric or illumination conditions.

---

## EXP-004 — Affine vs Homography

Evaluates the geometric transformation model used for registration.

The project feedback notes that affine or homography can be reasonable first models for local, already map-projected imagery, but that lunar terrain is not inherently a flat surface and residual structure must be inspected.

---

## EXP-005 — Residual Analysis

Analyzes registration residuals spatially and statistically.

EXP-005 provides the diagnostic foundation for determining whether remaining error may plausibly be reduced through local correspondence refinement.

---

## EXP-006 — Sub-Pixel Refinement

Attempts to reduce residual registration error through fractional-pixel refinement **after geometric registration has already been established**.

---

# 6. What "Sub-Pixel" Means

A conventional pixel-level coordinate can be represented as:

$$
p = (x,y)
$$

A refined coordinate can be represented as:

$$
p' = (x+\delta_x,\ y+\delta_y)
$$

where:

$$
\delta_x,\delta_y \in \mathbb{R}
$$

and typically:

$$
|\delta_x| < 1
$$

$$
|\delta_y| < 1
$$

The refinement therefore estimates a coordinate between discrete pixel locations.

For example:

```text
Initial:
(x, y)

Refined:
(x + δx, y + δy)
```

The existence of a fractional coordinate is a **numerical localization result**. It is not, by itself, evidence that the physical registration is accurate to the same fractional-pixel level.

---

# 7. Experiment Scope

EXP-006 studies the effect of enabling or disabling local sub-pixel refinement while keeping the rest of the registration pipeline as constant as practical.

### Primary Independent Variable

**Sub-pixel refinement**

### Control

**Sub-pixel refinement disabled**

### Experimental Condition

**Sub-pixel refinement enabled**

### Controlled Variables

Where possible, keep constant:

- source image
- reference image
- image crop
- preprocessing
- image representation
- scale
- feature detector
- feature descriptor
- matcher
- matching thresholds
- RANSAC configuration
- transformation model
- check-point set
- evaluation metrics
- random seed
- interpolation settings outside the refinement stage

If any additional variable changes, it must be explicitly documented.

---

# 8. Refinement Placement

The intended V1 geometry flow is:

```text
LOCAL MATCHES
      ↓
RANSAC
      ↓
VERIFIED INLIERS
      ↓
SUB-PIXEL TIE POINTS
      ↓
FINAL MODEL
      ↓
REGISTERED IMAGE
```

The feedback explicitly recommends:

```text
Candidate Matches
→ RANSAC + Initial Model
→ Sub-Pixel Refine Inliers
→ Refit Final Transform
```

rather than refining arbitrary candidate matches before geometric verification.

### Actual Refinement Placement

`[TBD]`

### Placement Classification

| Possible Placement                           | Status  |
| -------------------------------------------- | ------- |
| Before transformation estimation             | `[TBD]` |
| After initial transformation estimation      | `[TBD]` |
| Iteratively during transformation estimation | `[TBD]` |
| After transformation estimation              | `[TBD]` |

The final documentation must reflect the actual implementation rather than assuming a particular optimization order.

---

# 9. Initial vs Refined Correspondences

This distinction is fundamental to EXP-006.

## Initial Correspondence

The coordinate obtained from the detector/matcher and retained after geometric verification.

## Refined Correspondence

The coordinate obtained after local sub-pixel optimization/refinement.

| Stage   | Source Coordinate | Reference Coordinate |
| ------- | ----------------- | -------------------- |
| Initial | `[TBD]`           | `[TBD]`              |
| Refined | `[TBD]`           | `[TBD]`              |

The experiment should preserve both versions so that the effect of refinement can be quantified directly.

---

# 10. Refinement Displacement

For a point \(p\), define the refinement displacement as:

$$
\Delta p_{\text{refine}} =
p_{\text{refined}} - p_{\text{initial}}
$$

For coordinates:

$$
\Delta x =
x_{\text{refined}} - x_{\text{initial}}
$$

$$
\Delta y =
y_{\text{refined}} - y_{\text{initial}}
$$

The displacement magnitude is:

$$
\|\Delta p_{\text{refine}}\|
=
\sqrt{
(x_{\text{refined}}-x_{\text{initial}})^2
+
(y_{\text{refined}}-y_{\text{initial}})^2
}
$$

Report:

- mean displacement
- median displacement
- standard deviation
- P90
- P95
- P99 where sufficient samples exist
- maximum displacement

| Statistic | Refinement Displacement |
| --------- | ----------------------: |
| Mean      |                 `[TBD]` |
| Median    |                 `[TBD]` |
| P90       |                 `[TBD]` |
| P95       |                 `[TBD]` |
| P99       |                 `[TBD]` |
| Maximum   |                 `[TBD]` |

A large refinement displacement is not automatically an improvement, and a small displacement is not automatically evidence that refinement was unnecessary. Both must be interpreted against independent registration accuracy.

---

# 11. Sub-Pixel Refinement Method

### Implementation Method

`[TBD]`

Possible approaches include:

- local intensity interpolation
- image-patch optimization
- gradient-based optimization
- correlation-based refinement
- Lucas-Kanade-style refinement
- quadratic/parabolic peak fitting
- centroid refinement
- local template matching
- phase-correlation refinement
- least-squares image alignment
- feature localization refinement
- `cornerSubPix` where applicable

Only the method actually implemented by the repository should be recorded as implemented.

### Method Classification

| Method                       | Status  |
| ---------------------------- | ------- |
| Intensity-based              | `[TBD]` |
| Gradient-based               | `[TBD]` |
| Correlation-based            | `[TBD]` |
| Feature localization         | `[TBD]` |
| Template matching            | `[TBD]` |
| Phase/correlation refinement | `[TBD]` |
| Other                        | `[TBD]` |

---

# 12. Refinement Objective

If the implementation uses an explicit optimization objective, record the actual objective here.

### Objective Function

`[TBD]`

### Optimization Method

`[TBD]`

### Initialization

`[TBD]`

### Search Window

`[TBD]`

### Stopping Criterion

`[TBD]`

### Maximum Iterations

`[TBD]`

### Regularization

`[TBD]`

Do not replace the actual objective with a generic formulation if the implementation uses a different objective.

For reference only, a possible image-alignment objective could take the form:

$$
E(\delta_x,\delta_y)
=
\sum_i
\left[
I_1(x_i,y_i)
-
I_2(x_i+\delta_x,y_i+\delta_y)
\right]^2
$$

This equation is **not an implementation claim** unless the repository uses this formulation.

---

# 13. Interpolation

Sub-pixel localization requires some mechanism for evaluating image information between discrete pixels.

### Interpolation Configuration

| Parameter             | Value   |
| --------------------- | ------- |
| Interpolation method  | `[TBD]` |
| Patch size            | `[TBD]` |
| Search range          | `[TBD]` |
| Maximum iterations    | `[TBD]` |
| Convergence threshold | `[TBD]` |

Possible interpolation methods include:

- bilinear
- bicubic
- Lanczos
- spline
- other documented method

The interpolation method must be recorded because it can affect the estimated fractional-pixel coordinate.

Interpolation should not be treated as creating new physical image information.

---

# 14. Convergence

If the refinement method is iterative, record:

- initialization
- optimization iterations
- objective value
- coordinate update
- convergence criterion
- termination conditions
- failed refinements

| Metric                 |   Value |
| ---------------------- | ------: |
| Maximum iterations     | `[TBD]` |
| Convergence tolerance  | `[TBD]` |
| Successful refinements | `[TBD]` |
| Failed refinements     | `[TBD]` |
| Failure rate           | `[TBD]` |

If the implemented method is not iterative, document:

`Iterative convergence: [Not applicable / TBD]`

---

# 15. Controlled Experiment Design

The central comparison is:

```text
                 Same Input Pair
                       │
              Same Preprocessing
                       │
              Same Correspondences
                       │
             Same RANSAC Verification
                       │
             Same Initial Model
                       │
              ┌────────┴────────┐
              │                 │
        No Refinement      Refinement
              │                 │
        Final Transform    Refined Points
              │                 │
              │           Final Transform
              │                 │
              └────────┬────────┘
                       ↓
             Independent Check Points
                       ↓
                 Residual Metrics
```

## Control

Pixel-level/baseline registration without sub-pixel refinement.

## Experimental Condition

The same registration pipeline with sub-pixel refinement enabled.

The control condition is the reference condition used to isolate the effect of refinement.

It must not be described as an "incorrect" condition.

---

# 16. Transformation Model Control

EXP-006 should follow the transformation model established by the preceding geometry experiments or the V1 continuation protocol.

### Primary Transformation Model

`[TBD]`

### Model Source

`EXP-004 — Affine vs Homography`

If both models are actually evaluated, use:

| Model      | Refinement | Purpose    |
| ---------- | ---------- | ---------- |
| Affine     | Disabled   | Control    |
| Affine     | Enabled    | Refinement |
| Homography | Disabled   | Control    |
| Homography | Enabled    | Refinement |

If V1 continues with only one model, document only that model in the actual experiment results.

Do not infer a preferred model from this experiment unless it is explicitly part of the measured experiment design.

---

# 17. Initial Transformation

### Transformation Model

`[TBD]`

### Initial Transformation Source

`Verified RANSAC inliers`

### Final Transformation Source

`Refined verified correspondences`

The intended sequence is:

```text
Verified Inliers
      ↓
Initial Transformation
      ↓
Local Sub-Pixel Refinement
      ↓
Refined Correspondences
      ↓
Final Transformation Refit
```

The final transform must not be described as being produced directly from unverified candidate matches.

---

# 18. Independent Check-Point Evaluation

Independent check points are a mandatory component of the scientific evaluation whenever available.

## Fitting / Control Points

Points used to:

- establish geometric consistency
- refine correspondences
- estimate the transformation

## Independent Check Points

Points reserved for evaluating the final registration and not used to fit the final transformation.

The feedback explicitly warns against fitting and evaluating on the same points because fitting residuals can make registration quality appear better than true independent accuracy.

> **A reduction in fitting residual does not establish improved registration accuracy unless the improvement is also observed on independent evaluation points.**

### Independent Check-Point Status

`[TBD]`

If unavailable:

`Independent check-point evaluation: [Not available]`

---

# 19. Evaluation Data

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
| Projection              | `[TBD]` |
| Geographic overlap      | `[TBD]` |
| Check-point source      | `[TBD]` |
| Check-point count       | `[TBD]` |
| Illumination conditions | `[TBD]` |

---

# 20. Error Units

Registration error must be reported in **image pixels first**.

This is especially important because the experiment evaluates source-image sub-pixel accuracy.

### Primary Unit

`Source-image pixels`

### Secondary Unit

`Metres`, only when physically meaningful.

Conversion to metres requires:

- known GSD
- appropriate projection
- understood coordinate systems
- meaningful local ground interpretation
- suitable reference/check-point truth

The project feedback explicitly states that a `0.2` pixel error does not represent the same ground distance for different sensors.

Do not claim sub-metre or other ground accuracy without measured evidence.

---

# 21. Residual Metrics

At minimum, record:

- RMSE
- median error
- P90
- P95
- maximum error
- mean Δx
- mean Δy
- spatial coverage
- outlier count
- runtime

| Metric            | No Refinement | Sub-Pixel Refinement |  Change |
| ----------------- | ------------: | -------------------: | ------: |
| Check-point count |       `[TBD]` |              `[TBD]` | `[TBD]` |
| RMSE              |       `[TBD]` |              `[TBD]` | `[TBD]` |
| Median            |       `[TBD]` |              `[TBD]` | `[TBD]` |
| P90               |       `[TBD]` |              `[TBD]` | `[TBD]` |
| P95               |       `[TBD]` |              `[TBD]` | `[TBD]` |
| Maximum           |       `[TBD]` |              `[TBD]` | `[TBD]` |
| Mean Δx           |       `[TBD]` |              `[TBD]` | `[TBD]` |
| Mean Δy           |       `[TBD]` |              `[TBD]` | `[TBD]` |
| Spatial coverage  |       `[TBD]` |              `[TBD]` | `[TBD]` |
| Outlier count     |       `[TBD]` |              `[TBD]` | `[TBD]` |
| Runtime           |       `[TBD]` |              `[TBD]` | `[TBD]` |

---

# 22. RMSE Definition

For \(N\) independent check points, if the registration error for point \(i\) is:

$$
e_i =
\sqrt{
\Delta x_i^2+\Delta y_i^2
}
$$

then image-space RMSE may be reported as:

$$
RMSE =
\sqrt{
\frac{1}{N}
\sum_{i=1}^{N}
e_i^2
}
$$

The exact implementation used by the repository must be documented if it differs.

### RMSE Unit

`Source-image pixels`

### Check-Point RMSE

`[TBD]`

---

# 23. Ground Error

Ground error should only be calculated where the data and geometry make such a conversion meaningful.

| Metric            | Value   |
| ----------------- | ------- |
| Source GSD        | `[TBD]` |
| Reference GSD     | `[TBD]` |
| Coordinate system | `[TBD]` |
| Projection        | `[TBD]` |
| Ground conversion | `[TBD]` |
| Ground RMSE       | `[TBD]` |

If unsupported:

`Ground error: [Not available]`

---

# 24. Refinement Improvement

If measurements support it, define absolute RMSE improvement as:

$$
\Delta RMSE =
RMSE_{\text{before}}
-
RMSE_{\text{after}}
$$

Percentage improvement can be calculated as:

$$
Improvement(\%) =
100
\times
\frac{
RMSE_{\text{before}}-RMSE_{\text{after}}
}{
RMSE_{\text{before}}
}
$$

Percentage improvement must only be reported when:

- the baseline RMSE is non-zero
- the same evaluation points are used
- the same metric definition is used
- the same evaluation protocol is used

Absolute pixel improvement must also be reported.

Percentage improvement must not be the sole measure of success.

---

# 25. Expected Results Table

| Metric           | Baseline | Refined | Absolute Change | Relative Change |
| ---------------- | -------: | ------: | --------------: | --------------: |
| RMSE             |  `[TBD]` | `[TBD]` |         `[TBD]` |         `[TBD]` |
| Median           |  `[TBD]` | `[TBD]` |         `[TBD]` |         `[TBD]` |
| P95              |  `[TBD]` | `[TBD]` |         `[TBD]` |         `[TBD]` |
| Maximum          |  `[TBD]` | `[TBD]` |         `[TBD]` |         `[TBD]` |
| Mean Δx          |  `[TBD]` | `[TBD]` |         `[TBD]` |         `[TBD]` |
| Mean Δy          |  `[TBD]` | `[TBD]` |         `[TBD]` |         `[TBD]` |
| Spatial coverage |  `[TBD]` | `[TBD]` |         `[TBD]` |         `[TBD]` |
| Runtime          |  `[TBD]` | `[TBD]` |         `[TBD]` |         `[TBD]` |

---

# 26. Statistical and Practical Significance

A small numerical reduction in RMSE does not automatically establish a meaningful scientific improvement.

Interpret observed changes relative to:

- check-point uncertainty
- image resolution
- source GSD
- residual variance
- interpolation precision
- measurement noise
- expected repeatability

### Statistical Testing

If the same independent check points are evaluated before and after refinement, paired analysis may be appropriate.

Possible methods include:

- paired residual differences
- median improvement
- mean improvement
- bootstrap confidence interval
- Wilcoxon signed-rank test
- paired t-test where assumptions are appropriate

### Actual Statistical Test

`[TBD]`

### Statistical Significance Testing

`[Not implemented]`

Do not fabricate:

- p-values
- confidence intervals
- effect sizes

---

# 27. Residual Field Comparison

EXP-005 establishes residual analysis as an important diagnostic.

EXP-006 should compare residuals before and after refinement using the same independent check points wherever possible.

## Before Refinement

```text
Independent Check Points
        ↓
Initial Transform
        ↓
Residual Vector Field
```

## After Refinement

```text
Independent Check Points
        ↓
Final Refined Transform
        ↓
Residual Vector Field
```

Analyze:

- residual magnitude
- residual direction
- spatial clustering
- systematic bias
- edge effects
- spatially varying residuals

A successful refinement must be demonstrated through measured changes rather than visual appearance alone.

---

# 28. Mean Residual Components

Record:

$$
\overline{\Delta x}
$$

and

$$
\overline{\Delta y}
$$

| Metric  | No Refinement | Refined |
| ------- | ------------: | ------: |
| Mean Δx |       `[TBD]` | `[TBD]` |
| Mean Δy |       `[TBD]` | `[TBD]` |
| Std Δx  |       `[TBD]` | `[TBD]` |
| Std Δy  |       `[TBD]` | `[TBD]` |

These values help distinguish a general reduction in error from persistent directional bias.

---

# 29. Spatial Coverage

Refinement must not be evaluated solely on a small cluster of correspondences.

Report spatial distribution using the project-defined coverage metric where available.

Potential measurements include:

- grid coverage
- convex-hull coverage
- bounding-box coverage
- point density
- spatial clustering

### Coverage Method

`[TBD]`

### Coverage Result

`[TBD]`

The project feedback specifically recommends reporting spatial coverage so that successful matches are not concentrated around only one local lunar feature.

---

# 30. Texture Dependence

Sub-pixel refinement may depend strongly on local image structure.

Potential regions for analysis include:

- crater rims
- crater interiors
- smooth plains
- ridges
- high-gradient regions
- low-gradient regions
- shadow boundaries

If implemented, compare refinement performance against:

- gradient magnitude
- local variance
- entropy
- corner strength
- texture score

### Texture Metric

`[TBD]`

### Texture-Stratified Analysis

`[TBD]`

No texture dependency should be claimed unless supported by measurements.

---

# 31. Illumination Effects

Lunar illumination can change local appearance and shadow geometry.

Potential issues include:

- shadow changes
- brightness changes
- contrast changes
- shadow-boundary displacement
- nonlinear radiometric differences

The project feedback emphasizes that brightness normalization cannot make terrain observed under different Sun geometry identical.

> **Sub-pixel refinement cannot correct a correspondence that is geometrically wrong because of illumination-induced appearance change.**

### Illumination Condition

`[TBD]`

### Illumination Stress Test

`[Not implemented / TBD]`

If illumination stress testing is performed, report performance differences rather than claiming illumination invariance.

---

# 32. Scale Effects

If outputs from EXP-002 are used, evaluate refinement at the relevant scale levels.

| Scale   | No Refinement RMSE | Refined RMSE | Improvement |
| ------- | -----------------: | -----------: | ----------: |
| `[TBD]` |            `[TBD]` |      `[TBD]` |     `[TBD]` |
| `[TBD]` |            `[TBD]` |      `[TBD]` |     `[TBD]` |
| `[TBD]` |            `[TBD]` |      `[TBD]` |     `[TBD]` |

The scale analysis must preserve the principle established by the project:

> Compare information at physically meaningful scales before asking the matcher to solve fine alignment.

Upsampling does not restore spatial detail that was not measured by the source sensor.

---

# 33. Affine / Homography Interaction

If both models are actually evaluated:

| Model      | Refinement |    RMSE |     P95 | Runtime |
| ---------- | ---------- | ------: | ------: | ------: |
| Affine     | Disabled   | `[TBD]` | `[TBD]` | `[TBD]` |
| Affine     | Enabled    | `[TBD]` | `[TBD]` | `[TBD]` |
| Homography | Disabled   | `[TBD]` | `[TBD]` | `[TBD]` |
| Homography | Enabled    | `[TBD]` | `[TBD]` | `[TBD]` |

The purpose is to determine whether refinement behaves differently across geometric models.

No overall ranking or winner should be assigned.

If only one model is used:

`Model comparison: [Not performed]`

---

# 34. Runtime and Computational Cost

Sub-pixel refinement introduces additional computation and therefore runtime must be measured rather than assumed.

Measure:

- refinement time per correspondence
- total refinement time
- total registration runtime
- number of optimization iterations
- failure/retry overhead

| Metric             | No Refinement | Sub-Pixel Refinement |  Change |
| ------------------ | ------------: | -------------------: | ------: |
| Total runtime      |       `[TBD]` |              `[TBD]` | `[TBD]` |
| Refinement runtime |           N/A |              `[TBD]` | `[TBD]` |
| Iterations         |           N/A |              `[TBD]` | `[TBD]` |
| Failed refinements |           N/A |              `[TBD]` | `[TBD]` |
| Retry overhead     |           N/A |              `[TBD]` | `[TBD]` |

Do not describe refinement as computationally cheap without measurement.

---

# 35. Refinement Failure Modes

Potential failure modes include:

- insufficient local texture
- flat/low-gradient patches
- repetitive lunar texture
- shadow boundaries
- illumination mismatch
- patch occlusion
- poor initialization
- large initial displacement
- local minima
- interpolation artifacts
- edge-of-image patches
- noisy image regions
- insufficient signal-to-noise ratio
- unstable optimization
- non-convergence

Only actual observed failures should be reported as experiment results.

For each observed failure:

### Condition

`[TBD]`

### Observation

`[TBD]`

### Evidence

`[TBD]`

### Possible Cause

`[TBD]`

### Impact

`[TBD]`

### Follow-Up

`[TBD]`

---

# 36. Failure Analysis

Failed refinements are first-class experimental results and must not be silently discarded.

Record:

- total refinement attempts
- successful refinements
- failed refinements
- convergence failures
- invalid patches
- low-texture failures
- boundary failures
- abnormal displacement cases

| Failure Type |   Count |    Rate | Evidence |
| ------------ | ------: | ------: | -------- |
| `[TBD]`      | `[TBD]` | `[TBD]` | `[TBD]`  |
| `[TBD]`      | `[TBD]` | `[TBD]` | `[TBD]`  |
| `[TBD]`      | `[TBD]` | `[TBD]` | `[TBD]`  |

---

# 37. Sensor-Specific Analysis

If multiple sensor pairs are tested, analyze each sensor pair separately.

Potential sensors include:

- OHRC
- TMC-2
- IIRS
- LRO NAC
- LRO WAC

Only tested sensors should appear in final measured results.

| Sensor Pair | Model   | Baseline RMSE | Refined RMSE | Improvement | Runtime |
| ----------- | ------- | ------------: | -----------: | ----------: | ------: |
| `[TBD]`     | `[TBD]` |       `[TBD]` |      `[TBD]` |     `[TBD]` | `[TBD]` |

Heterogeneous sensors must not be combined into one undifferentiated average without documenting the rationale.

For IIRS, the project guidance requires a sensible 2D representation before conventional image matching; it should not be treated as an ordinary single-band camera image without documenting the representation.

---

# 38. Candidate and Verified Correspondence Accounting

Candidate matches and verified inliers must remain separate quantities.

The project feedback explicitly distinguishes matcher-generated candidate matches from geometrically verified inliers.

| Quantity           | No Refinement | Refined |
| ------------------ | ------------: | ------: |
| Candidate matches  |       `[TBD]` | `[TBD]` |
| Verified inliers   |       `[TBD]` | `[TBD]` |
| Inlier ratio       |       `[TBD]` | `[TBD]` |
| Refined inliers    |           N/A | `[TBD]` |
| Failed refinements |           N/A | `[TBD]` |

Refinement must not be credited with improving registration merely because a larger number of candidate matches exists.

---

# 39. Results

## Quantitative Results

`[TBD]`

## Independent Check-Point Results

`[TBD]`

## Refinement Displacement

`[TBD]`

## Residual Comparison

`[TBD]`

## Spatial Analysis

`[TBD]`

## Runtime

`[TBD]`

## Failure Analysis

`[TBD]`

## Qualitative Visualization

`[TBD]`

## Interpretation

`[TBD]`

No numerical result should be entered until it has been measured from the experiment.

---

# 40. Qualitative Visualizations

Recommended or implemented outputs should be documented separately.

| Visualization                      | Status  | Path    |
| ---------------------------------- | ------- | ------- |
| Initial correspondences            | `[TBD]` | `[TBD]` |
| Refined correspondences            | `[TBD]` | `[TBD]` |
| Refinement displacement vectors    | `[TBD]` | `[TBD]` |
| Residual vectors before refinement | `[TBD]` | `[TBD]` |
| Residual vectors after refinement  | `[TBD]` | `[TBD]` |
| Residual magnitude distribution    | `[TBD]` | `[TBD]` |
| Check-point overlay                | `[TBD]` | `[TBD]` |
| Local image patches                | `[TBD]` | `[TBD]` |
| Convergence curves                 | `[TBD]` | `[TBD]` |
| Failure examples                   | `[TBD]` | `[TBD]` |

---

# 41. Refinement Displacement Visualization

The displacement visualization should communicate:

```text
Initial Point
      │
      │  Δp_refine
      ↓
Refined Point
```

If vectors are amplified for visibility, record the visualization scale factor.

### Vector Visualization Scale

`[TBD]`

> **Visualization scaling does not change the underlying measured displacement.**

The raw displacement must always remain available in source-image pixel units.

---

# 42. Convergence Visualization

If iterative optimization is implemented, document:

- iteration number
- objective value
- coordinate update
- convergence threshold

| Iteration | Objective | Δx Update | Δy Update |
| --------: | --------: | --------: | --------: |
|   `[TBD]` |   `[TBD]` |   `[TBD]` |   `[TBD]` |
|   `[TBD]` |   `[TBD]` |   `[TBD]` |   `[TBD]` |
|   `[TBD]` |   `[TBD]` |   `[TBD]` |   `[TBD]` |

No convergence data should be fabricated.

---

# 43. Experiment Matrix

| Condition | Refinement | Transformation | Purpose                          |
| --------- | ---------- | -------------- | -------------------------------- |
| C0        | Disabled   | `[TBD]`        | Baseline control                 |
| E0        | Enabled    | `[TBD]`        | Measure refinement effect        |
| C1        | Disabled   | `[TBD]`        | Optional second model control    |
| E1        | Enabled    | `[TBD]`        | Optional second model refinement |

The final matrix must be reduced to the conditions actually executed.

---

# 44. Ablation Logic

The primary ablation is:

```text
A0 = No sub-pixel refinement
A1 = Sub-pixel refinement enabled
```

The only intended change between A0 and A1 is the refinement stage.

If additional ablations are performed, document them explicitly.

| Ablation | Change              | Purpose            | Status  |
| -------- | ------------------- | ------------------ | ------- |
| A0       | Refinement disabled | Control            | `[TBD]` |
| A1       | Refinement enabled  | Primary experiment | `[TBD]` |
| A2       | `[TBD]`             | `[TBD]`            | `[TBD]` |

---

# 45. Stress Conditions

Where the benchmark contains appropriate data, EXP-006 may be evaluated under:

| Stress Case         | Purpose                                   | Status  |
| ------------------- | ----------------------------------------- | ------- |
| Easy pair           | Verify baseline refinement behavior       | `[TBD]` |
| Sun-angle stress    | Test sensitivity to illumination changes  | `[TBD]` |
| Scale stress        | Test refinement across scale differences  | `[TBD]` |
| Modality stress     | Test sensor-aware representation effects  | `[TBD]` |
| Geometry stress     | Test model/refinement interaction         | `[TBD]` |
| Low-feature terrain | Test refinement stability in weak texture | `[TBD]` |

The project feedback recommends these categories as a small stress-test matrix rather than relying on a single easy pair.

---

# 46. Interpretation Rules

Results must be interpreted using the following logic.

### Case 1 — Lower Independent RMSE

If independent check-point RMSE decreases while the evaluation protocol remains unchanged, report the measured reduction.

### Case 2 — Lower Fitting Residual but Same Check-Point Error

This indicates that refinement improved the fitting objective without demonstrating improved independent registration accuracy.

### Case 3 — Lower RMSE but Large Runtime Increase

Report both the accuracy change and computational cost.

### Case 4 — Small Numerical Change

Compare the magnitude against uncertainty, repeatability, interpolation precision, and expected measurement noise before describing it as practically meaningful.

### Case 5 — Higher Error

Report the degradation as an experimental result.

### Case 6 — No Meaningful Change

Report that the measured data did not demonstrate a meaningful improvement under the tested condition.

No result should be forced into a positive conclusion.

---

# 47. Results Interpretation Template

Use the following structure after measurements are available:

```text
Under the tested condition, sub-pixel refinement changed the independent
check-point RMSE from [baseline] px to [refined] px, corresponding to an
absolute change of [value] px.

The refinement displacement distribution had a median magnitude of
[value] px and a P95 magnitude of [value] px.

The observed residual field changed from [description] to [description].

Total runtime changed from [baseline] to [refined].

The observed result should be interpreted in the context of [texture /
illumination / scale / geometry / checkpoint uncertainty].
```

This template is intentionally descriptive and must be populated only with measured results.

---

# 48. Reproducibility

| Item                  | Value     |
| --------------------- | --------- |
| Experiment ID         | `EXP-006` |
| Git commit            | `[TBD]`   |
| Dataset version       | `[TBD]`   |
| Source image          | `[TBD]`   |
| Reference image       | `[TBD]`   |
| Sensor pair           | `[TBD]`   |
| Representation        | `[TBD]`   |
| Scale strategy        | `[TBD]`   |
| Transformation model  | `[TBD]`   |
| Refinement method     | `[TBD]`   |
| Patch size            | `[TBD]`   |
| Interpolation         | `[TBD]`   |
| Search range          | `[TBD]`   |
| Maximum iterations    | `[TBD]`   |
| Convergence threshold | `[TBD]`   |
| Check-point set       | `[TBD]`   |
| Random seed           | `[TBD]`   |
| Python version        | `[TBD]`   |
| OpenCV version        | `[TBD]`   |
| OS                    | `[TBD]`   |
| CPU                   | `[TBD]`   |
| GPU                   | `[TBD]`   |
| CUDA                  | `[TBD]`   |

---

# 49. Configuration

The exact repository configuration schema should be used when available.

If no experiment-specific schema is currently provided, the following is a **documentation template**, not a claim about the existing implementation:

```yaml
experiment:
  id: EXP-006
  name: subpixel-refinement
  version: V1

input:
  source_image: TBD
  reference_image: TBD

geometry:
  model: TBD

refinement:
  enabled: true
  method: TBD
  patch_size: TBD
  interpolation: TBD
  search_range: TBD
  max_iterations: TBD
  convergence_threshold: TBD

evaluation:
  check_points: TBD
  metrics:
    - rmse_px
    - median_error_px
    - p95_error_px
    - max_error_px
    - runtime
```

The actual repository configuration must take precedence over this documentation template.

---

# 50. Output Artifacts

| Artifact                | Path    | Required    | Status  | Description                           |
| ----------------------- | ------- | ----------- | ------- | ------------------------------------- |
| Source image            | `[TBD]` | Yes         | `[TBD]` | Experiment input                      |
| Reference image         | `[TBD]` | Yes         | `[TBD]` | Registration reference                |
| Initial correspondences | `[TBD]` | Yes         | `[TBD]` | Pre-refinement coordinates            |
| Verified inliers        | `[TBD]` | Yes         | `[TBD]` | RANSAC-verified points                |
| Refined correspondences | `[TBD]` | Yes         | `[TBD]` | Fractional-pixel coordinates          |
| Initial transform       | `[TBD]` | Yes         | `[TBD]` | Transformation before refinement      |
| Final transform         | `[TBD]` | Yes         | `[TBD]` | Transformation refit after refinement |
| Registered image        | `[TBD]` | Yes         | `[TBD]` | Final registration                    |
| Check-point metrics     | `[TBD]` | Yes         | `[TBD]` | Independent evaluation                |
| Residual vectors        | `[TBD]` | Yes         | `[TBD]` | Before/after residuals                |
| Refinement displacement | `[TBD]` | Yes         | `[TBD]` | Point displacement statistics         |
| Runtime logs            | `[TBD]` | Recommended | `[TBD]` | Computational measurements            |
| Configuration           | `[TBD]` | Yes         | `[TBD]` | Reproducibility configuration         |

---

# 51. Required Evidence

A complete EXP-006 result should provide evidence for:

- initial verified correspondences
- initial transformation
- refined correspondence coordinates
- refinement displacement
- final transformation
- independent check-point evaluation
- residual comparison
- spatial coverage
- runtime
- refinement failures
- configuration/reproducibility metadata

A visual overlay alone is not sufficient.

The project feedback explicitly identifies numerical error, inlier statistics, coverage and runtime as important evidence for evaluating the registration pipeline.

---

# 52. Quality-Control Checklist

## Data

- [ ] Source image is identified.
- [ ] Reference image is identified.
- [ ] Sensor/product metadata is recorded.
- [ ] Source GSD is recorded where available.
- [ ] Reference GSD is recorded where available.
- [ ] Projection information is recorded where available.

## Correspondence

- [ ] Candidate matches are recorded.
- [ ] RANSAC verification is performed.
- [ ] Verified inliers are recorded.
- [ ] Candidate matches are not confused with verified inliers.

## Refinement

- [ ] Refinement method is explicitly identified.
- [ ] Refinement is applied at the documented pipeline stage.
- [ ] Initial coordinates are preserved.
- [ ] Refined coordinates are preserved.
- [ ] Refinement displacement is measured.
- [ ] Interpolation method is recorded.
- [ ] Convergence behavior is recorded where applicable.
- [ ] Failed refinements are not silently discarded.

## Geometry

- [ ] Initial transformation is recorded.
- [ ] Final transformation is recorded.
- [ ] Transformation model is documented.
- [ ] Refined points are used according to the documented refit procedure.

## Evaluation

- [ ] Independent check points are used where available.
- [ ] Check points are not used to fit the final transformation.
- [ ] RMSE is reported in source-image pixels.
- [ ] Median error is reported.
- [ ] P95 error is reported where sufficient samples exist.
- [ ] Maximum error is reported.
- [ ] Mean Δx is reported.
- [ ] Mean Δy is reported.
- [ ] Spatial coverage is reported.
- [ ] Runtime is reported.

## Scientific Integrity

- [ ] No fabricated results are present.
- [ ] No unsupported accuracy claim is present.
- [ ] No physical sub-pixel accuracy is inferred solely from numerical coordinates.
- [ ] Ground error is only reported when physically meaningful.
- [ ] Limitations are documented.
- [ ] Failures are retained.
- [ ] Control and experimental conditions are comparable.

---

# 53. Acceptance Criteria

EXP-006 should not be considered scientifically complete merely because the refinement algorithm executes.

The experiment must demonstrate that:

1. Reliable verified correspondences exist before refinement.
2. The refinement stage operates on the intended verified points.
3. Initial and refined coordinates can be compared.
4. The final transformation is estimated according to the documented procedure.
5. Independent check-point evaluation is performed where available.
6. Registration error is reported in source-image pixels.
7. Refinement displacement is measured.
8. Runtime and refinement failures are recorded.
9. Residual behavior is compared before and after refinement.
10. All reported numerical results are reproducible from documented inputs and configuration.

### Acceptance Status

| Requirement              | Status  | Evidence |
| ------------------------ | ------- | -------- |
| Verified correspondences | `[TBD]` | `[TBD]`  |
| Initial transformation   | `[TBD]` | `[TBD]`  |
| Sub-pixel refinement     | `[TBD]` | `[TBD]`  |
| Refined coordinates      | `[TBD]` | `[TBD]`  |
| Final transformation     | `[TBD]` | `[TBD]`  |
| Independent check points | `[TBD]` | `[TBD]`  |
| RMSE measurement         | `[TBD]` | `[TBD]`  |
| Residual comparison      | `[TBD]` | `[TBD]`  |
| Spatial coverage         | `[TBD]` | `[TBD]`  |
| Runtime                  | `[TBD]` | `[TBD]`  |
| Failure accounting       | `[TBD]` | `[TBD]`  |
| Reproducibility          | `[TBD]` | `[TBD]`  |

---

# 54. Limitations

The following limitations must be considered when interpreting EXP-006:

### Image Information

Sub-pixel interpolation does not recover spatial detail absent from the original sensor measurement.

### Correspondence Quality

Incorrect correspondences cannot generally be corrected simply by optimizing a local patch around the wrong location.

### Geometric Model

Sub-pixel refinement does not automatically compensate for an inappropriate global transformation model.

### Lunar Relief

The Moon is not a perfectly planar surface. Terrain relief and sensor/view geometry can introduce residual structure that local point refinement cannot necessarily remove.

### Illumination

Different Sun angles can alter local appearance and shadow geometry.

### Interpolation

The estimated fractional coordinate can depend on the selected interpolation method.

### Ground Truth

Independent evaluation is limited by the accuracy and quality of the available check points.

### Sensor Differences

Sub-pixel image-space accuracy does not represent the same physical ground accuracy across sensors with different GSDs.

---

# 55. Expected Scientific Outcomes

EXP-006 is designed to distinguish among several possible outcomes.

### Outcome A — Independent Accuracy Improves

Sub-pixel refinement reduces independent check-point error while maintaining acceptable runtime and failure behavior.

### Outcome B — Fitting Improves but Independent Accuracy Does Not

The refinement changes the local fitting objective but does not demonstrate improved generalization to independent check points.

### Outcome C — Refinement Has Negligible Effect

The initial correspondence localization may already be sufficiently accurate for the tested imagery.

### Outcome D — Refinement Degrades Accuracy

The optimization may be affected by:

- weak texture
- interpolation
- illumination
- local minima
- incorrect initialization
- geometric-model limitations

### Outcome E — Benefit Is Condition-Dependent

Refinement may help some scale, texture, illumination, or sensor conditions but not others.

All of these are valid experimental outcomes. The experiment should report whichever is supported by measurements.

---

# 56. Final Comparison Framework

The final V1 comparison should preserve the experimental progression:

| Experiment | Primary Question                                                                      |
| ---------- | ------------------------------------------------------------------------------------- |
| EXP-001    | Can classical feature matching establish a measurable baseline?                       |
| EXP-002    | Does physical scale handling improve correspondence across GSD differences?           |
| EXP-003    | Does structural representation improve difficult image correspondence?                |
| EXP-004    | Which tested geometric model describes the observed registration behavior?            |
| EXP-005    | What residual structures and failure modes remain?                                    |
| EXP-006    | Can verified correspondences be refined to improve independent registration accuracy? |

The project feedback recommends keeping the experiments measurable and comparing methods on the same test pairs rather than relying on qualitative claims.

---

# 57. Final Results Summary

| Metric                         | Result     |
| ------------------------------ | ---------- |
| Source image                   | `[TBD]`    |
| Reference image                | `[TBD]`    |
| Transformation model           | `[TBD]`    |
| Refinement method              | `[TBD]`    |
| Initial verified inliers       | `[TBD]`    |
| Refined correspondences        | `[TBD]`    |
| Median refinement displacement | `[TBD]` px |
| P95 refinement displacement    | `[TBD]` px |
| Baseline check-point RMSE      | `[TBD]` px |
| Refined check-point RMSE       | `[TBD]` px |
| Absolute RMSE change           | `[TBD]` px |
| Relative RMSE change           | `[TBD]`    |
| Spatial coverage               | `[TBD]`    |
| Refinement failure rate        | `[TBD]`    |
| Baseline runtime               | `[TBD]`    |
| Refined runtime                | `[TBD]`    |
| Statistical test               | `[TBD]`    |
| Statistical result             | `[TBD]`    |
| Overall experimental finding   | `[TBD]`    |

---

# 58. Conclusion

EXP-006 evaluates sub-pixel refinement as a **final precision stage** in the V1 lunar image registration pipeline.

The experiment must preserve the distinction between:

```text
Candidate Matches
        ↓
Verified Inliers
        ↓
Initial Transformation
        ↓
Sub-Pixel Refinement
        ↓
Final Transformation
        ↓
Independent Evaluation
```

The central measurement is not whether the algorithm produces fractional coordinates. The central measurement is whether those refined coordinates lead to a **measurable improvement in independent registration accuracy**.

The primary evidence should therefore come from:

- independent check-point RMSE
- median error
- high-percentile error
- residual-field behavior
- spatial coverage
- refinement displacement
- failure rate
- runtime
- reproducibility

No improvement should be claimed until it is demonstrated by the measured experiment.

---

# 59. Reproducible Execution

### Execution Command

```bash
# EXP-006 execution command
[TBD]
```

### Expected Execution Record

```text
Experiment ID: EXP-006
Input pair: [TBD]
Transformation model: [TBD]
Refinement method: [TBD]
Refinement enabled: true
Check-point set: [TBD]
Random seed: [TBD]
Git commit: [TBD]
```

---

# 60. Related Project Documentation

The experiment should remain consistent with the following confirmed project documentation:

- `README.md`
- `data/README.md`
- `data/raw/README.md`
- `data/external/README.md`
- `data/interim/README.md`
- `data/processed/README.md`
- `data/ground_truth/README.md`
- `data/ground_truth/CONTROL_POINTS.md`
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
- `experiments/templates/EXPERIMENT_TEMPLATE.md`
- `experiments/v1/README.md`
- `experiments/v1/baseline/EXP-001-sift-baseline/README.md`
- `experiments/v1/preprocessing/EXP-002-scale-pyramid/README.md`
- `experiments/v1/preprocessing/EXP-003-gradient-representation/README.md`
- `experiments/v1/geometry/EXP-004-affine-vs-homography/README.md`
- `experiments/v1/geometry/EXP-005-residual-analysis/README.md`

---

# 61. Source Material Notes

This experiment documentation follows the project-specific technical guidance that:

- RANSAC should establish reliable inliers before sub-pixel refinement.
- Refined tie points should be used to refit the final transformation.
- Independent check points should be kept separate from transformation-fitting points.
- Source-image pixels should be the primary registration-error unit.
- Ground error should only be reported when GSD and projection make the conversion meaningful.
- Spatial coverage, inlier statistics, RMSE and runtime are important measurable outputs.
- Sub-pixel refinement should not be presented as a substitute for correct correspondence or geometric modeling.
- Upsampling should not be presented as recovery of missing spatial information.
- Sensor-specific behavior should be preserved rather than hidden inside a single mixed result.

These principles are consistent with the provided lunar registration feedback and the V1 experimental direction.

---

# 62. Change Log

| Version | Date    | Change                        | Author  |
| ------- | ------- | ----------------------------- | ------- |
| V1      | `[TBD]` | Initial EXP-006 documentation | `[TBD]` |
| V1      | `[TBD]` | `[TBD]`                       | `[TBD]` |

---

## Experiment Status

**EXP-006 — Sub-Pixel Refinement**

**Current Status:** `[TBD]`

**Primary Evidence Status:** `[TBD]`

**Independent Check-Point Evaluation:** `[TBD]`

**Measured Improvement:** `[TBD]`

**Reproducibility Status:** `[TBD]`
