# ChandraMap Terminology

This document is the canonical **human-facing vocabulary reference for ChandraMap**.

It defines how important scientific, engineering, benchmarking, and research terms should be understood when reading or contributing to the project.

The main principle is:

> **Use one precise term for one scientific concept whenever the distinction matters.**

For example:

```text
Matcher
    ↓
Candidate Match
    ↓
Geometric Verification
    ↓
Verified Inlier
```

A candidate match and a verified inlier should not both be called simply a "match" when the processing stage matters.

This glossary explains terminology. It does not define the complete processing pipeline, metric equations, benchmark thresholds, implementation APIs, or repository architecture.

The detailed AI-agent terminology reference is maintained separately in [`.ai/context/TERMINOLOGY.md`](../../.ai/context/TERMINOLOGY.md). The two documents should remain scientifically consistent.

---

## 1. How to Use This Glossary

Use this document when:

- writing ChandraMap documentation
- naming scientific outputs
- discussing benchmark results
- reviewing pull requests
- describing source/reference geometry
- discussing sensors or datasets
- describing matching methods
- reporting registration error
- explaining benchmark V1–V4
- distinguishing implemented, tested, experimental, and planned work

When a term has a more exact definition elsewhere, this glossary provides the conceptual meaning and links the term to the appropriate project context.

### Precision Matters

Prefer:

> **candidate match**

over:

> match

when the correspondence has not yet passed geometric verification.

Prefer:

> **source-image pixel error**

over:

> pixel error

when the coordinate space matters.

Prefer:

> **Benchmark V1**

over:

> version 1

when referring to the classical research configuration.

---

# 2. Project and Workflow Terms

## ChandraMap

An open-source lunar image correspondence and registration research/software project focused on identifying common physical lunar features across heterogeneous observations, verifying their geometric consistency, aligning the imagery, and measuring registration quality.

ChandraMap is not primarily a Moon-map UI, generic GIS, image editor, or spacecraft mission-control system.

---

## Source Image / Source Observation

The lunar image, scientific observation, or derived registration representation being registered, localized, or mapped into a reference frame.

Prefer:

> **Source Image**

over:

> Image 1

when registration direction matters.

---

## Reference Image / Reference Observation

The image, product, tile, or region against which the source is compared and aligned.

Prefer:

> **Reference Image**

over:

> Image 2

when scientific roles matter.

---

## Source-to-Reference Direction

The direction of the geometric mapping between image coordinate systems.

Conceptually:

```text
source coordinates
        ↓
transformation
        ↓
reference coordinates
```

Transformation direction must remain explicit.

The inverse mapping is not automatically interchangeable with the forward mapping.

---

## Correspondence

A relationship between locations in two observations that are intended to represent the same physical lunar feature or terrain point.

Correspondence is a scientific concept.

A matcher proposal becomes a **candidate match** first; it should not automatically be treated as confirmed physical correspondence.

---

## Image Registration

The process of estimating and applying the geometric relationship needed to align a source observation with a reference observation.

Registration is more than resizing two images to similar dimensions.

It normally involves:

- correspondence
- geometric verification
- transformation estimation
- alignment
- evaluation

---

## Alignment

A broad descriptive term for bringing observations into a common geometric relationship.

Where technical precision matters, prefer:

> **registration**

for the formal correspondence, transformation, and evaluation process.

---

## Geolocation / Localization

Determining where an observation corresponds within a larger planetary or geospatial reference domain.

Localization is broader than local registration.

---

## Known-Overlap Registration

A registration problem where the correct or approximate overlapping reference region is already available.

Canonical **Benchmark V1** uses this problem type.

Known overlap removes the global-search problem.

It does not make the local correspondence problem trivial.

---

## Global Localization / Unknown-Location Localization

A problem where the correct reference region is not already known and must first be identified from a larger reference collection.

Conceptually:

```text
Source Observation
        ↓
Global Retrieval
        ↓
Candidate Reference Region
        ↓
Local Registration
```

---

## Core Scientific Output

The primary scientific result of ChandraMap.

Conceptually, this includes:

```text
Correspondences
      +
Geometry
      +
Registered Result
      +
Measured Quality
      +
Accept / Reject
```

A mosaic or map interface is not the core scientific output.

---

## Downstream Application

A capability that consumes scientifically validated registration results but is not itself the core correspondence problem.

Examples may include:

- mosaics
- interactive lunar maps
- dashboards
- registered visualizations
- APIs
- reports

---

# 3. Lunar Instruments and Data Terms

## Chandrayaan-2

An Indian lunar mission that provides several important instrument/data contexts used by ChandraMap.

This glossary does not attempt to document the complete mission.

---

## OHRC

**Orbiter High Resolution Camera.**

A Chandrayaan-2 instrument providing very-high-resolution panchromatic lunar imagery.

Project documentation commonly references approximately:

> **~0.25–0.32 m/px**

as broad instrument-level context.

Specific product metadata should be used for actual processing.

---

## TMC-2

**Terrain Mapping Camera-2.**

A Chandrayaan-2 panchromatic terrain-imaging instrument.

Project documentation commonly references approximately:

> **~5 m/px**

as broad context.

Use:

> **TMC-2**

rather than simply:

> TMC

when referring specifically to the Chandrayaan-2 instrument.

---

## IIRS

**Imaging Infrared Spectrometer.**

A Chandrayaan-2 hyperspectral / imaging-infrared instrument.

Project documentation commonly references approximately:

> **~80 m/px**

as broad spatial context.

IIRS should not be described simply as:

> a low-resolution camera.

Its spectral nature is scientifically important.

No single fixed spectral-band count should be assumed by ChandraMap when product metadata is authoritative.

---

## Hyperspectral Image / Hyperspectral Cube

An observation that contains many spectral measurements for each spatial location.

It is often represented conceptually as a three-dimensional array containing:

```text
spatial height
×
spatial width
×
spectral information
```

Exact array-axis order must come from the actual product or processing contract.

Do not assume one universal cube layout.

---

## Spectral Band

A defined wavelength interval or channel within hyperspectral data.

Different bands may represent the lunar surface differently.

Band selection for registration is therefore a methodological choice rather than an automatic universal rule.

---

## 2D Registration Representation

A two-dimensional image representation derived from data such as a hyperspectral cube so that conventional image-registration methods can operate on it.

Possible research representations include:

- selected spectral band
- PCA-derived component
- multi-band composite
- gradient representation
- edge/structural representation

No one representation is assumed to be universally best.

---

## LRO

**Lunar Reconnaissance Orbiter.**

The lunar mission/platform associated with several reference datasets relevant to ChandraMap.

Do not confuse:

> LRO

with:

> LROC.

---

## LROC

**Lunar Reconnaissance Orbiter Camera.**

The camera system aboard LRO.

LROC includes imaging systems such as NAC and WAC.

---

## NAC

**Narrow Angle Camera.**

A high-resolution LROC imaging system used for detailed lunar observations.

Its effective product scale varies and should be taken from actual product metadata rather than one universal value.

---

## WAC

**Wide Angle Camera.**

A LROC imaging system providing broader-area lunar coverage and context.

NAC and WAC have different scientific roles and should not be treated as interchangeable.

---

## LOLA

**Lunar Orbiter Laser Altimeter.**

An LRO instrument used to measure lunar topography/elevation.

Within ChandraMap, LOLA-related terrain information may be relevant to advanced terrain-aware research.

Its mention does not imply current integration.

---

## DEM

**Digital Elevation Model.**

A gridded representation of surface elevation.

A DEM may eventually support terrain-aware registration or geometric reasoning.

---

## DTM

**Digital Terrain Model.**

A terrain-elevation representation used in geospatial and remote-sensing contexts.

Project documentation should not assume that `DEM` and `DTM` are always identical terms in every external data source.

---

# 4. Scale and Spatial Terms

## Pixel

A discrete image sample in an image array.

A pixel count alone does not describe how much physical lunar terrain is represented.

---

## Image Dimensions

The number of pixels in an image array.

For example:

```text
width × height
```

Image dimensions are not the same as physical spatial resolution.

---

## Spatial Resolution

A description of the physical spatial detail represented by an imaging product.

Within ChandraMap, use the term carefully because `resolution` is often used ambiguously.

Where possible, distinguish:

- image dimensions
- GSD
- actual spatial detail

---

## Spectral Resolution

The ability of a spectral instrument to distinguish information across wavelength intervals.

Spectral resolution is different from spatial resolution.

---

## GSD

**Ground Sampling Distance.**

The approximate physical ground distance represented by one image pixel.

Typical units include:

```text
m/px
```

GSD is a physical sampling concept.

It is not:

- image width
- image height
- file size
- number of pixels

---

## Scale Difference

A difference in the apparent or physical pixel scale of the same terrain between source and reference observations.

In ChandraMap, `scale` should be qualified when possible because it may refer to:

- GSD
- raster resize factor
- feature scale
- geometric transformation scale

These are not interchangeable.

---

## Effective Ground Scale

The ground-scale context associated with a processed or resampled image representation.

Resampling can change the array representation.

It does not recreate missing sensor-measured detail.

---

## Upsampling

Resampling an image to a larger number of pixels.

Important:

> **Upsampling does not recover physical detail that the original sensor did not measure.**

Prefer:

> upsampled image

over:

> higher-resolution image

when only interpolation occurred.

---

## Downsampling

Resampling an image to fewer pixels.

Downsampling may be useful when comparing a high-resolution reference with a much coarser source at a more comparable effective scale.

---

## Image Pyramid

A collection of progressively resampled versions of an image used for multi-scale processing.

Image-pyramid processing is relevant to later scale-aware research.

Its definition here does not imply that canonical V1 requires it.

---

# 5. Features and Correspondences

## Feature

A visually or structurally distinctive image pattern that may help identify the same terrain across observations.

Within this scientific context, `feature` refers to image structure rather than an application/software feature.

---

## Keypoint

An image location selected by a feature detector as potentially distinctive.

A keypoint is not itself a correspondence.

---

## Detector

An algorithm or component that identifies keypoint locations.

---

## Descriptor

A numerical representation describing the local image neighborhood around a keypoint or feature.

Descriptors are commonly compared to propose local correspondences.

---

## Local Feature

A feature associated with a specific image location.

Local features are useful for point-level correspondence.

---

## Local Descriptor

A descriptor associated with a particular local feature or keypoint.

Local descriptors support point-level matching.

---

## Global Descriptor

A numerical representation summarizing an entire image, tile, or larger region.

Global descriptors are primarily useful for:

- retrieval
- candidate-region search

They are not equivalent to local descriptors used for point-level correspondence.

---

## Candidate Match

A proposed correspondence between a source-image location and a reference-image location **before geometric verification**.

This is the preferred term for matcher output prior to RANSAC or equivalent geometry checks.

**Do not confuse with:** Verified Inlier.

---

## Match

A potentially ambiguous general term.

In precise ChandraMap documentation, prefer:

- **candidate match** before geometric verification
- **verified inlier** after geometric verification

Avoid statements such as:

> 100 matches

when the processing stage is unclear.

---

## Verified Inlier

A candidate correspondence classified as geometrically consistent with the selected transformation/model.

A verified inlier is:

> **model-consistent**

but is not automatically:

> **independent physical ground truth**.

---

## Outlier

A candidate correspondence that is inconsistent with the accepted geometric model or is otherwise rejected by geometric verification.

---

## Correspondence Set

A collection of source/reference point relationships considered together during matching, verification, or evaluation.

The processing stage should be stated when necessary.

---

## Sparse Correspondence

A set of correspondences defined at selected image locations rather than for every pixel.

Classical keypoint methods and many learned local-feature approaches produce sparse correspondences.

---

## Tie Point

A corresponding point linking two observations and used to constrain their geometric relationship.

Project usage should keep tie points separate from externally known control points where that distinction matters.

---

## Control Point

A point whose position is known in an external or authoritative reference coordinate system and can be used to constrain or evaluate geometry.

A tie point is not automatically a control point.

---

## Fit Point

A point or correspondence used directly when estimating a transformation.

---

## Check Point

An independent point used to evaluate a transformation after fitting.

When independent evaluation is intended:

> **check points should not also be used as fit points.**

---

## Ground Truth

Trusted reference information used to evaluate a method.

Ground truth may include:

- independently verified correspondences
- trusted coordinates
- known correct reference regions

Matcher output is not its own ground truth.

RANSAC inliers are not automatically ground truth.

---

# 6. Matching Methods

## Matcher

A component or algorithm that proposes relationships between source and reference features or image locations.

Matcher output should generally be treated as **candidate correspondences** until geometric verification is performed.

---

## SIFT

**Scale-Invariant Feature Transform.**

A classical local-feature detector and descriptor used as the primary classical baseline family in ChandraMap.

The name should not be interpreted as guaranteeing perfect registration across arbitrary physical scale differences.

---

## RootSIFT

A transformation/normalization applied to SIFT descriptors to modify their matching behavior.

RootSIFT is not a separate keypoint detector.

If used in a benchmark, it should be identified explicitly rather than silently grouped under `SIFT`.

---

## ORB

A fast classical local-feature detector/descriptor that may be useful as an alternate or speed-oriented baseline where the project explicitly evaluates it.

Its definition here does not imply that ORB is the canonical V1 method.

---

## ALIKED

A learned sparse local-feature method used to detect keypoints and produce local feature descriptions.

Conceptually:

```text
Image
  ↓
ALIKED
  ↓
Sparse Local Features
```

ALIKED does not perform the entire registration pipeline.

---

## LightGlue

A learned matcher for compatible local feature representations.

Conceptually:

```text
Local Features
      ↓
LightGlue
      ↓
Candidate Correspondences
```

Important distinction:

```text
ALIKED
→ feature extraction

LightGlue
→ feature matching
```

---

## LoFTR

A detector-free local correspondence/matching method.

It estimates correspondences without requiring a traditional separate keypoint detector/descriptor stage.

Do not describe LoFTR simply as:

> a feature extractor.

---

## Detector-Free Matching

A correspondence approach that does not depend on a traditional detect-keypoints-then-describe-them pipeline.

LoFTR is an example of this class of method.

---

## RIFT

A remote-sensing-oriented multimodal image-matching research method discussed as a possible direction in ChandraMap research.

Its definition here does not imply implementation or current support.

---

## CFOG

A structural feature/representation approach used in remote-sensing image-registration research.

It may be relevant to ChandraMap research into multi-modal correspondence.

Its definition here does not imply implementation or current support.

---

## Matcher Confidence

A score produced by a matcher representing its internal assessment of a proposed correspondence.

Matcher confidence is not automatically equivalent to:

- geometric correctness
- registration accuracy
- calibrated probability of successful registration

A percentage-like confidence should not be reported as a probability unless the score has been defined and calibrated appropriately.

---

# 7. Geometry and Registration

## Geometric Verification

The stage that determines whether candidate matches are mutually consistent with a geometric model.

Conceptually:

```text
Candidate Matches
        ↓
Geometric Verification
        ↓
Verified Inliers
+
Rejected Outliers
```

---

## RANSAC

**Random Sample Consensus.**

A robust estimation method used to identify a geometrically consistent subset of candidate correspondences while limiting the influence of outliers.

RANSAC is not:

- a feature detector
- a descriptor
- a local matcher

---

## Inlier Threshold

A configured geometric tolerance used by a robust estimator to determine whether a candidate correspondence is sufficiently consistent with a model.

Exact values belong in benchmark/configuration documentation.

This glossary does not define one universal threshold.

---

## Inlier Mask

A Boolean or binary labeling aligned with the candidate-match list indicating which candidates were classified as inliers.

A critical invariant is:

```text
candidate match i
↔
inlier-mask entry i
```

Filtering or reordering matches must not silently break this relationship.

---

## Consensus Set

The subset of candidate correspondences considered consistent with a model during robust estimation.

Within normal ChandraMap language, the accepted consensus correspondences are typically discussed as verified inliers.

---

## Transform / Transformation

A mathematical mapping from one coordinate space to another.

A transformation is not fully described by the matrix alone when the following matter:

- model type
- direction
- source coordinate space
- target/reference coordinate space

---

## Transform Direction

The direction in which a transformation maps coordinates.

For example:

```text
source → reference
```

is different from:

```text
reference → source
```

Always state the direction when ambiguity is possible.

---

## Translation

A transformation that shifts coordinates by an offset.

---

## Rotation

A transformation that changes orientation around an origin or center.

---

## Geometric Scale Factor

A scale component of an estimated geometric transformation.

Do not confuse with:

> GSD.

GSD describes physical sampling.

A transformation scale factor describes geometric mapping between coordinate systems.

---

## Similarity Transform

A geometric transformation combining translation, rotation, and uniform scale.

It is less flexible than a full affine transformation.

---

## Affine Transform

A planar transformation capable of representing:

- translation
- rotation
- scale
- shear

while preserving parallel lines.

Within ChandraMap, affine geometry is a useful baseline approximation where appropriate.

It does not model arbitrary three-dimensional lunar terrain relief.

---

## Homography

A planar projective transformation more flexible than an affine model.

A homography can model projective relationships under suitable assumptions.

It is not a universal model for:

- arbitrary terrain relief
- all viewpoint differences
- full 3D lunar geometry

---

## Degenerate Geometry

A point configuration or estimation condition that does not sufficiently constrain a reliable transformation.

Examples may include:

- too few points
- severe clustering
- nearly collinear points
- numerically unstable configurations

---

## Residual

The remaining difference between an observed correspondence and the position predicted by an estimated model.

Residual values should include:

- coordinate context
- units

when reported numerically.

---

## Reprojection Error

The distance between an observed point and the position predicted after applying the estimated transformation.

The exact coordinate domain, evaluation population, and units belong in metric documentation.

---

## Warp / Warping

Resampling an image according to an estimated transformation into another image or coordinate frame.

Important:

> **Successful warping does not prove accurate registration.**

Warping applies geometry.

Evaluation determines whether that geometry is trustworthy.

---

## Registered Image

A derived image produced by transforming the source into the reference frame.

The term does not by itself imply that the registration passed a particular quality criterion.

---

## Registered Preview

A registered visualization intended primarily for inspection or communication.

Prefer this term when the transformed raster is mainly a diagnostic output rather than the authoritative scientific result.

---

## Global Geometry

A single transformation applied across the entire relevant overlap.

Affine transforms and homographies are examples of global 2D models.

---

## Local / Piecewise Geometry

A geometry model that allows different areas of an overlap to use different or spatially varying transformations.

This is generally an advanced research concept rather than part of the classical V1 baseline.

---

## Sub-Pixel

A positional precision or error smaller than one pixel in a specified image coordinate system.

Critical distinction:

> **Sub-pixel does not automatically mean sub-metre.**

---

## Fractional-Pixel Coordinate

An image coordinate containing non-integer values.

Example conceptually:

```text
(x = 100.4, y = 217.8)
```

Fractional coordinates can arise during refinement or geometric transformation.

---

## Sub-Pixel Refinement

A process that refines initially estimated point locations to fractional-pixel precision.

Where the pipeline methodology requires it, refinement occurs after initial geometric verification and is followed by final transform refitting.

Its definition here does not imply canonical V1 inclusion.

---

## Final Transform Refit

Re-estimating the transformation using the final accepted/refined tie-point coordinates.

Conceptually:

```text
Verified Inliers
      ↓
Point Refinement
      ↓
Refined Tie Points
      ↓
Final Transform Refit
```

Using an outdated pre-refinement transform after tie points change would be scientifically inconsistent.

---

## Terrain-Aware / DEM-Aware Registration

Registration that explicitly uses surface elevation or terrain geometry to model effects not captured adequately by simple planar transformations.

This is an advanced research concept.

Its definition does not imply current implementation.

---

# 8. Coordinates and Geospatial Terms

## Image Coordinate

A position measured within an image.

Common representations include:

- `(x, y)`
- `(row, column)`

These should not be treated as interchangeable.

---

## `(x, y)`

A common geometric coordinate convention in which:

```text
x → horizontal direction
y → vertical direction
```

Library-specific conventions must still be verified at implementation boundaries.

---

## `(row, column)`

An array-index convention in which:

```text
row    → vertical array index
column → horizontal array index
```

In many image-processing systems:

```text
x ≈ column
y ≈ row
```

but code must not silently assume this at every boundary.

---

## Pixel Coordinate

A position expressed in image-pixel units.

Pixel coordinates must identify which image they belong to when source/reference ambiguity exists.

---

## Pixel Origin

The convention defining where image coordinates begin.

Different libraries and geospatial representations may use different origin/center conventions.

Do not assume a universal project convention unless explicitly defined by the relevant contract.

---

## Geospatial Coordinate

A coordinate representing a location in a planetary/geographic/projected reference system rather than image-array space.

---

## CRS

**Coordinate Reference System.**

The coordinate framework used to interpret geospatial coordinates.

---

## Lunar CRS

A coordinate reference system appropriate to lunar spatial data.

Important:

> Lunar data must not be silently treated as Earth WGS84 / EPSG:4326.

Planetary coordinate systems require their own correct spatial definitions.

---

## Latitude / Longitude

Angular coordinates describing position on a planetary body.

The exact planetary datum and longitude convention must be understood before converting or comparing coordinates.

---

## Longitude Convention

The convention used to represent longitude.

Examples include differences in:

- positive-east vs positive-west
- `0–360°`
- `-180–180°`

Do not convert between conventions silently.

---

## Projection

A mathematical mapping between a planetary surface/reference body and a planar coordinate system.

---

## Map-Projected Image

An image product whose pixels have been mapped into a defined geospatial/projected coordinate framework.

Do not assume every ChandraMap input is map-projected.

---

## Orthorectification

Geometric correction intended to reduce distortions related to viewing geometry and terrain, producing an image tied more consistently to a reference surface.

Exact orthorectification methodology depends on the scientific product.

---

## Footprint

The spatial region on the lunar surface covered by an observation or product.

A footprint is not always equivalent to a simple rectangular bounding box.

---

## Bounding Box

A rectangular spatial extent that encloses a region.

A bounding box may approximate a footprint but does not necessarily represent its exact shape.

---

## Overlap

The lunar surface area represented by both the source and reference observations.

---

## Partial Overlap

A case where only part of the source and reference observations represents the same terrain.

Not every feature is expected to have a valid counterpart under partial overlap.

---

# 9. Evaluation and Metric Terms

## Quality Metric

A numerical or categorical measure used to describe some aspect of correspondence, geometry, registration, retrieval, or system behavior.

Different metrics answer different questions.

---

## RMSE

**Root Mean Square Error.**

A summary measure of positional error magnitude.

A meaningful RMSE report must identify:

- evaluated point population
- coordinate space
- units
- whether the evaluated points were used for fitting

Do not assume all RMSE values are directly comparable.

---

## Source-Image Pixel Error

Registration or positional error measured in pixel units of the source-image coordinate system.

Prefer explicit wording such as:

```text
0.8 source-image px
```

rather than:

```text
0.8 px
```

when source/reference pixel scales differ.

---

## Reference-Image Pixel Error

Registration or positional error measured in pixel units of the reference-image coordinate system.

Do not silently switch between source and reference pixel domains.

---

## Ground Error

Registration or localization error expressed in a physical ground unit such as metres.

Ground error should be reported only when:

- valid spatial information exists
- the coordinate context supports conversion
- the conversion is scientifically meaningful

---

## Fit Residual

A residual measured on points used to estimate the transformation.

Useful for model diagnostics.

**Do not confuse with:** Independent Check-Point Accuracy.

---

## Check-Point Error

Error measured using independent check points that were not used for fitting.

Where valid independent check points exist, this provides stronger evidence of final registration accuracy than fit residual alone.

---

## Inlier Count

The number of candidate correspondences classified as geometrically consistent with the selected model.

A high inlier count alone does not prove high registration accuracy.

---

## Inlier Ratio

The proportion of the relevant candidate-match population classified as verified inliers.

Conceptually:

```text
verified inliers
----------------
candidate matches
```

Exact denominator and edge-case behavior belong in metric documentation.

---

## Spatial Coverage

A measure of how widely verified correspondences are distributed across the relevant image or overlap region.

Coverage complements match count.

---

## Grid Coverage

A spatial-coverage approach based on how many predefined image/grid regions contain useful correspondences.

Exact grid construction belongs in metric documentation.

---

## Convex-Hull Coverage

A coverage measure based on the area spanned by a point set relative to a relevant image or overlap area.

Exact normalization and edge-case behavior belong in metric documentation.

---

## Success Rate

The proportion of benchmark cases satisfying the benchmark-defined success criteria.

The denominator must be explicit.

---

## Failure Rate

The proportion of benchmark cases that fail according to the defined benchmark policy.

Failure and rejection categories should not be hidden when reporting aggregate performance.

---

## Confidence

A generic term for a score indicating certainty or reliability.

Within ChandraMap, `confidence` should be used only when the score has a clear definition.

Avoid arbitrary percentage-style confidence values.

---

## Quality Gate

A defined rule used to determine whether scientific evidence is sufficient to accept a result.

A quality gate may consider several metrics.

Exact conditions belong in benchmark/configuration documentation.

---

## Accept / Refine / Reject

A conceptual quality decision:

### Accept

The available evidence satisfies the defined acceptance criteria.

### Refine

Additional processing is justified before a final decision.

### Reject

The available evidence is insufficient for trustworthy registration.

Not every benchmark version requires all three branches.

---

## Accepted Registration

A registration satisfying the defined scientific quality criteria.

No universal acceptance threshold is defined by this glossary.

---

## Rejected Registration

A processed registration attempt for which the available evidence is insufficient for acceptance.

Rejection can represent correct scientific behavior.

---

## Failed Run

An execution in which the method could not produce the intended scientific result.

Exact status semantics should follow the project's result/benchmark contract.

---

## Invalid Run

A run compromised by factors such as:

- corrupted input
- incorrect configuration
- implementation defect
- unusable benchmark definition

An invalid run should not automatically be counted as a normal scientific method failure.

---

## Not Run

No execution occurred.

`Not Run` is not equivalent to:

- Passed
- Failed
- Rejected

---

## Runtime

Measured execution time for a clearly defined portion of processing.

Runtime reporting should state the measured scope and relevant hardware context where needed.

---

# 10. Retrieval Terms

## Global Retrieval

Searching a larger reference collection to identify regions likely to contain the same lunar terrain as a source observation.

It answers:

> **Where should local registration look?**

---

## Local Matching

Finding point-level correspondences within a selected source/reference pair.

It answers:

> **Which local points correspond?**

Global retrieval and local matching are different tasks.

---

## Retrieval Candidate

A reference tile or region returned by a global-search stage as a possible location for the source observation.

**Do not confuse with:** Candidate Match.

A retrieval candidate is a **region**.

A candidate match is a **point correspondence**.

---

## Candidate Region

A reference region selected for further local matching and geometric verification.

---

## Reference Tile

A spatial subset of a larger reference dataset prepared for retrieval or local processing.

---

## Reference Pyramid

A set of reference representations prepared at multiple scales.

This is distinct from a simple image pyramid when the representations are used as part of a larger reference-search system.

---

## Top-K

The highest-ranked `K` retrieval candidates returned by a search stage.

The value of `K` belongs in configuration/benchmark documentation.

---

## Top-1

The highest-ranked retrieval candidate.

A Top-1 candidate is not automatically correct.

---

## Recall@K

A retrieval metric describing whether/how often the correct reference candidate appears among the top `K` returned candidates.

Exact formula and benchmark truth definitions belong in metric documentation.

---

## Vector Index

A data structure designed to support efficient similarity search over numerical vectors.

---

## FAISS

A library/tool used for efficient vector similarity search and indexing.

In ChandraMap's conceptual retrieval flow:

```text
Reference Tile
      ↓
Global Descriptor
      ↓
Vector Index

Source
      ↓
Global Descriptor
      ↓
Vector Search
      ↓
Candidate Reference IDs
```

FAISS does **not** by itself:

- extract local image features
- establish point correspondences
- run RANSAC
- estimate image-registration geometry

Avoid phrases such as:

> FAISS found the matching lunar points.

Prefer:

> Vector retrieval returned candidate reference regions.

---

## Metadata-Constrained Search

Using reliable scientific metadata such as location or footprint to restrict the reference search region.

Using valid metadata is scientifically appropriate.

It should not be discarded merely to force image-only retrieval.

---

## Image-Only Retrieval

Candidate-region search based primarily on visual/image representations rather than trusted location metadata.

Useful when reliable spatial metadata is unavailable or intentionally excluded by the experiment.

---

## Offline Retrieval Preparation

Precomputing reference-side information before a query arrives.

Potential examples include:

- reference tiles
- global descriptors
- vector indexes
- ID/metadata mappings

---

## Online Retrieval

Processing a query/source observation against a prepared reference collection to generate candidate regions.

---

# 11. Benchmark and Research Terms

## Benchmark

A controlled evaluation used to compare methods or configurations under defined conditions.

A benchmark is not simply any demonstration run.

---

## Baseline

A simple, reproducible reference method against which later improvements are measured.

Within ChandraMap:

> **Benchmark V1 is the primary classical baseline.**

---

## Benchmark V1

The classical known-overlap local-registration baseline.

Conceptually:

```text
Known Overlap
      ↓
SIFT
      ↓
Classical Matching
      ↓
RANSAC
      ↓
Affine / Homography
      ↓
Registration
      ↓
Evaluation
```

Exact scope is defined in [Benchmark V1 Scope](./v1-scope.md).

---

## Benchmark V2

The benchmark stage focused primarily on investigating sensor-aware and scale-aware improvements beyond the classical baseline.

Its exact methodology belongs in its benchmark specification.

---

## Benchmark V3

The benchmark/research stage focused on advanced matching and, where appropriate, candidate-region retrieval.

Its exact methodology belongs in its benchmark specification.

---

## Benchmark V4

The advanced research configuration focused on robustness, refinement, advanced geometry, uncertainty, and related research directions.

V4 is not automatically:

- best
- final
- universally superior

---

## Software Version

A release/package/API version belonging to software versioning.

For example:

```text
software v1.0.0
```

is conceptually different from:

```text
Benchmark V1
```

Do not use the terms interchangeably.

---

## Ablation

An experiment designed to isolate the effect of removing, adding, or changing one controlled component or factor.

---

## Controlled Comparison

A comparison in which relevant conditions are intentionally held constant so that a methodological change can be interpreted more clearly.

---

## Stress Case

A benchmark case representing a deliberately challenging condition.

Examples may include:

- large scale difference
- strong illumination difference
- modality mismatch
- low-feature terrain
- partial overlap

---

## Stress Category

A group of benchmark cases sharing a defined challenge type.

Category definitions should be based on meaningful data properties rather than simply whether one method happened to fail.

---

## Oracle Experiment

An analysis that deliberately uses information unavailable during normal inference to estimate an upper bound or diagnostic potential.

Oracle results must not be presented as normal system performance.

---

## Data Leakage

Evaluation or test information influencing:

- training
- parameter tuning
- candidate selection
- method selection
- transformation fitting

in a way that invalidates independent evaluation.

---

## Geographic Leakage

Supposedly independent training/validation/test partitions containing overlapping, nearly identical, or geographically duplicated lunar regions.

---

## Domain Shift

A difference between the data distribution used to develop/train a method and the data on which it is evaluated.

Example:

> a model developed primarily on terrestrial imagery and evaluated on lunar imagery.

---

## Reproducibility

The ability to reconstruct a scientific result from the relevant information, potentially including:

- data
- configuration
- code
- metric definition
- model/checkpoint
- seed
- environment

as applicable.

---

## Experiment

A controlled research execution designed to answer a specific question.

Do not use `experiment` as a synonym for every ordinary program execution.

---

## Hypothesis

A testable proposed explanation or prediction that has not yet been established by evidence.

---

## Research Result

An observed outcome produced by an executed experiment or benchmark.

A planned experiment is not a result.

---

## Conclusion

An interpretation supported by results.

A conclusion should remain bounded by:

- evaluated data
- methodology
- assumptions
- limitations

---

## Negative Result

A valid research result in which a proposed method:

- does not improve performance
- performs worse
- creates instability
- exposes a limitation

Negative results are scientifically useful and should not automatically be hidden.

---

# 12. Data and Provenance Terms

## Dataset

A collection of scientific products, benchmark cases, derived data, or other data items used for project work.

Dataset membership should be identifiable when used for reproducible evaluation.

---

## Product

A scientific data item distributed by a mission/provider with associated metadata and provenance.

---

## Product ID

An identifier associated with a particular scientific product.

Use the provider/project-defined identifier where applicable.

Do not invent product identifiers.

---

## Raw Data

A term that should be used according to provider/project processing terminology.

Do not use `raw` loosely when a mission defines specific product-processing levels.

---

## Calibrated Product

A product that has undergone sensor/instrument calibration processing.

Exact mission processing levels belong in dataset/provider documentation.

---

## Derived Data / Derived Representation

Data produced from an original scientific product through ChandraMap or research processing.

Examples may include:

- normalized image
- resampled image
- pyramid level
- selected spectral band
- PCA component
- gradient representation
- registered raster

Derived data should remain distinguishable from authoritative source products.

---

## Metadata

Descriptive information associated with a scientific product, result, or experiment.

Potential examples include:

- mission
- instrument
- product ID
- acquisition context
- GSD
- projection
- footprint
- illumination information

Not every product provides every field.

---

## Product Metadata

Metadata supplied or derived for a specific scientific product.

Important rule:

> **Specific product metadata should take precedence over broad approximate instrument-level values for actual scientific processing.**

---

## Provenance

Information describing:

- where data came from
- which source products were used
- which processing steps created a derived result

Provenance supports reproducibility and scientific auditability.

---

## Pair

A source/reference combination selected for correspondence or registration evaluation.

---

## Pair ID

A stable benchmark identifier representing a particular source/reference evaluation case.

This glossary does not define a required ID format.

---

## Benchmark Manifest

A reproducible record defining which cases belong to a benchmark and the relevant references needed to execute/evaluate them.

Exact schema belongs in benchmark/data documentation.

---

## Fixture

A small controlled data object used for software testing.

A fixture is not automatically a scientific benchmark dataset.

---

## Artifact

A generated output produced by processing.

Examples may include:

- registered image
- match visualization
- report
- descriptor file
- retrieval index
- metrics export

Artifacts are derived outputs.

They are not authoritative source mission products.

---

## Cache

A disposable or rebuildable stored result intended to reduce repeated computation.

A cache is not:

- source scientific data
- benchmark ground truth
- canonical provenance

---

## Model Checkpoint

Saved parameters/state for a learned model.

**Do not confuse with:** Evaluation Check Point.

---

## Evaluation Check Point

An independent geometric point used to evaluate a transformation.

Use the explicit phrase:

> evaluation check point

when confusion with an ML model checkpoint is possible.

---

# 13. Project Status and Engineering Terms

## Implemented

The capability exists in current code.

`Implemented` does not automatically mean:

- tested
- benchmarked
- supported

---

## Tested

Relevant tests or validation procedures were actually executed.

The existence of a test file does not by itself justify saying:

> tested.

---

## Documented

The capability, concept, or design is described in project documentation.

Important:

> **Documented ≠ Implemented**

---

## Experimental

A capability or method being investigated without the stability/canonical guarantees of established project behavior.

---

## Planned

Intended future work.

A planned capability is not a current feature.

---

## Proposed

An idea under consideration.

A proposal does not necessarily represent an approved roadmap commitment.

---

## Supported

A capability or data type intentionally accepted and maintained as part of current project behavior.

Do not use `supported` merely because one experimental example once worked.

---

## Deprecated

Still present but intentionally being replaced or moved toward removal.

Do not invent a removal date unless the project defines one.

---

## Removed

No longer present in current project behavior.

---

## Canonical

The officially defined reference configuration, interpretation, or methodology within ChandraMap.

Example:

> **canonical Benchmark V1**

means the approved baseline methodology—not every experiment that happens to be related to V1.

---

## Core

Functionality central to ChandraMap's primary scientific correspondence and registration mission.

---

## Downstream

Functionality that depends on core scientific results but is not itself part of the central correspondence/registration problem.

---

## Optional

A capability not required for the main or canonical workflow.

---

## Failure

A broad term indicating that a requested outcome could not be produced.

Where precision matters, qualify it as:

- scientific failure
- registration failure
- retrieval failure
- infrastructure failure
- software error

---

## Rejection

A deliberate scientific decision that available evidence is insufficient to accept a registration.

Rejection can represent correct system behavior.

---

## Error

An ambiguous term that may refer to either:

1. a numerical/scientific error measurement, or
2. a software fault/exception.

Prefer explicit phrases such as:

- registration error
- reprojection error
- software error

when ambiguity is possible.

---

# 14. Commonly Confused Terms

| Term                    | Do Not Confuse With           | Difference                                                                               |
| ----------------------- | ----------------------------- | ---------------------------------------------------------------------------------------- |
| **Candidate Match**     | Verified Inlier               | Candidate is before geometry; verified inlier is model-consistent after geometry         |
| **Verified Inlier**     | Ground Truth                  | Inlier is model-consistent; ground truth is independently trusted evaluation information |
| **Source Image**        | Reference Image               | Their roles determine registration direction                                             |
| **Image Dimensions**    | GSD                           | Pixel count versus physical ground sampling                                              |
| **Spatial Resolution**  | Spectral Resolution           | Spatial terrain detail versus wavelength discrimination                                  |
| **Upsampling**          | Resolution Recovery           | Interpolation increases samples but does not recreate missing physical detail            |
| **GSD**                 | Geometric Scale Factor        | Physical ground sampling versus transformation scale                                     |
| **Local Descriptor**    | Global Descriptor             | Point-level matching versus image/region retrieval                                       |
| **Retrieval Candidate** | Candidate Match               | Candidate region versus candidate point correspondence                                   |
| **Global Retrieval**    | Local Matching                | Finds where to search versus which local points correspond                               |
| **Registration**        | Retrieval                     | Aligns observations versus finds candidate regions                                       |
| **FAISS**               | Local Image Matcher           | Vector similarity search versus point correspondence                                     |
| **ALIKED**              | LightGlue                     | Local feature extraction versus feature matching                                         |
| **LoFTR**               | Traditional Feature Extractor | Detector-free correspondence method rather than a separate keypoint detector             |
| **RANSAC**              | Matcher                       | Geometric verification/model estimation versus correspondence proposal                   |
| **Warp**                | Validation                    | Applies transform versus evaluates correctness                                           |
| **Fit Point**           | Check Point                   | Used for estimation versus independent evaluation                                        |
| **Fit Residual**        | Independent Accuracy          | Error on fitted data versus held-out evaluation                                          |
| **Sub-Pixel**           | Sub-Metre                     | Image-space magnitude versus physical ground distance                                    |
| **`(x, y)`**            | `(row, column)`               | Geometric coordinates versus array indexing                                              |
| **Benchmark V1**        | Software `v1.0.0`             | Research configuration versus software release                                           |
| **Documented**          | Implemented                   | Description exists versus code exists                                                    |
| **Implemented**         | Tested                        | Code exists versus validation was actually executed                                      |
| **Rejected**            | Software Error                | Deliberate scientific abstention versus implementation fault                             |
| **Failed**              | Not Run                       | An attempted execution failed versus no execution occurred                               |
| **LRO**                 | LROC                          | Mission/platform versus camera system                                                    |
| **NAC**                 | WAC                           | Narrow-angle detailed imagery versus wider-area imagery                                  |
| **Model Checkpoint**    | Evaluation Check Point        | Saved ML model state versus independent geometry-evaluation point                        |

---

# 15. Preferred ChandraMap Wording

## Prefer

Use precise language such as:

- **Source Image**
- **Reference Image**
- **Candidate Match**
- **Verified Inlier**
- **Rejected Outlier**
- **Source → Reference Transform**
- **Registered Preview**
- **Source-Image Pixel Error**
- **Reference-Image Pixel Error**
- **Ground Error in Metres**, when scientifically valid
- **Global Retrieval Candidate**
- **Local Correspondence**
- **Benchmark V1**
- **Terrain Mapping Camera-2 (TMC-2)**
- **IIRS hyperspectral / imaging-infrared data**
- **2D registration representation**
- **Explicit rejection**
- **Measured registration quality**

---

## Avoid or Use Carefully

| Prefer                  | Avoid / Use Carefully    | Reason                                                       |
| ----------------------- | ------------------------ | ------------------------------------------------------------ |
| Candidate Match         | Match                    | Processing stage becomes explicit                            |
| Verified Inlier         | Good Match               | Geometric meaning is clearer                                 |
| Source / Reference      | Image 1 / Image 2        | Direction remains clear                                      |
| Source-image px         | Pixels                   | Unit domain is explicit                                      |
| Upsampled               | Higher resolution        | Avoids false physical-detail claim                           |
| Registered Preview      | Correct Image            | Registration still requires evaluation                       |
| Retrieval Candidate     | Match                    | Region retrieval differs from point matching                 |
| TMC-2                   | TMC                      | Correct instrument naming                                    |
| IIRS hyperspectral data | Low-resolution camera    | Preserves modality                                           |
| Benchmark V1            | Version 1                | Avoids software-version confusion                            |
| Explicit Rejection      | Failed Completely        | Rejection can be correct scientific behavior                 |
| Global Retrieval        | FAISS Matching           | FAISS is a vector-search implementation, not the task itself |
| Local Correspondence    | FAISS Match              | FAISS does not establish local point correspondence          |
| LoFTR Correspondence    | LoFTR Feature Extraction | LoFTR is detector-free matching/correspondence               |
| Registration Error      | AI Accuracy              | Identifies an actual measurable quantity                     |

---

## Avoid "AI Accuracy"

`AI accuracy` is not a scientifically useful ChandraMap metric.

Use the actual measurement, such as:

- RMSE
- inlier ratio
- spatial coverage
- Recall@K
- success rate
- failure rate

---

## Avoid Undefined Confidence Percentages

Do not write:

> 92% confidence

unless a formally defined, validated, and appropriately calibrated confidence quantity exists.

---

## Avoid "Perfect Match"

Avoid describing a scientific registration as:

> perfect

without a formal definition and evidence.

---

## Avoid "High Resolution" Without Context

Where scientific precision matters, prefer actual product/GSD context rather than vague labels.

---

## Avoid Ambiguous Pixel Error

Prefer:

```text
0.8 source-image px
```

over:

```text
0.8 px
```

when source/reference pixel domains matter.

---

## Avoid Ambiguous Coordinates

Avoid:

```text
point (100, 200)
```

when the coordinate convention matters.

Prefer:

```text
source-image (x, y)
```

or:

```text
array (row, column)
```

as appropriate.

---

## Avoid Ambiguous Transform Language

Prefer:

> source → reference transform

over:

> transformation matrix

when direction matters.

---

## Avoid "Convert IIRS to Grayscale"

Prefer:

> derive a defined 2D registration representation from IIRS data

because the latter preserves the scientific distinction between native hyperspectral data and a derived image used for matching.

---

## Avoid "Increase the Resolution"

If only raster dimensions changed, prefer:

> upsample the image

or:

> resample the image

Physical sensor resolution has not increased.

---

## Avoid Vague Benchmark Conclusions

When real results exist, prefer evidence-specific wording such as:

> V2 produced lower measured RMSE on the evaluated subset.

Avoid:

> V2 is better.

without defining the metric and evaluation population.

---

# 16. Abbreviations

| Abbreviation | Meaning                                                              |
| ------------ | -------------------------------------------------------------------- |
| **OHRC**     | Orbiter High Resolution Camera                                       |
| **TMC-2**    | Terrain Mapping Camera-2                                             |
| **IIRS**     | Imaging Infrared Spectrometer                                        |
| **LRO**      | Lunar Reconnaissance Orbiter                                         |
| **LROC**     | Lunar Reconnaissance Orbiter Camera                                  |
| **NAC**      | Narrow Angle Camera                                                  |
| **WAC**      | Wide Angle Camera                                                    |
| **LOLA**     | Lunar Orbiter Laser Altimeter                                        |
| **GSD**      | Ground Sampling Distance                                             |
| **CRS**      | Coordinate Reference System                                          |
| **DEM**      | Digital Elevation Model                                              |
| **DTM**      | Digital Terrain Model                                                |
| **SIFT**     | Scale-Invariant Feature Transform                                    |
| **ORB**      | Oriented FAST and Rotated BRIEF                                      |
| **RANSAC**   | Random Sample Consensus                                              |
| **RMSE**     | Root Mean Square Error                                               |
| **PCA**      | Principal Component Analysis                                         |
| **FAISS**    | Vector similarity-search/indexing library used in retrieval contexts |

The glossary intentionally focuses on project-relevant meaning rather than providing exhaustive algorithm histories.

---

# 17. Related Documents

- [Project Overview](./overview.md) — explains what ChandraMap is
- [Problem Statement](./problem-statement.md) — defines the scientific and engineering challenge
- [Project Goals](./goals.md) — defines desired project outcomes
- [Project Non-Goals](./non-goals.md) — defines boundaries around the project mission
- [Benchmark V1 Scope](./v1-scope.md) — defines the human-facing classical baseline scope
- [AI Terminology Reference](../../.ai/context/TERMINOLOGY.md) — stricter terminology guidance for AI agents and maintainers
- [Project Context](../../.ai/context/PROJECT_CONTEXT.md) — canonical project identity and scientific purpose
- [Domain Context](../../.ai/context/DOMAIN_CONTEXT.md) — lunar imaging and scientific constraints
- [Dataset Context](../../.ai/context/DATASETS.md) — detailed product, sensor, metadata, and provenance guidance
- [Canonical AI V1 Scope](../../.ai/context/V1_SCOPE.md) — authoritative detailed V1 contract
- [Processing Pipeline](../../.ai/architecture/PIPELINE.md) — detailed processing order and stage semantics
- [System Overview](../../.ai/architecture/SYSTEM_OVERVIEW.md) — architectural responsibility boundaries
- [Data Flow](../../.ai/architecture/DATA_FLOW.md) — data, coordinate, transform, and result semantics
- [Benchmark Rules](../../.ai/development/BENCHMARK_RULES.md) — controlled scientific evaluation rules
- [Testing Rules](../../.ai/development/TESTING_RULES.md) — software and scientific testing standards
- [Documentation Rules](../../.ai/development/DOCUMENTATION_RULES.md) — documentation and terminology consistency rules

This glossary should be updated when ChandraMap introduces a new cross-cutting scientific concept, changes canonical terminology, or discovers an ambiguity that could affect code, benchmarks, documentation, reports, or scientific interpretation.

<!-- Source specification: :contentReference[oaicite:0]{index=0} -->
