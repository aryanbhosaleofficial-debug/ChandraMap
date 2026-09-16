# AGENTS.md

This file is the repository-level operating guide for AI coding agents and automated software-engineering assistants working on **ChandraMap**.

Its purpose is to help agents understand the project before making changes, preserve scientific and engineering integrity, minimize unnecessary modifications, validate work appropriately, and avoid inventing repository facts.

> **Primary rule: UNDERSTAND BEFORE MODIFYING.**

Correctness takes priority over speed, convenience, token reduction, or architectural preference.

---

## 1. Mission

ChandraMap is an open-source research and engineering project for:

- lunar image correspondence
- lunar image registration
- multi-sensor lunar image matching
- geospatial localization
- computer vision
- remote sensing
- scientific computing
- benchmarking
- AI/ML experimentation
- backend and MLOps infrastructure
- lunar mapping applications

The original problem context is **SIH 26166 — multi-modal, Sun-angle and scale invariant image correspondence using Chandrayaan-2 imagery**.

The project is being developed beyond the original hackathon context as a reusable research and engineering system.

The fundamental problem is:

> Find reliable correspondences between observations of the same lunar terrain despite differences in sensor, resolution, scale, illumination, Sun angle, viewing geometry, modality, and terrain relief.

Typical source instruments may include:

- Chandrayaan-2 OHRC
- Chandrayaan-2 TMC-2
- Chandrayaan-2 IIRS

Typical reference imagery may include:

- LRO NAC
- LRO WAC

Potential research data may additionally include scientifically justified products such as:

- Kaguya / SELENE
- DEM / DTM products
- LOLA-derived data
- synthetic lunar augmentation datasets
- other planetary datasets used for controlled research

The primary scientific outputs are correspondence and registration results, not merely visualization.

Expected outputs may include:

- candidate correspondences
- geometrically verified inliers
- rejected matches
- tie points
- transformation models
- registered imagery
- registered previews
- geospatial localization where supported
- quality metrics
- confidence information
- explicit failure information

A lunar mosaic, globe, visualization, or map UI is a **downstream application**.

A visually convincing overlay is not evidence that registration is scientifically correct.

---

## 2. Instruction Scope

This root `AGENTS.md` defines repository-wide default behavior for coding and engineering agents.

It is not a replacement for:

- `README.md`
- `CONTRIBUTING.md`
- `ROADMAP.md`
- `CHANGELOG.md`
- `SECURITY.md`
- `CODE_OF_CONDUCT.md`
- architecture documentation
- benchmark specifications
- detailed domain documentation
- language-specific style configuration

Agents should use those sources when relevant rather than duplicating their contents here.

If nested `AGENTS.md` files exist, more specific instructions for the affected subtree should be considered alongside these repository-wide rules.

Do not assume nested agent files exist.

If tool-specific instruction files such as `CLAUDE.md` exist, read them only when applicable. Shared architectural and scientific truth should remain consistent across tools.

### Instruction priority

Use the most authoritative applicable source available:

1. explicit requirements from the current human task
2. applicable repository policies and project documentation
3. applicable local or subdirectory agent instructions
4. established repository architecture and conventions
5. general software-engineering best practices

A task request must not be used to justify:

- exposing secrets
- violating licenses
- falsifying scientific results
- fabricating tests or benchmarks
- corrupting project data
- unnecessarily destroying user work

When instructions conflict materially, identify the conflict rather than silently choosing whichever interpretation is easiest.

---

## 3. Understand Before Modifying

Before changing anything, inspect the **smallest relevant set of repository context** necessary to understand the task.

Depending on the change, that may include:

- the affected file
- its containing module
- nearby implementations
- directly related tests
- relevant configuration
- public interfaces
- schemas or contracts
- benchmark definitions
- applicable documentation

Do not modify code merely from assumptions about how the project probably works.

### Never assume without repository evidence

Do not assume:

- framework
- architecture
- package manager
- dependency manager
- database
- storage system
- API structure
- service boundaries
- model architecture
- model availability
- command-line interface
- environment variable names
- configuration keys
- file paths
- benchmark results
- dataset contents
- installed tools
- deployment platform
- CI architecture

Search the repository before introducing a new:

- utility
- helper
- abstraction
- configuration field
- schema
- model wrapper
- endpoint
- service
- dependency
- command
- data structure

Prefer an existing implementation or established pattern when one already exists.

### Do not fabricate repository facts

Never invent:

- files
- directories
- classes
- functions
- APIs
- routes
- configuration options
- environment variables
- database tables
- dependency versions
- release versions
- CLI commands
- benchmark numbers
- test counts
- CI jobs
- dataset licenses
- model checkpoints
- experiment results

If a fact cannot be verified, state that it is unknown.

---

## 4. Progressive Context Loading

Use progressive context loading.

A typical investigation order is:

```text
Task
  ↓
Affected file
  ↓
Nearby module
  ↓
Relevant tests
  ↓
Relevant configuration
  ↓
Relevant documentation
  ↓
Broader repository context only if required
```

For a narrowly scoped change, do not automatically read:

- every source file
- all documentation
- all experiments
- all notebooks
- all benchmark versions
- all historical results

Token efficiency matters, but never at the cost of correctness.

Do not refuse to inspect necessary context merely to minimize context usage.

---

## 5. Project Scientific Principles

Scientific validity and reproducibility are project requirements, not optional documentation concerns.

### 5.1 Sensor handling

Do not treat OHRC, TMC-2, and IIRS as equivalent image sources.

#### OHRC

OHRC is a high-resolution panchromatic lunar imaging instrument.

Official documentation may report approximately **0.25–0.32 m/pixel**, depending on product and source documentation.

For implementation:

- use actual product metadata where available
- preserve sensor and product provenance
- support fine terrain correspondence when information content permits
- do not hard-code one resolution globally without evidence

#### TMC-2

Use the name:

> **TMC-2**

when referring to the Chandrayaan-2 instrument.

TMC-2 is approximately **5 m/pixel** panchromatic terrain imagery and can provide useful terrain-scale structural correspondence.

Do not casually rename it to generic `TMC` in project-facing scientific documentation unless external source terminology requires it.

#### IIRS

IIRS is an **imaging infrared spectrometer / hyperspectral instrument**, not simply a low-resolution grayscale camera.

Its spatial scale is approximately **80 m/pixel**, while spectral information spans many bands.

Do not automatically feed a full hyperspectral cube into a conventional 2D grayscale feature matcher.

Registration experiments may derive a 2D representation using approaches such as:

- selected bands
- PCA
- composites
- gradients
- edges
- structural representations

Use only approaches supported by repository implementation or an explicitly defined experiment.

Do not claim that an IIRS representation preserves information that the source data does not physically contain.

#### LRO NAC

LRO NAC may serve as a high-resolution lunar reference.

Resolution varies by observation geometry and product.

Do not assume one fixed NAC resolution for all data.

#### LRO WAC

LRO WAC provides wider-area lunar context and may be useful for:

- coarse reference search
- global-scale imagery
- broad illumination context
- retrieval experiments

Treat NAC and WAC as different products with different roles.

---

### 5.2 Physical scale handling

Image resizing does not create spatial information.

Upsampling an approximately `80 m/pixel` IIRS-derived image to a much larger pixel grid does **not** make it equivalent to:

- TMC-2 at approximately `5 m/pixel`
- NAC at sub-to-few-metre scale
- OHRC at sub-metre scale

Prefer physically meaningful strategies such as:

- reference pyramids
- multi-resolution search
- downsampling the higher-resolution side
- comparison at similar effective ground scales
- coarse-to-fine localization

Fine refinement must stop when the source sensor lacks sufficient information to support a finer claim.

Do not describe interpolation as detail recovery.

---

### 5.3 Illumination handling

Lunar illumination differences are not merely brightness changes.

Different Sun angles change:

- shadow position
- shadow length
- local contrast
- apparent crater geometry
- ridge and relief appearance

Histogram equalization or contrast normalization cannot undo shadow geometry.

When investigating illumination robustness, possible experimental representations include:

- gradients
- edges
- structural descriptors
- phase-based representations
- shadow-aware features
- illumination metadata
- controlled photometric correction

Do not claim illumination invariance unless supported by measured evidence.

---

### 5.4 Matching terminology

Use precise terminology.

A matcher output is normally:

> **candidate matches**

It does not become:

> **verified matches / inliers**

until geometric verification accepts it.

Matcher confidence is not geometric proof.

Recommended conceptual flow:

```text
Candidate Matches
        ↓
RANSAC / Initial Geometric Model
        ↓
Verified Inliers
        ↓
Sub-Pixel Tie-Point Refinement
        ↓
Final Transform Refit
        ↓
Registration
        ↓
Independent Evaluation
```

Do not skip the final transform refit after refined tie-point coordinates are produced unless the implementation explicitly justifies a different design.

---

### 5.5 Sub-pixel accuracy

Sub-pixel refinement should normally operate on geometrically verified tie points rather than arbitrary raw candidate matches.

Report source-image accuracy in **source-image pixels** before converting it into ground units.

Do not confuse:

> sub-pixel image accuracy

with:

> sub-metre ground accuracy

For example, the physical meaning of `0.2 px` differs substantially between OHRC, TMC-2, and IIRS.

Conversion to metres requires appropriate:

- ground sampling distance
- map projection
- product geometry
- reference truth

---

### 5.6 Transformation direction

Always establish whether a transformation maps:

```text
source → reference
```

or:

```text
reference → source
```

Do not infer direction solely from variable names.

Tests and documentation should make direction explicit when ambiguity could cause incorrect results.

---

### 5.7 Image coordinate conventions

Before modifying numerical registration code, verify the project's coordinate convention.

Common possibilities include:

```text
(x, y)
```

and:

```text
(row, column)
```

Do not silently swap them.

Also verify:

- image origin
- width/height ordering
- pixel-center convention
- zero-based indexing
- source/reference ordering

---

### 5.8 Numerical units

Preserve explicit distinctions between units such as:

- pixels
- metres
- metres/pixel
- degrees
- radians
- latitude/longitude
- normalized coordinates

Avoid ambiguous numeric fields in public interfaces.

---

### 5.9 Geospatial assumptions

Do not assume Earth-centric geospatial defaults are appropriate for lunar imagery.

When working with:

- CRS definitions
- projection systems
- longitude conventions
- planetary coordinate systems
- spheroids
- map projection metadata

inspect project or mission documentation and existing implementation.

Do not casually substitute an Earth CRS.

---

### 5.10 Geometry model selection

Affine transforms or homographies can be useful for local, map-projected pairs, but lunar terrain is not a flat poster.

Inspect residual behavior before assuming a single global transform is sufficient.

Potential reasons for spatially varying residuals include:

- terrain relief
- viewpoint differences
- sensor geometry
- incomplete orthorectification
- projection mismatch

Local or piecewise refinement should be introduced only when justified by observed failure patterns.

A flexible warp must not be used to conceal poor correspondences.

---

## 6. Benchmark Architecture

ChandraMap uses conceptual benchmark/research configurations.

These are **not software release versions**.

Do not assume:

```text
Benchmark V1 == package release v1.0.0
Benchmark V2 == package release v2.0.0
Benchmark V3 == package release v3.0.0
Benchmark V4 == package release v4.0.0
```

unless repository documentation explicitly defines such a mapping.

### Benchmark V1 — Classical baseline

V1 should remain simple, reproducible, and explainable.

Typical conceptual flow:

```text
Input Pair
    ↓
Basic Preprocessing
    ↓
SIFT / RootSIFT
    ↓
Descriptor Matching
    ↓
Match Filtering
    ↓
RANSAC
    ↓
Affine / Homography
    ↓
Registration
    ↓
Evaluation
```

Do not introduce unnecessary advanced ML dependencies into V1.

V1 exists to provide a controlled baseline.

---

### Benchmark V2 — Sensor-aware and multi-scale

V2 may introduce:

- sensor routing
- sensor-specific preprocessing
- OHRC-specific handling
- TMC-2-specific handling
- IIRS 2D registration representations
- reference pyramids
- scale-aware search
- gradients or structural representations
- illumination stress handling
- improved rejection rules
- spatial coverage metrics

Preserve direct comparison with V1 where practical.

---

### Benchmark V3 — Advanced matching and retrieval

V3 may introduce stronger local matchers and optional global retrieval.

Potential local matching approaches may include:

- ALIKED + LightGlue
- LoFTR
- RIFT-inspired approaches
- CFOG-inspired approaches
- other justified remote-sensing matchers

Global retrieval and local matching are different tasks.

A conceptual retrieval system may look like:

```text
OFFLINE

Reference Imagery
        ↓
Tile Generation
        ↓
Multi-Scale Pyramid
        ↓
Global Descriptor Extraction
        ↓
Vector Index + Metadata Index
```

```text
ONLINE

Source Image
        ↓
Global Descriptor
        ↓
Top-K Candidate Retrieval
        ↓
Local Matching
        ↓
Geometric Verification
```

### FAISS rule

FAISS is a vector similarity-search/indexing system.

It does **not** itself extract image features.

Do not conflate:

> global retrieval descriptors

with:

> local matching features

These solve different problems.

---

### Benchmark V4 — Research-grade robustness

V4 may explore:

- robust sensor routing
- improved IIRS processing
- DEM-aware geometry
- local or piecewise refinement
- uncertainty estimation
- confidence calibration
- quality gates
- accept/refine/reject logic
- failure classification
- matcher selection
- scalable retrieval
- reproducible experiment orchestration
- sensor-specific model/configuration selection

Do not interpret "advanced" as "add more neural networks."

A method belongs in V4 only if it addresses a documented weakness or research question.

---

## 7. Evaluation Rules

Registration quality must be demonstrated through measured evidence.

Relevant metrics may include:

- check-point RMSE
- source-image pixel error
- reprojection residuals
- inlier count
- inlier ratio
- grid coverage
- convex-hull coverage
- spatial spread
- Recall@1
- Recall@5
- runtime
- registration success rate
- failure rate

### Independent evaluation

Do not fit and evaluate a transform using exactly the same points when independent evaluation is available.

Preferred pattern:

```text
Fit Points
    ↓
Estimate Transformation

Independent Check Points
    ↓
Evaluate Final Registration
```

When challenge-provided or independently verified ground truth exists, prefer it.

Do not label training/fitting residuals as independent validation.

---

### Spatial coverage

A large inlier count is not enough.

A cluster of matches around one crater can produce a high inlier count while poorly constraining the rest of the overlap.

Where appropriate, evaluate spatial distribution using metrics such as:

- occupied grid cells
- grid coverage
- convex-hull coverage
- normalized spatial spread

---

### Benchmark integrity

Never:

- invent benchmark values
- manually alter numerical result files to improve appearance
- hide failed pairs
- cherry-pick successful examples and present them as general performance
- silently change metric definitions
- silently change evaluation methodology
- compare incompatible evaluation methods as though they are equivalent

If evaluation methodology changes, document the effect on comparability.

---

### Performance claims

Do not write claims such as:

- "30% faster"
- "2× more accurate"
- "significantly better"
- "state-of-the-art"
- "fully invariant"
- "highest accuracy"

without measured evidence.

A performance claim should identify, where relevant:

- baseline
- benchmark
- dataset or image pairs
- metric
- configuration
- hardware
- evaluation procedure

---

## 8. Failure-First Design

ChandraMap should be capable of rejecting unreliable registration.

Do not force every input to produce an apparently successful result.

Potential rejection reasons include:

- invalid input
- unsupported sensor
- insufficient keypoints
- insufficient matches
- insufficient verified inliers
- poor spatial coverage
- degenerate transformation
- excessive residual error
- retrieval ambiguity
- inconsistent geometry
- no meaningful overlap
- unavailable model weights
- insufficient source information

Failures should remain visible and interpretable.

Do not silently turn failure into success.

---

## 9. Repository Navigation

Inspect the real repository before assuming its layout.

Possible top-level areas may include directories such as:

```text
.github/
apps/
artifacts/
benchmarks/
configs/
contracts/
data/
deploy/
docs/
experiments/
notebooks/
research/
results/
scripts/
services/
src/
tests/
```

Do not assume any of these exist until confirmed.

When present, a common separation should be respected:

```text
Stable / reusable implementation
            ↓
        src/... or equivalent

Exploratory research
            ↓
experiments/ / research/ / notebooks/ or equivalent
```

Do not move experimental code into stable core modules merely because one experiment produced a promising result.

Promotion into core implementation should normally include:

- cleanup
- tests
- configuration
- documentation
- benchmark evidence where scientifically relevant

---

## 10. Engineering Rules

### Minimal changes

Prefer the smallest change that correctly solves the requested problem.

Do not:

- refactor unrelated modules
- rename unrelated files
- reformat the entire repository
- replace established architecture unnecessarily
- introduce a framework for a small fix
- rewrite modules solely because another coding style is preferred

Focused patches are easier to review and safer for benchmark reproducibility.

---

### No drive-by refactoring

Unrelated issues discovered during a task may be reported.

Do not automatically fix them unless they are:

- necessary for correctness
- necessary for security
- required to complete the requested task
- explicitly requested

---

### Reuse existing patterns

Before creating a helper, abstraction, endpoint, schema, wrapper, service, or config field:

1. search the relevant repository scope
2. inspect existing implementations
3. reuse established patterns when appropriate

Avoid duplicate abstractions.

---

### Configuration

Do not hard-code research parameters when the project already has a configuration system.

Potential configurable values may include:

- matcher
- RANSAC thresholds
- preprocessing mode
- pyramid levels
- quality thresholds
- benchmark dataset
- retrieval settings
- model checkpoint
- sensor route

Inspect existing configuration structures before adding new keys.

---

### Dependencies

Do not add a dependency without checking whether existing dependencies already solve the problem.

Consider:

- necessity
- maintenance status
- license
- package size
- CPU requirements
- GPU requirements
- CI impact
- platform compatibility
- security
- transitive dependencies
- reproducibility

Document significant dependency additions where repository conventions require it.

---

### Public interfaces

Treat changes to these as potentially breaking:

- CLI commands
- APIs
- configuration formats
- schemas
- serialized outputs
- benchmark definitions
- result file formats

Search for all producers and consumers before changing a shared contract.

Do not casually rename public fields.

---

### Error handling

Do not silently swallow failures.

Errors should explain, where practical:

- what failed
- which input or operation caused the failure
- what the caller may need to correct

Do not leak:

- credentials
- secrets
- private tokens
- unnecessary internal system paths

---

### Logging

Follow existing logging conventions.

Avoid:

- random `print()` calls in core code
- logging complete scientific arrays
- logging credentials
- logging unnecessary full user datasets
- suppressing exceptions without useful context

---

### Comments and docstrings

Comments should explain:

- why a non-obvious decision exists
- scientific assumptions
- numerical edge cases
- implementation constraints

Do not add comments that simply restate obvious code.

Important public or non-obvious APIs should document, where relevant:

- purpose
- parameters
- return values
- units
- coordinate conventions
- exceptions
- scientific assumptions

Keep documentation synchronized with implementation.

---

## 11. Python and Numerical Code

Follow repository tooling and established style rather than inventing new formatting rules.

Where consistent with existing code:

- use type hints for important interfaces
- prefer clear function boundaries
- use meaningful names
- avoid hidden mutable global state
- handle errors explicitly
- use `pathlib` where appropriate
- separate I/O from core numerical logic when useful
- preserve deterministic behavior where practical
- avoid unnecessary copies of large arrays

Numerical and geospatial code is especially sensitive to:

- coordinate ordering
- transformation direction
- units
- precision
- projection assumptions
- image origin
- source/reference ordering

Inspect tests and existing implementation before changing these areas.

---

## 12. AI/ML Model Rules

When adding or changing a learned model, document where relevant:

- model name
- architecture
- upstream source
- checkpoint source
- license
- model version
- expected preprocessing
- framework version
- hardware requirements
- CPU/GPU behavior

Do not assume pretrained terrestrial features are automatically robust to lunar imagery.

Benchmark them.

### Model downloads

Avoid silently downloading large checkpoints during:

- module import
- test collection
- basic CLI startup
- installation

Prefer explicit model acquisition or repository-approved caching behavior.

---

## 13. Data and Artifact Rules

### Large scientific data

Large mission datasets should generally not be committed to Git unless repository policy explicitly says otherwise.

Prefer:

- dataset manifests
- checksums
- metadata
- download instructions
- download scripts
- small legal test fixtures

Respect external dataset licensing and redistribution requirements.

Do not automatically redistribute mission data.

---

### Data provenance

Preserve scientifically meaningful metadata where relevant, including:

- mission
- instrument
- product identifier
- source
- product level
- projection
- GSD
- footprint
- illumination metadata
- viewing geometry
- acquisition metadata

Do not strip provenance without justification.

---

### Generated files

Before modifying a generated file, determine whether the repository intentionally tracks it.

Generated artifacts may include:

- benchmark outputs
- registered images
- previews
- model checkpoints
- image tiles
- retrieval indexes
- caches
- logs
- build outputs
- experiment results

Do not manually edit generated numerical results when they should be reproduced from source.

---

### Result provenance

Generated scientific result files should be traceable, where practical, to:

- benchmark
- configuration
- dataset/pairs
- code version or commit
- model/checkpoint
- random seed
- command or execution method

---

## 14. Notebook and Research Rules

Notebooks are useful for exploration but should not become the only implementation of core project functionality.

When modifying notebooks:

- preserve reproducibility
- avoid secrets
- avoid embedding large datasets unnecessarily
- avoid excessive generated output when repository conventions discourage it
- move reusable algorithms into normal modules when appropriate
- record important experiment assumptions

Experimental code may prioritize exploration over API stability, but must still preserve:

- safety
- provenance
- scientific honesty
- reproducibility
- data-handling rules

---

## 15. Security Rules

Never commit:

- `.env`
- passwords
- API keys
- private tokens
- private certificates
- SSH private keys
- cloud credentials
- database credentials
- service-account credentials

`.env.example`, when present, should contain only safe examples or variable names.

If an exposed credential is discovered, deleting the line is not sufficient.

Treat the credential as potentially compromised and recommend rotation.

Follow `SECURITY.md` when applicable.

---

### Untrusted files

Treat external files as untrusted when appropriate.

Potential input types may include:

- GeoTIFF
- TIFF
- PNG
- JPEG
- PDS products
- metadata files
- hyperspectral cubes
- NumPy files
- archives
- model checkpoints
- vector indexes
- configuration files

Security-sensitive code should consider:

- file-size limits
- malformed metadata
- path traversal
- unsafe archive extraction
- parser errors
- decompression bombs
- resource exhaustion
- unsafe deserialization

---

### Deserialization

Avoid unsafe loading of untrusted serialized objects.

Use caution with:

- `pickle`
- pickle-backed `joblib`
- arbitrary model checkpoints
- unsafe YAML loaders
- executable configuration formats

Prefer safer formats and loading mechanisms where practical.

---

### Subprocesses

When invoking external programs:

- prefer argument arrays
- validate user-controlled parameters
- avoid shell interpolation
- avoid `shell=True` unless clearly necessary and safe
- surface useful failures
- avoid exposing secrets in command arguments

---

### Network operations

Do not introduce network calls unnecessarily.

If a feature downloads:

- imagery
- datasets
- metadata
- model weights

make the behavior explicit and configurable when appropriate.

Avoid making ordinary unit tests depend on unreliable external services.

---

## 16. Backend and Frontend Rules

### Backend

For backend work:

- validate external input
- use explicit schemas where repository conventions support them
- preserve service boundaries
- handle file paths safely
- preserve useful error states
- avoid exposing unnecessary internal details
- avoid coupling stable APIs directly to one experimental matcher unless intended
- avoid performing expensive synchronous ML/CV work in inappropriate request paths

---

### Frontend and visualization

UI must represent scientific state accurately.

Distinguish:

- candidate matches
- verified inliers
- rejected matches
- accepted registration
- failed registration

Display metrics with correct units.

Do not:

- invent confidence percentages
- hard-code fake benchmark numbers
- use decorative experimental values
- imply that an attractive overlay proves accuracy

Keep scientific computation separate from presentation logic where practical.

---

## 17. Testing Strategy

After changing code, run the smallest relevant validation first.

Preferred progression:

```text
Changed Function
      ↓
Relevant Unit Tests
      ↓
Affected Module Tests
      ↓
Relevant Integration Tests
      ↓
Broader Test Suite if justified
      ↓
Scientific Benchmark only when necessary
```

Do not run expensive full-project scientific benchmarks for every trivial change.

Do not claim tests passed unless they actually ran successfully.

---

### Unit testing

Potential unit-test targets include:

- preprocessing
- feature utilities
- metadata parsing
- coordinate conversions
- transformation helpers
- metrics
- validation
- configuration loading

Avoid overmocking numerical behavior that can be tested directly.

---

### Integration testing

Potential integration tests may cover:

- known image-pair registration
- retrieval → local matching
- CLI workflows
- API workflows
- result serialization
- pipeline configuration

Use small fixtures where possible.

---

### Regression testing

A bug fix should receive a regression test when practical.

A benchmark-impacting bug fix should include evidence that the original failure no longer occurs.

---

### Failure testing

Relevant cases may include:

- blank images
- corrupt files
- missing metadata
- unsupported sensors
- insufficient keypoints
- insufficient matches
- zero RANSAC inliers
- degenerate homography
- no overlap
- invalid coordinates
- missing model weights
- invalid configuration
- malformed dataset manifests

---

### Benchmark-impacting changes

If a change affects registration quality, successful execution is not enough.

Compare against an appropriate baseline using controlled conditions where practical.

Record:

- data pairs
- configuration
- matcher
- metrics
- evaluation method
- hardware when runtime matters
- seed where relevant

---

## 18. Git and Change Safety

Treat existing uncommitted user changes as intentional unless there is evidence otherwise.

Do not overwrite them casually.

If they conflict with the requested task, surface the conflict.

### Avoid destructive operations

Do not casually:

- delete large directory trees
- reset repository history
- force-push
- wipe databases
- delete datasets
- remove experiment results
- recursively modify permissions
- remove all untracked files

Prefer reversible operations.

### Git safety

Do not:

- rewrite unrelated history
- discard unrelated user changes
- modify unrelated commits
- reset files outside task scope
- force-push

unless explicitly requested and clearly justified.

---

### Deletion

Before deleting a:

- file
- function
- config key
- dependency
- schema field

search for:

- references
- tests
- documentation
- scripts
- CI usage
- deployment usage

Do not assume "not imported nearby" means unused.

---

### Renaming

When renaming, update all applicable:

- imports
- tests
- configuration
- documentation
- scripts
- API contracts
- serialized formats

Consider compatibility before completing the rename.

Avoid partial renames.

---

### Formatting

Avoid large format-only diffs unrelated to the requested work.

Limit formatting to relevant files unless repository tooling requires otherwise.

---

## 19. CI, Docker, and MLOps

### CI

Inspect existing CI configuration before modifying it.

Do not invent CI architecture.

When changing CI workflows:

- preserve least privilege where practical
- avoid exposing secrets to untrusted code
- minimize unnecessary permissions
- use stable/reviewed actions
- evaluate caching carefully
- keep lightweight CI separate from expensive scientific benchmarks where appropriate

---

### Docker

If Docker is used:

- inspect existing Dockerfiles and Compose files
- avoid redundant containers
- never embed secrets
- keep build contexts appropriately small
- account for CPU/GPU requirements
- avoid unnecessary privileged execution
- preserve reproducibility

Do not introduce Docker solely because it is common elsewhere.

---

### MLOps

Keep the following concepts distinct:

- model definition
- model checkpoint
- dataset
- experiment configuration
- benchmark result
- production artifact

Do not silently replace one without updating provenance and configuration.

---

## 20. Documentation Rules

Documentation must describe actual project state.

Do not write:

> "ChandraMap supports IIRS registration"

when only a planned design exists.

Use wording such as:

> "Planned support"

when that accurately reflects implementation status.

Do not transform a research idea into a proven capability.

### When behavior changes

Determine whether corresponding documentation needs updates.

Relevant documentation may include:

- `README.md`
- `CHANGELOG.md`
- `ROADMAP.md`
- `CONTRIBUTING.md`
- `SECURITY.md`
- files under `docs/`
- benchmark documentation
- API documentation
- CLI documentation

Do not update unrelated documentation.

---

### CHANGELOG

`CHANGELOG.md` should describe completed notable changes.

Consider updating it for:

- user-facing features
- breaking changes
- significant bug fixes
- benchmark methodology changes
- supported sensor changes
- major API/schema changes
- security fixes

Do not add every internal refactor.

---

### ROADMAP

`ROADMAP.md` describes planned work.

Do not mark roadmap items complete unless implementation and evidence support that status.

Do not move future ideas into the changelog as though they are completed.

---

### CONTRIBUTING

Follow `CONTRIBUTING.md` when present for:

- development workflow
- testing
- style
- contribution process
- benchmark requirements
- documentation expectations

Do not duplicate the full contribution guide inside this file.

---

### Security and conduct

Use:

- `SECURITY.md` for vulnerability-sensitive work
- `CODE_OF_CONDUCT.md` for community and communication expectations

Technical disagreement should remain evidence-based and professional.

---

### License and third-party code

Before introducing copied or adapted third-party code, verify:

- source
- license
- compatibility
- attribution requirements

Do not copy arbitrary online code into the repository without checking licensing.

---

### Research citations

When implementing published methods materially, preserve appropriate citations where repository conventions require them.

Possible examples include work related to:

- SIFT
- LightGlue
- ALIKED
- LoFTR
- RIFT
- CFOG

Do not invent papers or citations.

---

## 21. Optional `.ai/` Integration

If a structured `.ai/` directory exists, this `AGENTS.md` should act as an entry point rather than duplicating all detailed instructions.

A repository may contain task-specific AI documentation such as:

```text
.ai/
├── README.md
├── ENGINEERING_RULES.md
├── context/
├── architecture/
├── development/
├── testing/
├── benchmarks/
└── workflows/
```

Do not assume these paths exist.

Load only the relevant AI context for the task.

For example:

```text
Architecture task
→ architecture-related AI context

Benchmark task
→ benchmark/evaluation rules

Dataset task
→ dataset/domain context

Testing task
→ testing instructions
```

Do not automatically load an entire `.ai/` directory.

---

## 22. Research Claim Discipline

Always separate:

> **Established fact**

from:

> **Repository-specific measured result**

from:

> **Hypothesis / proposed research direction**

Examples:

- Sensor specifications documented by mission sources are external facts.
- A measured RMSE from a ChandraMap benchmark is a repository-specific result.
- A proposed matcher expected to improve illumination robustness is a hypothesis until tested.

Do not collapse these categories.

Do not describe ChandraMap as:

- state-of-the-art
- universally robust
- fully invariant
- highest accuracy
- production-proven

without appropriate evidence.

---

## 23. Agent Communication

After completing technical work, report concisely:

- what changed
- why it changed
- validation performed
- important limitations
- files affected when useful

Do not generate a large essay after a small patch.

### No fake execution

Never claim:

- tests passed
- build succeeded
- benchmark improved
- deployment succeeded
- file was created
- command completed

unless that action actually occurred and its result was observed.

### No fake inspection

Never say:

> "I checked `pyproject.toml`"

unless it was actually inspected.

Never claim repository knowledge that was not loaded.

---

## 24. When Tests Cannot Run

If validation cannot run because of:

- missing dependencies
- unavailable GPU
- unavailable dataset
- unavailable model weights
- network restrictions
- environment limitations

do not claim success.

Report:

- what was attempted
- what prevented execution
- which validation remains outstanding

---

## 25. Ambiguity and Conflicts

### Ambiguous requirements

If multiple interpretations would produce materially different architecture or behavior:

1. inspect repository evidence
2. identify existing conventions
3. determine whether one interpretation is clearly established
4. request clarification if significant ambiguity remains

For minor low-risk implementation details, follow established project conventions.

---

### Conflicting documentation

If repository documents conflict:

- identify the conflict
- prefer the more specific/current authoritative source where clear
- do not silently combine contradictory requirements
- report the inconsistency if it affects implementation

---

### Code versus documentation

Do not automatically assume either code or documentation is correct.

Investigate whether:

- code changed without documentation
- documentation describes intended but unimplemented behavior
- implementation contains a regression
- tests clarify expected behavior

---

## 26. Before-Change Checklist

Before editing:

- [ ] I understand the requested behavior.
- [ ] I identified the smallest relevant project area.
- [ ] I inspected the affected implementation.
- [ ] I checked nearby relevant tests.
- [ ] I checked relevant configuration.
- [ ] I checked applicable project documentation.
- [ ] I searched for existing utilities and established patterns.
- [ ] I identified public-interface compatibility implications.
- [ ] I identified benchmark/scientific impact where applicable.
- [ ] I understand coordinate conventions and units where relevant.
- [ ] I am not assuming unverified repository facts.
- [ ] I am not about to overwrite unrelated user changes.

---

## 27. Completion Checklist

Before declaring the task complete:

- [ ] The requested behavior is implemented.
- [ ] The change remains focused.
- [ ] Relevant tests were actually run where possible.
- [ ] Test failures or limitations are disclosed.
- [ ] No obvious regression was introduced.
- [ ] Documentation was updated where necessary.
- [ ] No secrets were introduced.
- [ ] No unrelated user changes were overwritten.
- [ ] Public interfaces remain compatible unless intentionally changed.
- [ ] Scientific claims are evidence-based.
- [ ] Benchmark methodology was not silently altered.
- [ ] Generated results were not manually falsified.
- [ ] Failure behavior remains explicit.
- [ ] Data/model/result provenance remains adequate where relevant.

---

## 28. Prohibited Behaviors

Agents must not:

- invent repository architecture
- fabricate files, APIs, dependencies, or configuration
- fabricate benchmark results
- claim tests ran when they did not
- claim builds or deployments succeeded without observing them
- silently change benchmark methodology
- hard-code fake confidence values
- commit credentials or secrets
- overwrite unrelated user work
- perform destructive Git operations unnecessarily
- add large scientific datasets without project justification
- redistribute data without considering applicable licensing
- treat IIRS as an ordinary grayscale camera without a defined conversion
- claim interpolation or upsampling recovers missing spatial detail
- call candidate matches verified before geometric verification
- treat matcher confidence as geometric proof
- apply sub-pixel refinement blindly to arbitrary candidate matches
- omit final transform refitting when refined coordinates are intended to define the final model
- present fitting-point residuals as independent validation
- claim sub-pixel image accuracy implies sub-metre ground accuracy
- ignore spatial distribution of correspondences
- hide failure cases to make results appear stronger
- force unreliable registration to appear successful
- add dependencies without justification
- promote research prototypes directly into stable code without validation
- claim roadmap features are implemented
- describe FAISS as a feature extractor
- confuse global retrieval features with local matching features
- introduce Earth-specific geospatial assumptions into lunar processing without verification
- manually edit benchmark result numbers for presentation
- call the project state-of-the-art without evidence

---

## 29. Documentation Map

Use the appropriate source of truth rather than expanding this file indefinitely.

| Source               | Primary purpose                                                        |
| -------------------- | ---------------------------------------------------------------------- |
| `README.md`          | Project overview, setup, usage, user-facing entry point                |
| `CONTRIBUTING.md`    | Contribution workflow, development expectations                        |
| `ROADMAP.md`         | Planned technical and research direction                               |
| `CHANGELOG.md`       | Completed notable changes                                              |
| `SECURITY.md`        | Security and vulnerability procedures                                  |
| `CODE_OF_CONDUCT.md` | Community behavior expectations                                        |
| `CITATION.cff`       | Software citation metadata                                             |
| `docs/`              | Detailed technical, architectural, project, and research documentation |
| `benchmarks/`        | Benchmark definitions/configurations when present                      |
| `configs/`           | Runtime and experiment configuration when present                      |
| `tests/`             | Executable behavioral expectations when present                        |
| `src/`               | Core reusable implementation when present                              |
| `experiments/`       | Experimental work when present                                         |
| `research/`          | Research implementations/notes when present                            |
| `notebooks/`         | Exploratory analysis when present                                      |
| `.ai/`               | Additional agent context when actually present                         |

When a listed file or directory is absent, do not invent it.

Inspect the real repository and use the sources that actually exist.

---

## 30. Final Operating Principle

For every task, optimize in this order:

```text
Correctness
    ↓
Project consistency
    ↓
Scientific validity
    ↓
Reproducibility
    ↓
Safety
    ↓
Maintainability
    ↓
Minimal change scope
    ↓
Efficiency / context reduction
```

The goal is not to produce the most code, use the most advanced model, or modify the most files.

The goal is to make the **smallest well-supported change that improves ChandraMap without compromising scientific, engineering, or repository integrity**.

<!-- Source request: :contentReference[oaicite:0]{index=0} -->
