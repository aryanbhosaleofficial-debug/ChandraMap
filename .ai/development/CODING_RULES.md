# ChandraMap Coding Rules

This document defines code-level standards for **ChandraMap**.

Its purpose is to help human contributors and AI coding agents write source code that is:

- correct
- scientifically valid
- explicit
- readable
- testable
- reproducible
- secure
- maintainable
- consistent with the existing repository

The primary coding principle is:

> **Write the simplest code that is correct, explicit, testable, and consistent with ChandraMap's scientific model.**

These rules govern how code should be written. Architectural ownership belongs in `MODULE_MAP.md`; repository-wide change strategy belongs in `ENGINEERING_RULES.md`; detailed testing policy belongs in `TESTING_RULES.md`.

---

## 1. Purpose

`CODING_RULES.md` defines practical conventions for:

- naming
- functions
- classes
- modules
- imports
- typing
- scientific arrays
- coordinates
- units
- numerical code
- image/raster processing
- hyperspectral processing
- correspondence and geometry
- retrieval
- evaluation
- configuration
- failure handling
- logging
- documentation
- file I/O
- security-sensitive code
- learned models
- backend/application code
- frontend code
- research code
- performance
- reproducibility
- public interfaces

This document does not define exact formatter, linter, type-checker, test-runner, package-manager, framework, dependency version, or repository command unless those are established by the repository itself.

---

## 2. Rule Priority

When coding guidance conflicts, use the following priority:

1. **Correctness**
2. **Scientific validity**
3. **Security**
4. **Actual repository tooling and configuration**
5. **Architecture constraints**
6. **Existing local repository conventions**
7. **Clarity**
8. **Reproducibility**
9. **Testability**
10. **Maintainability**
11. **Performance**
12. **Brevity**
13. **Personal style preference**

Do not sacrifice correctness or scientific validity for:

- shorter code
- clever abstractions
- style preferences
- micro-optimizations
- architectural fashion

---

## 3. Repository Tooling Is the Source of Truth

Before applying tool-specific style rules, inspect the repository.

Potential sources include:

- `pyproject.toml`
- `package.json`
- `.editorconfig`
- formatter configuration
- linter configuration
- type-checker configuration
- CI workflows
- nearby source modules
- tests
- contributor documentation

Do not assume the repository uses a particular:

- Python formatter
- Python linter
- Python type checker
- JavaScript/TypeScript formatter
- frontend linter
- test framework
- package manager

unless repository files verify it.

Use:

> the repository-configured formatter, linter, type checker, test runner, and package manager

rather than inventing commands.

---

## 4. Follow Existing Style First

Before changing code style:

1. inspect the target module
2. inspect neighboring modules
3. inspect relevant tests
4. inspect configuration/tooling

Prefer consistency with nearby correct code.

Do not reformat or rewrite unrelated code merely because another style is preferred.

A small scientific bug fix should not become a repository-wide style refactor.

---

## 5. General Code Quality

Code should generally be:

- focused
- explicit
- cohesive
- deterministic where practical
- unsurprising
- testable
- appropriately typed
- appropriately documented
- easy to review

Avoid:

- hidden side effects
- unexplained global state
- ambiguous scientific values
- duplicate scientific formulas
- silent fallbacks
- giant multi-responsibility functions
- unnecessary abstraction
- benchmark-specific hacks in reusable code
- local-machine assumptions

Prefer straightforward code over clever code.

---

# 6. Naming and Terminology

Names should communicate scientific meaning.

Prefer:

```python
source_image
reference_image
candidate_matches
verified_inliers
source_points
reference_points
pixel_error_px
ground_error_m
gsd_m_per_px
```

over vague names such as:

```python
img1
img2
pts
tmp
data2
value
final2
```

when the scientific meaning matters.

Short local loop variables may remain concise where their meaning is obvious.

---

## 6.1 Canonical ChandraMap Terminology

Public and scientific interfaces should follow `.ai/context/TERMINOLOGY.md`.

Important distinctions include:

- source image
- reference image
- candidate match
- verified inlier
- outlier
- tie point
- fit point
- check point
- global descriptor
- local descriptor
- retrieval candidate
- transformation
- registration result
- artifact

Do not create competing terminology without a specific reason.

---

## 6.2 Source and Reference Naming

Prefer:

```python
source_points
reference_points
source_image
reference_image
```

over:

```python
points1
points2
image_a
image_b
```

for important scientific interfaces.

This reduces transformation-direction mistakes.

---

## 6.3 Boolean Naming

Boolean names should read like conditions.

Prefer concepts such as:

```python
is_valid
has_metadata
requires_refinement
supports_projection
```

over:

```python
flag
check
value
```

Follow local repository conventions where they already establish a consistent style.

---

## 6.4 Abbreviations

Use well-established scientific abbreviations when they improve clarity.

Examples include:

- GSD
- RMSE
- CRS
- DEM
- DTM

Avoid obscure local abbreviations that make code harder to review.

---

# 7. Functions

Functions should normally have one coherent responsibility.

A reusable scientific function should not casually combine:

```text
File Loading
+
Preprocessing
+
Matching
+
RANSAC
+
Warping
+
Metric Calculation
+
Artifact Saving
```

unless the function is intentionally an orchestration layer.

---

## 7.1 Prefer Computation Separate from I/O

Where practical:

```text
I/O
↓
Validated Scientific Data
↓
Pure / Mostly Pure Computation
↓
Result
↓
Persistence / Visualization
```

For example, a spatial-coverage calculation should not also:

- open raster files
- save plots
- mutate global configuration

Separating scientific computation from I/O improves:

- testing
- reuse
- debugging
- benchmark comparability

---

## 7.2 Function Arguments

Avoid long lists of unrelated primitive arguments when an existing structured type or configuration object already represents the concept.

Do not create wrapper objects solely to avoid a small number of clear arguments.

Use the simplest interface that remains understandable.

---

## 7.3 Return Values

Return values should have clear semantics.

Avoid ambiguous structures such as:

```python
return x, y, z, status, info, data
```

when callers cannot easily tell what each value represents.

Where the repository already defines structured result types, reuse them.

Do not create competing result containers unnecessarily.

---

## 7.4 Optional Values

Treat:

- unknown
- unavailable
- zero
- empty
- invalid
- not applicable

as different states where the distinction matters.

Do not replace missing scientific metadata with plausible-looking values.

---

## 7.5 Sentinel Values

Avoid using unexplained numeric sentinels such as:

```python
-1
999
0
```

to mean scientific failure when explicit status or optional data would be clearer.

---

# 8. Classes and Structured Data

Use classes when they represent:

- meaningful state
- domain entities
- reusable components
- strategies with shared behavior
- stable structured interfaces

Prefer simple functions when a stateless function is sufficient.

Do not introduce classes merely to wrap one trivial operation.

---

## 8.1 Existing Modeling Style First

If the repository already uses:

- dataclasses
- typed models
- schemas
- another structured-data mechanism

for important domain concepts, use the existing approach.

Do not introduce a second modeling framework without a strong reason.

---

## 8.2 Important Structured Concepts

Structured data may be appropriate for concepts such as:

- product metadata
- correspondence sets
- transformations
- registration results
- benchmark records

Do not infer or invent exact class names from this document.

---

# 9. Module and Import Design

A source module should have one understandable responsibility.

Conceptual examples include:

- preprocessing
- matching
- geometry
- registration
- evaluation
- retrieval

Refer to `../architecture/MODULE_MAP.md` for ownership.

---

## 9.1 Avoid Mixed Responsibilities

Avoid modules that simultaneously own unrelated concerns such as:

```text
Dataset Download
+
Feature Matching
+
API Handling
+
Visualization
+
Benchmark Aggregation
```

---

## 9.2 Explicit Imports

Prefer explicit imports.

Avoid wildcard imports such as:

```python
from package import *
```

because they hide dependencies and ownership.

---

## 9.3 Import-Time Side Effects

Importing a scientific module should generally not:

- download models
- download datasets
- build indexes
- create directories unexpectedly
- run benchmarks
- connect to services
- start workers
- execute expensive inference

Heavy behavior should be explicit.

---

## 9.4 Dependency Direction

Code must respect `MODULE_MAP.md`.

In particular:

```text
apps / services / scripts
        ↓
scientific core
```

is valid.

Avoid:

```text
scientific core
        ↓
frontend
```

or:

```text
scientific core
        ↓
HTTP request objects
```

Tests may import production code.

Production code must not import tests.

---

## 9.5 Circular Imports

Circular imports often reveal unclear responsibility.

Do not repeatedly add local imports solely to hide an architectural cycle without investigating it.

Local imports remain acceptable where they solve a legitimate technical issue.

---

# 10. Type Hints and Structured Interfaces

Use type hints for meaningful boundaries where repository conventions support typing.

They are especially valuable for:

- public functions
- metadata
- configuration
- coordinate collections
- transformations
- scientific results
- contracts

Type annotations must reflect actual behavior.

Do not annotate a return as a non-optional value if the function can actually return no value.

---

## 10.1 Avoid Meaningless `Any`

Use a meaningful type when one already exists.

Do not create excessive type machinery merely to eliminate every possible `Any`.

Clarity is the objective.

---

## 10.2 Array Semantics

A numerical-array type alone often cannot express enough scientific meaning.

At important boundaries, document relevant semantics such as:

- shape
- dimensionality
- channel ordering
- coordinate convention
- dtype
- value range
- units

For example:

```text
2D registration representation with shape (H, W)
```

is more informative than simply:

```text
array
```

---

# 11. Array Shapes

Array-shape assumptions must be explicit when they affect correctness.

Potential shapes include:

```text
(H, W)
(H, W, C)
(N, 2)
```

Hyperspectral products may use different band-axis layouts.

Do not assume:

```text
(H, W, bands)
```

or:

```text
(bands, H, W)
```

without verifying the loader/product convention.

---

## 11.1 Validate Important Shape Invariants

Validate important assumptions before expensive numerical operations.

Examples include:

- point arrays have compatible lengths
- correspondences have expected dimensionality
- descriptors align with keypoints
- masks align with imagery
- transform matrices have the required mathematical form

Do not repeatedly validate trivial local invariants when a trusted upstream boundary already guarantees them.

---

# 12. Numerical and Scientific Code

Scientific code must prioritize numerical correctness over compactness.

Consider:

- floating-point behavior
- finite-value checks
- degenerate geometry
- matrix conditioning
- division by very small values
- coordinate magnitude
- invalid transformations

---

## 12.1 Floating-Point Comparison

Do not rely blindly on exact equality for computed floating-point values.

Use numerically appropriate tolerance-based comparison where the scientific requirement allows it.

Exact tolerances belong to the relevant tests or metric specifications.

---

## 12.2 Finite Values

Handle:

- `NaN`
- positive infinity
- negative infinity

explicitly where they may arise.

Do not allow non-finite values to propagate silently into:

- RANSAC
- transformation estimation
- RMSE
- benchmark aggregation
- serialized results

---

## 12.3 Numerical Stability

Be careful around:

- matrix inversion
- normalization
- extremely small denominators
- singular systems
- badly conditioned geometry
- very large coordinate values

Validate assumptions before numerical operations whose failure could produce plausible but invalid output.

---

## 12.4 Degenerate Geometry

Geometry code should detect invalid or degenerate situations.

Examples may include:

- insufficient points
- unsuitable point distribution
- singular transformation
- non-finite model output

Do not return a plausible-looking matrix simply because a numerical library produced one.

---

# 13. Coordinates and Units

Coordinate handling is one of ChandraMap's highest-risk coding areas.

Coordinate semantics must remain explicit.

---

## 13.1 `(x, y)` vs `(row, column)`

Never assume these are interchangeable.

Common image geometry uses:

```text
x = column
y = row
```

while arrays commonly use:

```text
array[row, column]
```

Verify repository conventions before modifying coordinate logic.

Document coordinate order at important interfaces.

---

## 13.2 Source vs Reference Coordinate Space

Variable names should reveal ownership.

Prefer:

```python
source_points
reference_points
```

instead of:

```python
points1
points2
```

where the distinction is scientifically important.

---

## 13.3 Transform Direction

Transformation direction must remain explicit.

Conceptually distinguish:

```text
source → reference
```

from:

```text
reference → source
```

Avoid public scientific interfaces where the transformation is only called:

```python
H
matrix
transform
```

and its direction cannot be determined from context.

---

## 13.4 Units

Units should be explicit when scientific meaning depends on them.

Conceptual naming examples:

```python
pixel_error_px
ground_error_m
gsd_m_per_px
angle_deg
angle_rad
```

Do not mix:

- pixels
- metres
- degrees
- radians

without explicit conversion.

---

## 13.5 No Unit Magic

Avoid code conceptually equivalent to:

```python
distance = error * scale
```

when `scale` could mean:

- GSD
- pyramid scale
- geometric transform scale
- display scale

Use scientifically precise names.

---

## 13.6 Pixel vs Ground Accuracy

Image-space error and ground-space error are different quantities.

Do not infer:

```text
sub-pixel
```

therefore:

```text
sub-metre
```

Ground-space conversion requires valid spatial context.

---

# 14. Image and Raster Code

Scientific imagery must not be treated like ordinary display images by default.

---

## 14.1 Dtype

Do not assume every image is:

```text
8-bit unsigned integer
```

Scientific imagery may use:

- higher-bit-depth integer types
- floating point
- multi-band arrays

Handle dtype intentionally.

---

## 14.2 Value Ranges

When changing numerical range, distinguish whether the operation is:

- normalization
- clipping
- display conversion
- calibration-related processing
- another scientific transformation

Do not overwrite scientific data merely to satisfy a visualization API.

---

## 14.3 Scientific Data vs Display Data

Keep scientific representations distinct from display-ready representations.

Conceptually:

```text
Scientific Raster
        ↓
Display Conversion
        ↓
Preview
```

The preview must not replace the source scientific array in processing.

---

## 14.4 Masks and No-Data

Respect:

- no-data values
- invalid pixels
- masks
- unavailable regions

Do not treat no-data as valid terrain texture.

When image geometry changes, update associated masks consistently where necessary.

---

## 14.5 Resampling

When resampling:

- preserve original physical-scale meaning
- distinguish representation scale from source sensor GSD
- choose interpolation appropriate to the purpose
- preserve provenance where relevant

Do not describe interpolation as information recovery.

---

# 15. Hyperspectral and IIRS Code

IIRS code must recognize that native data may be multi-band or hyperspectral.

Do not assume:

```python
image.ndim == 2
```

for native IIRS products.

---

## 15.1 Representation Selection

Do not arbitrarily select one spectral band without explicit:

- configuration
- method
- scientific justification
- provenance

Potential derived representations may include:

- selected band
- PCA component
- spectral composite
- gradient representation
- edge representation

No one representation should be hard-coded as universally correct without evidence.

---

## 15.2 IIRS Provenance

Derived IIRS representations should preserve enough context to reconstruct their origin where practical.

Relevant information may include:

- source product/cube
- selected band(s)
- PCA component/configuration
- wavelength region
- normalization
- representation method

Do not label a derived 2D product as though it were the untouched native observation.

---

# 16. Feature Extraction and Matching

Feature extraction, matching, and geometric verification are separate responsibilities.

---

## 16.1 Feature Extraction

Feature extraction code should keep clear relationships among:

- keypoint coordinates
- descriptors
- optional feature scores

Descriptors must remain aligned with their corresponding keypoints.

Do not reorder one without updating the other.

---

## 16.2 Matcher Output

Matcher code should output:

> **candidate correspondences**

before geometric verification.

Do not call raw matcher output:

- verified matches
- trusted correspondences
- ground truth

---

## 16.3 ALIKED and LightGlue

If implemented:

```text
ALIKED
→ sparse feature extraction

LightGlue
→ sparse feature matching
```

Preserve that distinction in code and documentation where practical.

---

## 16.4 LoFTR

LoFTR is detector-free correspondence estimation.

Do not force it into an artificial:

```text
detector → descriptor → matcher
```

architecture if doing so misrepresents its behavior.

A normalized downstream candidate-correspondence interface is acceptable.

---

# 17. RANSAC and Geometry Code

Geometry code should operate on candidate correspondences.

Conceptually, it may produce:

- geometric model
- inlier classification
- verified inlier subset
- status
- residual information where appropriate

Do not mix geometry with:

- visualization
- HTTP transport
- dataset downloading
- benchmark reporting

---

## 17.1 Inlier Mask Alignment

If an inlier mask corresponds to an ordered match array, that relationship must remain correct.

Do not:

1. compute a mask
2. reorder/filter candidate matches
3. continue using the old mask

without updating the mapping.

Misaligned inlier masks can silently corrupt scientific results.

---

## 17.2 RANSAC Inliers Are Not Ground Truth

A RANSAC inlier is model-consistent under the chosen model and threshold.

It is not automatically independently verified physical truth.

Keep that distinction in naming, evaluation, and documentation.

---

# 18. Tie-Point Refinement

Where refinement is enabled, use the pipeline order established by architecture documentation.

Conceptually:

```text
Candidate Matches
      ↓
Geometric Verification
      ↓
Verified Inliers
      ↓
Tie-Point Refinement
      ↓
Refined Coordinates
      ↓
Final Transform Refit
```

---

## 18.1 Preserve Pre/Post Refinement State When Needed

Do not mutate original coordinates silently if scientific analysis requires comparing:

- matcher coordinates
- verified coordinates
- refined coordinates

---

## 18.2 Refit After Refinement

When tie-point coordinates change, refit the transformation.

Do not continue using the stale pre-refinement transform unless the algorithm explicitly requires it.

---

# 19. Registration Code

Registration/warp code should apply an accepted transformation.

It should not independently decide that the transformation is scientifically trustworthy unless that decision belongs to its documented responsibility.

Warping is not validation.

---

## 19.1 Identity Transform

Do not use an identity transformation as a silent fallback for failed registration.

Identity is valid only when scientifically supported.

---

# 20. Retrieval Code

Keep retrieval responsibilities distinct.

Conceptually:

```text
Global Descriptor Extraction
≠
Vector Index Construction
≠
Vector Search
≠
Local Matching
```

---

## 20.1 FAISS

If FAISS is used, treat it as a vector indexing/search component.

FAISS code should deal with concepts such as:

- vectors
- IDs
- distances/similarity
- ranking

It should not be represented as a system that:

- extracts image features
- understands lunar imagery
- verifies geometry
- performs registration

Candidate IDs must map back to reference products or tiles through project metadata.

---

# 21. Metric and Evaluation Code

Authoritative scientific metrics should be:

- centralized
- independently testable
- deterministic where practical
- explicit about units
- explicit about invalid inputs

Do not implement different RMSE formulas independently in:

- notebooks
- API code
- frontend code
- benchmark runners

when one authoritative implementation exists.

---

## 21.1 RMSE

RMSE code must make clear:

- which points are evaluated
- coordinate domain
- units
- invalid-point behavior

Do not label fit-point RMSE as independent check-point RMSE.

---

## 21.2 Fit vs Check Points

Keep:

```text
Fit Points
→ transformation estimation
```

separate from:

```text
Independent Check Points
→ evaluation
```

where independent check points exist.

Do not leak check-point truth into fitting.

---

## 21.3 Spatial Coverage

Coverage implementations should make explicit:

- evaluated spatial domain
- coordinate convention
- grid/geometric method
- boundary handling

Exact formulas belong in metrics documentation.

---

# 22. Configuration

Behavior-changing scientific parameters should use the repository's established configuration system where appropriate.

Potential examples include:

- matcher selection
- preprocessing parameters
- RANSAC configuration
- scale handling
- retrieval settings
- quality thresholds

Do not hide scientifically meaningful parameters as unexplained constants.

---

## 22.1 Configuration Selects; Code Implements

Prefer:

```text
Configuration
→ select SIFT

Core
→ implement/wrap SIFT behavior
```

Do not place substantial executable scientific logic inside configuration files.

---

## 22.2 Configuration Validation

Validate important configuration before scientific execution.

Reject invalid combinations clearly rather than silently coercing them to unrelated defaults.

Examples may include:

- unsupported matcher
- impossible scale
- invalid threshold
- incompatible options

Exact schema behavior depends on the repository.

---

## 22.3 Defaults

Defaults should be:

- documented
- reproducible
- scientifically reasonable
- stable enough for the context in which they are used

Changing a benchmark-affecting default may change methodology.

Treat such changes carefully.

---

# 23. Environment Variables

Use environment variables according to repository conventions.

They are generally more appropriate for:

- deployment/environment concerns
- credentials
- machine-specific settings

than for hiding important scientific experiment parameters that should be captured in explicit benchmark configuration.

---

# 24. Filesystem and Paths

Avoid hard-coded developer-specific paths such as:

```text
/home/user/project-data
C:\Users\name\data
```

in reusable source code.

Use repository-supported configurable path handling.

Where Python style and APIs support it, `pathlib` is often appropriate, but do not rewrite existing path conventions merely for style.

---

## 24.1 File I/O vs Scientific Computation

Prefer separation such as:

```text
Load Product
      ↓
Scientific Processing
      ↓
Return Result
      ↓
Save Output
```

rather than numerical functions opening arbitrary files internally without that being their responsibility.

---

## 24.2 Source Data Immutability

Treat original mission products as immutable where practical.

Do not overwrite original scientific products during:

- preprocessing
- registration
- visualization

Create derived outputs separately.

---

# 25. Error and Failure Handling

Distinguish:

### Expected Scientific Failure

Examples:

- insufficient candidate matches
- insufficient inliers
- no valid transform
- quality rejection
- no overlap

### Unexpected Software / Infrastructure Error

Examples:

- programming error
- malformed internal state
- unexpected dependency failure

These should not automatically use the same handling path.

---

## 25.1 Failure as Data

Expected scientific outcomes may be represented as explicit result/status data rather than exceptions.

For example:

```text
insufficient inliers
→ registration rejection
```

may be a valid result.

---

## 25.2 Exceptions

Reuse existing project exception patterns where present.

Do not create a large custom exception hierarchy without need.

Raise errors at the appropriate abstraction boundary.

---

## 25.3 Broad Exception Handling

Avoid:

```python
except Exception:
    pass
```

and equivalent silent swallowing.

A broad exception may be justified at an outer application boundary, but it should:

- preserve meaningful context
- avoid hiding programmer defects
- translate to a domain/application response deliberately

---

## 25.4 Assertions

Use assertions for developer invariants.

Do not rely on assertions for validation that must remain active for external/untrusted data.

---

# 26. Logging

Use the repository's established logging mechanism.

Avoid arbitrary `print()` calls in reusable scientific code.

Useful logging may include:

- pipeline stage
- product/pair identity
- selected matcher
- candidate count
- inlier count
- failure reason
- runtime

Avoid logging:

- complete image arrays
- descriptor matrices
- secrets
- credentials
- unnecessary private paths

---

## 26.1 Logging Configuration

Individual scientific modules should not independently configure global logging unless repository architecture explicitly assigns that responsibility.

Application/bootstrap code should normally configure handlers and destinations.

---

# 27. Documentation and Comments

Important public or non-obvious scientific interfaces should document, where relevant:

- purpose
- parameters
- return semantics
- units
- coordinate conventions
- array shapes
- failure behavior
- scientific assumptions

Follow the repository's existing docstring style.

Do not impose a documentation format that the repository does not use.

---

## 27.1 Comments Explain Why

Prefer comments explaining:

- scientific assumptions
- numerical edge cases
- coordinate conversions
- compatibility decisions
- performance trade-offs

Avoid comments that merely restate obvious code.

---

## 27.2 TODO and FIXME

TODOs should be actionable and specific.

Prefer:

```text
TODO: evaluate held-out check-point error when validated annotations become available.
```

over:

```text
TODO: fix later
```

Do not fill stable code with speculative TODOs.

---

## 27.3 Dead and Debug Code

Remove before completing a change:

- temporary print statements
- debugging breakpoints
- temporary image dumps
- hard-coded experiment paths
- fake values
- commented-out obsolete implementations

Use version control for history.

---

# 28. Security-Sensitive Code

External files and configuration should be treated as untrusted at trust boundaries.

Potential concerns include:

- path traversal
- malformed rasters
- archive extraction
- resource exhaustion
- unsafe deserialization
- subprocess injection

Detailed security policy belongs in `SECURITY.md` or dedicated security documentation.

---

## 28.1 Deserialization

Be cautious with formats capable of executing or reconstructing arbitrary objects.

Avoid unsafe loading of untrusted:

- pickle-like data
- object-constructing YAML
- serialized model objects

Use safer formats and loading mechanisms where practical.

---

## 28.2 Subprocesses

If external commands are required:

prefer:

- explicit executable
- argument arrays
- validated input

Avoid building shell commands through untrusted string concatenation.

Do not use shell execution without strong justification.

---

## 28.3 Network Operations

Network behavior should be:

- explicit
- failure-aware
- bounded by appropriate timeout behavior
- compatible with reproducibility requirements

Avoid unexpected downloads during:

- module import
- unit tests
- normal startup

---

## 28.4 Secrets

Never hard-code:

- API keys
- access tokens
- passwords
- credentials
- secret-bearing URLs

Use the project's established secret-management mechanism.

---

# 29. AI/ML Model Code

Learned-model wrappers should clearly define:

- input representation
- required preprocessing
- output semantics
- device handling
- model/checkpoint provenance

Avoid model downloads during import.

---

## 29.1 Device Handling

If CPU/GPU execution exists, device selection should be explicit according to repository design.

Avoid scattering hard-coded device calls throughout scientific code.

Do not claim GPU execution is required unless the method genuinely requires it.

---

## 29.2 Model / Checkpoint Provenance

Where benchmark results depend on a learned model, preserve enough information to identify the model configuration.

A result labeled only:

```text
LightGlue
```

may be insufficient if different feature extractors or checkpoints materially change behavior.

---

## 29.3 Verify External APIs

Before using external library APIs, verify the function/class exists in the repository's supported dependency context.

Do not write plausible-looking but nonexistent APIs.

AI-generated code receives the same review standard as human-written code.

---

# 30. Randomness and Determinism

Where randomness affects scientific output:

- use explicit seed handling where appropriate
- allow benchmark runs to record relevant seeds
- avoid unexpected global reseeding in low-level functions

Potential sources include:

- RANSAC
- sampling
- augmentation
- training

Prefer reproducible behavior where practical.

Do not claim bit-for-bit determinism across hardware or library versions unless guaranteed.

---

# 31. Backend and API Code

If backend code exists, route/controller logic should remain thin.

Conceptually:

```text
Request
  ↓
Validate / Translate
  ↓
Application / Scientific Core
  ↓
Structured Result
  ↓
Translate
  ↓
Response
```

API handlers should not directly contain canonical implementations of:

- SIFT
- LightGlue
- LoFTR
- RANSAC
- image warping
- RMSE

---

## 31.1 Contracts

Shared contract changes may affect:

- scientific core
- benchmark tooling
- backend
- frontend
- serialization
- tests

Before changing a shared schema, search producers and consumers.

Do not update only one side of a contract.

---

## 31.2 Error Translation

The scientific layer should define scientific failure meaning.

The API/application layer may translate that result to transport-specific output.

Do not make transport code invent scientific status.

---

# 32. Frontend Code

If frontend code exists, it should consume scientific results rather than recreate them.

Frontend responsibilities may include:

- visualization
- interaction
- formatting values
- displaying status
- plotting correspondences
- showing artifacts

Do not independently compute authoritative:

- RMSE
- geometric inliers
- transform validity
- registration confidence

inside UI components.

---

## 32.1 Preserve Scientific Labels

The UI should distinguish:

- candidate matches
- verified inliers
- rejected outliers
- accepted registration
- rejected registration
- software error
- placeholder/demo data

Placeholder values must be unmistakably labeled.

---

## 32.2 Frontend Tooling

If TypeScript or JavaScript is present, follow the repository's actual:

- compiler configuration
- linting
- formatting
- framework
- component conventions
- state-management patterns

Do not invent a frontend framework from this document.

---

# 33. Research and Experimental Code

Research code may evolve faster than stable core code.

It should still be:

- readable
- reproducible enough to inspect
- explicit about assumptions
- safe with source data
- free of committed secrets
- clear about experimental status

"Research" does not justify incomprehensible code.

---

## 33.1 Promoting Research Code

Before moving experimental code into the stable scientific core:

- clarify the interface
- remove notebook-only dependencies
- remove local hard-coded paths
- move meaningful parameters into configuration
- document assumptions
- add appropriate typing
- add relevant tests
- handle failure cases
- confirm benchmark value
- align with architectural ownership

---

# 34. Notebook Code

Notebooks are appropriate for:

- exploration
- visualization
- data inspection
- experiment analysis

Important reusable functionality should migrate into source modules once stabilized.

Avoid copying the same algorithm into multiple notebooks.

Production code should not normally import notebook-defined functionality.

---

# 35. Scripts and CLI

Scripts and CLI entry points should be thin.

Preferred conceptual flow:

```text
Parse Inputs
      ↓
Validate / Load Configuration
      ↓
Call Reusable Code
      ↓
Present / Save Result
```

Avoid implementing complete scientific pipelines only inside `scripts/`.

---

## 35.1 Package Imports

Stable source code should use the repository's proper package structure.

Avoid manipulating `sys.path` merely to make imports work.

Fix package/import ownership where appropriate.

---

# 36. Performance and Memory

Optimize based on evidence.

Do not replace clear correct code with complicated optimization because it appears faster.

Measure before making performance claims.

---

## 36.1 Numerical Vectorization

Use vectorized numerical operations where they:

- materially improve performance
- remain understandable
- preserve correctness

Avoid unnecessary Python loops over very large arrays when established array operations are appropriate.

Do not create unreadable vectorization for trivial workloads.

---

## 36.2 Large Raster Memory

Lunar imagery can be large.

Avoid unnecessary:

- full-resolution copies
- duplicate arrays
- dtype expansion
- loading of complete reference collections

Where justified, use repository-supported approaches such as:

- tiling
- windows
- chunking
- memory mapping
- caching

Do not build large-data infrastructure prematurely for small workloads.

---

## 36.3 In-Place Mutation

Be cautious with in-place modification of scientific arrays.

Mutation may complicate:

- provenance
- debugging
- comparison

At the same time, blindly copying huge arrays may waste memory.

Make mutation behavior explicit.

---

## 36.4 Concurrency

Add concurrency only when it solves a measured problem.

Consider:

- memory pressure
- ordering
- determinism
- thread/process safety
- model/device sharing
- failure propagation

Do not introduce concurrency for architectural appearance.

---

## 36.5 Async Code

Use asynchronous programming for appropriate I/O-bound application work.

Adding `async` to CPU-bound image registration does not by itself make it non-blocking or faster.

---

# 37. Caching

If expensive derived outputs are cached, cache identity should reflect scientifically relevant inputs.

Potential contributors include:

- source identity
- preprocessing configuration
- model/checkpoint
- pyramid level
- descriptor method

Avoid stale reuse after methodology changes.

Do not invent a cache architecture where none exists.

---

# 38. Reproducibility

Scientific code should allow important behavior to be reconstructed from available context such as:

- input identities
- configuration
- random seed where relevant
- algorithm/method
- model/checkpoint where relevant
- software revision where available

Avoid hidden environmental assumptions.

---

## 38.1 Benchmark Parameters Must Be Explicit

Do not manually alter parameters for individual benchmark pairs without recording that behavior as part of the experiment.

Canonical runs should be reproducible.

---

# 39. Public Interfaces and Compatibility

Public interfaces should be:

- semantically precise
- documented where appropriate
- stable where practical
- free of unnecessary implementation leakage

Do not expose third-party objects directly when doing so creates undesirable coupling.

Do not introduce abstraction simply to avoid every third-party type either.

---

## 39.1 Breaking Changes

Avoid unnecessary breaking changes to:

- APIs
- CLI behavior
- configuration
- result formats
- saved artifacts

When a breaking change is deliberate:

- update consumers
- document the impact
- provide migration guidance where appropriate

Follow actual repository release policy.

---

## 39.2 Deprecation

If an interface is deprecated, make the transition explicit where project conventions support it.

Do not silently change the meaning of an existing field or function.

---

# 40. Scientific Integrity

Scientific code must never fabricate or manipulate results for presentation.

Do not hard-code values such as:

```python
confidence = 0.92
rmse = 0.4
```

unless those values were actually calculated from valid data for that execution.

---

## 40.1 No Benchmark Manipulation

Do not implement:

- hidden pair-specific tuning
- filename-specific special cases
- removal of failed cases solely to improve averages
- post-evaluation outlier deletion solely to improve a score
- fake confidence
- fake accuracy

unless a controlled experiment explicitly defines the behavior and reports it transparently.

---

## 40.2 Failure Visibility

Do not replace failed scientific processing with:

- identity transform
- last successful transform
- fabricated metric
- empty "successful" result

Scientific failure may be the correct result.

---

# 41. Benchmark-Version Coding

Benchmark V1–V4 are configurations/compositions, not reasons to duplicate low-level algorithms.

Avoid scattered logic such as:

```python
if version == "V1":
    ...
elif version == "V2":
    ...
elif version == "V3":
    ...
```

inside unrelated low-level scientific functions.

Prefer:

```text
Benchmark Composition
        ↓
Configuration / Selected Component
        ↓
Reusable Scientific Implementation
```

---

## 41.1 Protect V1

Canonical V1 must remain consistent with `../context/V1_SCOPE.md`.

Do not silently add V2/V3/V4 capabilities such as:

- FAISS retrieval
- LightGlue
- LoFTR
- advanced sensor-aware scale handling
- DEM-aware processing

to the V1 baseline merely to improve results.

---

# 42. Dependencies

Prefer dependencies already used by the project.

Do not add another package simply because it makes one implementation shorter.

New dependencies should be justified through the repository's dependency/engineering policy.

---

## 42.1 Optional Dependencies

If advanced functionality relies on optional dependencies:

- fail clearly when the capability is unavailable
- explain what dependency/capability is missing
- do not silently disable scientific stages

Follow the repository's established optional-dependency mechanism.

---

# 43. Third-Party Code and Citations

Do not copy implementation code from external sources without checking:

- license
- attribution requirements
- compatibility with the repository's license

If an implementation materially follows a published method, preserve an appropriate citation/reference according to project conventions.

Do not invent citations.

---

# 44. Generated Files

Do not manually modify generated code or files when a source generator/template is authoritative.

Verify whether a file is generated before editing it.

Do not assume a generation system exists unless repository evidence confirms one.

---

# 45. Portability

Prefer code that avoids unnecessary:

- OS-specific paths
- shell-specific assumptions
- machine-specific device IDs
- local usernames
- developer-specific directories

Do not claim platform support that has not been verified.

---

# 46. Important Invariants

Important scientific invariants should be explicit in code, documentation, and tests where appropriate.

Examples include:

```text
Point arrays represent source/reference image pixels.

Coordinates use the repository-defined ordering.

Descriptors correspond one-to-one with keypoints.

Candidate matches are not yet verified.

Transform direction is source → reference.

Metric values retain units.

Check points are not used for fitting.
```

Do not leave critical invariants implicit.

---

# 47. Comments on Scientific Approximation

If code makes a scientific approximation, make that assumption discoverable.

Examples include:

- treating a local lunar region as approximately planar
- downsampling a reference to comparable effective GSD
- selecting a particular IIRS representation
- applying a global transform despite terrain relief

Scientific approximations should not be hidden inside seemingly generic utilities.

---

# 48. Refactoring

Refactor when it improves:

- correctness
- scientific clarity
- ownership
- testability
- maintainability

Do not refactor working code solely to impose a preferred pattern.

---

## 48.1 Scientific Refactors

When refactoring numerical/scientific code:

- preserve tests
- preserve benchmark semantics where intended
- avoid mixing large refactors with algorithm changes where possible

Separating structural and methodological changes makes regressions easier to diagnose.

---

# 49. Duplication

Small duplication may sometimes be preferable to premature abstraction.

Repeated authoritative scientific logic generally should not remain duplicated.

Examples that usually deserve one owner include:

- coordinate conversion
- transform application
- RMSE
- metadata normalization
- spatial coverage
- transformation-direction handling

---

# 50. Utility Modules

Avoid turning generic modules such as:

```text
utils
helpers
common
```

into dumping grounds for unrelated behavior.

When a helper grows into meaningful scientific functionality, move it under the responsibility it actually belongs to.

Do not create separate modules for every trivial helper.

---

# 51. Status Values

When scientific status is modeled, preserve meaningful distinctions.

Potential conceptual states include:

- accepted
- rejected
- failed
- unsupported
- refinement required

Do not collapse scientifically different states into one ambiguous Boolean without reason.

Do not invent an enum merely for stylistic preference if the project does not need one.

---

# 52. Serialization

Serialization must preserve scientific meaning.

A transformation may require context beyond its numerical matrix.

A metric may require context beyond its numerical value.

Consider:

- transform direction
- transform type
- coordinate domain
- units
- optional values
- NaN/Inf handling

Do not silently emit non-standard or invalid numerical JSON values.

---

# 53. Anti-Patterns

## 53.1 Ambiguous Scientific Names

Avoid important interfaces using only:

```text
img1
img2
pts
score
```

when source/reference or metric meaning matters.

---

## 53.2 Hidden Units

Avoid numerical values whose physical or image-space units cannot be determined.

---

## 53.3 Hidden Coordinate Convention

Avoid passing point arrays without a known coordinate domain/order.

---

## 53.4 Bare Transform

Avoid passing a transform whose direction and model cannot be determined.

---

## 53.5 Silent Scientific Defaults

Never invent missing:

- GSD
- CRS/projection
- sensor identity
- footprint

to keep a pipeline running.

---

## 53.6 Candidate-as-Verified

Matcher output remains candidate correspondence until geometry verifies it.

---

## 53.7 Stale Transform

Do not refine tie points while continuing to use the old transform silently.

---

## 53.8 Metric Duplication

Avoid multiple competing RMSE or coverage implementations.

---

## 53.9 Fake Confidence

Never hard-code or invent scientific confidence percentages.

---

## 53.10 Identity Fallback

Do not return an identity transform as if registration succeeded when estimation failed.

---

## 53.11 Giant Pipeline Function

Avoid one function containing:

```text
I/O
+
Preprocessing
+
Matching
+
RANSAC
+
Warp
+
Metrics
+
Storage
```

without being an explicit orchestration function.

---

## 53.12 Import-Time Downloads

Do not download models or datasets merely by importing a module.

---

## 53.13 Hard-Coded Local Paths

Do not commit developer-specific paths into reusable source code.

---

## 53.14 Version Checks Everywhere

Avoid scattering V1/V2/V3/V4 conditionals throughout low-level algorithms.

---

## 53.15 Notebook-Only Core

Important reusable algorithms must not exist only inside notebooks.

---

## 53.16 Utility Dumping Ground

Do not use a generic helpers module as the home for unrelated scientific logic.

---

# 54. Code Review Checklist

Before considering a code change ready:

- [ ] The implementation follows existing repository style and configured tooling.
- [ ] Naming uses canonical ChandraMap terminology where appropriate.
- [ ] Source and reference roles are clear.
- [ ] Functions and modules have coherent responsibilities.
- [ ] Public scientific interfaces are typed/documented where appropriate.
- [ ] Array shapes are clear where scientifically important.
- [ ] Coordinate conventions are explicit.
- [ ] Transform direction is explicit.
- [ ] Units are explicit.
- [ ] Missing metadata is not fabricated.
- [ ] Scientific arrays are not assumed to be 8-bit grayscale without evidence.
- [ ] No-data and masks are handled correctly where relevant.
- [ ] Resampling is not treated as physical detail recovery.
- [ ] Native IIRS data is not incorrectly assumed to be ordinary 2D grayscale.
- [ ] IIRS-derived representations preserve appropriate provenance.
- [ ] Descriptors remain aligned with keypoints.
- [ ] Matcher output is not treated as verified prematurely.
- [ ] Inlier masks remain aligned with candidate correspondences.
- [ ] RANSAC inliers are not mislabeled as ground truth.
- [ ] Tie-point refinement is followed by transform refitting where applicable.
- [ ] Fit-point and independent check-point evaluation are not confused.
- [ ] Metric implementations remain centralized.
- [ ] Metric units and evaluation populations are clear.
- [ ] Error and expected scientific failure behavior are explicit.
- [ ] No broad exception silently hides defects.
- [ ] No identity-transform failure fallback was introduced.
- [ ] No secrets were added.
- [ ] No developer-specific hard-coded paths were added.
- [ ] No import-time data/model download was introduced unintentionally.
- [ ] No unsafe deserialization or unsafe shell construction was introduced.
- [ ] No fake benchmark, RMSE, confidence, or accuracy values exist.
- [ ] No undocumented pair-specific benchmark tuning was introduced.
- [ ] No unnecessary dependency was introduced.
- [ ] No unrelated code was reformatted or refactored.
- [ ] Scientific assumptions remain visible.
- [ ] Relevant documentation/comments remain correct.
- [ ] The code remains testable without unnecessary frontend/deployment dependencies.

---

# 55. Related Development and Architecture Documents

`../ENGINEERING_RULES.md`
→ how repository changes should be approached safely

`TESTING_RULES.md`, when present
→ detailed testing requirements

`ERROR_HANDLING.md`, when present
→ detailed error/status conventions

`CONFIGURATION.md`, when present
→ configuration ownership and schema rules

`DEPENDENCIES.md`, when present
→ dependency policy

`../architecture/MODULE_MAP.md`
→ where code belongs

`../architecture/PIPELINE.md`
→ scientific processing order

`../architecture/DATA_FLOW.md`
→ data identity, lineage, coordinates, and result semantics

`../context/TERMINOLOGY.md`
→ canonical project language

`../context/V1_SCOPE.md`
→ canonical V1 benchmark boundary

This document should remain focused on how source code is written.

---

# 56. Key Coding Rules for AI Agents

1. Inspect repository tooling before making style or command assumptions.

2. Follow existing nearby conventions before personal style preferences.

3. Write the simplest code that remains correct and scientifically explicit.

4. Use canonical ChandraMap terminology in scientific interfaces.

5. Prefer explicit `source` and `reference` naming over `image1` and `image2`.

6. Keep coordinate conventions explicit.

7. Distinguish `(x, y)` from `(row, column)`.

8. Keep transformation direction explicit.

9. Keep units explicit.

10. Do not fabricate missing metadata.

11. Do not infer physical resolution from image dimensions.

12. Do not treat interpolation as physical detail recovery.

13. Preserve scientific data separately from display representations.

14. Preserve masks/no-data where scientifically relevant.

15. Do not assume all imagery is 8-bit grayscale.

16. Do not assume native IIRS data is an ordinary 2D image.

17. Keep IIRS representation derivation reproducible.

18. Keep descriptors aligned with their keypoints.

19. Matcher output is candidate correspondence data.

20. Geometric verification determines model-consistent verified inliers.

21. RANSAC inliers are not automatically ground truth.

22. Keep inlier masks aligned with candidate ordering.

23. Sub-pixel refinement should follow the approved verified-inlier flow.

24. Refit the transformation after tie-point coordinates change.

25. Never silently continue using a stale pre-refinement transform.

26. Do not silently return an identity transform after failure.

27. Scientific failure may be a valid result rather than an exception.

28. Centralize authoritative scientific metric implementations.

29. Do not independently calculate authoritative metrics in frontend code.

30. Fit-point error is not independent validation.

31. Do not invent confidence percentages.

32. Avoid unexplained scientific magic constants.

33. Use configuration for meaningful experimental variation.

34. Do not scatter benchmark-version conditionals through low-level algorithms.

35. Keep canonical V1 aligned with `V1_SCOPE.md`.

36. Do not download models or datasets at import time.

37. Do not hard-code developer-local filesystem paths.

38. Avoid unsafe deserialization of untrusted data.

39. Avoid untrusted shell interpolation.

40. Keep API/controller code thin.

41. Keep reusable scientific logic out of CLI-only code and notebooks.

42. Promote research code into the stable core only after its interface, tests, configuration, failure handling, and reproducibility are improved.

43. Optimize only when a relevant bottleneck has evidence.

44. Do not make performance claims without measurement.

45. Do not introduce architectural patterns or frameworks without an actual requirement.

46. Do not duplicate scientific formulas across modules.

47. Do not manipulate benchmark results for presentation.

48. No fake benchmark values, fake accuracy, fake confidence, or hand-edited scientific outputs.

49. Preserve scientific assumptions in code and documentation.

50. Repository tooling and verified implementation always take precedence over unverified generic coding advice.

<!-- Source specification: :contentReference[oaicite:0]{index=0} -->
