# DEM-Aware Registration

> **Status:** Future research specification
> **Implementation status:** Not implemented
> **Validation status:** Not experimentally validated in ChandraMap
> **Research role:** Investigate whether explicit terrain/elevation information can improve lunar image registration when a purely 2D geometric model is insufficient.

---

## 1. Purpose

This document defines a future research direction for **terrain-aware lunar image registration** using Digital Elevation Models (DEMs) and related terrain information.

The central motivation is that a 2D image transformation may be insufficient when the observed displacement between two lunar images varies spatially because of:

- terrain relief
- sensor/viewing geometry
- elevation differences
- parallax
- perspective effects
- projection differences

The research objective is not to assume that DEM information will improve every registration problem.

Instead, the objective is to determine:

> **When, why, and by how much can explicit lunar terrain information improve registration beyond purely image-based 2D transformation models?**

This document describes a research hypothesis, conceptual architectures, experimental methodology, evaluation requirements, failure modes, and possible future integration paths.

No DEM source, coordinate system, sensor model, numerical result, or implementation is assumed unless explicitly established elsewhere in the ChandraMap project.

---

# 2. Research Status

DEM-aware registration is a **future research direction**.

The current ChandraMap V1 research foundation focuses on measurable 2D image correspondence and registration, including:

- known source/reference image pairs
- SIFT-based baseline matching
- scale-pyramid research
- gradient/structure representations
- affine versus homography comparison
- residual analysis
- sub-pixel refinement
- independent registration evaluation

DEM-aware registration extends this research toward explicit terrain geometry.

| Capability                              | Status                 |
| --------------------------------------- | ---------------------- |
| 2D feature correspondence               | V1 research foundation |
| 2D geometric verification               | V1 research foundation |
| Affine/homography comparison            | V1 research            |
| Residual analysis                       | V1 research            |
| Sub-pixel refinement                    | V1 research            |
| DEM integration                         | Not implemented        |
| Terrain-aware projection                | Not implemented        |
| DEM-assisted registration               | Not implemented        |
| Sensor-model-based terrain registration | Not implemented        |
| 3D-to-2D registration                   | Not implemented        |
| DEM-aware bundle adjustment             | Not implemented        |
| DEM sensitivity benchmark               | Future research        |

---

# 3. Central Research Question

The central research question is:

> **Can explicit lunar terrain/elevation information improve geometric registration accuracy and robustness when 2D transformation models are insufficient?**

This should be decomposed into measurable questions:

- When does a planar 2D model become insufficient?
- How much registration error can be attributed to terrain relief?
- Can a DEM explain spatially varying registration residuals?
- Can DEM-derived geometry improve image alignment?
- Does terrain-aware registration improve independent checkpoint accuracy?
- Does it reduce systematic residual patterns?
- Does it improve registration under different viewing geometries?
- Can it reduce errors caused by terrain-induced parallax?
- What DEM resolution is required?
- How sensitive is the method to DEM error?
- How sensitive is it to camera/sensor geometry?
- How should DEM information interact with image correspondences?
- What is the computational cost compared with 2D registration?
- Under what conditions does DEM-aware registration provide measurable benefit?

The research must remain empirical.

DEM-aware registration should not be considered inherently superior to a 2D model.

---

# 4. Why DEM-Aware Registration Matters

A conventional 2D registration model represents the relationship between image coordinates using transformations such as:

- translation
- similarity
- affine transformation
- homography

These models can be effective when the scene and imaging geometry satisfy their assumptions sufficiently well.

However, lunar terrain is not generally planar.

Elevation differences can cause corresponding terrain points to appear at different image locations when viewed from different geometries.

This can produce:

- parallax
- spatially varying displacement
- viewpoint-dependent apparent position
- local scale changes
- geometric distortion
- occlusion differences

A single global transformation may therefore leave structured residuals.

Conceptually:

```text
Planar assumption

Terrain:
────────────────────────────

Image relationship:
One global 2D transformation
```

versus:

```text
Relief terrain

          /\        /\
_________/  \______/  \________

Image relationship:
Spatially varying geometric displacement
```

The importance of these effects depends on the actual observation conditions.

Relevant factors include:

- terrain relief
- image resolution
- sensor altitude
- incidence angle
- emission angle
- observation geometry
- viewing direction
- map projection
- DEM accuracy
- sensor model accuracy

These factors should be measured or documented where available.

---

# 5. Illumination and Terrain Geometry Are Different Problems

DEM-aware registration must not be confused with illumination normalization.

## 5.1 Illumination Change

Illumination changes image appearance through:

- Sun angle
- shadows
- local brightness
- contrast
- terrain shading

## 5.2 Terrain-Induced Geometric Difference

Terrain geometry can change apparent image position through:

- elevation
- viewing angle
- sensor geometry
- relief
- parallax

These effects interact but are not identical.

A DEM can provide geometric information about terrain, but it does not automatically solve photometric differences.

The same principle applies in the opposite direction:

> **Contrast normalization cannot move a shadow back to where it appeared under another illumination geometry.**

Likewise:

> **DEM-aware geometry does not automatically solve photometric differences.**

This distinction is important when interpreting future experiments.

---

# 6. DEM Basics

A Digital Elevation Model provides elevation values over a defined spatial domain.

Conceptually:

```text
(x, y) → z
```

where:

- `x` = horizontal spatial coordinate
- `y` = horizontal spatial coordinate
- `z` = terrain elevation

A DEM can therefore represent a surface such as:

```text
                  z
                  ↑
                  │        /\
                  │       /  \       /\
                  │  ____/    \_____/  \____
                  │
                  └──────────────────────────→ x,y
```

For image registration, the important question is not merely whether a DEM exists.

The important question is:

> **Can DEM-derived terrain geometry explain or predict the relationship between image observations?**

---

# 7. DEM Representation

A future DEM-aware pipeline may conceptually contain:

```text
Lunar DEM
   ↓
Coordinate / Projection Definition
   ↓
Terrain Surface
   ↓
Sensor / Camera Geometry
   ↓
3D Terrain Points
   ↓
Projection into Image Coordinates
```

The exact coordinate reference system, vertical datum, DEM source, resolution, and sensor geometry are:

- `[Not provided]`
- `[TBD]`

These must be documented before an actual experiment.

---

# 8. Ordinary 2D Registration

The existing ChandraMap V1 research can represent registration as a transformation:

$$
T : p^s \rightarrow p^r
$$

where:

- \(p^s\) is a source-image point
- \(p^r\) is a reference-image point
- \(T\) is the estimated geometric transformation

A homography can be written conceptually as:

$$
\mathbf{p}^r \sim H\mathbf{p}^s
$$

where \(H\) is a \(3\times3\) projective transformation.

This provides a useful baseline.

However, a single \(H\) assumes that one projective relationship adequately explains the image pair.

That assumption may break down when terrain relief creates spatially varying displacement.

---

# 9. Map-Projected Registration

Map-projected imagery is already expressed in a geographic or projected spatial representation.

Conceptually:

```text
Raw / Sensor Image
        ↓
Projection
        ↓
Map-Projected Image
        ↓
2D Registration
```

Map projection can reduce some geometric complexity.

However:

> Map projection does not automatically guarantee that two images of the same terrain will have identical 2D geometry.

Residual differences can remain because of:

- different observation geometries
- terrain relief
- sensor-model differences
- projection assumptions
- orthorectification quality
- DEM accuracy

Therefore, map projection and DEM-aware registration should not be treated as interchangeable concepts.

---

# 10. Orthorectification

Orthorectification attempts to correct image geometry using terrain information and imaging geometry so that terrain features are represented more consistently in a map coordinate system.

Conceptually:

```text
Sensor Image
     +
Sensor Geometry
     +
Terrain / DEM
     ↓
Orthorectification
     ↓
Map-Referenced Image
```

Orthorectification is different from simply estimating a 2D transformation between two already available images.

A future ChandraMap study must document whether input imagery is:

- raw
- geometrically corrected
- map projected
- orthorectified
- otherwise processed

The actual status for each dataset should come from authoritative project metadata.

---

# 11. DEM-Assisted Registration

DEM-assisted registration can refer to using terrain information to improve the estimated relationship between images.

A conceptual approach is:

```text
Image Correspondences
        +
DEM
        +
Imaging Geometry
        ↓
Terrain-Constrained Registration
```

The DEM can provide additional geometric information that is not present in the image coordinates alone.

The exact mathematical formulation remains a research choice.

---

# 12. Terrain-Aware Geometric Registration

Terrain-aware registration goes beyond simply adding a DEM as an input file.

The terrain model should participate in the geometric relationship between observations.

A conceptual model is:

```text
Terrain point
    ↓
3D coordinate
    ↓
Sensor model
    ↓
Predicted image coordinate
```

For two images:

```text
3D terrain point
      ├────────→ Source image
      │
      └────────→ Reference image
```

The registration problem can then be expressed as consistency between observed and predicted image coordinates.

---

# 13. 3D-to-2D Projection

A terrain-aware model can conceptually use:

$$
P = (X,Y,Z)
$$

as a 3D terrain point and project it into an image:

$$
p = \Pi(P;\theta)
$$

where:

- \(P\) is a 3D terrain point
- \(p\) is an image coordinate
- \(\Pi\) is a projection model
- \(\theta\) represents the relevant sensor/camera parameters

For two images:

$$
p_s = \Pi_s(P;\theta_s)
$$

$$
p_r = \Pi_r(P;\theta_r)
$$

The correspondence then becomes constrained by the same underlying 3D terrain point.

This is fundamentally different from fitting only:

$$
p_r = T(p_s)
$$

with a purely 2D transformation.

---

# 14. Terrain-Aware Registration Concept

A conceptual future pipeline is:

```text
Source Image
      ↓
Feature Detection / Representation
      ↓
Feature Correspondences
      ↓
Geometric Verification
      ↓
Terrain / DEM Association
      ↓
3D Terrain Constraints
      ↓
Sensor / Projection Model
      ↓
Terrain-Aware Optimization
      ↓
Independent Check Points
      ↓
Registration Evaluation
```

This should initially be treated as a research architecture rather than a production design.

---

# 15. Relationship to the Existing V1 Pipeline

The current V1 logic can be represented as:

```text
Candidate Matches
       ↓
RANSAC + Initial Model
       ↓
Verified Inliers
       ↓
Sub-Pixel Refinement
       ↓
Refit Final Transform
       ↓
Independent Evaluation
```

A future DEM-aware extension could become:

```text
Candidate Matches
       ↓
RANSAC + Initial 2D Model
       ↓
Verified Inliers
       ↓
DEM / Terrain Association
       ↓
Terrain-Aware Geometric Refinement
       ↓
Refit / Optimize
       ↓
Independent Evaluation
```

This preserves the existing principle that correspondences must first be evaluated geometrically.

---

# 16. DEM Should Not Replace Correspondence Validation

A DEM must not be used to make weak image correspondences appear valid.

The research should retain the distinction:

```text
Candidate Matches
       ↓
Geometric Verification
       ↓
Verified Inliers
```

before terrain-aware refinement.

A flexible terrain model can otherwise absorb incorrect correspondences.

This would produce an apparently successful optimization while hiding correspondence errors.

---

# 17. Residual Analysis as the Entry Point

The existing residual-analysis research provides a natural motivation for DEM-aware registration.

For correspondence \(i\):

$$
r_i = p_i^r - T(p_i^s)
$$

and residual magnitude:

$$
e_i = \|r_i\|
$$

A standard RMSE is:

$$
RMSE =
\sqrt{
\frac{1}{N}
\sum_{i=1}^{N}
e_i^2
}
$$

The key question is whether residuals show a systematic spatial pattern.

For example:

```text
Residual field

↑
│       → → →
│    → → → → →
│  → → → → → →
│ → → → → → →
└────────────────→
```

A structured residual field may indicate that a single global 2D model is not adequately explaining the image relationship.

This does not prove that terrain is the cause.

It provides a hypothesis to investigate.

---

# 18. Terrain-Explained Residuals

A future experiment can investigate whether residuals correlate with terrain variables such as:

- elevation
- local slope
- aspect
- terrain curvature
- relief magnitude
- viewing direction relative to terrain

The goal is to determine whether residual structure is consistent with terrain-induced geometry.

A possible research sequence is:

```text
2D Registration
      ↓
Residual Field
      ↓
Terrain Association
      ↓
Residual / Terrain Analysis
      ↓
Hypothesis
      ↓
Terrain-Aware Model
```

This is preferable to introducing a DEM without first establishing why it is needed.

---

# 19. Terrain Relief

Terrain relief represents elevation variation across the observed region.

A region with very small relief may be adequately approximated by a 2D model.

A region with substantial relief may exhibit more spatially varying displacement.

Therefore, future experiments should compare terrain conditions such as:

- low relief
- moderate relief
- high relief

The exact classification thresholds are `[TBD]`.

They should be based on measurable terrain properties rather than subjective visual categories.

---

# 20. Parallax

Parallax is a key motivation for terrain-aware registration.

When two observations have different viewing geometry, the image displacement of a terrain point can depend on its elevation and viewing direction.

Conceptually:

```text
                 Sensor A
                    \
                     \
                      ● Terrain point
                     /
                    /
                 Sensor B
```

Two sensors observing the same terrain surface can therefore project the same 3D point to different image coordinates.

The magnitude of this effect depends on the actual geometry.

DEM-aware registration is intended to model this relationship more explicitly.

---

# 21. Spatially Varying Transformation

A global homography assumes one transformation applies across the image.

Terrain-induced parallax may instead produce:

```text
Image
+-----------------------+
| →  →  →              |
|  →  →  →             |
|    →  →  →           |
|      →  →  →         |
+-----------------------+
```

where displacement changes with position.

A terrain-aware model attempts to explain this spatial variation through physical geometry rather than simply introducing an arbitrary flexible warp.

This distinction is important.

---

# 22. Flexible Warps vs Physical Geometry

A flexible 2D warp may reduce image residuals.

However:

> A lower residual does not automatically mean a more physically correct registration.

A flexible transformation can overfit correspondence noise.

A DEM-aware model provides an opportunity to constrain the solution using terrain geometry.

The comparison should therefore include:

```text
2D rigid/similarity model
2D affine model
2D homography
Flexible nonrigid model, if justified
DEM-aware physical model
```

Only models relevant to the actual experiment should be implemented.

---

# 23. Sensor Geometry

Terrain-aware registration requires appropriate knowledge of imaging geometry.

Potential information may include:

- sensor position
- viewing direction
- camera model
- optical geometry
- acquisition geometry
- image-to-ground relationship

The actual availability of these quantities in the ChandraMap data is:

`[Not provided]`

They must not be invented.

If the necessary sensor geometry is unavailable, a full physically based terrain-aware model may not be feasible.

---

# 24. Sensor-Specific Considerations

The relevant sensors have different characteristics.

### OHRC

OHRC provides high-detail visible imagery and can expose fine terrain structures.

The authoritative project metadata should determine the actual pixel scale and imaging geometry for each image.

### TMC-2

TMC-2 imagery differs in spatial and sensor characteristics from OHRC.

DEM-aware registration should therefore avoid assuming that the same geometric approximation applies identically to both.

### IIRS

IIRS is a hyperspectral/infrared instrument.

Before conventional image registration, an appropriate 2D representation must be established.

The DEM provides geometric terrain information but does not solve the spectral/modality differences between IIRS and other sensors.

---

# 25. DEM Resolution

DEM resolution is a major research variable.

A DEM that is too coarse may fail to represent terrain features relevant to image registration.

A higher-resolution DEM may better represent terrain relief but can introduce:

- greater storage
- greater computational cost
- sensitivity to DEM noise
- increased processing complexity

The research should therefore ask:

> **What DEM resolution is sufficient for the registration accuracy and image scales relevant to ChandraMap?**

No resolution should be declared appropriate without measurement.

---

# 26. DEM Error

A DEM is not perfect ground truth.

Potential DEM errors include:

- elevation uncertainty
- missing terrain detail
- interpolation artifacts
- spatial resolution limitations
- local reconstruction errors

A terrain-aware registration system may therefore inherit DEM errors.

Future experiments should investigate sensitivity to DEM perturbation.

Conceptually:

```text
True terrain
    ↓
DEM approximation
    ↓
Terrain-aware registration
    ↓
Registration error
```

The DEM should therefore be treated as an uncertainty-bearing input rather than an infallible geometric reference.

---

# 27. DEM Sensitivity Experiment

A controlled experiment could vary DEM quality:

```text
High-quality DEM representation
        ↓
Reference result

Downsampled DEM
        ↓
Registration result

Perturbed DEM
        ↓
Registration result

Coarse DEM
        ↓
Registration result
```

Compare:

- independent checkpoint RMSE
- residual structure
- inlier consistency
- runtime
- failure rate

The purpose is to determine how strongly the method depends on terrain accuracy.

---

# 28. Terrain Derivatives

A DEM can provide derived terrain variables.

Potential variables include:

### Elevation

$$
z(x,y)
$$

### Slope

Magnitude of terrain gradient.

### Aspect

Orientation of the terrain gradient.

### Curvature

A measure of how terrain shape changes spatially.

These variables may help analyze why some regions are more difficult to register.

They should not automatically be inserted into the registration model.

First establish whether they explain observed residual behavior.

---

# 29. Terrain and Illumination

Terrain affects both:

1. geometric projection
2. illumination appearance

These effects must remain separate.

For example:

```text
DEM
 ├── Terrain geometry
 │      ↓
 │   Projection / parallax
 │
 └── Terrain shape
        ↓
     Shading / shadows
```

The first branch is directly relevant to geometric registration.

The second branch affects photometric appearance.

A DEM-aware geometric model cannot by itself guarantee illumination invariance.

---

# 30. DEM-Derived Illumination

A future research direction could potentially use terrain and Sun geometry to model expected illumination.

However, this is a separate research problem.

It should not be conflated with the first DEM-aware registration experiment.

The initial experiment should focus on:

> **Does terrain geometry improve geometric registration?**

Photometric terrain modeling can remain a separate future extension.

---

# 31. Initial Research Hypothesis

A suitable initial hypothesis is:

> **When observed registration residuals exhibit spatial structure consistent with terrain relief and differing observation geometry, incorporating DEM-derived terrain geometry may reduce independent registration error relative to an appropriate 2D baseline.**

This hypothesis is deliberately conditional.

It does not claim:

- DEMs always improve registration
- homography is always insufficient
- terrain explains all residuals
- every lunar image pair requires 3D geometry

---

# 32. Null Hypothesis

A useful null hypothesis is:

> **For the evaluated ChandraMap image pairs, a suitably selected 2D registration model provides accuracy comparable to a DEM-aware model, such that explicit terrain information does not produce a meaningful improvement under the benchmark conditions.**

A negative result is scientifically useful.

It may show that DEM-aware registration is unnecessary for a particular class of image pairs.

---

# 33. Experimental Comparison

The central experiment should compare:

```text
Image Pair
   │
   ├── 2D Baseline
   │      ↓
   │   Registration
   │
   └── DEM-Aware Model
          ↓
       Registration
```

Both should use:

- the same image pair
- the same correspondence data where appropriate
- the same independent check points
- the same evaluation protocol
- comparable runtime measurements

This isolates the contribution of terrain information.

---

# 34. Baseline Hierarchy

A useful progression is:

### Baseline A

Translation or other simplest appropriate model.

### Baseline B

Affine transformation.

### Baseline C

Homography.

### Candidate

DEM-aware geometric model.

The exact baseline selection should follow the existing V1 experiments and the geometry of the image pair.

---

# 35. Correspondence Isolation

To determine whether DEM information improves the geometry rather than feature matching, the first experiment should ideally hold the correspondences constant.

Conceptually:

```text
Same Candidate Correspondences
          ↓
     ┌────┴────┐
     ↓         ↓
2D Model    DEM-Aware Model
     ↓         ↓
Evaluation   Evaluation
```

This prevents simultaneous changes in:

- feature extraction
- feature matching
- geometric model

from obscuring the source of improvement.

---

# 36. Geometric Verification

The existing distinction remains critical:

```text
Candidate Matches
       ↓
RANSAC + Initial Model
       ↓
Verified Inliers
```

DEM-aware refinement should operate on correspondences that have already passed appropriate geometric checks.

A DEM must not become a mechanism for accepting arbitrary candidate matches.

---

# 37. Independent Check Points

Independent evaluation remains mandatory.

Points used to estimate the transformation should not be reused as the only points used to judge accuracy.

Conceptually:

```text
Control / Fit Points
        ↓
Estimate Model
        ↓
Final Registration

Independent Check Points
        ↓
Evaluate Registration
```

This is particularly important for flexible or high-dimensional terrain-aware models.

---

# 38. Registration Error

For an independent check point \(i\):

$$
r_i =
p_i^{reference}
-
\hat{p}_i^{reference}
$$

where:

- \(p_i^{reference}\) is the observed/reference coordinate
- \(\hat{p}\_i^{reference}\) is the predicted coordinate

The residual magnitude is:

$$
e_i = \|r_i\|
$$

and RMSE:

$$
RMSE =
\sqrt{
\frac{1}{N}
\sum_{i=1}^{N}
e_i^2
}
$$

The primary registration accuracy unit should remain **source/reference image pixels**, consistent with the established ChandraMap evaluation principles.

Ground-distance error should only be reported when the required:

- GSD
- projection
- geometry
- reference information

make that conversion scientifically meaningful.

---

# 39. Residual Vector Analysis

A single RMSE value may hide important structure.

For each check point, retain:

$$
r_i=(r_{x,i},r_{y,i})
$$

Then analyze:

- direction
- magnitude
- spatial distribution
- correlation with terrain
- correlation with viewing geometry

A future terrain-aware study should compare residual fields before and after DEM integration.

---

# 40. Residual Field Hypothesis

A useful diagnostic pattern is:

```text
2D Model Residuals

→ → →
 → → → →
  → → → →
   → → →
```

followed by:

```text
DEM-Aware Residuals

→   →
   →
 →    →
```

This would be evidence of reduced systematic structure only if supported by quantitative evaluation.

Visual residual plots should therefore supplement, not replace:

- RMSE
- independent checkpoint measurements
- coverage
- failure rate

---

# 41. Spatial Coverage

A registration with low error on a small cluster of points may not represent reliable image-wide alignment.

Therefore, evaluation should continue to include spatial coverage.

Potential coverage measures should follow the established benchmark definitions.

A future DEM-aware result should report:

- number of verified inliers
- inlier ratio
- spatial coverage
- independent checkpoint count
- checkpoint RMSE

More correspondences are not automatically better if they are incorrect or spatially clustered.

---

# 42. Terrain Stratification

Future evaluation can divide check points by terrain characteristics.

For example:

```text
All check points
      ├── Low relief
      ├── Moderate relief
      └── High relief
```

Then compare registration error across groups.

This can help answer:

> Does DEM-aware registration provide value specifically where terrain relief is significant?

The exact relief thresholds remain `[TBD]`.

---

# 43. Viewing Geometry Stratification

A similar analysis can group image pairs by observation geometry.

Potential factors include:

- viewing-angle difference
- incidence-angle difference
- emission-angle difference
- sensor position differences

Actual metadata availability is `[Not provided]`.

If available, these variables should be used to investigate when terrain-aware geometry becomes useful.

---

# 44. Terrain-Aware Optimization

A possible future optimization objective could combine image correspondence error with terrain-projection consistency.

Conceptually:

$$
\min_{\theta}
\sum_i
\rho
\left(
\left\|
p_i -
\Pi(P_i;\theta)
\right\|^2
\right)
$$

where:

- \(P_i\) is a DEM-associated 3D point
- \(p_i\) is an observed image point
- \(\Pi\) is a projection model
- \(\theta\) contains the estimated geometric parameters
- \(\rho\) is an optional robust loss

The exact model is not selected by this document.

It is a research formulation requiring implementation and validation.

---

# 45. Robust Estimation

Outlier-resistant estimation remains important.

Image correspondences may contain:

- incorrect matches
- localization noise
- terrain-association errors
- occlusion differences
- image interpretation differences

A terrain-aware optimization should therefore not assume that every correspondence is correct.

The existing use of geometric verification provides a foundation for robust estimation.

---

# 46. Sensor Model Requirements

A physically meaningful DEM-aware model may require a sensor model capable of mapping terrain coordinates to image coordinates.

A conceptual function is:

$$
p = \Pi(X,Y,Z;\theta)
$$

where \(\theta\) contains sensor-specific parameters.

The exact sensor models available for:

- OHRC
- TMC-2
- IIRS

are `[Not provided]`.

Therefore, this document does not assume a particular sensor model implementation.

---

# 47. Sensor Model Uncertainty

Even with a DEM, registration can remain inaccurate if the sensor geometry is inaccurate.

Potential uncertainty sources include:

- position uncertainty
- pointing uncertainty
- camera calibration
- timing
- geometric preprocessing
- DEM uncertainty

Therefore:

```text
DEM
  +
Sensor Model
  +
Correspondences
```

should be treated as a coupled geometric system.

Improving only the DEM may not solve errors originating in sensor geometry.

---

# 48. DEM and Orthorectification Relationship

A future research architecture may compare:

```text
Raw / Existing Image
       ↓
DEM-based Orthorectification
       ↓
2D Registration
```

against:

```text
Raw / Existing Image
       ↓
Terrain-Aware Registration
```

These are different strategies.

The comparison should determine whether explicit terrain-aware registration provides value beyond whatever geometric correction is already present in the input products.

---

# 49. Possible Research Architectures

Several architectures can be investigated.

## Architecture A — DEM Diagnostics

```text
2D Registration
      ↓
Residual Analysis
      ↓
DEM Correlation
```

Purpose:

Determine whether terrain explains residual structure.

---

## Architecture B — DEM-Assisted Initialization

```text
DEM + Sensor Geometry
        ↓
Initial Registration
        ↓
2D Refinement
```

Purpose:

Use terrain geometry to produce a better initial alignment.

---

## Architecture C — Terrain-Constrained Optimization

```text
Image Correspondences
        +
DEM
        +
Sensor Geometry
        ↓
Joint Optimization
```

Purpose:

Estimate registration using explicit terrain constraints.

---

## Architecture D — 3D-to-2D Registration

```text
DEM
 ↓
3D Terrain Points
 ↓
Source Projection
 ↓
Reference Projection
 ↓
Geometric Alignment
```

Purpose:

Model the image relationship through a common 3D terrain surface.

These architectures should be treated as separate research hypotheses.

---

# 50. Recommended Experimental Progression

A conservative progression is:

```text
Stage 1
Existing 2D registration
        ↓
Residual analysis

Stage 2
Residual + terrain correlation
        ↓
Determine whether terrain explains error

Stage 3
DEM-assisted geometric initialization
        ↓
Measure improvement

Stage 4
Terrain-aware refinement
        ↓
Independent evaluation

Stage 5
3D-to-2D sensor-model registration
        ↓
Advanced validation

Stage 6
Multi-image / bundle-style optimization
        ↓
Future system research
```

This avoids introducing a complex physical model before demonstrating that terrain is actually relevant.

---

# 51. Experiment 1: Terrain-Relevance Study

### Objective

Determine whether terrain characteristics explain residual patterns from existing 2D registration.

### Inputs

- known source/reference image pair
- existing correspondence pipeline
- existing geometric model
- independent check points
- DEM, if available
- relevant metadata

### Procedure

```text
2D Registration
      ↓
Residuals
      ↓
Associate residuals with terrain
      ↓
Analyze spatial patterns
```

### Measurements

- RMSE
- residual magnitude
- residual direction
- spatial distribution
- elevation
- terrain relief
- other available terrain derivatives

### Expected outcome

One of:

- terrain appears relevant
- terrain does not explain the observed residuals
- evidence is inconclusive

---

# 52. Experiment 2: DEM-Assisted Registration

### Objective

Determine whether adding DEM-derived geometry improves an existing registration.

### Controlled variables

Keep constant where possible:

- image pair
- image representation
- feature extraction
- feature matching
- correspondence set
- independent check points

### Variable

Registration model:

```text
2D model
vs
DEM-aware model
```

### Measurements

- verified inliers
- inlier ratio
- spatial coverage
- independent checkpoint RMSE
- residual distribution
- runtime
- failure rate

---

# 53. Experiment 3: DEM Resolution

### Objective

Determine the required DEM resolution.

Conceptually:

```text
DEM A — fine
DEM B — medium
DEM C — coarse
```

Evaluate each under identical registration conditions.

The result should identify whether registration accuracy changes materially with terrain resolution.

No preferred resolution should be assumed beforehand.

---

# 54. Experiment 4: DEM Perturbation

### Objective

Measure sensitivity to terrain uncertainty.

Possible controlled perturbations include:

- elevation noise
- reduced resolution
- interpolation changes
- local terrain errors

The exact perturbation model is `[TBD]`.

Evaluate:

- checkpoint RMSE
- residual structure
- transformation stability
- failure rate

---

# 55. Experiment 5: Terrain Relief Stress Test

Evaluate pairs with different levels of terrain relief.

```text
Low relief
   ↓
Moderate relief
   ↓
High relief
```

Compare:

```text
2D registration
vs
DEM-aware registration
```

This experiment directly addresses whether DEM-aware registration becomes useful as terrain complexity increases.

---

# 56. Experiment 6: Viewing Geometry Stress Test

Where appropriate metadata exists, evaluate different observation geometries.

Potential factors include:

- sensor separation
- viewing-angle difference
- incidence-angle difference
- emission-angle difference

The purpose is to determine whether terrain-induced effects become more important as viewing geometry changes.

---

# 57. Experiment 7: Cross-Sensor Terrain-Aware Registration

Potential research combinations include:

```text
OHRC ↔ OHRC
OHRC ↔ TMC-2
TMC-2 ↔ OHRC
OHRC ↔ IIRS
TMC-2 ↔ IIRS
```

The exact benchmark combinations depend on available data.

Cross-sensor registration should be analyzed separately because differences in:

- spatial resolution
- modality
- radiometry
- spectral response
- image representation

can introduce errors that a DEM cannot resolve.

---

# 58. IIRS Considerations

IIRS requires an appropriate 2D representation before image correspondences can be established using conventional image-registration methods.

Possible representations may include:

- selected spectral band
- PCA-based representation
- composite representation
- structural representation

The selected representation must be documented and experimentally justified.

The DEM addresses terrain geometry, not the underlying modality mismatch.

Therefore, an IIRS DEM-aware experiment should explicitly separate:

```text
Representation problem
+
Correspondence problem
+
Terrain geometry problem
```

---

# 59. Terrain and Multi-Scale Registration

Terrain-aware registration should also consider the spatial scale of the imagery.

A DEM feature that is meaningful at one image scale may be irrelevant at another.

For example:

```text
Fine-resolution imagery
        ↓
Fine terrain structure may matter

Coarse-resolution imagery
        ↓
Fine terrain structure may not be observable
```

This reinforces the existing ChandraMap principle that physical resolution and image resizing are not equivalent.

Upsampling an image does not recover missing terrain detail.

---

# 60. DEM and Sub-Pixel Refinement

Sub-pixel refinement should remain a separate stage unless the research demonstrates that it should be integrated into the terrain model.

A possible future sequence is:

```text
Terrain-Aware Registration
        ↓
Initial Final Geometry
        ↓
Sub-Pixel Refinement
        ↓
Refit / Re-evaluate
```

The same caution applies:

> Numerical sub-pixel precision is not automatically evidence of physically accurate sub-pixel registration.

Independent check points remain necessary.

---

# 61. Ground Error vs Pixel Error

DEM-aware research may make ground-distance errors tempting to report.

However, ground-distance accuracy requires appropriate knowledge of:

- image scale
- projection
- sensor geometry
- terrain reference
- coordinate system

Therefore:

> **Source/reference pixel error remains the primary registration accuracy unit unless the required geospatial information supports a meaningful ground-distance conversion.**

If ground error is reported, the conversion method must be documented.

---

# 62. Bundle-Adjustment-Like Research

A more advanced future direction is joint optimization across:

- multiple images
- sensor parameters
- camera poses
- terrain points
- correspondences

Conceptually:

```text
Image 1 ─┐
Image 2 ─┼──→ Joint Geometric Optimization
Image 3 ─┘
             +
            DEM
```

This resembles bundle-adjustment-style reasoning.

It is substantially more complex than pairwise registration and should not be part of the initial DEM experiment.

---

# 63. Multi-Image Terrain Registration

If pairwise DEM-aware registration is validated, future research may investigate:

```text
Image A
   ↕
Image B
   ↕
Image C
   ↕
Image D
```

with a shared terrain/geometric constraint.

Potential benefits include:

- global consistency
- reduced drift
- shared geometric parameters
- improved mosaic alignment

This should remain a later research stage.

---

# 64. Lunar Mosaic Relationship

DEM-aware registration can eventually support lunar mosaicking.

A possible future architecture is:

```text
Multiple Images
      ↓
DEM-Aware Registration
      ↓
Globally Consistent Alignment
      ↓
Mosaic
```

However, mosaic generation is a downstream product.

The primary research question in this document remains registration accuracy and robustness.

A visually attractive mosaic is not sufficient evidence of geometric correctness.

---

# 65. Failure Modes

Future experiments should explicitly classify failure modes.

### 65.1 DEM too coarse

Terrain geometry is insufficiently represented.

### 65.2 DEM error

Incorrect elevation introduces geometric error.

### 65.3 Sensor geometry error

The projection model is inaccurate.

### 65.4 Incorrect terrain association

Image points are associated with incorrect terrain locations.

### 65.5 Occlusion

A terrain point visible in one image is not visible in another.

### 65.6 Illumination mismatch

Terrain-aware geometry does not resolve appearance differences.

### 65.7 Sparse correspondences

Insufficient correspondences prevent reliable estimation.

### 65.8 Low-feature terrain

The imagery does not provide enough stable correspondence evidence.

### 65.9 Overfitting

A flexible terrain-aware model fits correspondence noise.

### 65.10 Model mismatch

The selected physical model does not represent the actual imaging geometry.

---

# 66. Occlusion

Terrain-aware models must explicitly consider visibility.

A 3D terrain point may be:

```text
Visible in Image A
        ↓
Occluded in Image B
```

Such a point cannot necessarily provide a valid two-image correspondence.

A DEM alone may not solve visibility without an appropriate sensor geometry and line-of-sight model.

Therefore, occlusion handling should remain an explicit research consideration.

---

# 67. Terrain Model Overfitting

A high-dimensional terrain-aware model may produce lower training/fitting residuals without improving independent accuracy.

This is particularly dangerous if:

- many parameters are optimized
- correspondences are reused as evaluation points
- DEM geometry is flexible
- model complexity is not controlled

The benchmark must therefore prioritize independent check points.

---

# 68. Evaluation Metrics

A future DEM-aware benchmark should retain the established multi-dimensional evaluation philosophy.

## Correspondence metrics

- candidate match count
- verified inlier count
- inlier ratio

## Spatial metrics

- spatial coverage
- distribution of correspondences

## Registration metrics

- independent checkpoint RMSE
- residual distribution
- residual vectors

## Geospatial metrics

- ground error where scientifically meaningful
- geographic consistency where available

## System metrics

- runtime
- failure rate
- memory/resource requirements where relevant

No single metric should determine registration quality.

---

# 69. Primary Metric

The primary accuracy metric should be:

> **Independent check-point RMSE in image pixels**

subject to the project's established ground-truth protocol.

The DEM-aware model should be judged by whether it improves independent accuracy rather than merely reducing the fitting residual.

---

# 70. Secondary Metrics

Secondary metrics should include:

- inlier count
- inlier ratio
- spatial coverage
- residual structure
- runtime
- failure rate

A successful DEM-aware method should ideally demonstrate improvement without unacceptable degradation in these other dimensions.

---

# 71. Statistical Comparison

Future comparisons should report results across multiple image pairs rather than relying on one example.

Possible reporting includes:

- mean
- median
- spread
- distribution
- per-condition results

The exact statistical analysis is `[TBD]`.

The benchmark should preserve per-pair results so aggregate statistics do not hide failures.

---

# 72. Before-and-After Residual Analysis

For each suitable pair:

```text
2D Model
   ↓
Residual Field A

DEM-Aware Model
   ↓
Residual Field B
```

Compare:

- total RMSE
- residual magnitude
- spatial bias
- directional bias
- terrain correlation

This provides evidence for whether the DEM is solving the hypothesized geometric problem.

---

# 73. Runtime Considerations

DEM-aware registration can introduce additional computational operations:

- DEM lookup
- coordinate transformations
- 3D point construction
- projection
- visibility calculations
- terrain-aware optimization

Therefore, runtime must be measured.

The comparison should include:

```text
2D registration runtime
vs
DEM-aware registration runtime
```

and, where relevant:

```text
total pipeline runtime
```

A more accurate model may still be useful even if it is slower, but that trade-off must be explicitly documented.

---

# 74. Reproducibility Requirements

Every DEM-aware experiment should document:

### Image data

- source image identifier
- reference image identifier
- sensor
- image representation
- preprocessing

### DEM

- DEM identifier/source
- version
- resolution
- coordinate system
- vertical reference
- preprocessing

If any of these are unavailable:

`[Not provided]`

### Geometry

- sensor geometry
- projection model
- coordinate transformations
- parameter initialization

### Correspondence

- feature extractor
- matcher
- candidate correspondence count
- verification method

### Optimization

- model
- objective
- initialization
- stopping criteria
- robustness method

### Evaluation

- independent check points
- RMSE
- coverage
- runtime
- failure rate

---

# 75. Data Provenance

DEM data must have clear provenance.

At minimum, future experiments should record:

```text
DEM:
Version:
Source:
Acquisition / Product Information:
Resolution:
Coordinate Reference:
Vertical Reference:
Processing:
Resampling:
Interpolation:
Availability:
License / Usage Constraints:
```

No specific DEM source is established by this document.

---

# 76. Coordinate-System Integrity

Terrain-aware registration is highly sensitive to coordinate-system consistency.

A future implementation must explicitly document:

```text
Image Coordinates
       ↕
Projection Coordinates
       ↕
Geographic Coordinates
       ↕
Terrain Coordinates
```

Any transformation between these spaces must be defined.

Coordinate-system assumptions must never be left implicit in a reproducible experiment.

---

# 77. Interpolation

A DEM is usually sampled on a grid.

Terrain elevation at an arbitrary coordinate may therefore require interpolation.

Possible interpolation strategies are `[TBD]`.

The chosen interpolation method can affect projected terrain coordinates and therefore registration.

It should be treated as an explicit configuration parameter.

---

# 78. Resampling

DEM resampling can change the terrain surface.

Possible operations include:

- downsampling
- upsampling
- reprojection
- interpolation

Each operation should be documented.

As with image resizing:

> Changing sampling density does not create missing physical terrain information.

---

# 79. DEM Alignment

Before using a DEM for image registration, the DEM itself must be spatially consistent with the image reference frame.

Potential problems include:

- incorrect georeferencing
- projection mismatch
- offset
- rotation
- scale mismatch
- vertical-reference mismatch

A DEM that is geometrically misaligned can degrade registration rather than improve it.

---

# 80. Quality-Control Checks

Before DEM-aware registration, future experiments should verify:

- DEM coverage includes the relevant region
- coordinate systems are compatible
- terrain values are valid
- image and DEM extents overlap
- resolution is documented
- sensor geometry is available where required
- no unintended resampling has occurred
- no evaluation information has leaked into fitting

---

# 81. Research Experiment Matrix

A future benchmark can use a matrix such as:

| Experiment        | 2D Baseline | DEM-Aware  | Relief     | Viewing Geometry | Sensor     | Primary Measurement  |
| ----------------- | ----------- | ---------- | ---------- | ---------------- | ---------- | -------------------- |
| Baseline          | Yes         | No         | Mixed      | Mixed            | Controlled | RMSE                 |
| Terrain relevance | Yes         | Diagnostic | Stratified | Controlled       | Controlled | Residual correlation |
| DEM resolution    | Yes         | Yes        | Controlled | Controlled       | Controlled | RMSE                 |
| DEM sensitivity   | Yes         | Yes        | Controlled | Controlled       | Controlled | RMSE degradation     |
| Relief stress     | Yes         | Yes        | Stratified | Controlled       | Controlled | RMSE                 |
| Geometry stress   | Yes         | Yes        | Controlled | Stratified       | Controlled | RMSE                 |
| Cross-sensor      | Yes         | Yes        | Mixed      | Mixed            | Stratified | RMSE / failure       |
| End-to-end        | Yes         | Yes        | Mixed      | Mixed            | Mixed      | Registration success |

All numeric results remain `[TBD]` until measured.

---

# 82. Acceptance Evidence

A DEM-aware method should not be considered validated until it has evidence for:

### Geometric validity

- correct terrain/image association
- documented projection model
- appropriate geometric verification

### Accuracy

- independent checkpoint evaluation
- reproducible RMSE
- comparison against appropriate 2D baselines

### Robustness

- multiple image pairs
- representative terrain conditions
- relevant geometry conditions

### Reproducibility

- documented DEM provenance
- documented parameters
- repeatable experiments

### System behavior

- runtime
- failure modes
- resource requirements where relevant

---

# 83. Evidence That Would Justify Further Development

Further development would be justified if experiments demonstrate that:

- terrain explains a meaningful portion of residual error
- DEM-aware geometry reduces independent registration error
- improvements persist across multiple image pairs
- improvements are strongest under physically relevant terrain/viewing conditions
- the method does not merely overfit training correspondences
- DEM uncertainty is manageable
- computational cost is acceptable for the intended use case
- results are reproducible

The exact quantitative thresholds should come from the project's benchmark and acceptance criteria rather than being invented in this research note.

---

# 84. Evidence That Would Argue Against Immediate Integration

The research may indicate that DEM-aware registration should remain future work if:

- 2D models already provide sufficient independent accuracy
- terrain does not explain observed residuals
- DEM errors dominate the result
- sensor geometry is unavailable
- computational cost is disproportionate to the benefit
- improvements occur only on fitting points
- cross-sensor differences dominate the error
- results are inconsistent across representative pairs

A negative or inconclusive result should be recorded as a valid research outcome.

---

# 85. Relationship to Existing Research Notes

DEM-aware registration connects directly with:

- `research/notes/lunar-registration.md`
- `research/notes/illumination-invariance.md`
- `research/notes/scale-invariance.md`
- `research/notes/ground-truth-design.md`

The strongest connection is with residual analysis and geometric-model selection.

The research should use existing definitions and evaluation principles rather than introducing conflicting terminology.

---

# 86. Relationship to Future Research

DEM-aware registration is complementary to other future research directions.

Potential relationships include:

```text
Global Retrieval
       ↓
Candidate Region
       ↓
Local Matching
       ↓
DEM-Aware Registration
```

and:

```text
ALIKED
  +
LightGlue
  ↓
Correspondences
  ↓
DEM-Aware Geometry
```

Other future directions such as:

- IIRS-specific representation
- LOFTR
- RIFT/CFOG
- lunar mosaicking

can eventually interact with terrain-aware registration, but they should remain independently evaluated.

---

# 87. DEM-Aware Registration and Global Retrieval

A future large-scale system could combine retrieval and terrain-aware registration:

```text
Large Lunar Archive
        ↓
Global Retrieval
        ↓
Candidate Regions
        ↓
Local Correspondence
        ↓
Terrain-Aware Registration
        ↓
Independent Evaluation
```

This should only be attempted after each stage has independently demonstrated useful behavior.

---

# 88. DEM-Aware Registration and Lunar Mosaic

A future mosaic pipeline could use terrain-aware registration to improve consistency:

```text
Multiple Lunar Images
        ↓
Candidate Correspondence
        ↓
Terrain-Aware Pairwise Registration
        ↓
Global Geometric Optimization
        ↓
Lunar Mosaic
```

This remains future research.

A mosaic should not be used as the sole validation of terrain-aware registration.

---

# 89. Potential Future Architecture

A mature future architecture could conceptually be:

```text
                    Lunar Image Archive
                            │
                            ▼
                    Global Candidate Search
                            │
                            ▼
                  Sensor-Aware Representation
                            │
                            ▼
                    Local Correspondences
                            │
                            ▼
                  Geometric Verification
                            │
                 ┌──────────┴──────────┐
                 │                     │
                 ▼                     ▼
             2D Model              DEM / Terrain
                 │                     │
                 └──────────┬──────────┘
                            ▼
                  Terrain-Aware Geometry
                            │
                            ▼
                   Residual Analysis
                            │
                            ▼
                  Sub-Pixel Refinement
                            │
                            ▼
                Independent Check Points
                            │
                            ▼
                  Registration Evaluation
```

This is a possible future architecture, not a current implementation.

---

# 90. Research Maturity Levels

DEM-aware registration should progress through explicit maturity levels.

### Level 1 — Idea

Hypothesis that terrain may explain registration error.

### Level 2 — Diagnostic

Residuals are compared with terrain information.

### Level 3 — Prototype

A preliminary DEM-aware model is implemented.

### Level 4 — Controlled Experiment

The model is compared against a fixed 2D baseline.

### Level 5 — Benchmark Evidence

Multiple image pairs and stress conditions are evaluated.

### Level 6 — Reproducible Finding

The result can be reproduced using documented data and configuration.

### Level 7 — Candidate Capability

Evidence supports integration into a future ChandraMap version.

### Level 8 — Integrated Capability

Only after implementation, testing, benchmarking, and documentation.

Current status:

> **Level 1 — Future research specification**

---

# 91. Research Lifecycle

The intended lifecycle is:

```text
Current 2D Research
        ↓
Residual Analysis
        ↓
Terrain-Relevance Hypothesis
        ↓
Controlled DEM Experiment
        ↓
Benchmark Evaluation
        ↓
Independent Validation
        ↓
Failure Analysis
        ↓
Reproducibility
        ↓
Research Finding
        ↓
Future ChandraMap Version Candidate
```

This preserves the project's separation between research and production.

---

# 92. Open Research Questions

The following questions remain open:

1. Which ChandraMap image pairs exhibit measurable terrain-induced residuals?
2. What level of terrain relief makes a 2D model inadequate?
3. Which DEM resolution is sufficient?
4. How sensitive is registration to DEM error?
5. Which sensor geometry information is available?
6. Which terrain projection model is appropriate for each sensor?
7. How should occlusions be handled?
8. Can terrain explain systematic residual direction?
9. Can DEM-aware registration improve independent checkpoint RMSE?
10. Does the improvement persist across sensors?
11. How much additional runtime does terrain modeling require?
12. Does terrain-aware registration remain useful after orthorectification?
13. Can terrain geometry reduce the need for flexible 2D warps?
14. Can the approach support multi-image registration?
15. Can the same framework support future lunar mosaicking?

---

# 93. Known Limitations

This document does not establish:

- a specific DEM dataset
- a specific lunar coordinate system
- a specific vertical datum
- a specific sensor model
- a specific terrain-aware optimization
- a specific DEM resolution
- numerical registration improvements
- computational benchmarks
- validated cross-sensor terrain registration

These remain:

- `[TBD]`
- `[Not provided]`
- `[Planned]`
- `[Not implemented]`

as appropriate.

---

# 94. Scientific Guardrails

The following principles should remain mandatory.

### 1. DEM is not automatically ground truth

DEM uncertainty must be acknowledged.

### 2. Lower fitting error is not sufficient

Independent check points are required.

### 3. DEM does not solve illumination

Photometric differences require separate treatment.

### 4. More model flexibility is not automatically better

Complex terrain models can overfit.

### 5. A physically motivated model still requires validation

Physical plausibility does not replace experimental evidence.

### 6. Sensor differences must remain explicit

A DEM cannot compensate automatically for spectral or radiometric mismatch.

### 7. Pixel accuracy remains primary

Ground-distance accuracy requires appropriate geospatial information.

### 8. Negative results are valuable

If a 2D model is sufficient, that finding should be documented.

---

# 95. Recommended First Experiment

The first practical DEM experiment should be deliberately narrow.

### Question

> **Do the residuals of the existing 2D registration show systematic spatial behavior that is plausibly related to terrain relief?**

### Pipeline

```text
Known Source / Reference Pair
            ↓
Existing V1 Correspondence Pipeline
            ↓
2D Geometric Model
            ↓
Independent Check Points
            ↓
Residual Vectors
            ↓
Associate Residuals With DEM
            ↓
Terrain / Residual Analysis
```

### Why start here?

Because it determines whether the complexity of DEM-aware registration is justified.

There is little scientific value in implementing a complex 3D registration system before establishing that terrain geometry is a meaningful source of error for the target image pairs.

---

# 96. Recommended Second Experiment

If Experiment 1 provides evidence that terrain is relevant:

```text
Same Correspondences
        ├───────────────┐
        ↓               ↓
2D Model          DEM-Aware Model
        ↓               ↓
Independent       Independent
Check Points      Check Points
        ↓               ↓
      Compare Accuracy
```

Primary comparison:

- independent checkpoint RMSE

Secondary comparisons:

- residual structure
- spatial coverage
- inlier ratio
- runtime
- failure rate

---

# 97. Integration Gate

DEM-aware registration should become a candidate for future ChandraMap integration only after:

```text
✓ Terrain relevance demonstrated
✓ Controlled comparison completed
✓ Independent evaluation completed
✓ Multiple pairs evaluated
✓ Failure modes documented
✓ DEM provenance documented
✓ Sensor geometry documented
✓ Runtime measured
✓ Reproducibility demonstrated
```

Until then:

> **DEM-aware registration remains research, not production functionality.**

---

# 98. Final Research Position

DEM-aware registration represents a possible extension of ChandraMap from purely image-based 2D registration toward physically informed terrain geometry.

Its central purpose is not to make the registration model more complicated.

Its purpose is to test a specific hypothesis:

> **Some lunar image-registration errors may be caused by terrain-dependent geometric effects that cannot be adequately represented by a single 2D transformation.**

The appropriate research path is therefore:

```text
2D Registration
      ↓
Residual Analysis
      ↓
Terrain Correlation
      ↓
DEM Relevance
      ↓
Controlled DEM Experiment
      ↓
Independent Evaluation
      ↓
Stress Testing
      ↓
Reproducibility
      ↓
Future Integration Decision
```

A DEM-aware model should only be promoted when measured evidence demonstrates that explicit terrain information provides a meaningful and reproducible benefit under relevant ChandraMap conditions.

Until such evidence exists, this capability remains a **future research direction** and is **not implemented or validated in the current ChandraMap system**.
