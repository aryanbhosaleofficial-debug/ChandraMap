# Illumination Invariance in Lunar Image Correspondence

> **Research note:** ChandraMap
> **Path:** `research/notes/illumination-invariance.md`
> **Status:** Foundational research note
> **Scope:** Lunar image correspondence and registration
> **Primary concern:** Illumination variation, Sun-angle changes, and appearance/structure changes
> **Evidence status:** Conceptual and methodological; quantitative project conclusions are `[TBD]`

---

## 1. Overview

Illumination variation is one of the central challenges in lunar image correspondence.

Two images may depict the same lunar region while appearing substantially different because they were acquired under different illumination geometries. On the Moon, changing illumination can affect not only overall brightness but also **shadow position, shadow extent, local gradients, visible boundaries, texture appearance, and the apparent structure of terrain features**.

This makes lunar illumination different from a simple photometric transformation such as multiplying every pixel by a constant.

ChandraMap therefore treats illumination handling as a research and experimental problem rather than assuming that normalization automatically makes images comparable.

The project feedback specifically recommends comparing raw grayscale against gradient, edge, or other structure-focused representations under strong Sun-angle differences and evaluating the performance change quantitatively.

The central research principle is:

> **A useful illumination-robust representation should preserve correspondence-relevant terrain structure despite changes in appearance, but illumination robustness must be demonstrated experimentally rather than assumed.**

---

# 2. Why Illumination Matters to ChandraMap

ChandraMap aims to establish reliable correspondences between images of the same lunar region captured under potentially different:

- spatial resolutions
- scales
- illumination conditions
- Sun angles
- viewing conditions
- sensors
- spectral characteristics
- geometric conditions

Relevant project sensors include:

- Chandrayaan-2 OHRC
- Chandrayaan-2 TMC-2
- Chandrayaan-2 IIRS

The project feedback emphasizes that these sensors should not automatically be treated as equivalent images and that sensor modality and Sun angle can matter alongside nominal spatial resolution.

For illumination specifically, the important distinction is:

```text
Same terrain
     │
     ├── Similar illumination
     │       ↓
     │   Similar appearance
     │
     └── Different illumination
             ↓
        Different appearance
             │
             ├── brightness changes
             ├── contrast changes
             ├── gradient changes
             ├── edge changes
             ├── shadow displacement
             ├── shadow extent changes
             └── potentially different visible structure
```

The correspondence problem therefore cannot be reduced to matching identical pixel intensities.

---

# 3. What Is Illumination Invariance?

In image correspondence, **illumination invariance** describes the ability of a representation, feature, descriptor, or matching method to preserve useful correspondence information when the illumination affecting the same physical scene changes.

For ChandraMap, a more careful formulation is:

> An illumination-robust correspondence method should retain enough stable information about the underlying lunar terrain to establish reliable correspondences despite changes in illumination conditions.

This definition deliberately avoids claiming perfect invariance.

A method can be:

- more robust than a baseline
- partially invariant to certain photometric changes
- useful under a specific range of Sun-angle differences
- sensitive to shadow changes
- robust to brightness scaling but not shadow displacement

Therefore:

```text
Illumination robustness
        ≠
Perfect illumination invariance
```

The distinction is important for scientific reporting.

---

# 4. Three Different Meanings of "Illumination Robustness"

Illumination effects can be separated into at least three related but different categories.

## 4.1 Photometric Invariance

Photometric invariance concerns changes such as:

- brightness offset
- brightness scaling
- contrast changes
- intensity normalization
- local intensity variation

A simplified model might be:

$$
I'(x,y) = aI(x,y) + b
$$

where:

- \(I(x,y)\) is the original image intensity
- \(a\) represents intensity scaling
- \(b\) represents an intensity offset

A method that remains useful under this type of change can be described as photometrically robust.

This does **not** imply robustness to changing shadow geometry.

---

## 4.2 Structural Robustness

Structural robustness concerns preserving correspondence-relevant information such as:

- crater rims
- ridge lines
- boundaries
- local terrain transitions
- relative feature arrangement
- local gradient structure

A structure-focused representation may reduce dependence on absolute brightness.

For example:

```text
Raw grayscale
    ↓
Absolute / relative intensity information

Gradient representation
    ↓
Local intensity change

Edge representation
    ↓
Strong boundaries / transitions
```

These representations can expose different aspects of the same terrain.

However, they can also introduce new failure modes such as:

- fragmented edges
- amplified noise
- weak gradients
- polarity changes
- irrelevant boundaries

Therefore structural representation is a research variable, not a guaranteed solution.

---

## 4.3 Illumination-Geometry Robustness

This is the more difficult case.

When the Sun direction changes, illumination can alter the geometry of observed shadows and the visibility of terrain structure.

For example:

```text
Sun angle changes
        ↓
Shadow direction changes
        ↓
Shadow position changes
        ↓
Visible boundaries change
        ↓
Local image structure changes
```

This is fundamentally different from simply changing image brightness.

A representation can be robust to intensity scaling while still being affected by a shadow that has moved to another location.

---

# 5. Lunar Illumination Is Not Just Brightness

This is the central scientific point of this note.

A simple photometric change can sometimes be represented as:

```text
Same structure
     +
Different intensity
```

In such a situation, normalization may help.

But a change in lunar illumination geometry can behave more like:

```text
Same terrain
     +
Different lighting direction
     ↓
Different shadow geometry
     ↓
Different visible image structure
```

Project feedback explicitly notes that a different Sun angle can move or reverse shadows around a crater and that contrast normalization cannot undo that geometry.

The distinction can be summarized as:

```text
Simple photometric change
        ↓
Brightness / contrast differs
        ↓
Normalization may help


Different illumination geometry
        ↓
Shadow geometry changes
        ↓
Local structure changes
        ↓
Normalization alone may be insufficient
```

This distinction should be preserved throughout ChandraMap research.

---

# 6. Effects of Sun-Angle Changes

A changing Sun angle can influence the appearance of a lunar region in several ways.

Potential effects include:

- shadow direction
- shadow location
- shadow length
- shadow extent
- illuminated area
- dark-to-bright transitions
- local contrast
- gradient orientation
- gradient magnitude
- edge visibility
- texture visibility
- apparent feature boundaries

The same crater can therefore produce substantially different image evidence under different illumination.

This does not necessarily mean that the terrain itself changed.

Instead:

```text
Physical terrain
       ↓
Illumination geometry
       ↓
Observed radiance / image
```

The registration system observes the final image, not the terrain directly.

---

# 7. Shadows as a Correspondence Problem

Shadows deserve special treatment because they can be useful features in one condition and misleading features in another.

A shadow may contain:

- strong intensity transitions
- strong gradients
- clear boundaries
- highly distinctive local structure

This can make a shadow attractive to a feature detector.

However, if the Sun direction changes, the same physical terrain may produce a shadow in a different location.

Therefore:

```text
Strong image feature
        ≠
Stable terrain feature
```

A matching algorithm can obtain a visually strong but physically unstable correspondence if it matches illumination-dependent structures rather than stable terrain geometry.

This is one reason why high match counts alone are insufficient for evaluating lunar registration.

The project evaluation guidance recommends measuring inlier count and ratio together with spatial coverage and independent checkpoint error rather than relying on match confidence alone.

---

# 8. Stable Terrain Structure

A useful illumination-robust strategy is to investigate image evidence that corresponds more directly to terrain structure.

Potentially useful structures include:

- crater rims
- ridge lines
- local boundaries
- terrain transitions
- relative geometry between nearby features
- persistent morphological structures

Project feedback specifically identifies crater rims, ridge lines, edges, gradients, phase-based representations, and relative geometry as possible stable clues for illumination-stress experiments.

These should be treated as candidate information sources rather than guaranteed invariant features.

---

# 9. Representation Choices

ChandraMap V1 provides a useful framework for investigating how representation affects correspondence.

The primary conceptual comparison is:

```text
Raw grayscale
       vs
Gradient / edge / structural representation
```

This comparison is particularly relevant for strong Sun-angle differences.

---

## 9.1 Raw Grayscale

Raw grayscale retains the original image intensity.

Advantages may include:

- maximum retention of image information
- direct compatibility with conventional feature detectors
- simple interpretation
- minimal preprocessing

Potential limitations include sensitivity to:

- brightness changes
- contrast changes
- illumination differences
- shadow appearance changes
- cross-sensor radiometric differences

Raw grayscale should therefore remain an important baseline rather than being assumed to be inadequate.

---

## 9.2 Gradient Magnitude

Gradient magnitude describes the strength of local intensity change.

Conceptually:

$$
G(x,y)=\sqrt{G_x(x,y)^2+G_y(x,y)^2}
$$

where \(G_x\) and \(G_y\) represent horizontal and vertical intensity derivatives.

A gradient representation can emphasize:

- crater boundaries
- ridge transitions
- local terrain edges
- structural changes

However, gradients are still derived from image intensity.

Therefore:

> Gradient representation should not be described as automatically illumination invariant.

Gradient magnitude may change when:

- illumination changes
- shadows move
- contrast changes
- surface visibility changes
- noise increases

The exact gradient operator used by ChandraMap should be documented by the corresponding implementation/experiment.

If the operator is not specified:

```text
Gradient operator: [TBD]
```

should be used rather than assuming a particular implementation.

---

## 9.3 Gradient Orientation

Gradient orientation describes the direction of the local intensity change:

$$
\theta(x,y)=\operatorname{atan2}(G_y,G_x)
$$

Orientation can provide structural information that differs from raw intensity or gradient magnitude.

However, orientation can also become unstable when:

- gradient magnitude is weak
- local contrast reverses
- shadows move
- noise dominates
- the corresponding structure changes

Its value therefore requires empirical evaluation.

---

## 9.4 Edge Representation

An edge representation emphasizes selected intensity boundaries.

Conceptually:

```text
Intensity image
      ↓
Boundary detection
      ↓
Edge representation
```

Potential advantages:

- emphasizes strong boundaries
- reduces some absolute-intensity information
- may highlight terrain structure

Potential limitations:

- edge fragmentation
- sensitivity to threshold selection
- noise-induced edges
- irrelevant boundaries
- loss of useful grayscale information
- shadow edges that are not stable across illumination conditions

An edge representation should therefore be evaluated against the raw baseline.

---

## 9.5 Structural Representation

"Structural representation" is a broader concept.

It may refer to a representation designed to emphasize information related to terrain organization rather than absolute brightness.

Possible implementations include:

- gradients
- edges
- phase-based representations
- derived terrain-structure maps
- other explicitly implemented structural transformations

The exact method must be documented.

The term should not be used as a placeholder for an unspecified algorithm.

---

# 10. Normalization vs Illumination Robustness

Intensity normalization can be useful.

Possible operations include:

- global normalization
- local contrast normalization
- histogram-based normalization
- percentile normalization
- standardized intensity scaling

But normalization changes the numerical representation of an image; it does not necessarily reconstruct the physical appearance that would have been observed under another Sun direction.

Therefore:

```text
Normalization
    ↓
Changes image intensity distribution

Not necessarily:

Normalization
    ↓
Recreates original illumination geometry
```

The project feedback recommends testing photometric correction where illumination geometry is available, rather than assuming it solves the problem.

---

# 11. Illumination Effects on Feature Detection

Illumination changes can affect whether a feature detector produces repeatable keypoints.

A feature may be:

```text
Clearly visible
    ↓
Strong keypoint
```

under one illumination condition, but:

```text
Weak / shadowed / altered
    ↓
No keypoint
```

under another.

This can affect:

- keypoint count
- keypoint location
- scale
- orientation
- descriptor stability
- spatial distribution

For SIFT-based ChandraMap experiments, this is particularly important because SIFT is used as the V1 baseline.

Project feedback identifies SIFT as a simple, explainable baseline while noting that it can struggle under strong modality and illumination changes.

---

# 12. Illumination Effects on Descriptor Matching

Even when a keypoint is detected in both images, the corresponding descriptors may differ.

A simplified correspondence chain is:

```text
Image A
  ↓
Keypoints
  ↓
Descriptors
  ↓
Candidate matches

Image B
  ↓
Keypoints
  ↓
Descriptors
```

Illumination variation can change the local image neighborhood used to construct the descriptor.

Potential consequences include:

- increased descriptor distance
- fewer candidate matches
- more ambiguous matches
- lower ratio-test acceptance
- fewer geometrically consistent correspondences

These effects should be measured rather than inferred from visual appearance alone.

---

# 13. Illumination Effects on Candidate Matches

Candidate matches are proposed correspondences.

They are not yet geometrically verified.

The distinction is:

```text
Feature extraction
        ↓
Descriptor matching
        ↓
Candidate matches
        ↓
RANSAC / geometric verification
        ↓
Verified inliers
```

Illumination can affect candidate matching before geometric verification by changing:

- feature repeatability
- descriptor similarity
- match distances
- candidate count
- spatial distribution

A high number of candidate matches does not necessarily indicate good registration.

---

# 14. Illumination Effects on Geometric Verification

RANSAC or another geometric verification method attempts to identify a transformation supported by consistent correspondences.

For illumination-affected imagery:

```text
Candidate matches
       ↓
Some correspond to stable terrain
       ↓
Some correspond to illumination-dependent structures
       ↓
Geometric verification
       ↓
Verified inliers
```

If illumination causes many ambiguous or unstable matches, geometric verification may experience:

- reduced inlier count
- lower inlier ratio
- unstable transformation estimates
- spatially clustered inliers
- increased residuals

The exact effect depends on:

- image pair
- terrain
- scale
- representation
- feature detector
- descriptor
- geometric model
- RANSAC parameters

---

# 15. Illumination Effects on Registration Residuals

Even when a transformation is successfully estimated, illumination differences can affect residual error.

The project recommends inspecting residuals rather than relying only on a visually good overlay. If residual vectors vary systematically across an image, that may indicate that the selected global model is insufficient or that other geometric effects are present.

Relevant metrics include:

- independent checkpoint RMSE
- median error
- P90/P95 error where implemented
- maximum error where appropriate
- inlier count
- inlier ratio
- spatial coverage

For source-image registration, error should first be reported in source-image pixels. Conversion to metres is meaningful only when GSD, projection, and reference information support that interpretation.

---

# 16. Spatial Coverage Under Illumination Stress

Illumination changes may not affect the entire image uniformly.

Some terrain regions may remain highly distinctive while others become ambiguous.

Consequently:

```text
Many inliers
        ≠
Good spatial coverage
```

A method may obtain many correspondences around a single crater while failing elsewhere.

Potential coverage measurements include:

- grid coverage
- convex-hull coverage
- bounding-box coverage
- spatial density
- clustering analysis

The exact implementation should follow the experiment or benchmark definition.

---

# 17. Illumination and Geometric Model Selection

Illumination and geometry should not be conflated.

A poor registration result may arise because:

```text
Illumination difference
```

or:

```text
Scale mismatch
```

or:

```text
Sensor mismatch
```

or:

```text
Incorrect geometric model
```

or a combination of these.

For a local, already map-projected pair, affine or homography models may be useful initial models. However, lunar terrain is not a flat plane, and raw sensor/viewing geometry can introduce additional effects.

Therefore, illumination experiments should keep the geometric protocol controlled whenever possible.

---

# 18. Experimental Principle

Illumination robustness should be investigated experimentally.

The recommended conceptual comparison is:

```text
Same lunar region
       │
       ├── Similar illumination
       │       ↓
       │   Baseline condition
       │
       └── Different illumination
               ↓
           Stress condition
```

Then compare the same pipeline under both conditions.

A useful experiment should hold as many unrelated variables constant as practical.

---

# 19. Recommended Illumination Stress Test

A basic stress test can contain:

| Condition | Description                                            |
| --------- | ------------------------------------------------------ |
| A         | Same region under similar illumination                 |
| B         | Same region under substantially different illumination |

The exact image pairs and illumination measurements are:

```text
Image pairs: [TBD]
Sun-angle metadata: [TBD]
Illumination metadata: [TBD]
```

The project feedback explicitly recommends a Sun-angle stress test using the same region under similar versus very different lighting and reporting the resulting performance drop honestly.

---

# 20. Representation Ablation

A useful first ablation is:

```text
R0 — Raw grayscale
R1 — Gradient representation
```

Optional representations can be added only when actually implemented:

```text
R2 — Edge representation
R3 — Other structural representation
```

The primary comparison should keep the rest of the registration pipeline fixed.

For example:

```text
                  Raw        Gradient
                 ------      --------
Image pair          ✓            ✓
Scale handling      ✓            ✓
SIFT                ✓            ✓
Matcher             ✓            ✓
RANSAC              ✓            ✓
Evaluation points   ✓            ✓
Metrics             ✓            ✓
Representation      R0           R1
```

This makes representation the primary experimental variable.

---

# 21. V1 Connection

Illumination research connects directly to the V1 experimental progression.

The relevant conceptual sequence is:

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

These experiments should be interpreted according to their actual implementation and result status.

The project feedback recommends a build order in which a known pair is established first, followed by scale handling and a structure-focused preprocessing experiment before more advanced matching and refinement.

---

# 22. Relationship to EXP-001

`EXP-001 — SIFT Baseline` provides the conceptual control for later representation experiments.

The baseline should establish:

```text
Raw image
   ↓
SIFT
   ↓
Descriptor matching
   ↓
Filtering
   ↓
RANSAC
   ↓
Transformation
   ↓
Registration evaluation
```

Illumination research can then ask whether an alternative representation changes the measured outcome.

The baseline is important because without a controlled reference, an observed result cannot easily be attributed to the new representation.

---

# 23. Relationship to EXP-002

Scale and illumination are separate variables.

A scale mismatch can make correspondence difficult even if illumination is identical.

Likewise, an illumination difference can make correspondence difficult even when the effective scale is comparable.

Therefore:

```text
Scale handling
      +
Illumination handling
```

should not be treated as one variable.

Where EXP-002's reference pyramid is used, its scale configuration should remain fixed during an illumination representation comparison unless scale is itself being studied.

---

# 24. Relationship to EXP-003

`EXP-003 — Gradient Representation` is the most direct V1 experiment associated with this research note.

Its research motivation is:

> Determine whether a gradient/edge/structure-focused representation changes verified lunar correspondence under appearance and illumination differences compared with raw grayscale.

The experiment should not assume that gradients are illumination invariant.

Instead, it should measure:

- candidate matches
- verified inliers
- inlier ratio
- spatial coverage
- independent checkpoint RMSE
- runtime
- failure behavior

The actual implementation and results should remain authoritative in:

`experiments/v1/preprocessing/EXP-003-gradient-representation/README.md`

---

# 25. Relationship to Geometry Experiments

Illumination-related correspondence errors can propagate into geometric estimation.

Therefore, illumination research should connect to:

- `EXP-004 — Affine vs Homography`
- `EXP-005 — Residual Analysis`

These experiments help determine whether observed registration errors are associated with:

- correspondence quality
- geometric-model limitations
- spatially varying residuals
- terrain-related effects
- other factors

Illumination should not be blamed for a geometric failure without supporting evidence.

---

# 26. Relationship to Sub-Pixel Refinement

Sub-pixel refinement is downstream of reliable correspondence.

The intended conceptual sequence is:

```text
Candidate Matches
       ↓
RANSAC / Geometric Verification
       ↓
Verified Inliers
       ↓
Sub-Pixel Refinement
       ↓
Final Transformation Refit
       ↓
Independent Evaluation
```

Project feedback explicitly recommends refining verified control points and then estimating the final transformation from the refined points.

Illumination research should therefore avoid claiming that sub-pixel refinement solves a correspondence problem caused by incorrect or unstable illumination-dependent matches.

---

# 27. Metrics for Illumination Research

Illumination experiments should use measurable outcomes.

## Candidate Match Count

Measures how many correspondences survive initial matching/filtering.

Useful for diagnosing feature and descriptor behavior.

Not sufficient for determining registration quality.

---

## Verified Inlier Count

Measures how many candidate correspondences are consistent with the estimated geometric model.

Useful for assessing geometric consistency.

---

## Inlier Ratio

A typical definition is:

$$
\text{Inlier Ratio}
=
\frac{N_{\text{inliers}}}
{N_{\text{candidate matches}}}
$$

The exact project implementation should be followed.

---

## Spatial Coverage

Measures whether verified correspondences are distributed across the useful overlap.

Possible approaches:

- grid coverage
- convex hull
- spatial density
- clustering

---

## Independent Checkpoint RMSE

If independent check points are available:

$$
RMSE =
\sqrt{
\frac{1}{N}
\sum_{i=1}^{N}
e_i^2
}
$$

where \(e_i\) is the registration error at an independent evaluation point.

The transformation should not be fitted and evaluated on exactly the same points when independent evaluation is intended.

The project feedback specifically recommends independent check points because fitting and judging on the same points can make registration quality appear better than it actually is.

---

## Runtime

Measure where useful:

- preprocessing time
- representation-generation time
- feature extraction time
- matching time
- geometric verification time
- total runtime

Runtime should not be inferred from algorithm names.

---

# 28. Recommended Illumination Evaluation Matrix

A generic evaluation structure is:

| Representation                  | Similar illumination | Different illumination |
| ------------------------------- | -------------------: | ---------------------: |
| Raw grayscale                   |              `[TBD]` |                `[TBD]` |
| Gradient                        |              `[TBD]` |                `[TBD]` |
| Edge                            |              `[TBD]` |                `[TBD]` |
| Other structural representation |              `[TBD]` |                `[TBD]` |

Possible metrics for each cell:

- candidate matches
- verified inliers
- inlier ratio
- coverage
- checkpoint RMSE
- runtime

No result should be entered until it has actually been measured.

---

# 29. Measuring Performance Drop

An illumination stress test can compare a baseline condition with a harder condition.

For a metric where lower is better, such as RMSE:

$$
\Delta E = E_{\text{different illumination}} - E_{\text{similar illumination}}
$$

For a metric where higher is generally better, such as inlier ratio:

$$
\Delta R =
R_{\text{different illumination}}
-
R_{\text{similar illumination}}
$$

The interpretation must account for the metric direction.

A "performance drop" should therefore be calculated from actual measurements rather than described qualitatively.

---

# 30. Avoiding Confounded Experiments

An illumination experiment becomes difficult to interpret if several variables change simultaneously.

For example:

```text
Raw grayscale
    + different illumination
    + different sensor
    + different scale
    + different matcher
    + different geometric model
```

cannot cleanly establish that illumination caused the observed change.

Where practical, control:

- image pair
- source/reference sensor
- scale
- preprocessing
- feature detector
- descriptor
- matcher
- geometric model
- RANSAC parameters
- evaluation points
- random seed
- software environment

The primary independent variable should be the illumination condition or representation being investigated.

---

# 31. Sensor Interaction

Illumination robustness may differ by sensor.

The project identifies:

- OHRC as high-resolution visible panchromatic imagery
- TMC-2 as panchromatic terrain imagery at approximately meter-scale resolution
- IIRS as imaging infrared hyperspectral data with substantially coarser spatial resolution and multiple spectral bands

The exact product metadata should remain authoritative for any numerical value used in an experiment.

Illumination research should therefore avoid assuming that one representation behaves identically across all sensors.

---

# 32. IIRS and Illumination Research

IIRS introduces both spectral and spatial differences.

A full hyperspectral cube should not automatically be treated as a conventional 2D image for SIFT-style matching.

The project feedback recommends first investigating simple 2D representations such as:

- selected band
- PCA/composite
- structural map

before considering more complex approaches.

If illumination research includes IIRS, the exact representation conversion must be recorded:

```text
IIRS input
    ↓
2D representation
    ↓
Optional normalization
    ↓
Feature extraction
    ↓
Matching
```

The actual representation used is:

```text
IIRS representation: [TBD]
```

until confirmed by implementation.

---

# 33. Illumination and Cross-Sensor Matching

Cross-sensor correspondence combines several difficulties:

```text
Different sensor
       +
Different spatial scale
       +
Different spectral response
       +
Different illumination
       ↓
Strong appearance difference
```

A method that appears robust to illumination within one sensor should not automatically be described as robust to cross-sensor illumination differences.

Cross-sensor experiments should therefore be reported separately where practical.

The project guidance recommends separate sensor results rather than collapsing different sensors into a single mixed average.

---

# 34. What Illumination Experiments Can Establish

A controlled illumination experiment can potentially establish that:

- one representation produces more candidate matches under the tested condition
- one representation produces more verified inliers
- inlier ratios change under illumination stress
- spatial coverage changes
- checkpoint error changes
- runtime changes
- a particular failure mode occurs more frequently
- a representation performs differently under similar and different illumination

These are empirical findings tied to the tested conditions.

---

# 35. What Illumination Experiments Cannot Automatically Establish

A single experiment cannot automatically prove that:

- the representation is universally illumination invariant
- the method works for all lunar terrain
- the method works across every sensor
- the method is robust to every Sun angle
- shadows no longer matter
- brightness normalization solves illumination
- gradient representations always outperform grayscale
- the method generalizes to unseen sensors
- the method guarantees sub-pixel accuracy

Such claims require broader evidence.

---

# 36. Important Failure Modes

Illumination-related correspondence can fail in several ways.

## 36.1 Shadow Displacement

A shadow moves between images and creates apparently different structures.

```text
Same crater
    ↓
Different Sun angle
    ↓
Different shadow position
```

---

## 36.2 Shadow Polarity or Appearance Changes

A feature that appears dark in one condition may have a substantially different appearance under another illumination condition.

---

## 36.3 Weak Gradients

Some terrain may have insufficient local intensity change to produce stable gradient-based features.

---

## 36.4 Noise Amplification

Derivative-based representations can emphasize high-frequency noise.

---

## 36.5 Edge Fragmentation

An edge detector may produce discontinuous boundaries rather than stable terrain structures.

---

## 36.6 Irrelevant Boundaries

Strong edges can correspond to illumination boundaries or other image structures rather than stable terrain geometry.

---

## 36.7 Low-Feature Terrain

Smooth or repetitive terrain can produce insufficient distinctive correspondence.

---

## 36.8 Spatially Clustered Matches

An algorithm may find many matches in a small region while failing across the rest of the overlap.

---

## 36.9 Interaction With Scale

A feature visible at one scale may be absent or poorly represented at another.

---

## 36.10 Interaction With Sensor Modality

A terrain structure visible in one sensor may not appear with the same radiometric or spectral characteristics in another.

---

# 37. Failure Analysis Template

When an illumination-related failure is observed, document it using:

```markdown
## Failure Case

### Condition

[Image pair, sensor, illumination condition]

### Observed Behavior

[What happened]

### Evidence

[Metrics, visualization, residuals, or match distribution]

### Suspected Cause

[Evidence-based interpretation]

### Impact

[Effect on correspondence or registration]

### Reproduction

[How the failure can be reproduced]

### Follow-Up

[Potential experiment or mitigation]
```

Avoid labeling a failure as illumination-induced unless the available evidence supports that interpretation.

---

# 38. Visualization Strategy

Illumination research benefits from visual inspection.

Useful visualizations include:

### Raw Image Pair

```text
Source raw image
Reference raw image
```

### Representation Pair

```text
Source gradient
Reference gradient
```

### Candidate Matches

Display candidate correspondences before geometric verification.

### Verified Inliers

Display only RANSAC/geometrically verified correspondences.

### Spatial Coverage

Display the locations of verified points across the overlap.

### Registration Overlay

Display the resulting alignment.

### Residual Vectors

Show error direction and magnitude across the image.

### Illumination Metadata

Where available, record:

- Sun geometry
- viewing geometry
- acquisition information

Exact metadata availability:

```text
Illumination metadata: [TBD]
```

---

# 39. Residual Analysis Under Illumination Stress

Residuals can help distinguish between different failure mechanisms.

For example:

```text
Random residuals
    ↓
May indicate local matching uncertainty

Systematic residual trend
    ↓
May indicate geometric-model mismatch

Localized residual concentration
    ↓
May indicate problematic terrain / shadow / feature region

Large residuals near illumination-dependent structures
    ↓
Potential illumination-related correspondence instability
```

These interpretations are hypotheses until supported by the actual data.

Residual analysis should therefore be combined with visual and quantitative evidence.

---

# 40. Ground Error

Ground error should be used only when the necessary geospatial information makes the conversion meaningful.

For source-image error:

```text
Error = source-image pixels
```

A physical conversion may be represented conceptually as:

$$
E_{\text{ground}} \approx E_{\text{pixel}} \times GSD
$$

but this simplification should not be used blindly when projection, geometry, or local scale effects make it inappropriate.

The project guidance specifically recommends reporting source-image pixel error first and converting to metres only when GSD and projection/reference information support that conversion.

---

# 41. Illumination Metadata

Whenever available, research should preserve relevant illumination information.

Potential metadata includes:

- acquisition time
- Sun elevation
- Sun azimuth
- incidence geometry
- viewing geometry
- sensor orientation
- image product metadata

Exact availability depends on the source product.

Use:

```text
Sun-angle metadata: [TBD]
```

rather than reconstructing values without a documented source.

---

# 42. Synthetic Illumination Augmentation

Synthetic augmentation may be useful for controlled research.

Potential augmentation variables include:

- brightness
- contrast
- intensity scaling
- intensity offset
- simulated appearance changes

The project problem statement materials also identify synthetic lunar augmentations involving Sun angle, rotation, scale, and contrast as a potential dataset/training resource.

However, synthetic photometric changes should not automatically be treated as physically equivalent to real Sun-angle changes.

In particular:

```text
Synthetic brightness change
        ≠
Real shadow-geometry change
```

Synthetic experiments can complement real illumination stress tests but should not silently replace them.

---

# 43. Real vs Synthetic Illumination Stress

| Property           | Synthetic photometric change                             | Real illumination change                       |
| ------------------ | -------------------------------------------------------- | ---------------------------------------------- |
| Brightness         | Can be controlled                                        | Naturally varies                               |
| Contrast           | Can be controlled                                        | Naturally varies                               |
| Shadow geometry    | Usually not physically recreated by simple normalization | Can change physically                          |
| Terrain visibility | Limited by transformation                                | Can genuinely change                           |
| Reproducibility    | High                                                     | Depends on available imagery                   |
| Physical realism   | Depends on model                                         | Based on actual acquisition                    |
| Research role      | Controlled auxiliary test                                | Primary real-world stress case where available |

The two should be reported separately.

---

# 44. Photometric Correction as an Experiment

If illumination geometry metadata is available, a photometric correction can be tested as its own variable.

For example:

```text
Raw grayscale
        ↓
Matching

Photometrically corrected image
        ↓
Matching
```

The correction should be documented precisely.

Important parameters include:

- correction model
- input metadata
- normalization range
- clipping
- local/global operation
- implementation
- source/reference application

If not implemented:

```text
Photometric correction: [Not implemented]
```

---

# 45. Representation Normalization

Gradient or edge representations may require their own normalization.

Possible operations include:

- min-max normalization
- percentile normalization
- standardization
- local normalization
- no normalization

The actual implementation should be recorded.

Example:

| Operation               | Method  | Parameters | Applied to       |
| ----------------------- | ------- | ---------- | ---------------- |
| Gradient normalization  | `[TBD]` | `[TBD]`    | Source/reference |
| Intensity normalization | `[TBD]` | `[TBD]`    | Source/reference |

Normalization should be considered another experimental variable when it can materially affect the result.

---

# 46. Experimental Controls

A rigorous illumination experiment should define:

### Independent variable

Potentially:

- illumination condition
- representation
- photometric correction

### Controlled variables

Where practical:

- image region
- sensor
- scale
- preprocessing
- SIFT configuration
- matcher
- RANSAC model
- RANSAC threshold
- evaluation points
- random seed
- hardware/software environment

### Dependent variables

- candidate matches
- inliers
- inlier ratio
- spatial coverage
- checkpoint RMSE
- runtime
- success/failure

---

# 47. Recommended Experimental Question

A strong V1 question is:

> **Does a structure-focused image representation improve verified correspondence and registration accuracy for the same lunar region when illumination conditions differ, compared with raw grayscale under otherwise controlled conditions?**

This question is preferable to:

> "Are gradients illumination invariant?"

because the former defines a measurable comparison without assuming the conclusion.

---

# 48. Recommended Hypothesis

A testable hypothesis is:

> **A gradient or structure-focused representation may preserve correspondence-relevant terrain information more effectively than raw grayscale under the tested illumination differences.**

This remains a hypothesis until supported by measurements.

---

# 49. Evidence Required for a Research Conclusion

A meaningful illumination conclusion should ideally include:

1. defined image pairs
2. known or documented illumination conditions
3. controlled preprocessing
4. a baseline representation
5. an experimental representation
6. controlled matching configuration
7. geometric verification
8. independent evaluation where available
9. spatial coverage
10. quantitative metrics
11. failure analysis
12. limitations

A visual overlay alone is insufficient evidence for illumination robustness.

---

# 50. Reproducibility Record

For each illumination experiment, record:

| Parameter              | Value   |
| ---------------------- | ------- |
| Research question      | `[TBD]` |
| Experiment ID          | `[TBD]` |
| Source image           | `[TBD]` |
| Reference image        | `[TBD]` |
| Source sensor          | `[TBD]` |
| Reference sensor       | `[TBD]` |
| Source GSD             | `[TBD]` |
| Reference GSD          | `[TBD]` |
| Illumination condition | `[TBD]` |
| Sun geometry           | `[TBD]` |
| Representation         | `[TBD]` |
| Gradient operator      | `[TBD]` |
| Normalization          | `[TBD]` |
| SIFT configuration     | `[TBD]` |
| Matcher                | `[TBD]` |
| RANSAC model           | `[TBD]` |
| RANSAC threshold       | `[TBD]` |
| Random seed            | `[TBD]` |
| Evaluation points      | `[TBD]` |
| Checkpoint RMSE        | `[TBD]` |
| Inlier ratio           | `[TBD]` |
| Spatial coverage       | `[TBD]` |
| Runtime                | `[TBD]` |
| Git commit             | `[TBD]` |
| Dataset version        | `[TBD]` |
| Environment            | `[TBD]` |

---

# 51. Current Evidence Status

This research note defines the scientific problem and experimental reasoning.

It does **not** establish that ChandraMap currently has an illumination-invariant representation.

Current project-level evidence for quantitative illumination robustness is:

```text
Measured illumination robustness: [TBD]
Validated representation: [TBD]
Validated Sun-angle range: [TBD]
Validated sensor range: [TBD]
Validated terrain range: [TBD]
Independent benchmark evidence: [TBD]
```

The corresponding V1 experiments should provide the evidence needed to populate these fields.

---

# 52. Important Scientific Limitations

Illumination robustness is inherently limited by the information present in the imagery.

A representation cannot reliably recover information that is absent from the source image.

Important limitations include:

### Shadow Occlusion

A terrain region hidden by a shadow in one image may not provide direct correspondence evidence.

### Feature Visibility

A terrain feature may become weak or disappear under different illumination.

### Gradient Instability

Gradients can change with illumination and contrast.

### Edge Ambiguity

An edge may represent a terrain boundary, shadow boundary, or image artifact.

### Sensor Differences

A representation that works for visible imagery may behave differently for infrared or hyperspectral data.

### Scale Differences

A terrain feature may not be resolved at the same spatial scale in both images.

### Geometric Differences

Illumination is only one component of the registration problem.

### Limited Ground Truth

A conclusion is constrained by the quality and distribution of available evaluation points.

---

# 53. Open Research Questions

The following questions remain suitable for future investigation.

## Q1 — How much does real Sun-angle variation degrade SIFT correspondence?

```text
Similar illumination
        vs
Different illumination
```

Measure:

- candidate matches
- inliers
- inlier ratio
- coverage
- checkpoint RMSE

---

## Q2 — Does gradient representation reduce the degradation?

Compare:

```text
Raw grayscale
        vs
Gradient representation
```

under the same image pairs and matching configuration.

---

## Q3 — Which structural representation is most useful?

Potential candidates:

- gradient magnitude
- gradient orientation
- edge representation
- phase-based representation
- other implemented structural representations

The result should be based on measured evidence.

---

## Q4 — Are improvements consistent across sensors?

Test separately for:

- OHRC
- TMC-2
- IIRS-derived 2D representations

Do not assume one result transfers across all sensor types.

---

## Q5 — Does illumination affect spatial coverage?

A method may retain enough matches for RANSAC while losing coverage in specific terrain regions.

This should be measured explicitly.

---

## Q6 — Which terrain features remain stable?

Investigate whether:

- crater rims
- ridge lines
- terrain boundaries
- local gradients
- other structures

remain useful under the tested illumination changes.

---

## Q7 — Can residual patterns identify illumination-dependent mismatches?

Compare residual vectors against:

- shadow boundaries
- terrain structure
- illumination condition
- image position

---

## Q8 — Does photometric correction help before structural matching?

Test:

```text
Raw
Raw + photometric correction
Gradient
Gradient + photometric correction
```

only when the corresponding implementations are available.

---

## Q9 — How much illumination variation can the baseline tolerate?

Determine the empirical operating range rather than assuming a universal threshold.

---

## Q10 — Can illumination metadata improve routing?

If Sun/viewing geometry is available, investigate whether it can be used to:

- classify easy/hard pairs
- select preprocessing
- prioritize structural representations
- predict failure risk

This remains `[Planned]` unless implemented.

---

# 54. Research-to-Experiment Checklist

Before creating an illumination experiment:

- [ ] Define the research question.
- [ ] Define the hypothesis.
- [ ] Identify the image pair.
- [ ] Confirm source/reference sensors.
- [ ] Record GSD and scale information.
- [ ] Record illumination metadata if available.
- [ ] Define the baseline.
- [ ] Define the experimental representation.
- [ ] Fix unrelated preprocessing.
- [ ] Fix matching configuration.
- [ ] Fix geometric verification.
- [ ] Define independent evaluation.
- [ ] Define spatial coverage.
- [ ] Define expected metrics.
- [ ] Define failure criteria.
- [ ] Define reproducibility metadata.

---

# 55. Research Interpretation Rules

When interpreting an illumination experiment:

### Rule 1 — Do not equate more matches with better registration

A large number of incorrect or clustered matches is not sufficient.

### Rule 2 — Do not equate normalization with illumination invariance

Normalization addresses image intensity, not necessarily shadow geometry.

### Rule 3 — Do not equate gradients with invariance

Gradients can still change under different illumination.

### Rule 4 — Do not generalize from one image pair

A single successful pair demonstrates a result for that condition, not universal robustness.

### Rule 5 — Do not hide failures

Failures provide information about the operating limits of the method.

### Rule 6 — Do not use fitting error as independent accuracy

Use independent check points where available.

### Rule 7 — Do not convert pixel error to metres without valid scale/projection context

Physical interpretation requires appropriate metadata.

### Rule 8 — Do not confuse geometric-model failure with illumination failure

Use residual analysis and controlled comparisons.

---

# 56. Example Interpretation

A scientifically appropriate result might look like:

> Under the evaluated image pair, the gradient representation produced a higher verified inlier ratio than raw grayscale under the tested illumination difference. Independent checkpoint RMSE changed from `[A]` pixels to `[B]` pixels, while spatial coverage changed from `[C]` to `[D]`. This provides evidence that the tested representation may improve correspondence for this illumination condition. The result does not establish general illumination invariance across other Sun angles, sensors, or terrain types.

This is substantially different from:

> Gradient representation solves illumination variation.

The first statement is bounded by the evidence.

---

# 57. Relationship to the Benchmark

Illumination research should eventually contribute to a standardized benchmark condition if the project determines that illumination stress belongs in the official V1 evaluation.

A potential benchmark structure is:

```text
Benchmark
├── Similar-illumination cases
├── Sun-angle stress cases
├── Scale stress cases
├── Modality stress cases
└── Other defined stress cases
```

The exact official benchmark composition is governed by `benchmarks/`, not this research note.

Research may propose a benchmark condition.

The benchmark documentation must establish the final standardized protocol.

---

# 58. Expected Research Outputs

A completed illumination investigation should ideally produce:

```text
Research Question
        ↓
Defined Image Pairs
        ↓
Baseline Result
        ↓
Illumination-Stress Result
        ↓
Representation Comparison
        ↓
Quantitative Metrics
        ↓
Spatial Coverage
        ↓
Residual Analysis
        ↓
Failure Cases
        ↓
Research Finding
        ↓
Follow-Up Experiment
```

Useful artifacts may include:

- raw image pairs
- normalized images
- gradient images
- edge images
- candidate match plots
- verified inlier plots
- registration overlays
- residual visualizations
- metrics tables
- configuration
- logs
- experiment README
- research finding

Exact artifact locations should follow the repository's current result and experiment structure.

---

# 59. Current Research Conclusion

At the current documentation stage, the scientifically supported conclusion is:

> **Lunar illumination variation should be treated as a correspondence and registration stress factor rather than as a simple brightness difference.**

Changing Sun geometry can alter shadows and local image structure, so photometric normalization alone cannot be assumed to restore correspondence. Project technical feedback therefore recommends directly testing raw grayscale against gradient, edge, or other structure-focused representations using a controlled Sun-angle stress test.

The project has **not**, based on the evidence documented here, established that any particular representation is universally illumination invariant.

The quantitative conclusion remains:

```text
Illumination robustness of ChandraMap: [TBD]
```

---

# 60. Summary

The key research model is:

```text
Lunar Terrain
      │
      ▼
Sun / Illumination Geometry
      │
      ▼
Observed Image
      │
      ├── brightness
      ├── contrast
      ├── gradients
      ├── edges
      ├── shadows
      └── visible structure
      │
      ▼
Image Representation
      │
      ▼
Feature Detection / Description
      │
      ▼
Candidate Correspondences
      │
      ▼
Geometric Verification
      │
      ▼
Verified Inliers
      │
      ▼
Registration
      │
      ▼
Independent Evaluation
      │
      ├── RMSE
      ├── inlier ratio
      ├── spatial coverage
      ├── ground error where meaningful
      └── runtime
```

The central scientific question is therefore not:

> **"Can ChandraMap make images illumination invariant?"**

but:

> **"Which representations and processing strategies preserve enough stable lunar terrain information to maintain reliable correspondence under the illumination differences encountered by ChandraMap?"**

That question can be answered only through controlled experiments, quantitative evaluation, failure analysis, and reproducible evidence.

---

## Related ChandraMap Documentation

- [Research directory](../README.md)
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

## Source Materials

This note is grounded in the project materials provided for ChandraMap, including the SIH 26166 problem material and technical feedback concerning lunar illumination, sensor-aware preprocessing, scale handling, geometric verification, and measurable evaluation.

The technical feedback specifically emphasizes:

- treating Sun-angle variation as more than brightness variation,
- comparing raw grayscale with structural representations,
- using the same region under similar and substantially different illumination,
- reporting measurable performance changes,
- preserving sensor-specific handling,
- evaluating inlier statistics, spatial coverage, and independent registration error,
- and avoiding unsupported claims of illumination invariance.

Where the current implementation, dataset, or experiment results are not established by the available project material, this note intentionally uses `[TBD]`, `[Not implemented]`, or `[Planned]` rather than inventing project facts.
