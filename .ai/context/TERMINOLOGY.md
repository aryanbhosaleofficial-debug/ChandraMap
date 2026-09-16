# ChandraMap Terminology

This document is the canonical terminology reference for **ChandraMap**.

Its purpose is to keep technical language consistent across:

- source code
- documentation
- configuration
- APIs and schemas
- benchmark reports
- experiments
- research notes
- backend services
- frontend labels
- scientific results

Definitions in this file are intentionally concise. Scientific background belongs in `DOMAIN_CONTEXT.md`; exact dataset/product information belongs in `DATASETS.md`; pipeline behavior belongs in architecture documentation; exact metric equations belong in benchmark/metrics documentation.

---

## 1. How to Use This Glossary

Use this file whenever a term:

- appears across multiple modules or documents
- has a project-specific meaning
- could be scientifically ambiguous
- affects API/schema naming
- affects benchmark interpretation
- could be confused with a related concept

Where terminology conflicts with established project wording, prefer the canonical wording defined here unless more specific current project documentation deliberately defines otherwise.

Do not infer implementation status from the presence of a glossary entry.

A term may describe:

- implemented functionality
- experimental functionality
- planned functionality
- a general scientific concept

Implementation status must be determined separately.

---

## 2. Canonical Naming Rules

Use terminology precisely.

Prefer:

- **ChandraMap**, not inconsistent project-name variants
- **TMC-2**, not `TMC`, when specifically referring to the Chandrayaan-2 Terrain Mapping Camera-2
- **candidate match** before geometric verification
- **verified inlier** after geometric verification
- **source image** and **reference image** when directional roles matter
- **global retrieval** for region-level search
- **local matching** for point-level correspondence
- **source-image pixel error** when error is specifically measured in source pixels
- **ground error in metres** only when the physical conversion is valid and defined

Do not call matcher output "verified" merely because a matching algorithm reports high confidence.

---

# 3. Project Terms

## ChandraMap

An open-source lunar image correspondence and registration research system designed to identify the same physical lunar terrain across observations, geometrically align those observations, and evaluate registration quality.

ChandraMap is primarily a **correspondence and registration project**, not merely a Moon-map application.

---

## Correspondence

A relationship between locations in two images believed to represent the same physical lunar feature or surface location.

**Do not confuse with:** Registration.

---

## Registration

The process of geometrically aligning one image or image-derived product with another image or reference coordinate frame using reliable correspondences and an appropriate transformation.

Registration is broader than feature matching alone.

---

## Image Pair

Two images being compared for correspondence, registration, retrieval evaluation, or another defined experiment.

---

## Source Image

The image being localized, matched, or transformed relative to a reference.

**Direction matters:** A transformation may map source coordinates into reference coordinates.

---

## Reference Image

The image used as the target coordinate frame or comparison reference for a source image.

Source and reference roles must not be assumed interchangeable.

---

## Reference Dataset

A collection of imagery or products used as reference material for:

- search
- retrieval
- localization
- correspondence
- registration
- evaluation

---

## Registered Image

An image that has been geometrically transformed into alignment with the selected reference coordinate frame.

A registered image alone does not prove registration accuracy.

---

## Registered Preview

A visualization used to inspect a registration result.

A registered preview is evidence for visual inspection, not an independent accuracy metric.

---

## Registration Result

The structured outcome of a registration attempt.

Depending on project design, it may contain:

- correspondences
- verified inliers
- transformation
- quality metrics
- status
- artifacts
- failure/rejection information

Do not infer an exact schema from this definition.

---

## Registration Failure

A valid outcome indicating that the available evidence is insufficient, inconsistent, unsupported, or otherwise inadequate for trustworthy registration.

A registration failure is not necessarily a software error.

---

# 4. Mission and Sensor Terms

## Chandrayaan-2

Indian lunar mission context relevant to ChandraMap's source imagery and scientific problem.

Mission-history details belong outside this glossary.

---

## OHRC — Orbiter High Resolution Camera

A Chandrayaan-2 high-resolution panchromatic lunar imaging instrument relevant to detailed terrain correspondence.

Exact product characteristics should come from product metadata or dataset documentation.

---

## TMC-2 — Terrain Mapping Camera-2

A Chandrayaan-2 panchromatic terrain-imaging instrument.

**Canonical wording:** `TMC-2`

Avoid shortening this to `TMC` when the Chandrayaan-2 instrument specifically is meant.

---

## IIRS — Imaging Infrared Spectrometer

A Chandrayaan-2 hyperspectral / imaging-infrared instrument.

IIRS data should not be described merely as a "low-resolution camera."

A registration workflow may require deriving an appropriate 2D representation from its spectral data.

---

## LRO — Lunar Reconnaissance Orbiter

The lunar spacecraft/mission context associated with reference datasets relevant to ChandraMap.

---

## LROC — Lunar Reconnaissance Orbiter Camera

The camera system associated with LRO imagery.

**Distinction:** `LRO` refers to the mission/orbiter context; `LROC` refers to the camera system.

---

## NAC — Narrow Angle Camera

The narrow-angle component of the LROC imaging system.

Generally associated with higher-detail local lunar imagery than WAC.

---

## WAC — Wide Angle Camera

The wide-angle component of the LROC imaging system.

Generally associated with broader spatial coverage and wider contextual imaging than NAC.

---

## LOLA — Lunar Orbiter Laser Altimeter

An LRO instrument associated with lunar elevation/topographic information.

LOLA-derived information may be relevant to terrain-aware processing or validation where used.

---

## Kaguya / SELENE

A lunar mission/data context that may be relevant to optional or future ChandraMap research.

Do not interpret this glossary entry as evidence of current implementation support.

---

# 5. Image, Scale, and Resolution Terms

## Pixel

A discrete sample in a raster image.

Preferred abbreviation:

`px`

---

## Image Dimensions

The width and height of an image measured in pixels.

Example:

`1024 × 1024 px`

Image dimensions do **not** directly specify physical ground coverage or terrain detail.

---

## Spatial Resolution

The ability of an imaging system or product to distinguish spatial detail on the observed surface.

Do not use `resolution` alone when image dimensions, spatial resolution, and spectral resolution could be confused.

---

## GSD — Ground Sampling Distance

The approximate physical ground distance represented by one image pixel under the relevant product geometry.

Typical unit:

`m/px`

Product metadata should be used for exact processing values where available.

---

## Resolution

An ambiguous term that may refer to:

- image dimensions
- spatial resolution
- spectral resolution

Prefer the more specific term whenever ambiguity is possible.

---

## Physical Ground Scale

The physical surface scale represented by image pixels or image structures.

Usually related to GSD and product geometry.

---

## Effective Ground Scale

The approximate physical scale at which two images or representations are being compared.

Useful in scale-aware or pyramid-based matching.

---

## Scale

A context-dependent term that may mean:

- physical ground scale
- geometric scale factor
- image-pyramid level
- display scale

Qualify the term where ambiguity matters.

---

## Geometric Scale Factor

A multiplicative scale component of a transformation.

**Do not confuse with:** GSD.

---

## Upsampling

Increasing the number of image samples through interpolation.

Upsampling changes representation size but does **not** recreate unmeasured physical terrain detail.

---

## Downsampling

Reducing the number of spatial image samples, typically to create a coarser representation.

---

## Image Pyramid

A set of representations of the same image at multiple spatial scales.

---

## Reference Pyramid

A multi-resolution representation of reference imagery used for scale-aware search, retrieval, or matching.

---

## Spatial Detail

Physical terrain information that the source observation can meaningfully resolve.

---

## Spectral Resolution

The ability of an imaging system to distinguish information across wavelength or spectral intervals.

**Do not confuse with:** Spatial resolution.

---

# 6. Hyperspectral Terms

## Hyperspectral Image

Image data containing many spectral measurements for each spatial location.

---

## Hyperspectral Cube

A conceptual data representation such as:

```text
height × width × spectral bands
```

Unlike a grayscale image, each spatial pixel may contain a spectral vector.

---

## Band

One spectral channel or wavelength interval in multi-band or hyperspectral data.

Exact band structure is product-specific.

---

## Spectral Band Selection

Selection of one or more spectral channels for analysis or derivation of another representation.

---

## PCA — Principal Component Analysis

A dimensionality-reduction technique that may be used experimentally to derive lower-dimensional representations of hyperspectral data.

In ChandraMap, PCA may be investigated as one way to derive a registration-friendly representation.

It is not automatically the required IIRS preprocessing method.

---

## Composite

An image representation derived by combining multiple bands, components, or channels.

---

## Registration-Friendly Representation

A 2D representation derived from sensor data for correspondence or registration.

Possible examples include:

- selected band
- PCA component
- spectral composite
- gradient representation
- edge representation

No representation should be assumed universally optimal without evidence.

---

# 7. Correspondence and Matching Terms

## Feature

A distinctive image structure that may be useful for correspondence.

---

## Keypoint

A specific image location selected by a feature-detection method.

---

## Feature Detector

An algorithm that identifies distinctive image locations.

---

## Descriptor

A numerical representation of an image region or feature used for comparison.

---

## Local Descriptor

A descriptor representing a local image neighborhood, usually associated with a keypoint or local region.

---

## Matcher

An algorithm or component that proposes correspondences between image representations.

---

## Candidate Match

A proposed source/reference correspondence produced before geometric verification.

This is the preferred term for unverified matcher output.

**Do not confuse with:** Verified inlier.

---

## Match Confidence

A score produced by some matching methods indicating the algorithm's internal assessment of a candidate correspondence.

Match confidence is **not** proof of geometric or physical correctness.

---

## Verified Inlier

A candidate correspondence that is sufficiently consistent with the selected geometric model and verification threshold.

A verified inlier is model-consistent evidence.

It is not automatically absolute ground truth.

---

## Outlier

A candidate correspondence rejected as inconsistent with the selected geometric model or verification criteria.

---

## Correspondence Set

A collection of paired source/reference locations representing proposed or verified correspondences.

The status of the set should be clear where ambiguity is possible.

---

## Sparse Matching

Correspondence estimation using selected image points or sparse local features.

---

## Detector-Free Matching

A correspondence approach that does not rely on a separate keypoint-detector stage.

LoFTR is an example of a detector-free correspondence method.

---

## Local Matching

Precise correspondence estimation between a source image and a selected candidate reference region.

**Do not confuse with:** Global retrieval.

---

# 8. Feature and Matcher Terms

## SIFT — Scale-Invariant Feature Transform

A classical local feature method used as a useful ChandraMap baseline.

SIFT provides feature detection and local descriptors.

Do not describe SIFT as universally invariant to lunar illumination, modality, or extreme scale differences.

---

## RootSIFT

A transformed form of SIFT descriptors used to improve descriptor-comparison behavior in some applications.

It may serve as a classical baseline variation.

---

## ORB — Oriented FAST and Rotated BRIEF

A classical feature method that may be useful as a speed-oriented baseline or comparison method.

Do not assume it is the preferred accuracy baseline without benchmark evidence.

---

## ALIKED

A learned sparse local-feature approach capable of producing keypoints and local descriptors.

In a conceptual ALIKED + LightGlue pipeline:

```text
ALIKED
→ local feature extraction
```

---

## LightGlue

A learned sparse-feature matcher.

In a conceptual ALIKED + LightGlue pipeline:

```text
LightGlue
→ local feature matching
```

**Important:** ALIKED and LightGlue perform different roles.

---

## LoFTR

A detector-free correspondence method.

Do not describe LoFTR simply as:

- a keypoint detector
- a conventional local feature extractor

Its matching structure differs from sparse detector/descriptor pipelines.

---

## RIFT

A multimodal remote-sensing matching approach relevant as a possible research or benchmark direction.

Do not infer current implementation merely from its inclusion in this glossary.

---

## CFOG

**Channel Features of Oriented Gradients**, a representation/method associated with multimodal remote-sensing registration research.

Treat it as a research or benchmark concept unless current implementation establishes otherwise.

---

## Learned Matcher

A matching approach containing learned/model-based components for correspondence estimation.

---

## Pretrained Model

A learned model whose parameters were trained before use within ChandraMap.

Pretrained terrestrial models are not automatically validated for lunar imagery.

---

## Domain Shift

A difference between the data distribution used to train or develop a model and the data encountered during ChandraMap use.

For example, Earth-trained models may experience domain shift on lunar imagery.

---

## Inference

Execution of a trained model to produce:

- features
- descriptors
- correspondences
- predictions

---

## Training

Optimization of learned model parameters using training data.

Do not use `training` to describe ordinary preprocessing or inference.

---

# 9. Geometry and Registration Terms

## Geometric Verification

The process of testing candidate correspondences for consistency with an estimated geometric transformation.

---

## RANSAC — Random Sample Consensus

A robust model-estimation approach commonly used to identify geometrically consistent matches in the presence of outliers.

RANSAC does not make the original matcher correct; it evaluates geometric consistency.

---

## Inlier Threshold

The maximum allowed geometric deviation for a candidate correspondence to be treated as consistent with the selected model.

Exact thresholds belong in configuration or benchmark documentation.

---

## Consensus Set

The set of candidate matches judged consistent with a RANSAC-estimated model.

---

## Geometric Consistency

Agreement between candidate correspondences and a selected geometric transformation model.

---

## Degenerate Model

A transformation estimate that is invalid, unstable, or insufficiently constrained by the available correspondences.

---

## Transform / Transformation

A mathematical mapping between coordinate systems.

When direction matters, specify it explicitly.

---

## Source → Reference Transform

A transformation mapping coordinates from the source-image coordinate system into the reference-image coordinate system.

---

## Reference → Source Transform

A transformation mapping coordinates from the reference-image coordinate system into the source-image coordinate system.

---

## Translation

A transformation representing positional displacement.

---

## Rotation

Angular transformation between coordinate frames or image representations.

---

## Scale Factor

A multiplicative geometric scaling component of a transformation.

**Do not confuse with:** GSD or physical ground scale.

---

## Similarity Transform

A transformation combining:

- translation
- rotation
- uniform scaling

---

## Affine Transform

A transformation supporting effects such as:

- translation
- rotation
- scale
- shear

while preserving parallel lines.

---

## Homography

A projective transformation often used to align approximately planar image regions.

A homography is one possible transformation model.

It is not synonymous with registration.

A homography also does not imply that lunar terrain is physically planar.

---

## Global Transform

A single transformation applied across the full considered image overlap.

---

## Local Transform

A transformation applied to a local image region.

---

## Piecewise Transform

A spatially varying registration approach using different transformations for different regions.

Use as implementation terminology only where the project actually defines or implements it.

---

## Piecewise Warp

Application of spatially varying transformations across image regions.

---

## Warp

The operation of remapping image samples according to a transformation.

---

## Refit

Re-estimation of a transformation using updated or refined correspondence coordinates.

In a typical refinement flow:

```text
Verified Inliers
      ↓
Tie-Point Refinement
      ↓
Refit Final Transform
```

---

## Geometric Model

A mathematical transformation model relating image coordinates.

Examples:

- affine transformation
- homography

**Do not confuse with:** Learned model.

---

## Learned Model

A machine-learning model whose behavior is determined partly by learned parameters.

When the word `model` could mean either geometric or learned model, qualify it.

---

# 10. Tie-Point and Sub-Pixel Terms

## Tie Point

A corresponding location connecting two images or image-derived products.

Reliable tie points should ideally be:

- accurate
- geometrically consistent
- spatially distributed

---

## Control Point

A point associated with a known or constrained reference/geospatial position.

Projects may distinguish control points and tie points differently.

Do not declare them interchangeable unless ChandraMap explicitly standardizes that convention.

---

## Fit Point

A point used to estimate or finalize a transformation.

---

## Check Point

A point not used to estimate the transformation and reserved for independent evaluation.

**Critical distinction:**

```text
Fit Point
→ used to estimate transform

Check Point
→ used to evaluate transform
```

---

## Control Network

A network of control and/or tie points connecting multiple images or products.

This definition does not imply that ChandraMap currently implements a control-network system.

---

## Sub-Pixel

A spatial quantity finer than one image pixel.

Example:

`0.3 px`

---

## Sub-Pixel Refinement

Estimation of correspondence/tie-point coordinates at fractional-pixel precision, normally after initial matching and geometric verification.

---

## Fractional Pixel Coordinate

An image coordinate containing non-integer values.

Example:

`(412.35, 287.72)`

---

## Source-Image Pixel Error

An error quantity expressed in pixels of the source image.

Preferred unit notation:

`px`

The metric definition should state explicitly that source-image pixels are being used.

---

## Reference-Image Pixel Error

An error quantity expressed in pixels of the reference image.

Do not compare directly with source-image pixel error unless their relationship is understood.

---

## Ground Error

Physical registration error on the lunar surface.

Common unit:

`m`

Ground error requires a valid physical/geospatial conversion.

---

## Sub-Metre Accuracy

Physical error smaller than one metre.

This claim requires valid ground-space evaluation.

**Do not infer sub-metre accuracy from sub-pixel image error alone.**

---

# 11. Geospatial Terms

## Geolocation

Association of image content or an observation with a planetary surface location.

---

## Georeferencing

Establishing the relationship between raster/image coordinates and geographic or projected coordinates.

---

## Map Projection

A mathematical mapping between a planetary surface representation and planar map coordinates.

---

## Map-Projected Image

An image whose raster positions correspond to coordinates in a defined map projection.

---

## Orthorectification

Geometric correction intended to reduce displacement caused by imaging geometry and terrain relief using appropriate geometric/elevation information.

---

## CRS — Coordinate Reference System

A defined coordinate framework for interpreting spatial coordinates.

Do not assume Earth CRS definitions such as WGS84 are appropriate for lunar data.

---

## Latitude

Angular north/south location on a planetary body under a defined coordinate convention.

---

## Longitude

Angular east/west location on a planetary body under a defined coordinate convention.

---

## Longitude Convention

The rule used to represent longitude direction and range.

Possible conventions may include:

- positive-east
- positive-west
- `0°–360°`
- `-180°–+180°`

Do not assume one convention universally.

---

## Footprint

The geospatial region covered by an image or product.

---

## Bounding Box

A rectangular coordinate range approximating an area's extent.

A bounding box is not necessarily identical to an exact image footprint.

---

## Image Coordinates

Coordinates within an image raster.

These are distinct from geospatial coordinates.

---

## Geospatial Coordinates

Coordinates representing location in a geographic, planetary, or projected coordinate system.

---

## `(x, y)`

A common geometric coordinate notation.

Often:

```text
x → horizontal / column direction
y → vertical / row direction
```

Do not assume this is the repository-wide convention unless explicitly standardized.

---

## `(row, column)`

A common array-indexing convention.

Do not treat `(row, column)` as interchangeable with `(x, y)`.

---

## Pixel Origin

The reference location from which pixel coordinates are measured.

Exact project conventions should be documented where needed.

---

## Pixel Center

The geometric center of a raster pixel.

Pixel-center conventions matter in precise geospatial and registration calculations.

---

# 12. Metadata Terms

## Metadata

Information describing an image/product beyond its pixel values.

Potential examples include:

- mission
- sensor
- product identifier
- GSD
- projection
- footprint
- acquisition time
- illumination geometry
- viewing geometry

---

## Product Metadata

Metadata associated with a specific mission/instrument product.

Product metadata should normally override broad instrument-level approximations for actual processing.

---

## Sensor Metadata

Information describing the sensor or instrument associated with an observation.

---

## Acquisition Geometry

Geometric conditions associated with observation capture.

---

## Viewing Geometry

The geometric relationship between the imaging sensor and observed surface.

---

## Solar Geometry

The geometry of solar illumination at acquisition.

---

# 13. Retrieval Terms

## Global Retrieval

Search for likely reference regions corresponding to a source image.

Question answered:

> Where might this image belong?

Global retrieval is not precise local registration.

---

## Local Registration

Detailed alignment between a source image and a selected reference candidate.

Question answered:

> How do these two selected observations align?

---

## Global Descriptor

A compact vector representation describing an entire image or tile for retrieval.

---

## Local Descriptor

A representation of a local image feature used for precise correspondence.

Global and local descriptors have different purposes.

---

## Reference Tile

A spatial subset of a larger reference image or dataset used for search/retrieval.

---

## Tile Pyramid

A multi-scale collection of reference tiles.

---

## Candidate Region

A reference area selected for more detailed matching or registration.

---

## Retrieval Candidate

A region returned by a global or metadata-constrained search for later evaluation.

---

## Top-K Candidates

The `K` highest-ranked retrieval candidates returned by a search operation.

---

## Top-1 Candidate

The highest-ranked retrieval result.

---

## FAISS

A vector similarity-search and indexing library that may be used for retrieval.

Conceptually:

```text
Image
  ↓
Global Descriptor
  ↓
FAISS Search
  ↓
Candidate Vectors / Tiles
```

FAISS does **not** create image descriptors.

---

## Index

A data structure used to support efficient lookup or similarity search.

In retrieval context, an index may associate feature vectors with reference items and metadata.

---

## Metadata-Constrained Search

Search restricted using reliable metadata such as:

- approximate location
- footprint
- projection
- sensor information

---

## Image-Only Retrieval

Candidate search based primarily on image-derived representations rather than trusted geolocation metadata.

---

# 14. Coarse-to-Fine Terms

## Coarse Search

A lower-cost search stage used to identify promising:

- regions
- scales
- candidates

before detailed matching.

---

## Fine Matching

More precise correspondence estimation applied after the search space has been narrowed.

---

## Coarse-to-Fine

A strategy that progressively narrows the problem before applying expensive fine registration.

Conceptually:

```text
Coarse Search
      ↓
Candidate Region
      ↓
Local Matching
      ↓
Fine Refinement
```

---

# 15. Evaluation and Metric Terms

## Residual

The difference between an observed correspondence location and the corresponding predicted/transformed location.

---

## Residual Vector

A vector representing both direction and magnitude of a registration residual.

Residual vectors may reveal spatial error patterns that a scalar metric hides.

---

## Reprojection Error

The distance between a transformed/predicted point and its corresponding observed location.

Always specify units where reported.

---

## RMSE — Root Mean Square Error

A summary metric representing the root mean square of a set of errors.

RMSE should always be interpreted with:

- units
- evaluated population
- coordinate system
- fitting/evaluation role

Exact equations belong in metrics documentation.

---

## Fit-Point RMSE

RMSE computed on points that participated in transformation estimation.

Fit-point RMSE is not independent validation.

---

## Check-Point RMSE

RMSE computed using independent check points not used for fitting the transformation.

Where reliable check points exist, this provides stronger evidence of general registration accuracy.

---

## Candidate Match Count

Number of candidate correspondences produced before geometric verification.

---

## Inlier Count

Number of candidate correspondences accepted as consistent with the selected geometric model.

---

## Inlier Ratio

The proportion of candidate matches classified as verified inliers.

Conceptually:

```text
inlier ratio =
verified inlier count / candidate match count
```

Exact handling of edge cases belongs in metrics documentation.

---

## Spatial Coverage

A measure describing how widely verified correspondences are distributed across the relevant image or overlap area.

---

## Grid Coverage

Spatial coverage measured using occupancy of cells in a predefined grid.

---

## Convex-Hull Coverage

Coverage estimate based on the region enclosed by verified correspondence locations.

Exact normalization belongs in metrics documentation.

---

## Success Rate

The proportion of evaluated cases satisfying defined success criteria.

The success definition must be specified by benchmark documentation.

---

## Failure Rate

The proportion of evaluated cases resulting in a defined failure or rejection outcome.

---

## Runtime

Execution time for a clearly defined processing scope.

Runtime comparisons require comparable:

- hardware
- workload
- configuration
- pipeline scope

---

# 16. Retrieval Metrics

## Recall@1

A retrieval metric indicating whether the correct reference candidate appears at rank 1, aggregated according to the benchmark definition.

---

## Recall@5

A retrieval metric indicating whether the correct reference candidate appears within the first five ranked results.

---

## Recall@K

The general form of retrieval recall measuring whether the correct candidate occurs within the first `K` ranked results.

Exact aggregation rules belong in metrics documentation.

---

# 17. Quality and Confidence Terms

## Quality Metric

A measured quantity describing some aspect of correspondence, retrieval, transformation, or registration quality.

---

## Confidence Score

A defined score intended to estimate result reliability.

A confidence score must have a documented meaning.

Do not invent arbitrary percentages such as `92% confidence`.

---

## Matcher Confidence

A score associated with a specific candidate correspondence or matcher output.

**Do not confuse with:** Overall registration confidence.

---

## Registration Confidence

A project-defined reliability measure for an overall registration result, if such a calibrated measure exists.

It may conceptually consider evidence such as:

- geometric residuals
- inlier statistics
- spatial coverage
- retrieval quality
- model stability

Do not assume ChandraMap currently defines this score.

---

## Quality Gate

A criterion or group of criteria used to decide whether processing should:

- continue
- be refined
- be rejected

Do not infer implementation from the presence of this definition.

---

## Accept

A result status indicating that defined validity/quality conditions are satisfied.

---

## Refine

A result status or decision indicating that additional processing is required before final acceptance.

---

## Reject

A result status indicating that the available evidence is insufficient or inconsistent for trustworthy registration.

A rejection is not necessarily a software error.

---

# 18. Illumination and Structural Terms

## Sun Angle

General wording referring to solar illumination geometry.

When precision is required, use specific illumination metadata rather than the generic phrase alone.

---

## Shadow Geometry

The position, direction, length, and shape of terrain shadows created by illumination geometry.

Shadow geometry can change between observations of the same terrain.

---

## Radiometric Difference

A difference in recorded intensity or sensor response between observations.

---

## Radiometric Normalization

A transformation intended to make image intensity characteristics more comparable.

Potential examples include:

- contrast normalization
- histogram-based normalization

Radiometric normalization does not:

- recreate lost spatial detail
- undo terrain parallax
- fully reverse shadow geometry
- make different sensor modalities identical

---

## Structural Representation

An image representation emphasizing terrain structure rather than absolute intensity.

Possible examples include:

- gradients
- edges
- phase-derived representations

No structural representation should be assumed universally superior.

---

## Gradient

The direction and magnitude of local intensity change in an image.

---

## Edge

A location associated with a strong image-intensity or structural transition.

---

# 19. Terrain Terms

## Crater

A lunar impact structure that may provide recognizable geometric features for correspondence.

Crater-like appearance alone does not uniquely identify a geographic location.

---

## Crater Rim

The boundary or raised terrain structure surrounding a crater.

---

## Ridge

An elongated elevated terrain structure.

---

## Terrain Relief

Variation in lunar surface elevation.

Terrain relief can create geometry that a single planar transform may not completely explain.

---

## DEM — Digital Elevation Model

A digital representation of surface elevation.

Exact product meaning may vary by dataset.

---

## DTM — Digital Terrain Model

A terrain-elevation representation.

Where a project or dataset distinguishes DEM and DTM more precisely, use that specific convention.

---

## Parallax

Apparent positional displacement caused by differences in viewpoint together with scene depth or terrain relief.

---

# 20. Benchmark Terms

## Benchmark

A controlled evaluation used to compare methods or configurations under defined conditions.

---

## Baseline

A reference method or configuration used as a comparison point for later approaches.

---

## Benchmark Configuration

A defined processing configuration used for controlled evaluation.

---

## Benchmark V1

ChandraMap's conceptual classical baseline benchmark configuration.

**Not equivalent to:** Software `v1.0.0`.

---

## Benchmark V2

ChandraMap's conceptual sensor-aware and multi-scale benchmark configuration.

---

## Benchmark V3

ChandraMap's conceptual advanced matching and optional retrieval research configuration.

---

## Benchmark V4

ChandraMap's conceptual research-grade robustness configuration.

Exact V1–V4 definitions belong in benchmark specification documents.

---

## Benchmark Version

A research-configuration identifier such as:

- Benchmark V1
- Benchmark V2
- Benchmark V3
- Benchmark V4

It is distinct from a software release version.

---

## Benchmark Pair

A source/reference image pair included in a benchmark evaluation.

---

## Benchmark Suite

A collection of benchmark cases, datasets, configurations, and evaluation definitions.

---

## Stress Test

An evaluation designed to challenge a specific weakness or physical condition.

Possible categories include:

- Sun-angle stress
- scale stress
- modality stress
- geometry stress
- low-feature stress
- retrieval stress

---

## Ablation

An experiment that removes, replaces, or modifies a component to measure that component's contribution.

---

## Controlled Comparison

A comparison designed to keep relevant variables constant except for the factor being evaluated.

---

# 21. Software Release Terms

## Software Version

A version identifier for a software release.

Example forms may include:

- `0.1.0`
- `0.2.0`
- `1.0.0`

These examples describe versioning format only and do not claim current ChandraMap releases.

---

## Release

A published software version.

---

## Unreleased

Notable changes made since the previous release but not yet included in a published release.

---

## Benchmark Version vs Software Version

These are separate concepts.

```text
Benchmark V1 ≠ software v1.0.0
Benchmark V2 ≠ software v2.0.0
Benchmark V3 ≠ software v3.0.0
Benchmark V4 ≠ software v4.0.0
```

Do not map one numbering system to the other automatically.

---

# 22. Research and Reproducibility Terms

## Reproducibility

The ability to recreate an experiment or result using sufficiently documented:

- code
- data
- configuration
- model information
- environment information

---

## Experiment

A controlled research execution used to evaluate a hypothesis, method, or configuration.

---

## Experiment Configuration

The explicit settings controlling an experiment or benchmark run.

---

## Research Hypothesis

A statement to be tested experimentally.

A hypothesis must not be described as an established result before evidence supports it.

---

## Seed

A random-number initialization value used where relevant to reproducibility.

---

## Dataset Manifest

A structured record describing dataset items included in an experiment, benchmark, or processing run.

---

## Provenance

Information describing where a dataset, model, artifact, or result originated.

---

## Model Checkpoint

Saved model parameters/state associated with a learned model.

---

## Artifact

A generated output produced by processing or experimentation.

Examples may include:

- registered imagery
- visualizations
- metrics files
- benchmark reports
- model outputs

---

## Result

A structured processing or evaluation outcome.

Do not assume `artifact` and `result` are interchangeable if project schemas distinguish them.

---

# 23. Data Terms

## Dataset

An organized collection of data used for:

- processing
- experimentation
- testing
- benchmarking
- evaluation

---

## Product

A mission/instrument-provided or derived data item with associated processing and metadata.

---

## Product Identifier

An identifier associated with a specific mission or instrument data product.

---

## Raw Data

Data close to the original instrument acquisition state.

Exact mission processing-level terminology should come from dataset documentation.

---

## Calibrated Data

Data that has undergone instrument-related calibration or correction.

---

## Derived Product

A product created through processing of other source data.

---

## Ground Truth

Reference information regarded as sufficiently reliable for evaluation under a defined methodology.

Do not call unverified matcher output "ground truth."

---

# 24. Pipeline Terms

## Pipeline

An ordered sequence of processing stages.

---

## Stage

One logical operation within a larger processing pipeline.

---

## Preprocessing

Operations performed to prepare input data before core matching or registration stages.

---

## Sensor Routing

Selection of an appropriate processing path based on sensor or product characteristics.

---

## Normalization

A general transformation intended to place data into a more comparable representation.

Because `normalization` is ambiguous, prefer specific wording such as:

- intensity normalization
- coordinate normalization
- descriptor normalization

---

## Validation

Checking an input, intermediate value, or result against defined requirements.

---

## Post-Processing

Processing performed after a core algorithmic stage.

---

## Fallback

An alternative processing path used when a preferred method or source of information is unavailable or unsuitable.

---

# 25. Software Status Terms

These terms describe different project states and must not be used interchangeably.

## Implemented

Present in the current repository implementation.

Implementation does not automatically imply testing or support.

---

## Tested

Validated by tests or checks that were actually executed.

The existence of test files alone does not justify calling something tested.

---

## Documented

Described in project documentation.

Documented does not imply implemented.

---

## Experimental

Research or prototype functionality that may not be stable, default, or fully supported.

---

## Planned

Intended future work documented in roadmap/design material.

---

## Proposed

An idea under consideration that should not be described as implemented or committed functionality.

---

## Supported

Explicitly accepted and maintained by current project behavior and documentation.

Do not label something supported merely because an underlying library could theoretically process it.

---

## Deprecated

Still available but intended for future removal, replacement, or discontinuation.

---

## Removed

No longer present or supported.

---

# 26. Failure and Error Terms

## Invalid Input

Input that fails structural, semantic, or validation requirements.

---

## Unsupported Input

Input that may be valid in general but is not currently supported by the relevant ChandraMap component.

---

## Matching Failure

A pipeline outcome in which sufficient usable candidate correspondences cannot be produced.

---

## Geometric Verification Failure

A pipeline outcome in which candidate correspondences cannot support a sufficiently reliable geometric model.

---

## Retrieval Failure

Failure to obtain the required usable/correct reference candidate under the defined retrieval process.

---

## Registration Failure

An outcome in which a reliable final registration cannot be established according to defined criteria.

---

## Quality Rejection

Intentional rejection of a technically produced result because defined scientific or quality criteria are not satisfied.

---

## Pipeline Failure

An expected scientific/processing outcome where the available data or evidence is insufficient.

---

## Software Error

An unexpected implementation, runtime, infrastructure, or programming failure.

**Important distinction:**

```text
Pipeline Failure
→ valid scientific/processing outcome

Software Error
→ unexpected software problem
```

---

# 27. Units and Coordinate Language

## Pixel

Preferred abbreviation:

`px`

---

## Metre

Preferred abbreviation:

`m`

---

## Metres per Pixel

Preferred notation:

`m/px`

---

## Degree

Preferred symbol where supported:

`°`

---

## Radian

Preferred abbreviation:

`rad`

---

## Percentage

Preferred symbol:

`%`

Use percentages only for quantities with a defined percentage interpretation.

Do not create arbitrary confidence percentages.

---

## Pixel Error Convention

Whenever pixel error is reported, specify whether it refers to:

- source-image pixels
- reference-image pixels
- another defined raster coordinate space

Do not invent a project-wide convention unless the repository explicitly standardizes one.

---

## Coordinate Ordering

Do not assume one universal project convention for:

```text
(x, y)
```

versus:

```text
(row, column)
```

unless the repository explicitly defines one.

At public interfaces and scientific boundaries, coordinate order should be clear.

---

## Transform Direction

Where ambiguity matters, use explicit wording such as:

- `source → reference`
- `reference → source`

Do not rely on an unlabeled matrix to communicate direction.

---

# 28. Commonly Confused Terms

| Term A             | Term B                  | Difference                                                                                        |
| ------------------ | ----------------------- | ------------------------------------------------------------------------------------------------- |
| Candidate match    | Verified inlier         | Proposed correspondence before verification vs model-consistent correspondence after verification |
| Correspondence     | Registration            | Point/location relationship vs overall geometric alignment process                                |
| Source image       | Reference image         | Image being transformed/localized vs target comparison frame                                      |
| Global retrieval   | Local matching          | Region search vs precise correspondence                                                           |
| Global descriptor  | Local descriptor        | Whole-image/tile representation vs local feature representation                                   |
| Spatial resolution | Image dimensions        | Physical detail capability vs raster width/height                                                 |
| Spatial resolution | Spectral resolution     | Terrain detail vs wavelength discrimination                                                       |
| GSD                | Geometric scale factor  | Physical ground sampling vs transform scaling component                                           |
| Upsampling         | Detail recovery         | Interpolation/resampling vs unavailable physical information                                      |
| Fit point          | Check point             | Used for transformation estimation vs independent evaluation                                      |
| Pixel error        | Ground error            | Image-space quantity vs physical-space quantity                                                   |
| Sub-pixel accuracy | Sub-metre accuracy      | Fraction of image pixel vs physical metre-scale error                                             |
| Matcher confidence | Registration confidence | Local matching score vs whole-result reliability measure                                          |
| Inlier count       | Spatial coverage        | Number of accepted matches vs their spatial distribution                                          |
| Homography         | Registration            | One possible transformation model vs complete alignment process                                   |
| RANSAC inlier      | Ground truth            | Model-consistent candidate vs independently validated truth                                       |
| Global transform   | Local transform         | One model over full overlap vs region-specific model                                              |
| LRO                | LROC                    | Lunar mission/orbiter context vs camera system                                                    |
| NAC                | WAC                     | Narrow-angle detailed imagery vs wide-angle broader coverage                                      |
| Benchmark V1       | Software `v1.0.0`       | Research configuration vs software release version                                                |
| Implemented        | Documented              | Exists in code vs described in documentation                                                      |
| Experimental       | Supported               | Prototype/research status vs maintained functionality                                             |
| Planned            | Proposed                | Intended documented future work vs idea under consideration                                       |
| Pipeline failure   | Software error          | Valid processing outcome vs unexpected implementation failure                                     |
| Artifact           | Result                  | Generated output vs structured processing/evaluation outcome                                      |
| Geometric model    | Learned model           | Coordinate transformation model vs machine-learning model                                         |

---

# 29. Preferred Wording

Prefer precise project terminology.

| Prefer                                              | Avoid When Ambiguous or Misleading                              |
| --------------------------------------------------- | --------------------------------------------------------------- |
| **candidate matches**                               | high-confidence matches before geometric verification           |
| **verified inliers**                                | verified matches based only on matcher confidence               |
| **TMC-2**                                           | TMC when specifically referring to the Chandrayaan-2 instrument |
| **IIRS hyperspectral/imaging-infrared data**        | IIRS low-resolution camera                                      |
| **spatial resolution**                              | resolution when dimensions/spectral resolution may be confused  |
| **GSD** / **ground scale**                          | zoom level when referring to physical resolution                |
| **multi-scale search** / **scale-aware comparison** | resolution fixing                                               |
| **registered preview**                              | proof of correct registration                                   |
| **source-image pixel error**                        | pixel error when pixel frame is ambiguous                       |
| **ground error in metres**                          | sub-metre accuracy without valid ground conversion              |
| **retrieval candidate**                             | match when referring only to region-level search                |
| **local correspondence**                            | retrieval when referring to point-level matching                |
| **quality metric**                                  | AI accuracy without a definition                                |
| **geometric model**                                 | model where ML/geometric meaning is ambiguous                   |
| **learned model**                                   | model where geometric/ML meaning is ambiguous                   |

---

# 30. Discouraged or Ambiguous Wording

Avoid terminology that implies more evidence than actually exists.

## "High-confidence match"

Discouraged as a substitute for `verified inlier`.

A matcher can be highly confident and still geometrically wrong.

---

## "Perfect match"

Avoid unless explicitly defined under a measurable criterion.

---

## "AI accuracy"

Too vague without:

- metric
- dataset
- evaluation protocol
- units where relevant

---

## "92% confidence"

Do not use arbitrary confidence percentages without a documented and calibrated definition.

---

## "High resolution"

Use only when the context is clear.

Where scientific scale matters, prefer an explicit GSD or relative description.

---

## "Same image"

Avoid when two observations merely depict the same lunar region.

Prefer:

- same terrain
- same lunar region
- corresponding observation

---

## "Ground truth"

Do not use for:

- matcher output
- RANSAC inliers
- visually selected but unverified points

unless the evaluation method explicitly defines them as ground truth.

---

## "Sub-metre accuracy"

Do not use when only sub-pixel image-space accuracy has been measured.

---

## "Resolution enhanced"

Avoid when an image has only been interpolated or upsampled.

Prefer:

- upsampled
- resampled

---

## "Feature extractor" for LoFTR

Avoid when it would incorrectly describe LoFTR's detector-free correspondence role.

---

## "FAISS matching"

Avoid when referring to local image correspondence.

Prefer:

- FAISS retrieval
- vector search
- candidate retrieval

---

## "Accuracy"

Qualify when possible.

Examples:

- check-point RMSE
- source-image pixel error
- retrieval Recall@K
- registration success rate

---

## "Model"

Qualify when ambiguity exists.

Prefer:

- geometric model
- learned model
- retrieval model

---

# 31. Canonical Spelling and Capitalization

Use these forms consistently:

- ChandraMap
- Chandrayaan-2
- OHRC
- TMC-2
- IIRS
- LRO
- LROC
- NAC
- WAC
- LOLA
- GSD
- RMSE
- RANSAC
- SIFT
- RootSIFT
- ORB
- ALIKED
- LightGlue
- LoFTR
- RIFT
- CFOG
- PCA
- DEM
- DTM
- FAISS

Do not introduce inconsistent capitalization or spelling without a specific reason.

---

# 32. Abbreviations

| Abbreviation | Meaning                                |
| ------------ | -------------------------------------- |
| OHRC         | Orbiter High Resolution Camera         |
| TMC-2        | Terrain Mapping Camera-2               |
| IIRS         | Imaging Infrared Spectrometer          |
| LRO          | Lunar Reconnaissance Orbiter           |
| LROC         | Lunar Reconnaissance Orbiter Camera    |
| NAC          | Narrow Angle Camera                    |
| WAC          | Wide Angle Camera                      |
| LOLA         | Lunar Orbiter Laser Altimeter          |
| GSD          | Ground Sampling Distance               |
| RMSE         | Root Mean Square Error                 |
| RANSAC       | Random Sample Consensus                |
| SIFT         | Scale-Invariant Feature Transform      |
| ORB          | Oriented FAST and Rotated BRIEF        |
| PCA          | Principal Component Analysis           |
| DEM          | Digital Elevation Model                |
| DTM          | Digital Terrain Model                  |
| CFOG         | Channel Features of Oriented Gradients |

`FAISS` should be treated here as the name of the vector similarity-search/indexing library without relying on an acronym expansion.

---

# 33. API and Schema Terminology

Where terminology is reflected in public interfaces, prefer names that preserve scientific meaning.

Conceptually:

- pre-verification correspondences should be described as **candidate matches**
- geometrically accepted correspondences should be described as **verified inliers**
- retrieval results should be identified as **retrieval candidates**
- ground error and pixel error should not share an ambiguous unlabeled field

However, this glossary does **not** define current repository API field names.

Do not rename existing fields solely because an example term appears here.

Before changing terminology in:

- API schemas
- configuration
- serialized results
- frontend labels
- benchmark tables

inspect all consumers and compatibility implications.

---

# 34. Domain Terms vs Project Conventions

Some terms are general scientific/domain terms.

Examples:

- GSD
- RANSAC
- affine transform
- homography
- RMSE
- hyperspectral image

Other terms are ChandraMap project conventions.

Examples include:

- Benchmark V1
- Benchmark V2
- Benchmark V3
- Benchmark V4
- consistent use of `candidate match`
- consistent use of `verified inlier`

Do not present project conventions as universal scientific standards.

---

# 35. Current vs Future Terminology

A definition does not prove implementation.

For example, defining:

> Quality Gate

does not mean:

> ChandraMap currently has an implemented quality-gate subsystem.

Use status language separately:

- Implemented
- Tested
- Experimental
- Planned
- Proposed

Terminology documentation describes meaning, not implementation state.

---

# 36. Related Context Documents

This glossary should remain concise and defer deeper material appropriately.

`PROJECT_CONTEXT.md`
→ project identity, scope, and direction

`DOMAIN_CONTEXT.md`
→ scientific meaning and physical constraints

`DATASETS.md`
→ exact mission products, sources, formats, metadata, and data handling

Architecture documentation
→ how domain concepts are organized in the software

Pipeline documentation
→ processing order and stage responsibilities

Benchmark documentation
→ exact V1–V4 definitions and controlled comparisons

Metrics documentation
→ exact equations, thresholds, aggregation rules, and failure handling

Engineering rules
→ how code and repository changes should be performed

Do not duplicate those responsibilities here.

---

# 37. Maintaining This Glossary

Update `TERMINOLOGY.md` when:

- a new public concept is introduced
- terminology changes across multiple modules or documents
- benchmark naming changes
- ambiguity is repeatedly encountered
- sensor/product naming conventions change
- a metric becomes part of public project language
- conflicting terms need standardization

Do not add entries for every local variable or implementation detail.

---

## Adding a New Term

A term generally deserves an entry when it:

- appears across multiple modules or documents
- has project-specific meaning
- could be misunderstood
- affects scientific interpretation
- affects schemas or APIs
- affects benchmark interpretation

A term usually does not require an entry when it is:

- ordinary programming vocabulary
- a one-off local concept
- self-explanatory and unambiguous

---

## Terminology Change Safety

Changing a canonical term may affect:

- documentation
- APIs
- schemas
- configuration
- benchmark output
- frontend labels
- tests
- serialized results

Do not silently rename widely used concepts without inspecting consumers.

---

# 38. Key Terminology Rules for Agents

1. Use **ChandraMap** consistently.

2. Use **TMC-2** for the Chandrayaan-2 Terrain Mapping Camera-2.

3. Describe IIRS as **hyperspectral / imaging-infrared data**, not merely a low-resolution camera.

4. Distinguish **LRO** from **LROC** where relevant.

5. Distinguish **NAC** from **WAC**.

6. Distinguish **source image** from **reference image**.

7. Use **candidate match** before geometric verification.

8. Use **verified inlier** after geometric verification.

9. Do not treat matcher confidence as geometric correctness.

10. Distinguish feature **detector**, **descriptor**, and **matcher**.

11. Distinguish **ALIKED** from **LightGlue**.

12. Treat **LoFTR** as detector-free correspondence, not simply as a feature extractor.

13. Distinguish **global retrieval** from **local matching**.

14. Distinguish **global descriptors** from **local descriptors**.

15. FAISS searches/indexes vectors; it does not extract image features.

16. Distinguish **image dimensions** from **spatial resolution**.

17. Distinguish **spatial resolution** from **spectral resolution**.

18. Upsampling is not detail recovery.

19. Distinguish **GSD** from geometric scale factor.

20. Distinguish **image coordinates** from **geospatial coordinates**.

21. State transform direction where it matters.

22. Distinguish `(x, y)` from `(row, column)`.

23. Distinguish **fit points** from independent **check points**.

24. Distinguish **source-image pixel error** from **ground error**.

25. Do not equate sub-pixel accuracy with sub-metre accuracy.

26. Distinguish **inlier count** from **spatial coverage**.

27. Do not treat RANSAC inliers automatically as ground truth.

28. Distinguish a **homography** from the broader registration process.

29. Distinguish a **geometric model** from a **learned model**.

30. Distinguish benchmark versions from software release versions.

31. Distinguish **Implemented**, **Tested**, **Documented**, **Experimental**, **Planned**, and **Proposed**.

32. Distinguish **Supported** from merely technically possible.

33. Distinguish **pipeline failure** from **software error**.

34. Do not use undefined confidence percentages.

35. Do not call unverified matcher output ground truth.

36. Do not call upsampled imagery physically higher-resolution without qualification.

37. Specify units whenever registration error is reported.

38. Specify whether pixel error is measured in source-image pixels or reference-image pixels when relevant.

39. Prefer precise terms over ambiguous words such as `accuracy`, `resolution`, `scale`, `matching`, and `model`.

40. When implementation status is unknown, define the term without implying that ChandraMap currently implements it.

<!-- Source specification: :contentReference[oaicite:0]{index=0} -->
