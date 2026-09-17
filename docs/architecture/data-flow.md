# ChandraMap Data Flow

ChandraMap processes scientific lunar observations through a sequence of derived representations, correspondence evidence, geometric models, registration products, evaluation data, and final scientific results.

This document focuses on:

> **what information moves between ChandraMap components, what that information means, how it changes, and which scientific context must remain attached to it.**

The central data-flow principle is:

> **Scientific meaning must not be lost as data moves through the system.**

An image array, point array, transformation matrix, metric value, or generated artifact is not scientifically complete when its identity, coordinate system, units, provenance, or role is unknown.

This document describes the **logical scientific and application data flow**. It does not invent concrete classes, schemas, storage formats, API payloads, database tables, or current persistence mechanisms where repository evidence does not establish them.

---

## 1. Purpose

This document explains how information moves through ChandraMap from input observations to final scientific results.

It answers questions such as:

- What enters ChandraMap?
- What identifies a source observation?
- What identifies a reference observation?
- Which scientific metadata may accompany imagery?
- How are original products distinguished from derived representations?
- What does preprocessing consume and produce?
- How does scale handling affect data identity?
- What information flows through retrieval?
- What does local matching produce?
- What is a candidate correspondence?
- How are candidate correspondences converted into verified inliers?
- What information gives a transform scientific meaning?
- What changes when tie points are refined?
- Which transform is considered final?
- What does image registration consume and produce?
- How do fit points differ from independent check points?
- What does evaluation consume?
- How do metrics retain coordinate and unit meaning?
- How is scientific accept/reject state produced?
- What belongs to the authoritative scientific result?
- What is merely a diagnostic artifact?
- Which intermediate data may be cached?
- What may be persisted?
- How do backend transport data and scientific data differ?
- How does frontend view state differ from scientific state?
- How do Benchmark V1–V4 alter the data flow?
- How is provenance preserved?

This document defines:

**data meaning + data movement + data transformation + data lifecycle + scientific provenance.**

---

## 2. Data-Flow Principles

ChandraMap data flow follows several core principles.

### 2.1 Preserve Scientific Identity

Scientifically important data should remain associated with enough context to answer:

- where it came from
- whether it is source or reference
- which product it represents
- whether it is original or derived
- which representation is active
- which coordinate domain applies
- which configuration produced it

---

### 2.2 Original and Derived Data Remain Distinct

A normalized, cropped, resampled, pyramid, hyperspectral-derived, warped, or visualized representation must not silently replace the identity of its original scientific product.

---

### 2.3 Source and Reference Roles Remain Explicit

ChandraMap conceptually uses:

**Source**
The observation being registered.

**Reference**
The observation or region that defines the target registration context.

The preferred conceptual transform direction is:

```text
source coordinates
        ↓
     transform
        ↓
reference coordinates
```

If an implementation uses another convention internally, that convention must remain explicit.

---

### 2.4 Coordinate Meaning Travels with Data

A coordinate is incomplete without its domain.

Examples include:

- source-image `(x, y)`
- reference-image `(x, y)`
- array `(row, column)`
- pyramid-level coordinates
- projected lunar coordinates

These must not be silently interchanged.

---

### 2.5 Units Travel with Metrics

A metric is not merely a scalar.

Conceptually:

```text
metric
=
value
+
unit
+
coordinate domain
+
evaluation population
```

For example:

```text
0.8 source-image px
```

contains more scientific meaning than:

```text
0.8
```

---

### 2.6 Provenance Follows Derived Data

Derived scientific information should remain traceable, where scientifically important, to:

```text
Input Product
+
Representation
+
Configuration
+
Method
```

---

### 2.7 Rejection Is Data

Scientific rejection is not absence of output.

A rejected registration is itself a meaningful scientific result.

---

### 2.8 Generated Artifacts Are Not Scientific Truth

A plot, PNG, overlay, registered preview, report, or frontend visualization represents scientific results.

It must not become the only record of those results.

---

## 3. End-to-End Scientific Data Flow

The high-level data flow is:

```text
Source Product ──────────────┐
Source Metadata ─────────────┤
                             │
Reference Product ───────────┤
Reference Metadata ──────────┤
                             ▼
                  Scientific Observation Context
                             │
                             ▼
                   Validated Scientific Inputs
                             │
                             ▼
                    Derived Representations
                             │
                             ▼
                    Scale / Search Context
                             │
                  ┌──────────┴──────────┐
                  │                     │
          Known/Constrained        Optional Retrieval
             Reference                  │
                  │                     ▼
                  │              Retrieval Candidates
                  └──────────┬──────────┘
                             ▼
                    Local Correspondence
                             │
                             ▼
                 Candidate Correspondences
                             │
                             ▼
                    Candidate Filtering
                             │
                             ▼
                  Geometric Verification
                             │
                ┌────────────┴────────────┐
                ▼                         ▼
        Verified Inliers              Outliers
                │
                ▼
          Initial Geometry
                │
                ▼
      Optional Point Refinement
                │
                ▼
           Final Transform
                │
        ┌───────┴────────┐
        ▼                ▼
 Registration / Warp   Evaluation
        │                │
        └────────┬───────┘
                 ▼
         Accept / Reject State
                 │
                 ▼
     Authoritative Scientific Result
                 │
       ┌─────────┼───────────┬───────────┐
       ▼         ▼           ▼           ▼
   Benchmark   Backend      Frontend   Artifacts
```

Not every benchmark configuration enables every branch.

Canonical V1, for example, bypasses global retrieval.

---

## 4. Data Classification

ChandraMap data can be understood through several broad categories.

| Category                    | Meaning                                                    | Scientific Role              |
| --------------------------- | ---------------------------------------------------------- | ---------------------------- |
| Original scientific product | Provider/mission data                                      | External scientific source   |
| Metadata                    | Information describing product/acquisition/spatial context | Interpretation context       |
| Derived representation      | Processed form of original data                            | Scientific intermediate      |
| Local features/descriptors  | Derived local image evidence                               | Correspondence input         |
| Candidate correspondence    | Matcher-generated hypothesis                               | Pre-geometry evidence        |
| Verified inlier             | Model-consistent correspondence                            | Geometric evidence           |
| Transform                   | Coordinate mapping                                         | Registration geometry        |
| Registered output           | Source transformed into reference context                  | Derived scientific output    |
| Evaluation data             | Residual/error/coverage evidence                           | Quality interpretation       |
| Scientific result           | Final structured scientific outcome                        | Authoritative project result |
| Artifact                    | Visualization/report/derived file                          | Diagnostic/presentation      |
| Cache                       | Rebuildable intermediate                                   | Performance optimization     |
| Frontend view state         | Zoom/filter/tab/etc.                                       | Presentation only            |

---

### 4.1 Original Scientific Data

Original/provider products include scientifically relevant mission and reference imagery such as:

- Chandrayaan-2 OHRC products
- TMC-2 products
- IIRS products
- LRO/LROC NAC products
- LRO/LROC WAC products

Specific dataset support and product handling belong in dataset documentation.

---

### 4.2 Derived Scientific Data

Examples may include:

- normalized imagery
- masks applied to imagery
- resized representations
- pyramid levels
- selected IIRS bands
- PCA-derived representations
- gradients
- local descriptors
- registered rasters

Derived does not mean unimportant.

It means the data was produced from another scientific input and should retain lineage to that input.

---

### 4.3 Authoritative Scientific Results

The final structured scientific result is the primary project-level record of what happened during registration.

It should remain conceptually distinct from:

- visualizations
- application state
- report formatting
- caches

---

### 4.4 Diagnostic / Presentation Artifacts

Potential artifacts include:

- keypoint visualizations
- candidate-match images
- inlier/outlier plots
- overlays
- registered previews
- residual plots
- benchmark figures

These are derived presentations.

---

### 4.5 Cache Data

Caches are optional performance intermediates.

They must remain:

- rebuildable
- non-authoritative

---

### 4.6 Frontend View State

Examples include:

- zoom
- pan
- selected tab
- visible layer
- selected correspondence
- table filter

View state must never redefine scientific values.

---

## 5. Source and Reference Observations

Every local-registration attempt operates around two explicit scientific roles.

### Source Observation

The observation being geometrically registered.

Its identity should survive transitions such as:

```text
Source Product
      ↓
Source Representation
      ↓
Source Keypoints
      ↓
Source Candidate Points
      ↓
Verified Source Points
      ↓
Registered Source
```

---

### Reference Observation

The observation or region establishing the target registration frame.

Its identity should likewise survive:

```text
Reference Product
      ↓
Reference Representation
      ↓
Reference Keypoints
      ↓
Reference Candidate Points
      ↓
Verified Reference Points
```

---

### Source and Reference Must Not Collapse

Avoid generic downstream concepts such as:

```text
points_a
points_b
```

when direction is scientifically important.

The system should always be able to determine:

```text
source point i
↔
reference point i
```

---

## 6. Metadata and Scientific Context

An observation may carry scientific context such as:

- mission
- instrument
- product identifier
- GSD
- projection
- CRS
- longitude convention
- footprint
- acquisition information
- illumination context
- viewing geometry

Not every product necessarily provides every field.

---

### Unknown Metadata

If a value is unknown:

> **it remains unknown.**

Do not insert a fabricated value merely because a later component could use it.

---

### Product Metadata vs Nominal Instrument Context

Broad instrument specifications can provide approximate project context.

Specific product metadata should take precedence when it is available and scientifically appropriate.

For example:

```text
Nominal Instrument GSD
```

should not automatically override:

```text
Product-Specific GSD
```

for a particular observation.

---

### Metadata Transformation

If provider metadata is normalized internally, the normalization should preserve meaning.

Conceptually:

```text
Provider Metadata
       ↓
Internal Scientific Context
```

This document does not define concrete internal field names.

---

## 7. Validation Flow

External data enters ChandraMap in an unchecked state.

Conceptually:

```text
Unchecked External Input
        ↓
Validation
   ┌────┴─────┐
   ▼          ▼
Valid      Invalid / Unsupported
   │          │
   ▼          └──→ Stop or explicit failure path
Scientific Processing
```

---

### Validation State Is Meaningful

Invalid data must not be silently transformed into a later scientific failure.

For example:

```text
Corrupt Input
    ↓
Empty Array
    ↓
"No Features"
```

would misrepresent the true failure.

The correct interpretation is:

```text
Corrupt / Invalid Input
    ↓
Validation Failure
```

---

### Masks and No-Data

Where products include masks or no-data semantics, those semantics should remain aligned with the representation that uses them.

Do not assume:

```text
pixel value = 0
```

means:

```text
no-data
```

unless that meaning is explicitly defined.

---

### Mask Geometry

If an image is:

- cropped
- resized
- warped

the corresponding mask must remain geometrically compatible with that representation.

---

## 8. Representation and Preprocessing Flow

The representation consumed by matching may differ from the original scientific product.

Conceptually:

```text
Original Product
      ↓
Representation Selection
      ↓
Preprocessing
      ↓
Prepared Registration Representation
```

The prepared representation remains **derived scientific data**.

---

### 8.1 Panchromatic / 2D Imagery

For appropriate panchromatic or already-2D products:

```text
Original 2D Product
      ↓
Validation
      ↓
Minimal Scientific Preparation
      ↓
Prepared 2D Representation
```

The exact preparation depends on the benchmark/methodology.

---

### 8.2 Preprocessing Provenance

Where material, preprocessing should remain traceable to:

- input representation
- operation
- relevant configuration
- resulting representation

This does not require storing every intermediate byte.

The goal is interpretability and reproducibility.

---

### 8.3 IIRS / Hyperspectral Flow

IIRS requires different representation semantics.

Conceptually:

```text
Native IIRS Hyperspectral Product
              ↓
Representation Selection / Derivation
              ↓
Defined 2D Registration Representation
              ↓
Scientific Preprocessing
              ↓
Prepared 2D Representation
              ↓
Local Correspondence
```

Do not present:

```text
IIRS Cube
   ↓
"grayscale"
```

as a universally valid scientific transformation.

---

### IIRS Representation Identity

A derived IIRS representation may conceptually be based on:

- a selected spectral band
- a PCA-derived component
- a composite
- a structural/gradient representation

The chosen representation becomes part of the experiment methodology.

It must remain traceable where the result depends on it.

---

## 9. Scale and Pyramid Data Flow

Physical scale and raster dimensions are different concepts.

```text
image width × height
≠
ground sampling distance
```

A `1024 × 1024` raster does not describe its physical lunar sampling scale.

---

### Resampling

A resized image is a derived representation.

Conceptually:

```text
Original Representation
       ↓
Resampling
       ↓
Resampled Representation
```

If an image is enlarged:

```text
Upsampled Raster
```

means a denser digital sampling grid.

It does not mean:

```text
Higher-Resolution Lunar Observation
```

---

### Pyramid Levels

Where pyramid representations are used:

```text
Base Representation
       ↓
Pyramid Construction
       ↓
Level 0
Level 1
Level 2
...
```

Each level must remain linked to:

- its original product
- its source/reference role
- its resampling relationship
- its coordinate scale

---

### Keypoint Coordinates on Pyramid Levels

If features are detected on a scaled representation, their coordinates initially belong to that representation.

They must not be treated automatically as original-image coordinates.

---

### V1 Scale Boundary

Canonical V1 uses only the scale behavior permitted by V1 scope.

Advanced GSD-aware or pyramid-based scale selection belongs primarily to later benchmark research and should not appear as an active V1 data state unless explicitly included by the authoritative V1 definition.

---

## 10. Reference Candidate Flow

ChandraMap may obtain its reference region in different ways.

### Known Overlap

Canonical V1 uses:

```text
Known Source
      +
Known Overlapping Reference
      ↓
Local Registration
```

No retrieval-candidate stage is required.

---

### Metadata-Constrained Search

Where reliable spatial metadata is available:

```text
Source Spatial Metadata
        +
Reference Product / Footprint Information
        ↓
Constrained Reference Candidate
        ↓
Local Registration
```

Using valid metadata is not equivalent to image-only retrieval.

---

### Global Retrieval

When location cannot be sufficiently constrained:

```text
Source Representation
       ↓
Global Retrieval
       ↓
Candidate Reference Regions
       ↓
Local Registration
```

A retrieval candidate is an image/region/tile candidate.

It is not a point correspondence.

---

## 11. Retrieval Data Flow

Retrieval has two major data flows:

- offline reference preparation
- online query/search

This branch applies only where retrieval is enabled.

---

### 11.1 Offline Reference Preparation

Conceptually:

```text
Reference Products
       ↓
Reference Regions / Tiles
       ↓
Optional Scale Representations
       ↓
Global Descriptor Generation
       ↓
Global Descriptor Vectors
       ↓
Vector Index
       +
Reference Identity / Metadata Mapping
```

Each indexed descriptor must remain resolvable back to the scientific reference region it describes.

---

### 11.2 Global Descriptor Identity

A global descriptor is:

> a numerical representation of an image or region used for retrieval.

It must remain associated with information sufficient to identify its reference region.

A vector with no reliable mapping back to:

- product
- region/tile
- representation

has limited scientific usefulness.

---

### 11.3 Online Retrieval

Conceptually:

```text
Source Product
      ↓
Source Retrieval Representation
      ↓
Global Descriptor
      ↓
Vector Search
      ↓
Top-K Candidate Identifiers
      ↓
Resolve Reference Regions
      ↓
Local Correspondence
```

---

### 11.4 FAISS Boundary

Where FAISS is used, its data-flow responsibility is:

```text
Descriptor Vector
       ↓
FAISS Index / Search
       ↓
Candidate IDs + Similarity/Distance Information
       ↓
Reference Candidate Resolution
```

FAISS does not produce:

- image pixels
- keypoints
- local candidate correspondences
- RANSAC inliers
- transforms
- registered rasters

---

### Retrieval Candidate vs Candidate Match

Keep these terms distinct.

**Retrieval Candidate**

```text
possible reference image / tile / region
```

**Candidate Match**

```text
possible source point ↔ reference point correspondence
```

They operate at different levels.

---

## 12. Local Feature and Matcher Data Flow

Local correspondence consumes prepared source/reference representations.

Different matcher families can have different internal data flows.

---

### 12.1 SIFT

Conceptually:

```text
Prepared Source
      ↓
SIFT
      ↓
Source Keypoints
+
Source Descriptors
```

and:

```text
Prepared Reference
      ↓
SIFT
      ↓
Reference Keypoints
+
Reference Descriptors
```

Descriptor matching then produces candidate correspondences.

---

### 12.2 Keypoint Identity

A keypoint belongs to:

- one image representation
- one coordinate space
- one source/reference role

That relationship must survive matching.

---

### 12.3 Descriptor Alignment

Descriptor ordering must remain aligned with keypoint ordering.

Conceptually:

```text
descriptor i
↔
keypoint i
```

Breaking this relationship corrupts matching.

---

### 12.4 RootSIFT

Where RootSIFT is used:

```text
SIFT Descriptors
       ↓
RootSIFT Descriptor Transformation
       ↓
RootSIFT Descriptors
       ↓
Descriptor Matching
```

RootSIFT is not another detector.

---

### 12.5 Learned Sparse Flow

Where later benchmark research uses ALIKED + LightGlue:

```text
Prepared Source + Prepared Reference
              ↓
            ALIKED
              ↓
      Sparse Feature Sets
              ↓
          LightGlue
              ↓
   Candidate Correspondences
```

The extractor and matcher roles remain distinct.

---

### 12.6 Detector-Free Flow

A detector-free method such as LoFTR is represented conceptually as:

```text
Prepared Source
       +
Prepared Reference
       ↓
      LoFTR
       ↓
Candidate Point Correspondences
       +
Optional Matcher Scores
```

Do not invent artificial detector/descriptor objects merely to force every method into SIFT's internal structure.

---

## 13. Candidate Correspondence Flow

A candidate correspondence is a matcher-generated hypothesis.

Conceptually:

```text
Source Point
     ↔
Reference Point
```

It may also carry method-specific evidence such as matcher score or feature identity where available.

No exact fields are defined here.

---

### Raw and Filtered Candidates

Conceptually:

```text
Raw Candidate Correspondences
            ↓
     Matching Filters
            ↓
Filtered Candidate Correspondences
```

Candidate filtering is still part of correspondence processing.

It is not geometric verification.

---

### Candidate Pairing

Ordering must remain consistent.

```text
candidate 0
=
source point 0 ↔ reference point 0

candidate 1
=
source point 1 ↔ reference point 1
```

Filtering or sorting must preserve or explicitly rebuild that pairing.

---

### Matcher Score

A matcher score belongs to the matcher.

It must not silently become:

- final registration confidence
- probability of physical correctness
- scientific acceptance probability

unless an explicit calibration methodology establishes that interpretation.

---

## 14. Geometric Verification Flow

Geometric verification consumes paired source/reference coordinates from the candidate set.

Conceptually:

```text
Filtered Candidate Correspondences
             ↓
    Source Point Array
             +
   Reference Point Array
             ↓
      Robust Geometry
             ↓
Initial Transform
+
Inlier Mask
+
Geometry Status
```

---

### RANSAC

For the classical baseline:

```text
Candidate Points
      ↓
RANSAC
      ↓
Initial Model
+
Inlier Mask
```

RANSAC uses candidate relationships.

It does not create them.

---

### Inlier-Mask Semantics

The inlier mask must correspond to the **exact candidate set supplied to geometry**.

Incorrect:

```text
candidate set A
      ↓
RANSAC mask A

candidate set filtered/reordered into B
      ↓
apply mask A directly
```

Correct handling must preserve explicit alignment.

---

## 15. Verified Inlier and Outlier Flow

Conceptually:

```text
Candidate Correspondence Set
            +
       Inlier Mask
            ↓
    ┌───────┴────────┐
    ▼                ▼
Verified Inliers   Outliers
```

---

### Verified Inlier Meaning

A verified inlier means:

> **the candidate is consistent with the selected geometric model under the active verification policy.**

It does not automatically mean:

> **independently confirmed physical ground truth.**

---

### Outliers

Outliers are candidates rejected by geometric verification.

They may remain useful for:

- diagnostics
- failure analysis
- visualization

but they do not belong to the accepted geometric support set.

---

### Inlier Data Must Preserve Pairing

For every verified inlier:

```text
verified source point i
↔
verified reference point i
```

must remain true throughout any later refinement or evaluation.

---

## 16. Transform Data Flow

A transform is more than a numerical matrix.

Its scientific meaning conceptually includes:

- model type
- direction
- source coordinate domain
- destination coordinate domain
- numerical parameters
- validity
- supporting geometric evidence

No concrete transform class is defined here.

---

### 16.1 Transform Direction

Preferred conceptual direction:

```text
Source Coordinates
        ↓
Source → Reference Transform
        ↓
Reference Coordinates
```

If an implementation stores the inverse, that direction must remain explicit.

---

### 16.2 Initial Transform

An initial transform may be produced during robust geometric verification.

Conceptually:

```text
Candidate Correspondences
        ↓
RANSAC
        ↓
Initial Transform
```

---

### 16.3 Final Transform

The final transform is:

> the authoritative geometry used for final registration and evaluation.

In simple V1 processing:

```text
Initial Transform
=
Final Transform
```

may be possible depending on the approved methodology.

In later configurations:

```text
Initial Transform
      ↓
Refinement / Refit
      ↓
Final Transform
```

may occur.

---

### No Stale Transform

If accepted tie-point coordinates change, an old transform must not remain attached to them as though it were still authoritative.

---

## 17. Refinement Data Flow

Refinement is optional and version-dependent.

Where enabled:

```text
Verified Source Points
          +
Verified Reference Points
          ↓
Tie-Point / Sub-Pixel Refinement
          ↓
Refined Source Points
          +
Refined Reference Points
          ↓
Final Transform Refit
```

---

### Point Identity

Refinement must preserve correspondence identity:

```text
refined source point i
↔
refined reference point i
```

---

### Final Refit

If accepted coordinates change:

```text
Refined Coordinates
       ↓
Final Transform Refit
```

is required before the refined geometry can be treated as internally consistent.

---

### V1 Boundary

When canonical V1 excludes advanced refinement, this branch remains inactive.

Do not present it as standard V1 processing merely because it exists conceptually in the broader architecture.

---

## 18. Registration and Warp Flow

Registration consumes final geometry.

Conceptually:

```text
Source Representation / Raster
          +
Final Transform
          +
Reference Target Geometry
          ↓
Registration / Warp
          ↓
Registered Source / Transformed Coordinates
```

Possible outputs may include:

- registered raster
- transformed points
- overlap product
- registered preview

No specific raster format is implied.

---

### Registered Output Is Derived

A registered source image is not the original source product.

Its identity should remain conceptually:

```text
Original Source
      +
Final Transform
      ↓
Registered Source
```

---

### Registration Provenance

Where scientifically relevant, it should remain possible to determine:

- source input
- reference target
- final transform
- processing/interpolation context

that produced the registered output.

---

### Warp Success Is Not Accuracy

```text
Raster Successfully Warped
≠
Registration Scientifically Accurate
```

Warping is a transformation operation.

Evaluation determines quality.

---

## 19. Fit Data vs Independent Check Data

This distinction is fundamental.

### Fit Points

Points used to estimate/finalize the transform.

```text
Verified Fit Points
        ↓
Transform Estimation
```

---

### Independent Check Points

Trusted points withheld from model fitting and used for evaluation.

```text
Independent Check Points
        ↓
Final Transform Evaluation
```

---

### Combined Flow

```text
Verified Fit Points
        ↓
Transform Estimation
        ↓
Final Transform
        │
        ├────────────→ Registration
        │
        └────────────→ Independent Check Points
                              ↓
                     Accuracy Evaluation
```

---

### No Evaluation Leakage

Independent evaluation truth must not silently influence:

- reference selection
- matcher choice
- pair-specific filtering
- transform model selection
- threshold tuning

unless the experiment is explicitly labelled as diagnostic/oracle evaluation.

---

## 20. Evaluation and Metric Flow

Evaluation consumes the final geometry together with the appropriate scientific evidence.

Potential inputs include:

- candidate summary
- verified inliers
- final transform
- fit points
- independent check points
- source/reference scale information
- runtime context

Potential outputs include:

- residuals
- inlier ratio
- coverage
- RMSE
- independent check-point error
- ground error where valid
- runtime values
- quality evidence

Exact formulas belong elsewhere.

---

### Metric Population

Every metric should remain associated with the population used to compute it.

Examples:

```text
RMSE over fit points
```

```text
RMSE over independent check points
```

```text
inlier ratio over filtered candidates
```

```text
coverage over verified inliers
```

These are different scientific quantities.

---

### Metric Units

A metric should retain:

```text
value
+
unit
+
coordinate domain
+
population
```

---

### Source-Image Pixel Error

If measured in source pixels:

```text
source-image px
```

must remain explicit through:

```text
Core Result
→ Benchmark
→ Backend
→ Frontend
→ Report
```

---

### Reference-Image Pixel Error

Likewise:

```text
reference-image px
```

must not be silently shortened into generic `px` when the distinction matters.

---

### Ground Error

Ground-unit error should exist only when scientific conversion is valid.

Conceptually:

```text
Image-Space Error
       +
Valid Spatial / GSD / Projection Context
       ↓
Scientifically Valid Ground Error
```

A frontend must not generate authoritative ground error by multiplying pixel error by an approximate instrument specification.

---

### Sub-Pixel Semantics

`Sub-pixel` means:

> less than one pixel in the specified image coordinate system.

It does not automatically mean:

> less than one metre.

---

## 21. Accept / Reject Flow

Scientific decision logic consumes quality evidence.

Conceptually:

```text
Transform Validity
        +
Verified Support
        +
Coverage
        +
Residual Evidence
        +
Independent Accuracy where required
        ↓
Scientific Quality Decision
        ↓
Accepted / Rejected
```

No universal numerical thresholds are defined here.

---

### Scientific Status Is Explicit Data

Do not infer scientific success from:

- existence of a transform file
- existence of a registered PNG
- successful HTTP response
- completed backend job

The authoritative scientific result should carry explicit status.

---

### Rejection Reason

Where the project defines structured rejection information, it may conceptually distinguish causes such as:

- insufficient features
- insufficient candidate support
- no valid model
- degenerate geometry
- inadequate spatial support
- quality requirement not satisfied

Exact enums are not defined here.

---

## 22. Authoritative Scientific Result

The primary output of the scientific engine is one authoritative scientific result.

Conceptually, it may carry categories of information such as:

### Identity

- source/reference identity
- pair/run identity where defined

### Method Context

- configured methodology
- benchmark configuration
- representation strategy where relevant

### Correspondence Summary

- candidate support
- verified support

### Geometry

- final transform
- transform model
- direction
- coordinate semantics

### Evaluation

- metrics
- units
- populations

### Scientific Status

- accepted
- rejected
- invalid/unsupported where defined

### Failure Information

- why an accepted registration could not be produced

### Artifact References

- links/references to derived diagnostics where supported

No concrete result class or field names are defined here.

---

### One Authoritative Result

```text
                    Scientific Core
                         ↓
              Authoritative Result
             ┌───────────┼──────────┐
             ▼           ▼          ▼
         Benchmark     Backend      CLI
                         │
                         ▼
                      Frontend
```

Consumers may:

- serialize
- aggregate
- visualize
- report

the result.

They must not redefine its scientific meaning.

---

## 23. Diagnostic Artifact Flow

Scientific state may be converted into diagnostic artifacts.

Conceptually:

```text
Scientific Result
      +
Intermediate Evidence
      ↓
Artifact Generation
      ↓
Visualization / Report / Preview
```

Potential artifacts include:

- keypoint visualization
- candidate-match visualization
- inlier/outlier visualization
- spatial-coverage plot
- registered preview
- overlay
- residual plot
- benchmark report

---

### Artifact ≠ Result

A diagnostic image or report is not the authoritative scientific record.

Avoid:

```text
match_plot.png
=
only record of which correspondences were accepted
```

or:

```text
report.html
=
only copy of benchmark metrics
```

where structured scientific results exist.

---

### Artifact Lineage

Where practical:

```text
Artifact
   ↓
traceable to
   ↓
Scientific Result / Run
```

---

## 24. Cache and Intermediate Data

Some derived information may be useful to cache.

Conceptual examples include:

- prepared representations
- pyramid levels
- local descriptors
- global descriptors
- reference tiles
- vector indexes

This does not claim that ChandraMap currently caches any particular category.

---

### Cache Principle

A cache must remain:

```text
Rebuildable
+
Non-Authoritative
```

Deleting a cache should not erase the only scientific record of benchmark results.

---

### Cache Identity

A scientifically safe cache key conceptually depends on the upstream information that determines the cached output.

For example:

```text
Product Identity
+
Representation
+
Preprocessing Configuration
+
Scale
+
Method / Model Identity where relevant
```

No cache-key schema is defined here.

---

### Stale Cache Risk

Suppose:

```text
Preprocessing A
      ↓
Descriptors A
```

If preprocessing changes to:

```text
Preprocessing B
```

then `Descriptors A` may no longer be valid.

Cached derivatives should not silently cross incompatible scientific configurations.

---

## 25. Persistence and Result Lineage

Permanent persistence is an application/reproducibility concern rather than a requirement of the scientific engine itself.

The scientific engine should be able to compute a result without assuming that a database exists.

---

### Potentially Persistable Information

Depending on actual project needs, persistence may include:

- structured scientific results
- registered outputs
- benchmark outputs
- generated artifacts
- application/job metadata

No current database or persistence technology is assumed.

---

### Persistence Categories

Keep conceptually separate:

```text
Scientific Result
```

```text
Artifact
```

```text
Benchmark Aggregate
```

```text
Application / Job Metadata
```

They represent different responsibilities.

---

### Local Paths Are Not Scientific Identity

A local path may help locate data operationally.

It should not be the sole portable identity of:

- a mission product
- a benchmark pair
- a scientific run

where stronger identifiers are available.

---

## 26. Benchmark Data Flow

Benchmarking adds controlled pair-population and aggregation layers around pair-level scientific execution.

Conceptually:

```text
Benchmark Definition / Manifest
            ↓
Source / Reference Pair Definitions
            ↓
Benchmark Configuration
            ↓
Scientific Core Execution
            ↓
Pair-Level Results
            ↓
Benchmark Aggregation
            ↓
Tables / Reports / Figures
```

No manifest schema is defined here.

---

### Pair-Level vs Aggregate Results

Keep separate:

```text
Pair RMSE
```

from:

```text
Benchmark RMSE Summary
```

and:

```text
Pair Status
```

from:

```text
Benchmark Success / Rejection Rate
```

---

### Benchmark Population Identity

An aggregate result is meaningful only with knowledge of the population over which it was calculated.

Conceptually:

```text
Aggregate Metric
+
Benchmark Population Identity
+
Metric Definition
+
Configuration
```

---

### Rejected Cases Stay in the Flow

Rejected or failed cases must not disappear before benchmark aggregation.

Otherwise:

```text
Accuracy on successful cases
```

can be mistaken for:

```text
overall reliability
```

---

## 27. Benchmark V1 Data Flow

Canonical V1 begins with known overlap.

Its compact data flow is:

```text
Source Product + Metadata
          │
          ▼
Source 2D Representation
          │
          │       Known Reference Product + Metadata
          │                    │
          │                    ▼
          │         Reference 2D Representation
          │                    │
          └──────────┬─────────┘
                     ▼
              SIFT Features
        [optional RootSIFT descriptor
        configuration when explicitly used]
                     ↓
          Candidate Correspondences
                     ↓
            Candidate Filtering
                     ↓
                  RANSAC
            ┌────────┴────────┐
            ▼                 ▼
     Verified Inliers      Outliers
            │
            ▼
  Affine / Homography Geometry
            │
            ▼
    Coverage / Residual Evidence
            │
      ┌─────┴─────┐
      ▼           ▼
 Registration   Evaluation
      │           │
      └─────┬─────┘
            ▼
       Accept / Reject
            ↓
      Pair-Level Result
```

Canonical V1 does not include:

- global descriptors
- FAISS
- Top-K visual retrieval
- learned local matchers
- advanced sensor-specific routing
- native full-cube hyperspectral correspondence
- advanced refinement where excluded by V1 scope

---

## 28. V2, V3, and V4 Data-Flow Extensions

These are high-level research extensions, not implementation-status claims.

### V2 — Sensor / Scale Aware

Conceptually:

```text
Observation + Metadata
        ↓
Sensor-Aware Representation
        ↓
Scale / GSD Handling
        ↓
Shared Local Correspondence
        ↓
Shared Geometry
        ↓
Shared Evaluation
```

Potential additional data may include:

- sensor-context decisions
- representation identity
- scale hypothesis
- pyramid-level identity

---

### V3 — Advanced Matching / Retrieval

Conceptually:

```text
Source Representation
        ↓
Optional Global Descriptor
        ↓
Candidate Retrieval
        ↓
Reference Candidate Region
        ↓
Advanced Local Correspondence
        ↓
Shared Geometry
        ↓
Shared Evaluation
```

Possible learned methods add:

- model identity
- checkpoint identity
- matcher-specific scores

where scientifically relevant.

---

### V4 — Advanced Robustness

Conceptually:

```text
Verified Correspondences
        ↓
Advanced Refinement / Local Geometry
        ↓
Final Geometry
        ↓
Advanced Evaluation
        ↓
Quality / Uncertainty / Rejection Research
```

Additional data semantics may include:

- refined tie points
- local geometric state
- uncertainty information
- calibrated confidence if scientifically established

---

### Shared Result Semantics

V1–V4 are benchmark/research configurations.

They are not software data-format versions.

Prefer stable shared scientific result meaning across benchmark versions wherever possible.

---

## 29. Backend Data Flow

Backend/application data is not identical to internal scientific data.

Conceptually:

```text
Client Request
      ↓
Transport Representation
      ↓
Application Input
      ↓
Core Scientific Input
      ↓
Scientific Core
      ↓
Authoritative Core Result
      ↓
Application Result
      ↓
Transport Response
```

---

### Transport Object ≠ Scientific Domain Object

A request or response format exists for application communication.

It should not redefine:

- scientific coordinate semantics
- transform direction
- metric definition
- registration status

---

### Backend Serialization

Backend translation may serialize:

- status
- transform
- metrics
- provenance
- artifact references

It must not independently recalculate those scientific values.

---

### Scientific Rejection vs Application Success

These may coexist:

```text
Application Request:
Succeeded
```

```text
Scientific Registration:
Rejected
```

The application successfully executed a valid scientific workflow whose conclusion was that the registration was not sufficiently trustworthy.

---

### Job State

If a job/execution system is introduced, job lifecycle information must remain separate from scientific result state.

No job infrastructure is assumed by this document.

---

## 30. Frontend Data Flow

Conceptually:

```text
Backend Result
      ↓
Frontend Data Layer
      ↓
View Model
      ↓
Scientific Visualization
```

The frontend may derive:

- formatted strings
- chart-ready values
- selected rows
- visible layers
- presentation grouping

It must not derive new authoritative science.

---

### View State vs Scientific State

Examples of frontend view state:

- zoom
- pan
- selected tab
- selected correspondence
- hidden/visible overlay
- table sorting
- table filter

These may change:

> what the user sees.

They must not change:

- transform
- RMSE
- inlier classification
- scientific accept/reject status

---

### Filtered Benchmark Views

A frontend filter may show only one stress category.

That does not change the canonical full-population benchmark aggregate.

---

## 31. Configuration, Model, Seed, and Runtime Context

Scientific configuration is part of the method.

Conceptually:

```text
Benchmark / Application Selection
            ↓
Scientific Configuration
            ↓
Core Components
```

Avoid hidden scientific behavior determined by:

- filename
- developer-local state
- undocumented pair-specific conditions

---

### Configuration Provenance

For important benchmarked results, configuration should remain traceable enough to understand the executed methodology.

This document does not define configuration precedence.

---

### Randomness and Seeds

Where components use randomness:

- robust sampling
- training procedures
- experimental sampling

seed information may be scientifically relevant.

Do not claim determinism unless the actual method/runtime guarantees it.

---

### Model / Checkpoint Identity

For learned methods, a result may depend on:

- model architecture
- checkpoint
- model configuration

where applicable.

A model checkpoint is not the same as an evaluation check point.

---

### Runtime Context

Runtime measurements may depend on:

- hardware
- CPU/GPU
- image size
- cache state
- warm-up/model loading
- processing stages included

A bare timing value is not always directly comparable across runs.

---

## 32. Failure and Partial-Result Flow

Data flow can terminate before a complete registration is produced.

Conceptually:

```text
Invalid Input
→ no normal scientific processing

No Usable Features
→ no candidate correspondences

Insufficient Candidates
→ no geometric model

No Valid Model
→ no final transform

Quality Rejection
→ no accepted registration
```

---

### Partial Diagnostic Data

Earlier scientific evidence may still exist after later failure.

For example:

```text
Candidate Correspondences
        ↓
Geometry Fails
```

The candidate set may remain useful for diagnostics.

It must not be interpreted as an accepted registration result.

---

### Failure Categories

Conceptually distinguish:

- invalid input
- unsupported representation/capability
- scientific rejection
- external dependency failure
- software/runtime error

Do not reduce every outcome to:

```text
None
```

or:

```text
false
```

when richer semantics are available.

---

### No Placeholder Success

Do not create:

```text
identity transform
```

```text
RMSE = 0
```

or:

```text
empty inlier set + success
```

as replacements for genuine failure/rejection.

---

## 33. Data Invalidation Rules

Scientific intermediates depend on upstream state.

If upstream state changes, dependent data may become invalid.

For example:

```text
Source Product
      ↓
Preprocessing A
      ↓
Descriptors A
      ↓
Matches A
      ↓
Transform A
      ↓
Metrics A
```

If preprocessing changes:

```text
Preprocessing B
```

then the old:

- descriptors
- matches
- transform
- metrics

may no longer be valid.

---

### Change Propagation

| Upstream Change           | Potentially Invalidates                                         |
| ------------------------- | --------------------------------------------------------------- |
| Source/reference product  | All downstream derivatives                                      |
| Scientific representation | Features, descriptors, matches, geometry, registration, metrics |
| Preprocessing             | Dependent descriptors/matches onward                            |
| Scale/pyramid level       | Coordinates, features, matches, geometry                        |
| Matcher                   | Candidate correspondences onward                                |
| Candidate filtering       | Geometry onward                                                 |
| Transform model/policy    | Transform, warp, evaluation                                     |
| Refined tie points        | Final transform, warp, evaluation                               |
| Evaluation definition     | Metrics and benchmark aggregates                                |
| Benchmark pair population | Aggregate benchmark results                                     |
| Model/checkpoint          | Learned-method outputs onward                                   |

This table is conceptual rather than a cache implementation specification.

---

### Stale Output Rule

Never combine incompatible states such as:

```text
Refined Tie Points
+
Pre-Refinement Transform
```

and call them one final scientific result.

---

## 34. Provenance and Reproducibility

A robust scientific result should ideally allow reconstruction of the important scientific chain:

```text
Input Products
      ↓
Active Representations
      ↓
Configuration
      ↓
Method / Model
      ↓
Correspondence Evidence
      ↓
Final Geometry
      ↓
Registration
      ↓
Evaluation Metrics
      ↓
Scientific Status
      ↓
Artifacts
```

---

### Minimum Provenance Principle

Reproducibility does not require every run to persist every intermediate byte.

Preserve enough information to:

- interpret the result
- reproduce it where practical
- audit methodology
- compare runs
- identify important dependencies

---

### Product Identity vs Local Path

Prefer scientific/product identity where available.

A local filesystem path may change without the scientific product itself changing.

---

### Benchmark Provenance

Benchmark results should remain associated with:

```text
Pair Population
+
Scientific Configuration
+
Relevant Data Identity
+
Relevant Code / Model Identity
+
Metric Definitions
```

where the project records those items.

---

## 35. Trust Boundaries

External scientific/software data crosses a trust boundary before entering ChandraMap processing.

Potential external data includes:

- raster/image files
- metadata
- archives where supported
- configuration
- model checkpoints
- external vector indexes

Detailed security controls belong in dedicated security policy/implementation documentation.

---

### Trust Boundary Flow

```text
External Input
      ↓
Boundary / Structural Validation
      ↓
Scientific Validation
      ↓
Scientific Processing
```

---

### Sensitive Runtime Information

Scientific result serialization should not accidentally expose:

- secret environment variables
- credentials
- private tokens
- unnecessary local absolute paths

when results leave the local process/application boundary.

---

### External Scientific Data

Mission/provider products should be used and redistributed according to applicable provider/licensing terms.

Detailed dataset licensing belongs in dataset/legal documentation.

---

## 36. Data Ownership

| Data                              | Primary Conceptual Owner   | Typical Consumers                            |
| --------------------------------- | -------------------------- | -------------------------------------------- |
| Source/reference product identity | Data/domain layer          | Core, benchmark, backend                     |
| Scientific metadata               | Data/domain context        | Representation, scale, retrieval, evaluation |
| Prepared representation           | Scientific core            | Matcher, retrieval where applicable          |
| Local keypoints/descriptors       | Feature extraction         | Matcher                                      |
| Global descriptor                 | Retrieval descriptor stage | Vector search                                |
| Retrieval candidate               | Retrieval stage            | Local registration                           |
| Candidate correspondences         | Local matching             | Geometry, diagnostics                        |
| Verified inliers                  | Geometry                   | Refinement, transform/evaluation             |
| Outliers                          | Geometry                   | Diagnostics                                  |
| Transform                         | Geometry                   | Registration, evaluation                     |
| Registered output                 | Registration               | Evaluation, backend/UI/research              |
| Scientific metrics                | Evaluation                 | Decision, benchmark, backend/UI              |
| Scientific status                 | Decision/result layer      | All outer consumers                          |
| Scientific result                 | Core/result layer          | Benchmark, backend, CLI                      |
| Artifact                          | Artifact layer             | Backend/UI/research/docs                     |
| Benchmark aggregate               | Benchmark layer            | Reports/UI                                   |
| Application/job state             | Backend/application layer  | Frontend                                     |
| View state                        | Frontend                   | Frontend only                                |

This table describes logical ownership, not concrete class ownership.

---

## 37. Data Lifecycle

| Data Category                    | Created By                                 | Typical Lifetime            |                         Authoritative? |                            Rebuildable? |
| -------------------------------- | ------------------------------------------ | --------------------------- | -------------------------------------: | --------------------------------------: |
| Original scientific product      | External provider/source                   | External/project-controlled |                        External source |           No, not from ChandraMap alone |
| Product metadata                 | External provider / project interpretation | With product/context        |   Yes for its layer when authoritative |                       Depends on source |
| Prepared representation          | Scientific processing                      | Run or persisted derivative |                                     No |                             Usually yes |
| Pyramid/resampled representation | Scale processing                           | Run/cache                   |                                     No |                                     Yes |
| Local descriptors                | Feature extraction                         | Run/cache                   |                                     No |                                     Yes |
| Global descriptors               | Retrieval preparation                      | Run/index preparation       |                                     No |          Yes if inputs/method available |
| Candidate correspondences        | Matcher                                    | Run                         |                                     No |                                     Yes |
| Verified inliers                 | Geometry                                   | Run/result context          |                    Scientific evidence |                 Yes from upstream state |
| Final transform                  | Geometry/refit                             | Run/result context          | Authoritative geometry for that result | Recomputable from sufficient provenance |
| Registered output                | Registration                               | Run/artifact                |                         Derived output |                             Usually yes |
| Scientific metrics               | Evaluation                                 | Result                      |          Authoritative for that result |   Recomputable from sufficient evidence |
| Scientific status                | Decision/result layer                      | Result                      |                                    Yes |  Recomputable from same policy/evidence |
| Diagnostic artifact              | Artifact generation                        | Optional                    |                                     No |                             Usually yes |
| Cache                            | Cache subsystem if used                    | Temporary/rebuildable       |                                     No |                                     Yes |
| Frontend view state              | Frontend                                   | Session/view                |                                     No |                                     Yes |

No specific retention time or persistence mechanism is implied.

---

## 38. Data-Flow Invariants

The following invariants should remain true unless the scientific architecture is deliberately revised.

1. Source and reference identity is never ambiguous.

2. Original products and derived representations remain distinguishable.

3. Scientifically important representation provenance is preserved.

4. Missing metadata is not fabricated.

5. Product-specific metadata takes precedence over broad nominal context where appropriate.

6. Masks/no-data remain aligned with the representation they describe.

7. Image dimensions are not treated as physical GSD.

8. Upsampled data is identified as resampled data rather than higher-resolution observation.

9. Pyramid-level coordinates remain associated with their pyramid level.

10. Global descriptors remain associated with their reference image/region.

11. Vector-index identifiers reliably resolve back to reference scientific context.

12. Retrieval candidates are not point correspondences.

13. Global descriptors are not local descriptors.

14. Keypoints remain associated with the representation on which they were detected.

15. Descriptors remain aligned with their keypoints.

16. Candidate source/reference points remain paired.

17. Filtering does not silently break candidate ordering.

18. Candidate matches are not verified inliers.

19. Inlier masks correspond exactly to the candidate population supplied to geometry.

20. Verified inliers are model-consistent evidence, not independent ground truth.

21. Outliers remain distinguishable from accepted geometric support.

22. Transform model and direction remain explicit.

23. Transform coordinate domains remain explicit.

24. An initial transform is not automatically the final transform.

25. Refined source/reference points remain paired.

26. If accepted point coordinates change, the final transform is refit.

27. Final registration and evaluation use the final transform.

28. Fit data remains distinguishable from independent check data.

29. Evaluation truth does not leak upstream into ordinary model fitting or hidden tuning.

30. Metrics preserve the population on which they were calculated.

31. Metrics preserve scientifically meaningful units.

32. Source-image pixels and reference-image pixels are not interchangeable.

33. Ground-metre conversion requires valid scientific context.

34. Sub-pixel does not automatically imply sub-metre.

35. Registered output remains identifiable as derived from the source.

36. Warp completion does not become evidence of scientific accuracy by itself.

37. Scientific status is explicit.

38. Rejection remains a first-class scientific result.

39. Partial diagnostic output does not become accepted scientific output.

40. Cache data is not authoritative source truth.

41. Diagnostic artifacts are not authoritative scientific results.

42. Backend serialization does not recalculate or reinterpret scientific truth.

43. Frontend view state cannot mutate scientific state.

44. Benchmark aggregation retains failed/rejected cases according to methodology.

45. Benchmark aggregates remain tied to their evaluated population.

46. Configuration remains associated with benchmarked methodology.

47. Model/checkpoint identity remains traceable where learned-method behavior depends on it.

48. V1–V4 share consistent core result semantics where scientifically possible.

49. Benchmark V1–V4 are not software/data-format version numbers.

50. Current concrete contracts and conceptual target data flow must not be confused.

---

## 39. Data-Flow Anti-Patterns

### Anonymous Image Arrays

Bad:

```text
array
```

with no ability to identify:

- source/reference
- product
- representation
- scale
- coordinate context

---

### Derived-as-Original

A normalized, resized, PCA-derived, or warped representation is labelled as though it were the original mission product.

---

### Anonymous Points

Bad:

```text
[[x1, y1], [x2, y2], ...]
```

with no indication of:

- source/reference role
- coordinate domain
- active representation

---

### Candidate / Inlier Collapse

Candidate matches are overwritten and later treated as though they were geometrically verified correspondences.

---

### Mask Misalignment

An inlier mask from one candidate ordering is applied to another ordering.

---

### Anonymous Transform

Bad:

```text
[[...], [...], [...]]
```

with no model, direction, or coordinate meaning.

---

### Stale Transform

Tie points are refined but the pre-refinement transform remains labelled final.

---

### Evaluation Leakage

Independent check points or final truth influence ordinary fitting/tuning.

---

### Anonymous Metric

Bad:

```text
rmse = 0.8
```

without:

- units
- coordinate domain
- evaluation population

---

### Hidden Unit Conversion

Pixel error becomes metres without explicit valid spatial context.

---

### Hidden CRS Conversion

Lunar coordinates are silently transformed using an undocumented convention or Earth spatial defaults.

---

### Matcher Score as Final Confidence

A local matcher score becomes a reported registration probability without calibration.

---

### Warp-as-Truth

A successfully generated registered image is treated as proof that the alignment is accurate.

---

### Artifact-as-Truth

A PNG, chart, or HTML report becomes the only copy of scientific metrics.

---

### Cache-as-Truth

Deleting a cache destroys the only record of a benchmark result.

---

### Backend Recalculation

The application/backend layer implements another definition of RMSE or coverage.

---

### Frontend Recalculation

The browser recomputes authoritative scientific metrics or inlier classifications.

---

### Filename-as-Provenance

Scientific identity depends solely on a local filename.

---

### Cross-Version Result Forking

V1, V2, V3, and V4 create incompatible result meanings without scientific necessity.

---

### Placeholder Success

A failed workflow generates:

```text
identity transform
zero error
success
```

to satisfy downstream code.

Failure should remain failure/rejection.

---

## 40. Data Flow in Testing

Testing data and scientific benchmark data serve different purposes.

### Synthetic / Fixture Data

Useful for testing:

- coordinate handling
- transform direction
- geometry
- mask alignment
- metric calculations
- failure behavior

Conceptually:

```text
Known Source Points
       ↓
Known Transform
       ↓
Synthetic Reference Points
       ↓
Geometry Implementation
       ↓
Recovered Transform
       ↓
Compare with Known Truth
```

This validates software behavior.

It is not lunar-domain benchmark evidence.

---

### Real Lunar Data

Real source/reference observations are needed for:

- domain validation
- scientific benchmarking
- sensor/modality evaluation
- scale/illumination stress evaluation

Real data does not replace controlled software tests.

---

## 41. Current vs Target Data Flow

This document primarily defines stable scientific data semantics that ChandraMap should preserve.

It does **not** claim that every described data state currently exists as:

- a concrete Python class
- a serialized schema
- a persisted file
- an API DTO
- a database record
- a cache entry

In particular, descriptions of:

- retrieval descriptors
- vector indexes
- advanced refinement
- learned-model checkpoint provenance
- advanced V4 uncertainty data

are architectural/research data-flow concepts unless implementation evidence establishes them as current.

Actual concrete contracts take precedence when they exist.

---

## 42. Maintenance Rules

Update this document when a change materially affects the meaning or lifecycle of scientific data.

Examples include:

- a new core scientific state is introduced
- candidate correspondence semantics change
- transform representation changes
- coordinate conventions change
- refinement becomes part of an active workflow
- retrieval becomes integrated
- result semantics change
- benchmark aggregation changes
- backend/core scientific contracts change materially
- frontend begins consuming a new scientific state
- provenance requirements change
- metric unit/population meaning changes

Do not update this file for every internal implementation refactor that preserves the same data semantics.

---

## 43. Related Documents

- [`system-overview.md`](./system-overview.md) — major ChandraMap system responsibilities
- [`core-engine-architecture.md`](./core-engine-architecture.md) — reusable scientific-engine responsibilities
- [`backend-architecture.md`](./backend-architecture.md) — backend/application boundary and result translation
- [`frontend-architecture.md`](./frontend-architecture.md) — scientific-result visualization and frontend state boundaries
- [`module-map.md`](./module-map.md) — repository ownership and dependency direction
- [`v1-pipeline.md`](./v1-pipeline.md) — ordered canonical Benchmark V1 processing
- [`../project/overview.md`](../project/overview.md) — overall project context
- [`../project/terminology.md`](../project/terminology.md) — canonical terminology used by this data flow
- [`../project/assumptions.md`](../project/assumptions.md) — scientific assumptions underlying the flow
- [`../project/limitations.md`](../project/limitations.md) — scientific limitations and failure boundaries
- [`../project/v1-scope.md`](../project/v1-scope.md) — canonical human-facing V1 scope
- [`.ai/architecture/DATA_FLOW.md`](../../.ai/architecture/DATA_FLOW.md) — deeper AI/maintainer data-flow context
- [`.ai/architecture/PIPELINE.md`](../../.ai/architecture/PIPELINE.md) — broader processing-stage ordering
- [`.ai/architecture/MODULE_MAP.md`](../../.ai/architecture/MODULE_MAP.md) — AI-oriented repository responsibility map
- [`.ai/architecture/SYSTEM_OVERVIEW.md`](../../.ai/architecture/SYSTEM_OVERVIEW.md) — deeper system architecture context
- [`.ai/context/DATASETS.md`](../../.ai/context/DATASETS.md) — scientific dataset, provenance, and metadata context
- [`.ai/context/DOMAIN_CONTEXT.md`](../../.ai/context/DOMAIN_CONTEXT.md) — lunar imaging and geospatial constraints
- [`.ai/context/V1_SCOPE.md`](../../.ai/context/V1_SCOPE.md) — canonical detailed V1 contract
- [`.ai/development/BENCHMARK_RULES.md`](../../.ai/development/BENCHMARK_RULES.md) — benchmark comparability and result-governance rules
- [`.ai/development/TESTING_RULES.md`](../../.ai/development/TESTING_RULES.md) — software/scientific testing principles

ChandraMap data should become more derived as it moves through the system, but it should never become less interpretable.

The source/reference role, scientific provenance, coordinate meaning, transform semantics, metric units, and final status must remain understandable from input through final result.

<!-- Source specification: :contentReference[oaicite:0]{index=0} -->
