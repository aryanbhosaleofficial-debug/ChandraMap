# ChandraMap V1 Implementation Task

This document defines the implementation task for **ChandraMap Benchmark V1**.

It answers:

> **Starting from the current repository, how should V1 be implemented correctly, incrementally, and without accidentally turning it into V2, V3, or V4?**

V1 is the simplest trustworthy classical lunar-registration baseline.

Its purpose is not to maximize ChandraMap performance. Its purpose is to establish a transparent, reproducible, measurable baseline against which later benchmark configurations can be compared.

---

## 1. Task Objective

Implement the canonical **Benchmark V1** known-overlap lunar image-registration pipeline using the smallest coherent change set that fits the existing ChandraMap architecture.

Conceptually:

```text
Known Overlapping Source Image
            +
Known Overlapping Reference Image
            ↓
Input Validation
            ↓
Minimal 2D Preprocessing
            ↓
SIFT / Canonical RootSIFT Configuration
            ↓
Descriptor Matching
            ↓
Candidate Match Filtering
            ↓
RANSAC / Geometric Verification
            ↓
Verified Inliers
            ↓
Affine and/or Homography
            ↓
Spatial / Residual Analysis
            ↓
Registration / Warp
            ↓
Evaluation
            ↓
Accept / Reject
            ↓
Structured Result
```

V1 MUST remain:

- classical
- explainable
- CPU-capable where practical
- dependency-light
- reproducible
- measurable
- easy to debug
- explicit about failure
- intentionally limited

V1 MUST NOT be expanded merely because later-version methods could improve difficult cases.

---

## 2. Authoritative Scope

The canonical V1 definition is owned by:

[`../context/V1_SCOPE.md`](../context/V1_SCOPE.md)

If this task document conflicts with `V1_SCOPE.md`:

> **`V1_SCOPE.md` wins.**

This document defines **how to execute implementation work**.

It does not redefine what V1 scientifically includes or excludes.

---

### 2.1 Benchmark V1 Is Not Software v1.0.0

Benchmark V1 is a research/benchmark configuration.

It is not automatically:

```text
software v1.0.0
```

Implementing Benchmark V1 MUST NOT by itself trigger:

- package-version changes
- release tags
- release numbering
- fabricated changelog releases

Software release policy remains independent.

---

## 3. V1 Success Definition

V1 implementation is successful when ChandraMap can take a valid, known-overlap 2D source/reference pair and execute the canonical classical registration path while:

- generating candidate correspondences
- geometrically verifying those correspondences
- estimating the configured global transform
- rejecting unsupported or unreliable cases rather than forcing success
- generating a registered result when geometry is valid
- emitting measurable evaluation information
- preserving units, coordinate meaning, and source/reference direction
- producing one structured result
- remaining reproducible under its documented configuration

V1 does **not** need to achieve strong performance on every difficult lunar pair to be considered correctly implemented.

Weak performance may be scientifically valuable baseline evidence.

---

## 4. Explicit V1 Inclusions

Subject to `V1_SCOPE.md`, canonical V1 includes:

- known-overlap local registration
- registration-ready 2D imagery
- input validation
- minimal generic preprocessing
- SIFT as the primary classical baseline
- RootSIFT only when explicitly standardized as a named V1 configuration
- conventional descriptor matching
- deterministic candidate filtering
- RANSAC-based geometric verification
- separation of candidates, inliers, and outliers
- affine and/or homography according to canonical configuration
- explicit transformation direction
- global 2D registration/warp
- candidate-match count
- verified-inlier count
- inlier ratio
- residual/error diagnostics
- spatial coverage where defined
- independent check-point error where valid truth exists
- explicit units
- runtime where benchmark methodology defines it
- success/rejection/failure information
- reproducible configuration
- unit and integration tests
- benchmark integration
- diagnostic artifacts where useful

---

## 5. Explicit V1 Exclusions

Canonical V1 MUST NOT require:

- whole-Moon visual localization
- global image search
- global descriptors
- FAISS
- vector retrieval
- Top-K reference retrieval
- reference-index construction
- learned retrieval
- advanced sensor-specific routing
- full native IIRS hyperspectral cube processing
- IIRS band-selection research
- IIRS PCA/component optimization
- automatic reference-pyramid search
- automatic effective-GSD selection
- ALIKED
- LightGlue
- LoFTR
- SuperPoint
- SuperGlue
- RIFT
- CFOG
- learned local matching
- GPU-only execution
- DEM-aware registration
- local/piecewise warping
- dense optical flow as canonical V1
- advanced sub-pixel refinement unless explicitly permitted by `V1_SCOPE.md`
- learned confidence estimation
- confidence calibration
- adaptive matcher selection
- bundle adjustment
- whole-Moon mosaic generation
- interactive Moon-map UI as a scientific requirement
- Mars/Venus/general planetary support

Do not "help" V1 by silently introducing later-version capabilities.

---

## 6. Before You Modify Code

Implementation MUST begin with repository inspection.

Do not assume V1 starts from zero.

Do not assume existing files are production-ready merely because they exist.

Do not begin by creating a new parallel V1 implementation tree.

---

### 6.1 Required Repository Inspection

Inspect only the areas relevant to V1.

Determine:

- actual Python package location
- current entry points
- existing data/image loaders
- existing preprocessing
- existing feature extraction
- existing matching
- existing geometry/RANSAC
- existing transformation utilities
- existing registration/warp code
- existing metrics/evaluation
- existing result models/contracts
- existing configuration system
- existing benchmark infrastructure
- nearby tests
- dependency manifests
- relevant `.ai/` instructions
- V1-related prototypes or experiments

The repository itself is the implementation source of truth.

---

### 6.2 Search Before Create

Before creating any implementation, search for an existing owner.

Examples:

```text
Need SIFT?
→ inspect feature/matching code first.

Need RANSAC?
→ inspect geometry code first.

Need RMSE?
→ inspect evaluation/metric code first.

Need result schema?
→ inspect contracts/models/result code first.

Need configuration?
→ inspect the existing configuration system first.
```

Reuse compatible functionality.

Do not create duplicate implementations simply because the expected filename is not obvious.

---

### 6.3 Module Ownership

Follow:

[`../architecture/MODULE_MAP.md`](../architecture/MODULE_MAP.md)

and confirm actual paths from the current repository.

Conceptually:

```text
Data / I/O
→ loading and basic scientific input handling

Preprocessing
→ registration-ready representation

Features / Matching
→ SIFT and candidate correspondences

Geometry
→ RANSAC and transform estimation

Registration
→ warp / coordinate transformation

Evaluation
→ metrics and scientific quality evidence

Benchmark
→ controlled orchestration
```

Do not invent source paths in advance.

---

## 7. Current-State Audit

Before implementation, classify relevant capabilities as:

| Classification                | Meaning                                                            |
| ----------------------------- | ------------------------------------------------------------------ |
| **Existing and Reusable**     | Current implementation appears suitable for canonical V1           |
| **Existing but Incomplete**   | Correct architectural owner exists but V1 requirements are missing |
| **Existing but Experimental** | Prototype exists but requires stabilization before canonical use   |
| **Missing**                   | Required V1 capability has no suitable implementation              |
| **Out of Scope**              | Capability belongs outside canonical V1                            |

Do not publish a current-state table until repository inspection provides evidence.

If the current state has not been inspected, record only the required audit work.

---

## 8. Target Architecture

V1 should compose reusable scientific components.

Preferred conceptual structure:

```text
Shared Scientific Core
        ↓
Canonical V1 Configuration / Composition
        ↓
Benchmark / CLI / Other Thin Caller
```

Avoid:

```text
V1
├── duplicate SIFT
├── duplicate RANSAC
├── duplicate warp
└── duplicate RMSE
```

when shared implementations already exist.

The low-level SIFT or RANSAC implementation should not need to know that Benchmark V1 selected it.

---

## 9. Implementation Strategy

Implement V1 incrementally.

Preferred dependency order:

```text
Repository Audit
      ↓
Input / Result Contracts
      ↓
Input Validation
      ↓
Minimal Preprocessing
      ↓
Feature Extraction
      ↓
Descriptor Matching
      ↓
Candidate Filtering
      ↓
Geometric Verification
      ↓
Transform Estimation
      ↓
Inlier / Coverage Analysis
      ↓
Registration
      ↓
Evaluation
      ↓
Failure / Rejection Logic
      ↓
Structured Result
      ↓
Tests
      ↓
Benchmark Integration
      ↓
Documentation Synchronization
      ↓
Final Verification
```

Do not prioritize benchmark presentation or UI before the scientific path is correct.

---

# 10. Phase 0 — Repository Audit

### Goal

Understand what already exists and where V1 belongs.

### Actions

Inspect:

- package tree
- configuration
- loaders
- preprocessing
- matching
- geometry
- registration
- metrics
- contracts
- benchmark tooling
- tests
- dependency configuration

Search for V1-related prototype code or experiments before creating replacements.

### Expected Evidence

A short implementation plan identifying:

```text
REUSE
MODIFY
CREATE
OUT OF SCOPE
```

for each required V1 responsibility.

No code should be created solely because a target architecture diagram suggests a module name.

---

# 11. Phase 1 — Input and Result Contracts

Before algorithm implementation, confirm how the current repository represents V1 inputs and outputs.

Conceptually, V1 requires:

### Input

- source 2D image/representation
- reference 2D image/representation
- optional scientific metadata
- explicit configuration

### Output

- registration status
- correspondence statistics
- transformation information
- evaluation information
- runtime where defined
- failure/rejection information
- optional artifact references

Do not invent exact class names or serialized fields.

Reuse current contracts where possible.

---

### 11.1 Structured Result Principle

Avoid returning disconnected values such as:

```text
matrix
+
count
+
rmse
+
boolean
+
filename
```

without a coherent scientific result context.

Conceptually:

```text
V1 Pipeline
    ↓
Registration Result
    ├── Status
    ├── Source / Reference Identity
    ├── Correspondence Evidence
    ├── Geometry
    ├── Evaluation
    ├── Runtime Context
    ├── Failure Information
    └── Artifact References
```

Exact representation must follow repository architecture.

---

# 12. Phase 2 — Input Validation

Validation MUST occur before feature extraction.

Potential validation responsibilities include:

- input exists
- image is readable
- dimensions are valid
- expected dimensionality is present
- image is non-empty
- supported dtype/representation
- finite data where required
- masks align with imagery where applicable

Invalid basic input should not fail later as an unexplained SIFT or RANSAC error.

---

### 12.1 Scientific Raster Handling

Do not assume every input is:

```text
8-bit grayscale
```

Scientific imagery may use:

- higher-bit-depth integer data
- floating point
- masks/no-data values

Convert to the algorithm-ready representation deliberately.

Preserve the distinction between:

```text
Scientific Source Data
```

and:

```text
Algorithm-Ready Derived Representation
```

---

### 12.2 IIRS

Canonical V1 should not implement native full-cube IIRS processing unless `V1_SCOPE.md` explicitly changes.

If an externally prepared deterministic IIRS-derived 2D image is used:

- identify it as derived
- preserve available provenance
- do not describe it as native hyperspectral support

---

# 13. Phase 3 — Minimal Preprocessing

V1 preprocessing should remain deliberately small.

Potential operations, when justified, include:

- conversion of suitable input to a compatible grayscale representation
- finite-value handling
- mask/no-data handling
- deterministic intensity normalization
- simple contrast normalization when canonical configuration permits it

Do not construct a complex enhancement pipeline merely to improve scores.

---

### 13.1 Preprocessing Requirements

Every canonical preprocessing operation SHOULD be:

- deterministic where practical
- explicit
- reproducible
- documented
- stable for benchmark comparison
- configurable when scientifically meaningful

No hidden pair-specific enhancement is allowed.

---

### 13.2 No Advanced Sensor Routing

Do not introduce tuned branches such as:

```text
if OHRC:
    special high-performing preprocessing

if TMC-2:
    different tuned pipeline
```

as canonical V1 behavior unless authoritative V1 scope explicitly allows them.

Advanced sensor-aware processing belongs primarily to V2.

---

### 13.3 No False Resolution Recovery

If imagery is resized:

> resizing changes representation, not sensor-measured physical detail.

Do not rewrite original physical-resolution metadata as though upsampling recovered new terrain information.

---

# 14. Phase 4 — SIFT / RootSIFT

V1 uses SIFT as the primary classical feature baseline.

If RootSIFT is supported as part of V1, it must be explicitly identifiable as a separate standardized configuration where required.

Do not silently alternate between:

```text
SIFT
```

and:

```text
RootSIFT
```

under the same benchmark label.

---

### 14.1 Feature Output

Conceptually:

```text
Source Representation
        ↓
Source Keypoints + Descriptors

Reference Representation
        ↓
Reference Keypoints + Descriptors
```

Preserve:

- source/reference identity
- coordinate order
- descriptor/keypoint alignment

---

### 14.2 Feature Failure

Handle explicitly:

- zero detected keypoints
- missing descriptors
- insufficient features

Do not attempt normal descriptor matching against absent descriptor data.

---

# 15. Phase 5 — Descriptor Matching

Implement or reuse conventional descriptor matching according to canonical V1 configuration.

Potential strategies may include:

- nearest-neighbor matching
- KNN matching
- ratio filtering
- mutual/cross consistency

The actual canonical method and thresholds must come from project configuration/specification.

Do not invent thresholds in this task document.

---

### 15.1 Matcher Output Terminology

Before geometric verification, outputs MUST be treated as:

> **candidate matches**

Do not call them:

- verified matches
- trusted correspondences
- ground truth
- final tie points

---

### 15.2 Candidate Data Integrity

Where the project representation permits, preserve relationships among:

- source feature index
- reference feature index
- source coordinate
- reference coordinate
- descriptor distance/score

Filtering and sorting must not silently break alignment.

---

# 16. Phase 6 — Candidate Filtering

Apply the simple filtering strategy defined by canonical V1.

Filtering MUST be:

- deterministic where practical
- reproducible
- configuration-driven where appropriate
- independent of final benchmark truth

Do not inspect final RMSE and manually tune candidate filtering separately for each benchmark pair.

---

# 17. Phase 7 — RANSAC / Geometric Verification

RANSAC-based geometric verification is a core V1 requirement.

Correct conceptual flow:

```text
Candidate Matches
        ↓
RANSAC / Robust Estimation
        ↓
Initial Geometric Model
        +
Verified Inliers
        +
Rejected Outliers
```

Descriptor matches must not be sent directly to final registration without geometric verification.

---

### 17.1 RANSAC Responsibilities

RANSAC should conceptually provide:

- robust model estimation
- inlier classification
- verified-inlier subset
- rejected-outlier information
- explicit failure when geometry cannot be established

RANSAC inliers are:

> model-consistent correspondences

They are not automatically independent ground truth.

---

### 17.2 Geometric Failure Cases

Handle explicitly:

- insufficient candidate matches
- estimation failure
- non-finite transform
- invalid model
- degenerate geometry
- insufficient verified inliers

Do not force a transformation solely because the numerical library can return something.

---

### 17.3 Inlier-Mask Integrity

Protect:

```text
Candidate Match i
        ↔
Inlier Mask i
```

If correspondences are:

- sorted
- filtered
- sliced
- converted

after mask creation, update the relationship correctly.

A stale mask can silently corrupt V1 results.

---

# 18. Phase 8 — Transform Estimation

Canonical V1 may use:

- affine
- homography

according to `V1_SCOPE.md` and active configuration.

The selected transform model must be explicit.

Do not inspect ground-truth error and silently choose whichever model performs best for each pair unless an experiment explicitly defines oracle selection.

---

### 18.1 Transform Direction

Transformation direction is a critical invariant.

The implementation must explicitly define whether the transform is:

```text
source → reference
```

or:

```text
reference → source
```

Use that direction consistently in:

- estimation
- transformation application
- warping
- metric calculation
- serialization
- visualization

---

### 18.2 Coordinate Convention

Protect the distinction between:

```text
(x, y)
```

and:

```text
(row, column)
```

Likewise preserve:

- source-image pixel space
- reference-image pixel space

Do not allow coordinate meaning to become implicit at module boundaries.

---

### 18.3 Transform Validity

An accepted V1 transform must be finite and valid according to its configured model.

Do not serialize:

- `NaN`
- infinity
- degenerate model output

as successful registration geometry.

---

# 19. Phase 9 — Inlier and Coverage Analysis

After geometry, calculate the V1-defined diagnostics.

Potential information includes:

- candidate count
- verified-inlier count
- inlier ratio
- residuals
- spatial coverage

A returned RANSAC model alone is not sufficient scientific evidence for acceptance.

---

### 19.1 Match-Count Invariant

Protect:

```text
verified_inlier_count
<=
candidate_match_count
```

Violation indicates a data or bookkeeping defect.

---

### 19.2 Spatial Coverage

Use the authoritative project metric where defined.

Potential approaches may include:

- grid coverage
- convex-hull coverage

Do not create a V1-only coverage formula when an authoritative implementation already exists.

Coverage matters because:

```text
many clustered matches
```

may constrain global geometry less reliably than:

```text
fewer well-distributed matches
```

---

# 20. Phase 10 — Registration / Warp

When valid geometry exists, apply the accepted transformation according to project conventions.

Potential outputs may include:

- registered image
- transformed representation
- overlap view
- registered preview

Registration logic should remain separate from scientific acceptance logic.

---

### 20.1 Warp Is Not Validation

Successful image warping does not prove the geometry is accurate.

Do not set V1 status to successful merely because a raster was produced.

---

### 20.2 Warp Failure

Handle appropriately:

- invalid transform
- invalid target geometry
- incompatible shapes
- interpolation failure

Follow existing project error/status conventions.

---

### 20.3 No Identity Fallback

Do not silently use an identity transform when registration fails.

An identity transform is valid only when supported by the actual estimated geometry.

---

# 21. Phase 11 — Evaluation

Evaluate the final canonical V1 transformation with the project's authoritative metrics.

Potential V1 metric categories include:

- candidate match count
- verified inlier count
- inlier ratio
- fit residual
- spatial coverage
- independent check-point RMSE where available
- runtime where defined
- success/rejection/failure

Exact formulas belong in metrics documentation.

---

### 21.1 Fit Points vs Check Points

Where independent check points exist:

```text
Fit Points
    ↓
Estimate Transform

Independent Check Points
    ↓
Evaluate Final Transform
```

Do not use the fitting population as independent validation.

---

### 21.2 When Independent Check Points Are Missing

Lack of independent check points MUST NOT block V1 implementation.

V1 may still report:

- candidate count
- inlier count
- inlier ratio
- fit residuals
- spatial coverage
- status
- visual diagnostics

But it MUST NOT claim independent absolute registration accuracy without valid independent truth.

---

### 21.3 Error Units

Every numerical error must identify its unit and coordinate context.

Potential units include:

- source-image `px`
- reference-image `px`
- projected units
- metres where scientifically valid

Avoid unitless RMSE.

---

### 21.4 Ground Error

Convert to metres only when:

- valid GSD/spatial information exists
- the coordinate context is correct
- the conversion is scientifically meaningful

Do not derive precise physical error from a generic approximate instrument resolution.

---

### 21.5 Sub-Pixel

Sub-pixel accuracy means:

> error less than one pixel in a specified image coordinate space.

It does not automatically mean sub-metre accuracy.

---

# 22. Phase 12 — Failure and Rejection Logic

Failure is a valid V1 result.

Potential scientific rejection conditions may include:

- invalid input
- insufficient features
- insufficient candidates
- insufficient verified inliers
- degenerate geometry
- invalid transform
- inadequate spatial coverage
- evaluation failure under benchmark-defined criteria

Do not invent exact numerical thresholds here.

---

### 22.1 Scientific Failure vs Software Error

Distinguish:

**Expected Scientific Failure**

```text
insufficient reliable correspondences
```

from:

**Software Error**

```text
unexpected indexing or shape defect
```

Where project architecture supports it, expected scientific failure should become structured result/status information rather than an unexplained crash.

---

### 22.2 Status Integrity

A rejected or failed result must not carry semantics implying:

> accepted trustworthy registration

Do not populate success-only fields with fabricated values simply to preserve a uniform output shape.

---

# 23. Phase 13 — Structured Result

Ensure canonical V1 produces one structured scientific result consistent with existing project contracts.

Conceptually, it may preserve:

- source/reference identity
- configuration identity
- preprocessing context
- keypoint counts where retained
- candidate count
- verified-inlier count
- inlier ratio
- transform
- transform model
- transform direction
- spatial coverage
- error metrics
- metric units
- runtime where defined
- status
- failure/rejection reason
- artifact references

Do not invent a duplicate result schema when an appropriate project result type already exists.

---

# 24. Phase 14 — Diagnostic Artifacts

Optional V1 artifacts may include:

- feature visualization
- candidate-match visualization
- verified-inlier visualization
- rejected-outlier visualization
- registered image
- overlay
- residual visualization

These are diagnostic outputs.

They are downstream of authoritative scientific computation.

Visualization code MUST NOT independently redefine:

- RMSE
- inlier membership
- transform validity
- registration confidence
- benchmark status

---

# 25. Testing Plan

Follow:

[`../development/TESTING_RULES.md`](../development/TESTING_RULES.md)

V1 testing should validate both software behavior and scientific invariants.

---

### 25.1 Unit Tests

Relevant unit-level coverage should include, where applicable:

- input validation
- preprocessing behavior
- coordinate utilities
- transform application
- candidate filtering
- empty descriptors
- metric calculations
- spatial coverage
- result/status handling
- serialization semantics

Do not invent test filenames before inspecting the repository.

---

### 25.2 Geometry Tests

Use mathematically controlled synthetic data where appropriate.

Relevant cases include:

- known translation
- known affine transformation
- known homography where supported
- controlled outliers
- insufficient points
- degenerate geometry
- non-finite transform
- explicit transform direction

Do not test geometry only by asserting:

> a matrix was returned.

---

### 25.3 Failure Tests

Potential cases include:

- blank image
- constant image
- feature-poor image
- no descriptors
- no candidate matches
- insufficient RANSAC support
- degenerate correspondence geometry
- invalid transform
- controlled non-overlap where applicable

Expected rejection should not become an unrelated software crash.

---

### 25.4 Inlier-Mask Regression

Protect:

```text
candidate ordering
        ↓
inlier mask
        ↓
verified-inlier extraction
```

Sorting/filtering must not make the mask stale.

---

### 25.5 Integration Test

Create or reuse a small controlled pair that exercises:

```text
Input
  ↓
Validation
  ↓
Preprocessing
  ↓
SIFT
  ↓
Matching
  ↓
RANSAC
  ↓
Transform
  ↓
Registration
  ↓
Evaluation
  ↓
Structured Result
```

Normal integration testing should not require a full mission dataset.

---

### 25.6 Real Lunar Smoke Run

After software correctness is established, execute V1 on at least one verified real known-overlap lunar pair **when appropriate data is available**.

The purpose is domain confirmation, not a full benchmark.

Do not invent a required pair count.

Do not fabricate results when real data is unavailable.

---

## 26. Benchmark Integration

When benchmark infrastructure exists, V1 should integrate through it rather than implementing a separate benchmark-specific scientific pipeline.

Conceptually:

```text
Benchmark Definition
        ↓
Canonical V1 Configuration
        ↓
Reusable Scientific Core
        ↓
Structured Registration Result
        ↓
Benchmark Metrics / Result Record
```

The benchmark runner should:

- load controlled pair(s)
- select canonical V1 configuration
- invoke reusable scientific code
- preserve failures
- collect authoritative metrics
- preserve run context

It should not duplicate SIFT, RANSAC, transforms, or metrics.

Follow:

[`BENCHMARK_RULES.md`](BENCHMARK_RULES.md)

---

## 27. Reproducibility

A canonical V1 result should ideally remain traceable to:

```text
Input Pair
+
Effective Configuration
+
Code Revision
+
Random Seed where relevant
+
Metric Definition
```

Use existing repository mechanisms.

Do not invent a new result or provenance system solely for this task when suitable infrastructure exists.

---

### 27.1 Randomness

If RANSAC or another V1 dependency uses randomness:

- use repository-supported seed control where practical
- preserve relevant seed information
- do not promise determinism that the underlying implementation cannot guarantee

---

### 27.2 No Hidden Local State

Canonical V1 execution should not depend on:

- current notebook state
- random files in a local directory
- developer-specific absolute paths
- manually changed globals
- unrecorded interactive selections

---

## 28. Dependencies

Prefer the smallest dependency set needed by the classical baseline.

Before adding a dependency, inspect:

- project dependency manifest
- lockfiles where present
- existing imports
- repository dependency policy

Do not assume a particular CV library is installed without evidence.

---

### 28.1 V1 Dependency Exclusions

Do not add solely for canonical V1:

- large neural frameworks
- FAISS
- GPU-only packages
- model checkpoint infrastructure
- distributed execution frameworks

unless an already-established repository dependency is genuinely required for existing shared infrastructure.

---

### 28.2 Dependency Addition Criteria

If a new dependency is genuinely required, document:

- dependency
- purpose
- whether required or optional
- effect on V1

Do not add a package for trivial functionality already available safely through current dependencies or the standard library.

---

## 29. CLI, API, and UI Boundaries

### CLI / Script

If V1 requires a runnable entry point, prefer a thin caller:

```text
Parse Inputs
    ↓
Load Configuration
    ↓
Call V1 / Core Pipeline
    ↓
Present / Persist Result
```

Do not place the complete scientific implementation inside the CLI script.

---

### API

Backend/API integration is not inherently required for canonical V1.

If existing services need V1:

```text
API / Service
      ↓
Scientific Core
```

Do not duplicate V1 algorithms inside routes/controllers.

---

### UI

A polished frontend is not a V1 scientific completion requirement.

UI remains downstream of:

1. correspondence
2. geometric verification
3. transformation
4. numerical evaluation
5. diagnostic registration output

Do not use UI-first development to delay scientific correctness.

---

### Mosaic

Mosaic generation is downstream of reliable registration.

It is not part of canonical V1 core completion.

---

## 30. Implementation Task Table

Exact paths MUST be determined from repository inspection.

| ID     | Task                                            | Primary Responsibility | Depends On              | Expected Evidence                                 | Status |
| ------ | ----------------------------------------------- | ---------------------- | ----------------------- | ------------------------------------------------- | ------ |
| V1-001 | Audit existing V1-relevant repository code      | Repository inspection  | —                       | REUSE / MODIFY / CREATE assessment                | —      |
| V1-002 | Confirm canonical V1 scope                      | Task planning          | V1-001                  | No conflicts with `V1_SCOPE.md`                   | —      |
| V1-003 | Confirm input/result contracts                  | Domain/contracts       | V1-001                  | Existing or justified contract identified         | —      |
| V1-004 | Implement/reuse input validation                | Data / I/O             | V1-003                  | Controlled valid/invalid behavior                 | —      |
| V1-005 | Implement/reuse minimal preprocessing           | Preprocessing          | V1-004                  | Deterministic registration-ready representation   | —      |
| V1-006 | Implement/reuse SIFT feature extraction         | Features               | V1-005                  | Keypoints/descriptors or explicit failure         | —      |
| V1-007 | Implement/reuse descriptor matching             | Matching               | V1-006                  | Candidate correspondences                         | —      |
| V1-008 | Implement canonical candidate filtering         | Matching               | V1-007                  | Reproducible filtered candidates                  | —      |
| V1-009 | Implement/reuse RANSAC verification             | Geometry               | V1-008                  | Transform + aligned inlier/outlier classification | —      |
| V1-010 | Enforce transform model/direction semantics     | Geometry               | V1-009                  | Direction/model protected by tests                | —      |
| V1-011 | Implement/reuse inlier diagnostics and coverage | Evaluation             | V1-009                  | Counts, ratio, residual/coverage evidence         | —      |
| V1-012 | Implement/reuse registration warp               | Registration           | V1-010                  | Registered result only for valid geometry         | —      |
| V1-013 | Implement/reuse V1 evaluation                   | Evaluation             | V1-011, V1-012          | Metrics with units and correct population         | —      |
| V1-014 | Implement failure/rejection handling            | Pipeline/domain        | V1-009–V1-013           | Explicit scientific failure semantics             | —      |
| V1-015 | Produce canonical structured result             | Contracts/domain       | V1-003–V1-014           | One coherent result representation                | —      |
| V1-016 | Add/update unit and geometry tests              | Tests                  | Relevant implementation | Executed test evidence when run                   | —      |
| V1-017 | Add/update failure-path tests                   | Tests                  | V1-014                  | Explicit rejection paths verified                 | —      |
| V1-018 | Add/update V1 integration test                  | Tests                  | V1-015                  | Controlled end-to-end execution                   | —      |
| V1-019 | Integrate with benchmark infrastructure         | Benchmarking           | V1-015                  | Core reused; failures preserved                   | —      |
| V1-020 | Perform real lunar smoke run when data permits  | Domain validation      | V1-018                  | Actual execution evidence or Not Run              | —      |
| V1-021 | Synchronize affected documentation              | Documentation          | Implementation complete | Docs reflect actual behavior                      | —      |
| V1-022 | Perform final validation and completion review  | QA                     | All required tasks      | Accurate execution report                         | —      |

The `Status` column must be updated only from actual evidence.

Do not mark tasks complete merely because this plan exists.

---

## 31. Implementation Checkpoints

### Checkpoint A — Repository Audit

Required evidence:

- current architecture inspected
- reusable components identified
- likely modifications identified
- duplicate implementation avoided

---

### Checkpoint B — Contracts Confirmed

Required evidence:

- input representation understood
- result representation understood
- transform direction policy known
- coordinate conventions understood

---

### Checkpoint C — Validation and Preprocessing

Required evidence:

- malformed inputs handled
- algorithm-ready representation produced
- source scientific data not silently overwritten

---

### Checkpoint D — Classical Features

Required evidence:

- canonical SIFT/RootSIFT behavior selected
- keypoint/descriptor alignment preserved
- empty-feature behavior handled

---

### Checkpoint E — Candidate Matching

Required evidence:

- candidate matches generated
- filtering reproducible
- candidates correctly distinguished from verified inliers

---

### Checkpoint F — Geometry

Required evidence:

- RANSAC verification operational
- inlier mask aligned
- degenerate geometry rejected
- transform direction explicit

---

### Checkpoint G — Registration

Required evidence:

- valid transform can drive registration
- invalid geometry cannot silently produce accepted registration

---

### Checkpoint H — Evaluation and Rejection

Required evidence:

- scientific diagnostics emitted
- units explicit
- coverage available where required
- failure/rejection explicit

---

### Checkpoint I — Testing

Required evidence:

- relevant unit tests executed
- geometry tests executed
- failure-path tests executed
- integration test executed

Execution evidence must be real.

---

### Checkpoint J — Domain / Benchmark Integration

Required evidence when applicable:

- benchmark runner reuses core implementation
- real lunar smoke run performed when appropriate data exists
- otherwise clearly reported as Not Run

---

## 32. Potential Blockers

Potential blockers should be recorded when encountered.

Examples include:

- no verified known-overlap lunar pair
- unclear transformation direction
- missing or incompatible dependency
- undefined result contract
- missing benchmark manifest
- unavailable independent check points
- unsupported input representation
- unclear coordinate convention

These examples are not claims that the blockers currently exist.

---

### 32.1 Ground-Truth Blocker

Missing independent check points do not block implementation of V1.

They block claims of independent absolute registration accuracy.

Until valid check points exist, V1 may report appropriate diagnostics such as:

- fit residuals
- inlier statistics
- coverage
- status

---

### 32.2 Data Blocker

If real lunar benchmark data is unavailable during implementation, use appropriate:

- synthetic geometry
- compact test fixtures
- legal existing sample crops

for software validation.

Do not fabricate lunar benchmark measurements.

---

### 32.3 Dependency Blocker

If a required dependency is unavailable:

- use the repository's dependency-management process
- determine whether it is genuinely required

Do not silently substitute another algorithm and preserve the same V1 label.

---

## 33. Definition of Done

V1 is not complete merely because an aligned image can be displayed.

---

### 33.1 Implementation Definition of Done

Canonical V1 implementation should satisfy all applicable items:

- [ ] `V1_SCOPE.md` remains authoritative and unchanged unless separately required.
- [ ] Known-overlap input path exists.
- [ ] Input validation exists.
- [ ] Minimal deterministic preprocessing exists.
- [ ] Canonical SIFT/RootSIFT configuration is explicit.
- [ ] Candidate matching exists.
- [ ] Candidate filtering is reproducible.
- [ ] RANSAC/geometric verification exists.
- [ ] Candidate matches and verified inliers remain distinct.
- [ ] Inlier-mask alignment is preserved.
- [ ] Transform model is explicit.
- [ ] Transform direction is explicit.
- [ ] Coordinate conventions are explicit.
- [ ] Degenerate/non-finite geometry is rejected.
- [ ] Registration uses accepted geometry.
- [ ] Spatial coverage is available where required.
- [ ] Numerical evaluation exists.
- [ ] Error units are explicit.
- [ ] Failure/rejection is explicit.
- [ ] Identity transform is not used as silent failure fallback.
- [ ] One coherent scientific result is produced.
- [ ] No unnecessary duplicate core algorithms exist.
- [ ] No hidden pair-specific tuning exists.
- [ ] No hard-coded local paths are required.
- [ ] No fake measurements are present.
- [ ] No unnecessary V2/V3/V4 dependencies were introduced.
- [ ] Relevant tests were added or updated.
- [ ] Affected documentation matches implementation.

No benchmark pair count is required by this document.

---

### 33.2 Verification Levels

Keep these statuses separate.

| Level                     | Meaning                                                                     |
| ------------------------- | --------------------------------------------------------------------------- |
| **Specified**             | V1 requirements are documented                                              |
| **Implemented**           | Required code path exists                                                   |
| **Unit Verified**         | Relevant unit tests were actually executed successfully                     |
| **Integration Verified**  | Controlled end-to-end V1 software flow was actually executed successfully   |
| **Domain Smoke Verified** | V1 was actually executed on a verified real lunar known-overlap pair        |
| **Benchmarked**           | Defined scientific V1 benchmark was actually executed under benchmark rules |

Do not claim a higher level without execution evidence.

---

### 33.3 Weak Performance Is Not Incomplete Implementation

V1 may fail or perform poorly under:

- large scale gaps
- severe illumination differences
- cross-modality conditions
- repetitive terrain
- low-feature terrain

If the pipeline:

- follows canonical V1 methodology
- reports failure honestly
- produces reproducible evidence

then weak performance is a valid baseline result.

Do not add later-version methods merely to hide V1 limitations.

---

## 34. Scope Protection

### No Retrieval

Do not add:

```text
Reference Tiles
    ↓
Global Descriptor
    ↓
FAISS
    ↓
Top-K
```

to canonical V1.

V1 already assumes the relevant overlap is known.

---

### No Automatic Multi-Scale Search

Do not add full reference-pyramid search merely because V1 fails across a large scale gap.

Such limitations are useful evidence motivating V2.

---

### No Learned Matching

Do not introduce:

- ALIKED
- LightGlue
- LoFTR
- SuperPoint
- SuperGlue

into canonical V1.

---

### No Full IIRS Pipeline

Do not add native hyperspectral representation selection/research to canonical V1.

---

### No Advanced Geometry

Do not add:

- DEM-aware registration
- piecewise warps
- dense local deformation
- bundle adjustment

to V1.

---

### No Advanced Refinement

If `V1_SCOPE.md` excludes canonical sub-pixel refinement, do not introduce it merely to reduce RMSE.

A refinement experiment must be clearly non-canonical or assigned to the appropriate later benchmark.

---

## 35. Bug Fix vs Scope Expansion

During V1 implementation, distinguish correctness fixes from new methodology.

### Bug Fix

Examples:

- incorrect source/reference ordering
- stale inlier mask
- unit-conversion defect
- invalid transform accepted

These restore intended V1 behavior.

### Scope Expansion

Examples:

- SIFT replaced by LightGlue
- FAISS added
- DEM-based warping introduced
- automatic pyramid search introduced

These change methodology and belong outside canonical V1 unless the authoritative scope is revised.

---

## 36. Scientific Integrity Rules

Canonical V1 implementation MUST NOT use:

- fabricated correspondences
- manually edited transforms
- manually edited metrics
- benchmark-case-specific hard-coded answers
- filename-specific shortcuts
- hidden check-point use during fitting
- secret pair-specific threshold tuning
- silent exclusion of failed benchmark cases
- fabricated accuracy
- fabricated confidence
- fabricated runtime

Never implement logic equivalent to:

```text
if pair_id is known benchmark case:
    return known transform
```

That invalidates the benchmark.

---

### 36.1 No Manual Match Editing

Do not manually add or remove correspondences from canonical automated V1 results merely to improve registration.

Manual annotations may belong to:

- ground truth
- evaluation
- research analysis

They must not become hidden algorithm corrections.

---

### 36.2 No Baseline Gaming

Do not intentionally make V1 artificially strong or weak.

V1 should be a credible standard classical baseline.

Do not:

- sabotage it with unreasonable parameters
- rescue it with advanced later-version methods

---

## 37. Minimal-Diff and Refactoring Rules

Implement V1 with the smallest coherent repository change set.

Avoid unrelated:

- repository-wide renaming
- broad formatting
- architecture rewrites
- dependency upgrades
- cleanup campaigns

unless necessary for V1 correctness.

---

### 37.1 Preserve Existing Work

Do not overwrite unrelated:

- implementation
- experiments
- notebooks
- documentation
- benchmark work

Inspect before replacing files.

---

### 37.2 Prototype Migration

If an existing prototype must be promoted into reusable core code, prefer:

```text
Existing Prototype
        ↓
Extract Reusable Logic
        ↓
Add Tests
        ↓
Switch Canonical Caller
        ↓
Verify
        ↓
Deprecate / Remove Duplicate Only If Appropriate
```

Do not delete the working prototype before validating its replacement.

---

### 37.3 No Premature Framework

Do not introduce:

- plugin frameworks
- large abstract-factory hierarchies
- dependency-injection frameworks
- microservices
- queues
- distributed workers
- cloud databases
- Kubernetes

merely to make V1 appear production-grade.

A clean modular scientific Python pipeline is sufficient when it meets project needs.

---

## 38. Performance and Logging

Correctness and scientific validity come first.

Avoid obvious unnecessary work such as:

- repeated feature extraction within the same run
- unnecessary full-resolution copies
- repeatedly reloading identical input during one pipeline execution

Do not optimize speculative bottlenecks without evidence.

Canonical V1 should preferably remain CPU-capable where practical.

Do not claim specific runtime without measurement.

---

### 38.1 Logging

Useful V1 logs may include:

- pair/input identity
- current stage
- keypoint count
- candidate count
- inlier count
- transform-estimation status
- failure reason
- runtime information

Follow repository logging conventions.

Do not dump full scientific arrays or descriptor matrices into ordinary logs.

---

## 39. Security and File Safety

Treat externally supplied imagery and metadata as untrusted at software boundaries.

V1 work must not introduce:

- unsafe archive extraction
- unsafe deserialization
- shell injection
- arbitrary writes outside intended output locations

Follow repository security policy.

Security enhancements unrelated to V1 should not become scope-expanding infrastructure work unless required.

---

## 40. Documentation Synchronization

After implementation, review documentation affected by actual behavior changes.

Potentially relevant files include:

- root `README.md`
- `../context/V1_SCOPE.md`
- `../architecture/PIPELINE.md`
- `../architecture/MODULE_MAP.md`
- `../architecture/DATA_FLOW.md`
- benchmark documentation
- changelog where appropriate

Do not edit documents unnecessarily.

Do not mark V1 as implemented until implementation actually exists.

---

### 40.1 Status Language

Keep these distinct:

```text
Specified
→ requirements exist

Implemented
→ code exists

Tested / Verified
→ applicable validation was actually executed

Benchmarked
→ scientific benchmark was actually executed
```

Do not collapse them.

---

## 41. Final Validation

Use narrow-to-broad validation.

Conceptually:

```text
Affected Unit Tests
        ↓
V1 Geometry / Failure Tests
        ↓
V1 Integration Test
        ↓
Relevant Broader Repository Tests
        ↓
Real Lunar Smoke Run when applicable
        ↓
Scientific Benchmark when requested
```

Do not run an expensive scientific benchmark merely to compensate for missing unit tests.

---

### 41.1 Test Execution Reporting

Use explicit execution states:

- **Passed**
- **Failed**
- **Skipped**
- **Not Run**

Never convert:

```text
Not Run
```

into:

```text
Passed
```

---

### 41.2 No Fake Test Claims

Never report:

> All tests passed.

unless the relevant tests actually executed successfully.

Code inspection alone is not test execution.

---

### 41.3 No Fake Benchmark Claims

Never report:

> V1 benchmark completed.

unless it actually ran.

Do not infer V1 performance from unit or integration tests.

---

## 42. Stop Conditions

An AI coding agent assigned specifically to V1 should stop when:

- requested V1 implementation work is complete
- applicable validation has been performed
- affected documentation is synchronized
- remaining blockers/limitations are reported

Do not automatically continue into:

- V2
- V3
- V4
- global retrieval
- advanced IIRS research
- UI redesign
- mosaic generation
- unrelated refactoring

without explicit scope expansion.

---

## 43. Completion Report Requirements

After implementation work, the completion report should state:

- files changed
- behavior implemented
- components reused
- new components introduced
- tests executed
- tests passed
- tests failed
- tests skipped
- tests not run
- real lunar smoke-run status
- scientific benchmark status
- known limitations
- blockers
- documentation updated
- follow-up work outside V1 scope

Do not include invented scientific results.

If the benchmark was not executed, state:

> **Benchmark not run.**

---

## 44. V1 Implementation Review Checklist

Before declaring V1 implementation complete, verify:

- [ ] `V1_SCOPE.md` was reviewed.
- [ ] Actual repository structure was inspected.
- [ ] Existing functionality was searched before creating modules.
- [ ] Module ownership follows `MODULE_MAP.md`.
- [ ] No unnecessary duplicate SIFT implementation was added.
- [ ] No unnecessary duplicate RANSAC implementation was added.
- [ ] No unnecessary duplicate metric implementation was added.
- [ ] Known-overlap local registration remains the canonical mode.
- [ ] Whole-Moon/global retrieval was not added.
- [ ] FAISS was not added as a V1 requirement.
- [ ] Learned global descriptors were not added.
- [ ] ALIKED/LightGlue/LoFTR were not added to canonical V1.
- [ ] Native/full IIRS hyperspectral processing was not silently added.
- [ ] Advanced sensor routing was not silently added.
- [ ] Automatic reference-pyramid search was not silently added.
- [ ] DEM/local warping was not added.
- [ ] Minimal preprocessing is deterministic where practical.
- [ ] Scientific input data remains distinguishable from algorithm-ready derived representations.
- [ ] No-data/masks are respected where applicable.
- [ ] Scientific input is not blindly assumed to be 8-bit.
- [ ] SIFT/RootSIFT configuration is explicit.
- [ ] Candidate matching exists.
- [ ] Matcher output is correctly treated as candidate matches.
- [ ] Candidate index/coordinate alignment is preserved.
- [ ] RANSAC/geometric verification exists.
- [ ] Verified inliers are represented separately from candidates.
- [ ] Inlier mask remains correctly aligned.
- [ ] RANSAC inliers are not presented as ground truth.
- [ ] Transformation model is explicit.
- [ ] Transformation direction is explicit.
- [ ] Source/reference coordinate roles are explicit.
- [ ] `(x, y)` vs `(row, column)` semantics are protected.
- [ ] Degenerate/non-finite transforms are rejected.
- [ ] Spatial coverage is measured where required.
- [ ] Registration uses valid accepted geometry.
- [ ] Registered preview is not treated as proof of accuracy.
- [ ] Numerical evaluation uses authoritative metrics.
- [ ] Accuracy/error metrics include units.
- [ ] Fit points and independent check points are distinguished.
- [ ] Ground metres are reported only when scientifically justified.
- [ ] Failure/rejection is explicit.
- [ ] Identity transform is not used as silent fallback.
- [ ] Structured result follows existing contracts where possible.
- [ ] Diagnostic artifacts do not redefine scientific values.
- [ ] Unit tests cover relevant core behavior.
- [ ] Geometry tests protect transform direction.
- [ ] Failure-path tests exist where appropriate.
- [ ] Inlier-mask alignment is tested.
- [ ] Integration test exercises the canonical V1 path.
- [ ] Real lunar smoke run is performed only when suitable data is available.
- [ ] Benchmark integration reuses core scientific code.
- [ ] Canonical configuration is reproducible.
- [ ] No hidden pair-specific tuning exists.
- [ ] Evaluation truth was not used to select normal V1 parameters.
- [ ] No hard-coded benchmark answers exist.
- [ ] No manually edited canonical correspondences/results exist.
- [ ] No fake metric exists.
- [ ] No fake confidence exists.
- [ ] No fake benchmark result exists.
- [ ] No unnecessary neural/GPU dependency was added.
- [ ] No unrelated large refactor was included.
- [ ] Existing unrelated user/research work was preserved.
- [ ] Documentation was updated only where required.
- [ ] Actual test execution is reported accurately.
- [ ] Benchmark execution status is reported accurately.
- [ ] Benchmark V1 was not confused with software release v1.0.0.

---

## 45. Related Documents

Read these documents as needed before modifying V1:

[`../../AGENTS.md`](../../AGENTS.md)
→ repository-level AI/contributor instructions

[`../README.md`](../README.md)
→ `.ai/` navigation and context-loading guidance

[`../ENGINEERING_RULES.md`](../ENGINEERING_RULES.md)
→ repository-wide engineering rules

[`../context/PROJECT_CONTEXT.md`](../context/PROJECT_CONTEXT.md)
→ ChandraMap project identity and scientific goal

[`../context/DOMAIN_CONTEXT.md`](../context/DOMAIN_CONTEXT.md)
→ lunar imaging and registration constraints

[`../context/TERMINOLOGY.md`](../context/TERMINOLOGY.md)
→ canonical project terminology

[`../context/DATASETS.md`](../context/DATASETS.md)
→ scientific product, metadata, and provenance rules

[`../context/V1_SCOPE.md`](../context/V1_SCOPE.md)
→ authoritative canonical V1 scope

[`../architecture/SYSTEM_OVERVIEW.md`](../architecture/SYSTEM_OVERVIEW.md)
→ high-level system architecture

[`../architecture/PIPELINE.md`](../architecture/PIPELINE.md)
→ scientific stage ordering

[`../architecture/MODULE_MAP.md`](../architecture/MODULE_MAP.md)
→ repository responsibility ownership

[`../architecture/DATA_FLOW.md`](../architecture/DATA_FLOW.md)
→ data lineage, coordinates, units, and result semantics

[`../development/CODING_RULES.md`](../development/CODING_RULES.md)
→ source-code implementation rules

[`../development/TESTING_RULES.md`](../development/TESTING_RULES.md)
→ validation and testing rules

[`../development/DOCUMENTATION_RULES.md`](../development/DOCUMENTATION_RULES.md)
→ documentation correctness rules

[`../development/BENCHMARK_RULES.md`](../development/BENCHMARK_RULES.md)
→ benchmark governance and methodology

If another referenced file does not exist in the current repository, do not invent it. Use the actual repository structure discovered during implementation.

---

## 46. Key Rules for AI Agents

1. Read `V1_SCOPE.md` before implementing V1.

2. Inspect the actual repository before creating files.

3. Search for existing implementations before writing duplicates.

4. Follow `MODULE_MAP.md` for ownership.

5. Benchmark V1 is not software v1.0.0.

6. V1 assumes a known overlapping source/reference pair.

7. Do not add whole-Moon retrieval to V1.

8. Do not add FAISS to V1.

9. Do not add learned global descriptors to V1.

10. Do not add Top-K reference retrieval to V1.

11. Do not add ALIKED, LightGlue, or LoFTR to canonical V1.

12. Do not add full native IIRS hyperspectral processing to canonical V1.

13. Do not add advanced sensor routing to V1.

14. Do not add automatic reference-pyramid search to V1.

15. Do not add DEM-aware or local/piecewise warping to V1.

16. Keep preprocessing minimal and reproducible.

17. Use SIFT/RootSIFT according to the approved canonical configuration.

18. Keep SIFT and RootSIFT configurations explicitly distinguishable.

19. Matcher output is candidate correspondence data.

20. Candidate matches must pass geometric verification.

21. RANSAC produces model-consistent verified inliers and initial geometry.

22. RANSAC inliers are not independent ground truth.

23. Protect candidate/inlier-mask alignment.

24. Transformation direction must be explicit.

25. Coordinate conventions must be explicit.

26. Preserve source and reference coordinate spaces.

27. Units must be explicit.

28. Spatial coverage matters.

29. Do not accept geometry solely because RANSAC returned a matrix.

30. Do not force a transformation when geometric evidence is insufficient.

31. Warping does not prove registration accuracy.

32. Prefer independent check points for final accuracy where they exist.

33. Fit-point RMSE is not independent registration accuracy.

34. Convert error to metres only with valid spatial context.

35. Sub-pixel does not automatically mean sub-metre.

36. Failure/rejection is a valid V1 outcome.

37. Do not silently return an identity transform after failure.

38. Do not invent thresholds.

39. Do not tune parameters secretly per benchmark pair.

40. Do not use final evaluation truth to choose normal V1 parameters.

41. Do not hard-code benchmark answers.

42. Do not manually edit canonical matches, transforms, or metrics.

43. Keep canonical V1 CPU-capable where practical.

44. Keep V1 dependencies minimal.

45. Do not add neural/GPU dependencies merely to improve V1 results.

46. Put reusable scientific behavior in shared core modules.

47. Keep benchmark runners, scripts, and CLI entry points thin.

48. Keep API/UI layers downstream of the scientific core.

49. Do not make mosaic or map UI work part of V1 scientific completion.

50. Add tests for geometry, coordinate semantics, failure behavior, and result integrity.

51. Preserve reproducibility information.

52. Report test execution exactly as Passed, Failed, Skipped, or Not Run.

53. Never claim a test passed if it was not executed.

54. Never claim a benchmark was run if it was not executed.

55. Do not invent RMSE, coverage, inlier ratio, runtime, success rate, or confidence values.

56. Do not confuse `Specified`, `Implemented`, `Verified`, and `Benchmarked`.

57. Weak V1 scientific performance can still be a valid baseline result.

58. Distinguish bug fixes from scope expansion.

59. Do not tune V1 to look artificially strong or weak.

60. Preserve existing unrelated implementation and research work.

61. Keep the V1 change set focused and minimal.

62. Stop when the requested V1 work is complete.

63. Do not continue automatically into V2, V3, or V4.

64. The benchmark exists to measure the baseline, not to produce attractive numbers.

<!-- Source specification: :contentReference[oaicite:0]{index=0} -->
