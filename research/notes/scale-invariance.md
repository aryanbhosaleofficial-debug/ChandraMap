# Scale Invariance and Scale Robustness in ChandraMap

> **Research note:** `research/notes/scale-invariance.md`
> **Status:** Foundational research note
> **Scope:** Scale variation, spatial resolution, GSD, multi-scale matching, and their interaction with lunar image correspondence and registration
> **Project principle:** **Build small. Measure honestly. Keep the failures.**

---

## 1. Overview

ChandraMap is a lunar image correspondence and registration system intended to align images of the same lunar region when the observations may differ in spatial resolution, sensor, illumination, viewing geometry, and image representation.

Scale is one of the central difficulties because the same physical lunar structure can occupy substantially different numbers of pixels in different observations.

The project feedback explicitly recommends treating scale as a physical information problem rather than simply resizing images. In particular, a higher-resolution reference can be brought toward a comparable effective ground scale before coarse matching, while fine refinement should only be attempted where the source image actually contains the required spatial information.

The important distinction is:

> **Changing the number of pixels is not the same as recovering or creating spatial information.**

For ChandraMap, the objective is therefore not to make every image look identical. The objective is to determine whether useful correspondences can remain reliable when the same lunar terrain is represented at different effective scales.

---

## 2. Why Scale Matters in ChandraMap

The same lunar region may be observed by instruments with substantially different spatial sampling.

The project materials identify Chandrayaan-2 OHRC, TMC-2, and IIRS as important inputs, while LRO products may serve as reference data depending on the final dataset and evaluation definition. The project feedback emphasizes that these sensors should not be treated as identical images.

The supplied technical feedback describes approximately:

| Data source |   Approximate scale described in project materials | Registration implication                       |
| ----------- | -------------------------------------------------: | ---------------------------------------------- |
| OHRC        |   ~0.25–0.32 m/pixel depending on product/document | Fine terrain correspondence                    |
| TMC-2       |                                         ~5 m/pixel | Coarser structural correspondence              |
| IIRS        |                                        ~80 m/pixel | Large-scale structural/spectral representation |
| LRO NAC     | Often ~0.5–2 m/pixel depending on product/geometry | Potential high-resolution reference            |

These values are project-context values rather than universal sensor constants. The challenge product metadata should remain the final authority for the actual images used in experiments.

This creates potentially large scale differences.

For example, a crater rim, ridge, boulder field, or shadow boundary may span:

```text
Image A
████████████████████
        feature
        occupies
        many pixels

Image B
██████
 feature
 occupies
 fewer pixels
```

The physical terrain has not necessarily changed.

Its **image representation** has changed.

---

# 3. What Does "Scale" Mean?

Scale is not a single number in the ChandraMap problem.

It is useful to distinguish several related concepts.

## 3.1 Physical scale

Physical scale describes the relationship between a real-world lunar feature and its representation in an image.

For a simplified example:

```text
Physical lunar feature
        ↓
      100 m
        ↓
Image A: 1 m/pixel
        ↓
~100 pixels

Image B: 5 m/pixel
        ↓
~20 pixels
```

The same physical structure can therefore have very different pixel footprints.

---

## 3.2 Spatial resolution

Spatial resolution describes the level of spatial detail that an imaging system can represent.

Higher nominal spatial resolution generally permits smaller spatial structures to be represented, while coarser resolution causes small structures to be averaged, blurred, or lost.

However:

> **Higher spatial resolution does not automatically mean easier correspondence.**

The project feedback explicitly warns that sensor modality and Sun angle can matter as much as nominal resolution.

A high-resolution image under substantially different illumination may be harder to match than a lower-resolution image with more similar structural appearance.

---

## 3.3 Ground Sampling Distance (GSD)

GSD describes the approximate ground distance represented by one image pixel.

Conceptually:

```text
GSD = physical ground distance represented by a pixel
```

For example, if an image has an effective GSD of approximately:

```text
1 m/pixel
```

then a 100 m-wide structure could occupy roughly:

```text
100 pixels
```

under simplified conditions.

At:

```text
5 m/pixel
```

the same structure would occupy roughly:

```text
20 pixels
```

The exact relationship depends on image geometry, projection, sampling, product generation, and the feature itself.

Therefore GSD should be treated as an important scale descriptor rather than as a complete description of image similarity.

---

# 4. Scale Difference

A **scale difference** exists when the same physical structure occupies different pixel extents between two images.

A simplified one-dimensional example:

```text
Physical feature
──────────────────────────────
            100 m

Image A
|------------------------------|
100 pixels
1 m/pixel

Image B
|----------|
20 pixels
5 m/pixel
```

The physical feature is the same.

Its image-space scale is different.

This difference can affect:

- keypoint detection
- descriptor construction
- descriptor similarity
- correspondence search
- geometric estimation
- residual distributions
- registration accuracy

---

# 5. Scale Robustness

**Scale robustness** is the practical ability of a registration or correspondence method to continue producing useful results when the same terrain appears at different image scales.

For ChandraMap, a scale-robust method should ideally preserve useful correspondences across a defined range of effective scale differences.

This is an experimental property.

It should be measured using controlled image pairs rather than assumed from the algorithm name.

A useful conceptual definition is:

```text
Scale robustness
=
ability to maintain reliable correspondence
under tested scale differences
```

Possible measurements include:

- number of candidate matches
- number of geometrically verified inliers
- inlier ratio
- spatial coverage
- independent check-point error
- failure rate
- runtime

The project evaluation guidance specifically recommends a **scale stress** case involving a large GSD difference and comparing baseline and improved pipelines on the same test pairs.

---

# 6. Scale Invariance

**Scale invariance** is a stronger claim than scale robustness.

A representation or method is scale-invariant when its useful correspondence behavior remains effectively unchanged, or appropriately transformed, across a specified range of scale changes.

In practical computer vision, "scale invariant" is often used to describe methods designed to detect or represent structures across multiple scales.

However:

> **A multi-scale pipeline is not automatically scale-invariant.**

Likewise:

> **A scale pyramid is not automatically proof of scale invariance.**

For ChandraMap, these terms should be used carefully:

| Term             | Meaning                                                                     |
| ---------------- | --------------------------------------------------------------------------- |
| Scale-aware      | Explicitly considers scale                                                  |
| Multi-scale      | Processes or searches at multiple scales                                    |
| Scale-normalized | Attempts to bring representations toward a common scale                     |
| Scale-robust     | Demonstrated to retain useful performance across tested scale differences   |
| Scale-invariant  | Stronger property demonstrated across an appropriate range of scale changes |

Until controlled experiments demonstrate otherwise, ChandraMap should describe its approach as **multi-scale**, **scale-aware**, or **scale-robust under tested conditions**, rather than universally scale-invariant.

---

# 7. Scale Is Not the Same as Resizing

One of the most important concepts in this research note is the distinction between **resizing an image** and **observing the same physical surface at another spatial resolution**.

## 7.1 Digital resizing

Suppose an image is:

```text
1000 × 1000 pixels
```

and is resized to:

```text
2000 × 2000 pixels
```

The number of pixels has increased.

The underlying spatial information has not automatically increased.

Interpolation may estimate values between existing samples, but it cannot reconstruct spatial detail that was never recorded.

---

## 7.2 Independent sensor acquisition

Consider instead:

```text
Physical lunar surface
        ↓
Sensor A sampling
        ↓
Image A

Physical lunar surface
        ↓
Sensor B sampling
        ↓
Image B
```

The two sensors may have:

- different spatial sampling
- different optics
- different modulation/response characteristics
- different noise characteristics
- different spectral sensitivity
- different viewing geometry
- different processing pipelines

Therefore:

```text
resize(Image B)
```

is not necessarily equivalent to:

```text
independently acquired Image A
```

at the same nominal pixel dimensions.

---

# 8. Why Upsampling Does Not Solve Scale

The project feedback explicitly states that upsampling changes pixel count rather than recovering missing spatial information.

Consider:

```text
Original coarse image

+---+---+
|   |   |
+---+---+
|   |   |
+---+---+
```

After upsampling:

```text
+---+---+---+---+
|   |   |   |   |
+---+---+---+---+
|   |   |   |   |
+---+---+---+---+
|   |   |   |   |
+---+---+---+---+
|   |   |   |   |
+---+---+---+---+
```

There are now more pixels.

There is not necessarily more measured terrain information.

This matters particularly for very coarse data such as IIRS-derived representations.

The project guidance explicitly warns against enlarging coarse IIRS data to NAC/OHRC-like resolution and treating the result as newly recovered detail.

---

# 9. Physical Surface → Sensor → Image

A useful mental model for ChandraMap is:

```text
                 PHYSICAL MOON
                      │
                      ▼
             Lunar terrain structure
                      │
                      ▼
             Imaging geometry
                      │
                      ▼
                 Sensor sampling
                      │
          ┌───────────┼───────────┐
          ▼           ▼           ▼
        OHRC        TMC-2        IIRS
          │           │           │
          ▼           ▼           ▼
       Image A     Image B     Image C
```

The image is therefore the result of a measurement process.

Two images of the same region can differ because of:

- sampling scale
- sensor characteristics
- illumination
- viewing geometry
- spectral response
- image processing
- projection
- noise

Scale handling must therefore respect the measurement process rather than treating every image as an arbitrary bitmap.

---

# 10. How Scale Differences Affect Feature Detection

ChandraMap V1 uses SIFT as the initial explainable local matching baseline.

SIFT is useful in this context because it was designed to identify local structures across changes including image scale and rotation.

However, this does **not** mean that SIFT guarantees successful lunar correspondence across arbitrary GSD differences.

The project feedback specifically identifies SIFT as a baseline while noting that strong modality and illumination changes can remain difficult.

## 10.1 Keypoint visibility

A feature may be:

```text
clearly represented at fine scale
        ↓
weaker at medium scale
        ↓
not represented at coarse scale
```

For example, a small terrain structure that occupies only a few pixels at coarse resolution may cease to produce a stable keypoint.

---

## 10.2 Feature merging

Multiple fine-scale structures can become one coarse-scale structure.

Conceptually:

```text
Fine image

•  •   •
 \ |  /
  \| /
   ▼

Several structures
```

may become:

```text
Coarse image

   ●

One combined structure
```

The correspondence problem is no longer simply "find the same pixels."

The underlying structures themselves may have been spatially aggregated.

---

## 10.3 Feature disappearance

A feature that is visible at one scale may fall below the effective spatial resolution of another sensor.

This creates a fundamental limit:

> A matching method cannot reliably recover a physical structure that is not represented in the source measurement.

---

# 11. How Scale Affects Local Descriptors

Local descriptors represent the appearance or structure around a detected point.

Scale changes can alter:

- patch size
- gradient distribution
- edge structure
- local texture
- relative geometry
- contrast distribution
- number of represented structures

Even when the same physical feature remains detectable, the descriptor may not be identical.

Conceptually:

```text
Same terrain
      │
      ├── Fine scale → detailed descriptor
      │
      └── Coarse scale → aggregated descriptor
```

A descriptor therefore needs enough scale robustness to identify corresponding structures without requiring pixel-level identity.

---

# 12. Scale and SIFT

SIFT explicitly incorporates scale-space reasoning.

This makes it a useful baseline for ChandraMap's scale experiments.

However:

```text
SIFT is scale-aware
        ≠
SIFT solves all lunar scale differences
```

In particular, severe differences in:

- spatial resolution
- illumination
- sensor modality
- image quality
- terrain representation

can still reduce correspondence quality.

Therefore the correct scientific question is not:

> "Is SIFT scale-invariant?"

A more useful ChandraMap question is:

> "How does SIFT correspondence quality change as the effective scale difference between the source and reference images increases?"

That question can be measured.

---

# 13. Scale and Candidate Matches

A local matcher can produce candidate correspondences between features.

Scale differences may affect:

- number of detected features
- descriptor similarity
- nearest-neighbor relationships
- ambiguity between similar structures
- number of candidate matches
- spatial distribution of candidates

Importantly:

> **More candidate matches do not necessarily mean better registration.**

A large number of candidates can still contain many incorrect or spatially clustered matches.

The project explicitly distinguishes **candidate matches** from **verified inliers**. A matcher confidence score is not proof that a correspondence is geometrically correct; RANSAC/geometric verification is used to determine which candidates survive.

---

# 14. Scale and Geometric Verification

After candidate matching, ChandraMap's V1 pipeline uses geometric verification.

Conceptually:

```text
Source image
      │
      ▼
Feature detection
      │
      ▼
Candidate matches
      │
      ▼
RANSAC + initial model
      │
      ▼
Verified inliers
      │
      ▼
Transformation
      │
      ▼
Registration error
```

Scale differences can influence the geometry stage indirectly.

If scale produces poor or uneven correspondences, RANSAC may receive:

- too few valid points
- clustered points
- ambiguous correspondences
- spatially biased correspondences
- incorrect matches

The transformation model may then appear numerically stable while representing only a small portion of the overlap.

---

# 15. Scale and Transformation Estimation

A global transformation is estimated from image correspondences.

For a local, already map-projected image pair, the project uses affine or homography models as reasonable initial geometric models. However, lunar terrain is not a flat surface, and raw sensor/viewing geometry can introduce additional effects.

Scale differences can therefore interact with geometric estimation.

For example:

```text
True terrain correspondence
          ↓
different image scales
          ↓
different feature locations
          ↓
correspondence uncertainty
          ↓
transformation uncertainty
          ↓
residual error
```

This means that a scale problem may appear later as a geometry problem.

The experiment design should therefore avoid assuming that every transformation error is caused by the transformation model itself.

---

# 16. Scale and Residual Error

Registration residuals measure disagreement between predicted and observed point locations.

For an independently evaluated point:

```text
Observed point
      ●

Predicted point
      ×

Residual
      ●────×
```

If scale handling is poor, residuals may increase.

But residuals should be analyzed spatially rather than summarized by one number alone.

The project feedback recommends inspecting residual vectors across the image because systematic changes from one side of the image to another can indicate limitations of a global model or additional geometric effects.

---

# 17. Pixel Error and Physical Error

ChandraMap should report source-image registration error in **pixels first**.

For example:

```text
check-point RMSE = 0.25 source pixels
```

This is directly meaningful as an image-space registration measurement.

Converting that value into metres requires meaningful knowledge of:

- GSD
- projection
- product geometry
- reference truth

The project feedback explicitly warns that the same pixel error does not correspond to the same ground error for sensors with different GSDs.

Therefore:

```text
0.2 pixel on a fine-resolution image
```

and

```text
0.2 pixel on a coarse-resolution image
```

should not automatically be described as the same physical accuracy.

---

# 18. Image Pyramids

A central scale-handling concept for ChandraMap is the **image pyramid**.

An image pyramid represents an image at multiple spatial scales.

Conceptually:

```text
Level 0
████████████████████
████████████████████
████████████████████

        ↓ downsample

Level 1
██████████
██████████

        ↓ downsample

Level 2
█████
█████

        ↓ downsample

Level 3
███
███
```

Each level represents the image at a different effective spatial scale.

---

# 19. Why Use a Reference Pyramid?

The project feedback recommends building a reference pyramid or downsampling the higher-resolution side so that coarse matching can occur at comparable effective ground scales.

The underlying idea is:

```text
High-resolution reference
          │
          ├── Level 0: fine
          ├── Level 1: medium
          ├── Level 2: coarse
          └── Level 3: very coarse

Source image
          │
          ▼
Select comparable effective scale
          │
          ▼
Coarse correspondence
          │
          ▼
Fine refinement where justified
```

This is different from simply enlarging the source image.

---

# 20. Coarse-to-Fine Registration

A scale-aware registration strategy can be organized as:

```text
                    INPUTS
                      │
          ┌───────────┴───────────┐
          │                       │
       Source                 Reference
          │                       │
          │                  Build pyramid
          │                       │
          └───────────┬───────────┘
                      ▼
             Compare scales
                      │
                      ▼
             Coarse matching
                      │
                      ▼
            Candidate region
                      │
                      ▼
              Local matching
                      │
                      ▼
              RANSAC / model
                      │
                      ▼
             Verified inliers
                      │
                      ▼
          Sub-pixel refinement
                      │
                      ▼
              Final transform
                      │
                      ▼
          Independent evaluation
```

This reflects the project's stated direction of separating multi-scale search from fine matching.

---

# 21. Multi-Scale Search Is Not Automatically Multi-Scale Matching

These concepts should remain separate.

## Multi-scale search

The system searches for the correct region at multiple effective scales.

## Multi-scale matching

The local matcher itself operates across multiple image scales.

## Multi-scale registration

The registration process estimates alignment using representations at multiple scales.

A pipeline may perform one without fully performing the others.

For example:

```text
Reference pyramid
        ↓
coarse candidate search
        ↓
single-scale SIFT matching
```

is multi-scale search followed by single-scale local matching.

This distinction is useful when interpreting EXP-002.

---

# 22. Scale and Illumination

Scale cannot be studied independently from illumination in lunar imagery.

The Moon's appearance changes substantially with Sun geometry.

The project feedback emphasizes that a Sun-angle change can alter shadow location or direction around a crater. Brightness or contrast normalization cannot undo a change in shadow geometry.

This creates an interaction:

```text
Scale difference
      +
Sun-angle difference
      ↓
Different spatial representation
      +
Different shadow structure
      ↓
Harder correspondence
```

A feature may therefore appear different for two independent reasons:

1. it is represented at a different spatial scale;
2. its illumination geometry has changed.

---

# 23. Why Scale Normalization Does Not Solve Illumination

Consider a crater:

```text
Observation A

     ______
   /        \
  |   ███    |
   \________/


Observation B

     ______
   /        \
  |    ███   |
   \________/
```

The crater may be the same.

The shadow position has changed.

Rescaling or histogram normalization may change intensity values, but it cannot necessarily move the physical shadow into the corresponding position.

Therefore:

```text
scale normalization
        ≠
illumination normalization
```

The project recommends testing raw intensity against gradients, edges, or other structure-focused representations under difficult Sun-angle conditions.

---

# 24. Scale and Sensor Differences

Scale differences become especially important when different sensors are involved.

The three major Chandrayaan-2 inputs are not equivalent image sources.

The project materials describe:

- OHRC as high-detail visible panchromatic imagery;
- TMC-2 as coarser panchromatic terrain imagery;
- IIRS as imaging infrared hyperspectral data with much coarser spatial sampling.

The recommended approach is sensor-aware preprocessing rather than forcing all sensors through an identical image-processing path.

---

# 25. IIRS and the Scale Problem

IIRS requires particular care because the problem is not simply spatial scale.

It is also a modality and representation problem.

A full hyperspectral measurement cannot automatically be treated as an ordinary 2D grayscale image.

The project guidance recommends starting with a simple registration-friendly representation such as:

- a selected band
- a PCA/composite representation
- another structural representation

before attempting more complicated approaches.

Therefore:

```text
IIRS → spatial scale problem
       +
       spectral representation problem
```

not merely:

```text
IIRS → resize → solve
```

---

# 26. Scale and Structural Information

A useful principle for ChandraMap is:

> **Compare information, not pixel count.**

Suppose:

```text
Reference:
very high-resolution crater

Source:
very coarse measurement
```

The reference contains details that the source does not.

If the reference is simply resized upward or the source is artificially enlarged, the information imbalance remains.

A more meaningful approach is:

```text
High-resolution reference
          ↓
Scale-aware reduction
          ↓
Comparable effective scale
          ↓
Coarse correspondence
```

This does not make the images physically identical.

It reduces an avoidable mismatch in representation scale.

---

# 27. Scale and Information Loss

Downsampling itself also causes information loss.

For example:

```text
Fine image
████████████████
████████████████
████████████████
████████████████

        ↓

Coarse representation
████████
████████
```

Fine structures may disappear.

This means a reference pyramid should not be interpreted as containing equally useful information at every level.

Each pyramid level represents a different information regime.

The appropriate level depends on:

- source resolution
- reference resolution
- feature size
- terrain structure
- noise
- illumination
- sensor modality
- matching method

---

# 28. Scale Selection

A scale level should be selected based on the expected correspondence problem.

A conceptual scale-selection process is:

```text
Known / estimated source GSD
             │
             ▼
Reference GSD
             │
             ▼
Expected scale ratio
             │
             ▼
Candidate pyramid levels
             │
             ▼
Evaluate correspondence
             │
             ▼
Select experimentally supported level(s)
```

If reliable metadata is available, it should be retained and used appropriately.

The project feedback specifically recommends storing pixel scale, footprint, and lighting/viewing metadata when available.

---

# 29. Scale Ratio

For two simplified image representations, a scale ratio can be defined conceptually as:

$$
r = \frac{\text{GSD}_{A}}{\text{GSD}_{B}}
$$

where the exact convention should be documented consistently.

For example, if:

```text
GSD_A = 1 m/pixel
GSD_B = 5 m/pixel
```

then the absolute ratio between the effective sampling scales is:

$$
|r| = 5
$$

This means a physical feature may occupy roughly five times as many pixels in the finer representation under simplified conditions.

The ratio alone does not determine matching difficulty.

It should be considered alongside:

- feature size
- sensor characteristics
- illumination
- viewing geometry
- image processing
- terrain complexity

---

# 30. Scale Does Not Mean Uniform Scaling Everywhere

A single scalar scale ratio assumes that the difference between two image representations can be described approximately by one global factor.

That assumption may fail.

For example:

```text
Image A
───────────────
     ↘
       ↘

Image B
───────────────
   ↘
     ↘
```

Differences in:

- viewing geometry
- projection
- terrain relief
- orthorectification
- sensor geometry

can create spatially varying effects.

Therefore, scale mismatch and geometric distortion should not be treated as the same phenomenon.

---

# 31. Scale and Lunar Terrain Relief

The Moon is not a flat poster.

Crater walls, ridges, slopes, and relief can interact with imaging geometry.

The project guidance explicitly cautions against assuming that one global homography is always sufficient, particularly for raw imagery or stronger geometric differences.

Therefore:

```text
Scale difference
        +
Terrain relief
        +
Viewing geometry
        ↓
Potentially spatially varying image relationships
```

A scale pyramid can help address representation scale.

It does not automatically solve terrain-induced geometric distortion.

---

# 32. Scale and Spatial Coverage

A scale strategy should be evaluated not only by how many matches are found but also by where those matches occur.

Consider:

```text
Case A

●       ●

    ●

●       ●
```

versus:

```text
Case B

●●●●●●●
●●●●●●●
```

Both may contain many inliers.

Case B may provide weaker geometric control if all matches are concentrated in one small region.

The project therefore treats spatial coverage as an explicit metric alongside inlier count and inlier ratio.

---

# 33. Scale and Candidate/Inlier Counts

A scale experiment should distinguish at least:

### Candidate matches

Correspondences proposed by the local matching stage.

### Verified inliers

Candidate correspondences that remain geometrically consistent after robust model estimation.

A possible result table is:

| Scale condition           | Candidate matches | Verified inliers | Inlier ratio | Coverage | Check-point RMSE |
| ------------------------- | ----------------: | ---------------: | -----------: | -------: | ---------------: |
| Baseline scale            |             [TBD] |            [TBD] |        [TBD] |    [TBD] |            [TBD] |
| Moderate scale difference |             [TBD] |            [TBD] |        [TBD] |    [TBD] |            [TBD] |
| Large scale difference    |             [TBD] |            [TBD] |        [TBD] |    [TBD] |            [TBD] |
| Multi-scale pipeline      |             [TBD] |            [TBD] |        [TBD] |    [TBD] |            [TBD] |

No performance conclusion should be drawn until the actual experiment is run.

---

# 34. Scale and Registration Accuracy

A scale strategy is useful only if it improves the actual registration objective.

A visually attractive overlay is not enough.

The project recommends independent check points rather than evaluating the transformation only on the points used to fit it.

A useful evaluation chain is:

```text
Scale strategy
      ↓
Candidate correspondences
      ↓
RANSAC
      ↓
Verified inliers
      ↓
Transformation
      ↓
Independent check points
      ↓
Source-pixel RMSE
```

This prevents the scale experiment from becoming merely a feature-count comparison.

---

# 35. Scale Experiment Design

The scale question should be tested under controlled conditions.

A basic scale stress matrix could contain:

| Case | Scale condition                  | Illumination       | Purpose                          |
| ---- | -------------------------------- | ------------------ | -------------------------------- |
| A    | Similar effective scale          | Similar            | Baseline                         |
| B    | Moderate scale difference        | Similar            | Measure sensitivity              |
| C    | Large scale difference           | Similar            | Stress scale handling            |
| D    | Large scale difference           | Different          | Scale + illumination interaction |
| E    | Sensor-specific scale difference | Different modality | Cross-sensor stress              |
| F    | Low-feature terrain              | Scale difference   | Failure analysis                 |

The exact image pairs, GSD values, and thresholds are `[TBD]` until the benchmark is defined.

---

# 36. EXP-002: Scale Pyramid

The ChandraMap V1 experiment structure includes:

`experiments/v1/preprocessing/EXP-002-scale-pyramid/README.md`

This experiment should be understood as a controlled test of whether a multi-scale representation or pyramid strategy improves the registration pipeline under scale differences.

The project's build guidance specifically places a reference pyramid after the initial baseline and before more advanced matching approaches.

The conceptual hypothesis is:

> A scale-aware or pyramid-based search strategy may improve correspondence when the source and reference images have substantially different effective spatial scales.

This is a hypothesis, not an established ChandraMap result.

---

# 37. What EXP-002 Should Establish

A well-controlled scale-pyramid experiment should help answer questions such as:

1. Does a reference pyramid increase successful candidate correspondence under scale stress?
2. Does it increase the number of geometrically verified inliers?
3. Does it improve spatial coverage?
4. Does it reduce independent check-point RMSE?
5. Does it reduce failure rate?
6. What computational cost does the pyramid introduce?
7. Does the benefit remain under illumination changes?
8. Does the benefit differ between sensors?
9. At what scale differences does the approach stop helping?

The actual answers are `[TBD]` until the experiment is executed.

---

# 38. Baseline vs Scale-Aware Pipeline

A controlled comparison should preserve the same underlying image pairs.

Conceptually:

```text
Same image pairs
       │
       ├───────────────┐
       │               │
       ▼               ▼
SIFT baseline     Scale-aware pipeline
       │               │
       ▼               ▼
Candidate matches   Candidate matches
       │               │
       ▼               ▼
RANSAC              RANSAC
       │               │
       ▼               ▼
Inliers             Inliers
       │               │
       ▼               ▼
Transform           Transform
       │               │
       ▼               ▼
Check points        Check points
       │               │
       └───────┬───────┘
               ▼
          Compare metrics
```

The feedback recommends running the same test pairs through baseline and improved pipeline variants so that improvements can be attributed to actual pipeline changes.

---

# 39. Important Experimental Control

When testing scale, avoid changing several unrelated variables at the same time unless the experiment explicitly targets their interaction.

For a clean scale experiment, try to keep constant:

- image pair
- region
- matching method
- geometric model
- RANSAC configuration
- evaluation points
- evaluation metric
- preprocessing other than the tested scale operation

Then vary:

```text
scale condition
```

This makes the measured difference more interpretable.

---

# 40. Scale and Illumination Interaction Experiment

A separate experiment can explicitly test interaction.

For example:

```text
                 Similar illumination
                       │
            ┌──────────┴──────────┐
            │                     │
       Small scale           Large scale
       difference            difference


                 Different illumination
                       │
            ┌──────────┴──────────┐
            │                     │
       Small scale           Large scale
       difference            difference
```

This allows the project to distinguish:

```text
scale difficulty
```

from:

```text
scale + illumination difficulty
```

The project guidance specifically recommends separate stress cases for scale and Sun-angle variation.

---

# 41. Scale and Gradient/Structural Representation

Scale interacts with the representation used for matching.

EXP-003 addresses gradient representation.

A scale difference can alter raw intensity patterns, while structural representations may preserve some larger-scale terrain boundaries more consistently.

Potential representations include:

- grayscale intensity
- gradients
- edges
- structural maps
- other experimentally justified representations

However:

> A representation should not be assumed to solve scale variation simply because it emphasizes structure.

Its usefulness must be measured.

The project feedback recommends comparing raw grayscale with gradient/edge or other structure-focused representations for difficult illumination conditions.

---

# 42. Scale and Learned Matchers

The project may later test methods such as:

- ALIKED + LightGlue
- LoFTR
- RIFT/CFOG-inspired approaches

These methods should not be assumed to solve scale differences automatically.

The feedback specifically notes that learned terrestrial features are not automatically lunar-invariant and that LoFTR can still be affected by domain shift and extreme scale differences.

Therefore the scale question remains experimental:

```text
Does method X maintain correspondence
under ChandraMap's tested scale conditions?
```

not:

```text
Does method X claim scale robustness in its paper?
```

---

# 43. Scale and Retrieval

If ChandraMap uses global retrieval, scale handling can occur before local matching.

The intended architecture distinguishes global retrieval from local correspondence.

Conceptually:

```text
Reference images
      ↓
Reference tiles at useful scales
      ↓
Global descriptors
      ↓
Index
      ↓
Top-K candidate regions
      ↓
Local scale-aware matching
```

The project feedback emphasizes that FAISS performs vector similarity search and that reference data must first be prepared and indexed. Global retrieval and local matching are separate jobs.

Scale-aware retrieval should therefore not be confused with scale-invariant local correspondence.

---

# 44. Scale and Metadata

Scale handling should use available metadata where appropriate.

Potentially useful metadata includes:

- pixel scale
- GSD
- image dimensions
- footprint
- map projection
- acquisition information
- viewing geometry
- lighting information

The project feedback explicitly recommends preserving pixel scale, footprint, map projection, and lighting/viewing metadata when available.

Metadata is not a shortcut that invalidates image matching.

It is part of understanding the physical relationship between the images.

---

# 45. When Metadata Can Reduce the Scale Problem

Suppose the system knows:

```text
Source GSD
Reference GSD
Approximate footprint
Map projection
```

It can potentially make a better-informed choice of reference pyramid level or search scale.

Conceptually:

```text
Metadata
   ↓
Estimate effective scale relationship
   ↓
Choose candidate scale levels
   ↓
Perform image-based correspondence
```

This can reduce unnecessary search.

The project guidance explicitly states that using reliable metadata to restrict search is good engineering when such metadata is available.

---

# 46. Scale and Sub-Pixel Refinement

Sub-pixel refinement is a later stage of the pipeline.

The project sequence is:

```text
Candidate matches
       ↓
RANSAC + initial model
       ↓
Verified inliers
       ↓
Sub-pixel refinement
       ↓
Refit final transform
```

This order is important because refinement should operate on reliable correspondences rather than attempting to refine arbitrary candidate matches.

Scale handling and sub-pixel refinement therefore solve different problems.

### Scale handling

Attempts to obtain useful correspondence despite differences in image representation scale.

### Sub-pixel refinement

Attempts to localize already reliable correspondences more precisely.

A sub-pixel method cannot recover spatial information absent from the source sensor.

---

# 47. Scale and the Limit of Sub-Pixel Claims

Suppose a source image has very coarse spatial sampling.

A mathematical optimizer may still produce a fractional-pixel coordinate such as:

```text
x = 152.37
y = 91.64
```

That does not automatically mean that the underlying physical terrain position is known to an arbitrarily fine physical precision.

The project guidance therefore recommends reporting sub-pixel error in source-image pixels first and converting to metres only when GSD and projection make the conversion meaningful.

---

# 48. What a Successful Scale Experiment Would Mean

Suppose a multi-scale method produces:

- more verified inliers,
- better spatial coverage,
- lower independent check-point RMSE,
- lower failure rate,

under the same scale-stress image pairs.

A scientifically supported conclusion could be:

> The tested multi-scale configuration improved registration performance under the tested scale conditions.

That is a valid experimental statement.

It does **not** automatically establish:

> The system is scale-invariant.

Nor does it establish:

> The method works across all lunar scales.

The conclusion must remain within the tested conditions.

---

# 49. What a Failed Scale Experiment Would Mean

Failure is also scientifically useful.

For example:

```text
Large scale difference
        ↓
Pyramid added
        ↓
Inlier count increases
        ↓
RMSE remains poor
        ↓
Coverage remains clustered
```

This could indicate that the pyramid helped feature discovery without solving the final registration problem.

Likewise:

```text
Candidate matches ↑
Inliers ↓
```

could indicate that scale processing increased ambiguous candidates rather than useful correspondences.

The project philosophy explicitly emphasizes keeping failure cases and measuring actual outcomes rather than relying on decorative confidence scores.

---

# 50. Failure Modes

Important scale-related failure modes include:

## 50.1 Feature disappearance

Fine structures do not survive coarse sampling.

## 50.2 Feature merging

Multiple fine structures become one coarse structure.

## 50.3 Descriptor instability

The local descriptor changes too much across scales.

## 50.4 Excessive scale gap

The difference exceeds the practical robustness range of the matcher.

## 50.5 Scale + illumination interaction

A scale difference and changed shadows jointly alter appearance.

## 50.6 Scale + modality interaction

Different sensors represent the same terrain differently.

## 50.7 Spatially clustered matches

The scale strategy finds matches, but not across enough of the overlap.

## 50.8 Incorrect geometric model

Scale is handled adequately, but the remaining image relationship cannot be represented by the selected global transformation.

## 50.9 False confidence from visual overlays

A flexible transformation produces an attractive overlay despite weak underlying correspondences.

---

# 51. Scale Is Not the Only Cause of Registration Failure

When a registration fails, the diagnosis should not immediately be:

```text
scale problem
```

Possible causes include:

```text
Scale
Illumination
Sensor modality
Spectral representation
Viewing geometry
Projection
Terrain relief
Low texture
Repeated structures
Incorrect candidates
Geometric model
Poor spatial coverage
Insufficient source information
```

The project feedback explicitly recommends diagnosing failure as scale, illumination, modality, retrieval, geometry, or sub-pixel refinement rather than assuming one cause.

---

# 52. Scale Stress-Test Design

A useful V1 stress-test matrix is:

| Stress case         | Main variable                         | Secondary variables           | Primary measurements                  |
| ------------------- | ------------------------------------- | ----------------------------- | ------------------------------------- |
| Easy pair           | Moderate scale difference             | Similar illumination          | Inliers, coverage, RMSE               |
| Scale stress        | Large GSD difference                  | Similar illumination          | Inliers, coverage, RMSE, failure rate |
| Sun-angle stress    | Similar scale                         | Large illumination difference | Performance drop                      |
| Scale + Sun         | Large scale + illumination difference | Both                          | Robustness                            |
| Modality stress     | Different sensor representation       | Scale + modality              | Inliers, coverage, RMSE               |
| Geometry stress     | Stronger viewing/relief differences   | Scale + geometry              | Residual structure                    |
| Low-feature terrain | Weak local structure                  | Scale                         | Failure behavior                      |

The exact benchmark composition remains `[TBD]`.

---

# 53. Metrics for Scale Experiments

Scale experiments should use metrics connected to the actual registration objective.

## 53.1 Candidate match count

Number of proposed local correspondences.

Useful for diagnosing matcher behavior, but insufficient by itself.

---

## 53.2 Verified inlier count

Number of candidate correspondences surviving geometric verification.

More meaningful than raw candidate count.

---

## 53.3 Inlier ratio

$$
\text{Inlier Ratio}
=
\frac{\text{Verified Inliers}}
{\text{Candidate Matches}}
$$

Useful for evaluating correspondence quality.

---

## 53.4 Spatial coverage

Measures whether verified matches are distributed across the overlap.

Possible approaches include:

- grid coverage
- convex-hull coverage

The project specifically recommends spatial coverage as a metric.

---

## 53.5 Independent check-point RMSE

Measures registration error on points not used to fit the transformation.

This should be a central accuracy metric.

---

## 53.6 Failure rate

Useful for determining whether a method works consistently across image pairs rather than only on selected examples.

---

## 53.7 Runtime

A scale pyramid may improve correspondence while increasing computational cost.

Both accuracy and computational requirements should therefore be recorded.

---

# 54. What Not to Optimize

Do not optimize solely for:

```text
maximum number of matches
```

or:

```text
best-looking overlay
```

or:

```text
lowest training/example error
```

A scale strategy should instead be evaluated through a combination of:

```text
verified correspondence quality
+
spatial distribution
+
independent registration accuracy
+
failure behavior
+
runtime
```

---

# 55. Independent Check Points

Transformation fitting and evaluation should remain separate.

Incorrect:

```text
Inliers
   ↓
Fit transform
   ↓
Evaluate same inliers
```

Preferred:

```text
Verified control points
       │
       ├──────→ Fit transformation
       │
       ▼
Independent check points
       │
       ▼
Evaluate transformation
```

The project feedback explicitly warns that evaluating on the same points used for fitting can make the error appear better than actual registration quality.

---

# 56. Scale and Ground Truth

Scale experiments require a trustworthy evaluation reference.

Potential sources may include:

- challenge-provided ground truth
- independently checked tie points
- reference imagery with sufficiently reliable geometry
- project-defined control/check points

The exact ground-truth construction for the final ChandraMap benchmark is:

`[To be verified]`

Until it is defined, numerical claims about scale accuracy should not be treated as established project results.

---

# 57. Scale and Ground Error

Ground error can be useful when:

- GSD is known,
- projection is appropriate,
- coordinate reference is known,
- reference truth is sufficiently accurate.

Otherwise, source-image pixel error should remain primary.

Conceptually:

```text
Image-space error
        ↓
Validate physical scale metadata
        ↓
Validate projection / geometry
        ↓
Optional physical-unit conversion
```

This prevents a mathematically precise pixel measurement from being converted into an unjustified physical accuracy claim.

---

# 58. Relationship to V1 Pipeline

The current V1 conceptual pipeline can be understood as:

```text
Source Image
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
RANSAC
      ↓
Verified Inliers
      ↓
Transformation
      ↓
Independent Check-Point Evaluation
```

The project feedback describes the broader design as sensor-aware preprocessing, multi-scale search, local matching, geometric verification, refinement, and measurable evaluation.

Scale handling is therefore one component of a larger correspondence system.

---

# 59. Relationship to Other V1 Experiments

| Experiment                      | Scale-related question                                                                |
| ------------------------------- | ------------------------------------------------------------------------------------- |
| EXP-001 SIFT Baseline           | How does the baseline behave before scale-specific improvements?                      |
| EXP-002 Scale Pyramid           | Does a multi-scale representation improve performance under scale stress?             |
| EXP-003 Gradient Representation | Does a structural representation improve robustness when scale and appearance differ? |
| EXP-004 Affine vs Homography    | Does the selected geometric model explain residuals after scale handling?             |
| EXP-005 Residual Analysis       | What spatial patterns remain after registration?                                      |
| EXP-006 Subpixel Refinement     | Can verified correspondences be localized more precisely after scale-aware matching?  |

These experiments should remain separate enough that their effects can be interpreted.

---

# 60. Scale and EXP-001 Baseline

EXP-001 provides the baseline against which later changes can be compared.

The conceptual baseline is:

```text
SIFT
  ↓
descriptor matching
  ↓
candidate filtering
  ↓
RANSAC
  ↓
transformation
  ↓
evaluation
```

The scale experiment should not replace the baseline.

It should answer:

> What changes when explicit scale handling is added?

---

# 61. Scale and EXP-003

Scale and representation are related.

A scale change can modify local gradients and edges.

A gradient representation may suppress some illumination-related intensity changes, but it is not automatically a solution to spatial-resolution differences.

Therefore EXP-002 and EXP-003 should not be interpreted as solving the same problem.

A useful distinction is:

```text
EXP-002
"What happens when we change scale handling?"

EXP-003
"What happens when we change image representation?"
```

The experiments can later be combined into a full pipeline only after their individual effects are understood.

---

# 62. Scale and EXP-004

If scale-aware matching produces a different set of correspondences, the transformation stage may also change.

This means EXP-004 should use controlled correspondence inputs when possible.

Otherwise:

```text
scale change
   ↓
different matches
   ↓
different transformation
   ↓
different residual
```

can make it difficult to determine whether an observed improvement came from scale handling or from the geometric model.

---

# 63. Scale and EXP-005 Residual Analysis

Residual analysis is particularly important for determining whether scale handling has solved the relevant problem.

Possible patterns:

```text
Random small residuals
→ potentially well-explained geometry
```

```text
Residuals increase toward one side
→ possible systematic geometry/projection issue
```

```text
Residuals concentrated around one region
→ insufficient spatial coverage
```

```text
Large residuals near small structures
→ possible scale/resolution limitation
```

These are diagnostic hypotheses, not automatic interpretations.

The actual cause should be established using controlled analysis.

---

# 64. Scale and EXP-006 Sub-Pixel Refinement

Scale-aware matching should precede fine localization.

The intended sequence is:

```text
Scale-aware correspondence
        ↓
Geometric verification
        ↓
Reliable inliers
        ↓
Sub-pixel refinement
        ↓
Final transformation
```

Sub-pixel refinement should not be used to disguise poor scale handling.

If the source does not contain sufficient spatial information, the refinement stage cannot legitimately create that information.

---

# 65. A Useful Conceptual Boundary

The ChandraMap pipeline should distinguish:

```text
SEARCH SCALE
```

from:

```text
MEASUREMENT PRECISION
```

Search scale determines where and at what representation level correspondence is attempted.

Measurement precision determines how accurately an already-supported correspondence can be localized.

These are related but different.

---

# 66. Scale Robustness Is Data-Dependent

A method may work well for:

```text
OHRC ↔ LRO NAC
```

and poorly for:

```text
IIRS ↔ visible reference
```

without contradiction.

The second case may involve:

- larger spatial-resolution differences
- spectral differences
- lower spatial detail
- different noise characteristics
- different structural representations

Therefore scale robustness should be reported by relevant sensor/path rather than collapsed into a single mixed result.

The project feedback explicitly recommends separate sensor results rather than one mixed average when sensors have substantially different behavior.

---

# 67. Scale Robustness Is Range-Dependent

A statement such as:

> "The method is robust to scale."

is incomplete.

A scientifically useful statement specifies:

```text
robust under:
- which sensors?
- which GSD range?
- which terrain?
- which illumination conditions?
- which representation?
- which matcher?
- which geometric model?
- which evaluation metric?
```

For example:

> "The tested configuration retained successful registration across the evaluated GSD conditions for the tested image pairs."

This is appropriately bounded.

---

# 68. What Cannot Be Concluded

A successful scale experiment cannot automatically establish that:

- ChandraMap is universally scale-invariant.
- The method works at arbitrary GSD ratios.
- Upsampling recovered missing information.
- One pyramid level is universally optimal.
- SIFT is sufficient for all lunar sensors.
- A scale pyramid solves illumination changes.
- A scale pyramid solves sensor modality differences.
- A scale pyramid solves terrain-induced geometric distortion.
- Sub-pixel refinement recovers unavailable spatial information.
- Results on one sensor generalize to another sensor.
- Results on one terrain type generalize to all lunar terrain.

Each of these would require additional evidence.

---

# 69. Open Research Questions

The scale problem leaves several important research questions.

## RQ-1 — Effective scale range

What range of GSD differences can the baseline SIFT pipeline tolerate on the selected ChandraMap benchmark?

**Status:** `[TBD]`

---

## RQ-2 — Pyramid benefit

Does a reference image pyramid improve correspondence under large scale differences?

**Related experiment:** `EXP-002`

**Status:** `[Planned / To be verified]`

---

## RQ-3 — Optimal scale level

Does one pyramid level consistently provide the best correspondence, or does the useful level depend on the sensor pair and terrain?

**Status:** `[TBD]`

---

## RQ-4 — Scale and illumination

How does scale robustness change when the same region is observed under substantially different Sun angles?

**Status:** `[TBD]`

---

## RQ-5 — Scale and modality

How much of the observed correspondence degradation is caused by scale versus sensor/modality differences?

**Status:** `[TBD]`

---

## RQ-6 — Scale and spatial coverage

Does scale-aware matching improve the geographic distribution of verified correspondences, rather than only increasing match count?

**Status:** `[TBD]`

---

## RQ-7 — Scale and geometric models

After scale handling, are residuals still better explained by a global affine/homography model, or is additional geometry required?

**Related experiments:** `EXP-004`, `EXP-005`

**Status:** `[TBD]`

---

## RQ-8 — Scale and sub-pixel localization

Does scale-aware correspondence provide sufficiently stable control points for useful sub-pixel refinement?

**Related experiment:** `EXP-006`

**Status:** `[TBD]`

---

# 70. Recommended Scientific Language

Prefer:

> "multi-scale matching"

when describing the implementation.

Prefer:

> "scale-robust under the tested conditions"

when experiments demonstrate robustness.

Prefer:

> "scale-aware"

when the method explicitly considers scale but evidence is incomplete.

Avoid:

> "fully scale-invariant"

unless the evidence supports that stronger claim.

Avoid:

> "resolution independent"

unless demonstrated.

Avoid:

> "works at any scale"

unless experimentally established across an appropriately broad range.

---

# 71. Scale Experiment Reporting Template

The following template can be used when documenting a scale experiment.

```markdown
## Scale Experiment

### Objective

Determine whether [scale strategy] improves lunar image correspondence
under [defined scale conditions].

### Dataset

- Source sensor:
- Reference sensor:
- Image pairs:
- Terrain types:
- Source GSD:
- Reference GSD:
- Scale ratio:
- Illumination condition:
- Projection:
- Metadata availability:

### Baseline

- Matcher:
- Representation:
- Geometric model:
- RANSAC configuration:
- Evaluation protocol:

### Scale Method

- Pyramid:
- Pyramid levels:
- Downsampling method:
- Scale-selection rule:
- Coarse-to-fine procedure:

### Metrics

- Candidate matches:
- Verified inliers:
- Inlier ratio:
- Spatial coverage:
- Independent check-point RMSE:
- Failure rate:
- Runtime:

### Results

| Condition   | Candidates | Inliers | Inlier Ratio | Coverage |  RMSE | Runtime |
| ----------- | ---------: | ------: | -----------: | -------: | ----: | ------: |
| Baseline    |      [TBD] |   [TBD] |        [TBD] |    [TBD] | [TBD] |   [TBD] |
| Scale-aware |      [TBD] |   [TBD] |        [TBD] |    [TBD] | [TBD] |   [TBD] |

### Interpretation

[Describe only observations supported by the measured results.]

### Failure Cases

[Document unsuccessful pairs and suspected causes.]

### Limitations

[State the tested scale range, sensors, terrain and other limitations.]

### Conclusion

[State only the conclusion supported by the experiment.]
```

---

# 72. Recommended Scale Metadata Record

For every scale-sensitive image pair, the following metadata should be retained where available:

```markdown
### Scale Metadata

- Source image:
- Reference image:
- Source sensor:
- Reference sensor:
- Source pixel dimensions:
- Reference pixel dimensions:
- Source GSD:
- Reference GSD:
- Approximate scale ratio:
- Projection:
- Footprint:
- Viewing geometry:
- Illumination metadata:
- Product type:
- Calibration status:
- Resampling history:
- Pyramid level used:
- Scale-selection method:
- Notes:
```

Unknown values should be marked:

```text
[TBD]
[Not provided]
[To be verified]
```

rather than guessed.

---

# 73. Scale-Related Reproducibility Requirements

A scale experiment should record enough information for another researcher to reproduce the comparison.

At minimum:

- exact image identifiers
- source/reference sensor
- source/reference GSD where available
- image dimensions
- resampling method
- pyramid construction method
- pyramid levels
- scale-selection rule
- representation
- feature detector
- descriptor
- matching configuration
- geometric model
- RANSAC configuration
- evaluation points
- metrics
- software version
- experiment configuration
- result artifacts

The exact repository configuration format is:

`[To be verified]`

---

# 74. Resampling Provenance

Every resampling operation should be traceable.

For example:

```text
Original reference
      ↓
Downsample ×2
      ↓
Pyramid L1
      ↓
Downsample ×2
      ↓
Pyramid L2
```

The experiment record should make it possible to determine:

- what image was resampled,
- by what factor,
- using what method,
- for what purpose,
- and at what stage.

This prevents an experiment from becoming impossible to reproduce.

---

# 75. Avoiding Hidden Resampling

A particularly important implementation concern is accidental resampling.

For example:

```text
Image loader
   ↓
automatic resize
   ↓
matcher
```

could silently alter the effective scale.

Similarly:

```text
display code
   ↓
resize
   ↓
saved image
```

could create confusion if the resized image is later used as experimental input.

The experiment should distinguish:

```text
visualization resize
```

from:

```text
scientific resampling
```

---

# 76. Scale and Visualization

Visualization can be useful for diagnosing scale behavior.

Useful visualizations include:

- source/reference images at comparable display scale
- pyramid levels
- candidate matches
- RANSAC inliers
- spatial match distribution
- residual vectors
- registered overlays
- failed cases

However:

> A visually aligned overlay is evidence for inspection, not by itself a quantitative accuracy result.

The project emphasizes numerical error and independent check points as part of the first end-to-end milestone.

---

# 77. Scale Failure Analysis Checklist

When a scale experiment fails, inspect:

### Input

- Is the actual GSD known?
- Are the image products comparable?
- Was hidden resampling applied?
- Is the overlap correct?

### Features

- Are enough keypoints detected?
- Do keypoints survive at the chosen scale?
- Are they spatially distributed?

### Descriptors

- Are descriptors stable across the scale difference?
- Is the local appearance too different?

### Matching

- Are candidates concentrated in ambiguous terrain?
- Does the matcher produce many false candidates?

### Geometry

- Are RANSAC inliers spatially distributed?
- Does the transformation explain the residuals?

### Evaluation

- Was the transformation evaluated on independent points?
- Is the RMSE measured in source pixels?
- Is the result being compared on the same image pairs?

### Physical interpretation

- Does the source actually contain the spatial information required?
- Is the problem really scale, or is it illumination/modality/geometry?

---

# 78. Scale and Low-Feature Terrain

Scale problems become more difficult in smooth or repetitive terrain.

At fine scale:

```text
small terrain variations
████████████████████
```

may provide useful local structure.

After downsampling:

```text
██████████
```

those variations may disappear.

This can reduce the number of stable correspondences.

The project's stress-test design therefore includes low-feature terrain as a separate failure case.

---

# 79. Scale and Repetitive Terrain

Large-scale lunar terrain can contain repeated or visually similar structures.

For example:

```text
crater A ≈ crater B ≈ crater C
```

At a coarse scale, local distinctions may become weaker.

This can produce:

```text
correct physical region
        ↓
ambiguous local appearance
        ↓
multiple plausible matches
```

Scale-aware processing therefore needs geometric verification and spatial coverage rather than relying only on descriptor similarity.

---

# 80. Scale and Correspondence Uncertainty

Scale should be viewed as a source of uncertainty.

A correspondence can be uncertain because:

- the feature is undersampled,
- several structures merge,
- descriptors become ambiguous,
- illumination changes the appearance,
- sensor modality changes the representation,
- geometric distortion shifts the feature.

Thus:

```text
scale difference
      ↓
representation uncertainty
      ↓
correspondence uncertainty
      ↓
geometric uncertainty
      ↓
registration uncertainty
```

This provides a useful conceptual link between scale and final registration error.

---

# 81. Practical ChandraMap Principle

A useful project rule is:

> **Search coarse, match carefully, refine only where the source contains enough information.**

This reflects the project's recommendation to compare images at physically meaningful scales before fine alignment and to stop refinement where the source sensor does not contain sufficient spatial information.

---

# 82. Scale Strategy for V1

A conservative V1 scale strategy can be expressed as:

```text
1. Preserve original product metadata.
2. Determine source/reference effective scale where available.
3. Avoid treating upsampling as information recovery.
4. Build a reference pyramid where useful.
5. Search at comparable effective scales.
6. Perform local matching.
7. Verify candidates geometrically.
8. Inspect spatial coverage.
9. Refine only verified correspondences.
10. Evaluate on independent check points.
11. Report results by sensor/path and stress condition.
```

This is a research direction and experimental design principle, not a claim that every step is already implemented.

---

# 83. Scale Strategy for Different Sensors

A conceptual sensor-aware strategy is:

```text
OHRC
  ↓
Fine-resolution representation
  ↓
Compare with appropriately scaled reference
  ↓
Fine correspondence where supported


TMC-2
  ↓
Coarser structural representation
  ↓
Comparable reference scale
  ↓
Correspondence


IIRS
  ↓
Select/derive registration-friendly 2D representation
  ↓
Very coarse effective scale
  ↓
Large-scale structural correspondence
  ↓
Refinement only where justified
```

The project explicitly recommends separate sensor paths rather than forcing OHRC, TMC-2, and IIRS through one identical pipeline.

---

# 84. Scale and Cross-Sensor Registration

Cross-sensor registration combines multiple differences:

```text
                 CROSS-SENSOR
                     │
       ┌─────────────┼─────────────┐
       ▼             ▼             ▼
     Scale       Modality     Illumination
       │             │             │
       └─────────────┼─────────────┘
                     ▼
              Correspondence
                     │
                     ▼
                Geometry
```

This means that an experiment involving different sensors should not automatically attribute all performance differences to scale.

Sensor-specific results are therefore important.

---

# 85. Scale and Common Structural Representation

A possible strategy is to transform different sensor inputs into representations that preserve stable terrain structure.

Conceptually:

```text
OHRC ────────┐
             │
TMC-2 ───────┼──→ Common structural representation
             │
IIRS ────────┘
```

But this should not mean:

```text
force all sensors to identical pixel resolution
```

Instead:

```text
sensor-specific preparation
        ↓
physically meaningful scale
        ↓
structural representation
        ↓
correspondence
```

The project feedback recommends this sensor-aware approach.

---

# 86. Scale and Method Selection

Literature may describe a method as scale-robust, but ChandraMap should still test it under its own conditions.

The correct chain is:

```text
Literature claim
      ↓
Method hypothesis
      ↓
ChandraMap implementation
      ↓
Controlled experiment
      ↓
Measured result
      ↓
Bounded conclusion
```

Not:

```text
Literature claim
      ↓
ChandraMap result
```

This distinction is essential for scientific credibility.

---

# 87. Evidence Hierarchy for Scale Claims

When making a scale-related claim, distinguish:

### External evidence

What published work or technical documentation reports.

### Project hypothesis

What ChandraMap expects might happen.

### Experimental evidence

What the ChandraMap benchmark actually measures.

### Project conclusion

What can reasonably be concluded from those measurements.

For example:

```text
External:
Method was designed to handle scale variation.

Hypothesis:
It may help ChandraMap under large GSD differences.

Experiment:
Test on fixed image pairs.

Result:
[TBD]

Conclusion:
[TBD]
```

---

# 88. Avoiding Overclaiming

Do not write:

> "Our pyramid makes ChandraMap scale-invariant."

unless the evidence supports that exact statement.

Prefer:

> "The pyramid provides a multi-scale representation intended to reduce effective scale mismatch during correspondence search."

After measured results:

> "The tested pyramid configuration improved [metric] under the evaluated scale conditions."

This language keeps implementation facts separate from experimental conclusions.

---

# 89. Scale and the "Same Feature" Assumption

At different spatial resolutions, "the same feature" can become an ambiguous concept.

For example:

```text
Fine representation:

crater rim
+ small boulder
+ shadow boundary
+ local texture


Coarse representation:

one broad crater structure
```

The correspondence may therefore be between:

```text
fine local structure
```

and:

```text
coarse structural region
```

rather than identical visual patterns.

This is one reason why purely pixel-level similarity may be insufficient for cross-scale correspondence.

---

# 90. Scale and Feature Semantics

A feature can change semantic appearance across scales.

At fine scale:

```text
ridge
```

may be represented as:

```text
multiple edges + texture
```

At coarse scale:

```text
ridge
```

may become:

```text
one broad intensity transition
```

A successful matcher therefore needs to tolerate changes in local representation while retaining correspondence to the same physical terrain.

---

# 91. Scale and Registration Objective

The final objective is not:

```text
make images have identical dimensions
```

nor:

```text
maximize descriptor similarity
```

nor:

```text
maximize number of matches
```

The objective is:

```text
obtain reliable, well-distributed correspondences
        ↓
estimate a defensible geometric relationship
        ↓
produce accurate registration
        ↓
demonstrate accuracy using independent evaluation
```

This keeps scale handling connected to the actual ChandraMap problem.

---

# 92. Recommended Decision Framework

When considering a new scale technique, ask:

| Question                                                | Answer  |
| ------------------------------------------------------- | ------- |
| What physical scale problem does it address?            | `[TBD]` |
| Does it change image sampling or only pixel dimensions? | `[TBD]` |
| Does it preserve source information?                    | `[TBD]` |
| What scale range is targeted?                           | `[TBD]` |
| Which sensors are tested?                               | `[TBD]` |
| Which terrain types are tested?                         | `[TBD]` |
| Does it interact with illumination?                     | `[TBD]` |
| Does it change the representation?                      | `[TBD]` |
| Does it improve candidate matches?                      | `[TBD]` |
| Does it improve verified inliers?                       | `[TBD]` |
| Does it improve spatial coverage?                       | `[TBD]` |
| Does it improve independent RMSE?                       | `[TBD]` |
| What runtime cost does it add?                          | `[TBD]` |
| What failure cases remain?                              | `[TBD]` |

---

# 93. Research Questions → Experiments → Evidence

The scale research chain should remain explicit:

```text
Scale difference exists
        ↓
What does it do to correspondence?
        ↓
Research question
        ↓
Hypothesis
        ↓
Controlled scale experiment
        ↓
Measured matches / inliers / coverage / RMSE
        ↓
Residual and failure analysis
        ↓
Bounded conclusion
```

This prevents the repository from turning a proposed method into an assumed result.

---

# 94. Current Project Evidence

The supplied project materials currently support the following scientific direction:

1. The lunar correspondence problem involves meaningful differences in spatial scale and GSD.
2. OHRC, TMC-2, and IIRS should not be treated as equivalent image sources.
3. Multi-scale search is a recommended part of the architecture.
4. A reference pyramid or comparable-scale processing is recommended for large resolution differences.
5. Upsampling does not recover missing spatial information.
6. Fine refinement should be limited by the actual information present in the source.
7. Scale should be evaluated using controlled stress tests.
8. Scale should be evaluated using actual registration metrics rather than visual appearance alone.
9. Sensor-specific results should be preserved.
10. Scale should be analyzed separately from illumination, modality, and geometry while also testing their interactions.

These points are supported by the supplied technical feedback and project materials.

What has **not** been established from the supplied material is a measured ChandraMap scale-robustness range, a universal optimal pyramid configuration, or a demonstrated universal scale-invariant method.

Those remain `[TBD]`.

---

# 95. Known vs Unknown

| Topic                                         | Current status                  |
| --------------------------------------------- | ------------------------------- |
| Scale differences are relevant to ChandraMap  | Established project requirement |
| GSD is important to scale interpretation      | Established                     |
| Upsampling does not recover missing detail    | Established project principle   |
| Multi-scale/reference-pyramid strategy        | Planned/recommended             |
| EXP-002 scale-pyramid experiment              | Planned experiment structure    |
| Exact pyramid levels                          | `[TBD]`                         |
| Exact downsampling method                     | `[TBD]`                         |
| Exact scale-ratio benchmark                   | `[TBD]`                         |
| Measured scale robustness                     | `[TBD]`                         |
| Universal scale invariance                    | Not established                 |
| Optimal scale level                           | `[TBD]`                         |
| Scale robustness across all sensors           | Not established                 |
| Scale robustness under all Sun angles         | Not established                 |
| Scale robustness under all viewing geometries | Not established                 |

---

# 96. Practical Rules

For ChandraMap scale experiments:

1. **Do not equate resizing with physical resolution.**
2. **Do not treat upsampling as information recovery.**
3. **Preserve GSD and product metadata where available.**
4. **Use comparable effective ground scales for coarse correspondence.**
5. **Use reference pyramids where they solve a demonstrated problem.**
6. **Keep multi-scale search distinct from local fine matching.**
7. **Do not call a pyramid "scale-invariant" without evidence.**
8. **Do not attribute illumination failures automatically to scale.**
9. **Do not treat IIRS as simply a resized grayscale image.**
10. **Do not optimize only for match count.**
11. **Inspect spatial coverage.**
12. **Evaluate transformations on independent check points.**
13. **Report source-pixel error before physical-unit conversion.**
14. **Keep sensor-specific results separate.**
15. **Record failure cases.**
16. **State the tested scale range explicitly.**
17. **Compare baseline and improved methods on the same pairs.**
18. **Let measured evidence determine whether a scale technique remains in the pipeline.**

---

# 97. Minimal Mental Model

The scale problem can be reduced to:

```text
Same physical Moon
       │
       ├───────────────┐
       ▼               ▼
   Fine sampling   Coarse sampling
       │               │
       ▼               ▼
 Many pixels       Fewer pixels
       │               │
       └───────┬───────┘
               ▼
       Different appearance
               │
               ▼
       Correspondence problem
               │
               ▼
       Multi-scale strategy
               │
               ▼
       Geometric verification
               │
               ▼
       Independent evaluation
```

The purpose of scale handling is not to manufacture missing detail.

It is to make correspondence more physically and computationally meaningful.

---

# 98. Final Scientific Position

Scale variation is a fundamental part of the ChandraMap lunar correspondence problem because the same physical terrain can be represented at different spatial sampling scales.

The correct response is not simply to resize every image to the same dimensions.

A scientifically defensible scale strategy should:

```text
respect physical sampling
        ↓
preserve metadata
        ↓
compare meaningful effective scales
        ↓
use multi-scale search where justified
        ↓
perform local correspondence
        ↓
verify geometry
        ↓
evaluate independent points
        ↓
report failures and limitations
```

The central distinction is:

> **Scale-aware processing is a method design choice; scale robustness is an experimental result; scale invariance is a stronger scientific claim.**

For ChandraMap V1, the appropriate question is therefore not whether the pipeline can be called "scale-invariant" in the abstract.

The useful question is:

> **How reliably can ChandraMap establish accurate, well-distributed lunar correspondences as the effective spatial scale difference between source and reference imagery increases?**

That question can be answered through controlled experiments, beginning with the SIFT baseline and the planned `EXP-002-scale-pyramid` study, followed by residual, geometric, representation, and sub-pixel analysis.

Until those measurements exist, scale robustness should remain a research hypothesis rather than a claimed capability.

---

# 99. Related ChandraMap Documentation

## Research

- `research/README.md`
- `research/literature/README.md`
- `research/notes/`

## Related Research Notes

- `research/notes/ground-truth-design.md`
- `research/notes/scale-invariance.md`

> Additional research-note files: `[To be verified]`

## V1 Experiments

- `experiments/v1/README.md`
- `experiments/v1/baseline/EXP-001-sift-baseline/README.md`
- `experiments/v1/preprocessing/EXP-002-scale-pyramid/README.md`
- `experiments/v1/preprocessing/EXP-003-gradient-representation/README.md`
- `experiments/v1/geometry/EXP-004-affine-vs-homography/README.md`
- `experiments/v1/geometry/EXP-005-residual-analysis/README.md`
- `experiments/v1/refinement/EXP-006-subpixel-refinement/README.md`

## Experiment Template

- `experiments/templates/EXPERIMENT_TEMPLATE.md`

## Benchmarks

- `benchmarks/`

The exact benchmark protocol and final scale-stress dataset remain `[TBD]` unless defined elsewhere in the repository.

---

# 100. Source Basis

This research note is grounded primarily in the ChandraMap project materials and repository context supplied for this project, including:

- `SIH26166 Silarlar PS.pdf`
- `Aryan_SIH_26166_Lunar_Image_Correspondence_Feedback.pptx`
- `Aryan_Lunar_Image_Registration_Feedback.pdf`
- ChandraMap V1 experiment structure and terminology provided in the project repository context

The supplied feedback specifically supports the project's multi-scale direction, the distinction between physical scale and pixel count, reference-pyramid/coarse-to-fine processing, sensor-aware handling, independent check-point evaluation, and the warning against overclaiming scale invariance.

Specific implementation details, benchmark values, exact scale thresholds, measured results, and final dataset definitions are intentionally marked `[TBD]` where they were not established by the supplied project materials.
