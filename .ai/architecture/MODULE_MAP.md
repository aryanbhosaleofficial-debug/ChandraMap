# ChandraMap Module Map

This document maps ChandraMap's architectural responsibilities to repository locations.

It answers:

> **Where in the repository should each responsibility live, and which areas should collaborate without duplicating ownership?**

The actual repository tree remains the source of truth. This document should guide navigation and ownership, but it must not be used to invent modules, classes, packages, or services that do not exist.

ChandraMap's current repository context includes major top-level areas for reusable source code, benchmarks, configuration, data, research, services, applications, tests, results, artifacts, deployment support, documentation, and AI-agent context. Exact submodule ownership inside those areas must be verified from the current tree before changing code.

---

## 1. Purpose

`MODULE_MAP.md` exists to connect:

```text
Architectural Responsibility
        ↓
Repository Area
        ↓
Implementation Module
        ↓
Nearby Tests / Configuration / Documentation
```

Its main goals are to:

* give major responsibilities one clear architectural home
* prevent duplicate implementations
* keep scientific code reusable
* keep benchmark composition separate from scientific algorithms
* separate stable code from research experiments
* separate scientific computation from API/UI concerns
* clarify dependency direction
* reduce unnecessary repository-wide searching
* help AI agents identify the smallest relevant change surface

This document does not replace source inspection.

---

## 2. Source-of-Truth Rule

The repository tree is authoritative.

Before making a path-specific change:

1. inspect the actual relevant repository area
2. confirm that the expected module exists
3. inspect its neighboring modules
4. inspect nearby tests
5. inspect relevant configuration
6. inspect applicable architecture/context documentation
7. modify the smallest appropriate ownership area

Do not infer that a module exists simply because this document describes a responsibility.

For example:

```text
Pipeline concept:
Global Retrieval
```

does not automatically imply the repository contains:

```text
src/chandramap/retrieval/faiss.py
```

The implementation path must be verified.

---

## 3. How to Read This Document

This map distinguishes three concepts.

### Repository Area

A top-level or major repository location such as:

```text
src/chandramap/
benchmarks/
tests/
```

### Module Group

A logical implementation area inside a repository location.

Examples conceptually include:

* matching
* geometry
* evaluation
* retrieval

Exact package names must come from the repository.

### Responsibility

The behavior that one module or module group should primarily own.

Examples:

* candidate correspondence generation
* geometric verification
* RMSE calculation
* benchmark orchestration

One responsibility may collaborate with several modules, but it should normally have one clear primary owner.

---

# 4. Repository Architecture Principles

## 4.1 One Primary Owner per Significant Responsibility

Prefer:

```text
One Primary Owner
        +
Explicit Collaborators
```

over:

```text
Same Logic
├── API route
├── benchmark script
├── notebook
├── frontend
└── version-specific copy
```

---

## 4.2 Scientific Logic Belongs in Reusable Scientific Code

Reusable scientific functionality should primarily live under the project's reusable scientific package rather than inside:

* frontend code
* API routes
* benchmark runners
* notebooks
* shell scripts
* experiment-specific files

Application layers should orchestrate the scientific core.

---

## 4.3 Benchmark Versions Compose the Core

Benchmark V1–V4 should normally select and compose reusable components.

Avoid four mostly duplicated scientific pipelines.

---

## 4.4 Stable Core Must Not Depend on Experimental Code

Preferred direction:

```text
Research / Experiments
        ↓
Scientific Core
```

Avoid:

```text
Scientific Core
        ↓
Temporary Research Prototype
```

---

## 4.5 Presentation Must Not Own Scientific Truth

Frontend and visualization modules may display:

* matches
* transformations
* metrics
* registration status

They should not independently redefine their scientific meaning.

---

# 5. High-Level Repository Map

The current ChandraMap repository context documents the following major top-level areas:

```text
ChandraMap/
├── .ai/
├── .github/
├── apps/
├── artifacts/
├── benchmarks/
├── configs/
├── contracts/
├── data/
├── deploy/
├── docs/
├── experiments/
├── notebooks/
├── research/
├── results/
├── scripts/
├── services/
├── src/
│   └── chandramap/
└── tests/
```

This map intentionally stops at major areas.

Exact internal files and subpackages must be inspected from the current repository before use.

---

# 6. High-Level Responsibility Map

| Repository Area   | Primary Responsibility                          | Should Contain                                                                               | Should Not Contain                                                   |
| ----------------- | ----------------------------------------------- | -------------------------------------------------------------------------------------------- | -------------------------------------------------------------------- |
| `src/chandramap/` | Reusable ChandraMap implementation              | scientific algorithms, reusable domain logic, processing components                          | benchmark result files, UI implementation, notebook-only experiments |
| `benchmarks/`     | Controlled V1–V4 evaluation                     | benchmark definitions, manifests, composition, runners, comparison tooling                   | duplicated SIFT/RANSAC/metric implementations                        |
| `configs/`        | Configuration                                   | benchmark, matcher, processing, experiment settings where repository conventions define them | large scientific algorithms, generated metrics                       |
| `contracts/`      | Shared contracts/schemas where actually defined | data/result/API contracts according to current repository usage                              | unrelated algorithms                                                 |
| `data/`           | Dataset-oriented repository material            | manifests, small fixtures, dataset metadata/conventions according to current structure       | large arbitrary scientific code                                      |
| `experiments/`    | Reproducible experimental work                  | experiment definitions, controlled comparisons                                               | sole implementation of stable core algorithms                        |
| `research/`       | Research-oriented work                          | prototypes, research implementations, investigation material according to current structure  | silently promoted production defaults                                |
| `notebooks/`      | Interactive exploration                         | analysis, visualization, inspection                                                          | only copy of critical reusable algorithms                            |
| `services/`       | Backend/service integration                     | orchestration, transport adapters, application integration                                   | duplicated matching/geometry algorithms                              |
| `apps/`           | User-facing applications                        | UI/application composition                                                                   | authoritative scientific algorithms                                  |
| `scripts/`        | Thin operational entry points                   | orchestration, preparation, maintenance commands                                             | large reusable scientific implementations                            |
| `tests/`          | Automated verification                          | unit, integration, regression, lightweight scientific tests                                  | production implementations                                           |
| `results/`        | Generated scientific/run results                | metrics, summaries, run outputs according to repository policy                               | source implementation                                                |
| `artifacts/`      | Generated files/artifacts                       | registered images, plots, overlays, other generated outputs according to repository policy   | authoritative algorithms                                             |
| `deploy/`         | Deployment support                              | actual deployment configuration/scripts                                                      | scientific algorithms                                                |
| `docs/`           | Human-facing project documentation              | architecture, usage, scientific and developer documentation                                  | production implementation                                            |
| `.ai/`            | AI-agent context                                | project context, architecture, engineering guidance                                          | production code                                                      |
| `.github/`        | GitHub repository automation                    | workflows/templates/automation actually present                                              | scientific core implementation                                       |

---

# 7. Scientific Core

The primary reusable Python implementation belongs under:

```text
src/chandramap/
```

The exact internal package structure must be inspected before path-specific modifications.

The scientific core should conceptually own reusable logic for:

* scientific input representation
* metadata handling
* preprocessing
* sensor handling
* scale handling
* retrieval interfaces where implemented
* local features
* local correspondence
* geometric verification
* transformation estimation
* tie-point refinement where implemented
* registration
* evaluation
* quality/rejection logic

The scientific core should remain callable independently of:

* frontend components
* HTTP request objects
* browser state
* benchmark report rendering

---

# 8. Data / I/O Ownership

## Primary Architectural Home

Reusable scientific package:

```text
src/chandramap/
```

with the exact data/I/O submodule determined by repository inspection.

Dataset manifests and repository-level dataset definitions may collaborate through:

```text
data/
```

where that matches the current repository structure.

---

## Responsibility

Data/I/O code may own:

* opening scientific products
* reading rasters
* reading multi-band data
* reading metadata
* preserving masks/no-data information
* validating basic product structure
* adapting supported scientific formats into internal representations

---

## Must Not Own

Data loaders should not own:

* SIFT matching
* LightGlue
* LoFTR
* RANSAC
* registration-quality scoring
* frontend rendering
* benchmark-version selection

The loader answers:

> What is this scientific product and how do I read it?

It does not answer:

> Which matcher should V3 use?

---

# 9. Metadata Ownership

Metadata processing belongs within the scientific/data portion of the reusable core.

Potential responsibilities include:

* mission identity
* instrument identity
* product identifier
* GSD
* footprint
* projection
* planetary coordinate information
* acquisition information
* illumination information
* viewing geometry
* spectral metadata

Metadata code should not silently invent unavailable values.

Exact paths must be verified from the repository.

---

# 10. Sensor Handling Ownership

Where sensor-specific behavior exists, it belongs in reusable scientific code rather than benchmark scripts or API routes.

Conceptual responsibilities include:

```text
Sensor Identification
        ↓
Sensor-Appropriate Representation / Processing Choice
```

Relevant sensor contexts include:

* OHRC
* TMC-2
* IIRS

Potential later-version responsibilities include:

* OHRC preparation
* TMC-2 preparation
* IIRS spectral-to-2D representation

Avoid growing one generic file into a large sensor-processing dumping ground if responsibilities become substantial.

Do not create new sensor modules before inspecting current ownership.

---

# 11. Preprocessing Ownership

Preprocessing belongs in reusable scientific code.

Potential responsibilities include:

* intensity normalization
* grayscale preparation where appropriate
* no-data handling
* masking
* contrast processing
* deterministic representation preparation
* structural image representations where scientifically justified

Preprocessing should produce data suitable for downstream matching.

It should not own:

* RANSAC
* final transformation acceptance
* API serialization
* UI rendering

---

# 12. Scale Handling Ownership

Where scale handling becomes substantial, it belongs in reusable scientific code rather than being reimplemented independently by each benchmark version.

Potential responsibilities include:

* GSD comparison
* effective-scale reasoning
* pyramid construction
* pyramid-level selection
* coarse-to-fine scale handling

Benchmark configuration should select scale behavior.

The low-level scale implementation generally should not need to know whether V2 or V3 called it.

---

# 13. Retrieval Ownership

Global candidate-region retrieval should have a clear owner within reusable scientific functionality when implemented.

Potential responsibilities include:

* reference tiling support
* global descriptor integration
* vector-index interaction
* Top-K candidate production
* tile-to-product metadata mapping

Retrieval answers:

> Which reference region is likely to contain this source observation?

It does not own precise local registration.

---

## 13.1 FAISS Ownership

If FAISS is used, it belongs within retrieval/indexing responsibility or behind a retrieval adapter.

FAISS should not be owned by:

* geometry
* registration
* frontend
* API routes

FAISS:

```text
stores/searches vectors
```

It does not:

```text
extract local features
run RANSAC
estimate homographies
warp imagery
```

---

## 13.2 Offline vs Online Retrieval

Where implementation complexity justifies separation:

### Offline Responsibilities

```text
Reference Products
        ↓
Tiles
        ↓
Descriptors
        ↓
Index
```

### Online Responsibilities

```text
Query
  ↓
Descriptor
  ↓
Index Search
  ↓
Candidate Regions
```

Do not create separate packages merely because the conceptual processes differ if the implementation remains small.

---

# 14. Feature Extraction Ownership

Feature extraction belongs in reusable scientific code.

Potential implementations may include:

* SIFT
* RootSIFT-related descriptor handling
* ALIKED where introduced

Feature extraction should not own:

* RANSAC
* transform estimation
* result persistence
* benchmark aggregation

---

## ALIKED Responsibility

If used:

```text
ALIKED
→ sparse local feature extraction
```

Its conceptual responsibility should remain distinct from LightGlue.

---

# 15. Matching Ownership

Local matching belongs in reusable correspondence/matching code.

Potential implementations may include:

* conventional SIFT descriptor matching
* LightGlue
* LoFTR
* remote-sensing correspondence methods

The primary output is conceptually:

> **candidate correspondences**

The matching layer should not declare those correspondences scientifically verified merely because the matcher reports confidence.

---

## 15.1 LightGlue Responsibility

If used:

```text
Feature Sets
    ↓
LightGlue
    ↓
Candidate Correspondences
```

LightGlue belongs to matching responsibility.

---

## 15.2 LoFTR Responsibility

LoFTR is detector-free correspondence matching.

It should be owned by a correspondence/matcher abstraction rather than forced into a traditional feature-extractor-only module.

---

# 16. Geometry Ownership

Geometric verification and transform estimation require one clear scientific owner.

Potential responsibilities include:

* RANSAC
* affine estimation
* homography estimation
* inlier masks
* outlier rejection
* geometric-consistency tests
* degeneracy detection
* transformation-direction handling

Conceptually:

```text
Candidate Correspondences
        ↓
Geometry
        ↓
Verified Inliers
        +
Transformation
```

Geometry should not own:

* dataset downloading
* global retrieval
* frontend visualization

---

# 17. Transformation Ownership

Coordinate and transformation logic should have consistent ownership within the scientific core.

Important concepts include:

* affine transforms
* homographies
* transformation application
* inversion where valid
* transform direction

Avoid independently redefining:

```text
source → reference
```

versus:

```text
reference → source
```

through unrelated modules.

Coordinate/transformation conventions are high-risk cross-cutting logic and should remain centralized where practical.

---

# 18. Refinement Ownership

Where implemented, refinement belongs downstream of geometric verification.

Potential responsibilities include:

* local patch alignment
* sub-pixel tie-point refinement
* refined coordinate generation
* support for final transform refitting

Conceptually:

```text
Verified Inliers
      ↓
Refinement
      ↓
Refined Tie Points
      ↓
Final Transform Refit
```

Canonical V1 normally excludes advanced sub-pixel refinement according to `V1_SCOPE.md`.

The refinement implementation should not be copied into multiple benchmark-version folders.

---

# 19. Registration Ownership

Registration code should own application of an accepted transformation.

Potential responsibilities include:

* coordinate transformation
* image warping
* registered output generation
* overlap transformation

Registration should not own:

* scientific acceptance criteria
* benchmark aggregation
* frontend display

Warping an image does not prove the transform is valid.

---

# 20. Evaluation Ownership

Authoritative scientific metrics require one clear implementation owner within the reusable scientific/evaluation area.

Potential responsibilities include:

* reprojection residuals
* source-image pixel error
* check-point RMSE
* inlier count
* inlier ratio
* spatial coverage
* grid coverage
* convex-hull coverage
* retrieval metrics where applicable
* runtime aggregation where scientifically defined

Do not duplicate metric formulas independently in:

* frontend components
* API routes
* benchmark scripts
* notebooks

Benchmarks should call the authoritative evaluation implementation.

---

# 21. Quality and Failure Ownership

Where explicit quality logic exists, it should have clear ownership in the scientific/domain layer.

Potential responsibilities include:

* transform validity
* quality gates
* accept/refine/reject decisions
* scientific failure classification

Threshold values should normally come from benchmark/configuration definitions.

Do not hard-code benchmark-specific thresholds in unrelated application modules.

---

# 22. Domain Types and Contracts

Conceptual domain information includes:

* scientific image/product metadata
* candidate correspondences
* verified inliers
* transformations
* registration results
* evaluation results
* benchmark results

Their actual implementation may live in:

* reusable package types/models
* `contracts/`
* schemas
* another existing repository area

The current repository must be inspected before assigning exact ownership.

Do not invent classes such as:

```text
ImageProduct
RegistrationResult
BenchmarkRun
```

unless those classes actually exist.

---

# 23. Contracts Area

The repository contains a top-level:

```text
contracts/
```

Its exact role must be determined from its current contents.

Possible responsibilities may include:

* result contracts
* API schemas
* shared interfaces
* data contracts

Do not assume a specific framework or schema technology from the directory name alone.

If public result structures are defined there, adapters should reuse them rather than defining incompatible result formats independently.

---

# 24. Result Schema Ownership

Registration result semantics should ideally have one canonical owner.

Preferred conceptual flow:

```text
Scientific Pipeline
        ↓
Domain Result
        ↓
┌──────────────┬──────────────┬──────────────┐
│ Benchmark    │ API Adapter  │ UI Consumer  │
└──────────────┴──────────────┴──────────────┘
```

Avoid:

```text
Core Result A
API Result B
Benchmark Result C
Frontend Result D
```

all assigning different meanings to the same scientific fields.

Serialization adapters may change representation without changing scientific meaning.

---

# 25. Benchmark Architecture

The repository contains:

```text
benchmarks/
```

This area should own controlled scientific comparison rather than reusable algorithm implementations.

Potential responsibilities include:

* benchmark definitions
* pair manifests
* V1–V4 composition
* benchmark-specific configuration selection
* benchmark runners
* result aggregation
* comparison/report generation

It should not duplicate core implementations of:

* SIFT
* LightGlue
* LoFTR
* RANSAC
* affine/homography estimation
* metric equations

when those responsibilities already belong to reusable scientific code.

---

# 26. Benchmark Composition Model

Prefer:

```text
                   Shared Scientific Core
                           │
              ┌────────────┼────────────┐
              │            │            │
              ▼            ▼            ▼
          V1 Config    V2 Config    V3 Config
              │            │            │
              ▼            ▼            ▼
          Benchmark     Benchmark    Benchmark
             Run           Run          Run

                           │
                           ▼
                       V4 Config
                           │
                           ▼
                       Benchmark
                          Run
```

Low-level algorithms generally should not contain logic such as:

```text
if benchmark_version == "V3":
    ...
```

throughout the core.

Version awareness should primarily exist in composition/configuration.

---

# 27. V1 Composition

Canonical V1 should compose shared reusable functionality approximately as:

```text
Input Handling
      ↓
Minimal Preprocessing
      ↓
SIFT / Canonical RootSIFT Configuration
      ↓
Classical Descriptor Matching
      ↓
RANSAC / Geometry
      ↓
Affine / Homography
      ↓
Registration
      ↓
Evaluation
```

V1 should not own private copies of those algorithms merely because it is the first benchmark configuration.

`V1_SCOPE.md` remains authoritative for exact V1 boundaries.

---

# 28. V2 Composition

V2 may additionally compose shared capabilities for:

* sensor identification/routing
* sensor-aware preprocessing
* physical scale/GSD handling
* reference pyramids
* structural representations
* IIRS-derived 2D representations

V2 should extend reusable core capabilities rather than duplicate V1 implementation.

---

# 29. V3 Composition

V3 may additionally compose:

* ALIKED + LightGlue
* LoFTR
* remote-sensing correspondence methods
* global descriptors
* reference retrieval
* vector indexing
* FAISS where selected
* Top-K candidate handling

Retrieval and advanced matchers should remain reusable components rather than being embedded only inside a V3 benchmark runner.

---

# 30. V4 Composition

V4 may additionally compose research capabilities such as:

* advanced tie-point refinement
* local/piecewise geometry
* DEM-aware processing
* uncertainty estimation
* confidence calibration
* advanced quality decisions
* matcher-selection research
* failure classification

Only implemented or explicitly approved target responsibilities should receive concrete module paths.

---

# 31. Conceptual Benchmark Ownership Matrix

This table describes **reuse and architectural scope**, not current implementation status.

| Capability              | Shared Scientific Responsibility |      V1 |                V2 |                V3 |       V4 |
| ----------------------- | -------------------------------: | ------: | ----------------: | ----------------: | -------: |
| Input validation        |                              Yes |       ✓ |                 ✓ |                 ✓ |        ✓ |
| Metadata handling       |                              Yes |       ✓ |                 ✓ |                 ✓ |        ✓ |
| Generic preprocessing   |                              Yes |       ✓ |                 ✓ |                 ✓ |        ✓ |
| SIFT baseline           |                              Yes |       ✓ |                 ✓ |        comparison | optional |
| Sensor-aware processing |                              Yes | limited |                 ✓ |                 ✓ |        ✓ |
| Scale/GSD handling      |                              Yes | limited |                 ✓ |                 ✓ | advanced |
| Reference pyramid       |                              Yes |       — |                 ✓ |                 ✓ |        ✓ |
| Learned matcher         |                              Yes |       — | optional research |                 ✓ |        ✓ |
| Global retrieval        |                              Yes |       — | optional research |                 ✓ | advanced |
| RANSAC / geometry       |                              Yes |       ✓ |                 ✓ |                 ✓ |        ✓ |
| Registration            |                              Yes |       ✓ |                 ✓ |                 ✓ |        ✓ |
| Evaluation              |                              Yes |       ✓ |                 ✓ |                 ✓ |        ✓ |
| Advanced refinement     |             Yes when implemented |       — |          optional | optional/research |        ✓ |
| DEM-aware geometry      |             Yes when implemented |       — |                 — |      experimental | research |

`✓` means the capability belongs conceptually to that benchmark scope, not that it is currently implemented.

---

# 32. Configuration Ownership

The repository contains:

```text
configs/
```

Configuration should select behavior.

Code should implement behavior.

Conceptually:

```text
Configuration
    matcher = SIFT
        ↓
Scientific Core
    SIFT implementation
```

Avoid using configuration files as a place for substantial executable algorithm logic.

Potential configuration areas may include:

* benchmark configuration
* matcher selection
* preprocessing
* geometry
* retrieval
* sensor handling
* experiment configuration

Exact filenames and schema ownership must be verified from the repository.

---

# 33. Data Area

The repository contains:

```text
data/
```

Its exact internal structure must be inspected before making assumptions.

Potential responsibilities may include:

* manifests
* metadata records
* small test data
* data conventions
* references to external scientific products

Do not assume directories such as:

```text
data/raw/
data/interim/
data/processed/
```

exist unless confirmed.

Large mission products should not automatically be treated as normal Git-tracked repository content.

---

# 34. Experiments

The repository contains:

```text
experiments/
```

This area should support reproducible experiments that investigate questions such as:

* matcher comparison
* preprocessing comparison
* scale handling
* illumination stress
* IIRS representation
* transformation model comparison
* ablation studies

Experiments may depend on the scientific core.

Stable core code should not depend on experiment implementations.

---

# 35. Research

The repository contains:

```text
research/
```

Its exact distinction from `experiments/` must follow the actual repository's conventions.

Possible responsibilities may include:

* literature-driven prototypes
* early research implementations
* research notes
* exploratory algorithm development

Do not move stable functionality into `research/` merely because it is scientifically sophisticated.

Do not move experimental functionality into the stable core merely because one test case succeeds.

---

# 36. Notebooks

The repository contains:

```text
notebooks/
```

Appropriate responsibilities include:

* dataset inspection
* visualization
* exploratory analysis
* experiment review
* result analysis

Notebooks should not become the only implementation of reusable scientific functionality.

Preferred progression:

```text
Notebook Prototype
        ↓
Evidence / Stabilization
        ↓
Reusable Core Module
        ↓
Tests
```

when promotion is justified.

---

# 37. Scripts

The repository contains:

```text
scripts/
```

Scripts should generally remain thin orchestration or maintenance entry points.

Potential responsibilities include:

* dataset preparation
* benchmark invocation
* retrieval-index preparation
* result inspection
* repository/data validation
* migration utilities

Avoid embedding substantial reusable scientific algorithms directly in scripts.

Preferred:

```text
Script
  ↓
Reusable Core Functionality
```

not:

```text
Script
  ↓
Complete Independent Registration Implementation
```

---

# 38. Services

The repository contains:

```text
services/
```

Exact service implementations must be inspected before documentation assigns concrete ownership.

Potential service responsibilities may include:

* transport/application integration
* request validation
* job orchestration
* invoking scientific functionality
* artifact delivery
* result serialization

Service code should call the scientific core.

It should not contain separate SIFT, LightGlue, RANSAC, or RMSE implementations.

---

# 39. Applications

The repository contains:

```text
apps/
```

Possible application responsibilities include:

* frontend interfaces
* dashboards
* local demonstrations
* other user-facing application composition

Applications consume scientific results.

They should not own authoritative registration algorithms.

---

## 39.1 Frontend Ownership

If a frontend exists, it may own:

* image visualization
* match overlays
* verified-inlier visualization
* residual visualization
* metric display
* benchmark tables
* map rendering
* interaction state

It should not independently calculate authoritative:

* RANSAC inliers
* transforms
* RMSE
* spatial coverage
* scientific confidence

unless the repository explicitly adopts client-side scientific computation for a justified architecture.

---

# 40. Deployment

The repository contains:

```text
deploy/
```

Deployment-related code/configuration should remain separated from scientific implementation.

Potential responsibilities depend on actual repository contents.

Do not infer:

* Kubernetes
* cloud platform
* container topology
* queue system
* database architecture

from the directory name.

---

# 41. GitHub Repository Automation

The repository contains:

```text
.github/
```

It may hold:

* workflows
* issue templates
* pull request templates
* repository automation

Exact CI behavior must be determined from the actual workflow files.

Scientific core modules should not depend on GitHub Actions configuration.

---

# 42. Tests

The repository contains:

```text
tests/
```

Tests should follow production responsibilities where practical.

Potential categories include:

* data/input tests
* metadata tests
* preprocessing tests
* matching tests
* geometry tests
* registration tests
* evaluation tests
* retrieval tests
* integration tests
* regression tests

Exact paths must come from the current tree.

---

## 42.1 Test Ownership Matrix

| Production Responsibility | Test Responsibility               |
| ------------------------- | --------------------------------- |
| Scientific data loading   | loading/input-validation tests    |
| Metadata processing       | metadata/convention tests         |
| Preprocessing             | deterministic preprocessing tests |
| Scale handling            | scale/GSD behavior tests          |
| Retrieval                 | descriptor/index/candidate tests  |
| Feature extraction        | feature behavior/edge-case tests  |
| Matching                  | candidate-correspondence tests    |
| Geometry                  | RANSAC/transform/degeneracy tests |
| Refinement                | tie-point/refit tests             |
| Registration              | warp/coordinate tests             |
| Evaluation                | metric mathematics tests          |
| Quality logic             | acceptance/rejection tests        |
| Full scientific flow      | integration tests                 |
| Fixed discovered defects  | regression tests                  |

Tests should consume production functionality.

Production code must not depend on tests.

---

# 43. Results

The repository contains:

```text
results/
```

Its exact policy must follow current repository conventions.

Potential generated content may include:

* benchmark result records
* experiment summaries
* metric tables
* run metadata

Results are generated scientific outputs.

They are not reusable source-code modules.

---

# 44. Artifacts

The repository contains:

```text
artifacts/
```

Potential generated outputs may include:

* registered imagery
* match plots
* overlays
* residual plots
* reports
* retrieval indexes
* derived descriptors

Actual policy must be verified from repository documentation/configuration.

Artifacts must not become the authoritative implementation of scientific logic.

---

# 45. Documentation

The repository contains:

```text
docs/
```

This is the natural home for human-facing project documentation according to the repository's established structure.

Potential documentation responsibilities include:

* architecture
* sensors
* pipelines
* usage
* development
* benchmarks
* scientific interpretation

Do not place production algorithms in documentation directories.

---

# 46. AI Context

The repository contains:

```text
.ai/
```

`.ai/` is AI-agent-oriented repository context.

It may contain areas for:

* project context
* architecture
* development rules
* benchmark context
* research context
* workflows

Production software must not depend on `.ai/` files for ordinary runtime behavior unless the project explicitly introduces such a mechanism.

This `MODULE_MAP.md` belongs to:

```text
.ai/architecture/
```

---

# 47. Dependency Direction

Prefer dependency direction conceptually as:

```text
apps / services / scripts
          ↓
application orchestration
          ↓
scientific core
          ↓
domain/data interfaces
          ↓
external scientific libraries
```

Additional consumers:

```text
benchmarks
    ↓
scientific core

experiments / research
    ↓
scientific core

tests
    ↓
testable production modules
```

Avoid upward dependencies.

---

## 47.1 Dependency Matrix

| Module Area     | May Depend On                                                              | Should Avoid Depending On                           |
| --------------- | -------------------------------------------------------------------------- | --------------------------------------------------- |
| Scientific core | domain/data utilities, scientific libraries, explicit configuration values | frontend, HTTP request objects, benchmark manifests |
| Data/I/O        | file/geospatial/scientific parsing libraries                               | UI, benchmark scoring                               |
| Matching        | prepared representations, feature interfaces                               | frontend, dataset-download workflow                 |
| Geometry        | candidate correspondences, numerical utilities                             | UI, API routes, retrieval database                  |
| Evaluation      | scientific results/transforms/correspondences                              | frontend state                                      |
| Benchmarks      | scientific core, benchmark config, dataset manifests                       | duplicated scientific implementations               |
| Experiments     | core, benchmark utilities where useful                                     | becoming a dependency of stable core                |
| Services        | scientific core, contracts, application configuration                      | duplicate algorithms                                |
| Apps/UI         | result/API contracts                                                       | direct ownership of CV internals                    |
| Scripts         | reusable core and tooling                                                  | reusable algorithms defined only inside script      |
| Tests           | production modules and fixtures                                            | production code importing tests                     |

---

# 48. No Circular Dependency Principle

Avoid cycles such as:

```text
matching
  ↓
geometry
  ↓
matching
```

or:

```text
scientific core
      ↓
service
      ↓
scientific core
```

without a deliberately designed interface boundary.

Circular dependencies often indicate unclear ownership.

---

# 49. Configuration Dependency

Low-level scientific modules should receive the values they need.

They generally should not need to know:

> This run is Benchmark V3.

For example:

```text
Benchmark Composition
        ↓
RANSAC Configuration
        ↓
Geometry Module
```

The geometry implementation only needs the configuration required to perform geometry.

This keeps core components reusable.

---

# 50. Benchmark-Version Knowledge

Version knowledge should primarily live in:

* benchmark specifications
* benchmark composition
* benchmark configuration

Avoid scattering:

```python
if version == "V1":
    ...
elif version == "V2":
    ...
```

through unrelated scientific modules.

A low-level SIFT implementation should not need to know that V1 selected it.

---

# 51. Result Flow Ownership

Prefer:

```text
Scientific Modules
        ↓
Canonical Domain Result
        ↓
┌────────────────┬────────────────┬────────────────┐
│ Benchmark      │ Service/API    │ Application/UI │
│ Recorder       │ Adapter        │ Consumer       │
└────────────────┴────────────────┴────────────────┘
```

Adapters may transform representation.

They should preserve scientific meaning.

---

# 52. Error Ownership

Expected scientific failures should originate close to the scientific responsibility that detects them.

Example:

```text
Matcher
→ insufficient candidate correspondences

Geometry
→ no stable transformation

Evaluation
→ quality rejection
```

Transport/application layers may translate those outcomes into:

* CLI output
* API response
* UI message

The API should not invent scientific failure semantics.

---

# 53. Logging Ownership

Scientific modules may emit useful process/domain logs according to repository conventions.

Application/bootstrap code should normally control:

* handlers
* destinations
* verbosity
* global logging configuration

Avoid every scientific module independently configuring global logging.

---

# 54. Storage Ownership

Scientific algorithms should generally produce results and artifacts rather than hard-coding one storage mechanism.

Avoid unnecessary direct dependencies from algorithms to:

* a specific database
* cloud object storage
* deployment filesystem layout

unless persistence is genuinely part of the module's responsibility.

Orchestration/adapters should decide where outputs are persisted.

---

# 55. Model and Checkpoint Ownership

If learned models are introduced, model loading and checkpoint handling should have one clear owner.

Avoid duplicating checkpoint logic across:

* benchmark runners
* API services
* notebooks
* individual scripts

Preserve information such as:

* model identity
* checkpoint identity
* preprocessing requirements
* relevant configuration

where applicable.

---

# 56. Dataset Acquisition Ownership

Dataset acquisition must remain separate from registration algorithms.

Conceptually:

```text
Dataset Acquisition / Preparation
        ↓
Local Scientific Product
        ↓
Scientific Loader
        ↓
Registration Pipeline
```

A matcher should not download LRO or Chandrayaan products itself.

---

# 57. Coordinate Convention Ownership

Coordinate conventions are high-risk scientific infrastructure.

Avoid independently redefining:

* `(x, y)`
* `(row, column)`
* pixel center conventions
* source/reference transform direction
* longitude normalization
* projected coordinate handling

across unrelated modules.

Where the current repository centralizes these conventions, use that owner.

If it does not yet have a clear owner and a change requires one, introduce the smallest justified centralization rather than copying new conversions into multiple areas.

---

# 58. Metric Ownership

Scientific metrics should have one authoritative implementation.

Avoid:

```text
Benchmark RMSE implementation
Frontend RMSE implementation
Notebook RMSE implementation
API RMSE implementation
```

all using slightly different formulas.

Preferred:

```text
Scientific Evaluation
        ↓
Canonical Metric Result
        ↓
Benchmark / API / UI
```

Metric definitions should remain aligned with dedicated metrics documentation.

---

# 59. Matcher Ownership

If multiple matchers exist, keep their implementations under a coherent correspondence/matching responsibility.

Conceptually:

```text
Matching Responsibility
├── Classical matcher
├── LightGlue-based matcher
├── LoFTR matcher
└── Remote-sensing matcher
```

Do not scatter matchers across benchmark version directories merely because different versions first introduce them.

---

# 60. Utilities and Shared Code

Be cautious with broad modules such as:

```text
utils.py
helpers.py
common/
```

These can become architectural dumping grounds.

Prefer responsibility-specific ownership when functionality becomes substantial.

However, do not split every trivial helper into a new module.

Shared code should represent genuinely shared behavior.

---

# 61. Public vs Internal Modules

If the current repository establishes public interfaces, preserve them.

Potential public concepts may eventually include:

* registration entry points
* result contracts
* benchmark execution interfaces

Internal implementation details may include:

* private numerical helpers
* matcher-specific adapters
* caching helpers

Do not invent a public API boundary where the repository does not yet define one.

---

# 62. Entry Points

Actual entry points must be discovered from the repository.

Potential categories include:

* Python package entry
* CLI
* benchmark runner
* backend application
* frontend application
* data-preparation scripts

Conceptual flow should remain:

```text
CLI
  ↓
Orchestration
  ↓
Scientific Core
```

```text
Benchmark Runner
        ↓
Benchmark Composition
        ↓
Scientific Core
```

```text
API
 ↓
Service Adapter
 ↓
Scientific Core
```

```text
UI
 ↓
Application / Result Contract
```

Do not invent exact commands or filenames.

---

# 63. Module Ownership Matrix

Because exact internal `src/chandramap/` submodule names must be verified from the repository, this table identifies **architectural ownership areas** rather than invented package paths.

| Responsibility             | Primary Architectural Owner                                   | Collaborators                    | Must Not Primarily Live In           |
| -------------------------- | ------------------------------------------------------------- | -------------------------------- | ------------------------------------ |
| Scientific product loading | `src/chandramap/` data/I/O responsibility                     | `data/`, metadata                | UI, benchmark runner                 |
| Metadata interpretation    | `src/chandramap/` metadata responsibility                     | dataset I/O, sensor handling     | frontend                             |
| Sensor handling            | `src/chandramap/` scientific core                             | metadata, preprocessing          | API routes                           |
| Preprocessing              | `src/chandramap/` scientific core                             | sensor, scale                    | UI, benchmark reporting              |
| Scale/GSD handling         | `src/chandramap/` scientific core                             | metadata, preprocessing          | frontend                             |
| Global retrieval           | `src/chandramap/` retrieval responsibility                    | data, metadata, benchmark config | geometry, UI                         |
| Feature extraction         | `src/chandramap/` feature responsibility                      | preprocessing                    | API routes                           |
| Candidate matching         | `src/chandramap/` matching responsibility                     | features                         | frontend                             |
| RANSAC / geometry          | `src/chandramap/` geometry responsibility                     | matching                         | UI, data loader                      |
| Transform logic            | `src/chandramap/` geometry/registration responsibility        | evaluation                       | benchmark runner copies              |
| Tie-point refinement       | `src/chandramap/` refinement responsibility where implemented | geometry, registration           | frontend                             |
| Registration / warp        | `src/chandramap/` registration responsibility                 | geometry                         | UI science logic                     |
| Scientific metrics         | `src/chandramap/` evaluation responsibility                   | benchmarks                       | frontend                             |
| Quality/rejection          | `src/chandramap/` scientific/domain responsibility            | evaluation, config               | API inventing criteria               |
| Benchmark composition      | `benchmarks/`                                                 | configs, core                    | low-level algorithm modules          |
| Research experiments       | `experiments/`, `research/` according to current conventions  | core, benchmarks                 | stable core depending on experiments |
| Application transport      | `services/`                                                   | contracts, core                  | duplicated CV algorithms             |
| User-facing application    | `apps/`                                                       | services/contracts/results       | authoritative geometry/metrics       |
| Automated tests            | `tests/`                                                      | all production responsibilities  | production modules importing tests   |
| Generated results          | `results/`                                                    | benchmarks, experiments          | source package                       |
| Generated artifacts        | `artifacts/`                                                  | scientific/application workflows | source package                       |

---

# 64. Change Impact Map

Some responsibility areas have a larger scientific blast radius.

| Change Area                      | Likely Impact                                                  |
| -------------------------------- | -------------------------------------------------------------- |
| Dataset metadata normalization   | loaders, preprocessing, scale handling, provenance, benchmarks |
| Sensor identification            | sensor routing, preprocessing, benchmark interpretation        |
| Coordinate conventions           | matching, geometry, transforms, metrics, visualization         |
| Matcher interface                | multiple benchmark versions, geometry integration, tests       |
| RANSAC / geometry                | V1–V4 registration behavior and benchmark comparability        |
| Transformation direction         | warp, evaluation, serialization, visualization                 |
| Metric definitions               | historical benchmark comparability and reports                 |
| Result contracts                 | scientific core, benchmark tooling, services, UI               |
| Benchmark manifests              | benchmark population and comparability                         |
| Preprocessing                    | matcher behavior and benchmark methodology                     |
| Retrieval descriptor/index logic | V3+ retrieval performance and candidate selection              |

Treat changes in these areas carefully.

---

# 65. High-Risk Responsibilities

The following conceptual responsibilities deserve additional review because errors may affect many downstream results:

* coordinate ordering
* transformation direction
* planetary coordinate conversion
* metadata normalization
* GSD/unit handling
* metric equations
* fit-point/check-point separation
* benchmark manifests
* canonical result schemas
* quality/rejection semantics

A small code change in one of these areas can materially alter benchmark interpretation.

---

# 66. Current vs Target Module Map

## Current Repository Areas

The current project context documents major repository areas including:

* `.ai/`
* `.github/`
* `apps/`
* `artifacts/`
* `benchmarks/`
* `configs/`
* `contracts/`
* `data/`
* `deploy/`
* `docs/`
* `experiments/`
* `notebooks/`
* `research/`
* `results/`
* `scripts/`
* `services/`
* `src/chandramap/`
* `tests/`

This establishes high-level repository ownership areas.

It does **not** establish exact internal module names or implementation completeness.

---

## Target Responsibility Mapping

Where a scientific responsibility is not yet clearly separated, the preferred architectural home is:

```text
Reusable scientific behavior
→ src/chandramap/

Benchmark-specific composition
→ benchmarks/

Configuration
→ configs/

Research/prototypes
→ experiments/ or research/

Interactive analysis
→ notebooks/

Backend/application adapters
→ services/

User-facing applications
→ apps/

Tests
→ tests/

Generated metrics/results
→ results/

Generated visual/binary artifacts
→ artifacts/
```

This is an ownership rule, not permission to create new paths without first checking existing structure.

---

## Experimental Areas

Experimental functionality should remain identifiable through:

* its repository location
* documentation
* benchmark configuration
* explicit status

Experimental code should not silently redefine stable behavior.

---

## Planned Modules

Planned architecture concepts such as:

* global retrieval
* DEM-aware geometry
* advanced refinement
* matcher selection
* uncertainty calibration

must not be represented as existing source modules unless those paths are present in the repository.

---

# 67. Where New Code Should Go

Use responsibility first, then confirm the actual existing module.

| Need to Change / Add             | Inspect First                                                     |
| -------------------------------- | ----------------------------------------------------------------- |
| Scientific image loading         | data/I/O ownership inside `src/chandramap/`, then `data/` context |
| Product metadata handling        | metadata ownership inside `src/chandramap/`                       |
| OHRC/TMC-2/IIRS preprocessing    | sensor/preprocessing ownership inside `src/chandramap/`           |
| Scale/GSD handling               | scale/preprocessing scientific ownership                          |
| SIFT / RootSIFT                  | feature/matching scientific ownership                             |
| ALIKED feature integration       | feature ownership                                                 |
| LightGlue                        | matching ownership                                                |
| LoFTR                            | matching/correspondence ownership                                 |
| FAISS retrieval                  | retrieval/indexing ownership                                      |
| RANSAC                           | geometry ownership                                                |
| Affine/homography                | geometry/transform ownership                                      |
| Sub-pixel refinement             | refinement ownership                                              |
| Image warp                       | registration ownership                                            |
| RMSE                             | evaluation/metrics ownership                                      |
| Spatial coverage                 | evaluation/metrics ownership                                      |
| Accept/reject logic              | quality/evaluation scientific ownership                           |
| V1–V4 composition                | `benchmarks/` and relevant configuration                          |
| New experiment                   | `experiments/` or `research/` according to existing convention    |
| Exploratory visualization        | `notebooks/`                                                      |
| API/service integration          | `services/`                                                       |
| User interface                   | `apps/`                                                           |
| Operational command              | `scripts/` or existing CLI ownership                              |
| Unit/integration/regression test | `tests/` near matching production responsibility                  |
| Generated benchmark output       | `results/` according to repository policy                         |
| Generated image/report           | `artifacts/` according to repository policy                       |

Before creating anything new, search for an existing owner.

---

# 68. AI-Agent Repository Navigation

Use this map to minimize unnecessary repository loading.

Preferred workflow:

```text
Task
  ↓
Identify Responsibility
  ↓
Find Primary Repository Area
  ↓
Inspect Actual Module Tree
  ↓
Inspect Nearby Tests
  ↓
Inspect Relevant Config / Contract
  ↓
Inspect Collaborators Only If Needed
  ↓
Make Smallest Correct Change
```

Example:

```text
Task:
Fix RANSAC transform-direction bug
        ↓
Primary responsibility:
Geometry
        ↓
Inspect:
actual geometry modules
        +
nearby geometry tests
        +
coordinate/transform conventions
        ↓
Do NOT begin by scanning:
frontend + deploy + notebooks + entire repository
```

---

# 69. When to Extend an Existing Module

Prefer extending an existing module when:

* it already clearly owns the responsibility
* the new behavior is a small variant
* the existing interface remains coherent
* creating another module would duplicate structure

Examples conceptually:

* another geometric validity check in the existing geometry owner
* another coverage metric in the evaluation owner

---

# 70. When to Create a New Module

Create a module when the functionality has:

* a distinct responsibility
* enough complexity to justify separation
* independent testing value
* reuse potential
* a stable conceptual boundary

Before creating one, ask:

* Does an existing module already own this?
* Is this stable enough to deserve a module?
* Is this research rather than core functionality?
* Will another benchmark version reuse it?
* Does the new module create a dependency cycle?
* Can the change remain smaller?

Do not create one module per tiny helper.

---

# 71. When to Split a Module

Consider splitting when a module:

* owns unrelated responsibilities
* mixes I/O with algorithms and presentation
* has multiple independent reasons to change
* becomes difficult to test in isolation
* has turned into a generic dumping ground

Do not split a module purely because it has many lines.

Responsibility matters more than raw size.

---

# 72. Module Naming

Prefer names describing responsibility.

Good conceptual names include:

```text
matching
geometry
registration
evaluation
retrieval
preprocessing
```

Avoid vague names such as:

```text
misc
stuff
new
temp
final
helpers2
```

unless existing repository conventions already define meaningful use.

---

## 72.1 Benchmark Versions Should Not Name Reusable Algorithms

Avoid naming reusable scientific implementations:

```text
sift_v1.py
lightglue_v3.py
ransac_v2.py
```

solely because a benchmark version first uses them.

Prefer algorithm/responsibility naming.

Benchmark composition determines which method is active.

---

# 73. Module Boundary Anti-Patterns

## 73.1 God Module

Avoid one module owning:

```text
Data Loading
+
Preprocessing
+
Matching
+
Geometry
+
Metrics
+
Storage
```

---

## 73.2 Duplicate Matcher Logic

Avoid separate copies of the same matcher inside:

```text
V1
V2
V3
V4
```

---

## 73.3 Benchmark-Core Mixing

Reusable scientific algorithms should not contain benchmark pair selection.

---

## 73.4 Frontend Metric Duplication

Do not reimplement RMSE, inlier ratio, or coverage in the UI.

---

## 73.5 API Algorithm Duplication

Do not implement SIFT/RANSAC independently inside an API route.

---

## 73.6 Notebook Dependency

Production code should not import critical behavior from exploratory notebooks.

---

## 73.7 Utility Dumping Ground

Do not allow a generic helper module to accumulate:

* coordinate conversion
* metrics
* geometry
* metadata
* file I/O

without clear ownership.

---

## 73.8 Circular Dependencies

Avoid:

```text
geometry → matching → geometry
```

and:

```text
core → services → core
```

---

## 73.9 Dataset Coupling

A matcher should not directly open hard-coded mission paths.

Preferred:

```text
Dataset Layer
      ↓
Prepared Representation
      ↓
Matcher
```

---

## 73.10 Version-Folder Duplication

Avoid:

```text
src/chandramap/v1/
src/chandramap/v2/
src/chandramap/v3/
src/chandramap/v4/
```

each containing copied complete pipelines unless the implementations are genuinely independent and duplication is scientifically justified.

---

# 74. Module Dependency Rules

1. Scientific core modules must not depend on frontend modules.

2. Scientific core modules should not depend on HTTP request/response objects.

3. Dataset modules may depend on appropriate file, scientific, and geospatial libraries.

4. Matching may consume prepared representations but should not own dataset acquisition.

5. Geometry consumes candidate correspondences and should not depend on UI state.

6. Evaluation consumes scientific results and should not depend on presentation components.

7. Benchmarks may depend on scientific core modules.

8. Scientific core modules should not depend on benchmark manifests.

9. Research and experiments may depend on stable core modules.

10. Stable core modules should not depend on experimental research implementations.

11. Scripts should orchestrate reusable code rather than contain the canonical implementation.

12. Tests depend on production modules; production modules do not depend on tests.

13. Services may depend on scientific core and contracts.

14. Scientific core must not depend on services merely to execute.

15. Applications may consume services/contracts/results.

16. Scientific result semantics must not depend on frontend state.

17. Configuration should select behavior rather than implement algorithms.

18. Generated results and artifacts must not become dependencies of source implementation except through explicitly designed data workflows.

---

# 75. Generated Files

If the repository contains generated modules or generated contracts, identify their generator/source before editing them.

Do not manually edit generated code when a source template/schema/generator is authoritative.

This document does not assume any generated-code system currently exists.

---

# 76. Deprecated Modules

If a module becomes deprecated, document:

* its status
* its replacement
* whether new code may depend on it
* migration expectations where needed

Do not label modules deprecated without repository evidence.

---

# 77. Moving Responsibilities

When moving a module or responsibility, inspect:

* imports
* tests
* configuration
* scripts
* benchmark runners
* services
* applications
* documentation
* public interfaces
* serialized data where relevant

Avoid half-completed architecture migrations where two locations both appear authoritative.

---

# 78. Maintaining the Module Map

Update `MODULE_MAP.md` when:

* a major package/module is added
* responsibility moves between modules
* stable/research ownership changes
* benchmark composition changes materially
* services/apps are added or removed
* contract/result ownership changes
* important modules are deprecated
* repository package boundaries change

Do not update this document for every helper function.

The map should remain concise enough to navigate.

---

# 79. Related Architecture Documents

`SYSTEM_OVERVIEW.md`
→ defines the major logical subsystems

`PIPELINE.md`
→ defines the ordered scientific processing flow

`MODULE_MAP.md`
→ defines where those responsibilities belong in the repository

`DATA_FLOW.md`, when present
→ defines what information crosses the boundaries described here

`../context/PROJECT_CONTEXT.md`
→ defines the overall ChandraMap project

`../context/DOMAIN_CONTEXT.md`
→ defines the scientific constraints affecting modules

`../context/TERMINOLOGY.md`
→ defines canonical project terminology

`../context/DATASETS.md`
→ defines scientific product/data responsibilities

`../context/V1_SCOPE.md`
→ defines canonical Benchmark V1 scope

`../ENGINEERING_RULES.md`
→ defines how agents should modify the repository safely

Detailed testing rules, when present, should define testing conventions beyond the ownership guidance in this document.

---

# 80. Key Module-Map Rules for AI Agents

1. Inspect the real repository tree before assuming module ownership.

2. Do not invent packages, directories, classes, or functions.

3. Use this document to identify the likely responsibility area, then verify the actual path.

4. Each significant scientific responsibility should have a clear primary owner.

5. Reusable scientific algorithms belong in the scientific core.

6. UI modules must not own authoritative scientific computation.

7. API/service layers should orchestrate the scientific core rather than reimplement it.

8. Benchmark runners should compose reusable modules rather than duplicate algorithms.

9. Benchmark V1–V4 should reuse shared scientific components where appropriate.

10. Do not copy the complete pipeline into four benchmark-version implementations without justification.

11. Low-level algorithms generally should not know which benchmark version selected them.

12. Version-specific behavior should preferably be defined through benchmark composition/configuration.

13. Dataset loaders should not perform feature matching.

14. Dataset acquisition should remain separate from matching algorithms.

15. Preprocessing should not own geometric verification.

16. Matching should produce candidate correspondences.

17. Geometry should own geometric verification and transformation estimation.

18. RANSAC belongs to geometry responsibility.

19. Registration/warping should remain separate from scientific evaluation.

20. Evaluation should own authoritative scientific metric computation.

21. Frontend code should display metrics rather than redefine them.

22. Metric formulas should have one authoritative implementation.

23. Retrieval should remain separate from local matching.

24. FAISS/vector indexing belongs to retrieval/indexing responsibility.

25. Global descriptors and local descriptors should not be treated as the same responsibility.

26. IIRS spectral representation requires clear ownership rather than being hidden inside an ordinary grayscale loader.

27. ALIKED feature extraction and LightGlue matching have different conceptual ownership.

28. LoFTR belongs under correspondence/matching responsibility rather than being treated as a conventional feature extractor.

29. Stable core must not depend on experimental/research modules.

30. Research and experiment areas may depend on stable core functionality.

31. Tests depend on production modules, not the reverse.

32. Scientific core must not depend on backend transport objects.

33. Scientific core must not depend on frontend components.

34. Scripts should orchestrate reusable code rather than own it.

35. Notebooks should not be the only location of important reusable algorithms.

36. Configuration selects scientific behavior; it should not implement algorithms.

37. Generated results and artifacts do not belong in source modules.

38. Result/schema ownership should remain centralized where practical.

39. Coordinate and transformation conventions should not be duplicated randomly.

40. Preserve explicit `source → reference` or `reference → source` transform semantics.

41. Avoid generic utility dumping grounds.

42. Avoid circular dependencies.

43. Do not over-split trivial functionality.

44. Create a new module only when it has a distinct responsibility and testing/reuse value.

45. Extend an existing owner when the responsibility already belongs there.

46. Benchmark code may depend on core; core should not depend on benchmark definitions.

47. Application code may depend on scientific results; scientific meaning should not depend on the application.

48. Do not invent database, cloud, queue, or service modules merely to make the repository appear enterprise-grade.

49. Keep current repository paths distinct from target/planned architectural homes.

50. Update this map when major ownership or package boundaries materially change.

51. Prefer clear ownership and controlled dependencies over a large number of folders.

52. The actual repository tree remains the final source of truth.

<!-- Source specification: :contentReference[oaicite:0]{index=0} -->
