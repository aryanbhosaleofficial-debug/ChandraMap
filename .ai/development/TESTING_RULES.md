# ChandraMap Testing Rules

This document defines how **ChandraMap** software behavior and scientific behavior should be tested.

Its purpose is to help human contributors and AI coding agents answer:

> **What must be tested, at what level, and what evidence is required before a change can be considered correct?**

ChandraMap is a scientific image-correspondence and registration project. Testing must therefore validate more than whether code executes without crashing.

A numerically valid result can still be scientifically wrong.

For example, a transformation matrix may have the correct dimensions while mapping:

```text
reference → source
```

when the caller expects:

```text
source → reference
```

Testing must protect both **software correctness** and **scientific correctness**.

---

## 1. Purpose

`TESTING_RULES.md` defines testing expectations for:

- unit tests
- integration tests
- regression tests
- failure-path tests
- end-to-end tests
- scientific validation
- scientific benchmark tests
- numerical code
- coordinate and geometry logic
- image/raster processing
- correspondence and matching
- retrieval
- evaluation metrics
- sensor-specific processing
- AI/ML components
- result contracts
- configuration
- backend/API behavior where applicable
- frontend behavior where applicable
- CLI/script behavior where applicable
- reproducibility
- CPU/GPU execution
- randomness
- large-data workflows
- security-sensitive boundaries
- CI-friendly validation
- test maintenance

This file defines testing principles and expectations.

It does not define:

- exact metric formulas
- benchmark results
- specific CI workflow files
- repository test commands that have not been verified
- exact test-framework syntax
- complete test-case inventories

---

## 2. Testing Principles

ChandraMap testing should follow these principles.

### 2.1 Test Software Correctness

Software tests should verify that implementation behavior matches its contract.

Examples include:

- valid inputs are accepted
- invalid inputs fail correctly
- arrays have expected relationships
- transforms map coordinates correctly
- result status is preserved
- configuration is interpreted correctly

---

### 2.2 Test Scientific Correctness

Scientific tests should verify that implementation preserves intended scientific meaning.

Examples include:

- source/reference direction is correct
- coordinate ordering is correct
- units are preserved
- RANSAC inliers are not treated as ground truth
- fit points are not used as independent check points
- upsampling does not rewrite physical sensor resolution
- pixel-space and ground-space errors are not confused

---

### 2.3 Test Failure as a Valid Outcome

A scientifically correct outcome may be:

> **Reject**

Examples include:

- insufficient matches
- degenerate geometry
- missing required metadata
- no reliable transform
- retrieval failure

Expected scientific rejection is not automatically a software failure.

---

### 2.4 Prefer Controlled Evidence

Tests should use expected results derived from:

- analytically known mathematics
- controlled synthetic geometry
- trusted reference data
- validated annotations
- an independent reference implementation

Do not calculate the expected value using the same implementation being tested.

---

### 2.5 Test the Smallest Relevant Boundary

Prefer:

```text
Unit
  ↓
Integration
  ↓
Broader Relevant Suite
  ↓
Full Suite when justified
```

Do not run the largest possible workflow for every small change.

---

### 2.6 Preserve Scientific Context

Tests involving scientific values should preserve relevant information such as:

- coordinate domain
- source/reference role
- units
- sensor context
- GSD
- projection
- benchmark population

---

## 3. Testing Priorities

When deciding where testing effort matters most, prioritize:

1. scientific correctness
2. core algorithm correctness
3. coordinate and unit correctness
4. failure behavior
5. regression protection
6. reproducibility
7. public/shared contracts
8. integration behavior
9. benchmark integrity
10. performance where justified
11. presentation behavior

Do not prioritize superficial UI coverage while critical geometry remains untested.

---

# 4. Test Levels

ChandraMap should use complementary testing levels.

```text
                    ┌────────────────────────┐
                    │ Scientific Benchmarks  │
                    └────────────▲───────────┘
                                 │
                    ┌────────────┴───────────┐
                    │ End-to-End / System    │
                    └────────────▲───────────┘
                                 │
                    ┌────────────┴───────────┐
                    │ Integration Tests      │
                    └────────────▲───────────┘
                                 │
                    ┌────────────┴───────────┐
                    │ Unit / Invariant Tests │
                    └────────────────────────┘
```

Scientific benchmarks do not replace unit tests.

Unit tests do not replace scientific benchmarks.

---

## 4.1 Unit Tests

Unit tests validate small, isolated behavior.

Potential examples include:

- coordinate conversion
- transform application
- RMSE calculation
- spatial-coverage calculation
- metadata normalization
- input validation
- correspondence filtering
- configuration validation
- mask handling
- geometric utility functions

Unit tests should generally be:

- fast
- deterministic
- isolated
- small
- easy to diagnose

They should normally avoid:

- live network access
- full mission datasets
- model downloads
- frontend stacks
- external services

---

## 4.2 Integration Tests

Integration tests validate boundaries between components.

Examples include:

```text
Image Loader
    +
Preprocessor
```

```text
Matcher
    +
Geometry
```

```text
Geometry
    +
Registration
```

```text
Registration
    +
Evaluation
```

```text
Benchmark Configuration
    +
Pipeline Runner
```

```text
API Adapter
    +
Scientific Core
```

Do not turn every integration test into a full scientific benchmark.

---

## 4.3 Regression Tests

Regression tests protect against previously discovered defects.

When fixing a meaningful bug, add a regression test where practical.

Examples include:

- `(x, y)` / `(row, column)` swaps
- reversed source/reference transforms
- stale inlier masks
- stale pre-refinement transforms
- incorrect RMSE units
- empty-descriptor crashes
- no-data pixels interpreted as terrain
- invalid homography accepted
- failed benchmark cases omitted from aggregation

A comment such as:

> do not break this again

is not a substitute for a regression test.

---

## 4.4 Failure / Negative Tests

Failure-path tests verify correct behavior when the data does not support registration.

Potential cases include:

- blank image
- constant image
- insufficient keypoints
- empty descriptors
- zero candidate matches
- insufficient RANSAC support
- degenerate correspondences
- invalid transformation
- unsupported sensor
- malformed metadata
- corrupt raster
- no overlap
- retrieval miss
- poor spatial distribution
- missing required check-point data

Expected outcome may be:

```text
REJECT
```

rather than an exception.

---

## 4.5 Contract Tests

Where shared result/configuration/API contracts exist, contract tests should verify:

- required semantics
- serialization
- optional values
- status values
- units
- transformation direction
- backward compatibility where explicitly supported

Do not invent contract schemas in tests.

Use repository-defined contracts.

---

## 4.6 End-to-End Tests

A small number of end-to-end tests may validate important complete workflows.

Example:

```text
Known Source / Reference Pair
        ↓
Preprocessing
        ↓
Matching
        ↓
Geometry
        ↓
Registration
        ↓
Evaluation
        ↓
Structured Result
```

Prefer compact, stable fixtures for ordinary automated end-to-end testing.

Large-scale scientific evaluation belongs in benchmark workflows.

---

## 4.7 Scientific Benchmark Tests

Scientific benchmarks answer:

> **How well does this method perform?**

Ordinary tests answer:

> **Is the implementation behaving correctly?**

These are different questions.

Scientific benchmarks may measure:

- registration accuracy
- success/failure rate
- retrieval performance
- robustness across stress categories
- runtime
- comparative method performance

Benchmark performance should not be treated as ordinary CI pass/fail unless explicit regression criteria are defined.

---

## 4.8 Performance Tests

Performance tests measure properties such as:

- runtime
- memory usage
- throughput

They should usually remain separate from ordinary correctness tests when they are:

- slow
- hardware-dependent
- dataset-dependent

Do not invent performance budgets.

---

## 4.9 Security-Relevant Tests

Where appropriate, tests should validate trust boundaries such as:

- malformed file handling
- path traversal prevention
- archive handling
- unsafe serialization boundaries
- upload validation
- subprocess argument handling
- size/resource limits where defined

Detailed security policy belongs in `SECURITY.md`.

---

# 5. Repository Test Tooling

The repository is the source of truth for actual testing tools and commands.

Before documenting or executing repository-specific test commands, inspect applicable files such as:

- `pyproject.toml`
- `package.json`
- Makefile or task-runner files
- test configuration
- `.github/workflows/`
- existing test directories
- contributor documentation

Do not assume use of:

- pytest
- unittest
- tox
- nox
- coverage.py
- Hypothesis
- Jest
- Vitest
- Playwright
- Cypress
- Ruff
- mypy
- npm
- pnpm
- uv
- Poetry

without repository evidence.

If exact commands have not been verified, use:

> **Run the repository-configured test suite.**

Do not invent commands merely because they are common.

---

# 6. Unit Testing Rules

Unit tests should isolate the behavior under test wherever practical.

Prefer controlled inputs.

Examples include:

- small NumPy-like arrays
- synthetic coordinates
- deterministic masks
- minimal metadata objects
- tiny image representations

A unit test should make failure diagnosis straightforward.

Avoid making a unit test depend on:

- network state
- global dataset installation
- GPU availability
- user home directories
- external mission servers
- another test having already executed

---

## 6.1 One Behavior per Test

Prefer one clearly defined behavioral expectation per test where practical.

Avoid:

```text
one test
→ twenty unrelated scientific assertions
```

However, do not create hundreds of tiny tests when one structured or parameterized case is clearer.

---

## 6.2 Arrange / Act / Assert

Tests should have an understandable structure:

```text
Arrange
→ create controlled input

Act
→ execute behavior

Assert
→ verify meaningful outcome
```

Specific comment syntax is not required.

---

# 7. Integration Testing Rules

Integration tests should verify that neighboring responsibilities agree on:

- input/output semantics
- array shapes
- coordinate ordering
- source/reference roles
- units
- statuses
- configuration

High-value integration boundaries include:

| Boundary                  | Important Concern                    |
| ------------------------- | ------------------------------------ |
| Loader → Preprocessor     | data and metadata preserved          |
| Preprocessor → Matcher    | correct representation shape/meaning |
| Matcher → Geometry        | candidate correspondence semantics   |
| Geometry → Registration   | transform direction/model            |
| Geometry → Refinement     | verified-inlier identity             |
| Refinement → Transform    | refined coordinates used             |
| Registration → Evaluation | final transform used                 |
| Pipeline → Result         | scientific meaning preserved         |
| Core → API                | result semantics preserved           |
| Core → UI adapter         | metrics/status not redefined         |

---

# 8. Regression Testing Rules

A regression test should:

1. reproduce the original defect
2. fail before the fix where practical
3. pass after the fix
4. validate scientific behavior rather than implementation trivia

High-risk regression targets include:

- coordinates
- transform direction
- unit conversion
- metric definitions
- match/inlier alignment
- check-point handling
- benchmark aggregation
- configuration defaults
- sensor routing
- serialization

---

# 9. Failure-Path Testing

Failure handling is part of scientific correctness.

The pipeline should be tested for graceful and meaningful rejection when evidence is insufficient.

Potential categories include:

- validation failure
- preprocessing failure
- matching failure
- geometric-verification failure
- transformation failure
- retrieval failure
- quality rejection

Tests should verify that expected scientific failure does not become an unrelated software crash.

---

## 9.1 Expected Failure vs Software Error

Distinguish:

### Expected Scientific Failure

Example:

```text
insufficient verified inliers
```

Expected outcome may be a structured rejection.

### Unexpected Software Error

Example:

```text
index error caused by corrupted array indexing
```

This is an implementation defect.

Tests should not treat these as equivalent.

---

# 10. Scientific and Numerical Testing

Scientific numerical code should be tested beyond simple "returned successfully" assertions.

Relevant concerns include:

- floating-point precision
- NaN
- infinity
- degenerate inputs
- invalid matrix solutions
- singular geometry
- large coordinate values
- normalization edge cases
- zero denominators

---

## 10.1 Floating-Point Assertions

Use justified numerical tolerances.

Do not require exact float equality when numerical methods legitimately produce small differences.

Do not loosen tolerances arbitrarily merely to make tests pass.

Tolerance should reflect:

- quantity
- units
- algorithm
- numerical implementation
- scientific expectations

No universal tolerance is defined here.

---

## 10.2 NaN / Infinity

Where such values may occur, test handling of:

- `NaN`
- positive infinity
- negative infinity

Metric, geometry, and serialization code should not silently emit scientifically meaningless values.

---

## 10.3 Degenerate Cases

Relevant algorithms should be tested with scientifically meaningful degenerate inputs.

Examples include:

- repeated points
- insufficient points
- nearly collinear configurations where relevant
- singular transformations
- empty arrays
- invalid masks

Do not generate extreme edge cases with no connection to actual behavior merely to increase test count.

---

# 11. Coordinate and Geometry Testing

Coordinate handling is a high-risk area and deserves direct tests.

Protect distinctions among:

- `(x, y)`
- `(row, column)`
- source-image pixels
- reference-image pixels
- geospatial coordinates
- transform direction

A visually plausible overlay can still hide coordinate mistakes.

---

## 11.1 `(x, y)` vs `(row, column)`

Tests should verify the project's actual convention where relevant.

Do not assume:

```text
(x, y) == (row, column)
```

Common image geometry often interprets:

```text
x = column
y = row
```

while arrays commonly use:

```text
array[row, column]
```

The actual project convention remains authoritative.

---

## 11.2 Source vs Reference Coordinates

Use controlled tests to ensure the system does not accidentally swap:

```text
source_points
```

and:

```text
reference_points
```

This matters for:

- transformation estimation
- evaluation
- visualization
- serialization

---

## 11.3 Transform Direction

Explicitly test:

```text
source → reference
```

versus:

```text
reference → source
```

Do not test only whether a matrix has the correct shape.

---

## 11.4 Known-Transform Tests

Synthetic geometry provides strong test oracles.

Conceptually:

```text
Known Source Points
      +
Known Transform
      ↓
Expected Reference Points
```

Tests may validate:

- transformation application
- transform recovery
- inverse behavior where supported
- direction

---

## 11.5 Affine Tests

Where affine transforms are supported, relevant tests may cover:

- identity
- translation
- rotation
- scaling
- shear
- noisy correspondences
- outliers
- insufficient points
- degenerate geometry

No universal numerical tolerance is defined here.

---

## 11.6 Homography Tests

Where homographies are supported, test relevant behavior such as:

- known projective mapping
- valid geometric configurations
- outlier handling
- degeneracy
- non-finite output rejection
- source/reference direction

Do not assume a homography is valid merely because an estimation function returned a matrix.

---

# 12. RANSAC and Geometric Verification Testing

RANSAC/geometric-verification tests should include cases such as:

- clean correspondences
- mixed inliers/outliers
- predominantly incorrect candidates
- insufficient candidates
- degenerate geometry
- reproducibility where seed control is supported
- correct inlier-mask alignment

A successful function call is not sufficient evidence.

---

## 12.1 Inlier-Mask Alignment

A critical invariant is:

```text
Candidate Match i
        ↔
Inlier Mask i
```

Tests should protect this relationship after operations such as:

- filtering
- sorting
- slicing
- conversion
- serialization

A stale mask can silently corrupt scientific results.

---

## 12.2 Candidate Match vs Verified Inlier

Where result structures expose both concepts, tests should verify:

```text
candidate matches
        ↓
geometric verification
        ↓
verified inliers
```

Candidate and verified collections must not be populated as though they represent the same processing stage.

---

## 12.3 RANSAC Inliers Are Not Ground Truth

Tests and fixtures must not treat model-consistent inliers as independent truth unless external validation establishes that status.

---

# 13. Matching Tests

Matcher tests should focus on output semantics.

Potential expectations include:

- source coordinates remain aligned with reference coordinates
- no invalid feature indices
- match scores align with correspondence rows
- empty feature sets are handled
- candidate count is consistent
- invalid coordinates are rejected or handled correctly

---

## 13.1 SIFT / RootSIFT

For the classical baseline, test ChandraMap's integration around:

- feature extraction
- descriptor availability
- matching
- empty-feature behavior
- deterministic preprocessing where expected
- candidate-output semantics

Do not test third-party SIFT mathematics unless ChandraMap implements that mathematics itself.

---

## 13.2 ALIKED + LightGlue

If implemented, preserve their different roles.

### ALIKED Tests

May validate:

- feature extraction output
- coordinate semantics
- descriptor/feature alignment
- empty/invalid input handling

### LightGlue Tests

May validate:

- matching output
- candidate-pair semantics
- score alignment
- invalid feature input handling

Their composition belongs in integration testing.

---

## 13.3 LoFTR

LoFTR testing should reflect detector-free correspondence semantics.

Do not require artificial keypoint/descriptor objects merely to force LoFTR into a sparse-feature test pattern.

If ChandraMap normalizes its output to a common candidate-correspondence representation, test that boundary.

---

# 14. Refinement and Transform-Refit Tests

Where sub-pixel or local refinement exists, use controlled shifted patterns where fractional displacement is known.

Conceptual flow:

```text
Verified Inliers
      ↓
Refinement
      ↓
Refined Tie Points
      ↓
Final Transform Refit
```

Tests should not validate refinement in isolation while ignoring whether the final transform is updated correctly.

---

## 14.1 Stale Transform Regression

Protect against this defect:

1. compute initial transform
2. refine tie points
3. accidentally retain original transform

When pipeline specification requires refitting:

> The final transform must be estimated from the final refined coordinates.

---

# 15. Image and Raster Tests

Scientific imagery must not be assumed to behave like ordinary display images.

Relevant testing areas include:

- dtype
- numerical range
- masks
- no-data
- resampling
- raster dimensions
- multi-band layout

---

## 15.1 Dtype and Range

Where relevant, test:

- integer imagery
- floating-point imagery
- higher-bit-depth imagery
- normalized derived representations

Protect against:

- truncation
- overflow
- accidental clipping
- incorrect display conversion leaking into scientific computation

---

## 15.2 Masks and No-Data

Test that:

- invalid pixels remain invalid where required
- no-data is not treated as terrain texture
- masks remain aligned after transformations
- metric calculations handle invalid regions appropriately

Do not invent one universal no-data value.

---

## 15.3 Resampling

Test implementation behavior where resampling matters.

The test must preserve the scientific rule:

> Changing raster size does not recover missing physical sensor detail.

---

## 15.4 Image Warp Tests

Warp tests may verify:

- output geometry
- source/reference direction
- expected landmark movement
- valid-mask behavior
- interpolation configuration where relevant

Successful warp execution is not proof of accurate registration.

---

# 16. Sensor-Specific Testing

Where sensor-specific behavior exists, test each path according to its actual scientific representation.

---

## 16.1 OHRC

Potential test concerns include:

- valid 2D panchromatic handling
- metadata preservation
- high-resolution representation behavior
- sensor-specific preprocessing where implemented

Do not invent OHRC-specific test schemas.

---

## 16.2 TMC-2

Potential concerns include:

- 2D panchromatic handling
- metadata recognition
- scale-context preservation
- sensor-specific preprocessing where implemented

Use the canonical term `TMC-2`.

---

## 16.3 IIRS

IIRS requires dedicated tests because its native data may be hyperspectral.

Potential cases include:

- valid multi-band cube
- invalid band dimension
- missing required spectral metadata
- registration-representation derivation
- deterministic selected-band behavior where configured
- PCA reproducibility where implemented
- derived-representation provenance
- preventing accidental treatment of the entire cube as ordinary grayscale

Do not define one required IIRS representation unless the project actually standardizes one.

---

# 17. Scale and GSD Tests

Scale logic should be tested using controlled metadata.

Potential cases include:

- equal GSD
- known GSD ratio
- missing GSD
- invalid GSD
- selected pyramid level where implemented
- source/reference scale mismatch

---

## 17.1 Resampling Metadata Invariant

If an image is resized, tests should protect against silently rewriting original sensor resolution as if new detail were measured.

Conceptually:

```text
Original Sensor GSD
        ≠
Array Resize Factor
```

---

## 17.2 Physical Scale vs Geometric Scale

Tests should prevent confusion between:

- GSD
- image resampling factor
- geometric transformation scale

These quantities may all involve numerical "scale" but have different scientific meanings.

---

# 18. Geospatial Tests

Where geospatial functionality is implemented, test:

- projection interpretation
- coordinate conversions
- footprints
- overlap
- longitude conventions
- geospatial error conversion
- missing geospatial metadata

Do not assign terrestrial CRS assumptions to lunar data silently.

---

## 18.1 Lunar CRS Safety

Tests should protect against accidental defaults such as:

```text
WGS84
EPSG:4326
```

unless a particular conversion/interface explicitly requires and documents such usage.

---

## 18.2 Longitude Convention

Where multiple supported conventions exist, test actual project-supported conversions.

Do not invent a universal longitude convention.

---

## 18.3 Footprint Tests

Where footprints are used, relevant cases may include:

- valid footprint
- overlapping footprints
- non-overlap
- invalid geometry
- bounding-box approximation

Do not assert that a bounding box is identical to an exact image footprint.

---

# 19. Retrieval Tests

Global retrieval should be tested separately from local registration.

Potential stages include:

```text
Global Descriptor
      ↓
Vector Index
      ↓
Candidate Ranking
      ↓
Metadata Mapping
      ↓
Recall@K
```

Do not treat a successful local registration as proof that retrieval logic itself is correct.

---

## 19.1 Global Descriptor Tests

Where global descriptors exist, test:

- expected output structure
- descriptor dimensional consistency
- source/reference compatibility
- invalid input behavior
- model/checkpoint identity where relevant

---

## 19.2 FAISS / Vector-Index Tests

If FAISS is used, tests should validate behaviors such as:

- vectors enter the index correctly
- vector dimensions are compatible
- returned IDs map to the correct reference records
- index metadata remains aligned
- invalid/empty query behavior
- empty index behavior

FAISS tests should not imply that FAISS performs image feature extraction.

---

## 19.3 Top-K Tests

Where Top-K retrieval exists, relevant cases may include:

- ranking interpretation
- correct ID mapping
- fewer available candidates than requested
- empty index
- duplicate handling where the architecture permits duplicates

Exact `K` values belong in benchmark/configuration definitions.

---

## 19.4 Retrieval Ground Truth

Retrieval evaluation requires a known correct reference region or equivalent benchmark truth.

Do not determine retrieval success solely from a high similarity score.

---

## 19.5 Recall@K vs Registration RMSE

Keep retrieval and registration metrics separate.

```text
Recall@K
→ candidate-region retrieval quality
```

```text
RMSE
→ geometric registration error
```

They measure different tasks.

---

# 20. Metric Tests

Every authoritative scientific metric should have controlled tests.

Potential metrics include:

- candidate match count
- inlier count
- inlier ratio
- residual error
- reprojection error
- RMSE
- spatial coverage
- grid coverage
- convex-hull coverage
- retrieval Recall@K
- success rate
- failure rate
- runtime aggregation

Exact formulas belong in metric documentation.

---

## 20.1 Known-Answer Metric Tests

Prefer small, manually understandable examples whose expected values can be derived independently.

The expected result should not be obtained by invoking the same function under test.

---

## 20.2 RMSE Tests

RMSE tests should specify:

- evaluated points
- coordinate space
- units
- whether the points participated in transformation fitting

Do not assert an unlabeled numerical RMSE.

---

## 20.3 Fit-Point vs Check-Point Tests

Protect this distinction:

```text
Fit Points
    ↓
Estimate Transform
```

```text
Independent Check Points
    ↓
Evaluate Final Transform
```

Where check points are required for independent evaluation, tests should prevent accidental reuse of fit points.

---

## 20.4 No Fit-and-Judge

Tests should protect against:

```text
Fit Transform
     ↓
Measure Same Fit Points
     ↓
Call Result Independent Accuracy
```

Fit residuals may be valid diagnostics.

They are not independent validation.

---

## 20.5 Error Units

Test unit semantics where the result carries units.

Examples include:

- source-image pixels
- reference-image pixels
- metres

A change from:

```text
source-image px
```

to:

```text
reference-image px
```

must not occur silently.

---

## 20.6 Pixel-to-Ground Conversion

Where ground conversion exists, test:

- valid GSD
- invalid GSD
- missing GSD
- correct unit conversion
- applicable coordinate context

Do not produce metres when required physical context is unavailable.

---

## 20.7 Spatial Coverage

Use controlled point layouts.

Useful conceptual cases include:

- empty set
- clustered points
- uniformly distributed points
- edge-concentrated points

Tests should protect against interpreting:

```text
many clustered inliers
```

as automatically meaning:

```text
high spatial coverage
```

---

# 21. Result and Contract Tests

Structured scientific results deserve stronger semantic testing than presentation artifacts.

Where relevant, test preservation of:

- status
- source identity
- reference identity
- transformation
- transformation direction
- metric values
- metric units
- failure reason
- provenance

Use actual repository schemas.

Do not invent field names.

---

## 21.1 Serialization

Where results/configuration are serialized, relevant tests may include:

- round-trip behavior
- missing optional values
- status preservation
- unit preservation
- transform-direction preservation
- NaN/Inf handling
- supported compatibility

Do not assume backward compatibility unless the project defines it.

---

## 21.2 Core-to-API/UI Semantics

Where contracts exist, test that scientific meaning survives transitions such as:

```text
Scientific Result
      ↓
API Serialization
      ↓
Frontend Representation
```

Presentation layers may reformat values.

They must not redefine scientific meaning.

---

# 22. Benchmark Tests

Scientific benchmark execution should use controlled:

```text
Data
+
Configuration
+
Metric Definitions
```

Benchmark runs should preserve:

- pair/query identity
- configuration
- result status
- failures
- metric definitions
- relevant model/checkpoint information
- reproducibility metadata where implemented

---

## 22.1 V1 Testing

V1 is the classical baseline and deserves strong regression protection.

Canonical V1 behavior should remain aligned with:

```text
Known Pair
    ↓
Minimal Preprocessing
    ↓
SIFT / RootSIFT
    ↓
Candidate Matching
    ↓
RANSAC
    ↓
Affine / Homography
    ↓
Registration
    ↓
Evaluation / Failure
```

Tests should protect V1 from accidental contamination by later-version functionality.

---

## 22.2 V1 Scope Regression

Where architecture allows configuration checks, protect V1 from accidental mandatory inclusion of:

- FAISS retrieval
- global learned retrieval
- LightGlue
- LoFTR
- advanced IIRS processing
- DEM-aware geometry
- piecewise warping
- advanced refinement

unless `V1_SCOPE.md` changes deliberately.

---

## 22.3 V2 Testing

V2 tests may focus on implemented functionality such as:

- sensor routing
- sensor-aware preprocessing
- GSD-aware scale handling
- reference pyramids
- IIRS-derived representations
- structural processing

Do not assume every listed concept is already implemented.

---

## 22.4 V3 Testing

Where implemented, V3 testing may include:

- ALIKED outputs
- LightGlue matching
- LoFTR correspondence output
- global descriptors
- reference tiling
- vector indexing
- FAISS mapping
- Top-K retrieval
- candidate-to-local-registration integration

---

## 22.5 V4 Testing

Where implemented, V4 may require testing of:

- advanced tie-point refinement
- local/piecewise geometry
- DEM-aware processing
- uncertainty estimation
- confidence calibration
- matcher-selection logic
- advanced quality gates
- failure classification

Only test capabilities that actually exist.

---

## 22.6 Benchmark Version Isolation

Tests should help preserve boundaries such as:

```text
V1 configuration
→ does not accidentally activate V3 matcher
```

```text
V2 configuration
→ does not unexpectedly require V4 geometry
```

```text
Known-overlap V1
→ does not require global retrieval
```

Exact mechanisms depend on implementation.

---

## 22.7 Benchmark Version Is Not Software Version

Never assume:

```text
Benchmark V1 = software v1.0.0
```

Benchmark configurations and software releases are independent.

---

# 23. Benchmark Integrity

Benchmarking must protect scientific comparability.

When comparing methods, control relevant factors such as:

- benchmark pair/query set
- ground truth
- evaluation population
- metric implementation
- unit convention
- configuration
- preprocessing where appropriate

Do not change several variables silently and attribute the entire difference to one method.

---

## 23.1 Benchmark Manifest Tests

Where benchmark manifests exist, validate applicable properties such as:

- valid source/reference references
- unique pair identifiers where required
- valid referenced records
- valid evaluation references
- allowed stress categories where defined
- absence of prohibited duplicates

Do not invent manifest fields.

---

## 23.2 Benchmark Failures Must Remain Visible

Do not silently remove:

- rejected registrations
- no-match cases
- retrieval failures

from benchmark interpretation.

A benchmark that reports only successful examples can produce misleading conclusions.

---

## 23.3 No Cherry-Picking

Do not select only easy or successful pairs for reported scientific comparison unless the evaluation explicitly defines that subset.

Failure behavior is part of method performance.

---

## 23.4 Benchmark Regression Gates

If performance-regression gates are introduced:

- base them on measured historical evidence
- document tolerances
- account for nondeterminism
- account for hardware/environment differences

Do not invent benchmark thresholds in this file.

---

# 24. Test Data and Fixtures

Normal automated tests should use the smallest fixture that proves the required behavior.

Potential fixture categories include:

- synthetic arrays
- generated image patterns
- controlled coordinate sets
- minimal metadata
- small redistributable lunar crops

Do not require full mission products for simple mathematics.

---

## 24.1 Real vs Synthetic Fixtures

Use synthetic fixtures for:

- geometry
- mathematics
- failure handling
- coordinate conventions
- transform recovery

Use real lunar fixtures where actual sensor/image behavior matters, such as:

- loader integration
- feature behavior
- realistic preprocessing
- sensor-specific integration

Synthetic illumination or augmentation should not be assumed to reproduce all real lunar imaging conditions.

---

## 24.2 Fixture Provenance

Real scientific fixtures should preserve provenance where practical.

Avoid committing unexplained lunar image crops whose original product cannot be identified.

---

## 24.3 Fixture Size

Committed fixtures should remain small where practical.

Large mission datasets should remain external and be referenced through project dataset mechanisms.

---

## 24.4 Fixture Licensing

Before redistributing external scientific products or crops, verify applicable provider terms and attribution requirements.

Do not assume redistribution is automatically permitted.

---

# 25. Temporary Files and Test Isolation

Tests that modify files should use isolated temporary locations where practical.

Do not:

- overwrite source mission products
- modify benchmark truth
- mutate committed fixtures
- depend on developer-specific paths

Tests should clean up temporary artifacts where practical.

---

## 25.1 Filesystem Independence

Avoid test assumptions such as:

```text
/home/user/...
```

or:

```text
C:\Users\...
```

unless a platform-specific test intentionally evaluates such behavior.

---

## 25.2 Test-Order Independence

One test should generally not require another test to execute first.

Avoid hidden dependencies on:

- previous global state
- cached values from another test
- mutable shared configuration
- test execution order

---

# 26. CPU, GPU, and Model Testing

CPU/GPU testing should reflect actual repository support.

Do not assume normal CI has GPU access.

Potential categories may include:

- CPU-required tests
- optional GPU tests
- GPU-specific integration tests
- scientific benchmark runs

Use actual project test grouping if it exists.

Do not invent marker names.

---

## 26.1 Device Consistency

Where both CPU and GPU execution are supported, test semantic consistency within scientifically justified tolerance.

Do not require exact bitwise equality unless the implementation guarantees it.

---

## 26.2 Model Loading

For learned components, test applicable behavior such as:

- successful model loading
- missing checkpoint
- incompatible checkpoint
- expected output structure
- preprocessing compatibility
- device selection

Do not trigger large model downloads during ordinary unit tests.

---

## 26.3 Model Download Boundaries

If learned-model integration requires downloads, prefer explicit integration environments or pre-provisioned caches according to project design.

Normal test discovery/import should not unexpectedly initiate large downloads.

---

# 27. Reproducibility and Randomness

Where randomness affects scientific results:

- control seeds where practical
- preserve seed information where meaningful
- test repeatability expectations
- avoid hidden global reseeding

Potential randomized stages include:

- RANSAC
- sampling
- synthetic augmentation
- ML training

---

## 27.1 Determinism

Do not assert stronger determinism than the underlying implementation supports.

Some:

- GPU operations
- learned models
- parallel numerical kernels

may not be bit-for-bit deterministic.

Tests should reflect scientifically meaningful consistency rather than impossible guarantees.

---

## 27.2 Reproducibility Metadata

Where implemented, tests may protect preservation of:

- input identity
- configuration
- seed
- matcher
- model/checkpoint
- software revision
- metric definition
- result status

---

# 28. Backend and API Tests

If backend services exist, test responsibilities such as:

- request validation
- request-to-domain mapping
- scientific-result serialization
- expected failure mapping
- artifact access
- authorization/security where applicable

Do not duplicate core image-registration algorithms inside API tests.

---

## 28.1 API Failure Mapping

An expected scientific rejection should be represented according to the API design.

It should not automatically become an unhandled server error.

Exact transport semantics belong in API documentation.

---

# 29. Frontend Tests

Where a frontend exists, tests may cover:

- loading state
- successful-result state
- rejection/failure state
- metric rendering
- unit display
- candidate-vs-inlier labels
- missing-data handling
- accessibility where appropriate

Frontend tests should not independently validate authoritative scientific formulas.

---

## 29.1 Test/Demo Values

Fixture values used for UI testing must be clearly test/demo data.

Do not treat a hard-coded value such as:

```text
92% confidence
```

as though it were an actual scientific result.

---

# 30. CLI and Script Tests

Where CLI or operational scripts exist, test behavior such as:

- argument handling
- invalid input
- configuration loading
- status/exit behavior
- correct invocation of reusable core/application code

Do not re-test every scientific algorithm through the CLI.

---

# 31. Configuration Tests

Important configuration behavior should be tested.

Potential cases include:

- valid configuration
- missing required value
- invalid value
- unsupported combination
- default handling
- override behavior

Do not invent configuration schemas.

Use repository-defined configuration.

---

## 31.1 Benchmark-Affecting Defaults

A default change may change benchmark methodology.

If a default materially affects:

- matcher
- preprocessing
- geometry
- scale handling
- evaluation

consider:

- regression testing
- benchmark rerun
- documentation update

Do not treat scientific defaults as harmless cosmetic settings.

---

# 32. Security-Relevant Tests

External scientific files should be treated as potentially untrusted at software boundaries.

Relevant formats may include:

- raster products
- PDS-related products
- hyperspectral containers
- array files
- archives
- model checkpoints
- metadata files

Where risk justifies it, tests should verify safe failure for malformed input.

---

## 32.1 Security + Scientific Validity

A malformed file should:

- fail safely
- avoid uncontrolled crashes where practical
- not be interpreted as valid scientific input
- not produce fabricated scientific results

Security and scientific validation overlap at input boundaries.

---

# 33. Performance and Memory Tests

Performance tests should remain separate from correctness tests when appropriate.

Potential measures include:

- processing time
- peak memory
- throughput
- index build time
- retrieval query time

Do not invent required budgets.

---

## 33.1 Performance Claims

Do not claim:

> V3 is faster than V2

unless measured under documented conditions.

Relevant context may include:

- hardware
- input size
- model
- pipeline scope
- warm/cold cache behavior

---

## 33.2 Large-Image Memory Tests

Where justified, test behaviors such as:

- windowed access
- tiled processing
- repeated execution
- cache reuse
- large-array allocation

Do not introduce expensive stress tests without an actual risk or requirement.

---

# 34. CI Test Strategy

Normal CI should generally prioritize tests that are:

- reliable
- reasonably fast
- deterministic where practical
- dependency-light
- diagnostically useful

Large scientific workloads such as:

- full mission-data benchmarks
- GPU-heavy experiments
- whole-Moon retrieval evaluation

should generally be separated from normal pull-request checks unless repository infrastructure explicitly supports them.

This document does not claim any specific current CI architecture.

---

## 34.1 Coverage

Code coverage can be useful.

However:

> **High line coverage does not guarantee scientific correctness.**

Do not invent a required percentage.

Prioritize meaningful behavioral coverage, especially around:

- transforms
- coordinate conversions
- metrics
- geometry
- failure handling
- configuration
- scientific result contracts
- input validation

---

# 35. Flaky, Skipped, and Expected-Failure Tests

## 35.1 Flaky Tests

Investigate flaky tests.

Potential causes include:

- randomness
- race conditions
- timing
- live network dependencies
- GPU nondeterminism
- unstable algorithms
- stale shared state

Do not solve flakiness by repeatedly rerunning until success.

---

## 35.2 Skipped Tests

Skipping may be valid when:

- optional dependency is unavailable
- required hardware is unavailable
- external integration is intentionally disabled
- platform is unsupported

A skipped test should have a meaningful reason.

Do not skip unexpected failures simply to make CI green.

---

## 35.3 Expected Failures

If the repository's test framework supports expected-failure semantics, use them only for documented known limitations.

Do not convert unexpected regressions into expected failures merely to obtain a passing test run.

---

## 35.4 Disabled Tests

Do not:

- comment out
- delete
- disable

a failing test solely to make the suite pass.

First determine whether:

- the test is incorrect
- the implementation is incorrect
- the specification intentionally changed

Then update deliberately.

---

# 36. Data Leakage and ML Evaluation

For learned models and retrieval research, tests and dataset validation should protect against data leakage.

Potential leakage includes:

- same lunar region in train and test
- overlapping crops across splits
- same observation across evaluation partitions
- augmented copies of test images in training
- tuning thresholds on final test data

Such leakage can invalidate scientific conclusions.

---

## 36.1 Geographic Leakage

Where geographic splits are part of an experiment, validate that the intended geographic separation is actually satisfied.

Do not claim geographic independence without checking it.

---

## 36.2 Train / Validation / Test Discipline

Where ML research uses these partitions:

```text
Training
→ parameter learning

Validation
→ model/configuration selection

Test
→ final held-out evaluation
```

Do not repeatedly tune using the final test set.

---

# 37. Ground-Truth and Annotation Tests

Ground truth may itself contain errors.

Where ground-truth/check-point data exists, validate applicable properties such as:

- referenced image/product identity
- coordinate convention
- bounds
- duplicate points
- units
- fit/check assignment

Do not assume manually annotated data is automatically correct.

---

## 37.1 Manual Annotation Quality Control

Where the project defines manual annotation workflows, possible QC mechanisms may include:

- visual inspection
- bounds checks
- duplicate detection
- independent review

Do not claim such a process exists unless repository documentation establishes it.

---

# 38. Cache and Index Tests

Where caching exists, relevant tests may include:

- cache hit
- cache miss
- invalidation
- changed source data
- changed configuration
- corrupted cache

A stale cache must not silently make scientific results inconsistent.

---

## 38.1 Retrieval Index Synchronization

Where retrieval indexes exist, test that index metadata remains synchronized with:

- descriptor set
- reference tile set
- dataset version/context

Candidate IDs must continue to resolve to the correct scientific reference data.

---

# 39. Artifact Tests

Generated artifacts may be tested for:

- existence
- expected format
- association with the correct run/pair
- basic readability
- provenance/reference linkage

Artifacts such as images and plots should not be the sole source of numerical truth.

---

## 39.1 Golden / Snapshot Tests

Golden files may be useful for:

- structured serialization
- small deterministic reports
- stable contracts

Use image snapshots cautiously because legitimate differences may arise from:

- library versions
- interpolation
- rendering
- hardware

Do not define scientific correctness solely through pixel-perfect screenshot equality.

---

# 40. Mocking Rules

Mocks are useful at external boundaries such as:

- network access
- external storage
- model download
- third-party service

Do not mock the scientific behavior the test is meant to validate.

Example:

```text
Testing RMSE
→ do not mock RMSE
```

```text
Testing API error translation
→ replacing an expensive pipeline boundary may be appropriate
```

---

## 40.1 Avoid Over-Mocking

A test in which every dependency is mocked may prove very little.

Use integration tests to verify real component interaction.

---

# 41. Property and Invariant Testing

Property-based testing may be useful where repository tooling supports it.

Potential mathematical properties include:

- identity transform leaves points unchanged
- transform followed by valid inverse approximately restores coordinates
- inlier count cannot exceed candidate count
- defined coverage remains within its valid range
- rejected status does not masquerade as accepted

Do not add a property-testing dependency solely because this document mentions the approach.

---

## 41.1 Important Invariants

Useful invariants may include:

```text
candidate_count ≥ inlier_count
```

```text
descriptor_count == associated_keypoint_count
```

```text
source_match_count == reference_match_count
```

```text
refined tie points → final transform refit
```

```text
check points ∉ fit set
```

Exact invariant behavior depends on actual project definitions.

---

# 42. Pipeline-Order Tests

Where architecture exposes the ordering, protect critical flow:

```text
Candidate Matches
        ↓
Geometric Verification
        ↓
Verified Inliers
        ↓
Optional Sub-Pixel Refinement
        ↓
Final Transform Refit
        ↓
Registration
        ↓
Independent Evaluation
```

Refactoring must not accidentally reorder scientifically critical stages.

---

## 42.1 Search / Retrieval Ordering

Where relevant, protect:

```text
Reliable Metadata
      ↓
Metadata-Constrained Search
```

and:

```text
Unknown / Insufficient Location
      ↓
Global Retrieval
      ↓
Top-K Candidates
      ↓
Local Matching
```

Do not run whole-Moon retrieval unnecessarily when known overlap is part of the workflow contract.

---

# 43. AI-Agent Validation Workflow

AI agents modifying ChandraMap should follow a narrow-to-broad validation workflow.

1. identify affected behavior
2. inspect nearby production code
3. inspect nearby tests
4. identify scientific invariants
5. add or update the smallest relevant tests
6. run the narrowest useful validation first
7. expand to affected integration tests
8. run broader relevant tests when justified
9. run the full configured suite when change scope warrants it
10. report exactly what was executed

---

## 43.1 Narrow-to-Broad Validation

Preferred flow:

```text
Affected Unit Tests
        ↓
Affected Integration Tests
        ↓
Relevant Subsystem Suite
        ↓
Full Repository Suite when justified
```

This balances efficiency and confidence.

---

## 43.2 When Full-Suite Validation Is More Appropriate

Full validation is more appropriate when changes affect:

- shared scientific interfaces
- coordinate conventions
- geometry
- metrics
- broad configuration
- dependency versions
- package/module structure
- release preparation

Do not assume every documentation typo requires a full scientific benchmark.

---

# 44. Reporting Test Results

Validation reports should distinguish:

- **Passed**
- **Failed**
- **Skipped**
- **Not Run**

Do not collapse:

```text
Not Run
```

into:

```text
Passed
```

---

## 44.1 No Fake Execution Claims

Never claim:

> all tests passed

unless the tests were actually executed.

Never claim:

> benchmark validated

unless the benchmark actually ran.

Never claim:

> GPU tests passed

without actual GPU test execution.

Code inspection alone is not evidence that tests passed.

---

# 45. Testing Architecture Changes

Architecture refactors should demonstrate that intended behavior remains preserved.

Relevant validation may include:

- imports
- interfaces
- result contracts
- scientific invariants
- integration paths
- serialization

Where possible, avoid combining:

```text
large architecture refactor
+
scientific methodology change
```

in one change.

Separating them makes regressions easier to identify.

---

# 46. Dependency Change Testing

When changing important dependencies, test affected behaviors such as:

- image decoding
- geospatial operations
- numerical routines
- OpenCV-related behavior
- model loading
- serialization

Do not upgrade a dependency merely to make an unrelated test pass without understanding the effect.

---

## 46.1 Optional Dependencies

Where optional features depend on optional packages, test:

- behavior when dependency is available
- clear behavior when dependency is unavailable

Do not silently substitute a different scientific method unless architecture explicitly defines that fallback.

---

# 47. Test Naming and Assertions

Test names should describe expected behavior.

Prefer conceptually:

```text
test_rejects_empty_candidate_set
```

over:

```text
test_case_7
```

Follow actual repository/framework naming conventions.

---

## 47.1 Assert Scientific Meaning

Prefer assertions such as:

> final transform maps the known source points to the expected reference points

over:

> helper function was called exactly three times

unless call count is itself part of required behavior.

---

## 47.2 White-Box vs Black-Box

Prefer public/module-level behavior where practical.

White-box tests are appropriate for important internal numerical logic or invariants that need direct validation.

Avoid coupling every test to private implementation details.

---

# 48. Test Maintenance

Tests are part of the specification.

When intended production behavior changes:

- determine whether the specification changed
- update tests deliberately
- update related documentation where necessary
- consider benchmark comparability

Do not change tests merely because they fail after an unintended regression.

---

## 48.1 Metric Definition Changes

Changing an authoritative metric requires consideration of:

- metric unit tests
- regression tests
- benchmark comparability
- historical result interpretation
- metric documentation

Do not silently change RMSE, coverage, or other scientific metric semantics.

---

## 48.2 Breaking Scientific Changes

Changes involving:

- coordinate conventions
- transform direction
- units
- result schemas
- benchmark manifests
- metric definitions

have potentially large scientific impact.

They may require:

- migration
- updated tests
- benchmark reruns
- documentation changes

---

# 49. Testing Anti-Patterns

## 49.1 Crash-Only Testing

Bad:

> The function returned without throwing, therefore it is correct.

Scientific outputs require semantic assertions.

---

## 49.2 Visual-Only Validation

Bad:

> The overlay looks aligned, therefore accuracy is proven.

Visual inspection is diagnostic, not independent measurement.

---

## 49.3 Fit-and-Judge

Bad:

```text
Fit Transform
      ↓
Evaluate Same Fit Points
      ↓
Claim Independent Accuracy
```

---

## 49.4 Candidate-as-Ground-Truth

Raw matcher output is not ground truth.

---

## 49.5 RANSAC-Inlier-as-Ground-Truth

Model-consistent inliers are not automatically independent truth.

---

## 49.6 Unitless Assertions

Avoid assertions such as:

```text
rmse < threshold
```

without knowing:

- metric definition
- unit
- evaluation population

---

## 49.7 Coordinate Ambiguity

Do not write geometry tests where the coordinate convention or source/reference direction is unclear.

---

## 49.8 Giant-Dataset Unit Tests

Do not load full mission products to test small mathematical functions.

---

## 49.9 Live-Network Unit Tests

Do not make normal unit tests depend on external NASA/ISRO services.

---

## 49.10 Import-Time Model Downloads

Test discovery/import should not unexpectedly download model checkpoints.

---

## 49.11 Test-Order Dependency

Tests should not require another test to run first.

---

## 49.12 Silent Fixture Mutation

Do not modify shared source or benchmark fixtures in place.

---

## 49.13 Fake Success

Do not present:

- skipped
- disabled
- unexecuted

tests as passed.

---

## 49.14 Test Gaming

Never add production branches such as:

```text
if current_file_is_known_test_fixture:
    return expected_result
```

to make tests pass.

---

## 49.15 Excessive Mocking

Do not mock away the actual scientific behavior being tested.

---

## 49.16 Assertion Weakening

Do not immediately:

- remove an assertion
- dramatically loosen tolerance
- replace a semantic check with `is not None`

simply because a test fails.

Understand the cause first.

---

## 49.17 Successful-Case Cherry-Picking

Do not omit failed benchmark cases merely to improve reported results.

---

## 49.18 Uncontrolled Benchmark Changes

Do not compare methods while silently changing:

- pair set
- metric
- preprocessing
- threshold
- ground truth
- evaluation population

---

## 49.19 Self-Copy Expected Values

Do not calculate the expected result using the same algorithm/code path that is being tested.

---

# 50. Test Review Checklist

Before accepting testing work, verify:

- [ ] The test level matches the behavior being validated.
- [ ] Expected output comes from a controlled or independent oracle.
- [ ] Source and reference roles are explicit.
- [ ] Coordinate convention is explicit where relevant.
- [ ] Transform direction is explicit.
- [ ] Units are explicit.
- [ ] Array-shape assumptions are explicit where needed.
- [ ] Empty and degenerate inputs are covered where relevant.
- [ ] Expected scientific failure paths are tested.
- [ ] Candidate matches are not treated as verified automatically.
- [ ] RANSAC inliers are not mislabeled as ground truth.
- [ ] Inlier-mask alignment is preserved.
- [ ] Spatial distribution is tested where relevant.
- [ ] Refined tie points trigger final transform refitting where required.
- [ ] Final evaluation uses the final transform.
- [ ] Fit points and independent check points remain separate.
- [ ] Ground conversion is tested only with valid spatial context.
- [ ] Metric units and evaluation populations are clear.
- [ ] Masks/no-data are tested where relevant.
- [ ] Scientific dtype/range behavior is tested where relevant.
- [ ] NaN/Inf behavior is tested where relevant.
- [ ] IIRS is not treated as ordinary grayscale data.
- [ ] Scale metadata is not falsely improved by resampling.
- [ ] Retrieval tests are separated from local-registration tests.
- [ ] Vector-index IDs map correctly to scientific reference records where retrieval exists.
- [ ] Retrieval Recall@K is not confused with registration RMSE.
- [ ] Failed/rejected benchmark cases remain visible.
- [ ] No unnecessary live external dependency is required.
- [ ] Fixtures are small and reproducible where practical.
- [ ] Real scientific fixtures have provenance where practical.
- [ ] Tests do not overwrite source scientific data.
- [ ] Randomness is controlled where practical.
- [ ] GPU-specific testing is isolated appropriately.
- [ ] No fake benchmark thresholds were introduced.
- [ ] No arbitrary tolerance was loosened without justification.
- [ ] No failing test was disabled merely to make CI pass.
- [ ] Relevant regression tests were added for meaningful bug fixes.
- [ ] Actual validation performed is reported accurately.

---

# 51. Related Documents

The following ChandraMap documentation should be consulted where relevant:

[`../ENGINEERING_RULES.md`](../ENGINEERING_RULES.md)
→ repository-wide change and validation principles

[`CODING_RULES.md`](CODING_RULES.md)
→ source-code implementation conventions

[`../context/DOMAIN_CONTEXT.md`](../context/DOMAIN_CONTEXT.md)
→ lunar imaging and scientific constraints

[`../context/TERMINOLOGY.md`](../context/TERMINOLOGY.md)
→ canonical scientific and project terminology

[`../context/DATASETS.md`](../context/DATASETS.md)
→ scientific products, metadata, and provenance

[`../context/V1_SCOPE.md`](../context/V1_SCOPE.md)
→ canonical Benchmark V1 boundary

[`../architecture/PIPELINE.md`](../architecture/PIPELINE.md)
→ scientific processing order

[`../architecture/MODULE_MAP.md`](../architecture/MODULE_MAP.md)
→ implementation ownership

[`../architecture/DATA_FLOW.md`](../architecture/DATA_FLOW.md)
→ scientific data semantics and movement

Metrics documentation, when present, should define exact metric mathematics and edge-case behavior.

Do not invent links to files that do not exist.

---

# 52. Key Testing Rules for AI Agents

1. Inspect the repository's real testing tools before writing or running commands.

2. Do not invent a test framework, marker, package manager, or CI command.

3. Test software correctness and scientific correctness separately.

4. Use small deterministic unit tests for mathematical behavior.

5. Use integration tests for component boundaries.

6. Use controlled lunar benchmarks for scientific method comparison.

7. Do not use full mission datasets for ordinary unit tests.

8. Preserve provenance for real scientific fixtures where practical.

9. Never overwrite source mission data during tests.

10. Test `(x, y)` vs `(row, column)` explicitly where relevant.

11. Test source/reference coordinate roles.

12. Test `source → reference` vs `reference → source` transform direction.

13. Test coordinate-domain boundaries.

14. Test units.

15. Test NaN/Inf handling where relevant.

16. Test degenerate geometry.

17. Test empty keypoints and descriptors.

18. Test insufficient candidate matches.

19. Test insufficient geometric support.

20. Test invalid transformations.

21. Test inlier-mask alignment.

22. Matcher output is candidate data, not ground truth.

23. RANSAC inliers are model-consistent, not independent truth.

24. Test spatial distribution, not only correspondence count.

25. Protect the verified-inlier → refinement → transform-refit order.

26. Test the final transform after refined coordinates are produced.

27. A successful warp does not prove registration accuracy.

28. Keep fit points and independent check points separate.

29. Do not report fit-point RMSE as independent accuracy.

30. Test metric units and evaluated populations.

31. Do not convert pixel error to metres without valid GSD and coordinate context.

32. Keep retrieval tests separate from registration tests.

33. FAISS/vector-index tests validate vectors, IDs, ranking, and metadata mapping—not local image correspondence.

34. Retrieval Recall@K and registration RMSE test different behaviors.

35. Do not require network access in ordinary unit tests.

36. Do not unexpectedly download model checkpoints during test import or normal unit testing.

37. GPU tests should remain optional/isolated unless GPU support is an explicit required project dependency.

38. Preserve random seeds where meaningful.

39. Do not claim deterministic behavior the underlying implementation cannot guarantee.

40. Protect V1 as the classical baseline.

41. Do not silently add V3/V4 behavior to V1 tests or configuration.

42. Benchmark V1–V4 are not software release versions.

43. Failed and rejected benchmark cases must remain visible.

44. Do not cherry-pick only successful pairs.

45. Do not special-case benchmark fixture names inside production algorithms.

46. Do not weaken assertions merely because a test fails.

47. Do not disable tests merely to make CI green.

48. Add regression tests for meaningful bug fixes where practical.

49. Metric-definition changes require metric-test and benchmark-comparability review.

50. Run narrow relevant tests first and broaden validation as needed.

51. Report exactly what was run, passed, failed, skipped, or not run.

52. Never state that all tests passed unless the relevant tests were actually executed.

<!-- Source specification: :contentReference[oaicite:0]{index=0} -->
