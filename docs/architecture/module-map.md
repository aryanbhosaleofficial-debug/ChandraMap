# ChandraMap Module Map

This document maps ChandraMap's **logical architecture to its physical repository structure**.

Its purpose is to answer:

> **Where does a responsibility belong in the repository, and which areas should depend on which other areas?**

The most important rule is:

> **The repository is the source of truth. Do not create a new module until you have searched for an existing owner.**

This document intentionally distinguishes between:

- **confirmed repository locations**
- **logical architectural responsibilities**
- **canonical owners that are clearly identifiable**
- **responsibilities whose exact implementation owner has not yet been verified**
- **generated outputs**
- **research/experimental areas**

Where a narrower implementation path cannot be verified from the available repository structure, this document says so rather than inventing one.

---

## 1. Purpose

The module map connects two views of ChandraMap.

### Logical Architecture

Describes responsibilities such as:

- preprocessing
- matching
- geometric verification
- transformation
- registration
- evaluation
- retrieval
- benchmarking
- backend
- frontend

### Physical Architecture

Describes where those responsibilities actually live in the repository.

Conceptually:

```text
Logical Responsibility
        ↓
Actual Repository Owner
        ↓
Dependencies
        ↓
Tests / Configuration / Consumers
```

This document is therefore about:

- repository ownership
- navigation
- responsibility boundaries
- dependency direction

It is **not** a detailed pipeline or code reference.

---

## 2. Source-of-Truth Policy

The physical repository takes precedence over architectural intent.

If architecture documentation says:

> Geometry should be a separate subsystem.

but the current implementation still places geometry inside a broader source module, this document must describe the current repository honestly.

The correct approach is:

1. document current ownership
2. identify architectural ambiguity where verified
3. avoid inventing a future path
4. refactor only through deliberate repository work

Never fabricate paths simply because they would make the architecture look cleaner.

---

## 3. Repository Areas Confirmed for ChandraMap

The project structure available to this documentation set establishes the following major repository areas:

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

These directories provide the high-level physical boundaries used throughout this module map.

This is intentionally a **small architecture-relevant tree**, not a complete repository listing.

---

## 4. Root-Level Ownership

| Repository Area   | Primary Architectural Role                                                           |
| ----------------- | ------------------------------------------------------------------------------------ |
| `src/chandramap/` | Reusable ChandraMap scientific/application source package                            |
| `benchmarks/`     | Controlled benchmark definitions, runners, manifests, or benchmark support           |
| `configs/`        | Repository configuration, including scientific/benchmark configuration where defined |
| `contracts/`      | Shared contracts/schemas where established                                           |
| `tests/`          | Automated software validation                                                        |
| `research/`       | Research-oriented work that is not necessarily stable core                           |
| `experiments/`    | Controlled experimental implementations/runs                                         |
| `notebooks/`      | Interactive exploration, analysis, and visualization                                 |
| `scripts/`        | Executable orchestration and repository utilities                                    |
| `apps/`           | Application-layer code                                                               |
| `services/`       | Service/backend-oriented code                                                        |
| `data/`           | Scientific data organization/references according to repository policy               |
| `artifacts/`      | Generated scientific/application artifacts                                           |
| `results/`        | Generated or recorded result outputs                                                 |
| `deploy/`         | Deployment-related assets                                                            |
| `.github/`        | GitHub automation and repository workflow configuration                              |
| `docs/`           | Human-facing project documentation                                                   |
| `.ai/`            | AI-assisted engineering context and instructions                                     |

The existence of a directory alone does not prove that every intended responsibility beneath it is fully implemented.

---

# 5. Logical Architecture vs Physical Ownership

The logical system architecture is approximately:

```text
Applications / Services / Scripts / Benchmarks
                     ↓
                Orchestration
                     ↓
             Scientific Core
                     ↓
      Domain / Data / Result Concepts
                     ↓
       External Scientific Libraries
```

The physical repository provides these major anchors:

```text
apps/ ───────────────┐
services/ ───────────┤
scripts/ ────────────┤
benchmarks/ ─────────┼────→ src/chandramap/
research/ ───────────┤
experiments/ ────────┘

tests/
   └──────────────────────→ tests relevant repository code

configs/
   └──────────────────────→ configures supported workflows

contracts/
   └──────────────────────→ shared boundaries where defined

artifacts/ + results/
   ←────────────────────── generated outputs
```

The exact internal module relationships inside `src/chandramap/` must be determined from actual source/import inspection rather than inferred from this high-level tree.

---

# 6. Primary Ownership Map

The following table distinguishes confirmed repository areas from responsibilities whose narrower implementation owner has not yet been established here.

| Responsibility                 | Current Repository Location                        | Ownership Interpretation                                                 | Dependency Notes                                                            |
| ------------------------------ | -------------------------------------------------- | ------------------------------------------------------------------------ | --------------------------------------------------------------------------- |
| Reusable scientific source     | `src/chandramap/`                                  | Confirmed primary source-package area                                    | Should remain reusable by outer layers                                      |
| Scientific orchestration       | `src/chandramap/` and/or `scripts/`                | Exact canonical implementation file not asserted here                    | Scripts should invoke reusable code rather than own algorithms              |
| Input/data access              | `src/chandramap/`, `data/`                         | Data area confirmed; exact loader module requires source inspection      | Should not own matching/geometry                                            |
| Metadata handling              | `src/chandramap/`                                  | Narrow owner not verified here                                           | Should preserve scientific provenance                                       |
| Validation                     | `src/chandramap/`                                  | Narrow owner not verified here                                           | Scientific validation belongs inward of application boundaries              |
| Preprocessing                  | `src/chandramap/`                                  | Narrow canonical implementation not identified here                      | Should remain reusable across workflows                                     |
| Sensor-specific representation | `src/chandramap/`, possibly research areas         | Exact canonical owner not verified                                       | Do not assume per-sensor modules exist                                      |
| Scale handling                 | `src/chandramap/` or research/experimental areas   | Narrow owner not verified                                                | Must remain distinct from retrieval/matching where architecture supports it |
| Local feature extraction       | `src/chandramap/`                                  | Narrow matcher/feature module not verified                               | Shared scientific capability                                                |
| Local matching                 | `src/chandramap/`                                  | Narrow canonical module not identified here                              | Should produce candidate correspondences                                    |
| Candidate filtering            | `src/chandramap/`                                  | Narrow owner not verified                                                | Should precede geometric verification                                       |
| Geometric verification         | `src/chandramap/`                                  | Narrow RANSAC/geometry owner not verified                                | Should not belong to frontend/backend transport                             |
| Transform estimation           | `src/chandramap/`                                  | Narrow transform owner not verified                                      | Source→reference semantics must remain explicit                             |
| Refinement                     | No canonical location established here             | Responsibility may be later-version/experimental                         | Do not claim V1 ownership                                                   |
| Registration / warping         | `src/chandramap/`                                  | Narrow owner not verified                                                | Must remain distinct from evaluation                                        |
| Metrics / evaluation           | `src/chandramap/`                                  | Narrow canonical metric module not verified                              | Should have shared scientific ownership                                     |
| Scientific accept/reject       | `src/chandramap/`                                  | Exact owner not verified                                                 | Must remain distinct from backend job status                                |
| Global retrieval               | No canonical implementation owner established here | Target/later-version responsibility unless source proves otherwise       | Must remain separate from local matching                                    |
| Global descriptors             | No canonical implementation owner established here | Later research responsibility unless source proves otherwise             | Descriptor generation is not FAISS                                          |
| Vector indexing/search         | No canonical implementation owner established here | Do not assume FAISS implementation exists                                | Vector search must remain distinct from local registration                  |
| Structured contracts           | `contracts/`                                       | Confirmed contract area                                                  | Exact schema ownership requires contract inspection                         |
| Scientific results             | `results/`, `src/chandramap/`                      | `results/` is physical result area; core result model owner not asserted | Generated results should not replace scientific implementation              |
| Generated artifacts            | `artifacts/`                                       | Confirmed generated-output area                                          | Downstream from scientific result                                           |
| Benchmark orchestration        | `benchmarks/`                                      | Confirmed benchmark area                                                 | Should invoke shared scientific modules                                     |
| Benchmark configuration        | `benchmarks/`, `configs/`                          | Both confirmed; exact split must follow actual files                     | Avoid duplicating parameters                                                |
| Research                       | `research/`                                        | Confirmed research area                                                  | May depend on scientific core                                               |
| Experiments                    | `experiments/`                                     | Confirmed experimental area                                              | Should not become hidden core dependency                                    |
| Notebooks                      | `notebooks/`                                       | Confirmed analysis/exploration area                                      | Stable algorithms should not exist only here                                |
| Backend / services             | `services/`                                        | Confirmed service area                                                   | Should consume shared core rather than duplicate science                    |
| Applications                   | `apps/`                                            | Confirmed application area                                               | Exact frontend ownership requires source inspection                         |
| Scripts / CLI utilities        | `scripts/`                                         | Confirmed execution/support area                                         | Prefer orchestration over canonical algorithm ownership                     |
| Tests                          | `tests/`                                           | Confirmed test area                                                      | Production code must not depend on tests                                    |
| Deployment                     | `deploy/`                                          | Confirmed deployment area                                                | Must remain outside scientific algorithms                                   |
| CI / repository automation     | `.github/`                                         | Confirmed repository automation area                                     | Should validate but not define scientific algorithms                        |
| Human documentation            | `docs/`                                            | Confirmed human documentation area                                       | Not a runtime dependency                                                    |
| AI engineering context         | `.ai/`                                             | Confirmed agent-context area                                             | Not runtime ML implementation                                               |

---

# 7. Scientific Core Ownership

The primary physical source area for reusable ChandraMap code is:

```text
src/chandramap/
```

Architecturally, reusable scientific responsibilities should be owned within the source package rather than by:

- frontend code
- service routes
- benchmark reports
- notebooks
- generated results
- documentation

Conceptually, the source package is expected to contain or eventually host reusable responsibilities such as:

```text
Scientific Inputs
      ↓
Representation / Preprocessing
      ↓
Correspondence
      ↓
Geometry
      ↓
Transform
      ↓
Registration
      ↓
Evaluation
      ↓
Scientific Result
```

This document does **not** invent subpackages such as:

```text
src/chandramap/matching/
src/chandramap/geometry/
src/chandramap/evaluation/
```

unless those paths are independently confirmed from the repository.

At the current documentation level, `src/chandramap/` is the confirmed source-package boundary.

---

# 8. Core Engine Boundary

Code inside the reusable scientific core should be callable by outer consumers such as:

```text
benchmarks/
services/
apps/
scripts/
research/
experiments/
```

The desired dependency direction is:

```text
Outer Layer
    ↓
src/chandramap/
```

not:

```text
src/chandramap/
    ↓
apps/
```

or:

```text
src/chandramap/
    ↓
services/
```

or:

```text
src/chandramap/
    ↓
notebooks/
```

The source package should remain usable independently from presentation/deployment concerns.

---

# 9. Orchestration Ownership

Orchestration means:

- resolving configuration
- preparing a run
- selecting enabled components
- invoking stages
- assembling a final result

Potential physical owners include:

- reusable orchestration inside `src/chandramap/`
- execution wrappers inside `scripts/`
- benchmark runners inside `benchmarks/`
- application services inside `services/`

The architectural distinction is important:

```text
Orchestrator
    ↓
Scientific Component
```

The orchestrator should not contain an unrelated second implementation of the scientific component.

---

## Scripts

`scripts/` should ideally contain runnable wrappers and operational utilities.

Preferred:

```text
scripts/
   ↓
imports
   ↓
src/chandramap/
```

Avoid placing the only authoritative implementation of:

- SIFT
- RANSAC
- RMSE
- registration

inside one-off scripts.

If such ownership exists in current code, it should be treated as current reality and considered carefully before duplication elsewhere.

---

# 10. Input, Data, and Metadata Ownership

The confirmed data-related repository boundary is:

```text
data/
```

The reusable logic interpreting scientific data should belong in source code rather than in the data directory itself.

Conceptually:

```text
data/
  → scientific inputs / references

src/chandramap/
  → loading / validation / interpretation

configs/
  → methodology / configuration

benchmarks/
  → controlled pair definitions where established
```

`data/` should not become the owner of scientific algorithms.

---

## Data Responsibilities

Where actual modules exist, responsibility may include:

- image/raster loading
- scientific product access
- metadata parsing
- masks/no-data
- pair resolution
- dataset identity
- provenance

Exact implementation files should be added to this map only after source inspection confirms them.

---

# 11. Preprocessing Ownership

Preprocessing belongs conceptually inside the reusable scientific source layer:

```text
src/chandramap/
```

Potential responsibilities include:

- scientific 2D representation preparation
- normalization
- masks
- no-data handling
- filtering
- sensor-aware representations

The exact canonical preprocessing submodule is not asserted here.

Do not place canonical preprocessing logic independently into:

- benchmark runners
- frontend components
- backend routes
- notebooks

when reusable source code already owns or should own the behavior.

---

# 12. Sensor-Specific Ownership

ChandraMap works with scientific contexts including:

- OHRC
- TMC-2
- IIRS
- LRO/LROC NAC
- LRO/LROC WAC

This document does not assume that the repository has one module per sensor.

Sensor-specific code should be mapped only when actual modules are confirmed.

---

## IIRS Ownership Rule

If IIRS-specific implementation exists, maintain the conceptual distinction:

```text
Native Hyperspectral Data
        ↓
Representation Preparation
        ↓
2D Registration Representation
        ↓
Local Correspondence
```

Do not describe a generic conversion to 2D as a complete hyperspectral science pipeline.

The representation owner and the local matcher should remain conceptually distinct.

---

# 13. Scale-Handling Ownership

Scale-related responsibilities may include:

- resampling
- GSD comparison
- pyramid construction
- scale selection

The confirmed reusable-source boundary remains:

```text
src/chandramap/
```

but no narrower canonical scale module is asserted here without source evidence.

Scale experiments may also exist in:

```text
research/
experiments/
```

Experimental scale logic should not become a hidden dependency of the stable baseline.

---

# 14. Local Feature and Matching Ownership

The reusable implementation of local correspondence should live under the scientific source package when it is part of stable project behavior.

The exact existing module for:

- SIFT
- RootSIFT
- ORB
- ALIKED
- LightGlue
- LoFTR
- RIFT
- CFOG

is **not asserted by this document without direct implementation evidence**.

A method being mentioned in architecture/research documentation does not prove its code exists.

---

## Matcher Ownership Principle

Different methods may have different internals.

They should nevertheless produce a scientifically meaningful downstream concept:

```text
Candidate Correspondences
```

where appropriate.

The geometry layer should not need to know unnecessary matcher-specific implementation details.

---

## Candidate Match Ownership

Candidate correspondence creation belongs to local matching.

Geometric classification does not.

Keep:

```text
Matcher
→ Candidate Correspondences
```

separate from:

```text
Geometry
→ Verified Inliers / Rejected Outliers
```

---

# 15. Geometry Ownership

Geometric verification belongs in the scientific source layer.

Conceptual responsibilities include:

- RANSAC
- affine estimation
- homography estimation
- inlier-mask generation
- transform validation
- degeneracy detection
- verified-inlier extraction

The precise repository submodule responsible for these operations is not established here without source/import evidence.

Do not assume a path such as:

```text
src/chandramap/geometry/
```

unless it actually exists.

---

## Matching vs Geometry

This architectural boundary should remain clear even if current implementation code has not yet been split physically:

```text
Candidate Matches
       ↓
Geometry
       ↓
Verified Inliers
+
Transform
```

RANSAC should not be described as local matching.

---

# 16. Transform Ownership

Transform responsibilities include:

- transform representation
- transform direction
- application
- inversion where needed
- model identity
- validation

No dedicated transform package is asserted here.

Wherever transform logic currently resides, it must preserve:

```text
source → reference
```

semantics explicitly.

A future refactor should search existing source code before creating a new transform abstraction.

---

# 17. Refinement Ownership

Advanced tie-point/sub-pixel refinement is a later-version research responsibility unless the canonical implementation establishes otherwise.

No canonical refinement module is identified here.

Potential locations to inspect before creating anything new are:

```text
src/chandramap/
research/
experiments/
```

Canonical Benchmark V1 should not begin depending on refinement simply because an experimental refinement implementation exists elsewhere.

---

# 18. Registration and Warp Ownership

Image registration/warping belongs to the reusable scientific implementation rather than outer application layers.

Conceptually:

```text
Final Transform
      ↓
Registration / Warp
```

This should remain distinct from:

```text
Evaluation
```

A backend route or frontend viewer should not become the canonical owner of scientific warping behavior.

---

# 19. Metrics and Evaluation Ownership

Canonical scientific metrics should have shared implementation ownership.

Potential metric families include:

- candidate count
- verified-inlier count
- inlier ratio
- residuals
- RMSE
- spatial coverage
- retrieval metrics
- runtime measurements
- scientific success/rejection

The confirmed source boundary is:

```text
src/chandramap/
```

but the exact canonical metric module is not asserted here.

---

## Single-Owner Principle

Avoid:

```text
benchmark RMSE implementation
backend RMSE implementation
frontend RMSE implementation
notebook RMSE implementation
```

with different definitions.

Preferred architecture:

```text
Canonical Scientific Metric
           ↓
    Structured Result
     ↙      ↓      ↘
Benchmark Backend Frontend
```

If actual duplication exists, it should be documented only after source inspection confirms it.

---

# 20. Quality and Accept/Reject Ownership

Scientific acceptance/rejection belongs with scientific result evaluation, not application status handling.

Keep:

```text
Scientific Registration:
Accepted / Rejected
```

separate from:

```text
Backend Job:
Running / Completed / Failed
```

No canonical quality-policy module is asserted here without implementation evidence.

---

# 21. Retrieval Ownership

Global retrieval is a separate architectural capability from local registration.

A complete retrieval subsystem may conceptually include:

```text
Reference Preparation
        ↓
Reference Tiles
        ↓
Global Descriptors
        ↓
Vector Index
        ↓
Query Descriptor
        ↓
Top-K Search
        ↓
Candidate Resolution
```

No canonical current implementation location for this complete subsystem is established here.

Do not invent:

```text
src/chandramap/retrieval/
```

unless confirmed.

Potential areas to inspect before adding retrieval code include:

```text
src/chandramap/
research/
experiments/
benchmarks/
configs/
```

---

## FAISS Ownership

If vector search is introduced, FAISS or an equivalent mechanism should own:

- vector indexing
- vector similarity search

It should not own:

- descriptor extraction
- local correspondence
- RANSAC
- registration

Do not create one oversized "FAISS module" containing the complete retrieval and registration pipeline.

---

# 22. Benchmark Ownership

The confirmed benchmark repository area is:

```text
benchmarks/
```

The confirmed broader configuration area is:

```text
configs/
```

Benchmark code should primarily own:

- benchmark composition
- controlled pair selection
- benchmark execution
- aggregation
- comparison/report generation

It should reuse reusable scientific implementations.

---

## Benchmark Configuration vs Scientific Implementation

Preferred:

```text
benchmarks/
      +
configs/
      ↓
select / configure
      ↓
src/chandramap/
```

Avoid:

```text
benchmarks/v1/
→ its own RANSAC

benchmarks/v2/
→ another RANSAC

benchmarks/v3/
→ another RMSE

benchmarks/v4/
→ another warp
```

unless the benchmark intentionally compares those algorithms as different methods.

---

# 23. Benchmark V1–V4 Ownership

Benchmark V1–V4 are **research configurations**, not separate software products.

They should ideally differ through:

- selected methods
- enabled capabilities
- configuration
- benchmark protocol

rather than four duplicated codebases.

Conceptually:

```text
                 Shared Scientific Core
                         ↑
          ┌──────────────┼──────────────┐
          │              │              │
         V1             V2             V3             V4
     configuration  configuration  configuration  configuration
```

Low-level scientific modules should not require version names unless the version itself is genuinely part of their responsibility.

---

## Do Not Create Version-Named Core Implementations by Default

Avoid architecture based on large duplicated files such as:

```text
v1_complete_pipeline.py
v2_complete_pipeline.py
v3_complete_pipeline.py
v4_complete_pipeline.py
```

when configuration/composition can express the differences.

If such files already exist in the repository, document actual ownership before refactoring them.

---

# 24. V1 Physical Ownership

The current repository establishes these relevant physical areas for Benchmark V1:

```text
benchmarks/
configs/
src/chandramap/
tests/
results/
artifacts/
```

The intended ownership relationship is:

```text
benchmarks/ + configs/
          ↓
V1 composition / benchmark definition
          ↓
src/chandramap/
          ↓
reusable scientific implementation
          ↓
results/ + artifacts/
```

A narrower file-by-file mapping for:

- SIFT
- matching
- RANSAC
- affine/homography
- warping
- metrics

should only be added once the corresponding source paths are verified.

The canonical scientific scope is documented in:

[`../project/v1-scope.md`](../project/v1-scope.md).

The ordered scientific flow is documented in:

[`./v1-pipeline.md`](./v1-pipeline.md).

---

# 25. V2–V4 Physical Ownership

The repository contains shared areas capable of hosting later benchmark work:

```text
benchmarks/
configs/
src/chandramap/
research/
experiments/
```

However, this document does not assign unverified V2/V3/V4 modules.

### V2 Responsibilities

Potential architectural additions include:

- sensor-aware representation
- GSD/scale handling
- reference pyramids
- IIRS representation research

No dedicated physical V2 owner is asserted without implementation evidence.

### V3 Responsibilities

Potential additions include:

- advanced correspondence
- learned matching
- global retrieval
- global descriptors
- vector indexing

No dedicated physical V3 owner is asserted without implementation evidence.

### V4 Responsibilities

Potential additions include:

- advanced refinement
- local/piecewise geometry
- DEM-aware methods
- uncertainty
- calibration
- stronger rejection logic

No dedicated physical V4 owner is asserted without implementation evidence.

The absence of a mapped module here is preferable to inventing one.

---

# 26. Research Ownership

The repository contains two explicit research-oriented areas:

```text
research/
experiments/
```

These should be treated differently from stable reusable core code.

---

## `research/`

Suitable for work such as:

- research implementations
- methodological studies
- literature-linked prototypes
- method investigations

where repository convention supports it.

---

## `experiments/`

Suitable for controlled experiments such as:

- ablations
- parameter studies
- representation comparisons
- experimental matcher evaluation
- geometry experiments

---

## Research Dependency Direction

Preferred:

```text
research/
experiments/
      ↓
src/chandramap/
```

Avoid:

```text
src/chandramap/
      ↓
experiments/
```

Stable source should not silently depend on temporary experiment logic.

---

# 27. Notebook Ownership

The confirmed notebook area is:

```text
notebooks/
```

Notebooks are appropriate for:

- exploration
- data inspection
- visualization
- research analysis
- result interpretation

They should not become the sole owner of stable scientific algorithms.

Preferred:

```text
src/chandramap/
      ↓ imported by
notebooks/
```

Avoid stable behavior existing only as copied notebook cells.

---

# 28. Backend Ownership

The repository contains:

```text
services/
```

as the service-oriented physical area.

The detailed backend architecture is documented in:

[`backend-architecture.md`](./backend-architecture.md).

The exact service subdirectories, application entry points, transport framework, and endpoint structure are not asserted by this file without direct source evidence.

Architecturally, backend/service code should:

```text
services/
     ↓
src/chandramap/
```

rather than duplicating scientific implementation.

Backend responsibilities may include:

- application boundary
- orchestration
- result translation
- artifact access
- persistence integration

Scientific matching/geometry/metrics remain core responsibilities.

---

# 29. Frontend / Application Ownership

The repository contains:

```text
apps/
```

as an application-level physical area.

The detailed frontend architecture is documented in:

[`frontend-architecture.md`](./frontend-architecture.md).

This document does not assert:

- application name
- frontend framework
- router
- component paths
- state-management library

without repository evidence.

Application/frontend code should consume:

- backend/application contracts
- structured scientific results

and should not become the canonical owner of:

- RANSAC
- RMSE
- transformations
- scientific accept/reject logic

---

# 30. Contract Ownership

The repository contains:

```text
contracts/
```

This is the confirmed physical boundary for explicit shared contracts where the repository uses them.

Potential responsibilities may include:

- shared schemas
- structured data contracts
- service/application boundary definitions
- result exchange definitions

The exact contents must be inspected before claiming a specific schema exists.

---

## Scientific Result vs Transport Contract

Keep these concepts distinct.

```text
Scientific Result
→ domain/scientific meaning
```

```text
Transport Contract
→ representation across application boundary
```

An HTTP/application response should not redefine scientific semantics simply because it serializes them differently.

---

# 31. Configuration Ownership

The repository contains:

```text
configs/
```

This is the primary confirmed configuration area.

Potential configuration responsibilities may include:

- scientific methodology
- benchmark configuration
- application configuration
- deployment configuration

The exact internal split should follow actual repository files.

---

## Configuration Single-Source Rule

Avoid maintaining the same scientific threshold independently in:

```text
source code
benchmark definition
backend
frontend
```

Important scientific parameters should have one authoritative configuration mechanism.

---

## Configuration vs Code

Configuration should select scientifically supported behavior.

Code should implement it.

Avoid encoding pair-specific methodology in filenames or hard-coded conditions.

---

# 32. Test Ownership

The repository contains:

```text
tests/
```

This is the canonical test area at the root level.

Tests should validate repository behavior without becoming production dependencies.

Preferred:

```text
tests/
   ↓ imports
src/chandramap/
services/
apps/
other relevant modules
```

Avoid:

```text
src/chandramap/
   ↓
tests/
```

---

## Test Categories

Exact test subdirectories are not asserted here.

Depending on actual repository organization, tests may cover:

- scientific core
- geometry
- metrics
- integration
- backend
- frontend
- failure behavior
- benchmark support

Only documented/implemented categories should be treated as current.

---

# 33. Script Ownership

The repository contains:

```text
scripts/
```

Scripts may be appropriate for:

- data preparation
- benchmark invocation
- artifact generation
- maintenance
- development operations

Scripts should normally orchestrate canonical modules.

Avoid creating a new scientific algorithm exclusively inside a script when the responsibility belongs in reusable source code.

---

# 34. Data, Artifact, and Result Ownership

Three repository areas should remain conceptually distinct:

```text
data/
artifacts/
results/
```

---

## `data/`

Represents scientific data organization/references according to repository policy.

It should not be confused with generated output.

---

## `artifacts/`

Represents generated supporting outputs such as:

- visualizations
- registered previews
- plots
- derived files

where repository conventions define them.

Artifacts are downstream of scientific computation.

---

## `results/`

Represents result outputs or recorded benchmark/run results according to repository conventions.

A result directory is not the scientific implementation itself.

---

## Generated vs Canonical

Conceptually:

```text
src/ + configs/ + benchmarks/
         ↓
       execution
         ↓
results/ + artifacts/
```

Generated outputs should not become hidden inputs required to understand the canonical source implementation unless explicitly intended.

---

# 35. Deployment Ownership

The repository contains:

```text
deploy/
```

Deployment concerns belong here or in other verified deployment-specific root files.

The deployment layer may configure:

- applications
- services
- runtime environments

It should not contain canonical scientific algorithm implementations.

No specific deployment platform is asserted here.

---

# 36. CI and Repository Automation

The repository contains:

```text
.github/
```

This area owns GitHub-specific repository automation.

It may include:

- continuous integration
- test execution
- linting
- build validation

according to actual workflow files.

CI consumes repository commands and tests.

It should not define scientific methodology independently.

---

# 37. Documentation Ownership

The repository contains:

```text
docs/
```

for human-facing documentation.

Within this architecture set:

```text
docs/architecture/
```

contains human-readable architecture documentation including:

- [`system-overview.md`](./system-overview.md)
- [`core-engine-architecture.md`](./core-engine-architecture.md)
- [`backend-architecture.md`](./backend-architecture.md)
- [`frontend-architecture.md`](./frontend-architecture.md)
- [`v1-pipeline.md`](./v1-pipeline.md)
- this module map

The project documentation area should explain repository reality.

Runtime source code should not normally depend on architecture Markdown files.

---

# 38. `.ai/` Ownership

The repository also contains:

```text
.ai/
```

This area is **AI-assisted engineering context**, not machine-learning runtime source code.

It contains documentation/instructions for responsibilities such as:

- project context
- domain context
- terminology
- architecture context
- engineering rules
- task instructions

Important distinction:

```text
.ai/
≠
AI/ML Model Implementation
```

Runtime learned methods, if implemented, belong in actual source/research areas—not automatically under `.ai/`.

---

## Human vs AI Module Map

The human-facing file:

```text
docs/architecture/module-map.md
```

focuses on:

- contributor navigation
- repository responsibility ownership
- dependency direction

The AI-facing file:

```text
.ai/architecture/MODULE_MAP.md
```

may contain additional:

- agent loading guidance
- context rules
- implementation constraints

Both should describe the same repository reality.

---

# 39. Dependency Direction

The intended high-level dependency direction is:

```text
apps/ ────────────┐
services/ ────────┤
benchmarks/ ──────┤
scripts/ ─────────┤
research/ ────────┼────→ src/chandramap/
experiments/ ─────┘             │
                                ↓
                    scientific/domain dependencies

tests/
   └──────────────────────────→ relevant repository modules

configs/
   └──────────────────────────→ consumed by applicable workflows

contracts/
   └──────────────────────────→ shared across defined boundaries
```

Generated areas sit downstream:

```text
Scientific / Application Execution
              ↓
       results/ + artifacts/
```

Deployment sits outside the core:

```text
deploy/
   ↓
applications / services
```

---

# 40. Dependency Rules by Area

| Area              | May Depend On                                        | Should Not Depend On                                                                                      |
| ----------------- | ---------------------------------------------------- | --------------------------------------------------------------------------------------------------------- |
| `src/chandramap/` | scientific/domain dependencies                       | frontend, HTTP routes, notebooks, benchmark reports                                                       |
| `benchmarks/`     | scientific core, configs, benchmark data definitions | copied scientific algorithms                                                                              |
| `services/`       | core, contracts, application configuration           | duplicate matcher/geometry/metric implementations                                                         |
| `apps/`           | application/backend contracts                        | scientific implementation internals unless intentionally non-browser application architecture requires it |
| `scripts/`        | reusable repository modules                          | become sole owner of stable scientific algorithms                                                         |
| `research/`       | core + research-specific dependencies                | production/app layers unless experiment explicitly needs them                                             |
| `experiments/`    | core + experiment support                            | become hidden dependencies of stable core                                                                 |
| `notebooks/`      | reusable core, result/data interfaces                | become canonical implementation owner                                                                     |
| `tests/`          | relevant production/research modules                 | production modules depending back on tests                                                                |
| `configs/`        | no code dependency expected                          | scientific implementation                                                                                 |
| `contracts/`      | shared domain/application definitions where designed | UI implementation or benchmark reports                                                                    |
| `results/`        | generated from execution                             | source modules depending on result filenames                                                              |
| `artifacts/`      | generated from execution                             | canonical algorithm implementation                                                                        |
| `.ai/`            | documentation/context                                | runtime source depending on agent instructions                                                            |
| `docs/`           | documentation                                        | normal runtime execution                                                                                  |

---

# 41. Single Ownership Principle

Each major scientific responsibility should have one primary canonical owner.

Avoid structures such as:

```text
RMSE implementation #1
→ scientific source

RMSE implementation #2
→ benchmark runner

RMSE implementation #3
→ backend

RMSE implementation #4
→ frontend
```

Likewise avoid multiple independent implementations of:

- RANSAC
- coordinate conversion
- inlier ratio
- spatial coverage
- transform direction logic
- product metadata interpretation

unless those implementations intentionally represent different methods under controlled comparison.

---

# 42. Shared Components Across V1–V4

The architecture expects Benchmark V1–V4 to share reusable scientific capabilities where applicable.

Likely shared responsibility categories include:

- input handling
- validation
- transform semantics
- geometric verification infrastructure
- registration application
- metric definitions
- scientific result semantics

The actual modules providing those capabilities should be identified through source inspection before being listed here at file-level detail.

Do not infer sharing merely because the architecture recommends it.

---

# 43. Version-Specific Ownership

A module should be labelled V2-, V3-, or V4-specific only when:

- source location proves it
- configuration proves it
- benchmark composition proves it
- documentation clearly establishes that ownership

General scientific code should not be branded as a specific benchmark version without reason.

Prefer responsibility-oriented module names over temporary phase-oriented names.

For example, responsibility concepts such as:

```text
matching
geometry
evaluation
retrieval
```

are more durable than:

```text
v3_new_best_final
```

This is naming guidance, not a request to rename existing repository paths automatically.

---

# 44. Where Should I Make a Change?

Use this table as a starting point.

| Change                                           | Start Here                                                    |
| ------------------------------------------------ | ------------------------------------------------------------- |
| Modify reusable scientific behavior              | `src/chandramap/`                                             |
| Find V1 benchmark definition/composition         | `benchmarks/` and `configs/`                                  |
| Add or modify benchmark orchestration            | `benchmarks/`                                                 |
| Change repository scientific configuration       | `configs/`                                                    |
| Add shared application/data contract             | `contracts/` after checking existing contracts                |
| Modify backend/service integration               | `services/`                                                   |
| Modify application/frontend behavior             | `apps/`                                                       |
| Add research-only method                         | `research/` or `experiments/` depending repository convention |
| Add exploratory analysis                         | `notebooks/`                                                  |
| Add runnable/maintenance utility                 | `scripts/`                                                    |
| Add automated validation                         | `tests/`                                                      |
| Work with scientific data organization           | `data/`                                                       |
| Inspect/generated scientific outputs             | `results/`                                                    |
| Inspect/generated visualization or derived files | `artifacts/`                                                  |
| Modify deployment configuration                  | `deploy/`                                                     |
| Modify GitHub automation                         | `.github/`                                                    |
| Change human documentation                       | `docs/`                                                       |
| Change AI engineering context                    | `.ai/`                                                        |

For narrower algorithm work such as:

- local matcher
- RANSAC
- RMSE
- coverage
- transform handling

start with:

```text
src/chandramap/
```

and **search the actual code before creating a new submodule**.

No narrower canonical path should be assumed from this document.

---

# 45. Search Before Creating

Before creating a new module:

1. Search `src/chandramap/`.
2. Search `tests/` for existing imports and expected interfaces.
3. Search `configs/` for existing configuration ownership.
4. Search `benchmarks/` for composition logic.
5. Search `research/` and `experiments/` for prototypes.
6. Search `scripts/` for existing wrappers.
7. Review architecture documentation.
8. Check `.ai/` engineering context where relevant.

The rule is:

> **Search before create.**

A new module should solve a responsibility gap, not duplicate an existing owner.

---

# 46. New Module Decision Guide

Before adding a new module, ask:

1. Does this responsibility already have an owner?

2. Is this stable reusable code or experimental research?

3. Is it scientific logic, application logic, benchmark logic, or presentation logic?

4. Can multiple benchmark versions reuse it?

5. Is the difference actually configuration rather than new code?

6. Does creating it introduce a new outward dependency from the scientific core?

7. Does an existing package already provide a natural home?

8. Will the module have a clear independent responsibility?

9. Can it be tested independently?

10. Would creating it duplicate another implementation?

If ownership remains unclear, resolve the boundary before creating another generic module.

---

# 47. No Generic Utility Graveyard

Avoid placing scientifically meaningful functionality into generic modules merely because no immediate owner is obvious.

Examples of logic that deserves clear ownership include:

- transforms
- coordinate semantics
- RMSE
- spatial coverage
- correspondence conversion
- sensor metadata
- scale handling

Generic names such as:

```text
utils.py
helpers.py
common.py
```

should not become dumping grounds for domain logic.

If existing source currently uses such locations, document and refactor only after verifying actual ownership.

---

# 48. No God Module

Avoid one module owning all of:

```text
I/O
+
Preprocessing
+
Matching
+
RANSAC
+
Warping
+
Metrics
+
API
+
Visualization
```

Prototypes may begin simply.

Stable architecture should separate responsibilities enough to support:

- testing
- reuse
- controlled benchmarking
- maintenance

Do not fragment code excessively either; create boundaries where they represent meaningful responsibilities.

---

# 49. No Cross-Layer Dependency Leakage

Avoid relationships such as:

```text
src/chandramap/
→ apps/ frontend type
```

```text
scientific metric implementation
→ API request schema
```

```text
geometry implementation
→ benchmark report renderer
```

```text
scientific core
→ notebook
```

Outer layers depend inward.

Inner scientific layers should remain independent of outer presentation/orchestration details.

---

# 50. No Frontend Science Ownership

`apps/` may visualize:

- candidate matches
- verified inliers
- transforms
- metrics
- registered previews

It should not become the canonical owner of:

- RANSAC
- RMSE
- spatial coverage
- scientific accept/reject

The detailed boundary is documented in [`frontend-architecture.md`](./frontend-architecture.md).

---

# 51. No Backend Science Duplication

`services/` should invoke reusable scientific code.

Avoid adding backend-specific copies of:

- SIFT
- learned matchers
- RANSAC
- transform estimation
- metric calculations

when equivalent shared logic belongs in `src/chandramap/`.

See [`backend-architecture.md`](./backend-architecture.md).

---

# 52. No Benchmark Science Duplication

`benchmarks/` should define controlled experiments.

It should not become the only owner of general scientific algorithms.

Preferred:

```text
benchmarks/
    ↓
src/chandramap/
```

rather than:

```text
benchmarks/
    ↓
duplicated scientific pipeline
```

---

# 53. No Hidden Research-to-Core Dependency

Research may depend on core:

```text
research/
    ↓
src/chandramap/
```

The stable core should not silently require:

```text
research/
experiments/
notebooks/
```

for ordinary operation.

Promotion of a research method into core should be deliberate.

---

# 54. No Production Dependency on Tests

Tests consume production/source code.

Production/source code should not normally import:

```text
tests/
```

for runtime behavior.

Reusable fixtures or shared support should have intentional ownership outside the tests if production use is required.

---

# 55. No Documentation Runtime Dependency

Normal ChandraMap execution should not require:

```text
docs/
.ai/
```

as runtime implementation inputs.

These areas describe and guide the repository.

They are not scientific runtime packages.

---

# 56. Generated Files Are Not Modules

Generated outputs in:

```text
results/
artifacts/
```

should not be treated as hand-maintained source modules.

Similarly, generated caches or builds, where they exist, should remain distinct from canonical code/configuration.

This distinction matters for:

- code review
- reproducibility
- dependency reasoning
- version control policy

---

# 57. Result and Provenance Ownership

The architecture benefits from one consistent structured scientific result model shared conceptually by:

```text
benchmarks/
services/
apps/
scripts/
```

The exact current source module implementing that model is not asserted here.

Where result contracts exist under:

```text
contracts/
```

or inside:

```text
src/chandramap/
```

their roles should be distinguished carefully:

- scientific/domain result
- application/transport contract

These are related but not necessarily identical.

---

# 58. Artifact Ownership

Generated diagnostic/scientific artifacts belong downstream of scientific execution.

The repository provides:

```text
artifacts/
```

for such outputs.

Potential artifact categories may include:

- registered images
- correspondence visualizations
- residual visualizations
- generated figures
- reports

The exact current artifact taxonomy should follow actual repository contents.

Artifacts should remain traceable to the scientific run that created them where practical.

---

# 59. Data Dependency Direction

Scientific modules should ideally operate on meaningful data/domain concepts rather than:

- benchmark folder names
- frontend upload directories
- local developer absolute paths
- temporary notebook state

Conceptually:

```text
Input / Domain Representation
          ↓
Scientific Core
```

rather than:

```text
Hardcoded Local Path
          ↓
Scientific Algorithm
```

Repository paths should remain portable/configurable according to project conventions.

---

# 60. Current Ownership Gaps

The root repository structure establishes strong high-level ownership areas.

However, this human-facing map does not currently assert verified narrow canonical source paths for each of the following responsibilities:

- preprocessing
- sensor-specific representations
- scale handling
- SIFT/RootSIFT ownership
- learned matchers
- candidate filtering
- RANSAC
- transform abstractions
- refinement
- registration/warping
- RMSE
- spatial coverage
- scientific quality policy
- global descriptor extraction
- FAISS/vector search
- retrieval candidate resolution

For each of these:

> **Search `src/chandramap/`, imports, tests, benchmark code, and research areas before adding a new implementation.**

If no canonical owner exists, that absence should be recorded explicitly during implementation work rather than solved by silently inventing another competing path.

---

# 61. Architecture Review Checklist

Before merging a structural change, verify:

- [ ] Existing ownership was searched first.
- [ ] No duplicate scientific implementation was introduced.
- [ ] Stable scientific code lives in reusable source rather than only a script/notebook.
- [ ] Research-only code remains distinguishable from stable core.
- [ ] Benchmark configuration does not duplicate algorithms unnecessarily.
- [ ] Backend code does not reimplement scientific logic.
- [ ] Frontend code does not reimplement scientific logic.
- [ ] Source/reference semantics remain explicit.
- [ ] Candidate matches and verified inliers remain distinct.
- [ ] Transform ownership remains scientifically clear.
- [ ] Metrics have a canonical owner.
- [ ] Scientific accept/reject is separate from application/job status.
- [ ] Configuration does not duplicate the same parameter across unrelated layers.
- [ ] Production code does not depend on tests.
- [ ] Stable core does not depend on notebooks/experiments.
- [ ] Generated results/artifacts remain distinct from source.
- [ ] `.ai/` is not treated as runtime AI/ML code.
- [ ] V1–V4 remain benchmark configurations rather than duplicated source trees.
- [ ] Dependency direction remains inward toward reusable scientific code.
- [ ] New module names describe responsibility rather than temporary project phase.
- [ ] Exact paths documented here actually exist.

---

# 62. Relationship to Architecture Documents

## [`system-overview.md`](./system-overview.md)

Defines:

> the major ChandraMap system responsibilities.

This module map answers:

> where those responsibilities live physically.

---

## [`core-engine-architecture.md`](./core-engine-architecture.md)

Defines:

> the logical reusable scientific engine.

This module map identifies:

> the confirmed source boundary and repository areas supporting that engine.

---

## [`backend-architecture.md`](./backend-architecture.md)

Defines:

> backend/application responsibilities.

This module map identifies:

```text
services/
```

as the confirmed service-oriented repository area without inventing its internal framework/module layout.

---

## [`frontend-architecture.md`](./frontend-architecture.md)

Defines:

> frontend scientific-visualization responsibilities.

This module map identifies:

```text
apps/
```

as the confirmed application-level repository area without inventing specific pages/components/frameworks.

---

## [`v1-pipeline.md`](./v1-pipeline.md)

Defines:

> ordered canonical V1 scientific processing.

This module map describes the physical repository areas that should compose/reuse that processing rather than duplicate it.

---

## [`../project/v1-scope.md`](../project/v1-scope.md)

Defines the canonical human-facing V1 boundary.

Benchmark/source ownership must remain consistent with that scope.

---

## [`../project/terminology.md`](../project/terminology.md)

Defines canonical scientific terminology used when describing module responsibilities.

---

## [AI Module Map](../../.ai/architecture/MODULE_MAP.md)

Provides agent-oriented repository/module guidance.

This human-facing module map should remain consistent with it while prioritizing contributor navigation and architecture understanding.

---

## [AI Data Flow](../../.ai/architecture/DATA_FLOW.md)

Defines how scientific information moves between architectural responsibilities.

This file defines where those responsibilities belong physically.

---

## [AI Pipeline](../../.ai/architecture/PIPELINE.md)

Defines detailed processing order and later-version branches.

This module map should not duplicate that pipeline.

---

# 63. Key Module Map Rules

1. The repository is the source of truth.

2. Do not invent paths.

3. `src/chandramap/` is the confirmed reusable source-package boundary.

4. `benchmarks/` owns benchmark-oriented composition, not duplicated algorithms.

5. `configs/` owns configuration according to actual repository conventions.

6. `contracts/` is the confirmed shared-contract area where applicable.

7. `tests/` owns automated validation.

8. `research/` and `experiments/` own exploratory/research work.

9. `notebooks/` owns interactive exploration, not canonical stable science.

10. `scripts/` should orchestrate reusable modules rather than become the only scientific implementation.

11. `services/` is the confirmed service/backend-oriented area.

12. `apps/` is the confirmed application-oriented area.

13. `data/` is distinct from `results/` and `artifacts/`.

14. `results/` and `artifacts/` are downstream/generated areas rather than canonical source implementations.

15. `.ai/` contains AI engineering context, not runtime ML source.

16. `docs/` is documentation, not runtime source.

17. Outer layers may depend on reusable source.

18. Reusable scientific source should not depend on frontend/application presentation.

19. Core scientific source should not depend on benchmark-report rendering.

20. Core scientific source should not depend on notebooks or experiments.

21. Production modules should not depend on tests.

22. Matching and geometry are conceptually distinct responsibilities.

23. Candidate matches are not verified inliers.

24. Geometric verification should have one canonical scientific owner.

25. Metrics should have one canonical scientific owner.

26. Backend and frontend should consume metrics rather than implement their own versions.

27. Retrieval remains distinct from local matching.

28. FAISS/vector search, if introduced, owns vector indexing/search rather than descriptor generation or local registration.

29. V1–V4 are benchmark/research configurations, not software versions.

30. V1–V4 should reuse shared scientific modules where scientifically appropriate.

31. Low-level scientific code should not need benchmark-version names unnecessarily.

32. Research may depend on core; core should not silently depend on research.

33. Generated outputs are not source modules.

34. New modules should be created only after existing ownership is searched.

35. When no canonical owner exists, say so rather than fabricating one.

<!-- Source specification: :contentReference[oaicite:0]{index=0} -->
