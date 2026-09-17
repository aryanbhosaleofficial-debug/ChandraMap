# ChandraMap Core Engine Architecture

The **ChandraMap Core Engine** is the reusable scientific processing layer responsible for lunar image correspondence, geometric verification, registration, evaluation, and explicit scientific rejection.

Conceptually, the engine accepts:

```text
Source Observation
+
Reference Observation / Candidate Region
+
Scientific Context
+
Processing Configuration
+
Optional Evaluation Truth
```

and produces:

```text
Candidate Correspondence Evidence
+
Verified Geometry
+
Final Source → Reference Transform
+
Registration Output
+
Scientific Metrics
+
Accept / Reject Status
+
Failure / Rejection Context
```

The core architectural principle is:

> **Scientific registration logic must remain reusable independently of benchmarks, CLI tools, APIs, notebooks, frontend applications, maps, mosaics, reporting, deployment, and infrastructure.**

This document defines the **logical internal architecture** of that reusable scientific engine.

It does not claim that every logical component described here already exists as a separate implementation module.

---

## 1. Purpose

This document defines:

- what belongs inside the ChandraMap scientific core
- what must remain outside the core
- the major logical scientific components
- the responsibilities of those components
- allowed dependency directions
- scientific concepts that cross component boundaries
- how matcher implementations remain replaceable
- how retrieval remains separate from local correspondence
- how matching remains separate from geometric verification
- how transformation semantics remain explicit
- how optional refinement integrates safely
- how registration remains separate from evaluation
- how rejection becomes a first-class scientific result
- how Benchmark V1–V4 compose the same capabilities
- how outer applications consume one authoritative scientific result
- how experimental methods can enter the architecture without destabilizing the core

This document intentionally does **not** define:

- concrete Python classes
- repository module paths
- function signatures
- serialized schemas
- API endpoints
- database models
- configuration frameworks
- deployment infrastructure
- benchmark results

Actual repository ownership belongs in the module-map documentation.

Detailed stage ordering for canonical V1 belongs in [`v1-pipeline.md`](./v1-pipeline.md).

---

## 2. What Is the Core Engine?

The ChandraMap Core Engine is a reusable scientific processing engine that can conceptually:

1. receive explicit source and reference observations
2. validate scientific inputs
3. preserve scientific context and provenance
4. derive algorithm-appropriate image representations
5. optionally handle scale-related representations
6. obtain or receive a reference candidate
7. generate local candidate correspondences
8. filter candidate evidence
9. verify correspondences geometrically
10. estimate explicit source→reference geometry
11. assess geometric support
12. optionally refine verified tie points
13. establish the final transform
14. register the source observation
15. evaluate the final scientific result
16. accept or reject the registration
17. return one structured scientific outcome

The engine is not one algorithm.

It is the reusable composition of scientific capabilities required to produce and evaluate lunar registration results.

---

## 3. Core Engine Boundary

The core engine must remain independently usable without requiring:

- a frontend
- an HTTP server
- an API request object
- an interactive map
- a benchmark-report renderer
- a notebook session
- a deployment platform
- a database
- cloud infrastructure

Conceptually:

```text
OUTSIDE CORE
─────────────────────────────────────────────────────────
CLI
API / Service Adapters
Frontend
Benchmark Runner
Research Notebook
Web Map
Mosaic Builder
Reports
Deployment / Infrastructure
─────────────────────────────────────────────────────────
                         │
                         ▼
═════════════════════════════════════════════════════════
                    CORE ENGINE
═════════════════════════════════════════════════════════
Input / Validation
        ↓
Scientific Context
        ↓
Representation / Preprocessing
        ↓
Scale Handling                 [optional/version-dependent]
        ↓
Reference Candidate Resolution
        ↓
Local Correspondence
        ↓
Candidate Filtering
        ↓
Geometric Verification
        ↓
Geometric Quality
        ↓
Refinement                     [optional]
        ↓
Final Transform
        ↓
Registration / Warp
        ↓
Final Evaluation
        ↓
Accept / Reject
        ↓
Structured Scientific Result
═════════════════════════════════════════════════════════
```

Outer layers consume the engine.

The engine must not depend on those outer layers to perform scientific registration.

---

## 4. Current vs Logical Architecture

This document defines a **logical architectural contract**.

A component appearing here does not prove that:

- a dedicated module currently exists
- the capability is implemented
- the capability is tested
- the capability is benchmarked
- a particular interface has been finalized

Where repository implementation evidence is unavailable, treat the component as a responsibility boundary rather than a claim about current source layout.

Keep these states distinct:

- **Documented**
- **Implemented**
- **Tested**
- **Experimental**
- **Planned**
- **Proposed**

Target architecture must not be presented as current implementation reality.

---

## 5. Core Engine Design Goals

The engine architecture should optimize for the following priorities.

### Scientific Correctness

Preserve:

- source/reference roles
- coordinate semantics
- transform direction
- units
- correspondence state
- provenance
- failure meaning

---

### Clear Responsibility Boundaries

Each major component should own one coherent scientific responsibility.

For example:

```text
Matcher
→ proposes candidate correspondences

Geometry
→ verifies geometric consistency

Registration
→ applies accepted geometry

Evaluation
→ measures the scientific result
```

---

### Reusability

The same scientific engine should be usable by:

- Benchmark V1–V4
- CLI tools
- scripts
- applications
- APIs
- notebooks
- research experiments

---

### Replaceable Matchers

The architecture should permit classical, learned, detector-free, and research correspondence methods to be compared without rewriting shared geometry and evaluation unnecessarily.

---

### Explicit Coordinates and Transforms

Scientific meaning must not depend on undocumented assumptions about:

- `(x, y)`
- `(row, column)`
- source/reference direction
- image vs geospatial coordinates

---

### Reproducible Configuration

Scientifically meaningful algorithm choices should be explicit and traceable.

---

### First-Class Rejection

The architecture must support:

> **Reliable registration could not be established.**

without fabricating a successful transform.

---

### Centralized Evaluation

Authoritative scientific metrics should have one logical owner rather than being reimplemented independently by:

- benchmarks
- APIs
- frontend code
- notebooks

---

### Benchmark Independence

Low-level scientific capabilities should not need to know whether they were invoked by V1, V2, V3, or V4.

---

### Testability

Scientific components should be independently testable without requiring a full web or deployment stack.

---

## 6. Core Engine Non-Goals

The scientific engine itself should not own:

- HTTP routing
- frontend rendering
- user authentication
- dashboards
- map rendering
- benchmark report styling
- cloud deployment
- infrastructure orchestration
- database administration
- GitHub workflows
- mission-data portal functionality
- repository release automation

These systems may invoke or present core results.

They should not become dependencies of the scientific engine.

---

## 7. Architecture at a Glance

```text
                  Core Engine Request
                         │
                         ▼
              ┌────────────────────┐
              │ Input / Validation │
              └─────────┬──────────┘
                        │
                        ▼
              ┌────────────────────┐
              │ Scientific Context │
              │ + Provenance       │
              └─────────┬──────────┘
                        │
                        ▼
              ┌────────────────────┐
              │ Representation /   │
              │ Preprocessing      │
              └─────────┬──────────┘
                        │
                        ▼
              ┌────────────────────┐
              │ Scale Handling     │
              │ optional           │
              └─────────┬──────────┘
                        │
                        ▼
              ┌────────────────────┐
              │ Reference Candidate│
              │ Resolution         │
              └─────────┬──────────┘
                        │
                        ▼
              ┌────────────────────┐
              │ Local              │
              │ Correspondence     │
              └─────────┬──────────┘
                        │
                        ▼
              ┌────────────────────┐
              │ Candidate Filtering│
              └─────────┬──────────┘
                        │
                        ▼
              ┌────────────────────┐
              │ Geometry / RANSAC  │
              └─────────┬──────────┘
                        │
                        ▼
              ┌────────────────────┐
              │ Quality / Coverage │
              └─────────┬──────────┘
                        │
                        ▼
              ┌────────────────────┐
              │ Refinement         │
              │ optional           │
              └─────────┬──────────┘
                        │
                        ▼
              ┌────────────────────┐
              │ Final Transform    │
              └─────────┬──────────┘
                        │
                        ▼
              ┌────────────────────┐
              │ Registration / Warp│
              └─────────┬──────────┘
                        │
                        ▼
              ┌────────────────────┐
              │ Final Evaluation   │
              └─────────┬──────────┘
                        │
                        ▼
              ┌────────────────────┐
              │ Accept / Reject    │
              └─────────┬──────────┘
                        │
                        ▼
               Structured Core Result
```

Cross-cutting concerns include:

- scientific context
- configuration
- provenance
- coordinate semantics
- units
- failure semantics

---

## 8. Core Request and Result

One engine invocation can be understood conceptually as:

```text
Core Engine Request
=
Source Observation
+
Reference Observation / Candidate
+
Scientific Context
+
Scientific Configuration
+
Optional Independent Evaluation Truth
```

The resulting scientific outcome can be understood as:

```text
Core Engine Result
=
Status
+
Correspondence Evidence
+
Verified Geometry
+
Final Transform
+
Evaluation Metrics
+
Failure / Rejection Information
+
Optional Artifact References
```

These are conceptual contracts.

This document does not define exact field names or data structures.

---

## 9. Core Component Responsibilities

| Component                      | Owns                                         | Does Not Own                      |
| ------------------------------ | -------------------------------------------- | --------------------------------- |
| Validation                     | Input usability                              | Matcher selection                 |
| Scientific Context             | Identity, metadata, provenance meaning       | Final accuracy                    |
| Representation                 | Algorithm-ready scientific representation    | Correspondence                    |
| Preprocessing                  | Reproducible image preparation               | Geometric verification            |
| Scale Handling                 | Comparable sampling/scale strategy           | Feature correctness               |
| Reference Candidate Resolution | Which reference region to try                | Point-level correspondence        |
| Local Correspondence           | Candidate point relationships                | Ground truth                      |
| Candidate Filtering            | Pre-geometry match screening                 | Geometric truth                   |
| Geometry                       | Robust verification and transform estimation | UI/presentation                   |
| Geometric Quality              | Coverage, residual/support evidence          | Independent final accuracy alone  |
| Refinement                     | Improve already verified tie points          | Candidate discovery               |
| Registration                   | Apply accepted transform                     | Decide correctness                |
| Evaluation                     | Authoritative scientific metrics             | Display styling                   |
| Decision Policy                | Accept/reject using defined evidence         | Metric implementation duplication |
| Result                         | Authoritative scientific outcome             | Benchmark aggregation             |
| Diagnostics                    | Visualize scientific state                   | Modify scientific state           |

---

## 10. Scientific Context and Provenance

Scientific context carries the information needed to interpret processing correctly.

Potential context may include:

- source identity
- reference identity
- mission
- instrument
- product identity
- GSD
- projection
- footprint
- acquisition context
- illumination context
- representation provenance

Not every field will necessarily be available.

Unknown information should remain unknown rather than being fabricated.

---

### Context Must Survive Processing

Scientific identity should not disappear immediately after image loading.

Conceptually:

```text
Scientific Product
      ↓
Derived Representation
      ↓
Correspondence
      ↓
Geometry
      ↓
Scientific Result
```

The result should remain traceable to the relevant source/reference context where practical.

---

### Original vs Derived Data

Keep this distinction explicit:

```text
Original Mission / Scientific Product
                ↓
          Processing
                ↓
Derived Registration Representation
```

A normalized, resampled, selected-band, structural, or registered representation must not silently become indistinguishable from its original product.

---

## 11. Input and Validation

### Responsibility

Input validation determines whether the requested scientific processing can begin meaningfully.

Potential concerns include:

- readable input
- non-empty data
- valid dimensions
- supported representation
- finite/usable values
- mask/no-data semantics
- known source/reference roles
- required context for the active methodology

---

### Validation Must Fail Early

Preferred:

```text
Invalid Input
      ↓
Explicit Validation Failure
```

Avoid:

```text
Invalid Input
      ↓
Matcher Crashes Much Later
```

---

### Validation Must Not Own Scientific Method Selection

Validation should not:

- choose whichever matcher appears best
- change thresholds after seeing benchmark truth
- compute final registration accuracy
- silently repair unsupported science

Its responsibility is input usability.

---

## 12. Representation Layer

The representation layer translates scientific observations into forms appropriate for downstream algorithms while preserving provenance.

Possible representations may include:

- panchromatic 2D image
- normalized 2D representation
- structural representation
- selected spectral-band image
- dimensionality-reduced hyperspectral representation

Representation is distinct from matching.

---

### Panchromatic Inputs

Panchromatic observations may already be compatible with ordinary 2D local-feature methods after minimal validated preparation.

That does not eliminate the need to preserve:

- product identity
- numeric semantics
- masks
- scale context

---

### Hyperspectral / IIRS Boundary

IIRS is hyperspectral / imaging-infrared data.

The engine architecture should permit an explicit stage that derives an appropriate registration representation.

Conceptually:

```text
Native IIRS Observation
        ↓
Defined Representation Strategy
        ↓
2D Registration Representation
        ↓
Local Correspondence
```

Do not hard-wire:

```text
IIRS Cube
→ Generic Grayscale
```

as a universal scientific rule.

The choice of representation is methodological and may be benchmark-version dependent.

---

## 13. Preprocessing

### Responsibility

Preprocessing applies reproducible operations needed before correspondence.

Potential operations may include:

- valid-data masking
- numeric preparation
- normalization
- contrast preparation
- filtering
- structural preprocessing

Exact operations belong to the active methodology/configuration.

---

### Preprocessing Principles

Preprocessing should be:

- explicit
- reproducible
- deterministic where practical
- scientifically traceable

Avoid hidden logic such as:

```text
if pair_name == special_case:
    use different enhancement
```

unless the experiment explicitly defines pair-specific/oracle behavior.

---

### Preprocessing Is Not Matching

A preprocessing stage should not declare:

- matches
- inliers
- registration success

It produces a representation consumed by later scientific components.

---

## 14. Scale Handling

Scale handling owns the strategy for comparing imagery at useful sampling scales.

Potential later-version capabilities may include:

- downsampling
- image/reference pyramids
- effective-GSD comparison
- scale hypotheses
- physically informed candidate scale selection

---

### Scale Is Separate from Matching

The local matcher should not necessarily own all scale reasoning.

This separation allows the same matcher to be evaluated under different scale strategies.

---

### Upsampling Principle

The architecture must preserve:

```text
Upsampling
→ changes raster sampling
```

not:

```text
Upsampling
→ creates new physical terrain detail
```

---

### V1 Boundary

Canonical V1 may bypass advanced scale handling.

V2 can activate or extend the scale capability.

This illustrates an important rule:

> **A component can exist architecturally without being enabled by every benchmark configuration.**

---

## 15. Reference Candidate Resolution

Local correspondence needs a reference region.

How that region was discovered should remain separable from point-level matching.

A conceptual **reference candidate provider** may obtain it through different paths.

---

### Known Overlap

Used by canonical Benchmark V1.

```text
Known Reference Region
        ↓
Local Correspondence
```

No global retrieval is required.

---

### Metadata-Constrained Search

Reliable scientific metadata may constrain the reference region using:

- footprint
- approximate location
- projection/geospatial context

Using valid metadata is scientifically appropriate.

---

### Global Retrieval

Later research may perform:

```text
Source Representation
        ↓
Global Descriptor
        ↓
Vector Search
        ↓
Candidate Reference Region(s)
```

Global retrieval answers:

> **Which reference region should we try?**

It does not answer:

> **Which exact points correspond?**

---

## 16. Retrieval Boundary

Global retrieval may be an optional subsystem inside a wider scientific engine or an adjacent pre-registration subsystem.

Either architectural choice must preserve this distinction:

```text
Global Retrieval
→ candidate regions

Local Correspondence
→ candidate point correspondences
```

---

### Global vs Local Descriptors

A **global descriptor** represents an image or region for retrieval.

A **local descriptor** represents local image structure for point-level matching.

They must not be treated as interchangeable.

---

### FAISS Boundary

Where FAISS is used:

```text
Reference / Query Image
        ↓
Global Descriptor Generator
        ↓
Numerical Vector
        ↓
FAISS / Vector Index
        ↓
Candidate Reference IDs
```

FAISS does not own:

- SIFT keypoint extraction
- local descriptor generation
- local point matching
- RANSAC
- homography
- warping
- RMSE

It performs vector indexing/search.

---

## 17. Local Correspondence

### Responsibility

Generate point-level candidate correspondences between a prepared source and selected reference candidate.

This should be a replaceable scientific capability.

Conceptually:

```text
Prepared Source
        +
Prepared Reference
        ↓
Local Correspondence Method
        ↓
Candidate Correspondences
```

---

### Classical Sparse Family

Potential classical methods include:

- SIFT
- RootSIFT-based descriptor configurations
- ORB where a benchmark explicitly uses it

Canonical V1 is based on its approved classical configuration.

---

### Learned Sparse Family

Potential later research may use:

```text
Image
  ↓
ALIKED
  ↓
Sparse Features
  ↓
LightGlue
  ↓
Candidate Correspondences
```

Keep the responsibilities explicit:

- **ALIKED** → sparse feature extraction/description
- **LightGlue** → feature matching

---

### Detector-Free Family

LoFTR is detector-free.

The architecture should not require every matcher internally to provide:

```text
Detector
+
Descriptor
+
Descriptor Matcher
```

Instead, different matcher implementations should converge toward a common downstream scientific meaning:

> **candidate correspondences**

---

### Remote-Sensing Research Methods

Research methods such as RIFT or CFOG may be evaluated where scientifically justified.

Their mention here describes an extension category.

It does not claim implementation.

---

## 18. Normalized Correspondence Boundary

Different matcher families can produce different internal outputs.

Where scientifically possible, downstream geometry should receive an equivalent conceptual representation containing information such as:

- source coordinates
- corresponding reference coordinates
- optional matcher scores/metadata

No concrete class is defined here.

The architectural goal is:

```text
Different Matcher Internals
          ↓
Common Correspondence Meaning
          ↓
Shared Geometry
```

---

### Matcher Output Is Not Scientific Truth

Every matcher should conceptually produce:

> **candidate correspondences**

not:

> final ground-truth lunar correspondences.

Geometric verification remains a separate responsibility.

---

## 19. Candidate Filtering

### Responsibility

Apply configured pre-geometry filtering to matcher output.

Possible evidence may include:

- descriptor relationships
- mutual consistency
- matcher scores
- valid masks

Exact policies belong to benchmark/method configuration.

---

### Filtering Is Not Geometry

Filtering may reduce weak candidate matches.

It does not establish a coherent spatial transformation.

Therefore:

```text
Filtered Match
≠
Verified Inlier
```

---

## 20. Geometric Verification

### Responsibility

Determine whether candidate correspondences are mutually consistent with the configured geometric model.

Conceptually:

```text
Candidate Correspondences
          ↓
Robust Geometric Verification
          ↓
Initial Transform
+
Verified Inlier Mask
+
Verified Inliers
+
Rejected Outliers
```

RANSAC is the typical classical baseline mechanism.

---

### Geometry Should Be Matcher-Agnostic Where Possible

Shared geometry should ideally operate on correspondence coordinates regardless of whether those correspondences originated from:

- SIFT
- LightGlue
- LoFTR
- another compatible method

This makes matcher comparison more controlled.

---

### RANSAC Semantics

RANSAC inliers are:

> **model-consistent candidate correspondences**

They are not automatically:

> **independent physical ground truth**

That distinction must survive all downstream components.

---

## 21. Transform Model Architecture

A transform represents the mapping between source and reference coordinate spaces.

Potential model families include:

- affine
- homography
- later local/piecewise models
- later terrain-aware models

Not every benchmark enables every model.

---

### Transform Semantics

A transform should conceptually preserve enough information to answer:

- What model family is this?
- What is the source coordinate domain?
- What is the destination/reference domain?
- Which direction does it map?
- Is the transform valid?
- Which numerical parameters define it?

Conceptually:

```text
Source Coordinate Domain
          ↓
      Transform
          ↓
Reference Coordinate Domain
```

---

### Bare Matrix Anti-Pattern

Avoid architecture where an anonymous numerical matrix is passed through the system with no indication of:

- model family
- direction
- coordinate convention

A mathematically valid matrix can be scientifically ambiguous without context.

---

## 22. Geometric Quality and Coverage

Geometric quality evaluates whether the verified correspondences provide meaningful support for the estimated transform.

Potential evidence includes:

- verified-inlier count
- inlier ratio
- residual distribution
- spatial coverage
- degeneracy checks

---

### Coverage Ownership

Spatial coverage should have one authoritative scientific definition/implementation for a given metric.

Do not place different incompatible coverage calculations independently inside each matcher.

---

### Geometric Quality vs Independent Accuracy

Geometric quality answers questions such as:

> Does the verified correspondence set support this model coherently?

Independent evaluation answers:

> How accurately does the final transform predict trusted held-out information?

They are related but different.

---

## 23. Optional Tie-Point Refinement

### Responsibility

Optionally improve the positions of already verified correspondences.

Conceptually:

```text
Verified Inliers
      ↓
Optional Refinement
      ↓
Refined Tie Points
```

Refinement should be:

- optional
- explicitly configured
- version/method dependent

---

### Correct Scientific Order

Preferred:

```text
Candidate Correspondences
        ↓
Geometric Verification
        ↓
Verified Inliers
        ↓
Refinement
```

Avoid:

```text
All Raw Candidates
        ↓
Refine Everything
        ↓
Hope Geometry Becomes Correct
```

---

### V1 Boundary

Canonical V1 normally bypasses advanced refinement when excluded by its authoritative scope.

The engine may support a refinement capability without enabling it in V1.

---

## 24. Final Transform Refit

If refinement changes the coordinates of accepted tie points:

> **the final transform must be re-estimated using the final accepted coordinates.**

Conceptually:

```text
Verified Inliers
      ↓
Refined Tie Points
      ↓
FINAL TRANSFORM REFIT
```

Avoid:

```text
Refined Points
+
Old Pre-Refinement Transform
```

being treated as one internally consistent final result.

---

## 25. Registration / Warp

### Responsibility

Apply the accepted final transform to the appropriate source data or coordinates.

Possible outputs may include:

- transformed point coordinates
- registered image
- registered preview
- overlap representation

---

### Registration Depends on Final Geometry

Conceptually:

```text
Final Transform
      ↓
Registration / Warp
```

The registration subsystem should not alter the scientific transform merely to make an overlay appear more attractive.

---

### Warp Is Not Validation

A successfully interpolated raster does not prove that the registration is correct.

```text
Warp Completed
≠
Scientific Registration Accepted
```

Accuracy belongs to evaluation.

---

## 26. Final Evaluation

### Responsibility

Measure the scientific quality of the **final** transform and result.

Potential metric families may include:

- fit residuals
- independent check-point RMSE
- spatial coverage
- ground error where valid
- success/rejection state
- runtime where defined

Exact formulas belong in metric documentation.

---

### Evaluate the Final Transform

If refinement or refitting changes the transform:

```text
Preliminary Transform
→ obsolete for final accuracy
```

Evaluation must use:

```text
Final Transform
```

---

### Fit vs Check Points

The architecture should preserve:

```text
Fit Data
   ↓
Estimate Transform

Independent Check Data
   ↓
Evaluate Final Transform
```

when independent check points exist.

The same data should not silently serve as both fitting input and independent accuracy proof.

---

## 27. Quality / Accept-Reject Policy

### Responsibility

Combine the defined scientific evidence into a final decision:

```text
ACCEPT
```

or:

```text
REJECT
```

according to the active benchmark/method policy.

Potential evidence may include:

- transform validity
- verified support
- spatial coverage
- residual behavior
- independent error

No universal thresholds are defined here.

---

### Decision Policy vs Metrics

Evaluation computes scientific evidence.

Decision policy interprets that evidence.

Keeping the two responsibilities separate permits:

- metric reuse
- controlled benchmark policies
- explicit acceptance criteria

without duplicating metric calculations.

---

### Rejection Is a First-Class Result

A scientifically correct engine must support:

```text
Insufficient Reliable Evidence
          ↓
        REJECT
```

without returning fake success.

---

## 28. Structured Scientific Result

The core engine should produce one authoritative scientific result.

Conceptually, it may contain categories such as:

### Identity and Provenance

Which source/reference observations were processed.

### Status

Accepted, rejected, invalid, unsupported, or error according to actual project vocabulary.

### Correspondence Evidence

Candidate and verified support.

### Geometry

Transform model and coordinate semantics.

### Evaluation

Metrics with meaningful units.

### Failure / Rejection Information

Why processing did not produce an accepted registration.

### Artifact References

Optional references to registered or diagnostic outputs.

No exact schema or class is defined by this document.

---

## 29. One Result, Many Consumers

The engine should provide one authoritative scientific outcome for outer layers.

```text
                   Core Engine
                       ↓
          Authoritative Scientific Result
          ┌────────────┼─────────────┐
          │            │             │
          ▼            ▼             ▼
      Benchmark       CLI          API
          │                          │
          ▼                          ▼
   Aggregation                     Client
                         ┌───────────┴──────────┐
                         ▼                      ▼
                        UI                  Notebook
```

Downstream consumers may:

- display
- serialize
- aggregate
- visualize

the result.

They should not redefine its scientific truth.

---

## 30. Failure Architecture

Failure must be designed explicitly rather than emerging only through exceptions.

### Invalid Input

The requested input cannot be interpreted or processed correctly.

---

### Unsupported Input / Capability

The requested representation, method, or configuration is outside the active supported capability.

---

### Scientific Rejection

The engine executed correctly but could not establish sufficiently reliable registration.

Examples conceptually include:

- insufficient correspondence
- no valid geometry
- weak spatial support
- inadequate final quality

---

### Dependency / Environment Failure

An external scientific/software dependency required by the configured method is unavailable or fails.

---

### Software Error

An unexpected implementation defect or runtime fault occurs.

---

### No Fake Success Fallback

Never design:

```text
Registration Failed
      ↓
Identity Transform
      ↓
ACCEPT
```

unless identity is genuinely supported by scientific geometry.

---

### No Silent Method Fallback

Avoid:

```text
Matcher A Fails
      ↓
Silently Run Matcher B
      ↓
Report "Matcher A"
```

Fallbacks, when scientifically justified, must be explicit in:

- configuration
- provenance
- result interpretation

---

## 31. Diagnostic Artifacts

Diagnostics may visualize core scientific state.

Potential artifacts include:

- keypoint plots
- candidate-match plots
- inlier/outlier plots
- spatial-coverage plots
- registered preview
- overlays
- residual plots

---

### Diagnostics Are Read-Only with Respect to Science

Preferred:

```text
Scientific Result
      ↓
Diagnostic Visualization
```

Avoid:

```text
Visualization
      ↓
Modify Transform
      ↓
Modify Scientific Result
```

Presentation should not redefine scientific computation.

---

## 32. Configuration Architecture

The engine should receive scientific behavior through an explicit configuration mechanism.

Potential methodological areas include:

- representation
- preprocessing
- scale strategy
- reference candidate strategy
- matcher
- candidate filtering
- geometric model
- robust estimation
- refinement
- evaluation
- decision policy

This document does not prescribe a configuration framework.

---

### Configuration Is Part of the Scientific Method

For benchmarked behavior, configuration choices are scientifically meaningful.

Avoid scattering important methodological choices across:

- hidden constants
- pair-specific conditionals
- notebook variables
- developer-local state

---

### Core Components Should Not Need Benchmark Names

Prefer:

```text
Benchmark V1 Configuration
        ↓
selects:
- classical representation
- SIFT baseline
- configured filtering
- RANSAC
- affine/homography
- no retrieval
- no advanced refinement
```

over:

```text
inside low-level matcher:

if benchmark_version == "V1":
    use SIFT
```

Low-level components implement capabilities.

Higher-level composition defines benchmarks.

---

## 33. V1–V4 Capability Composition

Benchmark V1–V4 should be understood as configurations of shared scientific capabilities, not separate core engines.

| Core Capability             | V1            | V2                          | V3                   | V4                          |
| --------------------------- | ------------- | --------------------------- | -------------------- | --------------------------- |
| Input / validation          | Required      | Required                    | Required             | Required                    |
| Scientific context          | Required      | Required                    | Required             | Required                    |
| Basic representation        | Required      | Extended                    | Extended             | Extended                    |
| Sensor-aware representation | No / minimal  | Primary research area       | May be available     | May be available            |
| Advanced scale handling     | Minimal / off | Primary research area       | May be available     | May be available            |
| Classical matching          | Baseline      | Comparison baseline         | Comparison option    | Comparison option           |
| Learned matching            | No            | Not canonical V2 core       | Research option      | Research option             |
| Global retrieval            | No            | Normally outside core focus | Optional research    | Scaled research             |
| Geometric verification      | Required      | Required                    | Required             | Required                    |
| Global affine/homography    | Baseline      | Available                   | Available            | Available where appropriate |
| Advanced refinement         | Normally off  | Scope-dependent             | Possible             | Research focus              |
| Local / DEM-aware geometry  | No            | No                          | Experimental at most | Research direction          |
| Evaluation                  | Required      | Required                    | Required             | Required                    |
| Accept / reject             | Required      | Required                    | Required             | Required                    |

This table is conceptual and must remain subordinate to authoritative benchmark specifications.

It does not claim implementation status.

---

### Benchmark Versions Are Not Engine Versions

Avoid designing:

```text
CoreEngineV1
CoreEngineV2
CoreEngineV3
CoreEngineV4
```

as four independent duplicated systems solely because benchmark versions exist.

Prefer:

```text
Shared Core Engine
        +
Benchmark Configuration / Capability Composition
        ↓
V1 / V2 / V3 / V4
```

Software/package versioning, if present, is a separate concern.

---

## 34. Scientific Dependency Direction

Preferred dependency direction:

```text
Applications / Benchmarks / Research
                  ↓
            Core Orchestrator
                  ↓
   ┌──────────────┼────────────────┐
   ▼              ▼                ▼
Representation  Matching       Candidate Resolution
   │              │                │
   └──────────────┼────────────────┘
                  ▼
               Geometry
                  ▼
          Geometric Quality
                  ▼
             Refinement
             optional
                  ▼
            Final Transform
                  ▼
            Registration
                  ▼
             Evaluation
                  ▼
          Accept / Reject
                  ▼
               Result
```

Cross-cutting:

- scientific context
- configuration
- coordinate semantics
- units
- provenance

---

## 35. Allowed Dependencies

Conceptually appropriate relationships include:

```text
Orchestrator
→ configured scientific capabilities

Representation
→ scientific input/context concepts

Matcher
→ prepared representations + correspondence concepts

Geometry
→ correspondence + transform concepts

Refinement
→ verified tie-point concepts

Registration
→ transform + image/coordinate concepts

Evaluation
→ final transform/result/evaluation truth

Diagnostics
→ structured scientific result
```

Outer applications may depend on the engine.

The engine should not depend back on those applications.

---

## 36. Undesirable Dependencies

Avoid relationships such as:

```text
Matching
→ frontend

Geometry
→ HTTP route

Evaluation
→ benchmark HTML renderer

Core Result
→ UI component

Scientific Core
→ notebook state

SIFT implementation
→ benchmark manifest

RANSAC
→ FAISS

FAISS
→ warp implementation
```

These blur scientific responsibilities and reduce reuse.

---

## 37. Coordinates, Transforms, and Units

Coordinate semantics are architectural concerns, not presentation details.

The engine must distinguish:

- image `(x, y)`
- array `(row, column)`
- source-image coordinates
- reference-image coordinates
- geospatial coordinates

Do not rely on unlabeled numerical arrays to preserve scientific meaning.

---

### Transform Direction

Conceptually:

```text
Source Coordinate Domain
          ↓
    Source→Reference Transform
          ↓
Reference Coordinate Domain
```

The inverse is a different mapping.

---

### Unit Semantics

Metrics should declare meaningful units such as:

- source-image `px`
- reference-image `px`
- metres where scientifically justified
- seconds for runtime

Avoid anonymous scalar metrics whose interpretation depends on hidden context.

---

### Pixel to Ground Conversion

Do not silently convert:

```text
pixels
→ metres
```

without scientifically valid:

- GSD
- coordinate context
- product geometry
- metric definition

---

### Sub-Pixel vs Sub-Metre

`Sub-pixel` describes an image-coordinate magnitude.

It does not automatically mean:

`sub-metre`.

---

### Lunar Geospatial Context

Lunar data must not silently receive Earth defaults such as WGS84/EPSG:4326.

Longitude-convention changes must also remain explicit.

---

## 38. Provenance and Reproducibility

The engine should preserve enough context to answer questions such as:

- Which source observation?
- Which reference observation?
- Which derived representation?
- Which configuration?
- Which correspondence method?
- Which model/checkpoint where applicable?
- Which final transform?
- Which metric definitions?
- Which result?

Conceptually:

```text
Inputs
+
Scientific Context
+
Configuration
+
Code / Model Identity where relevant
        ↓
Core Engine
        ↓
Scientific Result
```

A heavyweight provenance database is not required by this architectural principle.

---

### Determinism

Where relevant:

- control randomness when practical
- record seeds when scientifically useful
- preserve model/checkpoint identity
- preserve methodology/configuration

Do not promise universal bitwise determinism across every environment.

---

## 39. State and Side Effects

Prefer explicit scientific data passing over hidden mutable global state.

Avoid conceptual patterns such as:

```text
global_current_matcher
global_current_transform
global_active_benchmark
global_pair_name
```

when explicit configuration/context can carry that information.

---

### Low-Level Scientific Components

Where practical, scientific functions/components should follow:

```text
Explicit Inputs
      ↓
Scientific Computation
      ↓
Explicit Outputs
```

without hidden dependence on:

- notebook variables
- global mutable state
- frontend state
- current HTTP request

This does not require purely functional programming everywhere.

---

## 40. I/O Boundary

File loading and saving should remain separable from mathematical/scientific computation where reasonable.

Avoid deeply mixing:

- file loading
- descriptor matching
- RANSAC
- RMSE
- image saving

inside one inseparable operation.

Separation improves:

- unit testing
- reuse
- benchmarking
- error interpretation

---

## 41. Artifact and Cache Boundaries

### Artifacts

Artifacts are generated outputs such as:

- registered images
- visualizations
- reports
- descriptor files
- indexes

They should derive from scientific computation.

They should not own scientific methodology.

---

### Caches

Caches may store reusable derived data such as:

- prepared representations
- descriptors
- pyramid levels
- retrieval vectors

where appropriate.

Cache contents should be:

- rebuildable
- traceable
- non-authoritative

The cache must not become the only record of benchmark truth.

---

## 42. Learned Model / Checkpoint Boundary

Advanced methods may depend on learned-model checkpoints.

Where applicable, the architecture should preserve:

- model identity
- checkpoint identity
- configuration
- device/runtime context where scientifically relevant

Model loading should not be confused with scientific result interpretation.

A checkpoint is an external dependency of a method, not a registration result.

---

## 43. External Dependency Boundary

External libraries may provide capabilities such as:

- feature extraction
- numerical linear algebra
- image warping
- geospatial I/O
- vector similarity search
- ML inference

ChandraMap should retain ownership of:

- scientific orchestration
- coordinate meaning
- source/reference semantics
- transform meaning
- evaluation semantics
- result status
- benchmark interpretation

Third-party library-specific objects should not unnecessarily leak through the entire engine if a stable project-owned scientific concept is more appropriate.

---

## 44. Core Orchestrator

The engine may have a high-level orchestration responsibility conceptually responsible for:

1. receiving source/reference/context/configuration
2. validating the request
3. invoking enabled scientific components in order
4. preserving scientific context
5. short-circuiting impossible downstream work
6. collecting authoritative intermediate evidence
7. finalizing one structured scientific result

The orchestrator controls sequence.

It should not contain the complete internal implementation of every algorithm.

---

### Orchestrator vs Component

Example:

```text
Orchestrator
→ calls Geometry

Geometry
→ performs robust geometric estimation
```

The orchestrator should not independently duplicate RANSAC mathematics.

---

### Short-Circuit Failure

Examples:

```text
No usable descriptors
→ do not run descriptor matching
```

```text
Too few candidate correspondences
→ do not run impossible geometry
```

```text
Invalid final transform
→ do not present warp as accepted registration
```

Stopping invalid downstream work makes failure semantics clearer.

---

## 45. Optional Components

The engine should allow optional scientific stages to be bypassed cleanly.

Conceptually:

### V1

```text
Retrieval          OFF
Advanced Scale     OFF / minimal
Learned Matching   OFF
Advanced Refinement OFF
```

### Later Research

A later configuration may enable one or more of those capabilities.

Disabled stages should not require meaningless placeholder execution.

---

## 46. Extension Points

The architecture should permit new research capabilities to enter through well-defined scientific responsibilities.

---

### New Matcher

Preferred conceptual extension:

```text
Prepared Source
+
Prepared Reference
        ↓
New Matcher
        ↓
Candidate Correspondences
        ↓
Existing Geometry
        ↓
Existing Evaluation
```

unless the research method genuinely requires changes to those downstream stages.

---

### New Geometry Model

Conceptually:

```text
Candidate Correspondence Coordinates
        ↓
New Geometry Strategy
        ↓
Explicit Transform
        ↓
Compatible Registration / Evaluation
```

A new geometry method should not require rewriting unrelated matchers.

---

### New Refinement Method

Conceptually:

```text
Verified Inliers
      ↓
New Refinement Method
      ↓
Refined Tie Points
      ↓
Final Transform Refit
```

---

### New Metric

A new metric should have:

- one scientific definition
- defined input population
- explicit units
- documented edge-case behavior
- shared implementation

Avoid benchmark-specific copies of the same metric name.

---

### New Sensor

Adding a sensor should primarily affect responsibilities such as:

- data interpretation
- metadata
- representation
- preprocessing
- scale context

It should not automatically require rewriting:

- RANSAC
- generic transform semantics
- shared metric definitions
- result architecture

unless the new sensor creates a genuine scientific requirement.

---

### New Retrieval Method

A new retrieval method should primarily replace or extend:

> **reference candidate resolution**

without unnecessarily changing:

- local geometry
- registration
- final evaluation

---

## 47. Research Integration

Experimental methods may initially live outside the stable core.

They can depend on stable core capabilities such as:

- evaluation
- geometry
- result semantics

This allows research to remain fast-moving without destabilizing canonical benchmark behavior.

---

### Promotion from Research to Core

Consider promoting an experimental capability when:

- its scientific responsibility is clear
- its interface is understood
- it is reproducible
- tests exist
- benchmark configuration can select it explicitly
- more than one workflow benefits from it
- its scientific value is understood

Do not promote every notebook prototype.

---

## 48. Performance Architecture

Scientific correctness remains the first priority.

Performance improvements may include:

- caching
- batching
- vectorized computation
- GPU inference
- offline descriptor preparation

where measured need exists.

Do not design the classical V1 core around unnecessary high-performance infrastructure.

---

### Offline vs Online Retrieval Work

Where global retrieval is enabled:

**Offline work** may include:

- reference preparation
- tiling
- global-descriptor computation
- index construction

**Online work** may include:

- query representation
- vector search
- candidate resolution
- local registration

This distinction matters for fair runtime reporting.

---

### Concurrency

This architecture does not prescribe:

- threads
- multiprocessing
- GPU batching
- distributed execution

Concurrency strategy should respond to demonstrated implementation/performance requirements.

---

## 49. Security and Trust Boundary

External scientific/software inputs may include:

- raster files
- archives
- metadata
- configurations
- model checkpoints
- external indexes

They cross a software trust boundary.

The engine architecture should support validation before these inputs influence scientific computation.

Detailed security policy belongs in dedicated repository security documentation.

---

## 50. Logging

Logging is useful for observability.

Potential stage context may include:

- pair/run identity
- component
- selected method
- candidate count
- verified-inlier count
- status
- timing

However:

> **Logs are not the authoritative scientific result.**

No logging technology is prescribed by this document.

---

## 51. Testability Boundaries

Each scientific responsibility should be independently testable where practical.

### Representation

Verify controlled representation behavior and preserved semantics.

### Matcher Boundary

Verify correspondence structure and source/reference pairing.

### Geometry

Recover known synthetic transforms under controlled conditions.

### Coverage

Evaluate known spatial point layouts.

### Transform

Verify direction and inversion semantics.

### Registration

Verify known transformations are applied correctly.

### Evaluation

Use controlled numerical examples.

### Failure Model

Verify rejection remains explicit rather than becoming fake success.

---

### Integration Testing

The core should support:

```text
Synthetic / Fixture Pair
        ↓
Core Engine
        ↓
Structured Scientific Result
```

without requiring:

- frontend
- HTTP server
- browser
- cloud deployment

---

### Software Testing vs Benchmarking

Software testing asks:

> Does the implementation behave correctly?

Scientific benchmarking asks:

> How well does the method perform on lunar registration problems?

Both are important.

They provide different evidence.

---

## 52. Architectural Invariants

The following principles should remain true unless the architecture is deliberately revised.

1. Source and reference roles remain explicit.

2. Source and reference coordinate spaces remain distinguishable.

3. `(x, y)` and `(row, column)` conventions are not silently mixed.

4. Transform direction remains explicit.

5. A transform retains meaningful model/domain semantics.

6. Candidate matches are not verified inliers.

7. Verified inliers are not independent ground truth.

8. Matcher confidence is not geometric correctness.

9. Retrieval candidates are not local point correspondences.

10. Global descriptors are not local descriptors.

11. FAISS is not a feature extractor.

12. FAISS is not a local matcher.

13. FAISS is not a registration algorithm.

14. Matching remains separate from geometric verification.

15. Candidate filtering remains separate from robust geometric verification.

16. Geometry remains separable from matcher implementation where scientifically possible.

17. RANSAC inliers remain model-consistent evidence rather than ground truth.

18. Geometry remains separate from final independent evaluation.

19. Spatial coverage has centralized scientific ownership.

20. Refinement, when enabled, operates after geometric verification.

21. If accepted point coordinates change, the final transform is refit.

22. Final evaluation uses the final transform.

23. Registration/warping remains downstream of established geometry.

24. Warp success does not imply registration accuracy.

25. Fit points remain distinguishable from independent check points.

26. Metric values retain explicit coordinate/unit semantics.

27. Pixel and ground error are not silently interchanged.

28. Metre conversion requires valid scientific context.

29. Sub-pixel does not imply sub-metre.

30. Scientific rejection remains a valid result.

31. Invalid input remains distinct from scientific rejection.

32. Unsupported capability remains distinct from scientific rejection.

33. Software error remains distinct from expected scientific failure.

34. Identity fallback cannot convert failed registration into fake success.

35. Silent method fallback is prohibited in canonical benchmark behavior.

36. Original scientific products remain distinguishable from derived representations.

37. Caches and generated artifacts do not become authoritative source truth.

38. Core algorithms do not depend on frontend components.

39. Core algorithms do not depend on API/HTTP transport types.

40. Core algorithms do not depend on benchmark-report rendering.

41. Benchmarks reuse core scientific implementations.

42. Benchmark V1–V4 do not require four duplicated engines.

43. Low-level scientific algorithms should not depend unnecessarily on benchmark version names.

44. Research may extend the core but must not silently redefine canonical behavior.

45. One authoritative scientific result is shared by downstream consumers.

46. Presentation layers do not rewrite inlier masks, transforms, metrics, or final status.

47. Configuration is part of reproducible scientific methodology.

48. Hidden pair-name tuning is prohibited from canonical benchmark behavior.

49. Important scientific state should not depend on hidden notebook variables.

50. Circular dependencies between scientific capabilities should be avoided.

---

## 53. Architectural Anti-Patterns

### Benchmark-Specific Algorithm Forks

Avoid duplicated scientific implementations such as:

```text
v1_matcher
v2_matcher
v3_matcher
v4_matcher
```

when benchmark versions merely configure the same capability differently.

---

### Monolithic Core Function

Avoid one giant operation owning:

```text
Load
→ Preprocess
→ SIFT
→ Match
→ RANSAC
→ Warp
→ RMSE
→ Save Images
→ Generate Report
```

An orchestrator may invoke those responsibilities.

It should not erase their boundaries.

---

### God Object

Avoid one object owning:

- every image
- every matcher
- all configuration
- all transforms
- all metrics
- database state
- frontend state
- benchmark state

without a clear architectural reason.

---

### Matcher Owns Geometry

Avoid matchers directly declaring final geometric truth.

```text
Matcher
→ Candidate Correspondences
```

should normally remain separate from:

```text
Geometry
→ Verified Inliers + Transform
```

---

### Geometry Owns Presentation

RANSAC/geometry code should not own dashboards, HTML rendering, or map widgets.

---

### Duplicate Metrics

Avoid:

```text
Matching RMSE
Benchmark RMSE
API RMSE
Frontend RMSE
```

with different definitions.

Metrics require centralized scientific ownership.

---

### Duplicate Geometry

Avoid copying one RANSAC implementation into multiple benchmark-specific pipelines unless the implementations intentionally represent different methods.

---

### Anonymous Matrix Passing

Avoid transforms that lose:

- model family
- direction
- coordinate semantics

---

### Anonymous Scientific Array Passing

Plain arrays are sometimes necessary internally.

However, architecture should avoid losing all knowledge of:

- source/reference role
- representation provenance
- coordinate meaning
- mask semantics

at important scientific boundaries.

---

### Hidden Pair Tuning

Avoid logic such as:

```text
if current_pair == "hard_case":
    change_thresholds()
```

in canonical benchmark execution.

---

### Silent Fallback

Avoid silently invoking a different method after failure while preserving the original method label.

---

### Identity Success

Avoid returning identity geometry as accepted success after failed estimation.

---

### Frontend Recalculation

Avoid browser/frontend code independently redefining authoritative:

- RMSE
- inlier status
- transformation validity
- acceptance status

---

### Notebook-Only Science

Avoid stable scientific algorithms existing only through interactive notebook cells or hidden state.

---

### Generic Utility Dump

Avoid placing scientifically important behavior such as:

- coordinate transforms
- coverage metrics
- RMSE
- descriptor handling

into a generic utility bucket where ownership and semantics become unclear.

---

### Premature Framework

Do not build a complex plugin, microservice, or distributed framework before real scientific use cases justify it.

A conceptual extension point does not require a heavyweight plugin system.

---

### Version-Forked Engine

Avoid:

```text
V1Engine
V2Engine
V3Engine
V4Engine
```

when composition of shared capabilities is sufficient.

---

### Frontend / API Dependency

Core scientific modules must not require:

- UI types
- HTTP request objects
- response objects
- route handlers

to perform registration.

---

### Database or Cloud Requirement

Do not make a database or cloud service mandatory for core scientific computation unless a verified project requirement genuinely demands it.

---

### Network on Import

Scientific code should not unexpectedly require network activity merely to import or initialize basic core capabilities.

---

## 54. Core Engine Extension Rule

Before adding a new capability, ask:

1. What scientific responsibility does it own?
2. Does an existing component already own that responsibility?
3. Is the capability core, optional, or experimental?
4. Is it useful beyond one experiment?
5. Can it produce output compatible with existing downstream components?
6. Does it preserve coordinate and transform semantics?
7. Is its behavior reproducible?
8. Can it be tested independently?
9. Does it introduce a dependency from the core toward an outer layer?
10. Does it contaminate canonical V1?
11. Does it require a new abstraction, or can an existing responsibility absorb it cleanly?
12. Does the added complexity provide clear scientific or engineering value?

If those questions cannot yet be answered, the capability may belong in research rather than stable core architecture.

---

## 55. Core Engine Definition of Done

At an architectural level, a mature reusable engine should eventually make it possible to:

- process explicit source/reference observations
- preserve scientific identity and provenance
- validate inputs
- prepare scientifically appropriate representations
- select or receive reference candidates
- generate candidate correspondences
- verify correspondence geometry
- represent transforms explicitly
- measure geometric support
- optionally refine verified tie points
- refit geometry when refined coordinates change
- apply final registration
- evaluate the final transform
- reject insufficient evidence
- return one authoritative structured result
- operate without UI/backend dependencies
- serve V1–V4 through capability composition
- be independently testable
- support reproducible scientific runs

This is an architectural target.

It is not a claim that every item is currently implemented.

---

## 56. Relationship to Other Documents

### [`system-overview.md`](./system-overview.md)

Defines the broader ChandraMap system around the scientific engine.

This document focuses only on the internal reusable scientific core.

---

### [`v1-pipeline.md`](./v1-pipeline.md)

Defines the ordered execution of one canonical Benchmark V1 pair through the relevant engine capabilities.

This document defines reusable responsibilities rather than one benchmark's exact sequence.

---

### [`../project/v1-scope.md`](../project/v1-scope.md)

Defines which capabilities belong to canonical V1.

The core engine can contain optional capabilities that V1 deliberately does not enable.

---

### [`../project/terminology.md`](../project/terminology.md)

Defines canonical human-facing terminology for concepts such as:

- candidate match
- verified inlier
- source/reference
- transform
- retrieval
- registration

The engine architecture should preserve those meanings.

---

### [`../project/assumptions.md`](../project/assumptions.md)

Defines scientific and methodological assumptions that engine processing may rely upon.

---

### [`../project/limitations.md`](../project/limitations.md)

Defines scientific and methodological limitations that the engine may expose, measure, reject, or investigate.

---

### [`.ai/architecture/SYSTEM_OVERVIEW.md`](../../.ai/architecture/SYSTEM_OVERVIEW.md)

Provides deeper system-architecture context for maintainers and AI-assisted engineering.

---

### [`.ai/architecture/PIPELINE.md`](../../.ai/architecture/PIPELINE.md)

Defines broader ChandraMap processing order, including later-version branches.

---

### [`.ai/architecture/MODULE_MAP.md`](../../.ai/architecture/MODULE_MAP.md)

Owns repository/module responsibility mapping.

Exact source paths should be documented there rather than invented in this logical architecture.

---

### [`.ai/architecture/DATA_FLOW.md`](../../.ai/architecture/DATA_FLOW.md)

Defines detailed scientific data, metadata, coordinate, transform, provenance, and result movement between responsibilities.

---

### [`.ai/development/BENCHMARK_RULES.md`](../../.ai/development/BENCHMARK_RULES.md)

Defines controlled-comparison and benchmark-governance rules used when composing and evaluating engine capabilities.

---

### [`.ai/development/TESTING_RULES.md`](../../.ai/development/TESTING_RULES.md)

Defines software and scientific testing expectations for engine components and integrated workflows.

---

## 57. Key Architecture Rules

1. ChandraMap has one reusable scientific core conceptually shared across benchmark configurations.

2. Benchmark V1–V4 are capability compositions, not four separate engines.

3. Source and reference identity remain explicit.

4. Scientific context survives important processing boundaries.

5. Original mission data remains distinguishable from derived representations.

6. Representation remains separate from correspondence.

7. IIRS is treated as hyperspectral/imaging-infrared data.

8. Native hyperspectral data is not blindly forced through ordinary grayscale matching.

9. Preprocessing remains configuration-driven and reproducible.

10. Scale handling remains a separate scientific responsibility.

11. Upsampling does not mean physical detail recovery.

12. Reference candidate discovery remains separable from local point correspondence.

13. Known-overlap, metadata-constrained, and global-retrieval paths are different reference-resolution strategies.

14. Global descriptors remain distinct from local descriptors.

15. FAISS is limited conceptually to vector indexing/search.

16. Local matchers produce candidate correspondences.

17. Candidate correspondences are not verified inliers.

18. ALIKED and LightGlue retain separate feature/matching roles.

19. Detector-free methods such as LoFTR are not forced into artificial SIFT-like internals.

20. Candidate filtering remains distinct from geometry.

21. Geometric verification remains matcher-agnostic where scientifically possible.

22. RANSAC inliers are model-consistent, not independent ground truth.

23. Transform model and direction remain explicit.

24. Anonymous transform matrices are discouraged at scientific boundaries.

25. Coverage and geometric-quality calculations have centralized ownership.

26. Refinement remains optional.

27. Refinement follows verification.

28. Final transforms are refit when accepted point coordinates change.

29. Registration/warping remains separate from evaluation.

30. Warp success does not prove registration accuracy.

31. Fit points and independent check points remain distinguishable.

32. Final evaluation uses final geometry.

33. Metrics retain explicit units and coordinate meaning.

34. Ground-distance conversion requires valid scientific context.

35. Sub-pixel does not automatically mean sub-metre.

36. Acceptance/rejection is explicit.

37. Scientific rejection is a valid result.

38. Invalid input, unsupported capability, scientific rejection, dependency failure, and software error remain distinct.

39. Failure must never become fake identity-transform success.

40. Silent method fallback is prohibited in canonical benchmark behavior.

41. One authoritative scientific result is shared by all outer consumers.

42. CLI/API/UI/reporting layers do not rewrite scientific results.

43. Diagnostics consume scientific state rather than define it.

44. Configuration is part of scientific methodology.

45. Low-level components should not unnecessarily know benchmark version names.

46. Optional capabilities must not become hidden V1 dependencies.

47. Research methods should enter through clear extension boundaries.

48. Stable scientific behavior should not depend on hidden notebook state.

49. I/O should remain separable from reusable scientific algorithms where practical.

50. Caches remain rebuildable and non-authoritative.

51. Learned checkpoint identity should be traceable where relevant.

52. The core should remain usable without frontend, HTTP, database, or cloud dependencies.

53. External files/configurations/models cross a trust boundary.

54. Core components should be independently testable.

55. Scientific benchmarking should invoke the same reusable core rather than benchmark-only algorithm copies.

56. Architecture complexity should follow demonstrated scientific or engineering requirements.

<!-- Source specification: :contentReference[oaicite:0]{index=0} -->
