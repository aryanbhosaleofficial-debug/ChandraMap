# ChandraMap System Architecture Overview

ChandraMap is designed as a **modular scientific image-correspondence and registration system** in which reusable scientific capabilities remain independent of presentation, delivery, benchmarking, and exploratory research code.

The central architectural principle is:

> **The scientific registration core must remain independent of UI, API transport, benchmark reporting, notebooks, and deployment infrastructure.**

Conceptually:

```text
User / Research Interfaces
CLI | Applications | API | Notebooks | Benchmark Runners
                         ↓
                 Orchestration
                         ↓
              Scientific Core
                         ↓
           Data / Domain Context
                         ↓
       External Scientific Libraries
```

The architecture exists to make it possible to:

- compare Benchmark V1–V4 fairly
- replace one correspondence method without rewriting evaluation
- introduce retrieval without coupling it to local geometry
- preserve scientific provenance
- reject unreliable registrations explicitly
- reuse the same scientific results across benchmarks, applications, APIs, and visualizations
- evolve research methods without repeatedly rebuilding the whole system

This document describes **logical responsibilities and architectural boundaries**.

It does not define exact repository paths, classes, APIs, schemas, or execution order.

---

## 1. Purpose

This document answers:

- What major ChandraMap subsystems exist conceptually?
- Which responsibilities belong to the scientific core?
- Which responsibilities belong to orchestration?
- Where do retrieval, matching, geometry, refinement, and evaluation belong?
- How should benchmark configurations reuse shared capabilities?
- Where do research experiments fit?
- How should applications and services access the scientific core?
- Which dependencies should point inward toward the core?
- Which dependencies must not point outward from the core?
- How should data, results, artifacts, and provenance remain separated?
- How can the architecture support V1–V4 without creating four duplicate codebases?

Detailed execution ordering belongs in the processing-pipeline documentation.

Detailed repository ownership belongs in the module-map documentation.

Detailed object/data movement belongs in the data-flow documentation.

---

## 2. Architectural Goals

ChandraMap architecture should support the following goals.

### Scientific Correctness

The architecture must preserve:

- source/reference identity
- coordinate meaning
- transform direction
- scientific units
- distinction between candidate and verified correspondences
- failure/rejection semantics
- provenance

Architectural convenience must not erase scientific meaning.

---

### Reusability

Core scientific components should be reusable by:

- benchmark runners
- scripts
- applications
- APIs
- research experiments
- visualization tools

without each layer reimplementing scientific logic.

---

### Benchmark Comparability

Benchmark V1–V4 should be composed from shared capabilities whenever scientifically appropriate.

A benchmark version should primarily describe:

> **which capabilities are selected and how they are configured**

rather than own a complete duplicate pipeline.

---

### Replaceability

A matcher should be replaceable without requiring evaluation to be rewritten.

A retrieval method should be replaceable without modifying local geometry.

A visualization should be replaceable without changing scientific calculations.

---

### Reproducibility

Important results should remain traceable to:

```text
Input Data
    +
Configuration
    +
Code Revision
    +
Model / Checkpoint where applicable
    +
Metric Definition
    ↓
Scientific Result
```

---

### Proportionate Complexity

ChandraMap is a research/scientific software project.

Professional architecture does not require unnecessary:

- microservices
- queues
- distributed systems
- databases
- cloud infrastructure
- service meshes

A clean modular system is preferable until actual requirements justify greater infrastructure complexity.

---

## 3. Architecture at a Glance

The conceptual architecture is:

```text
┌───────────────────────────────────────────────────────────────┐
│                 User / Research Interfaces                    │
│                                                               │
│   CLI / Scripts   Applications   API   Notebooks   Benchmarks │
└────────────────────────────┬──────────────────────────────────┘
                             │
                             ▼
┌───────────────────────────────────────────────────────────────┐
│                    Orchestration Layer                        │
│                                                               │
│   Input Setup   Configuration   Pipeline Composition          │
│   Result Routing   Artifact Coordination                      │
└────────────────────────────┬──────────────────────────────────┘
                             │
                             ▼
┌───────────────────────────────────────────────────────────────┐
│                 Scientific Registration Core                  │
│                                                               │
│  Input / Validation                                           │
│  Metadata / Sensor Context                                    │
│  Representation / Preprocessing                               │
│  Scale Handling                                               │
│  Retrieval                 [optional]                          │
│  Local Correspondence                                         │
│  Geometric Verification                                       │
│  Transform Estimation                                         │
│  Refinement                [optional]                          │
│  Registration / Warp                                          │
│  Evaluation / Quality                                         │
│  Accept / Reject                                               │
└────────────────────────────┬──────────────────────────────────┘
                             │
                             ▼
┌───────────────────────────────────────────────────────────────┐
│                Data / Results / Provenance                    │
│                                                               │
│ Scientific Products   Metadata   Configurations               │
│ Results               Artifacts  Derived Data   Caches        │
└───────────────────────────────────────────────────────────────┘
```

This is a **logical architecture**.

It should not be interpreted as proof that every illustrated subsystem is currently implemented as a separate code module.

---

## 4. Core Architectural Principles

The architecture is guided by several non-negotiable ideas.

### Scientific Core First

Correspondence, geometry, registration, and evaluation form the central scientific capability.

Presentation and delivery layers depend on this core.

The core must not depend on them.

---

### Capability Separation

Different scientific responsibilities should remain conceptually distinct.

For example:

```text
Retrieval
→ Where should local matching look?

Local Matching
→ Which local points might correspond?

Geometry
→ Which candidate correspondences agree?

Registration
→ How is the source transformed?

Evaluation
→ Is the result trustworthy?
```

---

### Configuration Selects Capabilities

Benchmark versions should select and compose capabilities through orchestration/configuration rather than forcing low-level algorithms to know about benchmark names.

---

### Scientific Failure Is Valid

The architecture must allow:

```text
Rejected
```

or:

```text
Insufficient Evidence
```

as legitimate outcomes.

A failed registration must not silently become an identity transform or fabricated success.

---

### Original and Derived Data Stay Distinct

Scientific mission products should remain distinguishable from:

- normalized representations
- resampled images
- image pyramids
- descriptors
- retrieval indexes
- registered outputs
- visualizations

---

## 5. System Boundaries

ChandraMap can be understood through six broad architectural boundaries.

### Scientific Core

Owns reusable scientific behavior.

Examples include:

- preprocessing
- scale handling
- correspondence
- geometry
- registration
- evaluation

---

### Orchestration

Owns composition and execution coordination.

Examples include:

- selecting configuration
- selecting benchmark composition
- preparing inputs
- invoking scientific components
- routing outputs

Orchestration should not duplicate algorithms.

---

### Benchmarking

Owns controlled experiment execution and aggregation.

It should use the scientific core rather than replace it.

---

### Research / Experiments

Owns exploratory work such as:

- alternative matchers
- ablations
- new IIRS representations
- experimental geometry
- retrieval studies

Research can depend on stable core components.

Stable core should not depend on experiments.

---

### Application / Service Interfaces

Potential interfaces include:

- CLI
- scripts
- API/service adapters
- interactive applications

They should call shared scientific behavior.

---

### Visualization / Downstream Systems

Potential downstream systems include:

- registered previews
- match visualizations
- maps
- mosaics
- reports

They consume results.

They do not define scientific truth.

---

## 6. Subsystem Responsibility Summary

| Subsystem                 | Primary Responsibility                                         | Must Not Own                         |
| ------------------------- | -------------------------------------------------------------- | ------------------------------------ |
| Data / Dataset            | Scientific data identity, provenance, pair definitions, access | Matcher or benchmark policy          |
| Input Validation          | Validate usable scientific inputs                              | Registration methodology             |
| Metadata / Sensor Context | Interpret product and sensor context                           | Fabricated metadata                  |
| Preprocessing             | Produce algorithm-ready representations                        | Final scientific evaluation          |
| Scale Handling            | Manage meaningful scale representations                        | Local matcher implementation         |
| Retrieval                 | Find candidate reference regions                               | Local geometric registration         |
| Local Correspondence      | Produce candidate point correspondences                        | Declare physical truth               |
| Geometry                  | Verify correspondence consistency and estimate geometry        | Presentation                         |
| Refinement                | Improve already verified point precision                       | Global candidate retrieval           |
| Registration              | Apply accepted geometry                                        | Decide accuracy alone                |
| Evaluation                | Compute authoritative scientific quality evidence              | UI styling                           |
| Accept / Reject           | Interpret quality evidence under defined policy                | Hidden fallback                      |
| Result / Artifact         | Preserve scientific outcome and derived outputs                | Redefine algorithms                  |
| Benchmarking              | Compose and run controlled configurations                      | Duplicate scientific implementations |
| Research                  | Explore alternative methods                                    | Become implicit stable dependency    |
| CLI / API / UI            | Expose or present scientific results                           | Recompute scientific truth           |

---

## 7. Data and Dataset Layer

The data layer provides controlled access to scientific products and the context required to interpret them.

Conceptually, it may manage responsibilities such as:

- source/reference product identity
- dataset membership
- benchmark pair definitions
- product paths or references
- product metadata
- valid-data masks
- derived-data references
- provenance relationships

The data layer should not own:

- matcher choice
- RANSAC policy
- benchmark conclusions
- frontend rendering

---

### Scientific Data Should Retain Context

Scientific imagery should not become an anonymous array earlier than necessary.

Relevant context may include:

- mission
- instrument
- product
- source/reference role
- GSD
- projection
- footprint
- representation provenance

Downstream scientific code should receive enough context to interpret its inputs correctly.

---

### Original vs Derived Data

Conceptually:

```text
Original Scientific Product
            ↓
        Processing
            ↓
Derived Representation / Artifact
```

Derived outputs should not overwrite the conceptual identity of their source products.

---

### Cache Boundary

Caches are performance aids.

They should be considered:

- rebuildable
- derived
- non-authoritative

A cache should not become the only place where essential scientific truth exists.

---

## 8. Input and Validation

The validation boundary protects downstream scientific processing from malformed or incompatible inputs.

Potential validation responsibilities include:

- input readability
- dimensions
- dimensionality
- numeric representation
- finite values where required
- valid mask alignment
- supported scientific representation
- presence of metadata required by the selected method

Validation should fail explicitly.

It should not allow malformed data to propagate until an unrelated matcher or geometry stage fails mysteriously.

---

### Validation Does Not Establish Scientific Success

Input validation answers:

> Can this input be processed under the selected workflow?

It does not answer:

> Can this pair be registered accurately?

Those are different responsibilities.

---

## 9. Metadata and Sensor Context

This subsystem interprets scientific context required by downstream components.

Potential information includes:

- source/reference identity
- mission
- instrument
- product identity
- GSD
- projection
- footprint
- illumination context
- viewing context
- processing state

Not every field is guaranteed to exist.

Unknown information should remain unknown rather than being fabricated.

---

### Sensor Routing

Later benchmark configurations may use sensor-aware routing.

Conceptually:

```text
Scientific Product
       ↓
Sensor / Product Context
       ↓
Appropriate Representation Strategy
```

Sensor routing is a higher-level methodological decision.

It should not be buried invisibly inside unrelated low-level algorithms.

Canonical Benchmark V1 should remain consistent with its intentionally minimal preprocessing scope.

---

## 10. Representation and Preprocessing

The preprocessing subsystem creates algorithm-ready representations while preserving the distinction between original and derived data.

Possible responsibilities may include:

- grayscale-compatible representation where scientifically appropriate
- mask application
- finite-value handling
- normalization
- contrast preparation
- structural representations
- hyperspectral-derived 2D representations
- resampling

The specific operations depend on the selected benchmark/methodology.

---

### Preprocessing Boundary

Preprocessing should not:

- overwrite authoritative mission products
- fabricate missing physical detail
- silently choose benchmark-specific behavior without configuration
- declare correspondence quality

Its responsibility is representation preparation.

---

### IIRS Boundary

IIRS is hyperspectral / imaging-infrared data.

A conventional 2D local matcher generally requires a derived two-dimensional representation.

Conceptually:

```text
Native Hyperspectral Observation
            ↓
Representation Stage
            ↓
Defined 2D Registration Representation
            ↓
Local Correspondence
```

The representation stage and local matcher are separate responsibilities.

A full hyperspectral cube should not be treated blindly as an ordinary grayscale image.

---

## 11. Scale Handling

Scale handling manages physically meaningful source/reference scale differences.

Potential later-version capabilities may include:

- controlled downsampling
- image pyramids
- reference pyramids
- effective-GSD comparison
- scale candidate generation

The architecture should keep scale handling logically distinct from local feature extraction.

---

### Physical Scale Principle

Upsampling may change raster dimensions.

It does not recreate physical terrain information.

Therefore scale handling should reason about:

- representation
- GSD
- observable detail

rather than only matching array sizes.

---

### V1 Scale Boundary

Canonical V1 intentionally uses limited scale handling.

Advanced automatic scale search belongs primarily to later benchmark research.

An advanced scale subsystem may exist architecturally without becoming a hidden V1 dependency.

---

## 12. Retrieval

Retrieval is an **optional subsystem**.

Its purpose is:

> identify likely reference regions when the source location or overlap is not already sufficiently known.

It is not required when reliable metadata or benchmark design already supplies the correct reference region.

---

### Metadata-Constrained Search

Preferred architectural principle:

```text
Reliable Spatial Metadata Available?
        │
        ├── Yes → Constrain Reference Search
        │
        └── No / Insufficient → Consider Visual Retrieval
```

ChandraMap should not ignore reliable scientific metadata merely to force an image-only retrieval problem.

---

### Offline Reference Preparation

Where retrieval is used, reference-side preparation may conceptually include:

```text
Reference Products
      ↓
Reference Regions / Tiles
      ↓
Optional Scale Representations
      ↓
Global Descriptors
      ↓
Vector Index
      +
Reference Metadata Mapping
```

This belongs outside the local registration algorithm.

---

### Online Retrieval

Conceptually:

```text
Source Representation
        ↓
Global Descriptor
        ↓
Vector Search
        ↓
Top-K Candidate Regions
        ↓
Resolve Reference Data
        ↓
Local Registration
```

---

### Global vs Local Descriptor

A **global descriptor** represents an image or region for retrieval.

A **local descriptor** represents a local feature for point-level correspondence.

They should not be treated as interchangeable.

---

### FAISS Boundary

If FAISS is used, its architectural responsibility is:

- vector indexing
- similarity search

FAISS does not own:

- image representation extraction
- global descriptor generation
- local keypoint extraction
- local correspondence
- RANSAC
- transformation estimation
- image registration

Conceptually:

```text
Image
  ↓
Descriptor Generator
  ↓
Vector
  ↓
FAISS
  ↓
Candidate IDs
```

---

## 13. Local Correspondence

The local-correspondence subsystem generates **candidate point correspondences** between a selected source/reference pair.

Its output is evidence for later geometric verification.

It must not declare candidate matches to be physical truth.

---

### Classical Methods

Potential classical approaches include:

- SIFT
- RootSIFT
- ORB where appropriate

Canonical V1 uses the classical baseline defined by its scope.

---

### Learned Sparse Methods

Potential later research may use learned sparse feature extraction and matching.

For example:

```text
Image
  ↓
ALIKED
  ↓
Local Features
  ↓
LightGlue
  ↓
Candidate Correspondences
```

ALIKED and LightGlue have different architectural responsibilities.

---

### Detector-Free Methods

LoFTR represents a detector-free correspondence approach.

Its internal operation should not be forced into an artificial detector/descriptor abstraction merely for architectural symmetry.

The architecture only requires that downstream geometry can receive an appropriate candidate-correspondence representation.

---

### Remote-Sensing Research Methods

Research directions may include methods such as:

- RIFT
- CFOG

Their inclusion in architectural discussion does not imply current implementation or support.

---

### Common Downstream Boundary

Different matcher families should conceptually converge on:

> **candidate correspondences**

that can be interpreted by shared geometry and evaluation components.

Exact classes or schemas belong elsewhere.

---

## 14. Geometric Verification

The geometric-verification subsystem determines whether candidate correspondences are mutually consistent with an allowed geometric model.

Conceptually:

```text
Candidate Correspondences
          ↓
Robust Geometric Verification
          ↓
Verified Inliers
+
Rejected Outliers
+
Initial Geometry
```

---

### RANSAC Role

RANSAC belongs to:

> robust geometric estimation.

It is not:

- a detector
- a descriptor
- a matcher
- a retrieval system

---

### Geometry Boundary

Geometry should not:

- depend on frontend state
- treat matcher confidence as geometric truth
- compute presentation-specific summaries
- contain benchmark-report formatting

Low-level geometry should ideally remain reusable across benchmark compositions.

---

### Verified Inlier Meaning

A verified inlier is:

> consistent with the selected model under the active verification policy.

It is not automatically:

> independently validated physical ground truth.

---

## 15. Transform Models

The transform-model capability represents the mathematical relationship between coordinate systems.

Baseline models may include:

- affine
- homography

Later research may investigate:

- local models
- piecewise models
- terrain-aware geometry
- spatially varying transforms

---

### Transform Semantics

Every scientifically meaningful transform should remain interpretable through:

- model type
- direction
- source coordinate space
- destination coordinate space

Conceptually:

```text
Source Coordinate Space
          ↓
       Transform
          ↓
Reference Coordinate Space
```

A bare matrix without those semantics can be ambiguous.

---

### Model Choice

Transform selection should be controlled by benchmark or methodology composition.

Model-specific logic should not be scattered across unrelated system areas.

---

## 16. Refinement

Refinement is an optional scientific subsystem.

Potential responsibilities include:

- improving verified tie-point locations
- estimating fractional-pixel coordinates
- refining correspondence precision
- refitting the final transform after coordinates change

Conceptually:

```text
Verified Inliers
      ↓
Optional Point Refinement
      ↓
Refined Tie Points
      ↓
Final Transform Refit
```

---

### Verification Before Refinement

Advanced point refinement should normally operate on already verified correspondences rather than arbitrary raw matcher candidates.

---

### Final Refit

If point coordinates change during refinement, the authoritative final transform should be estimated from the final coordinates rather than retaining stale pre-refinement geometry.

---

### V1 Boundary

Architecture may support refinement without requiring canonical V1 to use it.

Benchmark composition determines whether this optional capability is active.

---

## 17. Registration / Warp

The registration subsystem applies accepted geometry to source data or coordinates.

Potential outputs include:

- transformed coordinates
- registered image
- overlap representation
- registered preview

Registration consumes geometry.

It should not redefine geometry.

---

### Warp Is Not Validation

A successful warp means:

> the transform could be applied.

It does not establish:

> the transform is scientifically accurate.

Scientific evaluation remains a separate subsystem.

---

## 18. Evaluation and Quality

Evaluation owns authoritative scientific measurements describing correspondence and registration quality.

Potential metric families include:

- candidate count
- verified-inlier count
- inlier ratio
- residuals
- spatial coverage
- independent check-point RMSE
- ground error where scientifically valid
- retrieval Recall@K
- runtime
- success/rejection/failure

Exact formulas belong in metric documentation.

---

### Metric Centralization

Each authoritative metric should ideally have one logical owner.

Avoid architecture such as:

```text
Benchmark RMSE
API RMSE
Frontend RMSE
Notebook RMSE
```

where each layer implements a different definition.

Prefer:

```text
Evaluation
    ↓
Authoritative Metric Values
    ↓
Benchmark / API / UI / Reports
```

---

### Fit vs Check Points

The architecture should support separation between:

```text
Fit Points
    ↓
Geometry / Transform
```

and:

```text
Independent Check Points
        ↓
Evaluation
```

Evaluation data must not become hidden fitting input when independent accuracy is intended.

---

### Evaluation and Presentation

The UI may display:

- RMSE
- coverage
- inlier ratio
- status

but should consume authoritative values rather than independently recompute them.

---

## 19. Accept / Reject Decision

The quality-decision subsystem interprets scientific evidence according to the selected benchmark or method policy.

Possible inputs include:

- transform validity
- inlier support
- residuals
- spatial coverage
- independent error where available

No universal numerical acceptance thresholds are defined here.

---

### Rejection as a First-Class Result

The system must permit:

```text
Insufficient Reliable Evidence
        ↓
Rejected Registration
```

without converting that outcome into an exception or fabricated success.

---

### Policy vs Metric

Evaluation computes scientific evidence.

Acceptance policy interprets that evidence.

Keeping these responsibilities distinct allows the same metric implementation to support different controlled benchmark policies when scientifically justified.

---

## 20. Result and Artifact Layer

ChandraMap should conceptually produce one authoritative scientific outcome that can be consumed consistently by other layers.

Potential result categories include:

- source/reference identity
- provenance
- status
- correspondence evidence
- geometry
- evaluation
- failure/rejection context
- runtime context
- artifact references

This is a conceptual responsibility.

It does not define an exact class or serialized schema.

---

### One Authoritative Result

Preferred conceptual structure:

```text
Scientific Core
      ↓
Authoritative Scientific Result
      ↓
┌────────────┬────────────┬────────────┬──────────────┐
│ Benchmark  │ CLI / API  │ UI / Map   │ Reports      │
└────────────┴────────────┴────────────┴──────────────┘
```

Avoid different outer layers independently deciding scientific truth.

---

### Result vs Artifact

A **result** is the scientific outcome.

An **artifact** is a generated supporting file.

Examples of artifacts may include:

- registered image
- match visualization
- residual plot
- report
- descriptor cache
- retrieval index

Artifacts should remain traceable to the scientific run that created them.

---

## 21. Benchmark Architecture

Benchmarking is an orchestration responsibility.

A benchmark should:

- select controlled data
- resolve the benchmark configuration
- compose the required scientific capabilities
- execute the shared scientific core
- preserve failures and rejections
- aggregate results
- generate benchmark reports

It should not contain separate hidden implementations of the underlying algorithms.

---

### Benchmark Layer Must Not Duplicate Science

Avoid:

```text
V1 Benchmark
→ Own SIFT Implementation

V2 Benchmark
→ Own Geometry Implementation

V3 Benchmark
→ Own RMSE Implementation
```

Prefer:

```text
Benchmark Composition
        ↓
Shared Scientific Capabilities
        ↓
Shared Evaluation
```

---

### V1–V4 Are Compositions

Benchmark V1–V4 are research/pipeline configurations.

They are not separate software products.

They should compose shared capabilities where possible.

---

### V1 Architecture View

Conceptually:

```text
Input / Validation
      ↓
Minimal Preprocessing
      ↓
Classical Local Features
      ↓
Classical Matching
      ↓
RANSAC / Geometry
      ↓
Affine / Homography
      ↓
Registration
      ↓
Evaluation
      ↓
Scientific Result
```

Retrieval is bypassed.

Advanced sensor routing is bypassed.

Advanced refinement remains bypassed where excluded by canonical V1 scope.

---

### V2 Architecture View

High-level conceptual composition:

```text
Input
  ↓
Sensor / Product Context
  ↓
Sensor-Aware Representation
  ↓
Scale Handling
  ↓
Local Correspondence
  ↓
Shared Geometry
  ↓
Shared Registration / Evaluation
```

V2 primarily investigates sensor- and scale-related improvements.

Exact V2 methodology belongs in its benchmark specification.

---

### V3 Architecture View

High-level conceptual composition:

```text
Optional Global Retrieval
          +
Advanced Local Correspondence
          ↓
Shared Geometric Verification
          ↓
Registration
          ↓
Evaluation
```

Retrieval remains distinct from local matching.

---

### V4 Architecture View

High-level conceptual composition:

```text
Advanced Representation / Matching
              ↓
Verified Correspondence
              ↓
Advanced Refinement / Geometry
              ↓
Registration
              ↓
Quality / Uncertainty / Rejection Research
```

This does not imply that every capability is currently implemented.

---

### Benchmark Versions Are Not Software Releases

```text
Benchmark V1
Benchmark V2
Benchmark V3
Benchmark V4
```

do not mean:

```text
Software v1.0
Software v2.0
Software v3.0
Software v4.0
```

Release versioning and benchmark composition are separate concerns.

---

## 22. Shared Core vs Version-Specific Logic

The preferred architecture is:

```text
Shared Scientific Capabilities
          ↓
Version-Specific Composition
          ↓
Benchmark V1 / V2 / V3 / V4
```

Low-level algorithms should not be filled with logic such as:

```text
if benchmark == V1:
    ...
elif benchmark == V2:
    ...
```

unless the algorithm genuinely owns behavior that cannot be separated cleanly.

Version differences should primarily live in:

- configuration
- orchestration
- component selection

rather than duplicated scientific implementations.

---

## 23. Research and Experiment Architecture

Research work has different stability requirements from the scientific core.

Research may investigate:

- alternative matchers
- IIRS representations
- scale strategies
- retrieval methods
- ablations
- local geometry
- confidence calibration
- new evaluation ideas

Research code may be exploratory.

It should not automatically become a dependency of stable scientific components.

---

### Promotion from Research to Core

A research capability may be promoted when:

- its responsibility is clear
- its interface is understood
- behavior is reproducible
- tests exist
- it is useful beyond one experiment
- scientific value has been sufficiently established

Not every experiment needs to become permanent architecture.

---

### Notebook Boundary

Notebooks are appropriate for:

- exploratory analysis
- visualization
- result interpretation
- experiment discussion

Stable reusable scientific logic should not exist **only** inside notebooks.

Prefer:

```text
Reusable Core Function
      ↓
Notebook Imports / Uses It
```

over:

```text
Notebook
→ Only Existing Implementation
```

---

## 24. Configuration

Configuration expresses methodology.

Potential configuration responsibilities may include:

- preprocessing
- scale handling
- matcher selection
- matching policy
- geometric model
- robust estimation
- refinement
- retrieval
- evaluation
- quality gates

The actual repository configuration technology is outside this document.

---

### Configuration Boundary

Configuration should:

- select behavior
- expose meaningful methodological choices
- support reproducibility

Scientific modules should implement the selected behavior.

Avoid hiding benchmark methodology in:

- hard-coded constants
- developer-local edits
- filenames
- notebook state

---

## 25. CLI and Script Interfaces

Where CLI or runnable scripts exist, they should remain thin orchestration adapters.

Conceptually:

```text
Arguments
    ↓
Input / Configuration Resolution
    ↓
Scientific Core
    ↓
Structured Result
    ↓
Human / Machine Output
```

CLI code should not become the only implementation of:

- SIFT
- RANSAC
- transformation
- RMSE
- scientific acceptance logic

---

## 26. Backend and API Boundary

Backend/API functionality is an outer application concern where present or intentionally targeted.

Conceptually:

```text
Request
  ↓
Application / Service Adapter
  ↓
Scientific Core
  ↓
Structured Result
  ↓
Response Representation
```

An API layer may own:

- request validation
- input translation
- configuration selection
- invocation
- response serialization
- artifact references

It should not independently reimplement scientific algorithms.

This overview does not claim any particular backend framework or API endpoint exists.

---

## 27. Frontend and Visualization

A frontend or visualization layer may display:

- source/reference observations
- candidate correspondences
- verified inliers
- registered preview
- metrics
- failure/rejection status
- map context

The visualization layer should consume scientific outputs.

It should not independently decide:

- which correspondences are verified
- whether the transform is valid
- what RMSE means
- whether a registration should be accepted

---

## 28. Map and Mosaic as Downstream Systems

Map and mosaic functionality sits downstream of accepted scientific registration.

Conceptually:

```text
Correspondence
      ↓
Geometry
      ↓
Registration
      ↓
Evaluation
      ↓
Accepted Result
      ↓
Map / Mosaic / Visualization
```

Map or mosaic code should never become a dependency of the scientific registration core.

A visually smooth mosaic is not a substitute for validated registration.

---

## 29. Testing Boundaries

Tests should surround architectural boundaries rather than become dependencies of production code.

Potential testing levels include:

### Unit Tests

Validate individual scientific or engineering responsibilities.

Examples:

- coordinate conversion
- transform application
- metric calculation
- validation behavior

### Integration Tests

Validate interactions across components.

Example:

```text
Preprocessing
→ Correspondence
→ Geometry
→ Registration
→ Evaluation
```

### Contract / Interface Tests

Where project interfaces exist, verify that components preserve expected scientific semantics.

### Scientific Benchmarks

Measure method performance on controlled lunar evaluation populations.

---

### Tests vs Benchmarks

Software testing asks:

> **Does the implementation behave correctly?**

Scientific benchmarking asks:

> **How well does the method perform?**

A passing unit test does not prove lunar registration accuracy.

A difficult benchmark rejection does not automatically indicate a software defect.

---

## 30. Reproducibility and Provenance

The architecture should allow important results to be traced conceptually to:

```text
Source / Reference Data
        +
Derived Representations
        +
Configuration
        +
Code Revision
        +
Model / Checkpoint where applicable
        +
Metric Definition
        ↓
Scientific Result
        ↓
Artifacts / Reports
```

Detailed lineage structures belong in data-flow documentation.

---

### Result Lineage

It should be possible, where practical, to understand:

- which products generated the run
- which representation was used
- which method/configuration generated correspondences
- which transform was estimated
- which metrics were computed
- which artifacts belong to that result

---

### Model / Checkpoint Boundary

Learned model checkpoints are external scientific/software artifacts.

Their identity may matter for reproducibility.

Checkpoint management should remain distinct from the scientific meaning of:

- matching
- geometry
- evaluation

This document does not prescribe a model registry or storage system.

---

## 31. Dependency Direction

The preferred dependency direction is:

```text
UI / Applications / API / CLI / Scripts
                  ↓
             Orchestration
                  ↓
            Scientific Core
                  ↓
      Data / Domain Abstractions
                  ↓
     Scientific External Libraries
```

Additional relationships:

```text
Benchmarks
    ↓
Scientific Core

Research
    ↓
Scientific Core

Tests
    ↓
Relevant Layers
```

The reverse dependencies should generally be avoided.

---

### Scientific Core Must Not Depend On

The reusable scientific core should not require:

- frontend code
- HTTP routes
- benchmark-report rendering
- notebooks
- tests
- deployment infrastructure
- map UI
- mosaic presentation

---

### Research Dependency

Preferred:

```text
Research Experiment
        ↓
Scientific Core
```

Avoid:

```text
Scientific Core
        ↓
Temporary Research Notebook
```

---

### Benchmark Dependency

Preferred:

```text
Benchmark Runner
      ↓
Scientific Core
```

Avoid:

```text
Scientific Core
      ↓
Benchmark Report Code
```

---

### No Circular Dependencies

Avoid structures such as:

```text
Matching
   ↓
Geometry
   ↓
Matching
```

or:

```text
Core
 ↓
API
 ↓
Core
```

Responsibility direction should remain clear.

---

### Dependency Inversion

External scientific libraries may be wrapped behind project-owned abstractions where that creates meaningful value, such as:

- multiple interchangeable implementations
- scientific semantic normalization
- easier testing
- isolation of unstable external interfaces

Do not add abstraction layers solely for architectural appearance.

---

## 32. Error and Failure Boundaries

ChandraMap should preserve different failure classes.

### Invalid Input

Examples:

- unreadable data
- unsupported representation
- malformed scientific input

---

### Expected Scientific Failure

Examples:

- insufficient keypoints
- insufficient candidate correspondences
- unstable geometry
- poor spatial support
- registration rejection

These may represent correct scientific outcomes.

---

### Software / Infrastructure Failure

Examples:

- unexpected exception
- corrupted internal state
- resource failure

These indicate engineering problems rather than scientific rejection.

---

### No Silent Success Fallback

Scientific failure should not become:

```text
Identity Transform
+
Success
```

unless identity is genuinely the estimated valid geometry.

Likewise, the system should not silently switch methods and report that the originally requested method succeeded.

Fallbacks should be explicit and traceable when they exist.

---

## 33. Security and Trust Boundaries

External data should be treated as untrusted software input at system boundaries.

Potential external inputs include:

- image/raster files
- archives
- metadata
- configuration
- model checkpoints
- external datasets
- network-downloaded content

The architecture should preserve clear boundaries around input validation and file handling.

Detailed controls belong in the repository security documentation rather than this system overview.

---

## 34. Current vs Target Architecture

This document primarily describes the **logical architecture ChandraMap should preserve as the project evolves**.

It does not assign `Implemented`, `Experimental`, or `Planned` status to individual subsystems unless such status is independently verified from repository evidence.

Therefore:

- the existence of a subsystem in this architecture does not prove that code currently exists for it
- optional retrieval architecture does not imply current retrieval implementation
- learned-matcher architecture does not imply current learned-model support
- backend/UI boundaries do not imply a specific application stack exists
- V2/V3/V4 architecture descriptions are composition guidance, not implementation-status claims

Current repository ownership and implementation status should be derived from:

- actual source code
- configuration
- tests
- benchmark definitions
- module-map documentation

Target architecture must never be presented as implemented reality.

---

## 35. Core vs Optional Capabilities

### Core Scientific Responsibilities

Conceptually required for the fundamental correspondence/registration problem:

- scientific input handling
- input validation
- preprocessing/representation
- local correspondence
- geometric verification
- transformation
- registration
- evaluation
- explicit result/failure semantics

---

### Version-Dependent / Optional Responsibilities

Depending on benchmark scope:

- sensor-specific representations
- advanced scale handling
- retrieval
- global descriptors
- vector indexing
- learned matchers
- sub-pixel refinement
- local/piecewise geometry
- DEM-aware methods
- calibrated confidence

---

### Downstream / Application Responsibilities

- CLI presentation
- API access
- frontend
- map
- mosaic
- visualization
- reporting

These should remain outside the scientific core.

---

## 36. Architectural Invariants

The following rules should remain true unless the architecture is deliberately revised.

1. The scientific registration core does not depend on UI code.

2. The scientific registration core does not depend on HTTP routes or backend frameworks.

3. Benchmark runners reuse scientific algorithms rather than duplicating them.

4. Research code may depend on stable core components; stable core should not depend on experiments.

5. Data/provenance responsibilities remain separate from algorithm-selection policy.

6. Original scientific products remain distinguishable from derived representations.

7. Preprocessing remains conceptually separate from local matching.

8. Scale handling remains explicit rather than hidden in unrelated code.

9. Retrieval remains optional and distinct from local matching.

10. Global descriptors remain distinct from local descriptors.

11. Vector search remains distinct from descriptor extraction.

12. FAISS, where used, performs vector indexing/search rather than local image registration.

13. Matchers produce candidate correspondences.

14. Geometric verification determines model-consistent inliers.

15. Candidate matches and verified inliers are not interchangeable.

16. Verified inliers are not automatically independent ground truth.

17. RANSAC belongs to geometry, not matching.

18. Transform model and direction remain explicit.

19. Coordinate spaces remain explicit where transformations cross boundaries.

20. Optional refinement operates on verified correspondence evidence.

21. If tie-point coordinates change, final geometry is refit from the final points.

22. Registration/warping remains separate from scientific accuracy evaluation.

23. Evaluation owns authoritative metric definitions.

24. Acceptance/rejection consumes scientific evidence rather than UI state.

25. Failure and rejection remain valid scientific results.

26. One authoritative scientific result should be reusable across benchmark/API/UI/reporting layers.

27. UI/frontend should not create separate metric definitions.

28. API handlers should not contain duplicate registration algorithms.

29. V1–V4 should primarily compose shared capabilities.

30. Low-level scientific algorithms should not be littered with benchmark-version conditionals.

31. Canonical V1 remains a deliberately simple baseline.

32. Advanced optional components must not become hidden V1 dependencies.

33. Caches and artifacts do not replace original scientific truth.

34. Stable scientific behavior should not exist only inside notebooks.

35. Configuration should express important methodological choices explicitly.

36. Dependency cycles should be avoided.

37. Current architecture and target architecture must remain distinguishable.

38. Exact repository paths belong in module-map documentation.

39. Scientific result lineage should remain recoverable where practical.

40. Architectural complexity must be justified by scientific or engineering value.

---

## 37. Architectural Anti-Patterns

### Monolithic Pipeline Script

Avoid one large file owning:

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
Visualization
```

A prototype may begin simply, but stable reusable behavior should eventually have clear responsibility boundaries.

---

### Benchmark Forking

Avoid separate full implementations for:

```text
V1
V2
V3
V4
```

where shared capabilities could be composed instead.

Duplicated benchmark code creates:

- inconsistent bug fixes
- inconsistent metrics
- weaker comparability
- maintenance burden

---

### UI-Driven Science

Avoid frontend code independently computing:

- verified inliers
- RMSE
- transformation validity
- scientific acceptance

Presentation should consume authoritative results.

---

### API-Driven Science

Avoid placing complete scientific pipelines directly inside route/controller logic.

Transport logic and scientific logic should remain separate.

---

### Notebook-Only Core

Avoid making notebooks the only implementation of reusable registration behavior.

Notebooks should call shared implementations where possible.

---

### Hidden Dataset Assumptions

Avoid scientific code that only works because it secretly depends on:

- particular filenames
- local directories
- manually prepared developer state

without those assumptions being explicit.

---

### Magic Metrics

Avoid multiple unrelated implementations of the same named metric.

One layer should not define RMSE differently from another while using the same label.

---

### Silent Failure

Avoid:

```text
Geometry Failed
      ↓
Return Identity Transform
      ↓
Report Success
```

Failure must remain visible.

---

### Retrieval / Registration Coupling

Avoid making vector retrieval directly own:

- local feature matching
- RANSAC
- registration

Retrieval selects candidate regions.

Registration establishes local geometry.

---

### Algorithm / Storage Coupling

Avoid making a matcher require a particular database, UI, or storage technology unless scientifically necessary.

Scientific algorithms should operate on appropriate scientific inputs rather than infrastructure-specific concerns.

---

### God Module

Avoid one module owning unrelated responsibilities such as:

- data loading
- sensor routing
- matching
- geometry
- evaluation
- reporting

Responsibility boundaries should remain clear.

---

### Generic Utility Dump

Avoid moving domain-critical scientific logic into a generic `utils` concept where:

- ownership is unclear
- scientific semantics disappear
- testing becomes difficult

Shared utilities should remain genuinely generic.

Domain behavior should have domain ownership.

---

### Premature Microservices

Do not split preprocessing, matching, geometry, and metrics into network services simply to make the architecture appear sophisticated.

In-process modularity is sufficient until a real deployment/scaling requirement justifies distribution.

---

### Target Architecture Presented as Current

A conceptual subsystem diagram is not evidence that all subsystems currently exist.

Documentation must not transform architecture intent into false implementation status.

---

## 38. Architecture Evolution

Architecture should evolve with demonstrated project needs.

A new capability should not become a permanent subsystem merely because one experiment uses it.

Promote research behavior into stable architecture when:

- responsibility is clear
- boundaries are understood
- behavior is reproducible
- tests exist
- more than one workflow benefits
- the component has clear scientific or engineering value

---

### New Subsystem Criteria

A distinct subsystem is most justified when it has:

- a meaningful independent responsibility
- a clear interface
- multiple consumers or strong isolation value
- independent testing value
- a reason to evolve separately

Do not create one architectural subsystem for every small function.

---

### Shared Capability Rule

If a capability is reusable across benchmark versions:

> keep it shared.

If only the combination changes:

> change composition/configuration.

If the behavior is still exploratory:

> keep it in research until promotion is justified.

---

## 39. Architecture Decision Guidance

Before adding a new major component, ask:

1. Is this scientific core, orchestration, benchmarking, research, or presentation?

2. What single responsibility does it own?

3. Is that responsibility already owned elsewhere?

4. Does it need raw scientific data or only a structured scientific result?

5. Does it create an outward dependency from the scientific core?

6. Does it belong in benchmark composition rather than low-level algorithms?

7. Can it be reused across V1–V4?

8. Is it experimental enough to remain outside the stable core?

9. Is a new abstraction justified by real use cases?

10. Can the component be tested independently?

11. Will it preserve source/reference, coordinate, unit, and provenance semantics?

12. Does it introduce infrastructure complexity without scientific value?

---

## 40. Architecture Success Criteria

A strong ChandraMap architecture should make it possible to:

- replace a matcher without rewriting evaluation
- add a retrieval strategy without modifying local geometry
- compare V1–V4 using shared scientific implementations
- test geometry independently
- run the scientific core without frontend/backend dependencies
- expose the same scientific result through multiple interfaces
- preserve data and result provenance
- reject unreliable registrations explicitly
- reproduce important scientific runs
- add new research methods without destabilizing the entire system
- maintain a simple classical baseline while adding advanced optional capabilities

Architecture success is about:

- correctness
- separation of responsibility
- scientific traceability
- comparability
- maintainability

not the number of services or abstractions.

---

## 41. Architecture Review Checklist

- [ ] Scientific core remains independent of UI.
- [ ] Scientific core remains independent of HTTP/backend frameworks.
- [ ] Benchmarks reuse shared scientific algorithms.
- [ ] Research code is not an implicit stable-core dependency.
- [ ] Data/provenance remain separate from benchmark policy.
- [ ] Original and derived data remain distinguishable.
- [ ] Input validation has a clear boundary.
- [ ] Metadata context is not fabricated.
- [ ] Preprocessing remains separate from matching.
- [ ] IIRS is treated as hyperspectral/imaging-infrared data.
- [ ] IIRS-derived 2D representation is distinct from local matching.
- [ ] Scale handling remains explicit.
- [ ] Upsampling is not treated as physical detail recovery.
- [ ] Retrieval remains optional.
- [ ] Metadata-constrained search can bypass retrieval where appropriate.
- [ ] Offline reference preparation and online retrieval remain distinct.
- [ ] Global and local descriptors remain distinct.
- [ ] FAISS is treated only as vector indexing/search.
- [ ] Local matchers produce candidate correspondences.
- [ ] Candidate matches are not called verified inliers.
- [ ] ALIKED and LightGlue responsibilities remain distinct.
- [ ] LoFTR's detector-free semantics are preserved.
- [ ] RANSAC belongs to geometric verification.
- [ ] Transform model and direction remain explicit.
- [ ] Affine/homography are not presented as universal terrain models.
- [ ] Optional refinement follows geometric verification.
- [ ] Final geometry is refit after changed tie-point coordinates.
- [ ] Registration/warp is separate from evaluation.
- [ ] Evaluation owns authoritative metric definitions.
- [ ] Fit and independent check data remain distinguishable.
- [ ] Acceptance/rejection is explicit.
- [ ] Scientific rejection is a first-class result.
- [ ] Structured results are reusable across outer layers.
- [ ] Artifacts are distinct from scientific results.
- [ ] Benchmark V1–V4 compose shared capabilities.
- [ ] Benchmark V1–V4 are not software release numbers.
- [ ] Low-level algorithms do not unnecessarily depend on benchmark version names.
- [ ] Canonical V1 remains intentionally simple.
- [ ] Research/experiment boundaries remain clear.
- [ ] Stable core behavior does not live only in notebooks.
- [ ] Configuration expresses methodology rather than hiding it.
- [ ] CLI/scripts remain thin orchestration layers.
- [ ] API/service adapters do not duplicate registration science.
- [ ] UI does not recompute scientific truth.
- [ ] Map/mosaic remain downstream.
- [ ] Software testing and scientific benchmarking remain distinct.
- [ ] Reproducibility/provenance remain possible.
- [ ] Scientific failure and software failure remain distinguishable.
- [ ] Trust boundaries for external files are recognized.
- [ ] Dependency direction points inward toward the scientific core.
- [ ] Circular dependencies are avoided.
- [ ] Exact repository paths are deferred to module-map documentation.
- [ ] No unverified service, framework, database, or infrastructure is documented.
- [ ] Target architecture is not presented as current implementation.
- [ ] Architectural complexity remains proportionate to project needs.

---

## 42. Relationship with Other Documents

### Project Overview

[`../project/overview.md`](../project/overview.md) explains:

> **What is ChandraMap?**

This document explains:

> **How are major technical responsibilities separated?**

---

### Problem Statement

[`../project/problem-statement.md`](../project/problem-statement.md) defines the scientific problem.

The architecture defines the system responsibilities used to address it.

---

### Goals and Non-Goals

[`../project/goals.md`](../project/goals.md) defines desired outcomes.

[`../project/non-goals.md`](../project/non-goals.md) defines scope boundaries.

The architecture should support the goals without pulling non-goals into the scientific core.

---

### Assumptions

[`../project/assumptions.md`](../project/assumptions.md) defines scientific and methodological conditions on which workflows may rely.

The architecture must preserve enough context to make those assumptions explicit.

---

### Limitations

[`../project/limitations.md`](../project/limitations.md) defines scientific and methodological boundaries.

Architecture may provide places to investigate or mitigate limitations, but this overview does not claim to solve all of them.

---

### Benchmark V1 Scope

[`../project/v1-scope.md`](../project/v1-scope.md) defines which capabilities belong to canonical V1.

The system architecture defines reusable capabilities that can support V1 and later benchmarks.

---

### Terminology

[`../project/terminology.md`](../project/terminology.md) defines canonical human-facing vocabulary.

Subsystem names and scientific concepts in this document should remain consistent with that terminology.

---

### Canonical AI System Overview

[`.ai/architecture/SYSTEM_OVERVIEW.md`](../../.ai/architecture/SYSTEM_OVERVIEW.md) provides the deeper AI/maintainer architecture context.

This human-facing document should remain technically consistent with it.

---

### Processing Pipeline

[`.ai/architecture/PIPELINE.md`](../../.ai/architecture/PIPELINE.md) defines detailed processing-stage order.

This document defines **responsibilities**, not the complete execution sequence.

---

### Module Map

[`.ai/architecture/MODULE_MAP.md`](../../.ai/architecture/MODULE_MAP.md) owns actual repository responsibility mapping.

Exact implementation paths should be documented there rather than invented in this overview.

---

### Data Flow

[`.ai/architecture/DATA_FLOW.md`](../../.ai/architecture/DATA_FLOW.md) describes scientific information, coordinate, transform, provenance, and result movement between responsibilities.

This overview describes who owns those responsibilities.

---

### Benchmark Rules

[`.ai/development/BENCHMARK_RULES.md`](../../.ai/development/BENCHMARK_RULES.md) defines controlled scientific-comparison rules.

The architecture supports those rules by keeping benchmark orchestration separate from shared algorithms.

---

### Roadmap

[`../../ROADMAP.md`](../../ROADMAP.md) describes planned project evolution.

Roadmap items must not be treated as currently implemented architecture simply because they are planned.

---

## 43. Key Architecture Rules

1. Scientific correspondence and registration are the architectural core.

2. UI, API, map, mosaic, and reporting are outer/downstream layers.

3. Core scientific components must not depend on presentation layers.

4. Core scientific components must not depend on HTTP routes.

5. Benchmark runners orchestrate; they do not reimplement scientific algorithms.

6. Research code may depend on stable core; stable core should not depend on experiments.

7. Data/provenance remain separate from algorithm policy.

8. Original scientific products remain distinguishable from derived representations.

9. Input validation is a separate responsibility from scientific registration success.

10. Preprocessing remains separate from local matching.

11. Scale handling remains explicit.

12. Upsampling does not represent recovery of physical terrain detail.

13. Retrieval is optional and distinct from local matching.

14. Reliable metadata may constrain search before visual retrieval is introduced.

15. Global descriptors are distinct from local descriptors.

16. FAISS performs vector indexing/search, not local image registration.

17. Local matchers produce candidate correspondences.

18. Candidate correspondences are not verified inliers.

19. Geometry determines model-consistent inliers.

20. RANSAC belongs to geometric verification, not matching.

21. Verified inliers are not automatically independent ground truth.

22. Transform model, direction, and coordinate spaces remain explicit.

23. Affine/homography remain modeling choices rather than universal terrain solutions.

24. Optional refinement follows geometric verification.

25. Final transforms are refit when tie-point coordinates change.

26. Registration/warping is separate from accuracy evaluation.

27. Evaluation owns authoritative metric definitions.

28. Fit points and independent check points remain distinguishable.

29. Acceptance/rejection consumes scientific evidence rather than UI state.

30. Rejection is a valid scientific outcome.

31. One authoritative scientific result should be reusable by benchmark, API, CLI, UI, and reporting layers.

32. Artifacts support results; they do not redefine them.

33. Benchmark V1–V4 compose shared capabilities rather than duplicate complete systems.

34. Benchmark V1–V4 are not software release versions.

35. Low-level algorithms should avoid unnecessary benchmark-version branching.

36. Canonical V1 remains an intentionally simple baseline.

37. Optional advanced capabilities must not become hidden V1 dependencies.

38. Stable scientific behavior should not exist only inside notebooks.

39. Configuration should make methodology explicit.

40. Dependency direction should remain inward toward the scientific core.

41. Circular dependencies should be avoided.

42. Caches are rebuildable artifacts, not authoritative source truth.

43. Current architecture and target architecture must never be confused.

44. Actual repository paths belong in module-map documentation.

45. Unverified APIs, services, infrastructure, or schemas must not be invented.

46. Architecture complexity must be justified by scientific or engineering value.

<!-- Source specification: :contentReference[oaicite:0]{index=0} -->
