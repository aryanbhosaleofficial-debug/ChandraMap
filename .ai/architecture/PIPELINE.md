# ChandraMap Processing Pipeline

This document defines the authoritative high-level scientific processing flow for **ChandraMap**.

It describes what happens to lunar input data, in what order, which branches may be taken, where scientific validation occurs, how failures propagate, and how Benchmark V1–V4 differ conceptually.

The central processing principle is:

```text
Correct Input Understanding
        ↓
Physically Meaningful Preparation
        ↓
Candidate Search
        ↓
Candidate Correspondence
        ↓
Geometric Verification
        ↓
Reliable Tie-Point Refinement
        ↓
Final Transformation
        ↓
Registration
        ↓
Independent Evaluation
        ↓
Accept / Refine / Reject
```

A visually convincing registered image must never bypass geometric verification or quantitative evaluation.

Unless explicitly identified as current implementation elsewhere, the complete pipeline in this document should be interpreted as the **target scientific processing architecture**. V1, V2, V3, and V4 activate different subsets of this architecture.

---

## 1. Purpose

`PIPELINE.md` answers:

> **What happens to an input lunar image or product, in what order, and why?**

It defines:

- major processing stages
- stage ordering
- stage inputs and outputs at a conceptual level
- conditional search paths
- sensor-aware branches
- metadata-first localization
- optional global retrieval
- local correspondence
- geometric verification
- transformation estimation
- tie-point refinement
- final transform refitting
- registration
- evaluation
- failure/rejection paths
- benchmark-version differences
- reproducibility expectations

It does not define:

- exact classes or functions
- exact repository module ownership
- serialized schemas
- API routes
- metric equations
- benchmark results
- implementation status not verified elsewhere

Those concerns belong in more specialized documentation.

---

## 2. Pipeline Goals

The ChandraMap processing pipeline should remain:

- modular
- measurable
- reproducible
- benchmarkable
- configuration-driven
- sensor-aware where required
- scale-aware where required
- geometry-aware
- failure-aware
- independent of frontend presentation
- independent of HTTP/backend transport
- compatible with classical and learned matching methods
- suitable for controlled V1–V4 comparison

The pipeline should make it possible to determine not only:

> Did registration succeed?

but also:

> Why was the result accepted, rejected, or considered uncertain?

---

## 3. Pipeline Status Language

Use the following status language consistently.

| Status           | Meaning                                                    |
| ---------------- | ---------------------------------------------------------- |
| **Current**      | Verified as present in current implementation              |
| **Target**       | Intended architecture toward which the project is evolving |
| **Experimental** | Prototype or research implementation                       |
| **Planned**      | Intended future capability not yet implemented             |
| **Optional**     | Stage used only when the input/task requires it            |

This document describes the target pipeline broadly.

Do not infer that V2, V3, or V4 stages are already implemented merely because they appear here.

---

## 4. Core Processing Principles

The following ordering rules are strong pipeline invariants unless a documented research experiment intentionally changes them.

1. Validate inputs before expensive scientific processing.
2. Preserve metadata and provenance before deriving representations.
3. Identify instrument/product type before sensor-specific processing.
4. Use product-specific metadata instead of broad sensor assumptions where available.
5. Use physically meaningful scale information where possible.
6. Use reliable metadata to restrict search before considering expensive global visual retrieval.
7. Global retrieval precedes precise local registration.
8. Local matchers produce **candidate matches**.
9. Candidate matches are not yet trusted correspondences.
10. Geometric verification occurs before correspondences are treated as verified inliers.
11. Spatial distribution and residuals are evaluated before expensive refinement where practical.
12. Sub-pixel refinement should normally operate on verified inliers or tie points.
13. If tie-point coordinates change, refit the transformation.
14. Warping the image does not validate the transformation.
15. Independent evaluation should use the final transformation.
16. Final pipeline state may legitimately be **Reject**.

---

# 5. End-to-End Target Pipeline

```text
Source Lunar Product
        +
Reference Dataset
        ↓
Input Validation
        ↓
Metadata Extraction
        ↓
Instrument / Sensor Identification
        ↓
Product / Projection Understanding
        ↓
Sensor-Aware Preprocessing
        ↓
Scale / GSD Handling
        ↓
Candidate Search Strategy
        │
        ├── Reliable Metadata Available
        │        ↓
        │   Metadata-Constrained Search
        │
        └── Location Unknown / Metadata Insufficient
                 ↓
            Global Retrieval
                 ↓
            Top-K Candidates
        ↓
Candidate Reference Region(s)
        ↓
Local Correspondence Matching
        ↓
Candidate Matches
        ↓
Candidate Filtering
        ↓
Geometric Verification
        ↓
Initial Transform
        ↓
Verified Inliers
        ↓
Spatial Coverage + Residual Analysis
        ↓
Early Reject / Continue Decision
        ↓
Sub-Pixel Tie-Point Refinement
        ↓
Final Transform Refit
        ↓
Image Registration / Warp
        ↓
Independent Evaluation
        ↓
Quality Decision
        │
        ├── Accept
        ├── Refine
        └── Reject
        ↓
Structured Registration Result
        ↓
Optional Preview / Mosaic / Map UI
```

This diagram describes the long-term target flow.

Canonical V1 uses only a restricted subset.

---

# 6. Pipeline Modes

ChandraMap may execute the scientific pipeline in several modes.

## 6.1 Known-Overlap Registration

Used when the source/reference region is already known.

```text
Known Source
    +
Known Reference Region
        ↓
Local Processing
        ↓
Matching
        ↓
Geometry
        ↓
Registration
        ↓
Evaluation
```

This is the primary mode for Benchmark V1.

---

## 6.2 Metadata-Constrained Localization

Used when useful geospatial metadata already narrows the possible reference region.

```text
Source Product
      ↓
Footprint / Coordinates / Metadata
      ↓
Reference Search
      ↓
Candidate Region
      ↓
Local Registration
```

This avoids unnecessary whole-Moon visual retrieval.

---

## 6.3 Image-Only Global Retrieval

Used when reliable location metadata is unavailable or when the benchmark explicitly evaluates retrieval.

```text
Source Image
      ↓
Global Descriptor
      ↓
Vector Search
      ↓
Top-K Candidate Regions
      ↓
Local Registration per Candidate
```

This is primarily a V3+ research capability.

---

## 6.4 Benchmark Execution

Used to run controlled comparisons.

```text
Benchmark Manifest
        ↓
Select Pair(s)
        ↓
Load Benchmark Configuration
        ↓
Execute Pipeline Version
        ↓
Collect Metrics
        ↓
Collect Failures
        ↓
Record Run Metadata
        ↓
Compare V1 / V2 / V3 / V4
```

Benchmark execution must not manually alter scientific results.

---

# 7. Stage 1 — Input Validation

## Purpose

Reject structurally invalid or unsupported input before expensive processing begins.

Potential checks include:

- file exists
- file is readable
- raster/cube dimensions are valid
- data is non-empty
- finite values exist where required
- metadata is parseable
- required companion files are available
- product structure is consistent with declared input type
- sensor/product identity is recognized where required
- spectral structure is valid where relevant
- masks/no-data information is interpretable

---

## Output

Either:

```text
Validated Input
```

or:

```text
Validation Failure
```

---

## Validation Failure Examples

Potential failure conditions include:

- unreadable scientific product
- corrupt raster
- invalid dimensions
- malformed hyperspectral cube
- missing required metadata
- unsupported input representation
- unsupported product type
- missing companion metadata

Essential validation failure should stop the scientific pipeline.

Do not continue into feature matching merely to produce some output.

---

# 8. Stage 2 — Metadata Extraction

Extract available scientific metadata before transforming the imagery.

Potential metadata includes:

- mission
- instrument
- product identifier
- raster dimensions
- band information
- GSD / pixel scale
- projection
- CRS / planetary reference
- footprint
- acquisition time
- illumination geometry
- viewing geometry
- processing state

Do not fabricate missing metadata.

---

## Metadata Preservation

Where practical:

```text
Original Provider Metadata
        ↓
Normalization / Parsing
        ↓
Internal Scientific Metadata
```

Normalized metadata should preserve original scientific meaning.

If a correction or conversion is required, it should remain traceable rather than silently replacing the original value.

---

# 9. Stage 3 — Sensor / Product Identification

The pipeline should identify the relevant sensor/product before sensor-specific processing.

Primary contexts include:

- Chandrayaan-2 OHRC
- Chandrayaan-2 TMC-2
- Chandrayaan-2 IIRS
- LRO NAC
- LRO WAC

Prefer reliable product metadata over visual inference.

Do not identify a sensor merely because an image "looks like" a known instrument when metadata already exists.

---

## Unknown Sensor Path

If sensor identity cannot be established, possible architecture choices may include:

- require explicit configuration
- mark sensor as unknown
- use a generic 2D route
- reject the input

The actual behavior must come from repository implementation/configuration.

Do not invent a default here.

---

# 10. Stage 4 — Product and Geospatial Understanding

Determine relevant product geometry before image correspondence.

Potential questions include:

- Is the product raw or derived?
- Is it calibrated?
- Is it map-projected?
- Is it orthorectified?
- What projection is defined?
- What is its footprint?
- What coordinate convention is used?
- What is its GSD?
- Does reliable overlap metadata already exist?

This stage prevents known geospatial information from being unnecessarily rediscovered through generic computer vision.

---

## Map-Projected Path

When reliable map-projected products are available, use that information to support:

- overlap identification
- candidate search
- scale comparison
- coordinate reasoning

Do not discard valid geospatial information simply to make the problem "image only."

---

# 11. Stage 5 — Sensor-Aware Preprocessing

## Purpose

Produce a registration-ready representation while preserving scientific meaning and provenance.

Potential operations include:

- finite-value handling
- no-data masking
- grayscale conversion where meaningful
- intensity normalization
- contrast normalization
- mild filtering
- gradient representation
- edge representation
- other sensor-appropriate derived representations

Do not apply every available preprocessing operation automatically.

Preprocessing should remain:

- explicit
- reproducible
- scientifically justified
- benchmarkable

---

## 11.1 OHRC Path

OHRC preprocessing may need to consider:

- panchromatic intensity behavior
- high spatial detail
- shadows
- dynamic range
- scale difference relative to reference imagery

No fixed OHRC recipe should be assumed unless current configuration defines one.

---

## 11.2 TMC-2 Path

TMC-2 preprocessing may need to consider:

- panchromatic normalization
- terrain-scale structure
- scale differences relative to NAC/OHRC
- illumination variation

Use the canonical name:

> **TMC-2**

---

## 11.3 IIRS Path

IIRS is hyperspectral / imaging-infrared data.

A conceptual preprocessing route is:

```text
IIRS Hyperspectral Product
        ↓
Spectral / Representation Selection
        ↓
Registration-Friendly 2D Representation
```

Possible research representations may include:

- selected band
- selected spectral region
- PCA component
- multi-band composite
- gradient representation
- edge representation
- structural representation

Do not declare any one representation universally best without evidence.

Full native IIRS handling is primarily a later sensor-aware research concern rather than canonical V1 behavior.

---

## 11.4 Preprocessing Output

Conceptually:

```text
Registration-Ready Representation
        +
Associated Metadata
        +
Valid Region / Mask
        +
Transformation Provenance
```

Exact objects belong in `DATA_FLOW.md` or repository contracts.

---

# 12. Stage 6 — Scale / GSD Handling

## Purpose

Bring source and reference information into a physically meaningful comparison regime.

The pipeline must preserve the rule:

> **Resizing is not detail recovery.**

---

## Physical Scale Flow

```text
Source GSD
    +
Reference GSD
        ↓
Determine Scale Relationship
        ↓
Select or Derive Comparable Representation
        ↓
Local Correspondence at Meaningful Effective Scale
```

Where appropriate:

```text
High-Resolution Reference
        ↓
Downsample / Pyramid
        ↓
Comparable Effective Scale
```

is scientifically preferable to:

```text
Coarse Source
        ↓
Extreme Upsampling
        ↓
Pretend Fine Detail Exists
```

---

## V1 Scale Path

Canonical V1 remains intentionally limited.

Typical V1 assumptions:

- known overlap
- registration-ready 2D imagery
- minimal representation compatibility

V1 does not require automatic multi-scale reference-pyramid search unless `V1_SCOPE.md` explicitly changes that contract.

---

## V2 Scale Path

V2 may introduce:

- reference pyramids
- effective-GSD reasoning
- multi-scale correspondence
- coarse-to-fine processing
- automatic or configured scale-level selection

---

# 13. Stage 7 — Candidate Search Strategy

Before local correspondence, determine whether candidate-location search is required.

```text
Reliable Location / Overlap Metadata?
            │
            ├── YES
            │     ↓
            │ Metadata-Constrained Search
            │
            └── NO
                  ↓
             Global Retrieval
```

Do not run expensive global retrieval merely because it is available.

---

# 14. Stage 8A — Metadata-Constrained Search

When trustworthy geospatial information exists:

```text
Source Footprint / Approximate Position
        ↓
Reference Dataset Query
        ↓
Candidate Overlapping Region(s)
```

Possible search inputs include:

- footprint
- latitude/longitude
- map projection
- product geometry
- known benchmark pairing

Using reliable metadata is valid scientific/software engineering.

It is not "cheating."

---

# 15. Stage 8B — Global Visual Retrieval

Use global retrieval when:

- source location is unknown
- metadata is unavailable
- metadata is unreliable
- the benchmark explicitly tests image-based retrieval

Online conceptual flow:

```text
Source Representation
        ↓
Global Descriptor
        ↓
Vector Search
        ↓
Top-K Reference Candidates
```

A retrieval score does not establish precise geometric correspondence.

Every retained candidate must still pass local matching and geometric verification.

---

# 16. Offline Reference Preparation

Global retrieval requires a separate offline preparation workflow.

```text
Reference Products
        ↓
Validate Metadata
        ↓
Reference Tiling
        ↓
Optional Multi-Scale Pyramid
        ↓
Global Descriptor Extraction
        ↓
Vector Index Construction
        +
Tile Metadata Mapping
        ↓
Record Index Provenance
```

This should not be rebuilt from scratch for every online registration query unless explicitly required.

---

## 16.1 Reference Tiling

Reference imagery may be divided into candidate regions or tiles for retrieval.

Tile provenance should remain connected to:

- parent reference product
- geographic location
- pyramid level where relevant
- generation configuration

Exact tile sizes belong in retrieval configuration.

---

## 16.2 Multi-Scale Reference Levels

When retrieval must tolerate large scale differences, the offline database may contain multiple reference scales.

This remains derived data.

It does not alter the physical information content of the original source product.

---

## 16.3 Global Descriptor Generation

Each reference tile is converted into a global retrieval representation.

Reference and query descriptors must belong to compatible descriptor spaces.

Do not compare descriptors produced by incompatible methods or preprocessing without a scientifically defined reason.

---

## 16.4 FAISS Placement

If FAISS is used:

```text
Reference Global Descriptors
        ↓
FAISS Index

Query Global Descriptor
        ↓
FAISS Search
        ↓
Top-K Candidate IDs
```

FAISS does **not**:

- generate descriptors
- detect image keypoints
- perform local feature matching
- run RANSAC
- estimate homographies
- register images

It is a vector search/indexing component.

---

# 17. Stage 9 — Candidate Region Preparation

Before local matching, selected reference regions may need:

- crop extraction
- representation preparation
- scale selection
- masking
- metadata propagation

A candidate region must remain traceable to its parent reference product.

Do not detach a tile from its scientific/geospatial identity.

---

# 18. Stage 10 — Local Matching

Local matching estimates point-level or correspondence-level candidates between:

```text
Source Representation
```

and:

```text
Candidate Reference Region
```

Potential matcher families include classical, learned, and remote-sensing research methods.

---

## 18.1 SIFT / RootSIFT Path

Canonical conceptual flow:

```text
Source
  ↓
SIFT Keypoints + Descriptors

Reference
  ↓
SIFT Keypoints + Descriptors

Descriptors
  ↓
Nearest-Neighbor / KNN Matching
  ↓
Filtering
  ↓
Candidate Matches
```

Exact matching thresholds belong in benchmark/configuration documentation.

SIFT is the canonical V1 classical baseline unless another authoritative V1 specification changes it.

---

## 18.2 ORB Path

ORB may serve as a separate classical comparison where appropriate.

Do not silently combine ORB and SIFT into one undefined benchmark method.

---

## 18.3 ALIKED + LightGlue Path

Conceptually:

```text
Source + Reference
        ↓
ALIKED Local Features
        ↓
LightGlue Matching
        ↓
Candidate Matches
```

ALIKED and LightGlue have different responsibilities.

ALIKED provides sparse local features.

LightGlue matches those features.

---

## 18.4 LoFTR Path

Conceptually:

```text
Source + Reference
        ↓
LoFTR
        ↓
Candidate Correspondences
```

LoFTR is detector-free.

Do not model it as an ordinary:

```text
Detector
→ Descriptor
→ Matcher
```

pipeline.

---

## 18.5 Remote-Sensing Research Methods

Potential research approaches may include:

- RIFT-inspired methods
- CFOG-inspired methods
- other validated multimodal techniques

Their inclusion in this document does not imply current implementation.

---

# 19. Matcher Output Contract

Regardless of matcher family, downstream geometry should conceptually receive:

> **Candidate Correspondence Set**

At this point use:

> **candidate matches**

not:

> verified matches

Matcher confidence does not replace geometric verification.

---

# 20. Stage 11 — Candidate Match Filtering

Potential filtering may include:

- descriptor ratio criteria
- mutual matching
- cross-checking
- confidence thresholding
- bounds validation
- duplicate suppression

Filtering depends on the matcher.

Do not introduce hidden filtering designed to remove difficult cases from benchmark results.

All benchmark-affecting filters should be explicit and reproducible.

---

# 21. Stage 12 — Geometric Verification

Geometric verification is one of the pipeline's critical scientific boundaries.

```text
Candidate Matches
        ↓
RANSAC / Robust Estimation
        ↓
Initial Geometric Model
        ↓
Verified Inliers
        +
Rejected Outliers
```

Do not allow the normal flow:

```text
Candidate Matches
        ↓
Final Registration
```

without geometry validation unless a specifically documented method provides an equivalent geometric consistency mechanism.

---

## 21.1 RANSAC Responsibility

RANSAC may:

- estimate a plausible model
- tolerate outlier candidates
- identify a model-consistent subset
- produce an initial transformation

RANSAC does not:

- establish absolute physical truth
- solve incorrect sensor preprocessing
- guarantee adequate spatial distribution
- guarantee globally valid lunar geometry
- guarantee independent registration accuracy

---

# 22. Stage 13 — Initial Transform Estimation

Potential global transformation models include:

- affine
- homography

Selection should come from:

- benchmark scope
- configuration
- geometry assumptions

Do not inspect ground-truth evaluation results and then select whichever transform gives the preferred score unless that model-selection process is explicitly part of the experiment.

---

## Transform Direction

Transform direction must remain explicit.

Examples:

```text
source → reference
```

or:

```text
reference → source
```

Direction must remain consistent through:

- transform estimation
- warping
- metric computation
- serialization
- visualization

---

# 23. Stage 14 — Verified Inlier Analysis

Do not accept a transformation solely because RANSAC returned an inlier set.

Analyze evidence such as:

- inlier count
- inlier ratio
- spatial distribution
- residual magnitude
- residual direction
- transformation degeneracy
- concentration near image boundaries
- concentration around a single terrain structure

More inliers do not automatically mean better registration.

---

# 24. Stage 15 — Spatial Coverage

Spatial coverage measures whether verified inliers meaningfully span the overlap.

Potential approaches include:

- grid occupancy
- grid coverage ratio
- convex-hull coverage

Conceptually:

```text
Verified Inliers
        ↓
Spatial Distribution Analysis
        ↓
Coverage Metric
```

A large number of correspondences clustered around one crater may provide weaker global geometric support than fewer well-distributed correspondences.

Exact formulas belong in metric documentation.

---

# 25. Stage 16 — Residual Analysis

Analyze:

- residual magnitude
- residual direction
- spatial residual patterns

Systematic trends may indicate:

- inappropriate geometric model
- terrain relief
- projection mismatch
- wrong candidate region
- scale mismatch
- remaining misregistration

Residual analysis should help determine whether refinement is meaningful or whether the result should be rejected.

---

# 26. Stage 17 — Early Quality Decision

Before expensive refinement, clearly invalid geometry may be rejected.

Potential evidence includes:

- insufficient inliers
- degenerate transform
- poor spatial distribution
- non-finite transformation
- extreme residuals
- obviously inconsistent geometry

Conceptually:

```text
Initial Geometric Evidence
        ↓
Sufficient?
        │
        ├── YES → Continue
        │
        └── NO  → Reject
```

Do not refine obviously invalid correspondence sets simply to force success.

---

# 27. Stage 18 — Sub-Pixel Tie-Point Refinement

Where the active benchmark version permits it:

```text
Verified Inliers
        ↓
Local Patch / Signal Extraction
        ↓
Sub-Pixel Alignment
        ↓
Refined Source / Reference Coordinates
```

Potential methods may include:

- local correlation
- phase correlation
- patch-based alignment
- specialized planetary registration methods

Exact method selection belongs in benchmark/research documentation.

---

## Critical Refinement Order

Preferred order:

```text
Candidate Matches
        ↓
RANSAC
        ↓
Verified Inliers
        ↓
Sub-Pixel Refinement
```

Not:

```text
Candidate Matches
        ↓
Refine Every Candidate
        ↓
RANSAC
```

unless a specific documented experiment justifies that alternate method.

---

## V1 Refinement Path

Canonical V1 normally excludes advanced sub-pixel refinement according to `V1_SCOPE.md`.

Therefore V1 typically follows:

```text
RANSAC Inliers
        ↓
Canonical Global Transform
        ↓
Registration
        ↓
Baseline Evaluation
```

Do not silently introduce later-version refinement into V1.

---

# 28. Stage 19 — Final Transform Refit

If tie-point coordinates are refined, the transformation must normally be re-estimated.

```text
Refined Tie Points
        ↓
Refit Transformation
        ↓
Final Transform
```

Do not:

```text
Refine Coordinates
        ↓
Continue Using Old Transform
```

unless a specifically documented algorithm requires that behavior.

The final transformation used for registration and evaluation should correspond to the final accepted tie-point coordinates.

---

# 29. Stage 20 — Registration / Warp

Apply the accepted final transformation.

Potential outputs include:

- registered image
- transformed coordinates
- overlap region
- registered preview
- overlay-ready representation

Important:

> **Warping is not validation.**

A visually plausible warped image may still be based on:

- clustered matches
- false geometry
- poor transformation support
- overfit correspondences

---

## Interpolation

Image warping may require interpolation.

Do not confuse interpolation with physical information recovery.

Interpolation changes the raster sampling representation.

It does not create terrain detail that was never measured.

---

# 30. Stage 21 — Independent Evaluation

Evaluation should use the final accepted transformation.

Preferred structure:

```text
Fit Points
    ↓
Estimate Final Transform

Independent Check Points
    ↓
Evaluate Final Registration
```

Where independent check points or suitable ground truth exist, use them for final accuracy assessment.

---

## Check-Point RMSE

When available, independent check-point RMSE should specify:

- coordinate space
- units
- evaluation population
- that check points were withheld from fitting

Exact mathematical definitions belong in `METRICS.md` or equivalent benchmark documentation.

---

## When Independent Check Points Do Not Exist

Report available diagnostics honestly.

Possible outputs include:

- fit residuals
- inlier count
- inlier ratio
- spatial coverage
- registered preview

Do not call fit residual:

- independent registration accuracy
- ground-truth RMSE

unless it genuinely is.

---

# 31. Stage 22 — Ground-Error Conversion

Pixel-space error and physical ground error are separate measurements.

If converting error to metres, verify:

- valid product GSD
- relevant coordinate frame
- transform/evaluation direction
- projection context
- appropriateness of the conversion

Do not report:

```text
sub-pixel
```

as equivalent to:

```text
sub-metre
```

without scientifically valid conversion.

---

# 32. Stage 23 — Final Quality Decision

The final scientific decision may be:

```text
Final Evidence
        ↓
Quality Decision
        │
        ├── ACCEPT
        ├── REFINE
        └── REJECT
```

Potential evidence may include:

- independent RMSE
- inlier count
- inlier ratio
- spatial coverage
- residual patterns
- transform stability
- retrieval ambiguity
- other benchmark-defined quality measures

Exact quality thresholds must come from configuration/specification.

Do not invent them here.

---

## 32.1 Accept

`Accept` means the registration satisfies the defined requirements of the active benchmark or configuration.

It does not mean:

- perfect registration
- zero error
- universal validity

---

## 32.2 Refine

`Refine` means the result contains sufficient evidence to justify another defined refinement step.

Refinement must remain bounded.

Avoid indefinite loops that repeatedly tune a result until it appears successful.

---

## 32.3 Reject

`Reject` means the available evidence does not support a trustworthy registration under the active criteria.

Reject is a valid scientific outcome.

---

# 33. Stage 24 — Structured Registration Result

The final pipeline should conceptually produce a structured result containing relevant categories such as:

```text
Registration Result
├── status
├── source/reference identity
├── provenance
├── sensor/product metadata
├── preprocessing context
├── retrieval context where applicable
├── candidate match information
├── verified inlier information
├── transformation
├── evaluation metrics
├── quality decision
├── runtime information
├── failure/rejection reason
└── artifact references
```

These are conceptual responsibilities.

Do not infer exact API/schema field names from this diagram.

Exact structures belong in `DATA_FLOW.md`, contracts, or implementation.

---

# 34. Stage 25 — Optional Presentation Outputs

Only after scientific result generation should presentation layers produce items such as:

- candidate-match plots
- verified-inlier plots
- outlier plots
- residual vectors
- registered previews
- overlays
- benchmark dashboards
- interactive lunar maps
- mosaics

Presentation layers consume authoritative pipeline output.

They must not invent scientific metrics or success states.

---

# 35. Compact Online Pipeline

```text
Input
  ↓
Validate
  ↓
Read Metadata
  ↓
Identify Sensor
  ↓
Understand Product Geometry
  ↓
Preprocess
  ↓
Handle Scale
  ↓
Choose Search Route
  │
  ├── Metadata Search
  │
  └── Global Retrieval
          ↓
   Candidate Region(s)
          ↓
      Local Match
          ↓
   Candidate Matches
          ↓
   Candidate Filtering
          ↓
       RANSAC
          ↓
   Verified Inliers
          ↓
 Coverage + Residuals
          ↓
  Early Quality Check
          ↓
 Refine Tie Points
          ↓
   Refit Transform
          ↓
       Register
          ↓
Independent Evaluate
          ↓
 Accept / Refine / Reject
          ↓
 Structured Result
          ↓
 Optional UI / Mosaic
```

Stages not applicable to the active benchmark are skipped according to that benchmark's specification.

---

# 36. Offline Retrieval Preparation Pipeline

Primarily relevant to V3+ retrieval architectures.

```text
Reference Products
        ↓
Validate Products + Metadata
        ↓
Normalize Geospatial Context
        ↓
Generate Reference Tiles
        ↓
Optional Scale Pyramid
        ↓
Generate Global Descriptors
        ↓
Build Vector Index
        ↓
Store Descriptor ↔ Tile Mapping
        ↓
Record Index / Model / Data Provenance
```

The retrieval index is generated data.

The reference products and metadata remain the scientific source of truth.

---

# 37. Multiple Retrieval Candidates

When Top-K retrieval exists:

```text
Top-K Candidates
        ↓
Local Match Candidate 1
Local Match Candidate 2
...
Local Match Candidate K
        ↓
Geometric Verification
        ↓
Candidate Registration Evidence
        ↓
Select Valid Candidate
        OR
Reject All
```

Do not select the final candidate solely from retrieval similarity when stronger local geometric evidence is available.

---

# 38. Retrieval Failure

Global retrieval may fail because:

- correct region is absent from Top-K
- reference database is incomplete
- descriptor domain shift occurs
- scale differs too strongly
- illumination differs strongly
- source representation is unsuitable

Do not perform local registration against an arbitrary incorrect candidate and then report it as successful localization.

---

# 39. Benchmark Execution Pipeline

```text
Benchmark Manifest
        ↓
Select Benchmark Item / Pair
        ↓
Load Pipeline Version
        ↓
Load Canonical Configuration
        ↓
Execute Scientific Pipeline
        ↓
Capture Success / Failure
        ↓
Collect Metrics
        ↓
Record Configuration + Provenance
        ↓
Store Result
        ↓
Aggregate / Compare
```

The benchmark layer must not manually modify scientific results.

---

# 40. Benchmark V1 Pipeline

Canonical V1 remains the classical known-overlap baseline.

```text
Known Overlapping Pair
        ↓
Input Validation
        ↓
Minimal Generic Preprocessing
        ↓
SIFT / Canonical RootSIFT Configuration
        ↓
Descriptor Matching
        ↓
Candidate Filtering
        ↓
Candidate Matches
        ↓
RANSAC
        ↓
Verified Inliers
        ↓
Affine / Homography
        ↓
Spatial Coverage + Residuals
        ↓
Registration
        ↓
Baseline Evaluation
        ↓
Success / Failure
```

Canonical V1 typically excludes:

- global retrieval
- FAISS
- Top-K whole-Moon search
- advanced sensor routing
- native/full IIRS cube processing
- learned matchers
- automated multi-scale reference-pyramid search
- advanced sub-pixel refinement unless explicitly included by `V1_SCOPE.md`
- DEM-aware geometry
- piecewise warping

`V1_SCOPE.md` remains authoritative for V1 boundaries.

---

# 41. Benchmark V2 Pipeline

V2 extends the classical baseline with sensor and physical-scale awareness.

Conceptually:

```text
Input
  ↓
Validation + Metadata
  ↓
Sensor Identification
  ↓
Sensor-Aware Preprocessing
  ↓
Physical Scale / GSD Handling
  ↓
Reference Pyramid / Comparable Scale
  ↓
Local Matching
  ↓
Geometric Verification
  ↓
Registration
  ↓
Evaluation
```

Potential V2 research includes:

- OHRC-specific preparation
- TMC-2-specific preparation
- IIRS-derived 2D representations
- effective-GSD reasoning
- reference pyramids
- multi-scale matching
- structural preprocessing
- stronger illumination stress handling

V2 does not automatically require global retrieval.

---

# 42. Benchmark V3 Pipeline

V3 may introduce:

1. stronger local correspondence
2. optional global retrieval

Conceptually:

```text
Source Product
      ↓
Metadata Search
      OR
Global Retrieval
      ↓
Top-K Candidate Region(s)
      ↓
ALIKED + LightGlue
      OR
LoFTR
      OR
Selected Remote-Sensing Method
      ↓
Candidate Matches
      ↓
Geometry
      ↓
Registration
      ↓
Evaluation
```

Potential retrieval path:

```text
Source
  ↓
Global Descriptor
  ↓
FAISS / Vector Search
  ↓
Top-K Candidates
  ↓
Local Matching
```

All methods should be compared under controlled benchmark conditions.

---

# 43. Benchmark V4 Pipeline

V4 may introduce justified research-grade robustness.

Potential flow:

```text
Candidate Registration
        ↓
Detailed Residual / Geometry Analysis
        ↓
Advanced Tie-Point Refinement
        ↓
Local / Piecewise / Terrain-Aware Correction
        ↓
Final Refit
        ↓
Uncertainty / Confidence Analysis
        ↓
Quality Decision
        ↓
Accept / Refine / Reject
```

Potential research areas include:

- advanced IIRS representations
- DEM-aware geometry
- local transforms
- piecewise refinement
- uncertainty estimation
- calibrated confidence
- matcher selection
- failure classification
- scalable retrieval

V4 does not mean:

> use every advanced algorithm simultaneously.

Every additional stage should address a measured limitation.

---

# 44. Conceptual Version Comparison

The following table describes benchmark scope, not verified implementation status.

| Pipeline Capability                    | V1                     | V2                    | V3                    | V4                    |
| -------------------------------------- | ---------------------- | --------------------- | --------------------- | --------------------- |
| Known-overlap local registration       | Core                   | Core                  | Core                  | Core                  |
| Generic preprocessing                  | Core                   | Core                  | Core                  | Core                  |
| Sensor-aware preprocessing             | Minimal                | Core                  | Core                  | Advanced              |
| Physical GSD handling                  | Limited                | Core                  | Core                  | Advanced              |
| Reference-pyramid processing           | No / minimal           | Core                  | Supported             | Advanced              |
| Metadata-constrained search            | Allowed                | Supported             | Supported             | Supported             |
| Global visual retrieval                | No                     | Optional / research   | Core when required    | Scalable / advanced   |
| SIFT baseline                          | Core                   | Available             | Comparison baseline   | Available             |
| Learned local matching                 | No                     | Optional research     | Core comparison       | Adaptive / advanced   |
| RANSAC / geometry verification         | Core                   | Core                  | Core                  | Core / extended       |
| Spatial coverage evaluation            | Core                   | Core                  | Core                  | Core                  |
| Sub-pixel refinement                   | Normally excluded      | Optional / introduced | Supported research    | Advanced              |
| Final transform refit after refinement | N/A when no refinement | Required when refined | Required when refined | Required when refined |
| DEM-aware geometry                     | No                     | No / research         | Experimental          | Research focus        |
| Quality decision                       | Basic                  | Improved              | Improved              | Advanced              |
| Benchmarking                           | Core                   | Core                  | Core                  | Core                  |

Do not use this table to claim that a stage is already implemented.

---

# 45. Sensor Path Summary

| Sensor / Product | Data Nature                       | Main Pipeline Concern                                            |
| ---------------- | --------------------------------- | ---------------------------------------------------------------- |
| **OHRC**         | High-resolution panchromatic      | Fine detail, scale compatibility, illumination                   |
| **TMC-2**        | Panchromatic terrain imagery      | Terrain-scale structure, GSD differences                         |
| **IIRS**         | Hyperspectral / imaging IR        | Derive a scientifically justified 2D registration representation |
| **LRO NAC**      | High-resolution reference imagery | Fine reference registration and scale compatibility              |
| **LRO WAC**      | Wide-area reference/context       | Broader context and coarse localization                          |

Actual product metadata remains authoritative.

---

# 46. Failure Pipeline

Failure is an explicit processing branch.

```text
Input
  ↓
Validation Failure
  └────────────────────────────► REJECT

Preprocessing / Feature Extraction
  ↓
Insufficient Valid Information
  └────────────────────────────► REJECT

Matching
  ↓
Insufficient Candidate Matches
  └────────────────────────────► REJECT

Geometric Verification
  ↓
Insufficient / Inconsistent Inliers
  └────────────────────────────► REJECT

Transform Estimation
  ↓
Degenerate / Invalid Model
  └────────────────────────────► REJECT

Spatial Coverage
  ↓
Poor Distribution
  └────────────────────────────► REJECT / REFINE

Residual Analysis
  ↓
Inconsistent Geometry
  └────────────────────────────► REJECT / REFINE

Independent Evaluation
  ↓
Fails Defined Quality Criteria
  └────────────────────────────► REJECT / REFINE
```

Do not hide failed cases merely because successful examples produce better demonstrations.

---

# 47. Scientific Failure vs Software Error

Distinguish:

## Expected Scientific Failure

Examples:

- insufficient matches
- no overlap
- poor coverage
- no stable transform
- retrieval miss
- quality rejection

These should usually become structured pipeline outcomes.

## Unexpected Software Error

Examples:

- unhandled parser exception
- corrupted internal state
- programming error
- unexpected dependency failure

These should follow software error-handling mechanisms.

Do not report expected scientific rejection as if the software crashed.

---

# 48. Quality Evidence Hierarchy

The following is a conceptual evidence hierarchy:

```text
Visual Similarity
        <
Matcher Score
        <
Geometric Consistency
        <
Spatial Distribution
        <
Independent Registration Error
```

This is not a numerical weighting scheme.

It illustrates that:

- visual appearance alone is weak evidence
- matcher confidence alone is weak evidence
- geometric support is stronger
- independent evaluation is stronger still where available

---

# 49. Fit-and-Judge Anti-Pattern

Do not perform:

```text
Use Points to Fit Transform
        ↓
Measure Error on Same Points
        ↓
Claim Independent Accuracy
```

Prefer:

```text
Fit Set
    ↓
Estimate Transform

Independent Check Set
    ↓
Evaluate Transform
```

where such independent data exists.

Fit residuals remain useful diagnostics, but they are not equivalent to independent validation.

---

# 50. Pipeline Configuration

Meaningful pipeline variation should be explicit.

Potential configurable choices include:

- preprocessing strategy
- sensor-specific representation
- pyramid levels
- local matcher
- descriptor matching rules
- filtering thresholds
- RANSAC settings
- transform model
- refinement strategy
- global descriptor
- retrieval candidate count
- quality gates

Do not invent exact configuration keys or filenames here.

Use the repository's actual configuration system.

---

## No Hidden Per-Pair Tuning

Canonical benchmark runs should avoid undocumented manual parameter changes for individual pairs.

If pair-specific configuration is scientifically required, it must be:

- explicit
- reproducible
- recorded with the run

---

# 51. Randomness

Where pipeline stages use randomness:

- expose or record a seed where practical
- preserve relevant configuration
- document unavoidable nondeterminism

RANSAC and learned frameworks may not always behave identically across all environments.

Do not claim complete determinism unless it is genuinely guaranteed.

---

# 52. Provenance Through the Pipeline

Important provenance should survive from input to result.

```text
Original Product
        ↓
Metadata
        ↓
Preprocessing Configuration
        ↓
Derived Representation
        ↓
Search / Retrieval Configuration
        ↓
Matching Configuration
        ↓
Geometric Model
        ↓
Evaluation Definition
        ↓
Final Registration Result
```

This allows a result to be reconstructed and interpreted.

---

# 53. Intermediate Artifacts

Potential intermediate data may include:

- preprocessed image
- mask
- pyramid level
- reference tile
- global descriptor
- candidate match set
- inlier set
- refined tie points
- transformation
- residual vectors

Do not persist every intermediate artifact automatically.

Persistence should be justified by:

- debugging
- reproducibility
- benchmarking
- performance/caching
- research analysis

---

# 54. Cacheable Stages

Potential expensive reusable stages include:

- reference pyramids
- reference tiles
- global descriptors
- model features
- vector indexes

If caching is implemented, cache identity should account for relevant:

- source data
- method/model
- preprocessing
- configuration
- scale

to avoid stale or incompatible reuse.

Do not invent a cache architecture in this document.

---

# 55. Logging and Observability

Useful stage-level information may include:

- stage entered
- stage duration
- pair/input identifier
- sensor
- selected matcher
- candidate count
- inlier count
- spatial coverage
- transform-estimation status
- rejection reason

Avoid logging:

- complete image arrays
- huge descriptor matrices
- secrets
- unnecessary private paths

Follow existing repository logging conventions.

---

# 56. Runtime Measurement

When runtime is part of a benchmark, define the timing scope.

Possible categories include:

- preprocessing time
- retrieval time
- local matching time
- geometric verification time
- refinement time
- full registration time
- end-to-end pipeline time

Do not directly compare runtime measurements taken over different stage scopes.

---

# 57. Testing by Pipeline Stage

Pipeline boundaries should support focused tests.

| Stage                | Example Test Focus                        |
| -------------------- | ----------------------------------------- |
| Input validation     | Invalid/corrupt/empty input               |
| Metadata extraction  | Known metadata fixture                    |
| Preprocessing        | Deterministic representation output       |
| Scale handling       | Known scale relationship                  |
| Retrieval            | Candidate ranking with controlled fixture |
| Matching             | Empty descriptors / known match behavior  |
| Geometry             | Known inliers/outliers                    |
| Transform estimation | Known coordinate mapping                  |
| Spatial coverage     | Controlled point distributions            |
| Metrics              | Mathematically known examples             |
| Registration         | Known synthetic transform                 |
| Failure handling     | Insufficient evidence paths               |
| V1 integration       | Compact known-overlap lunar pair          |
| Benchmark execution  | Controlled pair/config/result flow        |

Large mission datasets should not be required for ordinary unit tests.

---

# 58. Pipeline Benchmarking

Controlled comparisons should preserve, where scientifically appropriate:

```text
Same Pair
+
Same Benchmark Definition
+
Same Metric Definition
+
Controlled Configuration
```

while changing the component under study.

Do not silently modify:

- input pair
- preprocessing
- matcher threshold
- transform policy
- metric definition
- evaluation points

and attribute the entire result difference to one algorithm.

---

# 59. Pipeline Ablations

Ablation experiments intentionally alter one component.

Examples include:

- V2 without sensor-specific preprocessing
- V3 using SIFT instead of a learned matcher
- V4 without local refinement

Ablations should be:

- explicitly named
- separately configured
- reported separately

Do not silently change canonical benchmark definitions.

---

# 60. Pipeline Anti-Patterns

## 60.1 Warp-First Pipeline

Avoid:

```text
Input
  ↓
Guess Transform
  ↓
Warp
  ↓
Looks Good
```

Registration must be supported by correspondence and geometry.

---

## 60.2 Confidence-First Acceptance

Do not accept candidate matches solely because a matcher reports high confidence.

Matcher confidence is not geometric proof.

---

## 60.3 Upsampling as Resolution Recovery

Do not treat interpolation as new physical information.

---

## 60.4 Fit-and-Judge

Do not fit a transformation and then use only the same points to claim independent accuracy.

---

## 60.5 One Threshold for Everything

Do not assume one undocumented threshold works across:

- all sensors
- all scales
- all matchers
- all benchmark versions

Thresholds should be explicit configuration/specification.

---

## 60.6 Whole-Moon Search by Default

Do not ignore reliable metadata and perform expensive global retrieval unnecessarily.

---

## 60.7 Monolithic Pipeline Script

Avoid placing:

- loading
- preprocessing
- retrieval
- matching
- geometry
- registration
- metrics
- visualization

inside one untestable function or script.

---

## 60.8 UI-Calculated Science

Frontend code must not become the authoritative source of scientific metrics or registration decisions.

---

## 60.9 Silent Fallback

Do not silently change:

- matcher
- threshold
- transform type
- retrieval strategy
- preprocessing

when a stage fails.

Fallback behavior must be explicit and traceable.

---

## 60.10 Forced Success

Do not return a transformation merely because one can be numerically estimated.

Quality rejection is valid.

---

## 60.11 Benchmark Contamination

Do not silently add V2/V3/V4 functionality to the V1 baseline.

---

# 61. Critical Ordering Changes

Changes to the following order require explicit scientific justification:

### Refinement Before Verification

```text
Candidate Matches
→ Sub-Pixel Refinement
→ RANSAC
```

instead of the normal verified-inlier-first approach.

### Evaluation Before Final Refit

Evaluating an old transform after tie-point refinement.

### Retrieval After Local Registration

Running global search only after a supposed local registration has already been chosen.

### Warp Before Geometry

Generating a registered image before validating correspondences.

### Scale Handling Too Late

Attempting local matching across an extreme GSD gap before addressing physical-scale compatibility.

### Metadata Discard Before Search

Ignoring available footprint/location data and using expensive global retrieval without reason.

These alternatives may sometimes be legitimate research experiments, but they must not arise accidentally.

---

# 62. Current vs Target Pipeline

This document intentionally does not claim implementation status for individual stages unless established elsewhere.

Use these categories when implementation status is later documented:

| Status           | Meaning                                 |
| ---------------- | --------------------------------------- |
| **Current**      | Implemented and evidenced in repository |
| **Experimental** | Research/prototype implementation       |
| **Target**       | Approved intended architecture          |
| **Planned**      | Future development                      |
| **Optional**     | Used only for applicable workflows      |

A future implementation-status table may track stages such as:

- input validation
- SIFT matching
- RANSAC
- sensor routing
- multi-scale processing
- learned matching
- global retrieval
- local refinement
- DEM-aware geometry

Do not populate that table with unsupported status claims.

---

# 63. Pipeline Change Rules

Material processing-flow changes should consider their effect on:

- benchmark comparability
- configuration
- metrics
- result provenance
- architecture documentation
- benchmark specifications
- changelog where appropriate

Changes that can materially affect scientific results include:

- reordering major stages
- modifying preprocessing
- changing matcher policy
- changing RANSAC/geometric verification
- changing transform model
- introducing refinement
- changing evaluation population
- changing metric semantics

Do not make such changes silently.

---

# 64. Pipeline Definition of Done

A mature target ChandraMap pipeline should eventually be able to:

1. accept supported scientific input
2. validate the input
3. preserve product identity and metadata
4. identify relevant sensor/product context
5. understand available projection/geospatial information
6. derive an appropriate registration representation
7. handle physical scale meaningfully
8. decide whether candidate search is needed
9. use metadata-constrained search where possible
10. perform global retrieval only where necessary
11. select one or more candidate reference regions
12. generate local candidate correspondences
13. geometrically verify candidate matches
14. identify verified inliers and rejected outliers
15. estimate an initial transformation
16. evaluate inlier coverage and residual patterns
17. reject obviously invalid geometry
18. refine supported tie points where benchmark scope permits
19. refit the final transformation after refinement
20. register the image using the final accepted transformation
21. evaluate the final result independently where possible
22. distinguish pixel-space and ground-space errors
23. produce Accept / Refine / Reject outcome
24. emit a structured registration result
25. preserve enough provenance for reproducibility
26. expose presentation artifacts without confusing them with scientific validation

This is a target capability description.

It is not a statement that every stage is currently implemented.

---

# 65. Relationship with Other Documentation

## `SYSTEM_OVERVIEW.md`

Defines:

> What major architectural components exist conceptually?

This file defines:

> In what order are those components used during scientific processing?

---

## `MODULE_MAP.md`

Should define:

> Which actual repository modules implement each stage?

This document must not invent module ownership.

---

## `DATA_FLOW.md`

Should define:

> What data structures and information move between pipeline stages?

For example:

```text
PIPELINE:
Matcher → Geometry

DATA FLOW:
Candidate Correspondence Data → Geometry Input
```

Exact class names should come from repository implementation.

---

## `V1_SCOPE.md`

Defines the authoritative canonical V1 benchmark boundary.

This pipeline must not silently add V2/V3/V4 features to V1.

---

## `DOMAIN_CONTEXT.md`

Explains why:

- GSD matters
- illumination matters
- sensor modality matters
- terrain geometry matters

This file translates those constraints into processing order.

---

## `DATASETS.md`

Defines:

- scientific products
- metadata
- provenance
- dataset roles

This pipeline defines when those properties are read and used.

---

## `TERMINOLOGY.md`

Provides canonical terms including:

- source image
- reference image
- candidate match
- verified inlier
- outlier
- tie point
- check point
- global retrieval
- local matching
- registration
- transformation
- spatial coverage

Use those terms consistently.

---

## Metrics Documentation

This file defines **where** metrics are calculated.

Metrics documentation should define **how** they are calculated mathematically.

---

# 66. Key Pipeline Rules for AI Agents

1. Validate inputs before scientific processing.

2. Preserve original metadata and provenance.

3. Identify sensor/product before sensor-specific preprocessing.

4. Use product-specific metadata over broad sensor approximations where available.

5. Treat OHRC, TMC-2, and IIRS as physically different data sources.

6. Describe IIRS as hyperspectral / imaging-infrared data.

7. IIRS needs an explicit registration-ready 2D representation before ordinary 2D matching.

8. Do not assume one IIRS representation is universally best.

9. Upsampling does not recover lost terrain detail.

10. Address physical scale before expecting local features to solve extreme GSD gaps.

11. Use reliable metadata to restrict candidate search whenever possible.

12. Do not run whole-Moon retrieval by default.

13. Global retrieval and local registration are separate stages.

14. Global descriptors and local descriptors have different purposes.

15. FAISS searches/indexes vectors; it does not extract image features.

16. FAISS does not perform local matching, RANSAC, or registration.

17. Top-K retrieval candidates still require local matching and geometric verification.

18. Local matcher output must be called **candidate matches**.

19. Matcher confidence is not geometric proof.

20. Geometric verification must precede trusting candidate matches.

21. RANSAC should produce model-consistent verified inliers and an initial transform.

22. RANSAC inliers are not automatically ground truth.

23. Do not accept a registration based only on inlier count.

24. Spatial distribution of verified inliers matters.

25. Analyze residual magnitude and spatial pattern.

26. Reject clearly invalid geometry before expensive refinement.

27. Sub-pixel refinement should normally operate on verified inliers.

28. Refit the transformation after tie-point coordinates are refined.

29. Do not continue using a stale pre-refinement transform after changing coordinates.

30. Keep transform direction explicit.

31. Registration/warping does not validate scientific accuracy.

32. Prefer independent check points for final evaluation where they exist.

33. Do not present fit-point RMSE as independent accuracy.

34. Always report error units.

35. Distinguish source-image pixel error from reference-image pixel error.

36. Distinguish pixel error from physical ground error.

37. Sub-pixel does not automatically mean sub-metre.

38. Final pipeline state may legitimately be **Reject**.

39. Do not force registration when evidence is insufficient.

40. V1 remains the simple classical known-overlap baseline.

41. Canonical V1 does not require global retrieval or FAISS.

42. Canonical V1 does not require learned matchers.

43. Canonical V1 does not require native/full IIRS hyperspectral processing.

44. V2 introduces the main sensor-aware and scale-aware processing.

45. V3 may introduce advanced learned matching and optional global retrieval.

46. V4 may introduce advanced refinement, terrain-aware processing, uncertainty, and stronger quality decisions.

47. Do not silently move V2/V3/V4 functionality into V1.

48. Benchmark versions are not software release versions.

49. Benchmark comparisons must control data, configuration, and metric definitions.

50. Do not silently tune parameters per benchmark pair.

51. Do not invent thresholds.

52. Do not invent benchmark values.

53. Do not invent Top-K values.

54. Do not invent model/checkpoint choices.

55. Do not describe target pipeline stages as currently implemented without repository evidence.

56. Distinguish expected scientific failure from unexpected software error.

57. Preserve provenance through derived representations, matching, geometry, and evaluation.

58. Do not manually alter benchmark outputs.

59. Scientific outputs must originate from the scientific pipeline, not frontend logic.

60. Mosaics, maps, dashboards, and registered previews are downstream of validated registration.

<!-- Source specification: :contentReference[oaicite:0]{index=0} -->
