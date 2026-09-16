# ChandraMap Data Flow

This document defines the authoritative high-level **scientific data flow** for ChandraMap.

It explains what information enters the system, how scientific context travels with that information, what major processing stages consume and produce, how provenance and coordinate semantics are preserved, how correspondences evolve into verified geometry, and how scientific results differ from generated artifacts.

The central principle is:

> **Scientific imagery must not move through ChandraMap as anonymous pixel arrays when identity, metadata, coordinate context, scale, provenance, or processing history are scientifically relevant.**

Conceptually, important data should remain connected to:

```text
Pixels
+
Scientific Metadata
+
Provenance
+
Coordinate Context
+
Processing History
```

This document describes conceptual data movement. It does not define concrete Python classes, API schemas, database models, serialized field names, or current implementation status unless those are verified elsewhere.

---

## 1. Purpose

`DATA_FLOW.md` answers:

> **What information moves between ChandraMap components, in what form, and what must remain preserved as it moves?**

It defines the movement of:

- source scientific products
- reference scientific products
- product metadata
- derived representations
- retrieval data
- local features
- candidate correspondences
- verified inliers
- transformation data
- refined tie points
- evaluation data
- benchmark context
- structured results
- generated artifacts
- failure/status information

It also defines important boundaries around:

- source vs reference identity
- authoritative vs derived data
- fit data vs evaluation data
- initial vs final transforms
- image-space vs geospatial coordinates
- pixel units vs ground units
- scientific result vs presentation artifact

---

## 2. Core Data-Flow Principles

Every important transformation should preserve enough information to answer:

1. Where did this data originate?
2. Which mission, sensor, or product produced it?
3. Which source or reference role does it have?
4. Which processing operations changed it?
5. Which configuration produced the derived representation?
6. Which coordinate domain does it use?
7. Which units apply to its measurements?
8. Is it authoritative source data or derived data?
9. Which benchmark or experiment produced it?
10. Can the output be traced back to its scientific inputs?

Important data lineage should not disappear merely because one pipeline stage only requires an array numerically.

---

## 3. End-to-End Data Flow

```text
Source Product
    +
Source Metadata
        │
        ▼
Validated Source Record
        │
        ├────────────────────────────────┐
        │                                │
        ▼                                ▼
Derived Source Representation      Reference Data
                                         │
                                         ▼
                               Reference Candidate(s)
                                         │
        ┌────────────────────────────────┘
        │
        ▼
Source Representation
        +
Reference Representation
        │
        ▼
Candidate Correspondences
        │
        ▼
Geometric Verification
        │
        ├── Rejected Outliers
        │
        └── Verified Inliers
                    │
                    ▼
             Initial Transform
                    │
                    ▼
         Coverage + Residual Data
                    │
                    ▼
       Optional Tie-Point Refinement
                    │
                    ▼
             Refined Tie Points
                    │
                    ▼
           Final Transform Refit
                    │
             ┌──────┴──────┐
             ▼             ▼
      Registered Output   Independent Evaluation
             │             │
             └──────┬──────┘
                    ▼
          Registration Result
                    │
          ┌─────────┴─────────┐
          ▼                   ▼
     Result Record         Artifacts
                               │
                        Preview / Overlay /
                        Plot / Report / Map
```

This is the target conceptual flow.

Canonical V1 uses only the subset defined by `V1_SCOPE.md`.

---

## 4. Primary Data Categories

ChandraMap should distinguish at least the following conceptual data categories:

| Category                          | Meaning                                                                                   |
| --------------------------------- | ----------------------------------------------------------------------------------------- |
| **Source Scientific Data**        | Original observation being localized or registered                                        |
| **Reference Scientific Data**     | Original reference imagery or reference-region data                                       |
| **Scientific Metadata**           | Mission, sensor, scale, projection, footprint, acquisition, spectral, and related context |
| **Derived Image Representation**  | Processed image representation created from an original product                           |
| **Retrieval Data**                | Tiles, global descriptors, index results, candidate rankings                              |
| **Candidate Correspondence Data** | Pre-verification point relationships proposed by a matcher                                |
| **Verified Correspondence Data**  | Geometry-consistent inliers after verification                                            |
| **Transformation Data**           | Initial and final geometric models                                                        |
| **Evaluation Data**               | Residuals, coverage, check-point error, runtime, success/failure evidence                 |
| **Benchmark Metadata**            | Pair identity, benchmark version, configuration, run context                              |
| **Structured Result**             | Authoritative project-level scientific outcome                                            |
| **Artifact**                      | Generated visualization, registered image, report, plot, or other file                    |
| **Failure / Status Data**         | Structured explanation of rejection or processing failure                                 |

---

## 5. Authoritative vs Derived Data

### Authoritative Scientific Inputs

Examples include:

- original Chandrayaan-2 mission products
- original LRO/LROC products
- validated reference/control/check-point information
- authoritative provider metadata

These should remain traceable and preferably immutable.

### Derived Scientific Data

Examples include:

- normalized images
- PCA components
- selected spectral bands
- image pyramids
- tiles
- descriptors
- candidate correspondences
- verified inliers
- transforms
- metrics

Derived scientific data should retain lineage to its source.

### Presentation Artifacts

Examples include:

- match plots
- overlays
- registered previews
- benchmark charts
- dashboards

Presentation artifacts are not the authoritative scientific record.

---

# 6. Source Scientific Data

Primary source-product contexts include:

- Chandrayaan-2 OHRC
- Chandrayaan-2 TMC-2
- Chandrayaan-2 IIRS

Conceptually:

```text
Source Product
      ↓
Source Identity
      +
Sensor Metadata
      +
Raster / Cube Data
      +
Geospatial Context
      +
Provenance
```

The source must not be reduced prematurely to an anonymous numerical array when later processing requires knowledge of its scientific context.

---

## 6.1 Source Identity

Where available, source identity may include concepts such as:

- mission
- instrument
- product identifier
- dataset item identity
- original filename or product reference
- benchmark pair identity

This document does not prescribe exact field names.

The requirement is traceability.

---

# 7. Reference Scientific Data

Reference data may include:

- LRO NAC
- LRO WAC

Potential future or supporting reference data may include:

- Kaguya / SELENE
- DEM / DTM products
- LOLA-derived products

Reference data should preserve, where relevant:

- reference identity
- parent product identity
- footprint
- projection
- GSD
- tile lineage
- pyramid-level lineage
- provider provenance

---

## 7.1 Source vs Reference Identity

Source and reference roles must remain explicit.

Avoid generic semantics such as:

```text
image_a
image_b
```

when directional geometry matters.

Prefer conceptual meaning equivalent to:

```text
Source Coordinates
        ↓
Source → Reference Transform
        ↓
Reference Coordinates
```

The source/reference distinction must survive through:

- matching
- geometry
- registration
- evaluation
- serialization
- visualization

---

# 8. Metadata Flow

Scientific metadata should enter early and remain available to stages that need it.

Potential metadata groups include:

### Identity

- mission
- instrument
- product identifier

### Raster

- width
- height
- band count
- data type
- valid-data mask
- no-data information

### Spatial

- GSD
- footprint
- map projection
- body reference
- geotransform where available

### Acquisition

- acquisition timestamp
- viewing geometry

### Illumination

- available solar/illumination geometry

### Spectral

- band information
- wavelength information
- calibration-related metadata

Do not require fields that the underlying product does not provide.

---

## 8.1 Provider Metadata vs Normalized Metadata

Distinguish:

```text
Provider Metadata
```

from:

```text
Normalized ChandraMap Metadata
```

Conceptually:

```text
Provider Product
      ↓
Provider Metadata Parsing
      ↓
Normalized Metadata
      +
Reference to Original Metadata
```

Normalization should improve consistency of access.

It must not silently rewrite scientific meaning.

---

## 8.2 Missing Metadata

Missing metadata should remain explicitly:

- unknown
- unavailable
- not applicable

unless a value is derived through a documented process.

Never fabricate:

- GSD
- projection
- footprint
- instrument identity
- Sun angle
- coordinates

because a downstream algorithm would prefer them.

---

# 9. Validation Flow

Conceptually:

```text
Raw Input
   ↓
Validation
   ├── Valid
   │     ↓
   │ Validated Scientific Input
   │
   └── Invalid
         ↓
     Structured Failure
```

A validated input should conceptually preserve:

- source/reference role
- product identity
- data array/cube
- valid-data mask
- metadata
- provenance
- validation state

Do not infer an actual class or schema from this description.

---

# 10. Sensor Routing Data

Sensor identification may produce processing-route information.

```text
Validated Input
      ↓
Instrument Identity
      ↓
Processing Route
      │
      ├── OHRC
      ├── TMC-2
      ├── IIRS
      └── Generic / Unsupported
```

The routing decision is scientific processing context.

It does not need to exist as a dedicated object unless implementation requires one.

---

# 11. Derived Image Representations

Preprocessing may produce representations such as:

- normalized imagery
- grayscale imagery
- masked imagery
- contrast-adjusted imagery
- gradient imagery
- edge imagery
- selected spectral band
- PCA-derived representation
- spectral composite
- downsampled reference
- pyramid level

Each important derived representation should remain connected conceptually to:

```text
Original Product
      +
Transformation Method
      +
Configuration
      ↓
Derived Representation
```

Do not allow derived data to become indistinguishable from original mission products.

---

## 11.1 Representation Provenance

Useful provenance may include:

- source product identity
- preprocessing method
- relevant parameters
- scale transformation
- sensor route
- model or algorithm identity where applicable

The exact storage mechanism is implementation-specific.

---

# 12. IIRS Data Flow

IIRS requires explicit hyperspectral handling.

Conceptually:

```text
IIRS Hyperspectral Cube
        ↓
Spectral Metadata
        ↓
Representation Method
        │
        ├── Selected Band
        ├── PCA Component
        ├── Spectral Composite
        └── Structural Representation
        ↓
2D Registration Representation
        ↓
Local Registration Flow
```

No representation should be described as canonical unless repository evidence establishes that choice.

---

## 12.1 IIRS Representation Provenance

Where relevant, preserve:

- source cube identity
- selected band or bands
- wavelength region
- PCA configuration/component
- normalization
- composite method
- structural processing method

Avoid a situation where a derived file such as:

```text
iirs_image.png
```

becomes the only surviving information about how the representation was produced.

---

# 13. Scale and Pyramid Data Flow

Where physical-scale handling exists:

```text
High-Resolution Reference
        ↓
Pyramid Generation
        ↓
Pyramid Levels
        ↓
Level Context
        ├── Parent Product
        ├── Pyramid Level
        ├── Resampling Relationship
        └── Effective GSD
        ↓
Selected Reference Representation
```

Pyramid levels are derived representations.

They are not new scientific observations.

---

## 13.1 Physical Scale Semantics

Keep separate concepts for:

- raster dimensions
- physical GSD
- resampling factor
- pyramid level
- geometric scale factor

Do not collapse these into one ambiguous notion of `scale`.

---

## 13.2 Upsampling

If upsampling is used:

```text
Original Product
      ↓
Interpolation
      ↓
Resampled Representation
```

the original physical GSD must not be rewritten as though the sensor captured finer physical information.

Resampling modifies representation.

It does not improve original sensor resolution.

---

# 14. Metadata-Constrained Search Data

When reliable location information exists:

```text
Source Metadata
      ↓
Source Footprint / Approximate Region
      ↓
Reference Spatial Query
      ↓
Candidate Reference Product(s)
      ↓
Candidate Region(s)
```

Candidate reference regions should retain:

- parent reference identity
- overlap relationship
- scale information
- geographic context where available

Metadata-first search preserves valuable scientific context and avoids unnecessary global retrieval.

---

# 15. Global Retrieval Data Flow

When metadata is insufficient:

```text
Source Representation
      ↓
Global Descriptor
      ↓
Vector Query
      ↓
Ranked Candidate IDs
      ↓
Candidate Metadata Lookup
      ↓
Candidate Reference Regions
      ↓
Local Registration
```

Important distinctions:

- global descriptor = derived retrieval representation
- ranked candidate list = retrieval result
- retrieval result ≠ verified registration

---

# 16. Offline Retrieval Data

A retrieval database may require a separate offline preparation flow.

```text
Reference Product
      ↓
Reference Tile Generation
      ↓
Optional Pyramid Level
      ↓
Global Descriptor
      ↓
Vector Index
      +
Descriptor ↔ Tile Mapping
      +
Tile Metadata
```

The retrieval index must remain traceable to the scientific reference data used to create it.

---

## 16.1 Tile Lineage

A reference tile should retain conceptually:

- parent product identity
- tile identity
- tile footprint
- scale/pyramid level where relevant
- derivation configuration

The tile should not become detached from its parent scientific product.

---

## 16.2 FAISS Data Boundary

If FAISS is used:

### Input

```text
Numeric Descriptor Vectors
```

### Output

Conceptually:

```text
Candidate Vector IDs
+
Distances / Similarity Information
+
Ranking
```

FAISS does not carry the complete scientific meaning of those vectors by itself.

Therefore:

```text
FAISS Candidate ID
      ↓
Metadata / Tile Mapping
      ↓
Reference Tile / Product
```

is required conceptually.

---

## 16.3 Retrieval Index Provenance

A retrieval index should ideally remain traceable to:

- reference dataset identity/version
- tile-generation configuration
- pyramid configuration
- descriptor method
- model/checkpoint where applicable
- index configuration

Exact fields are implementation-specific.

---

# 17. Sparse Feature Data Flow

Classical sparse-feature flow may be:

```text
Image Representation
      ↓
Feature Extraction
      ↓
Keypoint Coordinates
      +
Descriptors
      +
Optional Feature Scores
```

for both source and reference imagery.

---

## 17.1 Keypoint Coordinate Semantics

Every keypoint set must have a known coordinate convention.

Potential ambiguity includes:

```text
(x, y)
```

versus:

```text
(row, column)
```

Coordinate ordering should not become implicit at module or serialization boundaries.

---

## 17.2 Descriptor Lineage

Descriptors must remain associated with:

- image identity
- keypoint ordering
- descriptor method
- source representation
- relevant configuration

Do not independently reorder descriptors without preserving alignment with their keypoints.

---

# 18. ALIKED + LightGlue Data Flow

Conceptually:

```text
Source Representation
      ↓
ALIKED
      ↓
Source Local Features

Reference Representation
      ↓
ALIKED
      ↓
Reference Local Features

Source + Reference Feature Sets
      ↓
LightGlue
      ↓
Candidate Correspondences
```

ALIKED and LightGlue have different roles.

Their data lineage should remain conceptually distinct.

---

# 19. LoFTR Data Flow

LoFTR is detector-free.

Conceptually:

```text
Source Representation
      +
Reference Representation
      ↓
LoFTR
      ↓
Candidate Correspondence Coordinates
      +
Optional Match Scores
```

Do not force its internal data model into a traditional keypoint-descriptor architecture if that would misrepresent the method.

Downstream ChandraMap processing may still normalize its output into a common candidate-correspondence representation.

---

# 20. Candidate Correspondence Data

Candidate correspondence data is a critical boundary.

Conceptually, each candidate relates:

```text
Source Coordinate
        ↔
Reference Coordinate
```

Potential associated information may include:

- matcher score
- descriptor distance
- source feature identity
- reference feature identity
- matcher/method information

Candidate correspondences remain **unverified** until geometry evaluates them.

---

## 20.1 Candidate Match Set Context

A candidate set should conceptually preserve:

- source identity
- reference identity
- coordinate convention
- matcher identity
- candidate count
- optional scores
- configuration/provenance

Do not detach coordinates from the images or representations they belong to.

---

# 21. Geometric Verification Data Flow

Conceptually:

```text
Candidate Correspondence Set
        ↓
RANSAC / Geometry
        ↓
Initial Transform
        +
Inlier Classification
        +
Verified Inlier Set
        +
Rejected Outlier Set
```

If an inlier mask is used, its ordering must remain aligned with the original candidate set.

---

## 21.1 Correspondence Lineage

It should remain possible to determine conceptually:

```text
Candidate Match
      ↓
Verified Inlier
```

or:

```text
Candidate Match
      ↓
Rejected Outlier
```

This lineage supports:

- debugging
- visualization
- benchmark analysis
- failure analysis

---

# 22. Verified Inlier Data

Verified inliers should retain conceptually:

- source coordinates
- reference coordinates
- original candidate relationship
- geometric status
- optional residual
- original matcher evidence

Verified inliers are model-consistent correspondences.

They are not automatically independent ground truth.

---

# 23. Initial Transform Data

The initial geometric model should preserve enough context to identify:

- transformation model type
- transformation direction
- parameter/matrix representation
- source coordinate domain
- reference/target coordinate domain
- estimation status
- supporting verified inliers

A bare matrix without direction or coordinate meaning is not sufficiently self-describing for safe cross-component use.

---

## 23.1 Transform Direction

Every transform crossing an architectural boundary must make clear whether it represents:

```text
source → reference
```

or:

```text
reference → source
```

This rule applies to:

- estimation
- registration
- evaluation
- serialization
- visualization

---

## 23.2 Initial vs Final Transform

When refinement exists, distinguish:

```text
Initial Transform
```

from:

```text
Final Transform
```

Conceptually:

```text
Verified Inliers
      ↓
Initial Transform
      ↓
Tie-Point Refinement
      ↓
Refined Coordinates
      ↓
Final Transform Refit
      ↓
Final Transform
```

Do not silently overwrite this lineage when it is needed for scientific analysis or debugging.

---

# 24. Spatial Coverage Data

Coverage computation may consume:

```text
Verified Inlier Coordinates
        +
Relevant Image / Overlap Geometry
```

and produce one or more coverage measures.

Potential conceptual measures include:

- occupied grid cells
- grid coverage
- convex-hull coverage

Exact definitions belong in metrics documentation.

---

# 25. Residual Data

Residual analysis may produce:

- residual magnitude per correspondence
- 2D residual vector
- aggregate residual statistics
- spatial residual distributions

Every residual must preserve its units and coordinate domain.

Possible contexts include:

- source-image pixels
- reference-image pixels
- projected units

Avoid unitless residual values.

---

# 26. Early Quality Data

After geometric verification, the pipeline may combine:

```text
Verified Inliers
+
Spatial Coverage
+
Residuals
+
Transform Validity
```

to create an early quality assessment.

Conceptual outcomes include:

- continue
- refine
- reject

Exact thresholds belong in benchmark/configuration definitions.

---

# 27. Tie-Point Refinement Data Flow

Where enabled:

```text
Verified Inlier Pair
      ↓
Local Source Context
      +
Local Reference Context
      ↓
Refinement Method
      ↓
Refined Source Coordinate
      +
Refined Reference Coordinate
      +
Optional Refinement Evidence
```

The refined coordinates represent new tie-point estimates.

They should not be confused with the original matcher coordinates.

---

## 27.1 Tie-Point Lineage

Conceptually preserve:

```text
Original Candidate Coordinates
        ↓
Verified Inlier Coordinates
        ↓
Refined Tie-Point Coordinates
```

This allows later analysis to determine whether refinement improved or degraded the correspondence.

---

# 28. Final Transform Refit

When tie-point coordinates change:

```text
Refined Tie Points
      ↓
Transform Estimation
      ↓
Final Transform
```

The final transform must correspond to the refined coordinates.

Do not silently continue using the original RANSAC transform after tie-point refinement.

---

# 29. Registration / Warp Data Flow

Conceptually:

```text
Final Transform
      +
Source Representation / Product
      +
Reference Target Geometry
      ↓
Warp / Registration
      ↓
Registered Image
      +
Registration Geometry
```

The registered image is derived data.

It is not an original mission product.

---

## 29.1 Registration Provenance

Where relevant, registered output should remain traceable to:

- source product
- selected reference product/region
- final transform
- transform direction
- interpolation method
- output geometry
- processing configuration

---

# 30. Fit Data vs Evaluation Data

This boundary is scientifically critical.

### Fit Data

```text
Fit Tie Points
      ↓
Transformation Estimation
```

### Evaluation Data

```text
Independent Check Points
      ↓
Final Transform Evaluation
```

Check points must not accidentally enter fitting if they are intended to provide independent evaluation.

---

## 30.1 Fit/Evaluation Leakage

Avoid:

```text
All Points
   ↓
Fit Transform
   ↓
Evaluate Same Points
   ↓
Call Result Independent Accuracy
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

where independent truth exists.

---

# 31. Ground-Truth and Check-Point Data

Ground-truth or check-point information may come from:

- challenge-provided correspondences
- independently validated control/check points
- trusted geospatial reference products

Ground truth must remain separate from matcher-generated correspondence data.

A model must not generate its own truth and then evaluate itself against that same output.

---

## 31.1 Ground-Truth Provenance

Where available, preserve:

- source of truth
- annotation/reference method
- coordinate convention
- units
- version/revision

Exact schemas belong elsewhere.

---

# 32. Evaluation Data Flow

Conceptually:

```text
Final Transform
      +
Independent Evaluation Data
      +
Coordinate Context
      +
Unit Context
      ↓
Metric Computation
      ↓
Evaluation Metrics
```

Potential outputs include:

- check-point RMSE
- pixel error
- residual statistics
- inlier statistics
- coverage
- runtime
- registration status
- retrieval Recall@K where applicable

Full equations belong in metrics documentation.

---

## 32.1 Error Unit Flow

If a metric is computed in:

```text
source-image pixels
```

that unit must remain attached to the result.

If converted to:

```text
metres
```

the conversion assumptions and spatial context must remain traceable.

Avoid naked numbers.

---

## 32.2 Pixel-to-Ground Conversion

Conceptually:

```text
Pixel Error
      +
Valid Spatial Context
      ↓
Ground-Distance Conversion
      ↓
Ground Error
```

Required context may include:

- valid GSD
- projection/geometry
- correct coordinate domain

Do not equate:

```text
0.5 px
```

with:

```text
0.5 m
```

without a valid conversion.

---

# 33. Retrieval Metric Data

Retrieval evaluation uses a different data family from local registration evaluation.

Conceptually:

```text
Query Identity
      +
Known Correct Reference Region
      +
Ranked Candidate List
      ↓
Recall@K
```

Retrieval ground truth and local registration check points are distinct.

Do not mix:

- Recall@K
- RMSE
- inlier ratio

as though they measure the same behavior.

---

# 34. Runtime Data

Runtime measurements should preserve scope.

Potential conceptual timing categories include:

- preprocessing duration
- retrieval duration
- local matching duration
- geometric verification duration
- refinement duration
- registration duration
- full pipeline duration

Do not compare:

```text
matching-only runtime
```

against:

```text
full end-to-end runtime
```

without clearly identifying the difference.

---

# 35. Quality Decision Data Flow

Final quality evidence may conceptually include:

- transform validity
- candidate match count
- verified inlier count
- inlier ratio
- spatial coverage
- residuals
- independent RMSE
- retrieval ambiguity
- runtime/status

Conceptual output:

```text
Quality Decision
    ├── Accept
    ├── Refine
    └── Reject
```

Do not invent a confidence equation.

---

## 35.1 Confidence Data

Matcher confidence and final registration confidence are different concepts.

If ChandraMap later defines registration confidence, it should come from a documented and calibrated method.

Do not generate arbitrary values such as:

```text
92% confidence
```

from ad hoc metric combinations.

---

# 36. Structured Registration Result

The pipeline should conceptually converge on one authoritative scientific result representation.

Potential information categories include:

### Identity

- run identity
- source identity
- reference identity
- benchmark pair identity

### Status

- accepted
- rejected
- failed
- refinement required

### Processing Context

- benchmark version
- configuration
- preprocessing
- matcher
- geometric model

### Correspondence Evidence

- candidate information
- verified inlier information
- outlier information where retained

### Geometry

- initial transform
- final transform
- transform direction

### Evaluation

- residuals
- spatial coverage
- RMSE/error
- units
- runtime

### Retrieval

Where applicable:

- ranked candidates
- selected candidate
- retrieval evidence

### Failure

Where applicable:

- failure category
- explanatory context

### Artifact References

Where applicable:

- registered image
- match visualization
- overlay
- report

These categories are conceptual only.

Do not infer concrete field names or schemas.

---

# 37. One Authoritative Result Principle

Avoid independent scientific result definitions in:

- scientific core
- benchmark tooling
- API
- frontend

Prefer:

```text
Scientific Registration Result
        ↓
        ├── Benchmark Serialization
        ├── API Serialization
        └── Frontend Presentation
```

Adapters may transform representation.

They must preserve scientific meaning.

---

# 38. Result vs Artifact

## Result

Structured scientific information.

Examples conceptually include:

- transformation
- RMSE
- inlier count
- coverage
- status

## Artifact

Generated file or visualization.

Examples include:

- PNG match plot
- registered GeoTIFF
- residual chart
- HTML/PDF report

The artifact must not become the only surviving location of the scientific result.

---

# 39. Artifact Data Flow

Conceptually:

```text
Registration Result
      ↓
Artifact Generation
      ├── Match Visualization
      ├── Inlier / Outlier Visualization
      ├── Residual Plot
      ├── Registered Image
      ├── Overlay
      └── Report
```

Artifacts consume scientific results.

They should not independently recompute authoritative metrics.

---

# 40. Backend / API Data Flow

If a backend exists:

```text
Request
      ↓
Boundary Validation
      ↓
Scientific / Domain Input
      ↓
Core Pipeline
      ↓
Registration Result
      ↓
Serialization Adapter
      ↓
API Response / Job Record
```

The service layer may translate formats.

It must not redefine scientific semantics.

---

# 41. UI / Visualization Data Flow

Conceptually:

```text
Scientific Result
      ↓
Application / API Adapter
      ↓
Presentation Data
      ↓
Frontend Visualization
```

The frontend may receive:

- measured metrics
- status
- correspondence visualization data
- residual information
- artifact references

It must not invent:

- RMSE
- inlier status
- transform quality
- scientific confidence

---

# 42. CLI Data Flow

Conceptually:

```text
CLI Arguments / Configuration
        ↓
Validated Pipeline Configuration
        ↓
Scientific Pipeline
        ↓
Structured Result
        ↓
Console Summary / Saved Outputs
```

Do not make console strings the only representation of scientific results.

---

# 43. Benchmark Data Flow

Conceptually:

```text
Benchmark Manifest
        ↓
Pair Definition
        +
Benchmark Configuration
        ↓
Pipeline Execution
        ↓
Registration Result
        ↓
Per-Pair Benchmark Record
        ↓
Aggregate Comparison
        ↓
Benchmark Report
```

Per-pair results should remain auditable beneath aggregated values where practical.

---

## 43.1 Benchmark Run Context

A benchmark result should ideally remain linked to:

- benchmark version
- pair identity
- dataset identity
- effective configuration
- code revision where available
- model/checkpoint where relevant
- metric definition/version where applicable

Do not invent an exact run-ID format.

---

## 43.2 Failed Runs in Aggregation

Failed and rejected runs must not silently disappear from benchmark summaries.

Aggregation should conceptually preserve:

- successful cases
- rejected cases
- failed cases
- relevant failure statistics

Do not calculate averages only over successful cases while hiding failures unless the metric definition explicitly states that behavior.

---

# 44. V1 Data Flow

Canonical V1 remains intentionally simple.

```text
Known Source 2D Image
        +
Known Reference 2D Image
        ↓
Validated Pair
        ↓
Minimal Preprocessing
        ↓
SIFT / Canonical RootSIFT Configuration
        ↓
Candidate Matches
        ↓
RANSAC
        ↓
Verified Inliers
        ↓
Affine / Homography
        ↓
Registration
        ↓
Baseline Evaluation
        ↓
V1 Result
```

Canonical V1 normally excludes:

- global descriptors
- FAISS
- Top-K global retrieval
- full native IIRS hyperspectral processing
- advanced learned matcher data
- DEM data
- advanced local refinement

unless `V1_SCOPE.md` explicitly defines otherwise.

---

# 45. V2 Data Flow

V2 may introduce additional scientific context and derived data such as:

- sensor route
- sensor-specific representation metadata
- effective GSD
- pyramid-level information
- IIRS-derived 2D representation
- structural representation metadata

Conceptually:

```text
Scientific Product
      ↓
Sensor / Product Metadata
      ↓
Sensor-Aware Representation
      ↓
Scale-Aware Representation
      ↓
Local Registration
      ↓
V2 Evaluation
```

This describes target V2 scope, not current implementation status.

---

# 46. V3 Data Flow

V3 may introduce:

- global descriptors
- reference tile identities
- vector index results
- Top-K reference candidates
- learned local features
- LightGlue match information
- LoFTR correspondence output
- remote-sensing matcher output

These should converge into the common downstream geometry flow:

```text
Matcher-Specific Output
        ↓
Normalized Candidate Correspondence Data
        ↓
Geometric Verification
        ↓
Verified Inliers
        ↓
Registration
        ↓
Evaluation
```

---

# 47. V4 Data Flow

V4 may introduce additional data such as:

- DEM/terrain context
- local transformation information
- refined tie-point uncertainty
- calibrated confidence
- matcher-selection metadata
- failure classification
- piecewise geometry
- advanced IIRS representation context

Do not treat these as current data structures unless repository evidence confirms implementation.

---

# 48. Conceptual Version Data-Flow Comparison

This table describes benchmark scope, not verified implementation status.

| Data Concept                        | V1                   | V2                        | V3                          | V4              |
| ----------------------------------- | -------------------- | ------------------------- | --------------------------- | --------------- |
| 2D source/reference imagery         | Core                 | Core                      | Core                        | Core            |
| Sensor metadata                     | Basic/preserved      | Core                      | Core                        | Core            |
| Effective GSD information           | Limited              | Core                      | Core                        | Advanced        |
| Reference pyramid context           | No / minimal         | Core                      | Supported                   | Advanced        |
| IIRS-derived 2D metadata            | Canonically excluded | Research / core candidate | Supported research          | Advanced        |
| Global descriptor                   | No                   | Optional research         | Core when retrieval is used | Core / advanced |
| Top-K retrieval candidates          | No                   | Optional research         | Core when required          | Core            |
| Learned matcher evidence            | No                   | Optional research         | Core comparison             | Advanced        |
| Verified inliers                    | Core                 | Core                      | Core                        | Core            |
| Refined tie points                  | Normally no          | Optional                  | Supported research          | Advanced        |
| DEM / terrain context               | No                   | No / research             | Experimental                | Research        |
| Uncertainty / calibrated confidence | No                   | Minimal / none            | Optional                    | Advanced        |

---

# 49. Coordinate-System Flow

Coordinate semantics are a high-risk data boundary.

At minimum, distinguish image spaces:

```text
Source Image Pixels
        ↓
Source → Reference Transform
        ↓
Reference Image Pixels
```

Where geospatial mapping exists:

```text
Reference Image Pixels
        ↓
Geotransform / Projection
        ↓
Projected Lunar Coordinates
        ↓
Lunar Geographic Coordinates
```

Do not collapse every coordinate domain into generic `x` and `y`.

---

## 49.1 Coordinate Domain Labels

Conceptual coordinate domains may include:

- source image pixel space
- reference image pixel space
- projected lunar map space
- lunar geographic space

These are concepts, not prescribed variable names.

The important requirement is explicit domain identity.

---

## 49.2 `(x, y)` vs `(row, column)`

A common interpretation is:

```text
x = column
y = row
```

while array indexing commonly uses:

```text
array[row, column]
```

The actual project convention must be verified.

Never silently convert between them across data-flow boundaries.

---

# 50. Unit Flow

Scientific measurements should carry clear units.

Examples:

| Quantity       | Example Unit         |
| -------------- | -------------------- |
| GSD            | `m/px`               |
| Image residual | `px`                 |
| Ground error   | `m`                  |
| Angle          | `°` or `rad`         |
| Runtime        | documented time unit |

Do not pass unitless numerical values across public or scientific boundaries when ambiguity could affect interpretation.

---

# 51. Transform and Coordinate Safety

A transformation should not move between components as an unlabeled matrix if downstream consumers cannot determine:

- model type
- direction
- input coordinate domain
- output coordinate domain

This is especially important at:

- evaluation boundaries
- registration boundaries
- API serialization
- artifact generation
- frontend visualization

---

# 52. Mask and Valid-Region Flow

Scientific rasters may contain:

- no-data pixels
- masked regions
- invalid samples

Conceptually:

```text
Input Raster
    +
Valid Mask
      ↓
Preprocessing
      ↓
Derived Representation
    +
Updated Valid Mask
```

Do not allow no-data regions to become ordinary matching texture silently.

---

# 53. Configuration Data Flow

Pipeline configuration should enter processing explicitly.

Conceptually:

```text
Benchmark / Runtime Configuration
        ├── Preprocessing Configuration
        ├── Scale Configuration
        ├── Retrieval Configuration
        ├── Matcher Configuration
        ├── Geometry Configuration
        ├── Refinement Configuration
        └── Evaluation Configuration
                 ↓
              Pipeline
```

Canonical benchmark runs should not depend on hidden manual parameter values.

---

## 53.1 Effective Configuration Snapshot

For reproducible experiments, the effective configuration should ideally be recordable with the result.

The exact serialization format is implementation-specific.

---

# 54. Model and Checkpoint Data Flow

When learned methods are used:

```text
Model Definition
      +
Checkpoint
      +
Required Preprocessing
      ↓
Feature / Matching Component
      ↓
Derived Features / Correspondences
```

Where relevant, preserve:

- model identity
- checkpoint identity
- relevant version/hash
- preprocessing assumptions
- configuration

Do not reduce experiment identity to only a generic label such as:

```text
LightGlue
```

when materially different feature extractors or checkpoints were used.

---

# 55. Random Seed Data

Where randomness affects execution:

```text
Run Configuration
      ↓
Random Seed
      ↓
Randomized Stage
      ↓
Result Context
```

Potential examples include:

- RANSAC
- sampling
- synthetic augmentation
- model training

Do not claim determinism when the underlying method or environment does not guarantee it.

---

# 56. Provenance Flow

Provenance should survive key derivation stages.

```text
Mission Product
      ↓
Product Identity
      ↓
Derived Representation
      ↓
Matching Configuration
      ↓
Correspondences
      ↓
Transformation
      ↓
Evaluation
      ↓
Registration Result
      ↓
Artifact
```

A downstream output should remain traceable to its scientific inputs where practical.

---

## 56.1 Provenance Through Caching

If derived data is cached, cache identity should preserve or reference:

- parent product
- processing configuration
- method/model identity
- scale/pyramid level
- relevant software/version context where required

Caching must not break scientific lineage.

---

# 57. Data Retention Classes

Not every intermediate object needs permanent storage.

Useful conceptual categories are:

## Source

Original scientific input.

Examples:

- mission product
- trusted ground truth

## Ephemeral

Short-lived processing data.

Examples:

- temporary normalized array
- local image patch
- short-lived model tensor

## Cacheable

Expensive derived data useful for reuse.

Examples:

- reference tiles
- pyramid levels
- global descriptors
- retrieval index
- reusable model features

## Persistent Scientific Result

Data needed for reproducibility or reporting.

Examples:

- final transformation
- registration result
- benchmark metric record
- artifact manifest

This classification does not prescribe a storage backend.

---

# 58. Original-Data Immutability

Original mission data should preferably be treated as immutable input.

Prefer:

```text
Original Mission Product
        ↓
Derived Representation
```

over:

```text
Original Mission Product
        ↓
Modified In Place
```

where practical.

This supports:

- reproducibility
- provenance
- debugging
- safe reprocessing

---

# 59. Serialization Boundaries

Data may require serialization when crossing:

- process boundaries
- API boundaries
- benchmark persistence
- result storage
- frontend boundaries

At these boundaries, scientific semantics must survive serialization.

For example:

```text
Transform Matrix
```

may be insufficient without:

- model type
- direction
- coordinate domains

Likewise:

```text
RMSE = value
```

may be insufficient without:

- units
- evaluation population
- coordinate context

---

# 60. API Contract Flow

If explicit contracts exist:

```text
Scientific Domain Result
        ↓
Serialization Adapter
        ↓
API Contract
        ↓
Client
```

Do not expose internal numerical structures as permanent external schemas accidentally.

Public contract design should be deliberate.

---

# 61. Frontend View-Model Flow

Frontend-specific data may simplify scientific results for display.

Conceptually:

```text
Registration Result
      ↓
Presentation Adapter
      ↓
Frontend View Data
```

Possible presentation transformations include:

- point arrays for plotting
- formatted metric labels
- artifact URLs
- status labels

Presentation transformation must not alter authoritative scientific values.

---

# 62. Benchmark Aggregation Flow

Conceptually:

```text
Individual Registration Results
        ↓
Per-Pair Metric Records
        ↓
Aggregation
        ↓
Version Comparison
        ↓
Benchmark Report
```

Aggregate statistics should remain auditable against their underlying per-pair results.

---

# 63. Ablation Data Flow

Ablations should preserve common data flow while changing a controlled component.

Conceptually:

```text
Same Pair
+
Same Evaluation
+
Same Geometry Policy
        ↓
Matcher A vs Matcher B
```

when the experiment is intended to isolate matcher choice.

Do not change multiple unrelated stages and attribute the result to only one component.

---

# 64. Test Data Flow

Tests should use controlled inputs.

### Geometry Test

```text
Known Source Points
      +
Known Transform
      ↓
Geometry / Transform Logic
      ↓
Expected Reference Points
```

### Metric Test

```text
Known Residuals
      ↓
Metric Calculation
      ↓
Expected Metric
```

### Integration Test

```text
Compact Source / Reference Pair
      ↓
Pipeline
      ↓
Structured Result
```

Full mission datasets should not be necessary for every test.

---

# 65. Failure Data Flow

Failure should be treated as structured information.

```text
Pipeline Stage
      ↓
Failure Condition
      ↓
Failure Category
      +
Context
      ↓
Pipeline Termination / Rejection
      ↓
Structured Result
```

Potential failure categories include:

- validation failure
- unsupported input
- preprocessing failure
- insufficient features
- insufficient candidate matches
- geometric verification failure
- degenerate transform
- retrieval failure
- quality rejection
- evaluation failure
- unexpected software error

---

## 65.1 Scientific Failure vs Software Error

Distinguish:

### Expected Scientific Failure

Example:

> insufficient verified inliers

### Unexpected Software Error

Example:

> unhandled parser exception

Do not reduce both to an ambiguous:

```text
failed = true
```

if the architecture supports richer meaning.

---

# 66. Security and Trust Boundaries

External data should be considered untrusted until validated.

Potential untrusted inputs include:

- uploaded images
- downloaded mission rasters
- archives
- scientific metadata files
- model checkpoints
- configuration files
- serialized numerical objects

Conceptually:

```text
External Data
      ↓
Validation / Safe Parsing
      ↓
Internal Scientific Representation
```

Detailed security requirements belong in security documentation.

---

# 67. Large-Data Flow

Planetary imagery may be too large for naïve full-memory processing.

Potential strategies may include:

- windowed access
- tiling
- chunking
- memory mapping
- pyramids
- cache reuse

This document does not prescribe which implementation currently exists.

Do not assume the complete lunar reference dataset fits in memory.

---

## 67.1 Data Copy Principle

Avoid unnecessary copies of large arrays where practical.

However:

> Correctness, ownership clarity, and scientific validity take priority over minimizing memory copies.

Optimization should not make data lineage ambiguous.

---

# 68. Data-Flow Responsibility Table

| From                       | Data                                 | To                                     | Purpose                               |
| -------------------------- | ------------------------------------ | -------------------------------------- | ------------------------------------- |
| Dataset/input layer        | scientific data + metadata           | validation                             | establish valid scientific input      |
| Validation                 | validated product + metadata         | preprocessing / sensor handling        | begin scientific processing           |
| Metadata                   | sensor, GSD, footprint, projection   | routing / scale / search               | provide scientific context            |
| Preprocessing              | derived representation + provenance  | matching                               | prepare registration input            |
| Scale handling             | selected scale representation        | matching / retrieval                   | make comparison physically meaningful |
| Metadata search            | candidate regions                    | local matching                         | restrict reference search             |
| Global retrieval           | ranked candidate regions             | local matching                         | identify possible reference location  |
| Feature extraction         | keypoints + descriptors              | matcher                                | support sparse correspondence         |
| Matcher                    | candidate correspondences            | geometry                               | generate pre-verification matches     |
| Geometry                   | verified inliers + initial transform | refinement / registration / evaluation | establish geometric support           |
| Coverage/residual analysis | quality evidence                     | decision/refinement                    | reject or continue                    |
| Refinement                 | refined tie points                   | transform estimator                    | improve coordinate localization       |
| Transform estimator        | final transform                      | registration                           | align source/reference geometry       |
| Registration               | registered output                    | evaluation / artifacts                 | produce aligned representation        |
| Independent evaluation     | metrics                              | quality decision                       | assess final registration             |
| Pipeline                   | structured result                    | benchmark / API / CLI / UI             | downstream consumption                |

---

# 69. Conceptual Data-Object Reference

| Conceptual Data Object | Meaning                                         | Classification                  |
| ---------------------- | ----------------------------------------------- | ------------------------------- |
| Source Product         | Original source observation                     | Authoritative input             |
| Reference Product      | Original reference observation                  | Authoritative input             |
| Provider Metadata      | Original product context                        | Authoritative input             |
| Normalized Metadata    | Standardized project representation of metadata | Normalized scientific context   |
| Derived Representation | Processed registration-ready imagery            | Derived                         |
| Reference Tile         | Spatial subset of reference product             | Derived                         |
| Global Descriptor      | Retrieval vector                                | Derived                         |
| Candidate Match Set    | Pre-verification correspondences                | Derived                         |
| Verified Inlier Set    | Geometry-consistent correspondence subset       | Derived                         |
| Initial Transform      | First verified geometric model                  | Derived                         |
| Refined Tie Points     | Improved correspondence coordinates             | Derived                         |
| Final Transform        | Final registration geometry                     | Derived scientific result       |
| Evaluation Metrics     | Measured quality evidence                       | Derived scientific result       |
| Registration Result    | Authoritative pipeline outcome                  | Project-level scientific result |
| Artifact               | Visualization/file derived from result          | Derived output                  |

---

# 70. Current vs Target Data Flow

This document defines the target conceptual data flow.

Use the following meanings when implementation status is documented:

### Current

Verified in current code/tests/configuration.

### Experimental

Research/prototype path.

### Target

Approved architecture not necessarily implemented.

### Planned

Future capability.

Examples of later-version target data paths include:

- sensor-aware IIRS representation
- multi-scale reference pyramids
- global descriptors
- vector retrieval
- learned correspondences
- refined tie points
- DEM-aware geometry
- uncertainty data

Their description here must not be interpreted as proof of current implementation.

---

# 71. Data-Flow Anti-Patterns

## 71.1 Anonymous Array Flow

Avoid passing scientific imagery without identity or metadata when that context matters.

---

## 71.2 Unlabeled Coordinate Flow

Avoid passing:

```text
(x, y)
```

without knowing:

- coordinate convention
- coordinate domain
- source/reference role

---

## 71.3 Bare Transform Matrix

Avoid passing only a matrix when consumers need:

- model type
- direction
- coordinate domains

---

## 71.4 Unitless Metrics

Avoid:

```text
RMSE = 0.7
```

without units and evaluation context.

---

## 71.5 Provenance Loss

Do not let a derived representation lose its parent product identity.

---

## 71.6 Candidate-as-Truth

Do not treat matcher output as verified correspondence.

---

## 71.7 Inlier-as-Ground-Truth

Do not treat RANSAC inliers as independent ground truth automatically.

---

## 71.8 Fit/Evaluation Leakage

Do not allow check points intended for independent evaluation to participate in fitting.

---

## 71.9 Stale Transform

Do not refine tie-point coordinates and then keep using the old pre-refinement transformation silently.

---

## 71.10 Frontend Metric Recreation

Do not independently reimplement scientific metrics in presentation code.

---

## 71.11 Artifact-as-Result

Do not allow a plot or preview image to become the only surviving scientific record.

---

## 71.12 Silent Failure Loss

Do not remove failed/rejected benchmark cases from aggregate results without explicit metric semantics.

---

## 71.13 Retrieval / Registration Confusion

A retrieval ranking is not proof of precise local registration.

---

## 71.14 Index-as-Dataset

A FAISS/vector index is generated retrieval data.

It is not the authoritative reference dataset.

---

## 71.15 Resampled-as-Original

Do not represent upsampled, downsampled, PCA-derived, or otherwise transformed imagery as an original mission acquisition.

---

# 72. Data-Flow Change Rules

Material data-flow changes may affect:

- scientific interpretation
- schemas
- APIs
- benchmarks
- reproducibility
- serialization
- visualization
- historical result compatibility
- tests

Changes requiring particular care include:

- coordinate-order changes
- transform-direction changes
- source/reference role changes
- metric-unit changes
- result semantics changes
- fit/check-point handling changes
- benchmark ground-truth changes
- retrieval candidate identity changes

Such changes should be documented deliberately.

---

## 72.1 Potentially Breaking Data-Flow Changes

A change may be breaking when it alters:

- public result structure
- transform orientation
- coordinate conventions
- metric units
- serialization meaning
- configuration semantics
- benchmark identity
- status semantics

Where relevant, update appropriate architecture, benchmark, changelog, and migration documentation.

Do not invent a release policy here.

---

# 73. Relationship with Other Documents

## `SYSTEM_OVERVIEW.md`

Defines:

> Which major system components exist?

This document defines:

> What information moves between them?

---

## `PIPELINE.md`

Defines:

> When does each processing stage execute?

This document defines:

> What does that stage receive and produce?

Example:

```text
PIPELINE:
Candidate Matching → RANSAC

DATA FLOW:
Candidate Correspondence Set
    ↓
Geometric Verification
    ↓
Verified Inliers + Initial Transform
```

---

## `MODULE_MAP.md`

Defines:

> Which repository area owns each responsibility?

This document does not assign unverified implementation paths.

---

## `../context/DATASETS.md`

Defines:

> What scientific products and metadata mean.

This document defines:

> How those products and metadata move through processing.

---

## `../context/TERMINOLOGY.md`

Provides canonical terminology such as:

- source image
- reference image
- candidate match
- verified inlier
- outlier
- tie point
- check point
- transform
- registration result
- global descriptor
- retrieval candidate
- artifact

---

## `../context/V1_SCOPE.md`

Defines the authoritative canonical V1 boundary.

The V1 data flow here must remain consistent with that scope.

---

## Metrics Documentation

Defines exact:

- formulas
- aggregation
- threshold semantics
- edge-case handling

This document only describes metric inputs, outputs, units, and movement.

---

# 74. Key Data-Flow Rules for AI Agents

1. Never treat scientific imagery as context-free arrays when metadata matters.

2. Preserve source/reference identity throughout processing.

3. Preserve mission, instrument, and product provenance where available.

4. Distinguish authoritative scientific inputs from derived representations.

5. Do not present PCA, edge, gradient, tiled, or resampled data as original mission products.

6. IIRS cube-to-2D conversion must retain representation provenance.

7. Distinguish image dimensions from physical GSD.

8. Preserve original physical scale meaning after resampling.

9. Reference pyramid levels must remain linked to their parent product.

10. Reference tiles must retain parent-product and spatial identity.

11. Global descriptors are derived retrieval data.

12. FAISS consumes/searches vectors; it does not understand raw scientific imagery directly.

13. FAISS candidate IDs must map back to reference products or tiles.

14. Retrieval results are not registration results.

15. Local matcher output is candidate correspondence data.

16. Candidate matches must preserve source/reference coordinate pairing.

17. Coordinate order must be explicit.

18. Source and reference pixel spaces must remain distinguishable.

19. Matcher confidence is not geometric verification.

20. RANSAC consumes candidate correspondences and produces model-consistent inliers/outliers plus initial geometry.

21. Preserve candidate → inlier/outlier lineage where useful.

22. RANSAC inliers are not automatically ground truth.

23. Transform data must preserve model and direction.

24. Distinguish initial transform from final transform.

25. Sub-pixel refinement produces updated tie-point coordinates.

26. Refit the transformation after tie-point refinement.

27. Never silently keep using a stale pre-refinement transform.

28. Registered imagery is derived data.

29. Warping does not prove registration correctness.

30. Keep fit points separate from independent check points.

31. Do not leak check-point truth into fitting.

32. Ground truth must remain separate from matcher-generated data.

33. Evaluation metrics must preserve units.

34. Source-pixel error and ground error are different quantities.

35. Ground-distance conversion requires valid physical/geospatial context.

36. Retrieval Recall@K and registration RMSE measure different tasks.

37. Runtime values must identify their timing scope.

38. Quality decisions must consume scientific evidence.

39. Do not invent arbitrary confidence percentages.

40. Preserve failure category and context.

41. Distinguish expected scientific failure from software error.

42. Failed/rejected benchmark cases must remain visible in aggregate interpretation.

43. Prefer one authoritative scientific result consumed by benchmarks, APIs, CLI, and UI.

44. UI presentation may format scientific values but must not redefine them.

45. Artifacts must not become the only record of numerical results.

46. Preserve effective configuration for reproducible runs where practical.

47. Preserve model/checkpoint identity where learned methods are used.

48. Preserve random seeds where meaningful.

49. Preserve masks/no-data information where scientifically relevant.

50. Avoid unlabeled coordinate conversions.

51. Avoid unlabeled unit conversions.

52. Do not invent schemas or field names.

53. Do not invent storage, database, or queue architecture.

54. V1 data flow must remain consistent with `V1_SCOPE.md`.

55. Do not introduce V2/V3/V4-only data types into canonical V1 silently.

56. Current implementation status must come from repository evidence, not from this conceptual data-flow document.

<!-- Source specification: :contentReference[oaicite:0]{index=0} -->
