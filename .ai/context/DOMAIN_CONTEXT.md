# ChandraMap Domain Context

This document provides the scientific and technical domain context required to work correctly on **ChandraMap**.

ChandraMap deals with lunar image correspondence and registration across sensors, resolutions, illumination conditions, viewing geometries, and image modalities. These are planetary remote-sensing problems, not merely generic image-matching problems.

The purpose of this file is to help engineers and AI coding agents understand the **physical and scientific constraints behind the software** before changing registration, preprocessing, matching, geospatial, evaluation, or research components.

This document primarily answers:

> **What scientific facts and physical constraints must I understand before modifying ChandraMap?**

It does not define ChandraMap's complete architecture, coding rules, exact dataset formats, or benchmark specifications. Those concerns belong in their respective project documents.

---

## 1. Why Domain Context Matters

Many computer-vision algorithms assume that two images of the same scene will preserve enough visual structure for direct feature comparison.

Lunar imagery can violate that assumption.

The same physical terrain can appear substantially different because observations may differ in:

- sensor
- spectral response
- spatial resolution
- ground sampling distance
- Sun angle
- shadow direction
- illumination
- viewing geometry
- map projection
- terrain relief
- preprocessing level
- noise characteristics
- partial overlap

A technically valid ChandraMap implementation must therefore reason about both:

```text
Image Appearance
        +
Physical Meaning
```

A correspondence that looks visually plausible is not automatically a correspondence between the same physical lunar locations.

---

## 2. The Lunar Imaging Environment

### 2.1 Airless Surface and Directional Illumination

The lunar surface is observed under strong directional solar illumination.

The absence of an Earth-like atmosphere means illumination can produce strong contrast between:

- illuminated slopes
- crater walls
- ridges
- shadowed terrain

Changes in solar geometry can significantly change the appearance of the same surface.

This has direct consequences for image matching.

---

### 2.2 Terrain Structure

Lunar terrain may contain structures such as:

- impact craters
- crater rims
- ridges
- ejecta patterns
- depressions
- slopes
- boulders where spatial resolution permits
- other local relief structures

Many of these structures can provide useful correspondence cues.

However, their appearance depends on:

- image scale
- illumination
- viewing geometry
- sensor modality

---

### 2.3 Repetitive Features

Craters are useful landmarks, but they are also highly repetitive.

Multiple regions may contain visually similar circular or partially illuminated structures.

This creates ambiguity.

For example:

```text
Visually similar crater
        ≠
Same physical crater
```

A descriptor match therefore needs geometric and contextual verification.

---

### 2.4 Low-Feature Terrain

Some lunar regions may contain:

- smooth terrain
- weak texture
- repetitive terrain
- limited distinctive corners
- insufficient stable features

A matching method may legitimately fail in such areas.

The system should not assume that every image pair must produce a valid registration.

---

## 3. Spatial Resolution and Ground Sampling Distance

### 3.1 Spatial Resolution

Spatial resolution describes the approximate physical detail represented by an imaging system.

ChandraMap may work with instruments operating at substantially different spatial scales.

Approximate project-level examples include:

| Instrument          |                             Approximate Spatial Scale | General Role                                |
| ------------------- | ----------------------------------------------------: | ------------------------------------------- |
| Chandrayaan-2 OHRC  | ~0.25–0.32 m/pixel depending on product/documentation | Fine terrain detail                         |
| Chandrayaan-2 TMC-2 |                                            ~5 m/pixel | Terrain-scale structural imagery            |
| Chandrayaan-2 IIRS  |                             ~80 m/pixel spatial scale | Coarse hyperspectral/imaging-IR observation |
| LRO NAC             |                            Product/geometry dependent | High-resolution lunar reference imagery     |
| LRO WAC             |           Broader-area, lower-detail context than NAC | Wide-area reference/context                 |

These values are broad instrument-level context.

Actual processing should use product metadata where available.

---

### 3.2 Ground Sampling Distance

**Ground Sampling Distance (GSD)** describes approximately how much physical ground distance corresponds to one image pixel.

For example:

```text
5 m/pixel
```

means approximately five metres of lunar surface are represented by one pixel under the relevant product geometry.

GSD matters because pixel dimensions alone do not define physical image scale.

---

### 3.3 Pixel Dimensions vs Physical Ground Scale

Two images can both be:

```text
1024 × 1024 pixels
```

while representing very different physical surface areas.

For example:

```text
Image A
1024 × 1024
0.5 m/pixel

Image B
1024 × 1024
80 m/pixel
```

The arrays have the same dimensions, but their physical information content is very different.

Matching logic should therefore consider, when available:

- GSD
- footprint
- projected extent
- sensor
- acquisition geometry

Do not infer physical equivalence from image width and height.

---

### 3.4 Upsampling Does Not Recover Detail

This is a non-negotiable domain rule:

> **Interpolation can create more digital samples. It cannot recreate spatial information that the sensor never measured.**

Conceptually:

```text
80 m/pixel observation
        ↓
Interpolation
        ↓
Larger pixel array
```

does **not** become:

```text
Sub-metre physical terrain information
```

Upsampling may be useful for:

- numerical convenience
- visualization
- matching implementation requirements

but it is not physical detail recovery.

---

### 3.5 Physical Scale vs Digital Zoom

Distinguish:

```text
Physical scale
```

from:

```text
Displayed / resampled image size
```

Changing display scale does not change GSD.

This distinction matters in:

- preprocessing
- visualization
- multi-scale matching
- accuracy reporting
- benchmark interpretation

---

### 3.6 Physically Meaningful Multi-Scale Comparison

When source and reference imagery have substantially different GSD, prefer comparisons at physically meaningful scales.

A useful conceptual strategy is:

```text
High-Resolution Reference
        ↓
Build Resolution Pyramid
        ↓
Select Comparable Effective Scale
        ↓
Coarse Correspondence
        ↓
Fine Refinement
only where source information supports it
```

Possible mechanisms include:

- downsampling higher-resolution imagery
- reference pyramids
- coarse-to-fine search
- GSD-aware candidate selection

Do not request geometric precision from a source image beyond the information physically available in that source.

---

## 4. Chandrayaan-2 Sensor Context

The primary Chandrayaan-2 instruments relevant to ChandraMap should not be treated as interchangeable cameras.

---

### 4.1 OHRC

**OHRC — Orbiter High Resolution Camera**

General characteristics:

- visible/panchromatic lunar imaging
- very high spatial resolution
- approximately `0.25–0.32 m/pixel` depending on product/documentation
- capable of showing fine terrain structure

Registration implications:

- useful for fine local correspondence
- may support detailed tie-point placement
- may contain terrain information absent from coarser instruments

However, high resolution does not automatically make matching easy.

OHRC matching can still be affected by:

- different illumination
- weak or partial overlap
- projection differences
- repetitive terrain
- viewing geometry

Actual product metadata should be preferred over broad instrument-level approximations.

---

### 4.2 TMC-2

**TMC-2 — Terrain Mapping Camera-2**

General characteristics:

- panchromatic terrain imagery
- approximately `5 m/pixel`
- useful for larger terrain structures
- associated with terrain/stereo applications

TMC-2 may act as a useful structural scale between:

- very detailed OHRC imagery
- much coarser IIRS spatial sampling

Use the name:

> **TMC-2**

when referring specifically to the Chandrayaan-2 instrument.

Do not casually replace it with `TMC` where instrument identity matters.

---

### 4.3 IIRS

**IIRS — Imaging Infrared Spectrometer**

IIRS should be understood as:

> **hyperspectral / imaging-infrared data**

not simply as:

> a low-resolution grayscale camera

Its measurements contain information across many spectral channels.

Its spatial resolution is significantly coarser than OHRC and TMC-2.

This means IIRS carries two different forms of information that must not be confused:

```text
Spectral information
        +
Spatial information
```

---

### 4.4 Hyperspectral Data Structure

Conceptually, a grayscale image may be represented as:

```text
height × width
```

A hyperspectral observation may conceptually be represented as:

```text
height × width × spectral bands
```

Each spatial pixel can contain a spectral response rather than one intensity value.

This is valuable for material and spectral analysis but complicates direct use with conventional 2D local-feature pipelines.

---

### 4.5 IIRS Registration Representations

A conventional 2D image matcher generally requires a 2D representation.

Potential IIRS research representations may therefore include:

- selected spectral band
- selected wavelength-region representation
- PCA-derived component
- spectral composite
- gradient representation
- edge representation
- structural representation

These are implementation/research choices.

No representation should be assumed universally best without controlled evidence.

---

### 4.6 IIRS Band-Count Caution

Do not hard-code an exact number of IIRS bands merely from a general instrument description.

Project materials may use wording such as:

- roughly 250 bands
- approximately 256 bands

Exact product structure should come from:

- actual product metadata
- authoritative product documentation
- dataset-specific definitions

The stable domain fact is:

> IIRS is a hyperspectral/imaging-infrared instrument with many spectral channels.

Exact product dimensions belong in dataset documentation rather than generic domain assumptions.

---

### 4.7 Spectral Richness Is Not Spatial Resolution

This distinction is critical.

An instrument may contain:

```text
Many spectral bands
```

while still having:

```text
Coarse spatial sampling
```

Therefore:

> More bands do not mean more spatial terrain detail.

Spectral and spatial resolution are different dimensions of information.

---

## 5. LRO Reference Context

Lunar Reconnaissance Orbiter imagery may provide important reference data for ChandraMap.

The distinction between NAC and WAC matters.

---

### 5.1 LRO NAC

**NAC — Narrow Angle Camera**

General role:

- high-resolution lunar imagery
- detailed local reference data
- potentially useful for fine registration

Its actual scale varies with observation and product geometry.

A coarse source observation may therefore need to be compared against a downsampled or pyramid representation of NAC data rather than directly against its finest available detail.

---

### 5.2 LRO WAC

**WAC — Wide Angle Camera**

General role:

- broader lunar coverage
- wider spatial context
- lower spatial detail than NAC
- possible coarse localization/reference use

WAC may be useful when broad spatial coverage matters more than fine terrain detail.

---

### 5.3 NAC vs WAC

Do not use the generic phrase:

> LRO camera

when the actual instrument matters.

Conceptually:

```text
NAC
→ detailed/local reference

WAC
→ broader-area context
```

They serve different roles and should not automatically be treated as interchangeable datasets.

---

## 6. Multi-Sensor Registration

Cross-sensor matching is more difficult than same-sensor matching because images may differ in:

- spectral response
- radiometric response
- spatial resolution
- noise
- illumination
- geometry
- preprocessing
- projection

Therefore, identical physical terrain may not produce identical pixel patterns.

---

### 6.1 Modality Differences

Panchromatic imagery and hyperspectral/infrared imagery measure different aspects of the scene.

This means:

```text
Same terrain
        ≠
Same raw intensity pattern
```

Cross-modality matching should not assume direct radiometric equality.

---

### 6.2 Radiometric Differences

Possible intensity differences may arise from:

- sensor response
- illumination
- calibration
- spectral sensitivity
- preprocessing

Radiometric normalization can help some differences but does not remove all physical variation.

---

### 6.3 Structural Matching Motivation

Because raw intensity may be unreliable across sensors or illumination conditions, structural information may sometimes be more stable.

Potential representations include:

- gradients
- edges
- local shape
- phase-related features
- relative terrain geometry

These remain experimental choices unless benchmarked.

---

### 6.4 Domain Shift

A learned model trained mainly on terrestrial imagery may encounter substantial **domain shift** on lunar imagery.

Possible differences include:

- crater-dominated terrain
- absence of vegetation
- absence of buildings
- unusual illumination
- high-contrast shadows
- repetitive geological structures
- spectral differences
- different scale distributions

Therefore:

> Strong performance on terrestrial benchmarks does not prove strong lunar performance.

Learned models must be evaluated on relevant lunar data.

---

## 7. Lunar Illumination

### 7.1 Sun-Angle Effects

A change in solar geometry can alter:

- shadow direction
- shadow length
- illuminated crater walls
- visible ridge structure
- local intensity
- apparent feature shape

The same crater may therefore look substantially different between observations.

---

### 7.2 Brightness Normalization Is Limited

Operations such as:

- histogram normalization
- contrast stretching
- CLAHE
- local contrast adjustment

may improve radiometric comparability.

They cannot fully undo:

- changed shadow geometry
- terrain parallax
- missing detail
- sensor modality differences

Therefore:

```text
Brightness normalization
        ≠
Illumination invariance
```

---

### 7.3 Shadows

Shadows may contain useful shape information, but shadow boundaries are not fixed terrain features.

They move as solar geometry changes.

Therefore, matching based strongly on shadow contours must be interpreted carefully.

---

### 7.4 Structural Representations

Potentially more stable clues may include:

- crater rims
- ridge geometry
- edges
- gradients
- relative layout of nearby structures
- phase-related representations

However:

> No representation should be described as universally illumination invariant without evidence.

---

### 7.5 Illumination Metadata

When available, useful metadata may include concepts such as:

- solar incidence angle
- solar azimuth
- illumination direction
- local lighting geometry

Do not assume every product provides every quantity.

Exact availability belongs in dataset/product documentation.

---

## 8. Image Correspondence

A **correspondence** links positions in two images that represent the same physical terrain feature or surface location.

Potential examples include:

- point on a crater rim
- distinctive ridge intersection
- terrain corner
- stable local surface structure

Correspondence is more fundamental than producing a warped image.

A warp can appear visually convincing even when its control correspondences are poor.

---

## 9. Candidate Matches

A feature matcher produces proposed correspondences.

These should initially be treated as:

> **candidate matches**

Candidate matches may include:

- correct correspondences
- descriptor ambiguity
- repeated-crater confusion
- incorrect scale matches
- accidental local similarity

They require additional verification.

---

## 10. Geometric Verification

Geometric verification asks whether candidate correspondences are mutually consistent with a plausible transformation.

Conceptually:

```text
Candidate Matches
        ↓
Estimate Candidate Geometry
        ↓
Test Match Consistency
        ↓
Verified Inliers
        +
Rejected Outliers
```

Geometric verification does not prove absolute physical truth.

It determines consistency relative to:

- the selected transformation model
- the selected threshold
- the selected coordinate conventions

Independent validation remains important.

---

## 11. RANSAC

**RANSAC — Random Sample Consensus**

In ChandraMap's domain, RANSAC may be used to estimate a geometric model robustly in the presence of incorrect candidate matches.

Conceptually, it:

1. evaluates candidate geometric models
2. identifies matches consistent with a model
3. separates model-consistent matches from inconsistent ones

RANSAC does not make the original matcher correct.

It provides geometric outlier rejection.

---

## 12. Inliers and Outliers

### Inliers

Candidate correspondences sufficiently consistent with the estimated model and threshold.

### Outliers

Candidate correspondences inconsistent with that model.

Important:

```text
RANSAC inlier
        ≠
Guaranteed ground-truth correspondence
```

An inlier is model-consistent evidence.

That distinction matters particularly when:

- repetitive terrain creates coherent false matches
- the model is overly flexible
- thresholds are too permissive
- the overlap is small

---

## 13. Transformation Models

Different geometric models express different assumptions.

Potential models include:

- translation
- similarity transform
- affine transform
- homography

The appropriate model depends on:

- image geometry
- product processing
- field of view
- terrain relief
- overlap
- project/benchmark definition

Do not automatically assume one model is always correct.

---

### 13.1 Translation

A translation represents displacement without rotation, scale change, or distortion.

It is appropriate only when the image relationship is correspondingly simple.

---

### 13.2 Similarity Transform

A similarity transform can represent effects such as:

- translation
- rotation
- uniform scale

It is more flexible than translation while preserving shape relationships more strongly than affine or projective models.

---

### 13.3 Affine Transform

An affine transform can represent:

- translation
- rotation
- scale
- shear

while preserving parallel lines.

It can be useful when images are locally comparable or already geometrically normalized.

---

### 13.4 Homography

A homography is a projective transformation often used for approximately planar image alignment.

It can be effective for local registration.

However:

> **The Moon is not a flat plane.**

A single homography may fail to fully explain:

- terrain relief
- large spatial extent
- strong viewpoint differences
- raw sensor geometry
- projection differences

---

## 14. Global vs Local Geometry

If residual vectors vary systematically across the overlap, possible explanations include:

- terrain relief
- sensor geometry
- projection differences
- an overly simple transformation
- local distortion

Possible research directions may include:

- local refinement
- piecewise transforms
- sensor-model geometry
- DEM-assisted correction

These approaches are not automatically necessary for every pair.

The simplest sufficient geometric model should generally be preferred.

---

## 15. Terrain Relief

The lunar surface contains true three-dimensional relief.

Consequently, two images captured from different viewpoints may contain geometric differences that cannot be completely represented by one planar transformation.

Terrain effects become especially important when:

- relief is substantial
- viewing geometry differs
- images cover a large area
- products are not fully orthorectified

DEM/DTM information may eventually help model these effects.

---

## 16. DEM and DTM Context

Conceptually:

**DEM — Digital Elevation Model**

**DTM — Digital Terrain Model**

These describe terrain elevation or topography.

Potential registration-related uses include:

- orthorectification
- terrain-aware warping
- relief analysis
- geometric interpretation

Exact ChandraMap usage should be defined in dataset, architecture, or research documentation.

Do not assume DEM-aware processing is already implemented.

---

## 17. LOLA Context

**LOLA — Lunar Orbiter Laser Altimeter**

LOLA-derived topographic information may be relevant to future work involving:

- terrain geometry
- elevation reference
- DEM-related processing
- validation

Do not treat LOLA as a required project dependency unless current repository evidence establishes that role.

---

## 18. Map-Projected Imagery

A map projection transforms planetary surface coordinates into a planar coordinate representation.

Map-projected products may already account for significant portions of:

- image geometry
- coordinate alignment
- spacecraft viewing effects

When map-projected or otherwise geometrically corrected imagery is available, that information should be used rather than asking a generic vision system to rediscover known geometry unnecessarily.

---

## 19. Product Processing Levels

Mission data may exist in different processing states.

Conceptually these can include:

- raw observations
- calibrated products
- geometrically corrected products
- map-projected products
- orthorectified products
- derived products

Exact mission-specific level names and formats belong in `DATASETS.md` or authoritative product documentation.

Do not assume two files from the same instrument have identical processing history.

---

## 20. Orthorectification

Orthorectification attempts to correct geometric displacement associated with:

- viewing geometry
- terrain relief
- imaging geometry

using appropriate spatial models.

Orthorectified products can be easier to compare geospatially than raw imagery.

Do not assume all inputs are orthorectified.

---

## 21. Planetary Geospatial Context

### 21.1 Lunar Coordinates Are Not Earth Coordinates

Do not blindly apply Earth-centric geospatial defaults to lunar data.

The Moon has its own:

- planetary reference definitions
- coordinate conventions
- body model
- projection choices
- longitude conventions

Never silently assume:

```text
WGS84
EPSG:4326
```

is appropriate for lunar geometry.

---

### 21.2 Latitude and Longitude

Latitude and longitude may identify locations on the lunar surface, but their interpretation can depend on:

- planetary body definition
- latitude convention
- longitude convention
- projection
- product metadata

Use actual project/product documentation.

Do not guess.

---

### 21.3 Longitude Conventions

Planetary data may use conventions such as:

- positive-east
- positive-west
- `0°–360°`
- `-180°–+180°`

A coordinate expressed in one convention must not be silently interpreted as another.

Conversions should be explicit.

---

### 21.4 Image Coordinates vs Geospatial Coordinates

Distinguish:

```text
Image coordinates
(x, y)
(row, column)
```

from:

```text
Geospatial coordinates
latitude / longitude
projected coordinates
```

A transformation in image pixels is not automatically a geospatial transformation.

---

### 21.5 `x/y` vs `row/column`

This is a common source of geometry bugs.

A common image-coordinate convention is:

```text
x = column
y = row
```

while array access commonly uses:

```text
array[row, column]
```

Always inspect the actual repository convention.

Do not silently swap them.

---

### 21.6 Source vs Reference

ChandraMap should explicitly distinguish:

- **source image**
- **reference image**

A transform may represent:

```text
source → reference
```

or:

```text
reference → source
```

These are not interchangeable.

Transformation direction must be explicit in code, tests, metrics, and documentation when ambiguity matters.

---

## 22. Tie Points

Tie points are corresponding locations linking observations or geospatial products.

Useful tie points should ideally be:

- accurate
- geometrically consistent
- spatially distributed

High tie-point count alone is insufficient.

A large cluster in one small region may poorly constrain registration elsewhere.

---

## 23. Sub-Pixel Registration

Sub-pixel accuracy refers to localization or residual accuracy smaller than one image pixel.

For example:

```text
0.3 px
```

is a sub-pixel quantity.

It does **not** automatically mean:

```text
0.3 m
```

Ground error depends on:

- sensor GSD
- projection
- reference quality
- geometry
- coordinate interpretation

---

## 24. Sub-Pixel Refinement

A scientifically clean conceptual sequence is:

```text
Candidate Matches
        ↓
Geometric Verification
        ↓
Verified Inliers
        ↓
Local Sub-Pixel Tie-Point Refinement
        ↓
Refined Coordinates
        ↓
Final Transform Refit
```

Potential refinement approaches may include:

- local correlation
- phase correlation
- patch-based refinement
- planetary registration tools

Exact implementation choices belong in pipeline or research documentation.

---

## 25. Evaluation Principles

Registration evaluation must account for:

- what points are evaluated
- how the transform was estimated
- coordinate system
- units
- reference quality
- spatial distribution

One metric rarely tells the full story.

---

### 25.1 Fit Points vs Check Points

**Fit points** are used to estimate a transformation.

**Check points** are withheld from fitting and used for evaluation.

Conceptually:

```text
Fit Points
    ↓
Estimate Transform
```

then:

```text
Independent Check Points
        ↓
Evaluate Transform
```

A transform will naturally perform well on the data used to fit it.

Independent check-point evaluation therefore provides stronger evidence of registration accuracy where available.

---

### 25.2 RMSE

**RMSE — Root Mean Square Error**

RMSE summarizes residual magnitude.

However, an RMSE value is incomplete without knowing:

- which points were included
- whether those points were used for fitting
- coordinate space
- units
- reference accuracy

For example:

```text
RMSE = 0.4
```

is not scientifically interpretable until the unit and evaluation procedure are known.

---

### 25.3 Inlier Count

Inlier count describes how many candidate correspondences survive geometric consistency testing.

A higher count may be useful, but:

> More inliers do not automatically imply a more accurate registration.

---

### 25.4 Inlier Ratio

Conceptually:

```text
inlier ratio =
verified inliers / candidate matches
```

This measures one aspect of match quality.

It is not a complete registration metric.

---

### 25.5 Spatial Coverage

Spatial coverage asks:

> Are reliable correspondences distributed throughout the overlapping region?

Possible measures include:

- grid occupancy
- grid coverage
- convex-hull coverage
- normalized area coverage

For example, dividing the overlap into a grid can reveal whether inliers occupy many regions or only one local cluster.

---

### 25.6 Residual Vectors

Residual vectors provide both:

- error magnitude
- error direction

Visualizing residual vectors can reveal systematic patterns that a single aggregate number may hide.

For example, residuals consistently increasing toward one side of an image may indicate:

- model insufficiency
- projection effects
- relief-related distortion
- coordinate error

---

### 25.7 Error Units

Registration error may be expressed in:

- source-image pixels
- reference-image pixels
- map units
- metres

The unit must always be explicit.

Do not directly compare values expressed in different coordinate systems or units without appropriate conversion.

---

## 26. Registration Success

A scientifically meaningful registration success should normally involve several forms of evidence.

Potential evidence includes:

- sufficient reliable correspondences
- geometric consistency
- useful spatial distribution
- stable transformation
- acceptable independent error
- valid coordinate interpretation

Success should not be defined merely as:

> image warping completed without throwing an exception

---

## 27. Registration Failure

Failure may occur because:

- images do not overlap
- terrain lacks sufficient features
- the scale gap is too large
- illumination differences are extreme
- the current representation is unsuitable for a modality
- retrieval selected an incorrect candidate
- the transformation model is inadequate
- metadata is incorrect
- input data is unsupported

Failure is useful scientific information.

The system should be able to report it honestly.

---

## 28. Confidence

Do not treat an arbitrary number such as:

```text
92% confidence
```

as scientifically meaningful unless the quantity is explicitly defined and calibrated.

A useful confidence or quality model may eventually combine evidence such as:

- residuals
- inlier statistics
- spatial coverage
- retrieval score
- transformation stability
- independent validation

Until such a definition exists, decorative percentages should not be treated as scientific results.

---

## 29. Feature Detectors and Descriptors

A feature-based registration system may contain two conceptually different operations.

### Feature Detection

Find distinctive image locations.

### Feature Description

Represent local neighborhoods around those locations for comparison.

A matcher then uses those representations to propose correspondences.

These roles should remain conceptually distinct.

---

## 30. SIFT

**SIFT** is useful as a classical ChandraMap baseline because it provides:

- keypoint detection
- local descriptors
- some scale robustness
- some rotation robustness
- mature implementations
- interpretable behavior

However, SIFT is not guaranteed to solve:

- extreme illumination variation
- severe modality difference
- very large GSD mismatch
- low-feature terrain
- repetitive crater patterns

Its role as a baseline does not imply universal suitability.

---

## 31. RootSIFT

RootSIFT applies a transformation to SIFT descriptors that can improve descriptor comparison behavior in some matching settings.

Within ChandraMap it may serve as a classical baseline variation.

Detailed formulas and implementation choices belong elsewhere.

---

## 32. ORB

ORB can provide a computationally efficient classical feature pipeline in some contexts.

It may be useful as:

- speed-oriented baseline
- comparison method

Do not assume it is the preferred accuracy method without benchmark evidence.

---

## 33. Learned Matching Methods

Potential research methods include:

- ALIKED + LightGlue
- LoFTR

These methods may provide advantages under some conditions.

However, pretrained terrestrial models must not automatically be described as:

- lunar-invariant
- illumination-invariant
- modality-invariant
- universally superior to classical methods

They require lunar evaluation.

---

### 33.1 ALIKED + LightGlue

Conceptually:

```text
ALIKED
→ sparse local feature detection/description

LightGlue
→ sparse feature matching
```

Do not describe the two components as performing the same role.

---

### 33.2 LoFTR

LoFTR is a **detector-free correspondence method**.

It does not follow the same conceptual structure as:

```text
Detect keypoints
    ↓
Describe keypoints
    ↓
Match descriptors
```

Do not describe LoFTR merely as another feature detector.

---

## 34. Remote-Sensing Registration Methods

Methods such as:

- RIFT
- CFOG

may be relevant research candidates because multimodal remote-sensing registration often emphasizes structural consistency rather than direct raw-intensity equality.

Treat these as:

- research approaches
- benchmark candidates
- potential future methods

unless current repository evidence establishes a stronger implementation status.

---

## 35. Metadata-Constrained Search

If reliable geospatial or acquisition metadata already narrows the possible overlap, use it.

Potentially useful metadata may include:

- footprint
- approximate coordinates
- projection
- acquisition geometry
- sensor identity

Using known metadata is valid engineering.

Do not perform whole-Moon visual retrieval solely because retrieval is technically possible.

---

## 36. Global Retrieval

When source location is unknown, the task may include:

> Where on the Moon is this observation located?

This is different from local image registration.

Conceptually:

```text
Source Image
      ↓
Global Representation
      ↓
Reference Search
      ↓
Top-K Candidate Regions
      ↓
Local Registration
```

---

## 37. Global Descriptors vs Local Descriptors

### Global Descriptor

Represents an entire image or tile for retrieval.

Purpose:

```text
Find likely region
```

### Local Descriptor

Represents local image structures.

Purpose:

```text
Find precise point correspondences
```

Do not use these terms interchangeably.

---

## 38. FAISS

If used, **FAISS** should be understood as a vector similarity-search/indexing component.

Conceptually:

```text
Reference Tile
      ↓
Global Descriptor
      ↓
FAISS Index
```

Query:

```text
Source Image
      ↓
Compatible Global Descriptor
      ↓
FAISS Search
      ↓
Top-K Candidate Tiles
```

FAISS does **not** itself extract image features.

---

## 39. Retrieval vs Registration

Keep the questions separate.

### Retrieval

```text
Where is the likely region?
```

### Registration

```text
How do the two selected observations align precisely?
```

Retrieval can narrow the search space.

Registration establishes detailed correspondence and geometry.

A project may use one without always requiring the other.

---

## 40. Coarse-to-Fine Processing

A useful general strategy for large search spaces is:

```text
Coarse Search
      ↓
Candidate Region
      ↓
Local Matching
      ↓
Geometric Verification
      ↓
Fine Refinement
```

This can reduce the need to apply expensive fine matching against an entire lunar reference database.

The exact coarse-to-fine architecture belongs in pipeline documentation.

---

## 41. Crater Features

Craters provide useful geometric structures because many are stable physical terrain features.

At the same time, they create ambiguity because lunar surfaces contain many craters of similar apparent shape.

Therefore:

```text
Crater-like appearance
        ≠
Unique geographic identity
```

Relative geometry and wider terrain context matter.

---

## 42. Noise and Filtering

Light preprocessing may improve feature stability.

However, aggressive filtering can remove useful structures such as:

- crater edges
- small ridges
- high-frequency terrain detail
- local keypoints

Preprocessing should therefore be justified by measured matching or registration behavior.

---

## 43. Edge and Gradient Representations

Edges and gradients can reduce dependence on absolute intensity and emphasize terrain structure.

They may be useful under:

- illumination variation
- radiometric differences
- some cross-sensor conditions

However, they may also emphasize:

- noise
- shadow boundaries
- preprocessing artifacts

Their usefulness is therefore a research question, not an established universal rule.

---

## 44. Ground Truth

Registration ground truth may potentially come from:

- challenge-provided correspondences
- manually verified control/check points
- trusted map-projected references
- controlled geospatial products
- other independently validated sources

The exact ChandraMap ground-truth procedure belongs in benchmark/evaluation documentation.

Do not invent ground truth or treat estimated correspondences as ground truth automatically.

---

## 45. Domain Stress Cases

Different benchmark cases stress different physical aspects of the problem.

Potential categories include:

| Case                   | Domain Challenge              |
| ---------------------- | ----------------------------- |
| Similar illumination   | Basic correspondence          |
| Different illumination | Shadow and appearance changes |
| Large scale difference | GSD mismatch                  |
| Cross-modality         | Sensor response difference    |
| Relief-rich terrain    | Non-planar geometry           |
| Low-feature terrain    | Weak distinctiveness          |
| Unknown location       | Retrieval/localization        |
| Partial overlap        | Incomplete shared area        |

These categories describe useful scientific tests.

They do not imply that datasets or benchmark results for every category already exist.

---

## 46. Domain Facts vs Implementation Choices

Keep physical facts separate from software choices.

### Example: Scale

**Domain fact:**

> Upsampling does not recreate unmeasured spatial detail.

**Implementation choice:**

> Which pyramid levels should be generated?

---

### Example: IIRS

**Domain fact:**

> IIRS is hyperspectral/imaging-infrared data.

**Implementation choice:**

> Which selected band or PCA representation should be used?

---

### Example: Geometric Verification

**Domain fact:**

> Candidate correspondences can contain outliers.

**Implementation choice:**

> Which RANSAC threshold should be used?

---

### Example: Terrain

**Domain fact:**

> Lunar terrain has real three-dimensional relief.

**Implementation choice:**

> Whether a particular benchmark needs affine, homography, local warping, or terrain-aware geometry.

---

## 47. Domain Facts vs Research Hypotheses

Scientific wording should distinguish what is established from what remains experimental.

### Established Domain Fact

> Upsampling does not recreate spatial information that was never measured.

### Research Hypothesis

> Gradient representations may improve matching under large illumination differences.

The second statement requires evidence.

Do not rewrite research hypotheses as scientific facts.

---

## 48. Stable Instrument Context vs Product Metadata

Some characteristics are useful as broad domain context.

For example:

> TMC-2 imagery is approximately several metres per pixel.

But actual processing should prefer the specific product's metadata whenever available.

This applies to:

- GSD
- image dimensions
- footprint
- projection
- illumination geometry
- viewing geometry
- band structure
- wavelength information

Broad documentation values should not override exact product-level information.

---

## 49. Scientific Claim Discipline

Do not state as established facts that:

- a matcher is lunar-invariant
- ChandraMap achieves sub-pixel accuracy
- a method is state-of-the-art
- one preprocessing method is universally best
- one geometric model always solves lunar registration
- learned methods always outperform classical methods
- more keypoints always improve accuracy
- one confidence score universally predicts success

These require controlled experimental evidence.

---

## 50. Planetary Computer Vision

Generic computer-vision methods remain useful, but ChandraMap operates at the intersection of:

```text
Computer Vision
        +
Remote Sensing
        +
Planetary Geometry
        +
Scientific Evaluation
```

A method that is valid for ordinary photographs may require adaptation or additional validation for lunar imagery.

---

## 51. Relationship with Other Context Documents

This document should remain focused on scientific constraints.

### `PROJECT_CONTEXT.md`

Explains:

> What is ChandraMap and what is the project trying to achieve?

---

### `DOMAIN_CONTEXT.md`

Explains:

> What scientific facts and physical constraints affect ChandraMap?

---

### `TERMINOLOGY.md`

Should provide concise canonical definitions for terms such as:

- correspondence
- registration
- candidate match
- verified inlier
- GSD
- tie point
- check point
- residual
- retrieval

Avoid turning this document into the authoritative glossary.

---

### `DATASETS.md`

Should contain exact dataset-specific information such as:

- mission/product source
- product identifiers
- formats
- processing levels
- metadata fields
- local storage rules
- acquisition procedures
- licensing/redistribution considerations

This file should explain what dataset properties mean scientifically rather than duplicating acquisition instructions.

---

### Architecture Documentation

Architecture documents should explain how ChandraMap implements these concepts.

For example:

**Domain context:**

> Large GSD differences require physically meaningful scale comparison.

**Pipeline context:**

```text
Build reference pyramid
      ↓
Choose scale
      ↓
Candidate search
      ↓
Local match
```

---

### Benchmark and Metrics Documentation

This file explains scientific interpretation.

Benchmark/metrics documents should define exact:

- equations
- thresholds
- aggregation rules
- benchmark pairs
- failure criteria
- implementation conventions

---

## 52. Key Domain Rules for Agents

1. Treat lunar imagery as **planetary remote-sensing data**, not generic photographs.

2. OHRC, TMC-2, and IIRS are physically different sensors.

3. IIRS is hyperspectral/imaging-infrared data, not simply a grayscale camera.

4. Spectral richness is not the same as spatial resolution.

5. Use product metadata for exact GSD, geometry, footprint, projection, and band information where available.

6. Pixel dimensions do not define physical image scale.

7. Upsampling does not recover missing terrain information.

8. Multi-scale comparison should reflect physically meaningful effective ground scale.

9. Different lunar Sun angles alter shadow geometry, not only brightness.

10. Brightness normalization alone cannot guarantee illumination invariance.

11. Shadow boundaries are not fixed surface features.

12. Candidate matches are not verified correspondences.

13. Matcher confidence is not geometric proof.

14. Geometric verification should identify model-consistent inliers before normal sub-pixel refinement.

15. Refit the final transformation after refined tie-point coordinates when that refinement defines the final model.

16. RANSAC inliers are model-consistent matches, not automatically ground truth.

17. More inliers do not necessarily mean better registration.

18. Spatial distribution of reliable matches matters.

19. A global homography is not always sufficient for three-dimensional lunar terrain.

20. Terrain relief and viewing geometry can create spatially varying residuals.

21. Do not use Earth-centric CRS assumptions blindly for lunar data.

22. Longitude convention, coordinate convention, and body reference must be explicit.

23. Image coordinates and geospatial coordinates are different coordinate spaces.

24. `x/y` and `row/column` conventions must be verified before modifying geometry code.

25. Transform direction—`source → reference` or `reference → source`—must be explicit.

26. Sub-pixel image-space accuracy does not automatically imply sub-metre ground accuracy.

27. Error units must always be explicit.

28. Fit-point residuals and independent check-point error are different measurements.

29. RMSE without units, coordinate context, and evaluation population is incomplete.

30. Visual alignment alone is insufficient evidence of registration quality.

31. Global retrieval and local registration are separate tasks.

32. Global descriptors and local descriptors serve different purposes.

33. FAISS searches/indexes vectors; it does not create image descriptors.

34. SIFT is a useful baseline, not a guaranteed lunar solution.

35. ALIKED and LightGlue perform different roles in a sparse learned matching pipeline.

36. LoFTR is detector-free and should not be treated simply as another keypoint extractor.

37. Pretrained terrestrial matchers are not automatically lunar-invariant.

38. Remote-sensing matching methods should be treated as research/benchmark candidates unless current implementation establishes otherwise.

39. Metadata-constrained search is valid engineering and can avoid unnecessary global retrieval.

40. Registration failure is a valid scientific output.

41. Arbitrary confidence percentages are not scientifically meaningful unless defined and calibrated.

42. Domain facts must remain separate from implementation choices.

43. Domain facts must remain separate from research hypotheses.

44. Broad instrument characteristics must not override exact product metadata.

45. Scientific claims require controlled evidence.

<!-- Source specification: :contentReference[oaicite:0]{index=0} -->
