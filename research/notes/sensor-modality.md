# Sensor Modality in Lunar Image Correspondence and Registration

> **Research Note:** `research/notes/sensor-modality.md`
> **Project:** ChandraMap
> **Problem Context:** SIH 26166 — Lunar Image Correspondence
> **Status:** Foundational research note
> **Scope:** Sensor modality, cross-sensor correspondence, registration, evaluation, and V1 research implications

---

## 1. Purpose

This note explains how differences between lunar imaging sensors affect image correspondence and registration in ChandraMap.

ChandraMap is concerned with establishing reliable correspondences between images representing the same lunar region and using those correspondences to estimate a meaningful geometric relationship. The images may come from different instruments, different spatial scales, different illumination conditions, and different viewing geometries.

Sensor modality is therefore not a cosmetic property of an input image. It can change what terrain structures are visible, how those structures appear, which features are repeatable, how descriptors behave, and whether a correspondence remains geometrically useful.

The central principle of this note is:

> **The same lunar surface is the physical scene; each sensor produces a different measurement of that scene.**

Consequently, cross-sensor registration should not assume that two images of the same terrain will have similar pixel intensities or identical local appearance.

The project feedback specifically emphasizes that OHRC, TMC-2, and IIRS should not be treated as identical image sources and that sensor-aware preprocessing should precede a common structural matching stage.

---

## 2. What This Note Covers

This note focuses on:

- sensor modality
- same-sensor versus cross-sensor registration
- spatial resolution and effective scale
- spectral response
- radiometric response
- image formation
- sensor-specific preprocessing
- illumination and Sun-angle interactions
- viewing geometry
- feature detection
- descriptors and matching
- geometric verification
- transformation estimation
- residual analysis
- sub-pixel refinement
- accuracy measurement
- modality stress testing
- OHRC, TMC-2, and IIRS
- V1 experiment relevance
- scientific limitations
- future research directions

It does **not** define:

- a complete benchmark specification
- an API
- a final production architecture
- a literature review
- a claim that cross-sensor registration has already been solved
- final performance numbers
- final acceptance thresholds

Where the current project materials do not establish a value or implementation decision, it is marked as `[TBD]`, `[Not provided]`, `[To be verified]`, `[Planned]`, or `[Not implemented]`.

---

# 3. ChandraMap Problem Context

## 3.1 The physical scene versus the measured image

A lunar surface contains physical structures such as:

- crater rims
- crater floors
- ridges
- valleys
- boulders
- slopes
- terrain boundaries
- albedo variations
- shadow boundaries
- other persistent geological structures

A sensor does not directly observe an abstract "feature."

Instead, the sensor measures the scene according to its own:

- spectral sensitivity
- spatial sampling
- radiometric response
- imaging characteristics
- viewing geometry
- acquisition conditions
- product-generation pipeline

A useful conceptual model is:

```text
                Physical Lunar Surface
                         │
          ┌──────────────┴──────────────┐
          │                             │
       Sensor A                      Sensor B
          │                             │
   Sensor-specific                Sensor-specific
   image formation                image formation
          │                             │
   spectral response              spectral response
   spatial sampling               spatial sampling
   radiometry                     radiometry
   noise / blur                   noise / blur
   viewing geometry               viewing geometry
          │                             │
       Image A                       Image B
          │                             │
          └──────────────┬──────────────┘
                         │
                  Correspondence
                         │
                  Geometric Model
                         │
                    Registration
```

The registration problem is therefore not simply:

> "Find pixels that look alike."

It is closer to:

> "Find observations in two sensor-specific representations that correspond to the same underlying lunar structures and are consistent with a physically and geometrically meaningful relationship."

---

# 4. What Sensor Modality Means in ChandraMap

## 4.1 Definition

In ChandraMap, **sensor modality** refers to the characteristics of an imaging system and its measurement process that influence how the same lunar surface is represented in an image.

Relevant dimensions include:

| Dimension               | Meaning for registration                                                          |
| ----------------------- | --------------------------------------------------------------------------------- |
| Spectral sensitivity    | Which wavelengths or spectral ranges contribute to the observation                |
| Spatial sampling        | How finely the surface is sampled spatially                                       |
| Radiometric response    | How measured signal values relate to observed scene properties                    |
| Imaging mechanism       | How the instrument forms the image or data product                                |
| Noise characteristics   | How measurement uncertainty appears in the data                                   |
| Blur / spatial response | How fine structures are preserved or attenuated                                   |
| Viewing geometry        | Relationship between sensor, surface, and observation direction                   |
| Acquisition conditions  | Conditions under which the image was acquired                                     |
| Product generation      | Calibration, projection, resampling, and other processing applied before delivery |

These dimensions are related but should not be collapsed into one variable.

For example:

- a spatial-resolution difference is not automatically a modality difference;
- a Sun-angle difference is not automatically a sensor difference;
- a projection difference is not automatically a spectral difference;
- a radiometric difference is not equivalent to a geometric difference.

A real cross-sensor pair can contain several of these differences simultaneously.

---

# 5. Why Cross-Sensor Registration Is Different

## 5.1 Same-sensor registration

In same-sensor registration, source and reference images are produced by the same or closely related imaging system.

This can provide greater consistency in:

- spectral response
- spatial sampling
- image formation
- radiometric behavior
- characteristic blur
- noise structure
- feature appearance

However, same-sensor registration is **not automatically easy**.

It may still involve:

- different Sun angles
- different viewing geometry
- scale differences
- temporal changes
- different processing
- terrain relief
- shadows
- image noise
- partial overlap

The advantage is that some sensor-specific differences are reduced.

---

## 5.2 Cross-sensor registration

In cross-sensor registration, source and reference images originate from different instruments.

Potential differences include:

- spatial resolution
- spectral response
- radiometric response
- image contrast
- noise
- spatial sampling
- optical response
- viewing geometry
- preprocessing
- map projection
- image representation

Thus, a feature that is visually obvious in one sensor may:

- appear weaker,
- appear broader,
- disappear,
- be represented differently,
- shift in apparent intensity,
- or require a different representation to become useful.

Cross-sensor registration should therefore be treated as a distinct research condition rather than merely a harder version of ordinary image matching.

---

# 6. Sensor Modality Is Not the Same as Spatial Scale

One of the most important distinctions in ChandraMap is:

> **Different spatial resolution does not fully explain different sensor behavior.**

Consider two images that are resampled to the same pixel dimensions and nominal pixel spacing.

They may still differ because the sensors measure different properties of the lunar surface.

```text
Physical lunar surface
        │
        ├───────────────┐
        │               │
     Sensor A        Sensor B
        │               │
 Spectral response  Spectral response
 Spatial response   Spatial response
 Radiometry         Radiometry
 Noise              Noise
        │               │
     Image A          Image B
        │               │
        └──── same nominal scale ────┘
                       │
                 Still different
                 measurements
```

Therefore:

```text
Same pixel size ≠ same information
```

and:

```text
Same image dimensions ≠ same spatial information
```

A common pixel grid can make data easier to compare, but it does not make the underlying sensors equivalent.

---

# 7. Spatial Resolution and Effective Scale

## 7.1 Why scale matters

Different sensors can observe the same terrain at substantially different spatial scales.

The project feedback identifies this as a major issue for the Chandrayaan-2 sensor set. The provided materials describe OHRC as very high-detail visible panchromatic imagery, TMC-2 as substantially coarser panchromatic terrain imagery, and IIRS as a much coarser imaging IR hyperspectral modality. Exact product pixel scale must be taken from the relevant challenge or product metadata rather than hard-coded from a generic description.

The consequence is that a small crater or ridge may:

- occupy many pixels in one sensor,
- occupy only a few pixels in another,
- or be below the useful spatial resolution of another observation.

---

## 7.2 Pixel resizing does not recover detail

Upsampling a coarse image creates additional pixels but does not create new spatial information.

Conceptually:

```text
Coarse observation
      │
      │ Upsampling
      ▼
More pixels
      │
      └── No recovery of missing terrain detail
```

Therefore, ChandraMap should not interpret an upsampled IIRS or other coarse observation as containing the same fine-scale information as a naturally high-resolution image.

The project feedback explicitly warns against enlarging coarse IIRS imagery to OHRC/NAC-like resolution and interpreting the result as recovered detail.

---

## 7.3 Comparable effective scale

For cross-sensor matching, the high-resolution image can instead be represented at a coarser effective scale.

A conceptual strategy is:

```text
High-resolution reference
          │
          ▼
Reference pyramid
          │
          ├── Fine scale
          ├── Medium scale
          └── Coarse scale
                         │
                         ▼
             Compare at appropriate scale
                         │
                         ▼
                   Local refinement
```

This is consistent with the project's multi-scale direction.

The important principle is:

> **Compare information at physically meaningful scales before asking the matcher to solve fine alignment.**

---

# 8. Spectral Characteristics

## 8.1 Why spectral response matters

A sensor measures a particular portion of the electromagnetic spectrum.

Different spectral sensitivity means that the same terrain can produce different image structures.

A surface feature that has strong contrast in one spectral range may have weaker contrast in another.

Therefore:

```text
Same terrain
     │
     ├── spectral response A → appearance A
     │
     └── spectral response B → appearance B
```

A conventional intensity-based matcher may therefore encounter substantial appearance changes even when the physical geometry is unchanged.

---

## 8.2 Spectral differences are not merely brightness differences

A simple brightness transformation assumes something approximately like:

$$
I_B(x,y) \approx aI_A(x,y)+b
$$

where \(a\) and \(b\) account for contrast and offset.

Cross-sensor observations may violate this assumption because different spectral responses can alter the relative appearance of structures.

Thus:

$$
\text{radiometric normalization}
\neq
\text{spectral harmonization}
$$

and:

$$
\text{brightness similarity}
\neq
\text{structural correspondence}
$$

This is one reason structural representations such as gradients, edges, or other modality-robust representations may be worth evaluating.

---

# 9. Radiometric Characteristics

Radiometric differences affect:

- intensity range
- local contrast
- dynamic range
- noise
- saturation
- local texture
- apparent feature strength

Two corresponding terrain regions may therefore have different pixel-value distributions.

This can affect:

- keypoint detection
- descriptor construction
- descriptor distance
- candidate-match ranking
- patch correlation
- sub-pixel refinement

A useful distinction is:

```text
Geometric similarity
        │
        └── Same physical structure / location

Appearance similarity
        │
        └── Similar measured image values
```

Cross-sensor registration may preserve the first while substantially changing the second.

---

# 10. OHRC, TMC-2, and IIRS in ChandraMap

The project specifically identifies Chandrayaan-2 OHRC, TMC-2, and IIRS as relevant inputs. The feedback recommends separate sensor-aware routes rather than forcing all three into one identical preprocessing pipeline.

## 10.1 Sensor overview

| Sensor    | Project-relevant characterization                                               | Registration implication                                                                  |
| --------- | ------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------- |
| **OHRC**  | High-detail visible panchromatic lunar imagery                                  | Supports fine terrain correspondence when the supplied product contains sufficient detail |
| **TMC-2** | Panchromatic terrain imagery at substantially coarser spatial scale than OHRC   | Useful for structural terrain correspondence and scale-bridging                           |
| **IIRS**  | Imaging IR hyperspectral modality with substantially coarser spatial resolution | Requires explicit 2D representation design before conventional image matching             |

The exact product-specific pixel scale, projection, footprint, and metadata should come from the supplied challenge/product data. Where conflicting generic values exist, the challenge product metadata is the appropriate authority.

---

# 11. OHRC as a Registration Modality

OHRC is relevant to ChandraMap as a high-detail visible panchromatic imaging source.

Its comparatively fine spatial detail can support:

- crater-rim correspondence
- ridge correspondence
- fine terrain structures
- high-resolution local registration

However, high spatial resolution does not eliminate:

- Sun-angle effects
- shadow differences
- viewpoint effects
- geometric distortion
- correspondence ambiguity
- radiometric differences

The project feedback explicitly cautions against assuming that high resolution automatically makes matching easier. Sensor modality and illumination can be as important as nominal resolution.

---

# 12. TMC-2 as a Registration Modality

TMC-2 provides a substantially coarser panchromatic representation than OHRC.

This makes it useful for studying:

- scale differences
- terrain-structure correspondence
- coarse-to-fine matching
- registration between substantially different spatial scales

Its coarser sampling means that fine structures visible in OHRC may not be represented equivalently.

Consequently, matching should prioritize structures that are actually supported by the TMC-2 observation.

The project feedback also notes that TMC-2 supports terrain mapping and that map-projection or DEM information can help where available.

---

# 13. IIRS Requires Special Treatment

## 13.1 Why IIRS is different

IIRS should not be treated simply as another grayscale camera image.

The project materials characterize IIRS as an imaging IR hyperspectral modality. The supplied feedback recommends treating it as spectral data and testing a registration-friendly 2D representation before applying conventional local matching.

A hyperspectral observation can be conceptualized as:

$$
I(x,y,\lambda)
$$

rather than simply:

$$
I(x,y)
$$

where \(\lambda\) represents wavelength or spectral channel.

A conventional 2D feature matcher generally expects a representation such as:

$$
I(x,y)
$$

Therefore, an explicit representation step is required.

---

## 13.2 Candidate IIRS representations

The project feedback identifies several sensible starting points:

- selected spectral band
- PCA representation
- spectral composite
- structural map

These should be treated as experimental representations rather than assumed solutions.

Conceptually:

```text
IIRS hyperspectral data
        │
        ├── Selected band
        │
        ├── PCA / dimensionality reduction
        │
        ├── Spectral composite
        │
        └── Structural representation
                    │
                    ▼
             2D registration image
                    │
                    ▼
             Local correspondence
```

The research question is not:

> "Which representation looks most like a normal grayscale image?"

It is:

> "Which representation preserves stable terrain structure that can support reliable correspondence with the reference modality?"

---

## 13.3 What should not be assumed

The project should not assume that:

- the entire hyperspectral cube can be passed directly into a conventional 2D matcher;
- one spectral band is universally optimal;
- PCA automatically preserves the most useful registration structures;
- spectral similarity implies geometric correspondence;
- coarse IIRS data can support arbitrary fine-scale registration.

The feedback explicitly recommends starting with a simple IIRS representation before developing a more complicated hyperspectral matching system.

---

# 14. Image Representation Is a Separate Design Layer

A key ChandraMap concept is:

> **Sensor-specific preparation should happen before common correspondence logic.**

The system can therefore be conceptualized as:

```text
OHRC ──► Sensor-specific preparation ──┐
                                       │
TMC-2 ─► Sensor-specific preparation ──┼──► Common structural representation
                                       │
IIRS ──► Spectral-to-2D preparation ───┘
                                                    │
                                                    ▼
                                             Feature / Matching
                                                    │
                                                    ▼
                                             Geometric Verification
```

This does not mean that all sensors must eventually become identical.

It means that the matching system should explicitly document what representation is being matched.

---

# 15. Sensor Modality and Feature Detection

Feature detection identifies locations that appear sufficiently distinctive or repeatable for correspondence.

Typical feature properties include:

- local contrast
- gradients
- corners
- blobs
- edges
- scale
- orientation
- local structural patterns

A sensor difference can change all of these.

For example:

```text
Physical crater rim
       │
       ├── high-resolution visible sensor
       │       └── strong edge structure
       │
       └── coarse / different spectral sensor
               └── weaker or differently shaped structure
```

A detector may therefore produce:

- different keypoint locations
- different keypoint densities
- different keypoint scales
- different orientations
- different spatial distributions

This means a failure to obtain correspondences does not necessarily imply that the terrain has no corresponding structure.

It may mean that the chosen representation or detector is poorly suited to the sensor pair.

---

# 16. SIFT as a V1 Baseline

The current V1 research sequence uses SIFT as the initial baseline.

The project feedback describes SIFT as a simple and explainable baseline that can provide a measurable reference, while also noting that it may struggle under strong modality and illumination changes.

The correct interpretation is:

> SIFT is a baseline against which sensor-aware improvements can be measured, not evidence that SIFT is sufficient for all cross-sensor cases.

This distinction is important for scientific evaluation.

A useful progression is:

```text
SIFT baseline
     │
     ▼
Measure difficult cases
     │
     ▼
Identify modality / scale failures
     │
     ▼
Test sensor-aware representation
     │
     ▼
Compare under identical test pairs
```

---

# 17. Descriptors Under Modality Change

A descriptor attempts to represent local image structure in a way that allows corresponding regions to be compared.

For same-sensor imagery, corresponding patches may have relatively similar local appearance.

For cross-sensor imagery:

$$
D_A(p_A) \not\approx D_B(p_B)
$$

even when:

$$
p_A \leftrightarrow p_B
$$

represents the same physical terrain structure.

Therefore, descriptor matching must be interpreted as a search for **corresponding structure**, not necessarily identical pixel appearance.

---

# 18. Structural Representations

When intensity becomes unreliable, structure may provide a more stable basis for matching.

Potential representations include:

- gradients
- edges
- local orientation
- phase-based representations
- structural maps
- other modality-aware representations

The project specifically recommends comparing raw grayscale against gradients, edges, or other structure-focused representations for difficult illumination conditions.

However:

> A structural representation is a hypothesis to test, not a guaranteed modality-invariant solution.

It can also remove useful information or amplify noise.

---

# 19. Matching: Candidate Correspondences

After feature detection and description, a matcher proposes candidate correspondences.

The resulting matches should be called:

> **Candidate Matches**

rather than automatically calling them high-confidence or verified matches.

A candidate match means:

> The local matching mechanism considers two observations sufficiently similar according to its matching criterion.

It does **not** mean:

> The two points are geometrically correct.

The project feedback explicitly distinguishes candidate matches from geometrically verified inliers.

---

# 20. Why Modality Can Increase False Matches

Cross-sensor appearance changes can cause:

- fewer valid matches
- ambiguous descriptor distances
- repeated structural patterns
- false local similarities
- spatially clustered matches
- matches concentrated around one prominent crater or edge

This creates an important distinction:

```text
Many matches
      ≠
Many correct matches
```

and:

```text
High matcher confidence
      ≠
Geometric correctness
```

Geometric verification must therefore remain a separate stage.

---

# 21. Geometric Verification

A common V1 sequence is:

```text
Candidate Matches
       │
       ▼
RANSAC + Initial Model
       │
       ▼
Verified Inliers
       │
       ▼
Sub-Pixel Refinement
       │
       ▼
Refit Final Transform
       │
       ▼
Independent Evaluation
```

This order matters.

RANSAC provides a mechanism for rejecting candidate correspondences that are inconsistent with the selected geometric model.

Only after the reliable inlier set has been established should local sub-pixel refinement be applied.

The project feedback explicitly recommends this order.

---

# 22. How Modality Affects RANSAC

RANSAC does not make the underlying correspondences correct.

It only identifies a subset that is consistent with a chosen model and threshold.

If cross-sensor matching produces:

- too few correct correspondences,
- heavily clustered correspondences,
- structurally ambiguous correspondences,
- systematic local errors,

then RANSAC may still produce a mathematically valid model that is scientifically weak.

Therefore, geometric verification should consider:

- inlier count
- inlier ratio
- spatial distribution
- residual magnitude
- residual direction
- model stability
- independent check-point performance

---

# 23. Transformation Models

Sensor modality itself does not define the correct transformation model.

The transformation depends on:

- image geometry
- projection
- terrain relief
- viewing direction
- sensor geometry
- local scene extent
- whether products are already map-projected or orthorectified

For a conceptual source-to-reference transformation:

$$
p_r \approx T(p_s)
$$

where:

- \(p_s\) is a source-image coordinate;
- \(p_r\) is the corresponding reference coordinate;
- \(T\) is the estimated geometric transformation.

---

## 23.1 Affine transformation

An affine model can represent effects such as:

- translation
- rotation
- scaling
- shear

It may be appropriate as a local approximation for some already-projected image pairs.

---

## 23.2 Homography

A homography provides a projective mapping between two image planes.

It can be useful for local planar or approximately planar relationships.

However:

> The Moon is not a flat poster.

Lunar relief and sensor/viewing geometry can produce spatially varying correspondence errors that cannot necessarily be explained by one global homography.

The project feedback specifically recommends inspecting residual vectors rather than assuming one global transformation is always sufficient.

---

# 24. Modality and Viewing Geometry

Sensor modality can interact strongly with viewing geometry.

Two images may differ because of:

- sensor location
- look direction
- incidence geometry
- observation angle
- terrain relief
- projection
- product generation

This can change the apparent shape or location of terrain structures.

Therefore:

```text
Sensor difference
        +
Viewing geometry difference
        +
Terrain relief
        │
        ▼
Spatially varying correspondence error
```

A global transformation may then fit one region well and another region poorly.

---

# 25. Modality and Illumination

Illumination should remain conceptually separate from sensor modality.

However, the two interact.

A sensor can observe the same terrain under different Sun angles, while different sensors may also have different spectral and radiometric responses.

A changed Sun angle can alter:

- shadow location
- shadow length
- local contrast
- apparent terrain boundaries
- visibility of slopes
- crater-rim appearance

The project feedback emphasizes that Sun-angle changes affect shadows, not merely brightness. Histogram or contrast normalization cannot move a shadow back to the position it would have had under a different illumination geometry.

---

# 26. Terrain Structure Versus Shadow Structure

A critical registration distinction is:

```text
Persistent terrain structure
        │
        ├── crater rim
        ├── ridge
        ├── valley
        └── stable surface boundary

Illumination-dependent structure
        │
        ├── cast shadow
        ├── shadow boundary
        └── illumination contrast
```

A shadow boundary may be visually strong but geometrically unstable across Sun angles.

Therefore, matching should not automatically treat the strongest edge as the most reliable correspondence.

---

# 27. Modality and Preprocessing

Preprocessing should make sensor observations more comparable without pretending that missing information exists.

A useful conceptual rule is:

> **Every preprocessing operation should have a measurable registration purpose.**

Potential operations include:

- calibration or use of standard products
- geometric normalization
- local contrast normalization
- denoising
- gradient extraction
- edge extraction
- spectral dimensionality reduction
- scale pyramid generation
- sensor-specific representation conversion

Each should be treated as a research variable when its effect is uncertain.

The project feedback recommends sensor-specific routes, preservation of footprint/pixel-scale/projection/lighting/viewing metadata, and comparison of intensity versus structural representations.

---

# 28. Sensor-Aware Preprocessing Principle

The preferred conceptual structure is:

```text
Input image + metadata
        │
        ▼
Identify sensor / product
        │
        ▼
Sensor-specific preparation
        │
        ▼
Comparable effective scale
        │
        ▼
Registration-friendly representation
        │
        ▼
Common local correspondence pipeline
```

This does not require separate algorithms for every sensor.

Instead, it makes the sensor-specific assumptions explicit.

---

# 29. Common Structural Representation

A common representation can be useful when the goal is to compare structurally similar terrain across different sensing modalities.

For example:

```text
OHRC ──────────────┐
                   │
TMC-2 ─────────────┼──► Structural representation
                   │
IIRS ─► 2D mapping ┘
                         │
                         ▼
                  Local matching
```

But the representation should be validated empirically.

A representation that improves IIRS-to-visible matching may not improve OHRC-to-TMC-2 matching.

Therefore, modality-specific evaluation should be retained.

---

# 30. Residual Analysis Under Modality Change

After geometric verification and transformation estimation, residuals provide information about registration quality.

For a correspondence \(i\):

$$
\mathbf{r}_i =
\mathbf{p}_{r,i}
-
T(\mathbf{p}_{s,i})
$$

and residual magnitude can be written as:

$$
e_i = \|\mathbf{r}_i\|
$$

Residuals should not be reduced to one number.

The spatial pattern is important.

---

## 30.1 Random residuals

Small, relatively unstructured residual vectors may indicate that the model captures the dominant relationship and remaining error is comparatively local.

This does not by itself establish accuracy.

---

## 30.2 Systematic residuals

A coherent residual field can indicate:

- incorrect transformation model
- projection mismatch
- viewing geometry
- terrain relief
- metadata error
- sensor geometry
- systematic preprocessing effects

For example:

```text
→ → → →
→ → → →
→ → → →
```

is different from:

```text
↗  →  ↘
↑  •  ↓
↖  ←  ↙
```

The exact interpretation requires the image geometry and experiment context.

---

# 31. Spatial Distribution of Inliers

Cross-sensor matching can produce many correct-looking points in a small region.

That is dangerous because a transformation estimated from one concentrated structure may not generalize across the overlap.

Therefore:

> **Match count must be considered together with spatial coverage.**

Possible measurements include:

- grid occupancy
- convex-hull coverage
- spatial spread
- per-region inlier counts

The project feedback specifically identifies spatial coverage as an important metric.

---

# 32. Modality and Registration Accuracy

Registration accuracy should be measured independently of the points used to fit the transformation whenever possible.

If the transformation is estimated from:

$$
P_{\text{fit}}
$$

then independent check points should come from:

$$
P_{\text{check}}
$$

with:

$$
P_{\text{fit}} \cap P_{\text{check}} = \varnothing
$$

The final transformation is then evaluated on points that did not determine the transformation.

This is especially important for cross-sensor registration because a flexible model can otherwise appear successful while generalizing poorly.

---

# 33. Primary Registration Metric

For independent check points, a common error measure is RMSE:

$$
RMSE =
\sqrt{
\frac{1}{N}
\sum_{i=1}^{N}
e_i^2
}
$$

where:

$$
e_i =
\left\|
p_{r,i}
-
T(p_{s,i})
\right\|
$$

The project framing treats source-image pixels as the primary unit for registration accuracy.

Ground error in metres should only be reported when the GSD, projection, and reference truth make that conversion meaningful.

---

# 34. Why Pixel Error Must Be Sensor-Aware

A value such as:

$$
0.2\ \text{pixel}
$$

does not represent the same physical distance for every sensor.

If the physical ground scale differs, then:

$$
0.2\ \text{pixel}_{A}
\neq
0.2\ \text{pixel}_{B}
$$

in general.

The project feedback explicitly warns that the same sub-pixel error expressed in TMC-2 pixels and IIRS pixels does not represent the same ground error.

Therefore, results should report:

1. source-image pixel error;
2. sensor/product identity;
3. pixel-scale metadata;
4. ground error only when scientifically justified.

---

# 35. Recommended Modality Evaluation Metrics

Cross-sensor experiments should report multiple dimensions.

| Metric                | What it measures                                 |
| --------------------- | ------------------------------------------------ |
| Candidate match count | Number of proposed correspondences               |
| Verified inlier count | Number surviving geometric verification          |
| Inlier ratio          | Fraction of candidates consistent with the model |
| Spatial coverage      | Whether inliers span the overlap                 |
| Check-point RMSE      | Independent registration error                   |
| Median error          | Typical registration error                       |
| P90 / P95 error       | Upper-tail behavior                              |
| Maximum error         | Worst observed check-point error                 |
| Residual vector field | Spatial structure of error                       |
| Runtime               | Computational cost                               |
| Failure rate          | Reliability across test pairs                    |

The project feedback recommends inlier statistics, spatial coverage, independent check-point RMSE, ground error when meaningful, runtime, and failure rate as measurable outputs.

---

# 36. Modality Stress Testing

A modality claim should not be based on one visually successful pair.

A useful V1 stress matrix is:

| Case                | Purpose                                              |
| ------------------- | ---------------------------------------------------- |
| Easy pair           | Establish an end-to-end baseline                     |
| Sun-angle stress    | Test robustness to changing shadows and illumination |
| Scale stress        | Test large spatial-scale differences                 |
| Modality stress     | Test sensor-aware representations                    |
| Geometry stress     | Test stronger geometric differences                  |
| Low-feature terrain | Expose ambiguity and failure cases                   |

This stress matrix is consistent with the project's evaluation guidance.

---

# 37. Cross-Sensor Experimental Comparison

To isolate the effect of sensor-aware processing, the same image pairs should be evaluated under controlled pipeline variants.

A useful comparison structure is:

```text
Same image pairs
       │
       ├── Baseline SIFT
       │
       ├── Stronger matcher
       │
       └── Sensor-aware + multi-scale pipeline
                    │
                    ▼
              Same metrics
```

The project feedback recommends comparing these paths on the same benchmark pairs so that observed changes can be attributed more clearly to the tested components.

---

# 38. What a Modality Experiment Should Control

Where practical, hold constant:

- source/reference pair
- crop
- ground-truth definition
- check-point set
- evaluation metrics
- geometric model
- RANSAC configuration
- matching thresholds
- random seed
- hardware environment
- timing methodology

Then vary only the modality-related component being studied.

For example:

```text
Experiment A
Raw representation
        vs
Experiment B
Gradient representation
```

with the rest of the pipeline controlled.

This makes the result interpretable.

---

# 39. V1 Experiment Relevance

Sensor modality is connected to several existing V1 experiments.

## EXP-001 — SIFT Baseline

Path:

`experiments/v1/baseline/EXP-001-sift-baseline/README.md`

Purpose in the modality context:

- establish a simple local-feature baseline;
- determine where ordinary feature matching fails;
- provide a reference for later sensor-aware methods.

A failure under cross-sensor conditions is itself useful evidence.

---

## EXP-002 — Scale Pyramid

Path:

`experiments/v1/preprocessing/EXP-002-scale-pyramid/README.md`

Relevant because spatial scale and modality often interact.

The purpose is not to make coarse data artificially high-resolution.

It is to search at physically meaningful effective scales.

---

## EXP-003 — Gradient Representation

Path:

`experiments/v1/preprocessing/EXP-003-gradient-representation/README.md`

Relevant because gradients can emphasize structural information while reducing dependence on absolute intensity.

This is particularly relevant when radiometric or illumination differences make raw intensity less stable.

The result must be measured rather than assumed.

---

## EXP-004 — Affine vs Homography

Path:

`experiments/v1/geometry/EXP-004-affine-vs-homography/README.md`

Relevant because cross-sensor differences can coexist with projection and viewing-geometry differences.

The sensor itself does not determine whether affine or homography is correct.

Residual behavior and image geometry must guide the interpretation.

---

## EXP-005 — Residual Analysis

Path:

`experiments/v1/geometry/EXP-005-residual-analysis/README.md`

Especially relevant for modality research.

A low global error with structured residuals may indicate that the transformation is only locally adequate.

Residual vectors can help distinguish:

- correspondence errors,
- model errors,
- geometry errors,
- systematic sensor effects.

---

## EXP-006 — Sub-Pixel Refinement

Path:

`experiments/v1/refinement/EXP-006-subpixel-refinement/README.md`

Relevant because sub-pixel refinement operates on already verified correspondences.

It should not be used to hide modality-related correspondence errors.

The correct conceptual order remains:

```text
Candidate matches
       ↓
Geometric verification
       ↓
Verified inliers
       ↓
Sub-pixel refinement
       ↓
Final transformation
       ↓
Independent check-point evaluation
```

The project feedback explicitly recommends refining verified control points and then refitting the final transformation.

---

# 40. Current V1 Sensor Strategy

The project materials support a staged approach rather than attempting to solve all sensors simultaneously.

A practical research sequence is:

```text
Known measurable pair
        │
        ▼
SIFT baseline
        │
        ▼
Scale-aware comparison
        │
        ▼
Structural representation
        │
        ▼
Sensor-specific processing
        │
        ├── OHRC
        ├── TMC-2
        └── IIRS
        │
        ▼
Geometric verification
        │
        ▼
Residual analysis
        │
        ▼
Sub-pixel refinement
        │
        ▼
Independent evaluation
```

The project feedback recommends developing one measurable end-to-end result first and adding sensors gradually, with OHRC/TMC-2 before treating IIRS as its own experiment.

This is an experimental sequencing recommendation, not a claim that one sensor is intrinsically more important than another.

---

# 41. Modality-Specific Data Metadata

Every cross-sensor experiment should preserve metadata needed to interpret the result.

At minimum, where available:

- sensor identity
- product identity
- image dimensions
- pixel scale / GSD
- footprint
- projection
- acquisition information
- illumination information
- viewing geometry
- preprocessing history
- representation used for matching
- resampling scale
- reference/source designation

The project feedback specifically recommends preserving pixel size, footprint, and lighting/viewing metadata when available.

If a field is unavailable, record:

`[Not provided]`

rather than estimating it without evidence.

---

# 42. Product Generation Matters

Two images from the same sensor may still differ substantially because of product processing.

Potential differences include:

- calibration
- map projection
- orthorectification
- resampling
- cropping
- contrast processing
- filtering
- derived representations

Therefore:

```text
Same sensor
     ≠
Automatically identical product geometry
```

Likewise:

```text
Different sensor
     ≠
Automatically impossible to register
```

The correct question is whether the available representations preserve enough common geometric structure for reliable correspondence.

---

# 43. Map Projection and Sensor Modality

If the supplied products are already map-projected or orthorectified, some geometric differences may have already been corrected upstream.

The vision pipeline should not unnecessarily relearn geometry that the product generation process already provides.

The project feedback recommends using projection and available geometry information where possible before asking image matching to solve the remaining problem.

However, the exact status of each ChandraMap input product is:

`[To be verified from supplied data]`

unless explicitly documented elsewhere in the repository.

---

# 44. Modality and Global Retrieval

Sensor modality can also affect global candidate retrieval.

A global descriptor built for one sensor may not transfer directly to another sensor.

Therefore, if global retrieval is used:

```text
Reference sensor
      │
      ▼
Reference representation
      │
      ▼
Reference index

Source sensor
      │
      ▼
Source representation
      │
      ▼
Query descriptor
      │
      ▼
Top-K candidates
```

The source and reference representations must be compatible with the retrieval objective.

The project architecture treats global retrieval as conditional: if reliable geographic or footprint metadata can constrain the search, retrieval may not need to solve the entire global localization problem.

Global retrieval is therefore related to modality but is not the primary subject of this note.

---

# 45. Modality and Learned Matchers

Modern matching methods may provide alternatives to classical SIFT-based matching.

The project feedback identifies possible research paths including:

- ALIKED + LightGlue
- LoFTR
- RIFT
- CFOG

However, learned terrestrial models should not automatically be assumed to be lunar-invariant.

The project materials specifically caution that pretrained models can suffer from domain shift and extreme scale differences.

Therefore:

> A more modern matcher is a hypothesis to benchmark, not evidence of modality robustness.

---

# 46. Cross-Sensor Correspondence Quality

A scientifically useful correspondence should satisfy multiple conditions:

1. The two points represent the same physical or structurally corresponding terrain.
2. The correspondence is spatially plausible.
3. It is consistent with the estimated geometric model.
4. It contributes to adequate spatial coverage.
5. It generalizes to independent check points where possible.

This gives:

```text
Appearance similarity
        +
Geometric consistency
        +
Spatial distribution
        +
Independent validation
        =
Useful correspondence evidence
```

No single matcher confidence score is sufficient.

---

# 47. Flexible Warps and Modality

A flexible local warp can sometimes reduce residuals.

However, it can also hide weak correspondences.

For example:

```text
Weak correspondences
        │
        ▼
Highly flexible warp
        │
        ▼
Low fitting residual
        │
        └── Does not necessarily imply accurate registration
```

The project feedback explicitly warns that flexible warping should not conceal inaccurate or poorly distributed control points.

Therefore, flexible models should be evaluated with:

- independent check points
- spatial coverage
- residual maps
- model complexity
- failure cases

---

# 48. Common Failure Modes

## 48.1 Treating all sensors identically

**Symptom:** One preprocessing path is applied to OHRC, TMC-2, and IIRS.

**Risk:** The representation may be inappropriate for one or more modalities.

**Interpretation:** Sensor-specific measurement differences have been hidden rather than addressed.

---

## 48.2 Solving modality differences by resizing

**Symptom:** A coarse image is enlarged to match a fine image.

**Risk:** Pixel count increases without recovering missing spatial detail.

**Interpretation:** The pipeline has changed representation size, not information content.

---

## 48.3 Matching raw intensity across strong spectral differences

**Symptom:** Intensity descriptors perform poorly across sensors.

**Possible cause:** Different spectral/radiometric responses.

**Response:** Test structural representations and sensor-specific preprocessing.

---

## 48.4 Treating IIRS as ordinary grayscale imagery

**Symptom:** A hyperspectral cube is directly forced into a standard 2D matching pipeline without a documented representation.

**Risk:** Spectral information and dimensionality are handled implicitly or incorrectly.

**Response:** Define and evaluate an explicit 2D representation first.

---

## 48.5 Confusing candidate matches with correct matches

**Symptom:** Matcher confidence is reported as registration accuracy.

**Risk:** False correspondences may be counted as successful.

**Response:** Use geometric verification and independent evaluation.

---

## 48.6 Using only inlier count

**Symptom:** A method with many inliers is declared successful.

**Risk:** Inliers may be spatially clustered.

**Response:** Report spatial coverage and independent check-point error.

---

## 48.7 Using only visual overlays

**Symptom:** A visually aligned image is presented without numerical evaluation.

**Risk:** Small systematic errors or local distortions may remain hidden.

**Response:** Include quantitative residual and check-point metrics.

---

## 48.8 Fitting and evaluating on the same points

**Symptom:** The transformation is fitted on inliers and RMSE is reported on those same points.

**Risk:** The reported error may underestimate generalization error.

**Response:** Maintain independent check points.

---

## 48.9 Assuming homography solves all lunar geometry

**Symptom:** One global homography is treated as physically universal.

**Risk:** Lunar relief and viewing geometry can create spatially varying errors.

**Response:** Inspect residual fields and test whether a more appropriate geometric treatment is required.

---

## 48.10 Claiming sub-pixel physical accuracy from coarse data

**Symptom:** A numerical sub-pixel estimate is interpreted as arbitrary physical precision.

**Risk:** Sensor resolution, sampling, noise, representation, and ground truth limit the physical meaning of the estimate.

**Response:** Report source-image pixel error first and convert to ground units only when justified.

---

# 49. What Cross-Sensor Success Does Not Prove

A successful cross-sensor registration experiment does **not** automatically prove that:

- the method is invariant to all sensors;
- the method is invariant to all illumination conditions;
- the method works globally across the Moon;
- the method works for every terrain type;
- the method works at arbitrary scale differences;
- the method is physically sub-pixel accurate in metres;
- the same representation is optimal for every sensor;
- a learned matcher is lunar-invariant;
- one transformation model is universally valid.

The result is valid only for the conditions actually evaluated.

---

# 50. What a Strong Modality Result Should Demonstrate

A strong scientific result should make it possible to answer:

1. Which sensor pair was tested?
2. What physical region was shared?
3. What were the spatial scales?
4. What were the sensor/product characteristics?
5. What representations were compared?
6. How many candidate matches were generated?
7. How many survived geometric verification?
8. Were the matches spatially distributed?
9. What transformation was estimated?
10. What were the independent check-point errors?
11. What happened to the residual field?
12. What happened under harder illumination or scale conditions?
13. What was the runtime?
14. What were the failure cases?
15. Can another researcher reproduce the comparison?

---

# 51. Recommended Modality Experiment Record

Each modality experiment should document:

```text
Experiment ID
Sensor pair
Source product
Reference product
Image dimensions
Pixel scale / GSD
Projection
Footprint
Illumination metadata
Viewing geometry
Preprocessing
Representation
Effective matching scale
Feature detector
Descriptor / matcher
Geometric model
RANSAC configuration
Verified inliers
Spatial coverage
Independent check points
RMSE
Median / P90 / P95 / maximum error
Residual field
Runtime
Failure status
Random seed
Software version
Data provenance
```

Any unavailable field should be explicitly recorded as:

`[Not provided]`

or:

`[To be verified]`

---

# 52. Reproducibility Requirements

Cross-sensor results are especially sensitive to data and preprocessing choices.

A reproducible experiment should preserve:

- exact source/reference products
- sensor identity
- image version
- preprocessing configuration
- representation configuration
- scale selection
- feature configuration
- matcher configuration
- geometric model
- RANSAC parameters
- refinement parameters
- check-point definition
- random seed where applicable
- software/environment version
- generated metrics
- visual diagnostics

A result should not depend on undocumented manual image adjustments.

---

# 53. Scientific Interpretation Framework

A useful interpretation sequence is:

### Step 1 — Identify the source of difference

Ask whether the observed mismatch is primarily associated with:

- modality
- scale
- illumination
- viewing geometry
- projection
- preprocessing
- terrain ambiguity

### Step 2 — Inspect correspondence behavior

Check:

- candidate count
- inlier count
- spatial distribution
- descriptor behavior
- rejected matches

### Step 3 — Inspect geometry

Check:

- model type
- residual magnitude
- residual direction
- spatial residual pattern

### Step 4 — Evaluate independently

Use:

- check-point RMSE
- robust error statistics
- ground error when meaningful

### Step 5 — Report limitations

If the method fails on a sensor or stress condition, preserve the failure as part of the result.

---

# 54. Modality Is a System Property, Not Only an Algorithm Property

A common mistake is to treat cross-sensor robustness as something the matcher alone must solve.

In reality:

```text
Sensor
  │
  ▼
Product generation
  │
  ▼
Preprocessing
  │
  ▼
Representation
  │
  ▼
Scale handling
  │
  ▼
Feature extraction
  │
  ▼
Matching
  │
  ▼
Geometric verification
  │
  ▼
Refinement
  │
  ▼
Evaluation
```

Every stage can influence cross-sensor performance.

Therefore, a weak cross-sensor result should not immediately be interpreted as a matcher failure.

---

# 55. Modality and the V1 Research Philosophy

The current V1 direction is consistent with a controlled, measurable research process:

> **Build the simplest measurable version first; make it more sensor-aware only after the baseline exposes where the problem actually occurs.**

The project feedback repeatedly emphasizes proving assumptions with real image pairs, retaining actual numerical evidence, and adding complexity only when it improves measured difficult cases.

This makes modality research a sequence of falsifiable questions rather than a collection of preprocessing tricks.

---

# 56. Example Research Questions

The following questions are appropriate for future ChandraMap experiments.

### Representation

- Does gradient representation improve cross-sensor correspondence compared with raw intensity?
- Which IIRS 2D representation preserves the most useful terrain structure?
- Does a structural representation reduce sensitivity to radiometric differences?

### Scale

- At what effective scale does cross-sensor correspondence become reliable?
- Does a reference pyramid improve matching for large resolution differences?
- At what point does fine refinement stop being supported by the source sensor?

### Matching

- Does SIFT degrade predictably as modality difference increases?
- Does a learned matcher improve difficult modality cases?
- Do multimodal structural descriptors provide more stable correspondences?

### Geometry

- Does sensor-aware preprocessing reduce systematic residuals?
- Does a single global model adequately explain the correspondence field?
- Are remaining errors associated with terrain relief or sensor geometry?

### Evaluation

- Does improved inlier count correspond to lower independent check-point error?
- Does improved matching coverage correspond to better generalization?
- Does sub-pixel refinement improve independent registration accuracy?

---

# 57. Future Research Directions

The following are research directions rather than current ChandraMap capabilities.

## 57.1 Learned multimodal representations

Potential future work:

- multimodal feature learning
- cross-sensor contrastive learning
- domain adaptation
- lunar-specific pretrained representations
- spectral-spatial representation learning

Status: `[Planned / Research Direction]`

---

## 57.2 IIRS-specific representation learning

Potential directions:

- learned spectral dimensionality reduction
- spectral-spatial embeddings
- terrain-structure extraction from hyperspectral data
- learned cross-modal alignment between IIRS and visible imagery

Status: `[Planned / Research Direction]`

---

## 57.3 Geometry-aware registration

Potential directions:

- DEM-assisted registration
- sensor-model-assisted correspondence
- orthorectification-aware matching
- local terrain-aware transformations
- piecewise registration with independent validation

Status: `[Planned / Research Direction]`

---

## 57.4 Physics-informed correspondence

Future systems could explicitly incorporate:

- Sun geometry
- terrain orientation
- illumination modeling
- sensor viewing geometry
- expected shadow behavior

The objective would be to distinguish stable terrain structure from illumination-dependent appearance.

Status: `[Research Direction]`

---

## 57.5 Modality-aware benchmark expansion

Future benchmarks could separate:

```text
Same sensor
      │
      ├── Similar illumination
      └── Different illumination

Cross sensor
      │
      ├── Similar scale
      ├── Moderate scale difference
      └── Large scale difference

IIRS cross-modality
      │
      ├── Band representation
      ├── PCA representation
      └── Structural representation
```

This would make modality-specific improvements easier to isolate.

---

# 58. Recommended Interpretation of OHRC / TMC-2 / IIRS Results

Results should be reported separately by sensor pair where possible.

For example:

| Source | Reference             | Modality condition          | Representation | Registration result |
| ------ | --------------------- | --------------------------- | -------------- | ------------------- |
| OHRC   | `[Reference product]` | Visible cross-product       | `[TBD]`        | `[TBD]`             |
| TMC-2  | `[Reference product]` | Panchromatic cross-scale    | `[TBD]`        | `[TBD]`             |
| IIRS   | `[Reference product]` | IR hyperspectral to visible | `[TBD]`        | `[TBD]`             |

Do not immediately collapse all sensors into one average.

A mixed average can hide:

- modality-specific failures;
- scale-specific failures;
- representation-specific improvements;
- differences in physical pixel scale;
- differences in information content.

The project feedback explicitly recommends separate sensor results rather than one mixed average.

---

# 59. Minimal Evidence Package for a Cross-Sensor Claim

Before claiming that a method improves sensor robustness, the minimum evidence should include:

### Data

- source image
- reference image
- sensor identities
- pixel-scale metadata
- overlap information

### Matching

- candidate correspondences
- verified inliers
- spatial distribution

### Geometry

- selected transformation
- residual vectors
- residual statistics

### Accuracy

- independent check-point error
- pixel units
- ground units only when meaningful

### Stress testing

- at least one controlled difficult condition

### Reproducibility

- preprocessing
- representation
- parameters
- experiment configuration

A visual overlay can supplement this evidence but should not replace it.

---

# 60. Practical Contributor Checklist

Before adding a new sensor or modality to ChandraMap, verify:

### Sensor definition

- [ ] Sensor identity is recorded.
- [ ] Imaging modality is documented.
- [ ] Spectral characteristics are documented where available.
- [ ] Spatial scale is recorded from product metadata.
- [ ] Radiometric/product characteristics are documented where available.

### Geometry

- [ ] Projection status is known.
- [ ] Footprint is known where available.
- [ ] Viewing geometry is documented where available.
- [ ] Illumination information is preserved where available.

### Representation

- [ ] The chosen image representation is explicitly documented.
- [ ] Hyperspectral inputs have an explicit 2D representation if conventional 2D matching is used.
- [ ] Resampling does not claim to recover missing spatial information.
- [ ] Effective scale is documented.

### Matching

- [ ] Candidate matches are distinguished from verified inliers.
- [ ] Geometric verification is applied.
- [ ] Spatial coverage is measured.
- [ ] Residuals are inspected.

### Evaluation

- [ ] Independent check points are used where available.
- [ ] RMSE is reported in source-image pixels.
- [ ] Ground error is reported only when meaningful.
- [ ] Runtime is recorded.
- [ ] Failures are reported.

### Scientific interpretation

- [ ] Modality effects are distinguished from scale effects.
- [ ] Illumination effects are distinguished from modality effects.
- [ ] Viewing geometry is considered.
- [ ] Results are not generalized beyond tested conditions.

---

# 61. Key Principles

The following principles should guide sensor-modality work in ChandraMap.

1. **The same lunar surface can produce substantially different measurements across sensors.**
2. **Sensor modality is not the same thing as spatial resolution.**
3. **Spatial resolution is not the same thing as pixel count.**
4. **Upsampling does not recover missing spatial information.**
5. **Spectral differences can change feature appearance even at the same spatial scale.**
6. **Radiometric normalization does not eliminate all cross-sensor differences.**
7. **Sun-angle changes affect geometry of shadows, not only brightness.**
8. **OHRC, TMC-2, and IIRS should not automatically share one identical preprocessing path.**
9. **IIRS requires an explicit registration-friendly 2D representation before conventional 2D matching.**
10. **Candidate matches are not verified correspondences.**
11. **RANSAC provides geometric verification; it does not guarantee physical correctness.**
12. **More matches are not necessarily better matches.**
13. **Spatial coverage matters.**
14. **A visually good overlay is not sufficient evidence of accurate registration.**
15. **Residual vectors can reveal systematic geometric problems hidden by a single score.**
16. **Sub-pixel refinement should operate on verified correspondences.**
17. **Transformation fitting and independent evaluation should use separate point sets.**
18. **Source-image pixel error is the primary registration unit unless a justified ground conversion exists.**
19. **A global transformation should not be assumed to explain all lunar terrain geometry.**
20. **Sensor-specific results should be preserved instead of hidden inside a mixed average.**
21. **Every modality improvement should be demonstrated with controlled measurements.**
22. **Failure cases are scientifically useful evidence.**
23. **No pretrained matcher should be assumed to be lunar- or modality-invariant without testing.**
24. **Product metadata is part of the scientific input, not optional decoration.**
25. **A cross-sensor registration claim is valid only within the conditions actually evaluated.**

---

# 62. Relationship to the ChandraMap Research Documentation

This note provides conceptual background for the repository's research and experiment documentation.

Relevant confirmed project documents include:

- `research/README.md`
- `research/literature/README.md`
- `research/notes/`
- `experiments/templates/EXPERIMENT_TEMPLATE.md`
- `experiments/v1/README.md`
- `experiments/v1/baseline/EXP-001-sift-baseline/README.md`
- `experiments/v1/preprocessing/EXP-002-scale-pyramid/README.md`
- `experiments/v1/preprocessing/EXP-003-gradient-representation/README.md`
- `experiments/v1/geometry/EXP-004-affine-vs-homography/README.md`
- `experiments/v1/geometry/EXP-005-residual-analysis/README.md`
- `experiments/v1/refinement/EXP-006-subpixel-refinement/README.md`

The experiment-specific documents remain the authoritative location for their respective experimental procedures and results. This note provides the scientific context in which those experiments should be interpreted.

---

# 63. Source Materials

The following project materials were used as the source context for this research note:

- `SIH26166 Silarlar PS.pdf`
- `Aryan_SIH_26166_Lunar_Image_Correspondence_Feedback.pptx`
- `Aryan_Lunar_Image_Registration_Feedback.pdf`

The supplied feedback specifically supports the sensor-aware treatment of OHRC, TMC-2, and IIRS, the distinction between scale and modality, the special treatment of IIRS, the use of structural representations, and the need for measurable cross-sensor evaluation.

The SIH problem material also identifies OHRC, TMC-2, and IIRS as relevant Chandrayaan-2 inputs and identifies LRO products as reference data in the broader project context.

---

# 64. Open Questions

The following should remain explicitly unresolved until confirmed from the actual supplied data or implementation:

- Exact product formats for every sensor: `[To be verified]`
- Exact product-specific GSD/pixel scale: `[To be verified]`
- Exact map-projection status of each input: `[To be verified]`
- Availability of footprint metadata: `[To be verified]`
- Availability of viewing-geometry metadata: `[To be verified]`
- Availability of illumination/Sun-angle metadata: `[To be verified]`
- Exact IIRS input form: full cube, bands, browse image, or derived product: `[To be verified]`
- Final IIRS 2D representation: `[TBD]`
- Final cross-sensor matcher: `[TBD]`
- Final geometric model selection: `[TBD]`
- Final modality-specific acceptance thresholds: `[TBD]`
- Quantitative cross-sensor performance: `[Not provided]`
- Demonstrated modality invariance: `[Not established]`

These should not be filled with assumed values merely to make the documentation appear complete.

---

# 65. Final Research Perspective

Sensor modality is one of the central reasons lunar image correspondence cannot be reduced to ordinary image similarity.

ChandraMap observes a common physical world through different measurement systems. OHRC, TMC-2, and IIRS therefore provide different representations of lunar terrain, with differences in spatial sampling, spectral response, radiometry, image formation, and potentially geometry.

The correct engineering response is not to hide these differences behind a single generic preprocessing step.

Instead, ChandraMap should:

```text
Understand the sensor
        ↓
Preserve its metadata
        ↓
Prepare it appropriately
        ↓
Compare at meaningful effective scales
        ↓
Extract stable structural information
        ↓
Generate candidate correspondences
        ↓
Verify them geometrically
        ↓
Inspect spatial coverage and residuals
        ↓
Refine only verified correspondences
        ↓
Evaluate independently
        ↓
Report sensor-specific performance
```

The most important scientific distinction is between **appearance similarity** and **geometric correspondence**.

Two corresponding lunar structures do not need to have identical pixel values. Conversely, two visually similar patches are not necessarily the same physical location.

For ChandraMap, the objective is therefore not to force different sensors to look identical. The objective is to identify representations and correspondence mechanisms that preserve enough stable lunar structure to establish reliable geometric relationships across the available sensing conditions.

That is why modality must be treated as an explicit research variable throughout the V1 pipeline rather than as an implementation detail.

> **Sensor-aware registration should make the measurements comparable without pretending that the sensors measured the same information.**
