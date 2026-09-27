# Geometric Transformation Models for Lunar Image Registration

> **Research note:** ChandraMap
> **Path:** `research/notes/geometric-models.md`
> **Scope:** Geometric transformation modeling for lunar image correspondence and registration
> **Primary models:** Affine transformation and homography/projective transformation
> **Status:** Foundational research note
> **Evidence status:** Conceptual and methodological; model-selection results are `[TBD]`

---

## 1. Overview

Geometric transformation models define how coordinates in one image are mapped to corresponding coordinates in another image.

For ChandraMap, this is the stage where verified image correspondences are converted into an explicit geometric relationship between a source image and a reference image.

The conceptual registration pipeline is:

```text
Source Image
     ↓
Feature Detection
     ↓
Descriptor Extraction
     ↓
Candidate Correspondences
     ↓
Geometric Verification
     ↓
Transformation Estimation
     ↓
Residual Analysis
     ↓
Optional Sub-Pixel Refinement
     ↓
Final Registration
```

The project feedback recommends using a simple geometric model first, fitting the initial model with RANSAC, inspecting residual vectors across the image, and only introducing more flexible warps when the evidence shows that the simpler model is insufficient.

The purpose of this note is therefore not to declare a universally correct transformation model. It establishes the scientific reasoning required to determine which model is appropriate for a particular lunar image pair.

---

# 2. Why Geometric Models Are Required

Feature matching produces corresponding points, but correspondences alone do not define a registered image.

Suppose the source image contains points:

$$
\mathbf{x}_i =
\begin{bmatrix}
x_i\\
y_i
\end{bmatrix}
$$

and the reference image contains corresponding points:

$$
\mathbf{x}'_i =
\begin{bmatrix}
x'_i\\
y'_i
\end{bmatrix}
$$

A geometric model defines a relationship:

$$
\mathbf{x}'_i \approx f(\mathbf{x}_i;\theta)
$$

where:

- \(f\) is the transformation model,
- \(\theta\) is the set of transformation parameters.

The model can then be used to:

- reject geometrically inconsistent matches,
- estimate image alignment,
- transform image coordinates,
- generate a registered image,
- calculate residual errors,
- evaluate registration accuracy.

Without a geometric model, ChandraMap cannot determine whether a collection of candidate matches is spatially consistent.

---

# 3. Correspondences and Geometry

A critical distinction in ChandraMap is:

```text
Candidate Matches
        ↓
Potential correspondences
        ↓
Geometric Verification
        ↓
Verified Inliers
        ↓
Transformation Estimation
```

Candidate matches are not automatically correct.

The project feedback explicitly recommends calling these **candidate matches** rather than treating matcher confidence as proof of correctness. RANSAC/geometric verification should determine which candidates become verified inliers.

This distinction is particularly important for lunar imagery because:

- crater structures can repeat,
- shadows can resemble terrain boundaries,
- scale can differ,
- sensor modalities can differ,
- illumination can change appearance,
- local terrain geometry can vary.

A geometric model provides a mechanism for testing whether proposed correspondences agree with a common spatial relationship.

---

# 4. ChandraMap Registration Context

ChandraMap may need to register images that differ in:

- sensor
- spatial resolution
- scale
- illumination
- viewing geometry
- image representation
- local terrain appearance

Relevant project imagery includes:

- Chandrayaan-2 OHRC
- Chandrayaan-2 TMC-2
- Chandrayaan-2 IIRS
- lunar reference products such as LRO imagery where applicable

The project materials emphasize that these inputs should not automatically be treated as identical image types. Sensor-specific preparation, comparable physical scale, illumination handling, and geometric verification are separate considerations.

Therefore, geometric modeling occurs **after or alongside correspondence generation**, not as a replacement for sensor-aware preprocessing.

---

# 5. Transformation Model vs Image Representation

These are different concepts.

### Image representation

Determines what information is presented to the matching algorithm.

Examples:

- raw grayscale
- gradient magnitude
- edge representation
- structural representation
- IIRS-derived 2D representation

### Geometric model

Determines how coordinates are related between the two images.

Examples:

- affine transformation
- homography
- local/piecewise transformation

Conceptually:

```text
Image Representation
        ↓
Feature / Descriptor Extraction
        ↓
Candidate Correspondences
        ↓
Geometric Model
        ↓
Verified Inliers
        ↓
Registration
```

Changing the representation and changing the geometric model are therefore separate experimental variables.

---

# 6. Geometric Assumptions Matter

Every transformation model makes assumptions about the relationship between the two images.

A model is useful only when its assumptions are sufficiently consistent with the actual image pair.

A model that is too simple may leave systematic residuals.

A model that is too flexible may:

- fit noise,
- absorb incorrect correspondences,
- hide poor control points,
- produce visually convincing but scientifically weak registration.

The project feedback therefore recommends:

> **Use the simplest transform that explains the residuals.**

This principle is particularly important for lunar imagery.

---

# 7. Affine Transformation

An affine transformation can be written as:

$$
x' = a_{11}x + a_{12}y + t_x
$$

$$
y' = a_{21}x + a_{22}y + t_y
$$

or in matrix form:

$$
\begin{bmatrix}
x'\\
y'
\end{bmatrix}
=
\begin{bmatrix}
a_{11} & a_{12}\\
a_{21} & a_{22}
\end{bmatrix}
\begin{bmatrix}
x\\
y
\end{bmatrix}
+
\begin{bmatrix}
t_x\\
t_y
\end{bmatrix}
$$

An affine model has six independent parameters.

---

# 8. What an Affine Model Can Represent

An affine transformation can represent combinations of:

- translation
- rotation
- scaling
- anisotropic scaling
- shear

Conceptually:

```text
Translation
Rotation
Scale
Shear
   ↓
Affine transformation
```

It preserves:

- straight lines
- parallelism

It does not generally preserve:

- angles
- distances
- areas

although special cases such as rigid transformations preserve additional properties.

---

# 9. Why Affine Transformation Is Useful in ChandraMap

An affine model can be a reasonable first geometric model when:

- the image pair is already map-projected or orthorectified,
- the overlap is local,
- perspective effects are limited,
- scale differences are approximately uniform or affine,
- terrain-induced distortions are small over the region.

The project feedback explicitly identifies affine transformations as a reasonable first model for a local, already map-projected pair.

Affine transformation is also attractive because it is relatively simple and interpretable.

---

# 10. What an Affine Model Cannot Represent Well

An affine model cannot generally represent:

- projective perspective effects,
- depth-dependent displacement,
- arbitrary nonlinear terrain distortion,
- local deformation,
- complex sensor geometry,
- relief-induced parallax.

If the true image relationship contains effects outside the affine model, residuals may exhibit systematic spatial structure.

For example:

```text
Observed residuals

← small                    large →
┌──────────────────────────────┐
│ → → → → → → → → → →         │
│   → → → → → → → →           │
│     → → → → → → →           │
└──────────────────────────────┘
```

A pattern like this would be evidence that the affine model may not adequately explain the observed correspondences.

It would not, by itself, prove that homography is the correct next model.

---

# 11. Homography / Projective Transformation

A homography represents a projective mapping between two image planes.

In homogeneous coordinates:

$$
\begin{bmatrix}
x'\\
y'\\
1
\end{bmatrix}
\sim
H
\begin{bmatrix}
x\\
y\\
1
\end{bmatrix}
$$

where:

$$
H =
\begin{bmatrix}
h_{11} & h_{12} & h_{13}\\
h_{21} & h_{22} & h_{23}\\
h_{31} & h_{32} & h_{33}
\end{bmatrix}
$$

and the transformation is defined up to a non-zero scale factor.

After homogeneous transformation:

$$
x' =
\frac{h_{11}x+h_{12}y+h_{13}}
{h_{31}x+h_{32}y+h_{33}}
$$

$$
y' =
\frac{h_{21}x+h_{22}y+h_{23}}
{h_{31}x+h_{32}y+h_{33}}
$$

A homography therefore has eight independent degrees of freedom.

---

# 12. What a Homography Can Represent

A homography can represent projective effects that an affine model cannot.

It can account for:

- translation
- rotation
- scaling
- shear
- projective perspective distortion

It can map one planar image region to another under a perspective transformation.

This makes homography a useful candidate for local image registration when the planar/projective approximation is reasonable.

---

# 13. Why Homography Is Not Automatically the Correct Lunar Model

The Moon is not a flat image plane.

A lunar surface contains:

- craters
- ridges
- slopes
- relief
- depressions
- varying terrain elevation

If the viewing geometry differs, points at different elevations can experience different image displacement.

A single global homography cannot generally model arbitrary 3D terrain-induced displacement.

The project feedback explicitly warns that the Moon is not a flat surface and that raw images may also contain sensor/viewing geometry. It therefore recommends not assuming that one global transformation is always sufficient.

---

# 14. Affine vs Homography

The conceptual difference is:

| Property                           | Affine                          | Homography                      |
| ---------------------------------- | ------------------------------- | ------------------------------- |
| Translation                        | Yes                             | Yes                             |
| Rotation                           | Yes                             | Yes                             |
| Scaling                            | Yes                             | Yes                             |
| Shear                              | Yes                             | Yes                             |
| Projective effects                 | No                              | Yes                             |
| Parallel lines remain parallel     | Yes                             | Not necessarily                 |
| Parameters                         | 6                               | 8 degrees of freedom            |
| Model flexibility                  | Lower                           | Higher                          |
| Typical minimum point requirement  | 3 non-collinear correspondences | 4 non-collinear correspondences |
| Risk of fitting unwanted variation | Lower                           | Higher                          |
| Lunar suitability                  | Pair-dependent                  | Pair-dependent                  |

The table describes mathematical model properties, not an overall ranking.

Neither model should be considered universally superior.

---

# 15. Minimum Correspondence Requirements

For an affine transformation, at least three non-collinear point correspondences are theoretically required to determine the six parameters.

For a homography, at least four point correspondences in a suitable non-degenerate configuration are theoretically required.

In practical ChandraMap registration, the number of correspondences should be substantially larger because:

- matches contain noise,
- some matches are outliers,
- point geometry can be degenerate,
- RANSAC needs alternatives from which to estimate candidate models,
- independent evaluation requires points not used for fitting.

Therefore:

> **The mathematical minimum is not an appropriate target for a robust registration experiment.**

---

# 16. Degenerate Point Configurations

Even with enough points numerically, their spatial arrangement can make transformation estimation unstable.

Examples include:

```text
Good distribution

●                 ●

        ●

●                 ●
```

versus:

```text
Poor distribution

● ● ● ● ● ● ●
```

or:

```text
Poor distribution

      ●
      ●
      ●
      ●
```

This is why spatial coverage is a core evaluation concept in ChandraMap.

A large number of matches clustered around one crater does not necessarily provide a well-constrained global transformation.

---

# 17. Spatial Coverage

The project evaluation guidance recommends measuring whether verified points are distributed across the overlap rather than clustered around a single feature. Suggested approaches include:

- grid coverage
- convex-hull coverage

A possible grid-based metric is:

```text
Image overlap
┌────┬────┬────┬────┐
│ ✓  │ ✓  │    │ ✓  │
├────┼────┼────┼────┤
│    │ ✓  │ ✓  │    │
├────┼────┼────┼────┤
│ ✓  │    │ ✓  │ ✓  │
├────┼────┼────┼────┤
│    │ ✓  │    │    │
└────┴────┴────┴────┘
```

The exact coverage definition should come from the benchmark implementation.

If not yet implemented:

```text
Spatial coverage implementation: [TBD]
```

---

# 18. RANSAC and Geometric Verification

ChandraMap should distinguish descriptor matching from geometric verification.

A conceptual pipeline is:

```text
Candidate Matches
        ↓
RANSAC
        ↓
Initial Geometric Model
        ↓
Inliers / Outliers
        ↓
Transformation
```

RANSAC repeatedly estimates a model from subsets of candidate correspondences and evaluates how many observations are consistent with that model under a chosen error threshold.

The specific implementation, threshold, confidence, iteration count, and random seed must be recorded by the experiment.

---

# 19. Why RANSAC Is Important

Feature matching can produce incorrect correspondences.

For example:

```text
Candidate matches

✓ correct
✓ correct
✗ incorrect
✓ correct
✗ incorrect
✗ incorrect
✓ correct
```

A geometric verification step attempts to identify the subset that can be explained by a common transformation.

This is especially important for lunar images because visually similar terrain structures and illumination-dependent features can create ambiguous correspondences.

---

# 20. Candidate Matches vs Verified Inliers

The terminology should remain explicit:

### Candidate match

A proposed correspondence generated by the matching stage.

### Verified inlier

A candidate correspondence that is consistent with the estimated geometric model according to the geometric verification procedure.

### Outlier

A candidate correspondence rejected by the geometric model.

Therefore:

```text
Candidate matches
        ↓
Geometric verification
        ├── Inliers
        └── Outliers
```

A matcher confidence score should not be treated as equivalent to geometric correctness.

---

# 21. RANSAC Parameters

A reproducible geometric experiment should record at least:

| Parameter              | Value   |
| ---------------------- | ------- |
| Model                  | `[TBD]` |
| Reprojection threshold | `[TBD]` |
| Confidence             | `[TBD]` |
| Maximum iterations     | `[TBD]` |
| Minimum inliers        | `[TBD]` |
| Random seed            | `[TBD]` |
| Degeneracy handling    | `[TBD]` |
| Implementation/library | `[TBD]` |

Do not invent parameter values in research documentation.

---

# 22. Transformation Estimation Sequence

The recommended sequence is:

```text
Candidate Matches
        ↓
RANSAC + Initial Model
        ↓
Verified Inliers
        ↓
Optional Sub-Pixel Refinement
        ↓
Refit Final Transformation
        ↓
Independent Evaluation
```

The project feedback specifically corrects the ordering so that RANSAC establishes reliable inliers before sub-pixel refinement, followed by final transformation refitting.

This is important because sub-pixel refinement should not be used to refine obviously incorrect correspondences.

---

# 23. Residuals

A residual measures the disagreement between an observed correspondence and the position predicted by the fitted transformation.

For correspondence \(i\):

$$
\mathbf{r}_i =
\mathbf{x}'_i -
\hat{\mathbf{x}}'_i
$$

where:

- \(\mathbf{x}'\_i\) is the observed reference point,
- \(\hat{\mathbf{x}}'\_i\) is the position predicted by the transformation.

The residual magnitude is:

$$
e_i = \|\mathbf{r}_i\|
$$

Residuals can be represented as:

- scalar errors
- vectors
- spatial fields
- histograms
- percentiles
- RMSE

---

# 24. Why Residuals Matter

A transformation can produce a visually convincing overlay while still containing systematic geometric error.

For example:

```text
Residual magnitude

Low ───────────────────────── High
```

or:

```text
Residual vectors

→ → → → →
 → → → →
  → → →
```

A systematic residual pattern can indicate that the selected model is not adequately describing the underlying geometry.

The project feedback specifically recommends inspecting residual vectors across the image rather than relying only on visual overlay quality.

---

# 25. Random vs Systematic Residuals

Residual patterns should be interpreted carefully.

### Approximately random residuals

May be consistent with:

- measurement noise
- local feature localization uncertainty
- descriptor/matching uncertainty

### Systematic residuals

May indicate:

- incorrect transformation model
- unmodeled perspective
- local terrain relief
- sensor geometry
- projection differences
- spatially varying registration error

However, residual structure should be interpreted together with the acquisition geometry and image metadata.

A systematic pattern does not automatically prove that the geometric model alone is responsible.

---

# 26. Residuals and Model Complexity

A useful model-selection principle is:

```text
Too simple
    ↓
Systematic residuals

Appropriate model
    ↓
Residuals sufficiently explained

Too flexible
    ↓
May fit noise / bad correspondences
```

The goal is not to minimize training or fitting residuals at any cost.

The goal is to identify a model that explains the measured correspondences without hiding poor correspondence quality.

---

# 27. Overfitting With Flexible Warps

A flexible transformation can make an overlay appear excellent even when the original correspondences are weak.

Conceptually:

```text
Weak correspondences
        ↓
Very flexible warp
        ↓
Visually convincing image
```

This does not necessarily mean that the underlying correspondence is accurate.

The project feedback explicitly warns against letting warping hide bad matches and recommends using flexible warps only after control points are accurate and well distributed.

---

# 28. Lunar Terrain and Non-Planarity

The lunar surface presents a fundamental modeling challenge.

A local image region can contain significant elevation variation.

For a changing viewpoint:

```text
Terrain point A ─────── elevation 1
Terrain point B ─────── elevation 2
Terrain point C ─────── elevation 3
```

their projected image locations can change differently.

A single 2D transformation assumes a simplified relationship between the two image planes.

This approximation may work over a sufficiently small or geometrically favorable region, but it cannot universally represent arbitrary 3D terrain.

---

# 29. Map-Projected or Orthorectified Products

Geometric modeling should account for whether the input products have already undergone geospatial correction.

If products are already:

- map-projected
- orthorectified
- geometrically corrected

then some geometric relationships may already have been handled upstream.

The project feedback explicitly recommends taking advantage of existing mapping/orthorectification rather than asking computer-vision registration to solve geometry that the product processing already provides.

Therefore, experiment metadata should include:

```text
Projection status: [TBD]
Orthorectification status: [TBD]
Map projection: [TBD]
DEM usage: [TBD]
```

---

# 30. Sensor Geometry

Raw or minimally processed imagery can contain effects associated with:

- spacecraft viewing geometry
- sensor geometry
- projection
- terrain relief
- acquisition configuration

These effects are different from ordinary feature-matching noise.

If they dominate the observed residuals, a simple image-plane transformation may be insufficient.

Potential future approaches include:

- sensor geometry models
- DEM-assisted correction
- orthorectification
- local/piecewise transformations
- physically informed photogrammetric models

These are research directions unless implemented.

---

# 31. Sensor Differences and Geometric Models

The geometric model does not eliminate differences between sensors.

For example:

```text
OHRC
  ↓
High-resolution visible imagery

TMC-2
  ↓
Lower-resolution panchromatic imagery

IIRS
  ↓
Hyperspectral / infrared measurements
```

The project materials emphasize that OHRC, TMC-2, and IIRS require sensor-aware handling and should not simply be forced through one identical preprocessing path.

A geometric model operates on the resulting correspondences. If sensor differences prevent reliable correspondence generation, choosing a more complex transformation cannot necessarily solve the problem.

---

# 32. Scale and Geometric Modeling

Scale differences and geometric transformations are related but should not be conflated.

A reference pyramid can make images comparable at a physically meaningful scale before local matching.

The project feedback recommends comparing information at comparable effective ground scales rather than solving scale differences by simple upsampling.

Conceptually:

```text
Different GSD
      ↓
Comparable effective scale
      ↓
Feature correspondence
      ↓
Geometric model
```

Upsampling does not recover spatial information absent from the source.

---

# 33. Illumination and Geometry Are Different Variables

Illumination can affect correspondence without necessarily changing the underlying terrain geometry.

For example:

```text
Same terrain
     ↓
Different Sun angle
     ↓
Different shadows / appearance
     ↓
Different candidate matches
```

This is different from:

```text
Different viewing geometry
     ↓
Different image projection
     ↓
Different spatial relationship
```

The two effects can interact, but they should remain separate experimental concepts.

The illumination research note discusses this distinction in greater detail.

---

# 34. Geometry After Illumination Changes

A representation may reduce illumination-related matching errors, but the resulting correspondences still need geometric verification.

Therefore:

```text
Illumination handling
        ↓
Better candidate correspondences
        ↓
RANSAC
        ↓
Geometric verification
```

A method should not be considered successful merely because it produces visually similar images after preprocessing.

The final evaluation remains geometric.

---

# 35. Transformation Model Selection as an Experiment

ChandraMap should determine model suitability empirically.

A controlled comparison can use:

```text
Same image pair
Same preprocessing
Same features
Same descriptor matching
Same candidate filtering
Same evaluation points
Same RANSAC protocol
        │
        ├── Affine
        │
        └── Homography
```

The primary changed variable is the geometric model.

---

# 36. EXP-004 — Affine vs Homography

The corresponding V1 experiment is:

`experiments/v1/geometry/EXP-004-affine-vs-homography/README.md`

Its role is to determine how the two candidate models behave under the selected V1 image pairs and evaluation protocol.

The experiment should compare measurable quantities such as:

- candidate matches
- verified inlier count
- inlier ratio
- spatial coverage
- checkpoint RMSE
- residual distribution
- runtime
- failure behavior

The experiment should not begin with the assumption that one model must win.

---

# 37. Recommended EXP-004 Comparison

A conceptual experiment matrix is:

| Variable            | Affine                | Homography            |
| ------------------- | --------------------- | --------------------- |
| Image pair          | Same                  | Same                  |
| Sensor pair         | Same                  | Same                  |
| Scale configuration | Same                  | Same                  |
| Representation      | Same                  | Same                  |
| Feature detector    | Same                  | Same                  |
| Descriptor          | Same                  | Same                  |
| Matcher             | Same                  | Same                  |
| Candidate filtering | Same                  | Same                  |
| RANSAC procedure    | Same where applicable | Same where applicable |
| Evaluation points   | Same                  | Same                  |
| Geometric model     | **Affine**            | **Homography**        |

This isolates model selection as the primary variable.

---

# 38. Metrics for Model Comparison

A model comparison should consider multiple measurements.

## 38.1 Inlier Count

$$
N_{\text{inliers}}
$$

Measures the number of candidate matches consistent with the fitted model.

---

## 38.2 Inlier Ratio

$$
R_{\text{inlier}}
=
\frac{N_{\text{inliers}}}
{N_{\text{candidate}}}
$$

This helps distinguish a model that accepts many candidates from one that only accepts a small fraction.

---

## 38.3 Spatial Coverage

Measures whether inliers are distributed across the overlap.

A high inlier count concentrated in one location may not provide sufficient geometric support.

---

## 38.4 Checkpoint RMSE

For independent check points:

$$
RMSE =
\sqrt{
\frac{1}{N}
\sum_{i=1}^{N}e_i^2
}
$$

This should be evaluated on points not used to fit the final transformation when independent evaluation is intended.

---

## 38.5 Residual Distribution

Useful statistics may include:

- mean
- median
- RMSE
- P90
- P95
- maximum

Only metrics implemented by the benchmark should be treated as official.

---

## 38.6 Runtime

Measure:

- transformation estimation time
- geometric verification time
- complete pipeline time where relevant

Do not assume a more complex model has a particular runtime without measurement.

---

# 39. Independent Check Points

One of the most important evaluation principles is:

> **Do not fit and judge the transformation using exactly the same points.**

If a model is estimated from the same points used to report error, the measured error can be artificially favorable.

The project guidance recommends using challenge ground truth where available, or independently checked tie points that are withheld from transformation fitting.

The conceptual split is:

```text
Control / fitting points
        ↓
Estimate transformation

Independent check points
        ↓
Evaluate transformation
```

---

# 40. Ground Truth and Check Points

Potential sources include:

- challenge-provided ground truth
- independently verified tie points
- other documented reference measurements

Exact V1 ground-truth implementation:

```text
Ground-truth source: [TBD]
Check-point format: [TBD]
Check-point count: [TBD]
Validation procedure: [TBD]
```

The transformation-fitting points and independent evaluation points should remain clearly distinguishable.

---

# 41. Error Units

The project requires careful reporting of error units.

The primary registration error should be reported in source-image pixels where appropriate.

For example:

```text
Checkpoint RMSE = [TBD] source pixels
```

Conversion to metres should only be performed when:

- GSD is known,
- projection is known,
- the reference relationship supports the conversion,
- the interpretation is scientifically meaningful.

The project feedback explicitly warns that the same pixel error does not imply the same ground error for sensors with different GSDs.

---

# 42. Residual Analysis — EXP-005

`EXP-005 — Residual Analysis` provides the next level of geometric investigation.

The conceptual question changes from:

> Which model produces a smaller aggregate error?

to:

> **Where and how does the model fail?**

Useful residual analyses include:

- residual magnitude map
- residual vector field
- residual histogram
- error vs image position
- error vs terrain region
- error vs feature type
- error vs illumination condition

The exact analyses implemented in EXP-005 should remain authoritative in that experiment's README.

---

# 43. Residual Vector Fields

A residual vector field can be visualized as:

```text
Image overlap

┌──────────────────────────────┐
│ →    →     ↗     →          │
│   →     →      ↗             │
│ →    →      →      →         │
│    →      →      →           │
└──────────────────────────────┘
```

The vectors indicate:

- direction of registration error
- magnitude of registration error
- spatial structure of error

Patterns may reveal whether a transformation is:

- globally consistent,
- locally biased,
- spatially distorted,
- inadequate for the observed geometry.

---

# 44. Model Underfitting

A model may be too simple for the image relationship.

Possible signs:

- systematic residual gradient
- residual curvature
- different error behavior across image regions
- consistent error near image boundaries
- structured displacement patterns

For example:

```text
Observed:

→ → → → → →
  → → → → →
    → → → →

Potential interpretation:
spatially varying model error
```

This should lead to investigation, not an automatic decision to use a more complex warp.

---

# 45. Model Overfitting

A model may be unnecessarily flexible relative to the evidence.

Potential warning signs:

- very low fitting residuals but poor independent checkpoint error
- unstable results across repeated runs
- sensitivity to small changes in correspondences
- strong transformation changes from a few points
- visually convincing registration with poor correspondence quality

This is why independent evaluation is essential.

---

# 46. Local and Piecewise Models

If a global affine or homography model is inadequate, future research may investigate:

- piecewise affine transformations
- local warps
- thin-plate-spline-like approaches
- local control-point warping
- DEM-assisted transformations
- sensor-model-based registration

These approaches should not be introduced merely to reduce residuals.

The project feedback specifically recommends testing local/piecewise warping or using sensor geometry/DEM information when residuals show systematic spatial variation.

---

# 47. Why Flexible Models Need Stronger Controls

A flexible model can absorb correspondence errors.

Therefore, before using one, verify:

- point accuracy
- point distribution
- inlier quality
- independent evaluation
- residual structure

Conceptually:

```text
Accurate + distributed points
          ↓
Flexible model can be meaningful

Weak + clustered points
          ↓
Flexible model may hide errors
```

The objective is not to create the best-looking overlay.

The objective is to establish a defensible geometric relationship.

---

# 48. Sub-Pixel Refinement

Sub-pixel refinement operates at a later stage.

The recommended conceptual sequence is:

```text
Candidate Matches
        ↓
RANSAC
        ↓
Verified Inliers
        ↓
Sub-Pixel Tie-Point Refinement
        ↓
Final Transform Refit
        ↓
Independent Check-Point Evaluation
```

The project feedback specifically recommends refining reliable inlier coordinates and then estimating the final transformation again.

Sub-pixel refinement therefore should not be treated as a substitute for choosing an appropriate geometric model.

---

# 49. Geometric Model and Sub-Pixel Refinement Interaction

Suppose the refined points become:

$$
\mathbf{x}_i^{*}
$$

and:

$$
\mathbf{x}'_i{}^{*}
$$

The final transformation should be estimated from the refined coordinates when that procedure is part of the experiment.

This can reduce localization error, but it cannot guarantee that the chosen geometric model is physically appropriate.

For example:

```text
Incorrect global model
        +
Highly precise points
        ↓
Precisely estimated incorrect model
```

Sub-pixel precision does not compensate for model mismatch.

---

# 50. Illumination-Dependent Residuals

Illumination can influence which correspondences survive geometric verification.

A residual pattern may therefore reflect both:

- geometric model limitations
- correspondence instability caused by illumination

These should be separated experimentally where possible.

For example:

```text
Same geometry
Different illumination
        ↓
Different correspondence set
        ↓
Different residual field
```

The residual analysis should therefore retain image-pair and illumination metadata.

---

# 51. Transformation Model and Scale

A geometric transformation should not be expected to solve large physical scale differences that should have been handled through multi-scale preprocessing.

The recommended conceptual order is:

```text
Sensor-aware preparation
        ↓
Comparable physical scale
        ↓
Local matching
        ↓
Geometric transformation
```

The project feedback explicitly recommends a reference pyramid or downsampling of the higher-resolution side to establish comparable effective ground scale before fine matching.

---

# 52. Transformation Model and Sensor Modality

A geometric model operates on coordinates, not on sensor physics.

If a source image and reference image have substantially different appearance because of:

- spectral response
- modality
- illumination
- resolution

then the primary problem may occur during correspondence generation.

A homography cannot turn an incorrect descriptor match into a correct terrain correspondence.

Therefore:

```text
Better correspondence
        +
Appropriate geometric model
        ↓
Reliable registration
```

Both components are necessary.

---

# 53. Model Selection Decision Framework

A practical ChandraMap decision process is:

```text
Are products already map-projected / orthorectified?
        │
        ├── Yes
        │    ↓
        │  Test simple local model
        │
        └── No / uncertain
             ↓
          Inspect sensor/view geometry
             ↓
          Determine whether image-plane model is sufficient
```

Then:

```text
Fit affine
   ↓
Inspect residuals
   │
   ├── Adequately explained
   │       ↓
   │    Retain affine candidate
   │
   └── Systematic residuals
           ↓
        Test homography
           ↓
        Reinspect residuals
```

If both fail:

```text
Investigate:
- local/piecewise model
- DEM/sensor geometry
- orthorectification
- data/projection issues
```

This is a research workflow, not a fixed universal algorithm.

---

# 54. Suggested Model-Selection Questions

For each image pair, ask:

1. Are the products map-projected?
2. Are they orthorectified?
3. What is the source GSD?
4. What is the reference GSD?
5. Is the overlap local?
6. Is viewpoint difference small or significant?
7. Is terrain relief important?
8. Are sensor geometry metadata available?
9. Are correspondences spatially distributed?
10. Does affine produce systematic residuals?
11. Does homography reduce systematic residuals?
12. Does independent checkpoint error improve?
13. Does the model remain stable across repeated runs?
14. Does increased model flexibility improve actual independent accuracy?

---

# 55. Avoiding "More Flexible = Better"

A common but unsafe assumption is:

```text
Affine
  ↓
Homography
  ↓
More flexible warp
  ↓
Better
```

This is not a valid scientific progression by itself.

A more flexible model has more ability to represent variation, but it also has more ability to fit:

- noise
- incorrect matches
- local artifacts
- poorly distributed control points

Therefore the appropriate question is:

> **Does the additional model flexibility explain genuine geometric structure and improve independent registration accuracy without compromising correspondence quality or interpretability?**

---

# 56. Recommended Evidence Table

A completed model comparison should eventually resemble:

| Model      | Candidate Matches | Inliers | Inlier Ratio | Coverage | Checkpoint RMSE | Runtime | Failure Notes |
| ---------- | ----------------: | ------: | -----------: | -------: | --------------: | ------: | ------------- |
| Affine     |           `[TBD]` | `[TBD]` |      `[TBD]` |  `[TBD]` |         `[TBD]` | `[TBD]` | `[TBD]`       |
| Homography |           `[TBD]` | `[TBD]` |      `[TBD]` |  `[TBD]` |         `[TBD]` | `[TBD]` | `[TBD]`       |

No numerical values should be entered until measured.

---

# 57. Qualitative Evaluation

Quantitative metrics should be supported by visual inspection.

Useful visualizations include:

- source image
- reference image
- candidate matches
- rejected matches
- verified inliers
- registered overlay
- residual vectors
- residual magnitude map
- spatial coverage map

A visual overlay is useful for diagnosis, but it should not replace independent quantitative evaluation.

---

# 58. Failure Cases

Important geometric failure cases include:

### Insufficient Correspondences

Too few reliable points to estimate a stable model.

### Degenerate Correspondence Geometry

Points are present but spatially arranged poorly.

### Clustered Inliers

Many matches occur around one local feature.

### Wrong Model

The selected model cannot explain the spatial relationship.

### Relief-Induced Distortion

Terrain elevation causes spatially varying displacement.

### Sensor Geometry Effects

Raw acquisition geometry introduces distortions not represented by a simple 2D model.

### Projection Mismatch

Images use different or incompatible geometric reference systems.

### Illumination-Driven False Matches

Shadows or appearance structures generate incorrect correspondences.

---

# 59. Failure-Case Documentation

Each failure should record:

```markdown
## Failure Case

### Image Pair

[TBD]

### Sensor / Product

[TBD]

### Model

[TBD]

### Observed Behavior

[TBD]

### Residual Pattern

[TBD]

### Spatial Distribution

[TBD]

### Suspected Cause

[TBD]

### Evidence

[TBD]

### Impact

[TBD]

### Reproduction

[TBD]

### Follow-Up Experiment

[TBD]
```

A failure should not be removed simply because it produces an inconvenient result.

Failure cases are evidence about the operating limits of the method.

---

# 60. Geometry Stress Testing

The project feedback proposes a geometry stress case involving relief-rich terrain or stronger viewpoint differences.

A useful stress matrix can include:

| Stress Case         | Scientific Purpose                       |
| ------------------- | ---------------------------------------- |
| Easy pair           | Establish end-to-end correctness         |
| Scale stress        | Test scale handling separately           |
| Sun-angle stress    | Test illumination-related correspondence |
| Modality stress     | Test sensor differences                  |
| Geometry stress     | Test transformation-model limitations    |
| Low-feature terrain | Test weak correspondence conditions      |

The exact benchmark cases remain `[TBD]` until the dataset is defined.

---

# 61. Relationship to the V1 Experimental Progression

Geometric modeling appears after the earlier representation and scale experiments.

The V1 progression is conceptually:

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
EXP-004
Affine vs Homography
        ↓
EXP-005
Residual Analysis
        ↓
EXP-006
Sub-Pixel Refinement
```

This ordering separates several sources of uncertainty.

---

# 62. EXP-001 — SIFT Baseline

The baseline establishes the initial correspondence pipeline:

```text
SIFT
 ↓
Descriptor Matching
 ↓
Candidate Filtering
 ↓
RANSAC
 ↓
Affine / Homography Candidate
 ↓
Residual Evaluation
```

The exact baseline model configuration should remain defined by:

`experiments/v1/baseline/EXP-001-sift-baseline/README.md`

The geometric-model research should not silently change the baseline while comparing models.

---

# 63. EXP-002 — Scale Pyramid

Scale handling should establish comparable physical scale before local geometric estimation.

The relevant experiment is:

`experiments/v1/preprocessing/EXP-002-scale-pyramid/README.md`

Its output should be considered a preprocessing/scale variable rather than a replacement for geometric modeling.

---

# 64. EXP-003 — Gradient Representation

Gradient representation investigates whether structural image information improves correspondence under difficult appearance conditions.

The relevant experiment is:

`experiments/v1/preprocessing/EXP-003-gradient-representation/README.md`

The geometric model should remain controlled while representation is being investigated.

---

# 65. EXP-004 — Affine vs Homography

This is the primary V1 experiment for the subject of this note:

`experiments/v1/geometry/EXP-004-affine-vs-homography/README.md`

The scientific objective is not:

> "Determine whether homography is better."

Instead:

> **Determine which model provides an adequate and empirically defensible geometric explanation for the selected ChandraMap image pairs under the defined evaluation protocol.**

---

# 66. EXP-005 — Residual Analysis

Residual analysis follows model comparison.

The relevant experiment is:

`experiments/v1/geometry/EXP-005-residual-analysis/README.md`

Its role is to investigate whether remaining errors show spatial structure that may indicate:

- model mismatch
- terrain-related effects
- sensor geometry
- correspondence problems
- other systematic effects

---

# 67. EXP-006 — Sub-Pixel Refinement

Sub-pixel refinement follows reliable geometric verification.

The relevant experiment is:

`experiments/v1/refinement/EXP-006-subpixel-refinement/README.md`

The final transformation should be refit after verified points are refined when that procedure is implemented.

The final evaluation should remain based on independent check points where available.

---

# 68. Reproducibility Requirements

A geometric-model experiment should record enough information to reproduce the transformation.

At minimum:

| Parameter            | Value   |
| -------------------- | ------- |
| Experiment ID        | `[TBD]` |
| Git commit           | `[TBD]` |
| Dataset version      | `[TBD]` |
| Source image         | `[TBD]` |
| Reference image      | `[TBD]` |
| Sensor pair          | `[TBD]` |
| Source GSD           | `[TBD]` |
| Reference GSD        | `[TBD]` |
| Projection           | `[TBD]` |
| Orthorectification   | `[TBD]` |
| Representation       | `[TBD]` |
| Feature detector     | `[TBD]` |
| Descriptor           | `[TBD]` |
| Matcher              | `[TBD]` |
| Candidate filtering  | `[TBD]` |
| Geometric model      | `[TBD]` |
| RANSAC threshold     | `[TBD]` |
| RANSAC confidence    | `[TBD]` |
| RANSAC iterations    | `[TBD]` |
| Random seed          | `[TBD]` |
| Sub-pixel refinement | `[TBD]` |
| Check-point set      | `[TBD]` |
| Python version       | `[TBD]` |
| OpenCV version       | `[TBD]` |
| Operating system     | `[TBD]` |
| CPU                  | `[TBD]` |
| GPU                  | `[TBD]` |
| CUDA                 | `[TBD]` |

---

# 69. Research Interpretation Rules

### Rule 1 — Do not select a model by visual overlay alone

A visually pleasing registration can still be geometrically wrong.

### Rule 2 — Do not use fitting error as independent accuracy

Use independent check points where available.

### Rule 3 — Do not assume homography is universally superior to affine

Model suitability depends on the image pair and geometric assumptions.

### Rule 4 — Do not assume affine is sufficient because the images look similar

Residual analysis should determine whether systematic error remains.

### Rule 5 — Do not add flexible warps just to reduce residuals

The additional flexibility must correspond to meaningful geometric structure.

### Rule 6 — Do not ignore spatial coverage

Clustered matches can produce an unstable transformation.

### Rule 7 — Do not confuse illumination with geometry

A shadow change can affect correspondence without representing terrain displacement.

### Rule 8 — Do not use geometry to compensate for missing sensor information

A transformation model cannot recover spatial detail that the source sensor did not resolve.

---

# 70. Open Research Questions

## Q1 — When is affine sufficient?

Determine the image-pair and acquisition conditions under which affine residuals are adequately small and non-systematic.

---

## Q2 — When does homography provide measurable benefit?

Compare independent checkpoint error, coverage, stability, and residual structure rather than only fitting error.

---

## Q3 — Are residual improvements caused by genuine geometry?

Investigate whether reduced residuals correspond to physically meaningful geometric effects or simply increased model flexibility.

---

## Q4 — How does terrain relief affect a global model?

Test relief-rich regions separately from relatively uniform terrain.

---

## Q5 — How do sensor differences affect model stability?

Evaluate OHRC, TMC-2, IIRS-derived representations, and reference products separately where data permit.

---

## Q6 — How does illumination interact with model selection?

Determine whether different illumination conditions change the quality or distribution of correspondences enough to alter the apparent suitability of a geometric model.

---

## Q7 — When is a local transformation justified?

Investigate whether systematic residual fields justify local/piecewise modeling.

---

## Q8 — When should DEM or sensor geometry replace image-plane approximation?

Determine the conditions under which image-based affine/homography models are no longer adequate.

---

# 71. Recommended Future Geometry Research

Potential future directions include:

### Local / Piecewise Registration

Useful when a single global model cannot explain spatially varying residuals.

### DEM-Assisted Registration

Use terrain elevation to model geometric displacement when suitable DEM information exists.

### Sensor-Model-Based Registration

Use acquisition geometry rather than relying entirely on 2D image transformations.

### Orthorectification-Aware Registration

Separate upstream geometric correction from image-based residual registration.

### Robust Local Warping

Investigate controlled local transformations only after reliable, well-distributed correspondences are established.

These remain research directions unless implemented.

---

# 72. Scientific Conclusion

The geometric transformation model is not merely a technical parameter in ChandraMap.

It determines what spatial relationship the registration system is willing to assume between two images.

The central principle is:

> **Use the simplest geometric model that adequately explains the verified correspondences and remains accurate on independent evaluation points.**

For V1, affine and homography provide practical candidate models for controlled comparison. The project feedback identifies both as reasonable first models for local, already map-projected pairs, while explicitly warning that lunar terrain and raw sensor/viewing geometry can make a single global model insufficient.

Therefore:

```text
Do not ask:

"Which model is universally best?"

Ask:

"Which model adequately explains this image pair,
under this acquisition geometry,
with these verified correspondences,
and with what independent error?"
```

That question is experimentally measurable and aligns with ChandraMap's research objective.

---

# 73. Summary

The complete geometric reasoning chain is:

```text
Source Image
     │
     ▼
Sensor-Aware Preparation
     │
     ▼
Comparable Scale / Representation
     │
     ▼
Feature Detection
     │
     ▼
Descriptor Matching
     │
     ▼
Candidate Correspondences
     │
     ▼
RANSAC + Candidate Geometric Model
     │
     ├───────────────┐
     ▼               ▼
 Affine          Homography
     │               │
     └───────┬───────┘
             ▼
      Verified Inliers
             │
             ▼
       Residual Analysis
             │
       ┌─────┴─────┐
       │           │
   Adequate     Systematic
    residuals    residuals
       │           │
       ▼           ▼
    Retain      Investigate
     model      model/data/
                 geometry
       │
       ▼
 Optional Sub-Pixel Refinement
       │
       ▼
 Final Transform Refit
       │
       ▼
 Independent Check Points
       │
       ▼
 Quantitative Registration Error
```

The final objective is not simply to obtain a transformation.

It is to obtain a transformation that is:

- supported by reliable correspondences,
- geometrically interpretable,
- appropriate for the image-pair assumptions,
- supported by spatially distributed control points,
- validated independently,
- reproducible,
- and honest about its limitations.

---

## Related ChandraMap Documentation

- [Research directory](../README.md)
- [Illumination invariance](./illumination-invariance.md)
- [Literature](../literature/README.md)
- [V1 experiments](../../experiments/v1/README.md)
- [Experiment template](../../experiments/templates/EXPERIMENT_TEMPLATE.md)
- [EXP-001 — SIFT Baseline](../../experiments/v1/baseline/EXP-001-sift-baseline/README.md)
- [EXP-002 — Scale Pyramid](../../experiments/v1/preprocessing/EXP-002-scale-pyramid/README.md)
- [EXP-003 — Gradient Representation](../../experiments/v1/preprocessing/EXP-003-gradient-representation/README.md)
- [EXP-004 — Affine vs Homography](../../experiments/v1/geometry/EXP-004-affine-vs-homography/README.md)
- [EXP-005 — Residual Analysis](../../experiments/v1/geometry/EXP-005-residual-analysis/README.md)
- [EXP-006 — Sub-Pixel Refinement](../../experiments/v1/refinement/EXP-006-subpixel-refinement/README.md)

---

## Source-Grounded Project Principles

The project source material establishes several principles reflected in this note:

- RANSAC/geometric verification should distinguish candidate matches from verified inliers.
- Affine or homography are reasonable initial models for local, already map-projected pairs, but a single global model should not be assumed to handle all lunar geometry.
- Residual vectors should be inspected across the image to identify systematic model error.
- Accurate, well-distributed control points should precede flexible warping.
- Transformation fitting and independent evaluation should use separate point sets where possible.
- Registration error should be reported in source-image pixels before converting to physical ground units when the required GSD and projection information make that conversion meaningful.
- Geometry stress testing should be evaluated alongside scale, illumination, modality, and low-feature cases.
- Sub-pixel refinement should operate on verified inliers and be followed by final transformation refitting.
- The project should build measurable end-to-end results incrementally rather than introducing increasingly complex geometry without evidence.

Where ChandraMap's exact implementation, dataset, transformation parameters, or measured model-selection results are not established by the available project material, this note intentionally uses `[TBD]` rather than inventing project-specific facts.
