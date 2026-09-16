# ChandraMap System Overview

ChandraMap is an open-source lunar image correspondence and registration research system. Its architecture is organized around one primary scientific capability:

> Given a lunar source image and a suitable reference image or reference region, establish reliable correspondences, verify them geometrically, estimate an appropriate transformation, register the imagery, and report measurable quality or failure information.

This document describes the **major architectural responsibilities and boundaries** of ChandraMap.

It answers:

> **What are the major parts of ChandraMap, and how should they fit together?**

It does not define exact pipeline execution order, repository file ownership, class names, APIs, or serialized data structures. Those responsibilities belong in more specialized architecture documents.

Unless explicitly stated otherwise, architecture described here should be interpreted as **architectural intent or target design**, not proof that every subsystem is currently implemented.

---

## 1. Purpose

The system architecture should make ChandraMap:

- scientifically correct
- modular
- testable
- benchmarkable
- reproducible
- configuration-driven
- sensor-aware where required
- failure-aware
- extensible for research
- independent of presentation technology
- independent of deployment technology
- explicit about data provenance

The architecture should preserve separation between:

```text
Scientific Data
      ↓
Scientific Processing
      ↓
Scientific Evaluation
      ↓
Results / Artifacts
      ↓
Optional Applications
```

The scientific registration core must remain usable without requiring a web application, frontend, deployment stack, or interactive visualization.

---

# 2. Architectural Goals

ChandraMap architecture should support the following high-level goals.

### Scientific Modularity

Separate:

- data loading
- preprocessing
- retrieval
- matching
- geometry
- registration
- evaluation

so each concern can be inspected and evaluated independently.

### Benchmarkability

V1, V2, V3, and V4 should be comparable without duplicating the entire system for each benchmark configuration.

### Reproducibility

A scientific result should be traceable, where practical, to:

- input data
- configuration
- algorithm selection
- model/checkpoint where relevant
- metric definitions
- generated outputs

### Research Flexibility

Experimental algorithms should be easy to evaluate without silently redefining stable behavior.

### Application Independence

Backend, CLI, notebooks, and UI should consume reusable scientific functionality rather than own it.

### Failure Awareness

The architecture must allow the system to report that reliable registration could not be established.

---

# 3. Core Architectural Principles

## 3.1 Scientific Core First

The architecture is centered on:

```text
Correspondence
      ↓
Geometric Verification
      ↓
Transformation
      ↓
Registration
      ↓
Evaluation
```

Mosaics, dashboards, APIs, and interactive maps are downstream consumers.

---

## 3.2 Presentation Must Not Own Science

Frontend code must not become the authoritative implementation of:

- feature extraction
- descriptor matching
- RANSAC
- transform estimation
- benchmark metrics
- registration-quality decisions

The UI should display scientific results produced by the scientific system.

---

## 3.3 Transport Must Not Own Science

HTTP routes, controllers, request handlers, job adapters, or equivalent service-layer code should not contain the canonical registration algorithm.

Conceptually:

```text
HTTP / CLI / UI Input
        ↓
Application Adapter
        ↓
Scientific Core
```

not:

```text
HTTP Route
        ↓
SIFT + RANSAC + Metrics
```

---

## 3.4 Data Provenance Is Architectural

Scientific data identity and provenance should survive through processing.

Derived representations should remain traceable to their source products where practical.

---

## 3.5 Benchmarking Is First-Class

Benchmarking is not merely a presentation feature.

ChandraMap should be architected so algorithms can be evaluated under controlled data, configuration, and metric definitions.

---

## 3.6 Failure Is a Valid Result

The architecture should support explicit states such as:

- successful registration
- quality rejection
- insufficient evidence
- unsupported input
- scientific processing failure

A transform should not be forced simply because a matrix can be computed.

---

# 4. System Context

ChandraMap sits between external planetary data providers and optional user-facing applications.

Conceptually:

```text
External Lunar Data Providers
        ↓
ChandraMap Data / Dataset Boundary
        ↓
Scientific Registration System
        ↓
Evaluation / Benchmarking
        ↓
Results / Artifacts
        ↓
Optional Applications / APIs / Visualization
```

External mission archives remain outside the ChandraMap system boundary.

Examples of external data-provider contexts may include:

- ISRO / ISSDC / PRADAN
- NASA / PDS / LROC
- other official planetary archives

ChandraMap consumes scientific products from these systems; it does not control them.

---

# 5. High-Level Architecture

```text
                   ┌────────────────────────────────┐
                   │     Lunar Data Sources         │
                   │                                │
                   │ Chandrayaan-2                  │
                   │ OHRC • TMC-2 • IIRS           │
                   │                                │
                   │ LRO / LROC                     │
                   │ NAC • WAC                      │
                   │                                │
                   │ Optional terrain/research data │
                   └───────────────┬────────────────┘
                                   │
                                   ▼
                   ┌────────────────────────────────┐
                   │   Data & Dataset Management    │
                   │                                │
                   │ products • manifests           │
                   │ metadata • provenance • I/O    │
                   └───────────────┬────────────────┘
                                   │
                                   ▼
                   ┌────────────────────────────────┐
                   │ Input Validation & Metadata    │
                   │                                │
                   │ format • sensor • masks        │
                   │ scale • projection • footprint │
                   └───────────────┬────────────────┘
                                   │
                                   ▼
                   ┌────────────────────────────────┐
                   │   Scientific Processing Core   │
                   │                                │
                   │ preprocessing                  │
                   │ scale handling                 │
                   │ retrieval where required       │
                   │ local matching                 │
                   │ geometric verification         │
                   │ transformation                 │
                   │ refinement where applicable    │
                   │ registration                   │
                   └───────────────┬────────────────┘
                                   │
                                   ▼
                   ┌────────────────────────────────┐
                   │ Evaluation & Quality           │
                   │                                │
                   │ residuals • RMSE • inliers     │
                   │ coverage • runtime • status    │
                   └───────────────┬────────────────┘
                                   │
                     ┌─────────────┴─────────────┐
                     │                           │
                     ▼                           ▼
          ┌────────────────────────┐   ┌────────────────────────┐
          │ Benchmark / Research   │   │ Results / Artifacts    │
          │                        │   │                        │
          │ V1 • V2 • V3 • V4    │   │ metrics • transforms   │
          │ experiments • ablation │   │ previews • reports     │
          └────────────────────────┘   └────────────┬───────────┘
                                                   │
                                   ┌───────────────┴───────────────┐
                                   │                               │
                                   ▼                               ▼
                        ┌────────────────────┐          ┌────────────────────┐
                        │ CLI / Backend / API│          │ UI / Map / Demo    │
                        │                    │          │ Visualization      │
                        └────────────────────┘          └────────────────────┘
```

This is a conceptual architecture.

It does not assert that every illustrated subsystem is currently implemented.

---

# 6. Architectural Layers

The system can be understood through the following logical layers:

| Layer                 | Primary Responsibility                               |
| --------------------- | ---------------------------------------------------- |
| Data & Dataset        | Scientific products, loading, manifests, provenance  |
| Input Validation      | Structural and semantic input validation             |
| Metadata & Sensor     | Product identity and sensor/geospatial context       |
| Preprocessing         | Registration-ready image representations             |
| Scale Handling        | Physical/resolution relationship management          |
| Retrieval             | Candidate-region discovery where necessary           |
| Local Matching        | Point-level candidate correspondence                 |
| Geometry              | Geometric verification and transformation estimation |
| Refinement            | Optional tie-point/local improvement                 |
| Registration          | Applying validated transformations                   |
| Evaluation            | Scientific metrics and quality evidence              |
| Quality / Decision    | Accept, refine, or reject decisions                  |
| Benchmark             | Controlled V1–V4 comparison                          |
| Research              | Experiments and ablations                            |
| Result / Artifact     | Scientific outputs and generated files               |
| Application / Service | CLI/API/job orchestration                            |
| Visualization         | Scientific result presentation                       |
| Infrastructure        | Packaging, CI, deployment, caches, runtime support   |

These are logical responsibilities.

They do not require one directory, class, or service per layer.

---

# 7. Data & Dataset Layer

The data layer owns ChandraMap's relationship with scientific products.

Its conceptual responsibilities include:

- locating datasets
- reading supported products
- preserving product identity
- preserving metadata
- dataset manifests
- provenance
- masks and no-data handling
- benchmark data references
- test fixtures
- local cache integration where configured

Primary data contexts include:

### Chandrayaan-2

- OHRC
- TMC-2
- IIRS

### LRO / LROC

- NAC
- WAC

### Supporting / Research Data

Potentially:

- DEM
- DTM
- LOLA-derived products
- Kaguya / SELENE
- synthetic or augmented data

Actual support must be determined from repository implementation.

---

## 7.1 Dataset Layer Boundary

The dataset layer should answer:

> What product is this, what metadata belongs to it, and how can its data be read safely?

It should not decide:

> Which matcher produces the best benchmark score?

Avoid combining scientific file loading and matcher policy in the same responsibility.

---

# 8. Input Validation Layer

Input validation should reject structurally invalid or unsupported data before expensive scientific processing.

Potential responsibilities include:

- file readability
- raster dimensions
- expected data shape
- finite-value validation
- mask/no-data handling
- metadata readability
- band-structure validation
- sensor/product recognition
- supported-input determination

Invalid scientific data should fail clearly.

Validation should not silently invent missing:

- GSD
- projection
- sensor identity
- coordinates
- footprint
- spectral metadata

---

# 9. Metadata and Sensor Layer

This layer provides normalized scientific context for downstream processing.

Potential information includes:

- mission
- instrument
- product identifier
- GSD
- projection
- footprint
- product type
- illumination geometry
- viewing geometry
- spectral information

Conceptually:

```text
Scientific Product
        ↓
Metadata Interpretation
        ↓
Normalized Product Context
```

Later benchmark configurations may use this context to select different processing strategies.

---

## 9.1 Sensor Routing

Sensor routing may eventually support flows such as:

```text
                    ┌── OHRC route
Input → Sensor ─────┼── TMC-2 route
                    └── IIRS route
```

Advanced sensor-aware routing is primarily a V2+ concern.

This overview does not assert that each route currently exists.

---

# 10. Scientific Processing Core

The scientific processing core is the central architectural responsibility of ChandraMap.

It should contain reusable scientific logic for:

- preprocessing
- scale handling
- local feature extraction
- descriptor matching
- candidate filtering
- geometric verification
- transformation estimation
- optional refinement
- registration
- scientific evaluation

The core should be callable independently from:

- frontend UI
- web framework
- API routes
- notebooks
- benchmark-reporting UI

---

## 10.1 Core Independence

A reusable scientific function should conceptually accept domain-relevant inputs and return domain-relevant outputs.

For example:

```text
Validated Source + Reference
            ↓
Scientific Registration Logic
            ↓
Registration Result
```

It should not require a browser or HTTP request object to operate.

---

# 11. Preprocessing Subsystem

Preprocessing converts scientific input into representations suitable for correspondence.

Potential responsibilities include:

- grayscale conversion where scientifically appropriate
- normalization
- contrast preparation
- masks
- finite-value handling
- structural representations
- sensor-specific derived representations in later versions

Preprocessing must preserve scientific meaning.

Derived representations should remain traceable to their source data.

---

# 12. Scale Handling Subsystem

Scale handling addresses differences in physical image scale and GSD.

### V1 Context

V1 intentionally keeps scale handling limited and uses known-overlap local pairs.

### V2+ Context

Later architecture may introduce:

- reference pyramids
- effective-GSD comparison
- multi-scale matching
- scale-level selection
- physically meaningful coarse-to-fine processing

The architecture must preserve the rule:

> Upsampling changes representation size; it does not create missing physical terrain information.

---

# 13. Retrieval Subsystem

Retrieval answers:

> Which reference region is likely to contain the source image?

Retrieval is separate from local point correspondence.

Two broad pathways may exist.

---

## 13.1 Metadata-Constrained Search

When reliable metadata exists:

```text
Source Metadata
      ↓
Location / Footprint Constraint
      ↓
Candidate Reference Region
```

This should be preferred over unnecessary global visual retrieval when metadata already provides sufficient localization.

---

## 13.2 Image-Based Retrieval

Where metadata is missing or insufficient:

```text
Source Image
      ↓
Global Descriptor
      ↓
Vector Search
      ↓
Top-K Candidate Regions
```

Image-based global retrieval is primarily associated with later benchmark/research configurations rather than canonical V1.

---

# 14. Retrieval Architecture

Where retrieval exists, offline and online responsibilities should remain distinct.

### Offline Reference Preparation

```text
Reference Products
      ↓
Reference Tiling
      ↓
Optional Multi-Scale Levels
      ↓
Global Descriptor Extraction
      ↓
Vector Index
      +
Tile Metadata
```

### Online Query

```text
Source Image
      ↓
Compatible Global Descriptor
      ↓
Index Search
      ↓
Top-K Candidate Regions
      ↓
Local Registration
```

---

## 14.1 FAISS Role

If FAISS is introduced, it belongs to the retrieval/indexing subsystem.

FAISS may:

- store vectors
- index vectors
- search vectors
- return nearest candidates

FAISS does not:

- produce image descriptors
- detect local features
- perform local correspondence
- perform RANSAC
- estimate registration geometry
- warp images

Avoid creating an architectural "FAISS component" that owns unrelated registration responsibilities.

---

# 15. Local Matching Subsystem

Local matching answers:

> Which precise source/reference image locations correspond?

Potential methods may include:

### Classical

- SIFT
- RootSIFT
- ORB where appropriate

### Advanced / Learned

Potential later configurations may investigate:

- ALIKED + LightGlue
- LoFTR

### Research

Potential research may investigate:

- RIFT-inspired methods
- CFOG-inspired methods
- other multimodal remote-sensing matchers

Do not infer implementation merely from inclusion in the architecture.

---

## 15.1 Matcher Interface Principle

Where practical, different matcher implementations should produce conceptually compatible output:

```text
Candidate Correspondence Set
```

This allows downstream geometry and evaluation to remain reusable.

Do not invent a class hierarchy or plugin framework solely for theoretical flexibility.

Use the simplest interface appropriate to actual repository needs.

---

# 16. Matching vs Geometric Verification

Matching and geometric verification are separate responsibilities.

```text
Local Matcher
      ↓
Candidate Matches
      ↓
Geometry
      ↓
Verified Inliers
```

Matcher confidence must not replace geometric verification.

---

# 17. Geometric Verification Subsystem

The geometry subsystem converts candidate correspondences into model-consistent evidence.

Canonical conceptual flow:

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

Responsibilities may include:

- robust model estimation
- inlier/outlier classification
- degeneracy detection
- transformation validation

RANSAC should remain architecturally separate from descriptor matching.

---

# 18. Transformation Subsystem

Potential global 2D transformation models include:

- affine transform
- homography

The architecture must keep transformation direction explicit:

```text
source → reference
```

or:

```text
reference → source
```

Avoid passing ambiguous transformation matrices between layers without directional meaning.

---

## 18.1 Advanced Geometry

Future research may evaluate:

- local transformations
- piecewise warps
- terrain-aware geometry
- DEM-assisted correction

These belong to later research configurations unless repository evidence establishes otherwise.

---

# 19. Refinement Subsystem

Where benchmark scope permits refinement, the conceptual order should be:

```text
Verified Inliers
      ↓
Local Tie-Point Refinement
      ↓
Refined Coordinates
      ↓
Final Transform Refit
```

Sub-pixel refinement should normally use geometrically supported points rather than arbitrary matcher candidates.

Whether refinement belongs in V1, V2, V3, or V4 is defined by benchmark scope documentation.

Canonical V1 currently treats advanced sub-pixel refinement as outside its baseline unless another authoritative V1 specification explicitly states otherwise.

---

# 20. Registration / Warp Subsystem

Registration applies an accepted transformation.

Potential outputs include:

- transformed image coordinates
- registered image
- overlap representation
- registered preview
- visualization-ready data

Important:

> Warping is not validation.

A warped image may look plausible even when the transformation is poorly supported.

Quality assessment belongs to evaluation.

---

# 21. Evaluation Subsystem

Evaluation should remain sufficiently independent from algorithm implementation to allow fair comparison across methods.

Potential responsibilities include:

- candidate match count
- inlier count
- inlier ratio
- residuals
- reprojection error
- spatial coverage
- independent check-point RMSE
- retrieval Recall@K
- runtime
- success/failure state

Authoritative metrics should come from the scientific evaluation layer.

They must not be invented or calculated independently by frontend presentation code.

---

## 21.1 Fit Points vs Check Points

The architecture should maintain this distinction:

```text
Fit Points
→ estimate transformation

Check Points
→ independently evaluate transformation
```

When valid independent check points exist, final accuracy evaluation should not rely solely on points used for fitting.

---

# 22. Quality and Decision Subsystem

Later benchmark configurations may use explicit quality decisions.

Conceptually:

```text
Scientific Evidence
      ↓
Quality Evaluation
      ↓
┌──────────┬──────────┬──────────┐
│  Accept  │  Refine  │  Reject  │
└──────────┴──────────┴──────────┘
```

Potential evidence may include:

- inlier support
- spatial coverage
- residuals
- transformation validity
- retrieval ambiguity
- independent error

Exact thresholds must come from benchmark/configuration definitions.

Do not invent them here.

---

# 23. Failure Architecture

Failure handling is a first-class architectural concern.

Potential structured outcomes include:

- invalid input
- unsupported product
- unsupported sensor
- insufficient features
- insufficient candidate matches
- geometric verification failure
- degenerate transformation
- registration failure
- quality rejection
- retrieval failure

These should remain distinguishable from unexpected software errors.

Conceptually:

```text
Scientific Failure
→ structured result/status

Unexpected Software Error
→ error handling / diagnostics
```

Do not turn expected scientific failure into a generic server exception when a structured result is appropriate.

---

# 24. Benchmark Layer

Benchmarking orchestrates controlled scientific comparison.

The benchmark layer may conceptually own:

- pair/data selection
- benchmark manifests
- version-specific configuration
- run orchestration
- metric collection
- result comparison
- benchmark reporting

It should not own the underlying SIFT, RANSAC, or registration algorithms.

Those belong in reusable scientific components.

---

# 25. Benchmark V1–V4 Architecture

Benchmark versions are **research configurations**, not software releases.

```text
Benchmark V1 ≠ software v1.0.0
Benchmark V2 ≠ software v2.0.0
Benchmark V3 ≠ software v3.0.0
Benchmark V4 ≠ software v4.0.0
```

The architecture should allow benchmark configurations to compose reusable components.

Conceptually:

```text
                   Shared Scientific Components
                            │
          ┌─────────────────┼─────────────────┐
          │                 │                 │
          ▼                 ▼                 ▼
      V1 Config         V2 Config         V3 Config
          │                 │                 │
          └─────────────────┼─────────────────┘
                            ▼
                         Metrics

                            │
                            ▼
                         V4 Config
```

The exact composition belongs in benchmark specifications.

---

## 25.1 V1 Architectural Role

V1 is the classical local-registration baseline.

Conceptually:

```text
Known Pair
   ↓
Minimal Preprocessing
   ↓
SIFT / RootSIFT
   ↓
Descriptor Matching
   ↓
Candidate Filtering
   ↓
RANSAC
   ↓
Affine / Homography
   ↓
Registration
   ↓
Metrics
```

V1 should remain intentionally simple.

See `../context/V1_SCOPE.md` for the benchmark boundary.

---

## 25.2 V2 Architectural Role

V2 extends the baseline with sensor and scale awareness.

Potential additions include:

- sensor routing
- OHRC/TMC-2/IIRS-aware processing
- physical GSD handling
- reference pyramids
- multi-scale comparison
- structural representations
- stronger illumination handling

This is architectural direction, not an implementation-status claim.

---

## 25.3 V3 Architectural Role

V3 may introduce advanced matching and optional global retrieval.

Potential additions include:

- ALIKED + LightGlue
- LoFTR
- remote-sensing matchers
- global descriptors
- reference tiling
- vector search
- FAISS
- Top-K retrieval

---

## 25.4 V4 Architectural Role

V4 may investigate research-grade robustness.

Potential areas include:

- local/piecewise refinement
- DEM-aware geometry
- advanced IIRS processing
- uncertainty estimation
- confidence calibration
- matcher selection
- advanced rejection logic
- scalable retrieval
- failure classification

Advanced architecture should be introduced only when it addresses demonstrated limitations.

---

# 26. Avoid Benchmark Pipeline Duplication

Avoid an architecture dominated by independent copies such as:

```text
v1_complete_pipeline.py
v2_complete_pipeline.py
v3_complete_pipeline.py
v4_complete_pipeline.py
```

when they repeat most of the same scientific implementation.

Prefer, where practical:

```text
Reusable Components
        +
Benchmark-Specific Configuration / Composition
        ↓
V1 / V2 / V3 / V4
```

However, do not create an elaborate abstraction framework prematurely.

A small repository may initially benefit from straightforward composition rather than a complex plugin system.

---

# 27. Research and Experiment Layer

Research work should be separated from stable/default scientific behavior.

Potential experiment areas include:

- matcher comparisons
- IIRS representation experiments
- scale studies
- illumination studies
- geometric-model comparisons
- ablation studies
- retrieval experiments

Research code may call the scientific core.

Research experiments must not silently redefine canonical benchmark or default behavior.

---

## 27.1 Notebook Role

Notebooks are useful for:

- data exploration
- visualization
- experiment analysis
- research review
- dataset inspection

They should not become the only location containing reusable core algorithms.

Once experimental logic stabilizes and becomes reusable, it should move into appropriate reusable modules according to repository conventions.

---

# 28. Configuration Architecture

Configuration should control scientific variation where practical.

Potential configurable concerns include:

- preprocessing
- matcher
- RANSAC
- transformation model
- scale selection
- retrieval settings
- quality thresholds
- benchmark configuration
- model/checkpoint selection

Use the repository's actual configuration mechanism.

Do not invent filenames, schemas, or configuration keys here.

---

## 28.1 Configuration vs Hidden Tuning

Scientific behavior should not depend on undocumented manual edits.

Prefer:

```text
Explicit Configuration
        ↓
Reproducible Execution
```

over:

```text
Edit Source Per Image
        ↓
Untraceable Result
```

Not every software constant needs configuration, but experiment-defining behavior should be explicit.

---

# 29. Result and Artifact Layer

The architecture should distinguish **results** from **artifacts**.

## Result

A structured scientific processing outcome.

Potential content may include:

- status
- transformation
- match statistics
- metrics
- provenance
- failure information

## Artifact

A generated file or output associated with a run.

Potential examples include:

- registered image
- overlay
- match visualization
- metrics file
- benchmark report
- descriptor cache
- vector index

Do not infer exact result schemas from these conceptual examples.

---

# 30. Result Traceability

Where practical, scientific results should remain traceable to:

```text
Input Products
      +
Benchmark Pair
      +
Configuration
      +
Algorithm / Model
      +
Metric Definition
      +
Code Revision
      ↓
Reconstructable Result
```

Generated numerical results must not be manually modified to improve presentation.

---

# 31. Raw Data vs Derived Data

The architecture should preserve clear boundaries among:

```text
Original Scientific Products
        ↓
Derived Representations
        ↓
Scientific Results
        ↓
Presentation Artifacts
```

Examples of derived representations include:

- image pyramids
- PCA representations
- gradients
- edges
- tiles
- descriptors
- embeddings

Examples of artifacts include:

- registered previews
- overlays
- benchmark charts
- reports

Do not label derived data as original mission data.

---

# 32. Cache Architecture

Potential caches may include:

- decoded imagery
- reference pyramids
- tiles
- descriptors
- embeddings
- retrieval indexes

Caches should be reproducible or invalidatable where practical.

The cache is not the authoritative scientific source.

Conceptually:

```text
Scientific Input
      +
Configuration
      ↓
Reproducible Cache
```

rather than:

```text
Cache
→ only surviving source of truth
```

---

# 33. Application and Service Layer

Optional application layers may expose scientific functionality through:

- CLI
- API
- batch workflows
- benchmark commands
- jobs

These layers should primarily perform:

- input adaptation
- orchestration
- validation at application boundaries
- invoking scientific functionality
- returning/persisting results

They should not duplicate core algorithms.

---

# 34. Backend / API Boundary

A backend may eventually support operations such as:

- registration requests
- benchmark execution
- retrieval requests
- artifact access
- result inspection

This overview intentionally does not define:

- endpoint names
- HTTP methods
- queue technology
- database
- worker architecture

Those details must come from current repository implementation or dedicated service architecture documentation.

---

## 34.1 Long-Running Processing

Some operations may become computationally expensive.

Future architecture may therefore use:

- background execution
- batch processing
- workers
- job-oriented workflows

if actual requirements justify them.

Do not introduce queue infrastructure merely because scientific tasks can be expensive.

---

# 35. CLI and Script Boundary

CLI tools and scripts may orchestrate operations such as:

- registration
- benchmark runs
- dataset preparation
- retrieval-index construction
- result inspection

They should call reusable scientific components rather than contain the entire algorithm implementation directly.

---

# 36. Frontend and Visualization Layer

Potential visualization responsibilities include:

- source/reference image display
- candidate-match display
- verified-inlier display
- outlier visualization
- registered overlays
- residual vectors
- benchmark tables
- metric presentation
- map display

Frontend code should consume scientific outputs.

It should not calculate authoritative registration truth independently.

---

## 36.1 Visual Scientific Integrity

The presentation layer should clearly distinguish:

- candidate matches
- verified inliers
- rejected outliers
- measured metrics
- example/placeholder values
- accepted registration
- rejected registration
- software errors

Do not display fabricated confidence or benchmark values.

---

# 37. Mosaic and Map Layer

Mosaics and interactive lunar maps are downstream applications.

Conceptually:

```text
Reliable Individual Registrations
            ↓
Multiple Aligned Products
            ↓
Mosaic / Map / Visualization
```

Do not make mosaic generation a prerequisite for proving local correspondence quality.

A visually smooth mosaic is not a substitute for measurable registration accuracy.

---

# 38. Infrastructure Layer

Infrastructure supports the scientific system.

Potential concerns include:

- packaging
- dependency management
- CI
- deployment
- containers
- caches
- artifact storage
- runtime environments

Infrastructure should not define scientific algorithms.

Do not infer or invent:

- Kubernetes
- Kafka
- Redis
- cloud databases
- object stores
- service meshes
- distributed queues

without repository evidence and an actual requirement.

---

# 39. Observability

Useful processing observability may include:

- processing stage
- benchmark pair ID
- selected matcher
- keypoint count
- candidate match count
- inlier count
- runtime
- failure/rejection reason

Avoid logging:

- full image arrays
- huge descriptor arrays
- credentials
- secrets
- unnecessary private paths

Follow existing logging conventions.

---

# 40. Security Boundaries

Important trust boundaries may include:

- user-provided files
- mission rasters
- archives
- external downloads
- model checkpoints
- serialized data
- generated output paths
- subprocess execution

The architecture should treat external input as potentially untrusted.

Detailed security behavior belongs in:

- root `SECURITY.md`
- engineering/security documentation

This overview defines the boundary, not the complete mitigation policy.

---

# 41. External Dependency Boundary

ChandraMap may rely on external libraries for capabilities such as:

- image processing
- numerical computing
- geospatial processing
- machine learning
- vector search

External libraries should remain implementation dependencies rather than becoming the conceptual architecture itself.

For example:

```text
Retrieval Subsystem
      ↓
Vector Search Adapter
      ↓
FAISS
```

is conceptually preferable to defining the whole retrieval architecture as:

```text
FAISS System
```

when the architectural responsibility is broader than one library.

---

## 41.1 Avoid Abstraction for Its Own Sake

Introduce an abstraction around an external dependency when it provides real value such as:

- testability
- replacement
- isolation of external API complexity
- reuse

Do not create wrappers merely to appear architecturally sophisticated.

---

# 42. Dependency Direction

A useful conceptual dependency direction is:

```text
Applications / UI / API
          ↓
Application Orchestration
          ↓
Scientific Core
          ↓
Domain Utilities / Interfaces
          ↓
External Libraries / Data Adapters
```

Avoid dependencies such as:

```text
Scientific Core
      ↓
React Component
```

or:

```text
Metric Calculation
      ↓
Frontend State
```

or:

```text
RANSAC
      ↓
HTTP Request Object
```

---

# 43. Responsibility Boundaries

Avoid circular or mixed responsibilities.

| Component     | Should Own                              | Should Not Own                   |
| ------------- | --------------------------------------- | -------------------------------- |
| Dataset layer | Product I/O, metadata, provenance       | Matcher selection                |
| Preprocessing | Registration representation preparation | Benchmark reporting UI           |
| Matcher       | Candidate correspondences               | Final scientific acceptance      |
| Geometry      | Verification and transform estimation   | File-upload handling             |
| Registration  | Transformation application              | Accuracy invention               |
| Evaluation    | Metrics and scientific evidence         | UI state                         |
| Benchmark     | Controlled orchestration/comparison     | Core algorithm duplication       |
| Backend/API   | Transport/orchestration                 | Canonical feature matching logic |
| Frontend      | Visualization/interaction               | Authoritative metric computation |

---

# 44. Conceptual Subsystem Interfaces

Exact classes belong elsewhere, but conceptual boundaries should remain clear.

```text
Dataset Layer
→ validated image/product + metadata

Preprocessing
→ registration-ready representation

Retrieval
→ candidate reference regions

Local Matcher
→ candidate correspondences

Geometry
→ verified inliers + transformation

Refinement
→ refined tie points / updated geometry

Registration
→ aligned image/coordinates

Evaluation
→ scientific metrics + quality evidence

Benchmark Runner
→ controlled runs + comparable result records

Application Layer
→ user/system-facing orchestration
```

Do not infer exact object names from this diagram.

---

# 45. Conceptual Domain Objects

The architecture may require concepts corresponding to:

- image product
- metadata
- candidate match
- correspondence set
- transform
- registration result
- metrics
- benchmark run

These are conceptual entities.

They are not declarations of actual repository classes such as:

```text
ImageProduct
RegistrationResult
BenchmarkRun
```

unless implementation documentation confirms those names.

Exact structures belong in `DATA_FLOW.md` and implementation.

---

# 46. Extensibility

Controlled benchmarking benefits from replaceable strategies for areas such as:

- preprocessing
- feature extraction
- matching
- geometric modeling
- evaluation

However, extensibility should remain proportional to actual needs.

Prefer:

```text
Simple Interface
      ↓
Two Implementations
```

over a large plugin framework created before requirements are understood.

---

# 47. Failure Propagation

Scientific failures should propagate upward with meaningful context.

Conceptually:

```text
Matcher
→ insufficient correspondences

Geometry
→ no valid transform

Evaluator
→ quality rejection

Application
→ clear scientific failure result
```

Do not reduce every failure to:

```text
500 Internal Server Error
```

when the event is actually an expected scientific outcome.

---

# 48. Reproducibility Architecture

ChandraMap should increasingly support reproducibility through explicit relationships among:

```text
Code
 +
Configuration
 +
Dataset Identity
 +
Benchmark Pair
 +
Model / Checkpoint where relevant
 +
Metric Definitions
 =
Reconstructable Run
```

Do not rely on undocumented:

- notebook state
- manual parameter changes
- local file naming
- UI-only settings

to define scientific experiments.

---

# 49. Testability

Subsystem boundaries should make scientific and software behavior testable independently.

Potential examples:

| Subsystem            | Appropriate Test Style                             |
| -------------------- | -------------------------------------------------- |
| Dataset loader       | Small scientific fixtures                          |
| Metadata parser      | Controlled metadata samples                        |
| Preprocessor         | Small deterministic arrays/images                  |
| Matcher              | Compact source/reference examples                  |
| Geometry             | Known synthetic point correspondences              |
| Metrics              | Mathematically controlled fixtures                 |
| Pipeline integration | Small known-overlap pair                           |
| Benchmark            | Controlled real lunar pairs                        |
| API adapter          | Application-layer tests without redefining science |

Testing the scientific core should not require launching an entire frontend/deployment environment.

---

# 50. CI Architecture

Normal CI should generally favor:

- unit tests
- lightweight integration tests
- configuration validation
- static checks defined by repository tooling

Large lunar datasets and expensive GPU benchmarks do not necessarily belong in every pull request.

Scientific benchmark execution may be handled separately where necessary.

This document does not claim any particular CI workflow currently exists.

---

# 51. Research vs Stable Architecture

The repository should preserve a boundary between:

```text
Stable / Reusable Scientific Components
```

and:

```text
Experimental / Research Components
```

Experimental work may explore:

- new matchers
- new IIRS representations
- new retrieval descriptors
- new illumination strategies
- new geometric models

Successful experimentation on one pair should not automatically alter the stable/default pipeline.

Promotion should involve appropriate:

- testing
- configuration
- documentation
- benchmark evidence
- failure handling

---

# 52. Current vs Target Architecture

This distinction is mandatory.

## Current

Functionality directly supported by the repository's present:

- code
- tests
- configuration
- actual interfaces

## Target

Approved architectural direction that the project is moving toward but may not yet implement fully.

## Experimental

Prototype/research functionality without stable guarantees.

## Planned

Documented future functionality not yet implemented.

This file primarily describes the **logical and target architecture** of ChandraMap.

It must not be used by itself as proof that every subsystem exists.

Before describing something as implemented, inspect the repository.

For example:

> The target V3 architecture may use a FAISS-backed vector-search component.

does not mean:

> ChandraMap currently runs a FAISS retrieval service.

Likewise:

> V4 may investigate DEM-aware geometry.

does not mean:

> ChandraMap currently performs DEM-aware warping.

---

# 53. Repository Mapping

Architectural responsibilities may map to repository areas such as:

- reusable scientific source code
- benchmarks
- configuration
- dataset definitions
- experiments
- research
- notebooks
- services
- applications
- tests
- results/artifacts
- documentation

The exact repository mapping belongs in `MODULE_MAP.md`.

Do not infer directories or package names from this logical architecture alone.

---

# 54. Architectural Invariants

The following principles should remain true as ChandraMap evolves:

1. Lunar correspondence and registration remain the scientific core.

2. Mosaics and map interfaces remain downstream of validated registration.

3. Frontend code does not own scientific algorithms.

4. HTTP/backend transport does not own scientific algorithms.

5. Scientific data provenance remains available where practical.

6. Exact product metadata takes priority over broad instrument approximations.

7. Raw mission products remain distinguishable from derived data.

8. Candidate matches remain distinct from verified inliers.

9. Matcher confidence does not replace geometric verification.

10. RANSAC/geometric verification remains conceptually separate from descriptor matching.

11. Transformation direction remains explicit where needed.

12. Registration/warping is not treated as proof of accuracy.

13. Evaluation remains quantitative and independent of visualization.

14. Fit points remain distinct from independent check points.

15. Failure/rejection remains a valid scientific result.

16. Global retrieval remains distinct from local registration.

17. Global descriptors remain distinct from local matching descriptors.

18. FAISS remains a vector-search/indexing component, not an image feature extractor.

19. Benchmark V1–V4 remain research configurations, not software releases.

20. V1 remains an intentionally simple classical baseline.

21. V2 owns the main sensor-aware and scale-aware expansion.

22. V3 may introduce advanced matching and retrieval.

23. V4 may introduce advanced robustness, refinement, and uncertainty research.

24. Benchmark versions should reuse scientific components where practical rather than duplicate entire pipelines.

25. Experimental code does not silently redefine stable defaults.

26. Scientific metrics are not hard-coded in frontend presentation code.

27. Configuration controls meaningful experiment variation rather than hidden manual tuning.

28. Target architecture is never presented as current implementation without evidence.

---

# 55. Architectural Anti-Patterns

## 55.1 Monolithic Pipeline Script

Avoid a single script owning:

```text
Data Loading
+ Preprocessing
+ Matching
+ RANSAC
+ Warping
+ Metrics
+ Storage
+ API
+ UI
```

with no meaningful responsibility boundaries.

---

## 55.2 UI-Driven Science

Avoid computing authoritative:

- RMSE
- inlier statistics
- benchmark scores
- confidence values

inside frontend presentation logic.

---

## 55.3 API-Driven Science

Avoid duplicating scientific registration logic directly inside route handlers.

---

## 55.4 Benchmark Forking

Avoid copying the full system into separate V1/V2/V3/V4 implementations where shared components would preserve comparability and reduce duplication.

---

## 55.5 Notebook-Only Core

Do not leave important reusable registration algorithms exclusively inside notebooks.

---

## 55.6 Hidden Dataset Assumptions

Avoid hard-coded assumptions such as:

- fixed sensor identity
- fixed GSD
- fixed local path
- fixed projection

without supporting data/configuration evidence.

---

## 55.7 Magic Metrics

Never embed fabricated:

- accuracy values
- confidence values
- benchmark scores

into application code or UI.

---

## 55.8 Silent Failure

Do not force an output when the scientific evidence is inadequate.

---

## 55.9 Algorithm Coupling

Do not design the entire project so every subsystem assumes exactly one matcher unless the benchmark scope intentionally requires it.

---

## 55.10 Storage Coupling

Do not make scientific algorithms depend directly on a specific database, object store, or cloud platform without a genuine architectural requirement.

---

## 55.11 Premature Enterprise Architecture

Do not add:

- microservices
- Kubernetes
- service meshes
- distributed messaging
- event sourcing
- distributed databases

merely to make a research repository look professional.

Professional architecture is the simplest architecture that correctly supports the requirements.

---

# 56. Architectural Decision Criteria

Before introducing a new subsystem or abstraction, ask:

- Does it solve an observed problem?
- Does it clarify responsibility?
- Does it improve testability?
- Does it improve benchmarkability?
- Does it reduce meaningful duplication?
- Does it improve reproducibility?
- Does it isolate an unstable external dependency?
- Is the added complexity justified?

A new component is generally justified when it has:

- distinct responsibility
- meaningful independent behavior
- a useful interface
- reuse potential
- independent testing value

Do not turn every utility into a service.

---

# 57. When Not to Abstract

Avoid premature abstraction when:

- only one implementation exists
- the research question is still changing
- the correct boundary is not yet understood
- abstraction adds more code than clarity
- requirements remain experimental

Research exploration may need to remain concrete before stable architectural interfaces are selected.

---

# 58. Architecture Evolution

Material architecture changes may require updates to:

- this system overview
- detailed pipeline documentation
- module ownership documentation
- data-flow documentation
- project context if project scope changes
- benchmark specifications where comparability changes
- roadmap for future direction
- changelog for notable completed changes

Do not update every architecture document mechanically for a small internal refactor.

---

# 59. Long-Term Target Architecture

A mature ChandraMap architecture may eventually support:

```text
Scientific Input Product
        ↓
Validation + Metadata
        ↓
Sensor-Aware Preparation
        ↓
Scale-Aware Processing
        ↓
Metadata-Constrained Search
             OR
        Global Retrieval
        ↓
Candidate Reference Region
        ↓
Local Matcher Selection
        ↓
Candidate Correspondences
        ↓
Geometric Verification
        ↓
Verified Inliers
        ↓
Local / Sub-Pixel Refinement
        ↓
Final Transform Refit
        ↓
Terrain-Aware Processing where justified
        ↓
Registration
        ↓
Independent Evaluation
        ↓
Accept / Refine / Reject
        ↓
Structured Registration Result
        ↓
API / CLI / UI / Mosaic
```

This is a **target architectural direction**.

It must not be interpreted as a declaration that every stage currently exists.

---

# 60. Related Architecture and Context Documents

Where present, use the following documents for deeper context.

[Project Context](../context/PROJECT_CONTEXT.md)
→ what ChandraMap is and why it exists

[Domain Context](../context/DOMAIN_CONTEXT.md)
→ lunar imaging, GSD, modality, illumination, geometry, and evaluation science

[Terminology](../context/TERMINOLOGY.md)
→ canonical project terminology

[Dataset Context](../context/DATASETS.md)
→ missions, scientific products, metadata, provenance, and dataset roles

[V1 Scope](../context/V1_SCOPE.md)
→ exact boundary of the classical Benchmark V1 baseline

`PIPELINE.md`, when present
→ ordered processing stages, branches, and failure paths

`MODULE_MAP.md`, when present
→ mapping of logical responsibilities to actual repository modules

`DATA_FLOW.md`, when present
→ objects, metadata, correspondences, results, and artifacts moving between subsystems

Benchmark documentation, when present
→ exact V1–V4 benchmark definitions and metric rules

Do not assume an unverified document exists merely because it belongs to the intended architecture documentation set.

---

# 61. Key Architecture Rules for AI Agents

1. Correspondence and registration are ChandraMap's scientific core.

2. Mosaics, maps, dashboards, and visualization are downstream consumers.

3. Keep scientific algorithms independent of frontend code.

4. Keep scientific algorithms independent of HTTP/backend transport objects.

5. Dataset handling should preserve scientific provenance and metadata.

6. Product metadata should override broad sensor approximations where available.

7. Sensor identification should precede sensor-specific processing.

8. Retrieval and local registration are separate architectural concerns.

9. Use metadata-constrained search when reliable metadata already identifies likely overlap.

10. Global image retrieval should exist only where it solves a real localization problem.

11. FAISS is a vector search/indexing component, not an image feature extractor.

12. Global descriptors and local descriptors serve different roles.

13. Local matchers produce candidate correspondences.

14. Candidate matches are not verified inliers.

15. Geometry verifies candidate correspondences.

16. Keep RANSAC/geometric verification separate from descriptor matching.

17. Keep transform direction explicit.

18. Registration/warping does not validate the transformation.

19. Evaluation should be independent of visualization.

20. Fit points and independent check points remain conceptually distinct.

21. Failure and rejection are valid system outcomes.

22. Scientific failures should not automatically become generic software exceptions.

23. V1–V4 are benchmark configurations, not software releases.

24. V1 remains the simple classical local-registration baseline.

25. V2 introduces the main sensor-aware and scale-aware architecture.

26. V3 may introduce advanced learned matching and global retrieval.

27. V4 may introduce advanced refinement, terrain-aware processing, uncertainty, and robust decision logic.

28. Do not duplicate the entire scientific pipeline for each benchmark version without justification.

29. Prefer shared reusable components plus version-specific composition where practical.

30. Do not create a complex plugin system merely for theoretical extensibility.

31. Research experiments should not silently redefine stable/default behavior.

32. Core reusable functionality should not exist only in notebooks.

33. Generated artifacts are distinct from scientific input datasets.

34. Raw mission products and derived representations must remain distinguishable.

35. Result metrics must come from scientific evaluation rather than presentation logic.

36. Configuration should define experiment variation rather than hidden per-image edits.

37. Preserve enough context for benchmark runs to be reproducible where practical.

38. Core registration should be testable without starting a frontend or deployment stack.

39. Do not invent API endpoints, databases, queues, cloud systems, or deployed services.

40. Do not overengineer ChandraMap merely to make the architecture appear enterprise-grade.

41. Introduce abstractions only when they create measurable architectural value.

42. Keep external mission archives outside the internal ChandraMap service boundary.

43. Do not describe target architecture as current implementation without repository evidence.

44. Architecture changes affecting benchmark methodology must preserve or explicitly document comparability.

45. The simplest architecture that preserves scientific correctness, reproducibility, and testability is preferred.

<!-- Source specification: :contentReference[oaicite:0]{index=0} -->
