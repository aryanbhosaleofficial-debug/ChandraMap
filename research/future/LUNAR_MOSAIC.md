# Lunar Mosaic Research

## 1. Purpose

This document defines a future-research direction for **automated lunar image mosaicking** within ChandraMap.

The objective is to investigate how individually registered lunar images or image tiles can be combined into a larger, spatially coherent representation of a lunar region while preserving measurable geometric accuracy, provenance, reproducibility, and scientific validity.

This document is a **research specification**. It does not claim that ChandraMap currently contains a production-ready lunar mosaicking system.

The research direction builds on the existing ChandraMap focus on:

- image correspondence
- local feature matching
- geometric verification
- image registration
- independent geometric evaluation
- scale handling
- illumination-aware representations
- future global retrieval
- multi-sensor research

The intended conceptual pipeline is:

```text
Multiple Lunar Images
        ↓
Image / Tile Identification
        ↓
Global Retrieval if Needed
        ↓
Local Correspondence
        ↓
Geometric Verification
        ↓
Pairwise / Multi-Image Registration
        ↓
Global Geometric Optimization
        ↓
Seam / Blend Processing
        ↓
Mosaic Generation
        ↓
Independent Geospatial / Geometric Validation
```

The central research question is:

> **Can ChandraMap's correspondence and registration framework be extended from reliable pairwise alignment to accurate, scalable, and reproducible multi-image lunar mosaicking?**

---

## 2. Scope

This document covers future investigation of:

- pairwise lunar image registration as a building block for mosaicking
- image and tile identification
- global retrieval for large image collections
- local correspondence
- geometric verification
- pairwise transformations
- multi-image registration
- global geometric optimization
- map-projected mosaicking
- seam handling
- photometric normalization
- blending
- mosaic quality assessment
- geospatial validation
- uncertainty propagation
- large-scale processing
- reproducibility
- failure analysis
- benchmark design

It does **not** establish:

- a production mosaicking implementation
- a final mosaic algorithm
- a final transformation model
- a final blending method
- a final dataset
- universal lunar cartographic accuracy
- a guaranteed solution for arbitrary lunar imagery
- a production data pipeline
- a validated global lunar map product

All of these remain subject to experimentation and validation.

---

# 3. Why Lunar Mosaicking Matters

Pairwise image registration answers a local question:

> Can two images of the same region be aligned?

Mosaicking introduces a larger question:

> Can many individually acquired images be aligned into one spatially coherent representation?

The second problem is substantially more difficult.

A collection of pairwise registrations can produce a mosaic that still contains:

- accumulated geometric drift
- inconsistent transformations
- local misalignment
- duplicated or missing regions
- seam artifacts
- brightness discontinuities
- exposure differences
- shadow differences
- scale inconsistencies
- sensor-dependent appearance
- inconsistent map coordinates
- local geometric distortions
- disconnected image components

Therefore:

> **Successful pairwise registration does not automatically imply successful multi-image mosaicking.**

A mosaicking system must evaluate both local registration quality and global consistency.

---

# 4. Terminology

## 4.1 Image Registration

Image registration estimates a geometric relationship between two images.

Conceptually:

```text
Image A + Image B
        ↓
Correspondences
        ↓
Geometric Verification
        ↓
Transformation
        ↓
Registered Image Pair
```

Registration is primarily concerned with estimating geometric alignment.

---

## 4.2 Image Alignment

Image alignment is the practical process of transforming one image into a coordinate system where corresponding structures occupy approximately the same spatial locations.

Alignment may be the result of:

- feature-based registration
- intensity-based optimization
- template matching
- map-coordinate transformation
- sensor geometry
- orthorectification
- other geometric models

Alignment is therefore a broader operational term than a specific algorithm.

---

## 4.3 Image Stitching

Image stitching generally refers to combining overlapping images after estimating their relative geometry.

A simplified workflow is:

```text
Images
 ↓
Pairwise Matching
 ↓
Transform Estimation
 ↓
Warping
 ↓
Seam Handling
 ↓
Blending
```

Stitching does not necessarily imply rigorous geospatial coordinates.

---

## 4.4 Image Mosaicking

Image mosaicking combines multiple images or tiles into a larger representation.

For ChandraMap, mosaicking should be treated as a **multi-stage geometric and radiometric processing problem**, rather than merely concatenating registered images.

A mosaic should ideally preserve:

- spatial consistency
- source provenance
- transformation provenance
- geometric quality
- image coverage
- valid-data masks
- uncertainty information
- reproducibility

---

## 4.5 Geospatial Mosaicking

Geospatial mosaicking combines imagery using an explicit spatial reference framework.

This may involve:

- map projections
- geographic coordinates
- projected coordinates
- pixel-to-map transformations
- sensor metadata
- image footprints
- spatial reference information

The exact coordinate reference system and projection strategy for ChandraMap mosaicking remains:

**[TBD]**

---

## 4.6 Photometric Blending

Photometric blending attempts to reduce visual discontinuities between overlapping images.

Possible causes of discontinuities include:

- different illumination
- different acquisition conditions
- exposure differences
- sensor response
- contrast differences
- atmospheric or instrumental effects where applicable
- processing differences

Photometric blending should not be treated as a substitute for geometric registration.

---

## 4.7 Map-Projected Mosaicking

Map-projected mosaicking combines imagery after transforming image observations into a common spatial coordinate system.

Conceptually:

```text
Raw / Source Images
        ↓
Sensor / Image Geometry
        ↓
Map Projection
        ↓
Common Spatial Grid
        ↓
Registration / Refinement
        ↓
Mosaic
```

The appropriate order of these operations depends on the available source products and metadata.

ChandraMap should not assume that all input imagery is already perfectly orthorectified or geometrically consistent.

---

# 5. Relationship to ChandraMap

The proposed mosaicking direction extends the existing ChandraMap research stack.

A simplified hierarchy is:

```text
Correspondence
      ↓
Pairwise Registration
      ↓
Multi-Image Registration
      ↓
Mosaic Generation
      ↓
Geospatial Mosaic Validation
```

Each level depends on the quality of the previous level.

If pairwise correspondences are unreliable, a global optimization stage can optimize the wrong relationships.

If pairwise transformations are biased, global optimization can distribute rather than eliminate the bias.

If the image geometry is incorrect, blending cannot correct it.

Therefore the research principle is:

> **Do not hide correspondence or registration failures inside the mosaic stage.**

---

# 6. Current Research Foundation

The mosaicking direction is intended to build on the established ChandraMap research areas.

Relevant project research includes:

- SIFT baseline
- scale-pyramid investigation
- gradient/structural representation
- affine vs homography analysis
- residual analysis
- subpixel refinement
- future RIFT/CFOG investigation
- future ALIKED investigation
- future LightGlue investigation
- future LoFTR investigation
- future global retrieval
- future FAISS-based retrieval infrastructure
- future IIRS investigation

The existing conceptual registration pipeline is:

```text
Source Image
    ↓
Preprocessing
    ↓
Scale Handling
    ↓
Representation
    ↓
Feature Detection / Matching
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

The mosaicking research direction adds:

```text
Registered Pair Graph
        ↓
Multi-Image Graph
        ↓
Global Geometric Optimization
        ↓
Consistent Image Poses
        ↓
Warping
        ↓
Seam / Blend Processing
        ↓
Mosaic
        ↓
Independent Validation
```

---

# 7. Lunar Imaging Challenges

Lunar mosaicking introduces several challenges that are particularly relevant to ChandraMap.

## 7.1 Scale Differences

Images may have substantially different spatial resolutions.

Relevant project imagery includes:

- Chandrayaan-2 OHRC
- Chandrayaan-2 TMC-2
- Chandrayaan-2 IIRS

The exact usable resolution relationship depends on the selected products and processing level.

Scale differences can affect:

- feature repeatability
- correspondence density
- geometric estimation
- overlap detection
- mosaic resolution
- resampling quality

A mosaic system must distinguish between:

```text
different pixel dimensions
```

and

```text
different actual spatial information content
```

Upsampling increases the number of pixels but does not recover spatial information absent from the original image.

---

# 8. Illumination and Shadow Challenges

Lunar illumination varies strongly between observations.

Changing illumination can modify:

- brightness
- contrast
- local gradients
- shadow boundaries
- visible surface appearance
- apparent local structure

A critical distinction is:

> **Illumination variation is not equivalent to a simple brightness offset.**

Similarly:

> **A changing shadow boundary is not necessarily a stable geometric feature.**

A mosaic system that simply minimizes pixel differences may therefore introduce incorrect geometric adjustments.

Structural representations investigated elsewhere in ChandraMap may help correspondence under radiometric variation, but their effectiveness for mosaicking must be measured.

---

# 9. Relief and Shadow Geometry

The lunar surface is three-dimensional.

Terrain includes:

- craters
- crater rims
- ridges
- valleys
- slopes
- boulders
- ejecta structures
- local relief

Changing viewing or illumination geometry can produce different image appearances of the same terrain.

A 2D transformation such as:

- translation
- similarity
- affine
- homography

may approximate local relationships under appropriate conditions, but these transformations do not represent arbitrary 3D terrain geometry.

Therefore:

> **A geometrically consistent 2D mosaic is not automatically a physically exact 3D reconstruction.**

Where relief or viewing geometry is significant, the limitations of the selected geometric model must be measured.

---

# 10. Sensor and Modality Differences

A future mosaic may involve:

- same-sensor imagery
- different acquisition conditions
- different resolutions
- different processing levels
- different sensors
- potentially different sensing modalities

Cross-sensor mosaicking can be substantially more difficult than same-sensor mosaicking.

For example:

```text
OHRC → OHRC
```

and

```text
OHRC → TMC-2
```

should not automatically be treated as equivalent benchmark conditions.

Similarly:

```text
OHRC → IIRS-derived representation
```

may require a different representation and evaluation strategy.

Sensor-specific results should therefore remain separately identifiable.

---

# 11. Mosaic Inputs

Potential mosaic inputs may include:

```text
Individual Images
      +
Image Metadata
      +
Spatial Footprints
      +
Acquisition Information
      +
Preprocessing Metadata
      +
Registration Results
```

Each input image should ideally have an explicit identifier.

Conceptually:

```text
Image ID
Source Instrument
Acquisition Metadata
Spatial Reference
Image Dimensions
Resolution / GSD Information
Processing Level
Valid-Data Mask
Footprint
```

The exact metadata schema is:

**[TBD]**

No metadata field should be assumed to exist without verification.

---

# 12. Tile Identification

A large lunar mosaic may involve many images.

The first problem is therefore determining which images are likely to overlap.

For a small controlled experiment:

```text
Image A
Image B
```

may be directly provided.

For a larger collection:

```text
Image Collection
        ↓
Spatial / Metadata Filtering
        ↓
Candidate Overlap Pairs
        ↓
Global Retrieval if Needed
        ↓
Local Registration
```

Candidate pair generation should be separated from local correspondence.

---

# 13. Global Retrieval

When the number of images becomes large, comparing every possible image pair becomes expensive.

For:

```text
N images
```

naive all-pairs comparison produces:

```text
N(N - 1) / 2
```

candidate pairs.

A future retrieval layer may reduce this search space.

Potential retrieval signals include:

- geographic metadata
- image footprints
- approximate coordinates
- visual retrieval
- learned global embeddings
- handcrafted global descriptors
- FAISS-based approximate nearest-neighbor indexing

The retrieval stage should be evaluated separately from local registration.

> **Retrieval identifies likely candidates; it does not establish geometric correspondence.**

---

# 14. Pairwise Registration as a Mosaic Building Block

For each candidate overlapping pair:

```text
Image A
   +
Image B
   ↓
Correspondence
   ↓
Geometric Verification
   ↓
Transformation
   ↓
Independent Evaluation
```

The output should not merely be:

```text
match = true
```

A research-grade pairwise registration record should preserve information such as:

- image identifiers
- candidate correspondence count
- verified inlier count
- inlier ratio
- transformation type
- transformation parameters
- spatial distribution
- independent checkpoint error
- residual statistics
- failure status
- processing configuration

Exact schema:

**[TBD]**

---

# 15. Candidate Matches vs Verified Matches

Mosaicking must distinguish:

### Candidate Correspondences

Feature or descriptor matching proposes a relationship.

### Geometrically Verified Inliers

The proposed relationship is consistent with the estimated geometric model within the selected verification procedure.

### Independent Check Points

Points withheld from transformation estimation are used to evaluate geometric accuracy.

This distinction is critical.

```text
Candidate Matches
        ↓
Geometric Verification
        ↓
Verified Inliers
        ↓
Transformation
        ↓
Independent Check Points
```

A high inlier count does not by itself prove that the registration is accurate.

---

# 16. Spatial Coverage

A major mosaic-specific issue is **where correspondences occur**.

Example:

```text
+-----------------------+
|                       |
|                       |
|        XXXXX          |
|        XXXXX          |
|                       |
+-----------------------+
```

A large number of clustered matches may provide weaker geometric control than fewer matches distributed across the overlap.

Mosaic evaluation should therefore consider:

- spatial distribution
- grid occupancy
- convex-hull coverage
- edge coverage
- overlap coverage
- concentration of control points

This extends the existing ChandraMap principle:

> **More matches are not automatically better matches.**

---

# 17. Pairwise Transformation Models

Potential models include:

- translation
- similarity
- affine
- homography
- other projective or sensor-specific models
- future physically motivated models

The appropriate model must be determined experimentally.

Existing ChandraMap research already treats affine vs homography as a controlled experiment rather than assuming one model is universally correct.

For mosaicking:

```text
Pairwise Model
      ↓
Pairwise Transformation
      ↓
Global Consistency
```

A transformation that performs well on one pair may not be appropriate for every pair in a large image collection.

---

# 18. Transformation Graph

A multi-image mosaic can be represented as a graph.

```text
Image A -------- Image B
   |                |
   |                |
   |                |
Image C -------- Image D
```

Each node represents an image.

Each edge represents a verified pairwise relationship.

Conceptually:

```text
Node = Image
Edge = Verified Registration
Edge Weight = Registration Confidence / Quality
```

The exact edge-weight definition is:

**[TBD]**

Potential evidence could include:

- independent validation error
- verified inlier count
- inlier ratio
- spatial coverage
- residual quality
- uncertainty

These signals must not be combined into an undocumented arbitrary score.

---

# 19. Why Pairwise Accuracy Is Not Enough

Consider:

```text
A → B
B → C
C → D
```

Even if each pairwise transformation is individually reasonable, accumulated error may produce:

```text
A -------------------------- D
 \                           /
  \____ accumulated drift __/
```

This is commonly described as **global drift** or **error accumulation**.

A mosaic therefore requires evaluation of both:

1. pairwise registration quality
2. global multi-image consistency

---

# 20. Global Geometric Optimization

A future global optimization stage may estimate a consistent pose for all images.

Conceptually:

```text
Pairwise Transformations
        ↓
Initial Image Poses
        ↓
Global Optimization
        ↓
Consistent Image Poses
```

The optimization objective should be explicitly defined.

Potential terms may include:

- pairwise geometric residuals
- control-point residuals
- independent geospatial constraints
- metadata-based constraints
- regularization
- uncertainty weighting

The exact objective is:

**[TBD]**

The optimization must not silently change the meaning of the original pairwise measurements.

---

# 21. Global Consistency

A useful diagnostic is loop consistency.

Example:

```text
A → B
B → C
C → A
```

The composed transformation should ideally be close to the identity relationship in the relevant coordinate system.

Conceptually:

```text
T_A→B
   ×
T_B→C
   ×
T_C→A
   ≈
Identity
```

Large loop inconsistency can indicate:

- weak pairwise registration
- incorrect transformation model
- accumulated error
- spatially varying distortion
- incorrect correspondences
- insufficient overlap
- metadata inconsistency

Loop closure should therefore be considered a useful future diagnostic.

---

# 22. Mosaic Coordinate System

The mosaic requires a defined output coordinate system.

Possible conceptual choices include:

- image-relative coordinates
- local projected coordinates
- planetary map coordinates
- another explicitly defined spatial reference

The selected coordinate system must be documented.

At minimum, the mosaic specification should record:

```text
Coordinate Reference
Projection
Pixel Size
Output Extent
Origin / Grid Definition
Resampling Method
NoData Definition
```

The exact schema and projection strategy are:

**[TBD]**

---

# 23. Warping and Resampling

Once global image poses have been estimated, source images must be mapped into the mosaic coordinate system.

Conceptually:

```text
Source Image
      ↓
Estimated Transformation
      ↓
Warp
      ↓
Common Mosaic Grid
```

Resampling can introduce differences in:

- sharpness
- edge location
- radiometry
- texture
- local geometry

Potential resampling methods should therefore be treated as experimental variables where they can materially affect evaluation.

The exact resampling configuration is:

**[TBD]**

---

# 24. Valid-Data Masks

Every source image should ideally have an associated valid-data mask.

The mask can distinguish:

```text
Valid image pixels
Invalid pixels
NoData
Padding
Potentially unusable border regions
```

This is important because image boundaries and padding can create artificial features.

A mosaic system should avoid treating:

- black borders
- padding
- missing data
- invalid pixels

as real lunar terrain.

---

# 25. Overlap Regions

When two or more images overlap, the mosaic must determine how the overlap is represented.

Conceptually:

```text
Image A
+----------------+
|       XXXXXXXX |
|       XXXXXXXX |
+----------------+

Image B
        +----------------+
        | XXXXXXXX       |
        | XXXXXXXX       |
        +----------------+
```

The overlap may contain:

- duplicated information
- radiometric differences
- geometric disagreement
- different shadow patterns
- different resolution
- different sharpness

Overlap regions are therefore important evaluation areas.

---

# 26. Seam Selection

A seam determines where the mosaic transitions from one source image to another.

Potential seam strategies may consider:

- overlap boundaries
- image quality
- geometric residual
- radiometric difference
- gradient continuity
- valid-data masks
- source priority

No final seam strategy is selected by this document.

A future experiment should measure whether seam placement introduces visible or measurable artifacts.

---

# 27. Photometric Normalization

Different source images may have different radiometric properties.

Possible causes include:

- illumination
- sensor response
- exposure
- contrast
- processing pipeline
- acquisition conditions

Photometric normalization may reduce discontinuities.

However:

> **Photometric normalization must not be allowed to conceal geometric misregistration.**

A mosaic should therefore be evaluated geometrically before and after photometric processing where appropriate.

---

# 28. Shadow-Aware Blending

Lunar shadows are particularly important.

A seam placed across a strong shadow boundary may create a visually plausible result that does not correspond to a consistent physical observation.

Potential research questions include:

- Should shadow boundaries be avoided during seam placement?
- Can structural information identify unstable shadow regions?
- Should overlapping observations be selected according to illumination compatibility?
- Can multiple illumination conditions be intentionally retained?
- Should the mosaic represent one observation or a normalized surface appearance?

These are open research questions.

---

# 29. Single-Observation vs Multi-Observation Mosaic

A critical distinction is whether the desired mosaic represents:

### Observation Mosaic

The mosaic preserves the appearance of selected observations.

```text
Source observations
      ↓
Geometric alignment
      ↓
Mosaic
```

### Appearance-Normalized Mosaic

The mosaic attempts to reduce differences between observations.

```text
Source observations
      ↓
Geometric alignment
      ↓
Photometric processing
      ↓
Mosaic
```

### Scientific Surface Representation

The output attempts to represent a spatially consistent surface quantity rather than merely a visually continuous image.

This requires substantially stronger assumptions and validation.

ChandraMap should not assume that visual continuity means scientific surface consistency.

---

# 30. Resolution Management

A mosaic may combine images with different spatial resolutions.

Potential strategies include:

- preserve source resolution
- resample to a common grid
- use multiple-resolution products
- select a reference resolution
- construct sensor-specific mosaics

The appropriate strategy depends on the intended scientific use.

The project should explicitly record:

```text
Input Resolution
Output Resolution
Resampling Direction
Resampling Method
Information-Loss Considerations
```

---

# 31. Multi-Resolution Mosaicking

A future system may need to represent imagery at multiple spatial scales.

Conceptually:

```text
High Resolution
     ↓
Medium Resolution
     ↓
Low Resolution
```

Potential applications include:

- overview maps
- regional analysis
- local registration
- retrieval
- visualization

A multi-resolution pyramid must not be interpreted as creating information unavailable at the source resolution.

---

# 32. Cross-Sensor Mosaicking

Cross-sensor mosaicking should be evaluated separately from same-sensor mosaicking.

Potential pair categories include:

```text
OHRC ↔ OHRC
TMC-2 ↔ TMC-2
OHRC ↔ TMC-2
OHRC ↔ IIRS-derived representation
TMC-2 ↔ IIRS-derived representation
```

Only pairs actually available in the benchmark should be evaluated.

The exact benchmark composition is:

**[TBD]**

Cross-sensor results should retain the sensor-pair identity.

---

# 33. IIRS Considerations

IIRS introduces a different representation problem from conventional grayscale optical imagery.

A future IIRS-related mosaic experiment may investigate:

```text
IIRS Data
   ↓
Selected Spectral Representation
   ↓
Structural / Feature Representation
   ↓
Registration
   ↓
Mosaic
```

Potential representations may include:

- selected bands
- band combinations
- dimensionality-reduced representations
- derived structural representations

The exact spectral preprocessing pipeline is:

**[TBD]**

No assumption should be made that a representation effective for OHRC or TMC-2 will transfer unchanged to IIRS.

---

# 34. Relationship to RIFT and CFOG

RIFT and CFOG are future representation directions within ChandraMap.

They may be investigated as potential correspondence mechanisms for difficult illumination or modality conditions.

For mosaicking, their role is indirect:

```text
RIFT / CFOG
      ↓
Correspondence
      ↓
Registration
      ↓
Multi-Image Geometry
      ↓
Mosaic
```

A mosaic experiment should therefore distinguish:

1. representation quality
2. correspondence quality
3. pairwise registration quality
4. global registration quality
5. final mosaic quality

An improvement in the final mosaic cannot automatically be attributed to RIFT or CFOG unless the experimental design isolates the contribution.

---

# 35. Relationship to ALIKED

ALIKED is a learned local feature direction.

Conceptually:

```text
ALIKED
  ↓
Local Features
  ↓
Matching
  ↓
Geometric Verification
  ↓
Registration
```

For mosaicking, ALIKED could potentially provide pairwise relationships for larger image collections.

The research question is not whether learned features are automatically better, but whether they provide measurable benefits under the lunar benchmark conditions.

Relevant measurements include:

- correspondence reliability
- spatial coverage
- registration error
- robustness
- runtime
- memory
- reproducibility

---

# 36. Relationship to LightGlue

LightGlue is a matching component rather than a complete mosaicking system.

Conceptually:

```text
Feature Extractor
      ↓
LightGlue
      ↓
Correspondences
      ↓
Geometric Verification
      ↓
Registration
```

If evaluated, LightGlue should therefore be treated as one component in the correspondence stack.

A mosaic benchmark must avoid attributing the complete performance of a pipeline to the matcher alone.

---

# 37. Relationship to LoFTR

LoFTR represents a detector-free learned correspondence direction.

A possible future pipeline is conceptually:

```text
Image Pair
    ↓
LoFTR
    ↓
Correspondences
    ↓
Geometric Verification
    ↓
Registration
```

The applicability to lunar mosaicking remains an empirical question.

Important variables include:

- image scale
- illumination
- terrain type
- sensor pair
- overlap
- image size
- computational cost
- correspondence density
- false correspondence behavior

---

# 38. Relationship to Global Retrieval and FAISS

Global retrieval may become important when the number of source images grows.

Conceptually:

```text
Large Image Collection
        ↓
Global Retrieval
        ↓
Candidate Overlap Pairs
        ↓
Local Matching
        ↓
Registration
```

FAISS may serve as an indexing mechanism for a suitable embedding or descriptor space.

FAISS itself does not perform geometric registration.

Therefore:

> **Retrieval infrastructure and geometric registration must remain separate benchmark components.**

---

# 39. Mosaic Graph Construction

A future mosaic pipeline may construct an image graph:

```text
              B
             / \
            /   \
           A-----C
            \     \
             \     D
              \   /
                E
```

Nodes:

```text
Images
```

Edges:

```text
Verified spatial relationships
```

Disconnected components may indicate that the available correspondence system cannot establish a continuous mosaic.

A research implementation should preserve disconnected components rather than silently forcing them together.

---

# 40. Graph Connectivity

Important graph diagnostics include:

- number of nodes
- number of edges
- connected components
- isolated images
- node degree
- weakly connected regions
- redundant connections
- loop availability

A connected graph is necessary for a single connected mosaic under the selected graph formulation, but connectivity alone does not establish geometric correctness.

---

# 41. Redundant Registration

Redundant image relationships are valuable.

For example:

```text
A ↔ B
A ↔ C
B ↔ C
```

provides multiple geometric constraints.

Redundancy can help detect:

- inconsistent pairwise registration
- weak matches
- transformation-model problems
- local geometric distortions

Future experiments should investigate whether redundant edges improve global stability.

---

# 42. Global Optimization vs Pairwise Optimization

Pairwise optimization:

```text
A ↔ B
B ↔ C
C ↔ D
```

Global optimization:

```text
A
↘
 B
 ↓
 C
 ↓
 D
```

with all available constraints considered together.

The objective is not simply to maximize the number of pairwise matches.

The objective is to estimate a spatial configuration that is consistent with the available reliable evidence.

---

# 43. Uncertainty

A research-grade mosaicking system should eventually consider uncertainty.

Potential uncertainty sources include:

- feature localization
- descriptor ambiguity
- correspondence uncertainty
- RANSAC model uncertainty
- transformation estimation
- image metadata
- projection
- resampling
- checkpoint uncertainty
- reference-data uncertainty

The exact uncertainty model is:

**[TBD]**

Until formally defined, uncertainty should not be represented by an unsupported confidence score.

---

# 44. Independent Geometric Validation

The final mosaic must be evaluated independently from the points used to estimate its geometry.

Conceptually:

```text
Correspondences
      ↓
Transformation Estimation
      ↓
Global Optimization
      ↓
Mosaic
      ↓
Independent Check Points
      ↓
Error Measurement
```

This follows the established ChandraMap ground-truth principle.

> **Do not fit and evaluate using the same control points.**

---

# 45. Mosaic-Level Ground Truth

Possible evaluation references include:

- independent check points
- known control points
- independently registered reference imagery
- map-coordinate reference data
- manually verified landmarks
- other validated geospatial references

The exact ground-truth source must be documented.

A mosaic should never be described as “accurate” merely because it visually appears continuous.

---

# 46. Mosaic Metrics

Mosaic evaluation should operate at multiple levels.

## 46.1 Pairwise Metrics

Potential metrics:

- candidate correspondence count
- verified inlier count
- inlier ratio
- spatial coverage
- residual error
- independent checkpoint error
- registration success rate

---

## 46.2 Global Geometric Metrics

Potential metrics:

- independent checkpoint RMSE
- MAE
- median error
- error percentiles
- maximum error as a diagnostic
- loop-closure residual
- global drift
- spatially varying residual
- disconnected component count

Pixel error should remain the primary metric when evaluation is performed in image coordinates.

Ground-distance error should only be reported when the relevant spatial scale, projection, and reference geometry justify it.

---

## 46.3 Mosaic Quality Metrics

Potential measurements include:

- seam discontinuity
- overlap consistency
- edge alignment
- duplicate-edge error
- spatial coverage
- missing-data area
- invalid-data contamination
- geometric distortion
- radiometric discontinuity

The exact metric definitions must be established before benchmarking.

---

# 47. Residual Vector Fields

Residual vectors can reveal systematic errors that a single RMSE value hides.

Example:

```text
→ → → →
→ → → →
→ → → →
```

may indicate systematic displacement.

Another pattern:

```text
↗ ↑ ↖
→ • ←
↘ ↓ ↙
```

may indicate radial or local distortion.

Residual visualization should therefore be part of mosaic diagnostics.

---

# 48. Global Drift

Global drift can be investigated by comparing expected and estimated positions across the mosaic.

Conceptually:

```text
Image A
  ↓
Image B
  ↓
Image C
  ↓
Image D
  ↓
Image E
```

Small pairwise errors can accumulate.

A mosaic benchmark should therefore measure error as a function of:

- graph distance
- spatial distance
- image sequence
- regional position

where such analysis is meaningful.

---

# 49. Seam Quality vs Geometric Quality

These must remain separate.

A mosaic may have:

```text
Excellent visual seam
+
Poor geometric alignment
```

or:

```text
Accurate geometry
+
Visible radiometric seam
```

These are different failure modes.

Therefore:

```text
Geometric Evaluation
```

and

```text
Visual / Photometric Evaluation
```

should be reported separately.

---

# 50. Visual Inspection

Visual inspection remains useful for failure analysis but should not replace quantitative evaluation.

Useful visualizations include:

- source images
- warped images
- overlap regions
- correspondence overlays
- inlier overlays
- residual vectors
- seam locations
- mosaic output
- difference images
- spatial error maps

Visual inspection should support, not replace, independent measurements.

---

# 51. Failure Modes

The future mosaic pipeline should explicitly track failures.

## 51.1 No Candidate Overlap

The retrieval or spatial filtering stage finds no plausible pair.

---

## 51.2 Insufficient Correspondences

The images overlap but too few reliable correspondences are available.

---

## 51.3 Geometric Verification Failure

Candidate matches do not form a stable geometric relationship.

---

## 51.4 Degenerate Geometry

Correspondences are insufficiently distributed or geometrically degenerate.

---

## 51.5 Clustered Correspondences

Many matches occur in a small region.

---

## 51.6 Incorrect Transformation Model

The selected transformation cannot represent the observed relationship adequately.

---

## 51.7 Shadow-Induced Correspondence Failure

Changing illumination produces unstable shadow boundaries.

---

## 51.8 Repetitive Terrain

Repeated structures create ambiguous correspondences.

---

## 51.9 Cross-Sensor Appearance Failure

The representation does not transfer reliably between sensor types.

---

## 51.10 Scale Failure

The effective resolution difference is too large for the selected correspondence method.

---

## 51.11 Global Drift

Pairwise relationships are individually plausible but collectively inconsistent.

---

## 51.12 Loop Inconsistency

Redundant image paths produce substantially different geometric results.

---

## 51.13 Seam Artifact

Geometric alignment may be acceptable, but the final blend contains visible discontinuities.

---

## 51.14 Radiometric Discontinuity

The mosaic contains strong intensity or contrast transitions.

---

## 51.15 Invalid-Data Contamination

Padding or invalid pixels are incorrectly included as valid terrain.

---

## 51.16 Disconnected Mosaic

The image graph contains multiple disconnected components.

---

## 51.17 False Global Consistency

An optimization process produces a mathematically consistent configuration from incorrect correspondences.

This is a particularly important failure mode.

> **Global consistency does not guarantee physical correctness.**

---

# 52. Failure Retention

Failed cases should not be silently removed.

A benchmark should preserve:

```text
Successful cases
Failed cases
Ambiguous cases
Insufficient-data cases
```

Each case should have a documented status.

Potential statuses:

```text
SUCCESS
FAILURE
AMBIGUOUS
INSUFFICIENT_OVERLAP
INSUFFICIENT_CORRESPONDENCE
GEOMETRIC_FAILURE
VALIDATION_FAILURE
```

The exact schema is:

**[TBD]**

---

# 53. Experimental Design Principles

Every future mosaicking experiment should define:

- objective
- hypothesis
- dataset
- image-pair or image-collection selection
- preprocessing
- representation
- matching method
- geometric model
- global optimization method
- blending method
- evaluation points
- metrics
- hardware
- software environment
- random seeds where applicable
- configuration
- expected outputs
- failure policy

The established experiment template should remain the structural reference.

---

# 54. Proposed Experiment Roadmap

The following experiment IDs are proposed research identifiers. They do not imply that these experiments already exist.

## `LUNAR-MOSAIC-EXP-001` Pairwise-to-Mosaic Baseline

### Objective

Determine whether the existing pairwise registration pipeline can produce a coherent multi-image mosaic.

### Compare

```text
Pairwise Registration
vs
Multi-Image Assembly
```

### Measure

- pairwise success
- graph connectivity
- global consistency
- independent checkpoint error
- drift
- visual seam quality

---

## `LUNAR-MOSAIC-EXP-002` Registration Graph Analysis

### Objective

Measure the effect of graph structure on global mosaic consistency.

### Variables

- number of edges
- redundant edges
- graph connectivity
- weak edges
- disconnected components

### Measurements

- connected components
- loop residuals
- global geometric error
- failure propagation

---

## `LUNAR-MOSAIC-EXP-003` Pairwise vs Global Optimization

### Objective

Compare sequential transformation composition with global geometric optimization.

### Measure

- accumulated drift
- checkpoint error
- loop consistency
- spatial residuals

No optimization method should be selected without controlled evaluation.

---

## `LUNAR-MOSAIC-EXP-004` Illumination Stress Test

### Objective

Measure mosaic stability under differing illumination conditions.

### Variables

- illumination difference
- shadow configuration
- image representation
- correspondence method

### Measure

- correspondence stability
- registration error
- graph connectivity
- seam artifacts
- global consistency

---

## `LUNAR-MOSAIC-EXP-005` Scale Stress Test

### Objective

Measure mosaic behavior across different spatial resolutions.

### Variables

- scale ratio
- pyramid configuration
- representation
- sensor pair

### Measure

- matching success
- geometric accuracy
- coverage
- runtime

---

## `LUNAR-MOSAIC-EXP-006` Cross-Sensor Mosaic Study

### Objective

Investigate whether a common mosaic can be constructed from selected cross-sensor image sets.

Potential comparisons:

```text
OHRC ↔ OHRC
TMC-2 ↔ TMC-2
OHRC ↔ TMC-2
```

Additional sensor combinations should only be included when appropriate data and ground truth exist.

---

## `LUNAR-MOSAIC-EXP-007` RIFT/CFOG Mosaic Contribution

### Objective

Determine whether structure-focused representations improve the upstream correspondences sufficiently to improve multi-image registration.

### Compare

```text
SIFT baseline
Gradient representation
RIFT
CFOG
```

where technically comparable.

The experiment must distinguish representation effects from matcher effects.

---

## `LUNAR-MOSAIC-EXP-008` Learned Feature Mosaic Study

### Objective

Investigate learned correspondence pipelines in the context of multi-image mosaicking.

Potential components include:

- ALIKED
- LightGlue
- LoFTR

The exact combinations are:

**[TBD]**

---

## `LUNAR-MOSAIC-EXP-009` Seam and Blend Study

### Objective

Measure whether seam and blending methods introduce or reduce artifacts without hiding geometric errors.

### Measure

- seam discontinuity
- overlap consistency
- radiometric difference
- geometric alignment
- visual quality

---

## `LUNAR-MOSAIC-EXP-010` Independent Mosaic Validation

### Objective

Evaluate the final mosaic against independent spatial references.

### Measure

- checkpoint RMSE
- spatial residuals
- global drift
- local distortion
- coverage

---

# 55. Proposed Experiment Matrix

| Experiment           | Main Variable               | Primary Evaluation                 |
| -------------------- | --------------------------- | ---------------------------------- |
| LUNAR-MOSAIC-EXP-001 | Pairwise-to-mosaic pipeline | Global consistency                 |
| LUNAR-MOSAIC-EXP-002 | Graph structure             | Loop/global residual               |
| LUNAR-MOSAIC-EXP-003 | Global optimization         | Drift/checkpoint error             |
| LUNAR-MOSAIC-EXP-004 | Illumination                | Robustness                         |
| LUNAR-MOSAIC-EXP-005 | Scale                       | Registration accuracy              |
| LUNAR-MOSAIC-EXP-006 | Sensor pair                 | Cross-sensor performance           |
| LUNAR-MOSAIC-EXP-007 | Representation              | Correspondence and mosaic accuracy |
| LUNAR-MOSAIC-EXP-008 | Learned features            | Correspondence and runtime         |
| LUNAR-MOSAIC-EXP-009 | Seam/blending               | Mosaic continuity                  |
| LUNAR-MOSAIC-EXP-010 | Independent validation      | Geospatial accuracy                |

These are proposed experiment directions, not reported results.

---

# 56. Fair Comparison Requirements

A fair comparison should hold constant wherever possible:

- image collection
- overlap conditions
- preprocessing
- spatial resolution
- geometric model
- evaluation points
- benchmark split
- validation protocol
- hardware
- runtime measurement procedure

When a method fundamentally requires a different architecture, the difference must be documented rather than hidden.

For example, a dense pixel-wise representation should not be forced into an artificial local-keypoint framework merely to make the implementation appear identical.

---

# 57. Representation vs Matcher vs Geometry

Mosaic experiments must separate three major contributions.

### Representation

Examples:

- grayscale
- gradient
- RIFT
- CFOG
- learned feature representation

### Matching

Examples:

- descriptor matching
- local matcher
- dense correlation
- LightGlue
- other matching strategy

### Geometry

Examples:

- affine
- homography
- other model

A result such as:

```text
Method A produced a better mosaic
```

is scientifically incomplete unless these components are identified.

---

# 58. Benchmark Dataset Structure

A future mosaic benchmark may conceptually contain:

```text
benchmark/
├── images/
├── metadata/
├── footprints/
├── overlap/
├── ground_truth/
├── splits/
├── configs/
└── manifests/
```

This is a conceptual structure only.

The actual repository structure should follow the established ChandraMap benchmark architecture.

---

# 59. Mosaic Benchmark Cases

Each benchmark case should ideally specify:

```text
Case ID
Image Collection
Sensor(s)
Overlap Relationships
Spatial Reference
Resolution
Illumination Conditions
Ground Truth Availability
Expected Evaluation
```

Example conceptual identifier:

```text
LUNAR-MOSAIC-CASE-001
```

The actual benchmark identifiers remain:

**[TBD]**

---

# 60. Benchmark Splits

Potential splits include:

```text
Development
Validation
Test
Stress Test
Cross-Sensor Test
```

The final benchmark split should be defined before final evaluation.

Test data should not be repeatedly used for method tuning.

---

# 61. Leakage Prevention

Potential sources of leakage include:

- using evaluation points during transformation fitting
- tuning thresholds on the test set
- selecting images after inspecting final test performance
- removing difficult cases after seeing results
- optimizing seam placement using hidden ground truth
- choosing hyperparameters based on final test errors

The benchmark protocol should explicitly prevent these behaviors.

---

# 62. Computational Requirements

Mosaicking can become substantially more expensive than pairwise registration.

Potential computational costs include:

- global retrieval
- local matching
- repeated pairwise registration
- graph construction
- global optimization
- image warping
- resampling
- blending
- multi-resolution generation
- validation

Runtime should therefore be measured separately for:

```text
Retrieval
Matching
Geometric Verification
Global Optimization
Warping
Blending
Validation
```

where practical.

---

# 63. Memory Considerations

Large mosaics can exceed the memory required for individual images.

Future implementations may require:

- tiled processing
- chunked warping
- streaming
- memory-mapped data
- intermediate artifact management
- multi-resolution processing

The implementation strategy is:

**[TBD]**

---

# 64. Large-Scale Processing

A scalable system should avoid requiring all source images and all intermediate products to reside in memory simultaneously.

A conceptual scalable pipeline is:

```text
Image Catalog
      ↓
Candidate Pair Generation
      ↓
Pairwise Registration
      ↓
Registration Graph
      ↓
Global Optimization
      ↓
Spatial Tiling
      ↓
Mosaic Generation
      ↓
Validation
```

The exact orchestration system remains:

**[TBD]**

---

# 65. Tile-Based Mosaic Generation

Large mosaics may be generated using spatial tiles.

Conceptually:

```text
+----+----+----+
| T1 | T2 | T3 |
+----+----+----+
| T4 | T5 | T6 |
+----+----+----+
| T7 | T8 | T9 |
+----+----+----+
```

Tile processing can reduce memory requirements but introduces additional concerns:

- tile boundary artifacts
- duplicated processing
- overlap handling
- coordinate consistency
- edge effects

These should be benchmarked if a tile-based implementation is adopted.

---

# 66. Multi-Resolution Output

A future mosaic may produce:

```text
Mosaic Level 0
Mosaic Level 1
Mosaic Level 2
...
```

Each level should document:

- pixel size
- extent
- resampling method
- source coverage
- valid-data mask

A lower-resolution pyramid must not be treated as independent source data.

---

# 67. Provenance

Every mosaic should retain provenance.

At minimum, provenance should identify:

```text
Source Images
Source Image Versions
Processing Configuration
Correspondence Method
Matcher
Geometric Model
Global Optimization Method
Warping Method
Resampling Method
Blend Method
Software Commit
Environment
Benchmark Version
```

Exact provenance schema:

**[TBD]**

---

# 68. Reproducibility

A mosaic result should be reproducible from:

```text
Source Data
+
Configuration
+
Code Version
+
Environment
+
Benchmark Version
```

The repository should avoid undocumented manual steps.

If manual intervention is necessary during research, it should be recorded explicitly.

---

# 69. Determinism

Where algorithms contain stochastic operations, record:

- random seed
- library version
- configuration
- hardware where relevant

If exact determinism cannot be guaranteed, report that limitation.

---

# 70. Artifact Management

Future experiments should preserve artifacts such as:

- transformation matrices
- registration graphs
- correspondence files
- inlier masks
- residual files
- checkpoint errors
- warped images
- seam masks
- final mosaics
- configuration files
- logs

Large generated artifacts should not automatically be committed to Git.

Repository storage rules should follow the project's established data and artifact policy.

---

# 71. Recommended Result Structure

A future experiment result may conceptually contain:

```text
results/
└── lunar-mosaic/
    └── <experiment-id>/
        ├── config/
        ├── logs/
        ├── graph/
        ├── transforms/
        ├── residuals/
        ├── validation/
        ├── mosaics/
        └── report/
```

This is a conceptual research structure.

The actual repository result structure should follow the established project conventions.

---

# 72. Minimum Research Report

Each mosaicking experiment should report:

## Dataset

- image collection
- sensors
- resolution
- acquisition conditions
- overlap

## Method

- preprocessing
- representation
- matching
- geometric model
- graph construction
- global optimization
- warping
- blending

## Evaluation

- pairwise metrics
- global metrics
- independent checkpoints
- spatial residuals
- coverage
- runtime

## Failures

- failed registrations
- disconnected components
- ambiguous cases
- seam failures
- geometric failures

## Reproducibility

- code commit
- configuration
- environment
- benchmark version

---

# 73. Mosaic Quality Checklist

Before considering a mosaic experiment complete:

### Data

- [ ] Source images identified.
- [ ] Sensor information recorded.
- [ ] Spatial reference documented.
- [ ] Resolution documented.
- [ ] Image footprints available where applicable.
- [ ] Valid-data regions identified.

### Correspondence

- [ ] Candidate matches recorded.
- [ ] Geometric inliers recorded.
- [ ] Spatial coverage evaluated.
- [ ] Failed pairwise registrations retained.

### Geometry

- [ ] Transformation model documented.
- [ ] Degeneracy checked.
- [ ] Residuals evaluated.
- [ ] Independent checkpoints available where possible.
- [ ] Global consistency evaluated.
- [ ] Loop consistency evaluated where applicable.

### Mosaic

- [ ] Warping documented.
- [ ] Resampling documented.
- [ ] Seam strategy documented.
- [ ] Blending strategy documented.
- [ ] Invalid-data handling documented.

### Validation

- [ ] Pairwise accuracy measured.
- [ ] Global accuracy measured.
- [ ] Spatial residuals inspected.
- [ ] Failure cases reported.
- [ ] Visual inspection performed.
- [ ] Results are reproducible.

---

# 74. What Does Not Count as Validation

The following should not be treated as sufficient validation by themselves:

- visually pleasing mosaic
- continuous seams
- large number of matches
- high RANSAC inlier count alone
- low training error
- low pairwise residual alone
- graph connectivity alone
- successful rendering
- absence of obvious visual artifacts

A valid mosaic claim requires quantitative evidence appropriate to the intended use.

---

# 75. Mosaic-Specific Ablation Studies

Potential ablations include:

```text
Without global optimization
vs
With global optimization
```

```text
Without illumination-aware representation
vs
With illumination-aware representation
```

```text
Without retrieval
vs
With retrieval
```

```text
Pairwise graph
vs
Redundant graph
```

```text
No photometric normalization
vs
Photometric normalization
```

```text
Single-resolution
vs
Multi-resolution
```

Each ablation should isolate one major factor where possible.

---

# 76. Expected Failure Analysis

Failure analysis should answer:

1. Where did the pipeline fail?
2. Why did it fail?
3. Was the failure caused by correspondence?
4. Was it caused by geometric modeling?
5. Was it caused by global optimization?
6. Was it caused by image quality?
7. Was it caused by illumination?
8. Was it caused by scale?
9. Was it caused by sensor differences?
10. Was it caused by blending or rendering?
11. Could the failure be detected automatically?
12. Does the failure invalidate the final mosaic?

---

# 77. Automated Failure Detection

Future systems may detect suspicious registrations using combinations of:

- insufficient inliers
- poor spatial coverage
- high residuals
- large independent checkpoint error
- loop inconsistency
- abnormal transformation parameters
- disconnected graph structure
- strong overlap disagreement

Any automated acceptance criterion must be benchmarked before being used as a production gate.

---

# 78. Transformation Sanity Checks

A future implementation should inspect estimated transformations for physically or numerically suspicious behavior.

Potential diagnostics include:

- extreme scale changes
- extreme rotation
- excessive shear
- unrealistic projective distortion
- near-degenerate matrices
- unstable transformations

Exact acceptance ranges should not be invented here.

They should be established from the benchmark and documented experimentally.

---

# 79. Geospatial Consistency

If source images have reliable spatial metadata, the mosaic should be compared against that information where appropriate.

Potential comparisons include:

```text
Metadata-based location
vs
Registration-derived location
```

and:

```text
Independent reference
vs
Mosaic-derived location
```

Metadata should be treated as evidence with known accuracy, not automatically as ground truth.

---

# 80. Local vs Global Accuracy

A mosaic can contain:

```text
Low average error
+
High local error
```

or:

```text
Good local alignment
+
Global drift
```

Therefore evaluation should be spatially resolved where possible.

Useful outputs include:

- regional error maps
- checkpoint distributions
- residual vectors
- per-image errors
- per-edge errors
- per-region errors

---

# 81. Sensor-Specific Evaluation

Results should be grouped by sensor combination.

For example:

| Sensor Relationship    | Evaluation                |
| ---------------------- | ------------------------- |
| OHRC ↔ OHRC            | Separate                  |
| TMC-2 ↔ TMC-2          | Separate                  |
| OHRC ↔ TMC-2           | Separate                  |
| IIRS-derived ↔ Optical | Separate when benchmarked |

Do not aggregate heterogeneous sensor pairs into one number if the aggregation hides meaningful differences.

---

# 82. Illumination-Stratified Evaluation

Where acquisition metadata supports it, benchmark cases may be grouped by illumination conditions.

Possible categories are:

- relatively similar illumination
- moderate illumination difference
- strong illumination difference
- substantially different shadow configuration

Exact thresholds must be defined from available metadata and benchmark design.

---

# 83. Scale-Stratified Evaluation

Similarly, results may be grouped by scale relationship.

Potential groups:

```text
Near-same resolution
Moderate resolution difference
Large resolution difference
```

Exact thresholds:

**[TBD]**

---

# 84. Terrain-Stratified Evaluation

If sufficient data exist, evaluation may be stratified by terrain characteristics.

Potential categories:

- crater interiors
- crater rims
- flat regions
- heavily textured terrain
- low-texture terrain
- rugged terrain
- repetitive terrain

The actual terrain taxonomy should be defined from the available dataset.

---

# 85. Visual and Scientific Objectives

Different mosaic applications may have different objectives.

### Visualization

Priority may be:

- continuity
- coverage
- appearance

### Registration Research

Priority may be:

- geometric accuracy
- correspondence reliability
- reproducibility

### Cartographic Product

Priority may include:

- spatial reference
- geometric accuracy
- map consistency
- provenance

### Scientific Analysis

Priority may additionally include:

- preservation of relevant physical information
- radiometric interpretation
- uncertainty
- scientific traceability

The target application must therefore be defined before selecting a final mosaic objective.

---

# 86. Production Separation

This document belongs under:

```text
research/future/
```

It should not be interpreted as a production specification.

The research sequence is:

```text
Hypothesis
   ↓
Controlled Experiment
   ↓
Benchmark
   ↓
Failure Analysis
   ↓
Reproducibility
   ↓
Evidence
   ↓
Engineering Decision
```

Only after sufficient evidence should a method be considered for a production pipeline.

---

# 87. Research Status

| Component                     | Status                         | Evidence / Next Step                              |
| ----------------------------- | ------------------------------ | ------------------------------------------------- |
| Pairwise SIFT registration    | Existing V1 research baseline  | Use as controlled reference                       |
| Scale-pyramid research        | Established V1 experiment path | Evaluate contribution to multi-image registration |
| Gradient representation       | Established V1 experiment path | Compare structural representation behavior        |
| Affine vs homography          | Established V1 experiment path | Apply controlled geometric evaluation             |
| Residual analysis             | Established V1 experiment path | Extend to mosaic-level diagnostics                |
| Subpixel refinement           | Established V1 experiment path | Evaluate effect on control-point accuracy         |
| RIFT research                 | Future research direction      | Validate before implementation decision           |
| CFOG research                 | Future research direction      | Validate before implementation decision           |
| ALIKED research               | Future research direction      | Evaluate as learned feature direction             |
| LightGlue research            | Future research direction      | Evaluate as matching component                    |
| LoFTR research                | Future research direction      | Evaluate detector-free correspondence             |
| Global retrieval              | Future research direction      | Evaluate at larger image-collection scale         |
| FAISS infrastructure          | Future research direction      | Evaluate retrieval scalability                    |
| Multi-image registration      | Proposed                       | Build controlled benchmark                        |
| Global geometric optimization | Proposed                       | Define and validate objective                     |
| Seam/blending research        | Proposed                       | Evaluate after geometry                           |
| Full lunar mosaic             | Future                         | Requires validated upstream components            |
| Production mosaic system      | Not established                | Requires research evidence                        |

---

# 88. Evidence Required Before Implementation

A mosaicking approach should not be adopted solely because it produces visually attractive results.

Evidence should demonstrate, where applicable:

- reliable pairwise correspondences
- acceptable independent registration error
- sufficient spatial coverage
- stable multi-image geometry
- manageable global drift
- robust graph connectivity
- controlled failure behavior
- acceptable computational cost
- reproducibility
- meaningful benefit relative to simpler alternatives

The exact acceptance criteria are:

**[TBD]**

They should be defined before final benchmark evaluation.

---

# 89. Questions the Research Must Answer

The future work should answer:

### Geometry

- Can pairwise transformations form a globally consistent mosaic?
- When is affine sufficient?
- When is homography insufficient?
- When is a more physically grounded model required?
- How much drift accumulates over large image graphs?

### Correspondence

- Which representation produces reliable correspondences?
- How does illumination affect correspondence?
- How does scale affect correspondence?
- How does sensor difference affect correspondence?
- Are correspondences spatially distributed?

### Optimization

- Does global optimization reduce drift?
- Can incorrect edges distort the entire mosaic?
- How should unreliable edges be handled?
- How should uncertainty be propagated?

### Mosaicking

- How should seams be selected?
- How should radiometric differences be handled?
- Can blending hide geometric errors?
- How should invalid data be treated?

### Scalability

- How does runtime grow with image count?
- How many candidate pairs can be processed?
- Is global retrieval required?
- Can processing be tiled?

### Validation

- What independent references are available?
- How should mosaic-level accuracy be measured?
- How should failures be reported?
- Can quality be automatically assessed?

---

# 90. Research Principles

The following principles should govern the implementation and evaluation of this future direction.

## Principle 1 — Registration Before Blending

Do not use blending to hide geometric errors.

## Principle 2 — Independent Evaluation

Do not evaluate geometry using only the points used to estimate it.

## Principle 3 — Preserve Failures

Do not silently remove difficult image pairs or disconnected components.

## Principle 4 — Separate Components

Keep retrieval, correspondence, geometry, optimization, warping, and blending separately measurable.

## Principle 5 — Measure Spatial Distribution

A large match count is not sufficient.

## Principle 6 — Preserve Provenance

Every output should be traceable to its source images and configuration.

## Principle 7 — Do Not Assume Invariance

No representation should be described as universally illumination-invariant, scale-invariant, or sensor-invariant without evidence.

## Principle 8 — Avoid False Precision

Do not report unsupported accuracy or confidence.

## Principle 9 — Prefer Controlled Experiments

Change one major factor at a time whenever practical.

## Principle 10 — Keep Research and Production Separate

Future methods must earn production status through evidence.

---

# 91. Proposed End-to-End Future Pipeline

A mature research pipeline may eventually resemble:

```text
                 Lunar Image Collection
                          │
                          ▼
                 Metadata / Footprints
                          │
                          ▼
                 Candidate Pair Search
                          │
                ┌─────────┴─────────┐
                │                   │
          Spatial Filtering     Global Retrieval
                │                   │
                └─────────┬─────────┘
                          ▼
                 Candidate Image Pairs
                          │
                          ▼
              Preprocessing / Scaling
                          │
                          ▼
               Feature / Representation
                          │
                          ▼
                   Local Matching
                          │
                          ▼
                 Candidate Matches
                          │
                          ▼
                 Geometric Verification
                          │
                          ▼
                 Verified Pairwise Edges
                          │
                          ▼
                  Registration Graph
                          │
                          ▼
                Global Geometric Model
                          │
                          ▼
                 Global Optimization
                          │
                          ▼
                   Image Warping
                          │
                          ▼
                 Common Mosaic Grid
                          │
                ┌─────────┴─────────┐
                │                   │
        Seam Selection       Photometric Processing
                │                   │
                └─────────┬─────────┘
                          ▼
                    Mosaic Output
                          │
                          ▼
               Independent Validation
                          │
                ┌─────────┴─────────┐
                │                   │
        Geometric Metrics     Visual Diagnostics
                │                   │
                └─────────┬─────────┘
                          ▼
                    Research Report
```

This is a target research architecture, not a claim about current implementation.

---

# 92. Minimal First Implementation

The first implementation should remain intentionally small.

A reasonable research progression is:

```text
Known Image Collection
        ↓
Known Overlap Relationships
        ↓
Existing SIFT Baseline
        ↓
Pairwise Registration
        ↓
Registration Graph
        ↓
Simple Global Coordinate Assembly
        ↓
Independent Validation
```

Only after this baseline is measurable should additional complexity be introduced.

Potential additions can then include:

```text
Global Retrieval
        ↓
RIFT / CFOG
        ↓
Learned Features
        ↓
Global Optimization
        ↓
Advanced Seam Handling
        ↓
Large-Scale Tiled Processing
```

This follows the broader ChandraMap research philosophy:

> **Build small. Measure honestly. Keep the failures.**

---

# 93. Suggested Development Sequence

### Phase 1 — Pairwise Foundation

- verify the existing pairwise registration pipeline
- establish independent checkpoint evaluation
- preserve transformations and residuals

### Phase 2 — Small Image Graph

- use a small known collection
- create pairwise registration edges
- inspect graph connectivity
- inspect loop consistency

### Phase 3 — Global Geometry

- establish a simple global coordinate representation
- compare sequential composition against global optimization
- measure accumulated drift

### Phase 4 — Mosaic Rendering

- warp images
- create overlap masks
- generate an initial mosaic
- separate geometric and visual evaluation

### Phase 5 — Illumination and Representation

- evaluate gradient representation
- evaluate RIFT
- evaluate CFOG where technically appropriate
- evaluate difficult illumination cases

### Phase 6 — Cross-Sensor

- evaluate selected OHRC/TMC-2 relationships
- investigate appropriate IIRS-derived representations

### Phase 7 — Retrieval and Scale

- introduce candidate-pair retrieval
- investigate FAISS where justified
- evaluate larger collections

### Phase 8 — Advanced Optimization

- global graph optimization
- uncertainty-aware weighting
- improved seam selection
- large-scale tiled processing

---

# 94. Open Research Questions

The following remain open:

1. What geometric model is sufficiently accurate for each lunar imaging condition?
2. How much pairwise error can a mosaic tolerate before global drift becomes unacceptable?
3. Which correspondence representation is most stable under strong illumination differences?
4. Can RIFT or CFOG improve difficult lunar registrations relative to simpler structural representations?
5. How should cross-sensor correspondences be weighted?
6. How should uncertain registration edges affect global optimization?
7. How should disconnected image collections be reported?
8. How should shadows influence seam selection?
9. Can photometric normalization improve continuity without masking geometry problems?
10. What is the appropriate output coordinate system for different mosaic use cases?
11. How should multi-resolution mosaics preserve provenance?
12. How should mosaic uncertainty be represented?
13. What independent geospatial references are sufficiently reliable for validation?
14. How should large-scale mosaic generation be benchmarked?
15. At what scale does global retrieval become necessary?
16. Which failure modes can be detected automatically?
17. Can a quality-control system reject unreliable image edges before they corrupt a global mosaic?
18. How should terrain relief affect the choice of geometric model?
19. What level of accuracy is required for the intended scientific use?
20. When does increased algorithmic complexity provide measurable benefit over the simpler V1 pipeline?

---

# 95. Expected Research Outputs

A successful research phase should produce more than a final image.

Expected outputs may include:

```text
Pairwise Registration Results
Registration Graph
Transformation Records
Independent Check-Point Results
Residual Maps
Global Optimization Results
Mosaic Outputs
Seam / Blend Diagnostics
Failure Reports
Runtime Measurements
Configuration Files
Reproducibility Metadata
Experiment Report
```

The exact artifact schema is:

**[TBD]**

---

# 96. Definition of Research Success

For this future direction, research success should mean that the project can demonstrate, with reproducible evidence:

- how image relationships are identified
- how pairwise correspondences are established
- how geometric relationships are verified
- how multi-image geometry is constructed
- how global consistency is evaluated
- how mosaics are generated
- how geometric accuracy is independently measured
- how radiometric and seam effects are separated from geometric quality
- how failures are detected and retained
- how results can be reproduced

A visually convincing mosaic without this evidence is not sufficient.

---

# 97. Final Research Position

Lunar mosaicking should be treated as an extension of ChandraMap's registration problem, not as a simple image-compositing step.

The conceptual progression is:

```text
Reliable Correspondence
        ↓
Reliable Pairwise Registration
        ↓
Reliable Registration Graph
        ↓
Globally Consistent Geometry
        ↓
Controlled Warping
        ↓
Scientifically Evaluated Mosaic
```

The most important research dependency is therefore upstream registration quality.

A mosaic cannot compensate for fundamentally incorrect correspondences.

Likewise, a successful pairwise registration benchmark does not establish that a large multi-image mosaic will remain globally consistent.

The future ChandraMap mosaicking direction should therefore proceed experimentally:

```text
Pairwise Baseline
        ↓
Small Controlled Graph
        ↓
Global Consistency
        ↓
Independent Validation
        ↓
Mosaic Generation
        ↓
Illumination / Scale Stress Tests
        ↓
Cross-Sensor Evaluation
        ↓
Large-Scale Retrieval
        ↓
Advanced Global Optimization
```

Until these stages are experimentally validated, lunar mosaicking remains a **future research direction** rather than a production capability.

---

# 98. Related ChandraMap Research

This research direction should be interpreted alongside:

- `research/README.md`
- `research/literature/README.md`
- `research/notes/lunar-registration.md`
- `research/notes/illumination-invariance.md`
- `research/notes/scale-invariance.md`
- `research/notes/ground-truth-design.md`
- `research/future/README.md`
- `research/future/IIRS.md`
- `research/future/ALIKED.md`
- `research/future/LIGHTGLUE.md`
- `research/future/LOFTR.md`
- `research/future/RIFT_CFOG.md`
- `research/future/GLOBAL_RETRIEVAL.md`
- `research/future/FAISS.md`
- `experiments/v1/README.md`
- `experiments/templates/EXPERIMENT_TEMPLATE.md`
- `experiments/v1/baseline/EXP-001-sift-baseline/README.md`
- `experiments/v1/preprocessing/EXP-002-scale-pyramid/README.md`
- `experiments/v1/preprocessing/EXP-003-gradient-representation/README.md`
- `experiments/v1/geometry/EXP-004-affine-vs-homography/README.md`
- `experiments/v1/geometry/EXP-005-residual-analysis/README.md`
- `experiments/v1/refinement/EXP-006-subpixel-refinement/README.md`

These references define the broader ChandraMap research context; this document extends that context toward multi-image lunar mosaicking.

---

# 99. Source Basis

This document is based on the established ChandraMap project context and the following supplied project materials:

- ChandraMap repository structure and project information provided in the project context
- SIH26166 problem statement
- lunar image correspondence feedback
- lunar image registration feedback
- established V1 experiment structure
- established ChandraMap research notes and future-research directions referenced above

Where implementation details, benchmark thresholds, metadata schemas, exact coordinate systems, or acceptance criteria have not been established, this document intentionally marks them as **[TBD]** rather than inventing project-specific decisions.
