# AI Software Engineering Rules

This document defines the universal software-engineering rules for AI coding agents working on **ChandraMap**.

It governs **how engineering work should be performed**: how tasks are understood, how repository context is loaded, how code is changed, how scientific validity and benchmark integrity are preserved, how changes are tested, and how completed work is reported.

This file does not replace project-specific architecture, domain, benchmark, testing, security, roadmap, or contribution documentation. Load those documents only when they are relevant to the current task.

> **Primary rule: UNDERSTAND BEFORE MODIFYING.**

The objective is not to make the largest change or use the most advanced technique. The objective is to make the **smallest well-supported change that correctly satisfies the requirement while preserving scientific, engineering, and repository integrity**.

---

## 1. Engineering Priorities

When engineering concerns compete, use the following priority order:

1. **Correctness**
2. **Scientific validity**
3. **Security**
4. **Project consistency**
5. **Reproducibility**
6. **Backward compatibility where required**
7. **Maintainability**
8. **Minimal change scope**
9. **Performance**
10. **Context/token efficiency**

Do not optimize token usage at the expense of understanding the task.

Do not optimize runtime at the expense of correctness unless the task explicitly requires that trade-off and the impact is measured.

Do not sacrifice scientific validity merely to make an output appear more successful.

---

## 2. Understand Before Modifying

Before changing anything, inspect the smallest relevant part of the repository necessary to understand the task.

Identify, where applicable:

- affected application, service, package, or module
- implementation language and framework
- entry point
- affected functions/classes
- nearby tests
- configuration
- public interfaces
- schemas/contracts
- relevant documentation
- applicable AI instructions
- benchmark or scientific implications

Do not assume:

- framework
- architecture
- package manager
- module location
- API design
- database
- storage system
- dependency
- model
- dataset format
- CLI command
- configuration schema
- testing framework
- deployment environment

without repository evidence.

Reuse existing:

- architecture
- conventions
- dependencies
- utilities
- schemas
- abstractions
- testing patterns
- configuration systems

when they remain appropriate.

Start with the smallest useful context and expand only when required.

Do not scan the entire repository for a narrowly scoped task unless evidence shows that the broader context is necessary.

---

## 3. Determine the Requirement

Before implementation, determine what the request actually requires.

Identify:

- requested behavior
- expected inputs
- expected outputs
- existing behavior that must remain unchanged
- affected modules
- public API/CLI implications
- configuration implications
- dataset implications
- benchmark implications
- scientific implications
- failure behavior
- compatibility requirements
- validation requirements

Classify the task when useful:

- bug fix
- feature
- refactor
- experiment
- benchmark change
- metric change
- documentation change
- configuration change
- dependency change
- infrastructure change
- security fix
- performance optimization

Do not treat every request as a new-feature request.

A bug fix should generally preserve intended behavior.

A refactor should generally preserve behavior unless explicitly stated otherwise.

An experiment should not automatically become production behavior.

---

## 4. Resolve Ambiguity Early

If the request permits multiple materially different implementations:

1. inspect existing repository conventions
2. inspect nearby implementation
3. inspect applicable documentation
4. inspect relevant tests
5. infer only where repository evidence is strong

Request clarification when ambiguity remains significant and an incorrect choice could materially affect:

- architecture
- public behavior
- scientific methodology
- benchmark definitions
- data formats
- serialized outputs
- compatibility
- security

Do not ask unnecessary questions when existing project patterns already resolve a low-risk implementation detail.

---

## 5. Load Context Progressively

Use progressive context loading.

```text
Task
  ↓
Applicable instructions
  ↓
Affected file/module
  ↓
Nearby tests
  ↓
Relevant configuration
  ↓
Relevant documentation
  ↓
Additional context only if necessary
```

### Small local change

A small validation or utility bug may require only:

- relevant engineering instructions
- affected implementation
- nearby tests
- relevant configuration

It should not automatically require:

- all benchmark specifications
- all dataset documentation
- the entire architecture
- every notebook
- every experiment

### Scientific pipeline change

A registration or matching change may require:

- engineering rules
- domain context
- pipeline documentation
- benchmark specification
- metric definitions
- affected implementation
- relevant configuration
- tests

Context size should match task scope and risk.

Correctness takes priority over minimizing context.

---

## 6. Stop Loading Context When It Is Sufficient

An agent generally has enough context when it can accurately explain:

- what needs to change
- why the change belongs where it does
- expected behavior
- behavior that must remain unchanged
- applicable project rules
- validation strategy
- compatibility impact
- scientific impact where relevant
- benchmark impact where relevant

Once these are understood from repository evidence, do not continue loading unrelated files.

---

## 7. Search Before Creating

Before creating a new:

- helper
- utility
- abstraction
- service
- configuration key
- schema
- model wrapper
- result object
- endpoint
- CLI option
- exception
- parser
- coordinate utility
- metric implementation

search the relevant repository scope for equivalent functionality.

Prefer:

```text
reuse
  ↓
extend
  ↓
small new abstraction
```

over duplicate implementations.

Do not introduce competing utilities for the same responsibility without a clear architectural reason.

---

## 8. Respect Existing Architecture

Do not redesign project architecture unless the task actually requires architectural change.

Preserve established:

- module boundaries
- dependency direction
- configuration mechanisms
- result contracts
- logging conventions
- serialization approaches
- service boundaries
- testing patterns

when they remain correct.

Do not introduce a design pattern, framework, service, or abstraction merely because it is common elsewhere.

Large architectural changes require stronger evidence and broader validation than local implementation changes.

---

## 9. Keep Changes Focused

Implement the smallest complete change that solves the requested requirement.

Do not unnecessarily:

- refactor unrelated modules
- rename unrelated files
- reorganize directories
- reformat unrelated code
- update unrelated dependencies
- change benchmark definitions
- rewrite working modules
- introduce new frameworks
- modify generated results

A focused diff is easier to:

- understand
- review
- test
- benchmark
- revert

---

### 9.1 No Drive-By Refactoring

If unrelated issues are discovered:

- report them when important
- recommend separate work where appropriate

Do not automatically fix them in the same task.

Exceptions include:

- a security issue that must be addressed immediately
- a correctness issue blocking the requested task
- a broken implementation that must be repaired to complete the task safely

Keep unrelated changes isolated whenever practical.

---

## 10. Preserve User Work

Treat existing repository modifications as intentional unless evidence shows otherwise.

Do not casually overwrite:

- uncommitted changes
- research experiments
- local configuration
- user-written documentation
- generated scientific results
- partially completed features

If the requested work conflicts with existing changes, surface the conflict.

Do not use destructive Git operations merely to obtain a clean workspace.

---

## 11. Do Not Fabricate Repository Facts

Never invent:

- files
- directories
- functions
- classes
- services
- APIs
- endpoints
- CLI commands
- package-manager commands
- dependencies
- environment variables
- datasets
- model checkpoints
- benchmark results
- test counts
- version numbers
- CI behavior
- deployment behavior
- configuration keys

If something has not been verified:

- inspect it, or
- state the uncertainty

Do not substitute confident assumptions for repository evidence.

---

## 12. Distinguish Project Status Clearly

Always distinguish among:

| Status           | Meaning                                        |
| ---------------- | ---------------------------------------------- |
| **Implemented**  | Exists in current implementation               |
| **Tested**       | Validated by tests that were actually executed |
| **Documented**   | Described in documentation                     |
| **Experimental** | Prototype/research implementation              |
| **Planned**      | Future work described by roadmap/design        |
| **Proposed**     | Idea under consideration                       |

Do not describe planned functionality as implemented.

Do not describe experimental code as stable behavior.

Do not describe functionality as tested unless relevant tests actually ran.

---

# 13. Scientific Engineering Rules

ChandraMap is fundamentally a **lunar image correspondence and registration system**.

Primary source imagery may include:

- Chandrayaan-2 OHRC
- Chandrayaan-2 TMC-2
- Chandrayaan-2 IIRS

Reference imagery may include:

- LRO NAC
- LRO WAC

Core outputs may include:

- candidate correspondences
- geometrically verified inliers
- tie points
- transformation models
- registered imagery
- localization information where meaningful
- quality metrics
- explicit rejection/failure information

Mosaics, globes, and map interfaces are downstream applications.

A visually convincing result is not sufficient scientific validation.

---

## 13.1 Sensor Handling

Do not treat OHRC, TMC-2, and IIRS as equivalent image sources.

### OHRC

OHRC provides high-resolution panchromatic lunar imagery.

Use actual product metadata when available.

Do not assume every OHRC product has identical scale or processing state.

### TMC-2

Use the instrument name:

**TMC-2**

when referring to the Chandrayaan-2 Terrain Mapping Camera-2.

TMC-2 provides panchromatic terrain imagery at a substantially coarser scale than OHRC and may support terrain-scale structural correspondence.

### IIRS

IIRS is **hyperspectral / imaging-infrared data** with substantially lower spatial resolution than OHRC or TMC-2.

Do not treat an entire hyperspectral cube as an ordinary grayscale image without an explicit scientific design.

A conventional 2D registration pipeline may require a registration-friendly representation such as:

- selected bands
- PCA-derived representation
- composite
- gradient representation
- edge representation
- other justified structural representation

The chosen representation must be defined and evaluated.

---

## 13.2 Physical Scale

Upsampling changes pixel count.

It does **not** recover missing spatial information.

Do not imply:

```text
80 m/px data
    ↓ interpolation
1 m/px physical information
```

Cross-resolution processing should use scientifically meaningful strategies such as:

- multi-resolution reference pyramids
- downsampling higher-resolution data
- scale-aware search
- effective GSD comparison
- coarse-to-fine localization

Fine refinement must not make claims beyond what the source sensor can physically support.

---

## 13.3 Illumination

Different lunar Sun angles change shadow geometry.

They may alter:

- shadow direction
- shadow length
- brightness
- contrast
- apparent crater structure
- ridge visibility

Brightness normalization alone cannot remove all of these differences.

Any claim of:

- illumination invariance
- Sun-angle invariance
- shadow robustness

must be supported by benchmark evidence.

---

## 13.4 Matching and Geometry

Candidate matcher output is not automatically a geometrically verified correspondence.

Matcher confidence is not geometric proof.

Use terminology accurately:

```text
Candidate Matches
        ↓
RANSAC / Initial Geometry
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

Do not rename candidate matches as verified inliers before geometric verification.

Sub-pixel refinement should normally operate on geometrically verified tie points rather than arbitrary raw matcher output.

After refined coordinates are available, refit the final transformation unless the documented algorithm intentionally uses another approach.

---

## 13.5 Evaluation

Do not present errors on fitting points as independent validation when independent evaluation is available.

Distinguish:

```text
fit-point residual
```

from:

```text
independent check-point error
```

Prefer independent check points or externally validated ground truth when available.

Also distinguish:

```text
pixel error
```

from:

```text
ground error in metres
```

A sub-pixel image error does not automatically imply sub-metre geospatial accuracy.

Ground-error claims require appropriate:

- GSD
- projection information
- coordinate handling
- reference truth

---

## 13.6 Spatial Coverage

More inliers do not automatically mean better registration.

A dense cluster of correspondences around one feature may constrain the global geometry poorly.

Where relevant, evaluate spatial distribution using metrics such as:

- occupied grid cells
- grid coverage
- convex-hull coverage
- other project-defined spatial spread metrics

---

## 13.7 Failure Handling

Registration failure is a valid outcome.

The system must be able to reject weak evidence.

Possible rejection causes may include:

- insufficient candidate matches
- insufficient verified inliers
- poor spatial coverage
- degenerate transformation
- excessive residuals
- unsupported input
- invalid metadata
- retrieval ambiguity
- insufficient overlap
- missing model resources

Do not convert:

> insufficient evidence

into:

> successful registration with low confidence

unless that behavior is explicitly defined by project design.

---

## 13.8 Benchmark Versions

ChandraMap may use:

- Benchmark V1
- Benchmark V2
- Benchmark V3
- Benchmark V4

These are research/benchmark configurations.

They are not automatically software release versions.

Do not assume:

```text
Benchmark V1 = software v1.0.0
Benchmark V2 = software v2.0.0
Benchmark V3 = software v3.0.0
Benchmark V4 = software v4.0.0
```

Never modify software-version metadata merely because a benchmark version changes.

---

## 13.9 Preserve V1 as a Baseline

Benchmark V1 should remain a simple classical baseline unless its specification explicitly changes.

Typical concepts may include:

- SIFT / RootSIFT
- descriptor matching
- match filtering
- RANSAC
- affine/homography estimation
- basic registration
- reproducible metrics

Do not introduce advanced learned matching into V1 solely to improve its benchmark score.

Its value is its simplicity and stability as a comparison baseline.

---

# 14. Core Code vs Experimental Code

Maintain a clear distinction between:

```text
Reusable / stable implementation
```

and:

```text
Experimental / research implementation
```

Possible project areas may include locations such as:

- `src/...`
- `experiments/...`
- `research/...`
- `notebooks/...`

Do not assume exact paths without repository inspection.

A promising research result should not be promoted directly into stable code.

Promotion may require:

- cleanup
- tests
- configuration
- documentation
- failure handling
- benchmark evidence
- interface stabilization

---

# 15. Code Quality Rules

Follow the repository's configured style and tooling.

Do not invent formatting, linting, or typing requirements when existing tooling defines them.

Where consistent with repository conventions, prefer:

- meaningful names
- focused functions
- explicit interfaces
- clear error handling
- type hints at important boundaries
- useful docstrings
- limited hidden global state
- separation of I/O and numerical logic
- deterministic behavior where practical

---

## 15.1 Readability

Prefer code that another maintainer can understand.

Avoid:

- clever one-liners for complex numerical logic
- deeply nested control flow
- hidden side effects
- unexplained abbreviations
- undocumented unit conversions
- unexplained constants

Research software benefits especially from explicit, readable code.

---

## 15.2 Comments

Comments should explain:

- why a non-obvious implementation exists
- scientific assumptions
- numerical edge cases
- performance trade-offs
- compatibility requirements

Do not add comments that merely repeat obvious code.

---

## 15.3 Docstrings

For important public or non-obvious functions, document where relevant:

- purpose
- parameters
- return values
- units
- coordinate conventions
- transform direction
- failure conditions
- assumptions

Keep docstrings synchronized with implementation.

---

## 15.4 Type Safety

Where the repository uses static typing:

- preserve existing type contracts
- update producers and consumers together
- avoid unnecessary `Any`
- make optional values explicit
- type important boundaries

Do not impose a new typing system without repository evidence.

---

## 15.5 Schemas and Contracts

When structured schemas already exist, do not replace them with undocumented arbitrary dictionaries.

Potential contracts include:

- API requests
- API responses
- registration results
- benchmark results
- configuration
- dataset manifests
- serialized artifacts

Schema changes may be breaking changes.

Search all producers and consumers before changing a contract.

---

# 16. Numerical and Geospatial Correctness

Image-registration and planetary-geospatial code is sensitive to convention errors.

Explicitly determine relevant conventions before modifying numerical code.

Examples include:

- `x, y` vs `row, column`
- width vs height
- source vs reference order
- image origin
- pixel-center convention
- zero-based indexing
- degrees vs radians
- metres vs pixels
- metres per pixel
- longitude convention
- coordinate reference system
- transformation direction

Do not infer these only from variable names.

Inspect implementation, tests, configuration, or authoritative documentation.

---

## 16.1 Units

Keep units explicit where ambiguity matters.

Examples:

- `px`
- `m`
- `m/px`
- degrees
- radians

Avoid unlabeled values in public interfaces when multiple interpretations are possible.

---

## 16.2 Transform Direction

Before changing transformation logic, determine whether a transform maps:

```text
source → reference
```

or:

```text
reference → source
```

Incorrect direction can produce visually plausible but scientifically incorrect output.

Tests should make transform direction explicit where practical.

---

## 16.3 Lunar Geospatial Systems

Do not automatically apply Earth-centric geospatial defaults to lunar data.

When modifying:

- CRS
- projection
- coordinate conversion
- longitude conventions
- planetary body models

inspect relevant project and mission documentation.

Do not silently substitute WGS84 or another Earth CRS for lunar coordinate systems.

---

# 17. Configuration Rules

Use the existing configuration system when one is present.

Potential configurable parameters may include:

- matcher
- RANSAC thresholds
- pyramid scales
- preprocessing mode
- benchmark dataset
- checkpoint
- quality thresholds
- retrieval parameters

Before creating a new configuration key:

1. inspect current configuration structure
2. inspect naming conventions
3. inspect validation
4. inspect defaults
5. inspect all consumers
6. inspect applicable documentation

Do not create hidden or duplicate configuration pathways.

---

## 17.1 Defaults

Default values should be:

- valid
- safe
- reproducible
- documented where necessary
- consistent with expected behavior

Do not silently change defaults that affect scientific results or benchmark comparisons.

---

## 17.2 Environment Variables

Do not add environment variables for ordinary experiment parameters without a clear reason.

Environment variables are generally more appropriate for:

- secrets
- deployment configuration
- runtime infrastructure

Research parameters often belong in explicit configuration files.

Follow existing repository conventions.

---

# 18. Dependency Rules

Before adding a dependency, determine:

- whether it is necessary
- whether existing packages already solve the task
- maintenance status
- license compatibility
- package size
- native/system requirements
- GPU/CUDA implications
- CI impact
- platform impact
- security impact
- reproducibility impact

Avoid dependency inflation.

---

## 18.1 Optional Dependencies

Large or specialized dependencies should be optional where architecture supports that design.

Examples may include:

- GPU frameworks
- advanced learned matchers
- large model ecosystems
- specialized planetary-processing software

Do not force the basic project import path to require every research dependency unless that is intentional repository architecture.

---

## 18.2 No Unrequested Upgrades

Do not upgrade:

- frameworks
- ML libraries
- geospatial libraries
- dependencies

merely because a newer release exists.

Version upgrades require a concrete engineering reason.

---

## 18.3 Lockfiles

If dependency changes regenerate a lockfile:

- verify that the update is expected
- review the generated diff
- avoid hand-editing generated lockfiles unless project tooling explicitly requires it

---

# 19. Models and Checkpoints

When adding or changing learned models, document where appropriate:

- model name
- architecture/source
- license
- checkpoint source
- framework
- expected preprocessing
- hardware requirements
- version or hash
- expected input representation

Do not assume pretrained terrestrial models are robust to lunar imagery.

Benchmark them.

Avoid silently downloading large model weights during:

- module import
- test discovery
- installation
- basic CLI startup

unless repository architecture explicitly defines that behavior.

---

# 20. Data and Artifact Rules

Do not commit large mission datasets merely because they are required locally.

Prefer repository-approved mechanisms such as:

- manifests
- download instructions
- checksums
- metadata
- small legal fixtures
- caching rules

Respect:

- dataset licensing
- attribution requirements
- redistribution rules
- mission data policies

---

## 20.1 Data Provenance

Where scientifically relevant, preserve:

- mission
- instrument
- product identifier
- product type/level
- source
- GSD
- projection
- footprint
- viewing geometry
- illumination metadata
- acquisition metadata

Do not discard metadata that materially affects scientific interpretation.

---

## 20.2 Generated Artifacts

Determine whether generated outputs are intended to be tracked before editing them.

Generated artifacts may include:

- benchmark reports
- registered imagery
- indexes
- model outputs
- build artifacts
- generated documentation
- caches
- experiment results

Do not manually edit generated outputs when a reproducible generation process exists.

---

## 20.3 Result Integrity

Never manually alter numerical result files to make performance appear better.

Scientific results must originate from reproducible execution.

---

# 21. Security Rules

Never commit or expose:

- `.env`
- passwords
- API keys
- access tokens
- private certificates
- SSH private keys
- cloud credentials
- service-account keys
- database passwords

Do not log secrets.

If a credential is discovered in repository history, deleting the visible line is not sufficient.

Treat the credential as potentially compromised and recommend:

- revocation
- rotation
- replacement

Follow repository security policy where applicable.

---

## 21.1 Untrusted Inputs

Potentially untrusted inputs may include:

- GeoTIFF
- TIFF
- PNG
- JPEG
- PDS products
- hyperspectral cubes
- HDF-style products
- archives
- NumPy artifacts
- configuration files
- model checkpoints
- vector indexes

When code crosses a trust boundary, consider:

- size limits
- malformed metadata
- parser failures
- path traversal
- decompression/resource exhaustion
- unsafe deserialization
- malicious archives

---

## 21.2 File Paths

Do not blindly trust user-controlled paths.

Where applicable:

- normalize paths
- restrict storage roots
- validate output locations
- prevent directory traversal
- avoid unsafe overwrite behavior

Do not invent sandboxing infrastructure if the project does not use it.

---

## 21.3 Archives

When handling archives, prevent extraction that enables:

- `../` path traversal
- absolute paths
- unintended overwrites
- unsafe symlink behavior

---

## 21.4 Deserialization

Treat deserialization as a security boundary.

Use caution with:

- `pickle`
- pickle-backed `joblib`
- unsafe YAML loaders
- arbitrary executable model checkpoints
- Python object deserialization

Prefer safer formats when practical.

---

## 21.5 Subprocesses

When invoking external programs:

- prefer argument arrays
- avoid shell interpolation
- validate dynamic arguments
- avoid `shell=True` unless clearly necessary
- surface useful failures
- avoid exposing secrets through command arguments

---

## 21.6 Network Access

Do not introduce external network calls without a clear requirement.

Network-dependent code should consider:

- timeouts
- retries where appropriate
- size limits
- provenance
- offline behavior
- reproducibility
- explicit failure reporting

Ordinary tests should not depend on live external services unless intentionally classified as network/integration tests.

---

# 22. Error Handling and Observability

Do not silently swallow important errors.

Errors should communicate:

- what failed
- enough relevant context to diagnose it
- what the caller may need to correct

Do not expose:

- credentials
- secrets
- unnecessary sensitive filesystem details

Distinguish:

```text
expected pipeline rejection
```

from:

```text
unexpected software failure
```

---

## 22.1 Failure States

Where project architecture supports explicit states, preserve meaningful distinctions such as:

- accepted
- needs refinement
- rejected
- failed

or the equivalent project-defined representation.

Do not collapse every state into generic success/failure if doing so removes scientifically important information.

---

## 22.2 Logging

Follow existing logging conventions.

Avoid:

- random `print()` calls in core code
- huge image/array dumps
- duplicate exception output
- credentials
- unnecessary private data

Useful logging may include, when appropriate:

- pipeline stage
- input identifier
- matcher
- candidate count
- inlier count
- failure reason
- runtime

Do not add excessive logging inside performance-sensitive loops without reason.

---

# 23. API and Backend Engineering

When modifying APIs:

- inspect the existing framework and contracts first
- validate external input
- preserve explicit request/response schemas
- maintain compatibility where required
- avoid returning internal stack traces
- handle uploaded files safely
- treat expensive CV/ML jobs appropriately
- separate transport logic from scientific/business logic when architecture supports it

Do not invent endpoints from roadmap concepts.

Do not tightly couple backend transport code to one experimental matcher if the project already supports or intends matcher abstraction.

Core registration logic should remain reusable outside a web server where practical.

---

# 24. Frontend and Visualization Engineering

User interfaces must preserve scientific meaning.

Distinguish clearly between:

- candidate matches
- verified inliers
- rejected matches
- actual benchmark values
- example/placeholder values
- accepted registrations
- failed/rejected registrations

Do not display invented confidence percentages as scientific results.

If values are examples, label them clearly as examples.

A visually convincing overlay is not proof of accurate registration.

When presenting scientific results, preserve access to relevant:

- metrics
- units
- failure states
- benchmark provenance where appropriate

---

# 25. Performance and Resource Use

Optimize after identifying an actual bottleneck where practical.

Measure:

```text
Before
vs.
After
```

Do not claim performance improvement without evidence.

Preserve correctness.

A faster implementation with degraded registration quality is not automatically an improvement.

---

## 25.1 Memory

Lunar imagery may be large.

Avoid unnecessary:

- array copies
- full-resolution duplication
- loading entire datasets into memory
- simultaneous retention of unnecessary pyramids
- repeated decoding

Use approaches such as:

- tiling
- chunking
- memory mapping

only when justified by project architecture and measured needs.

Do not prematurely optimize simple code without evidence.

---

## 25.2 Concurrency

If introducing:

- threads
- processes
- asynchronous execution
- GPU concurrency

identify:

- shared state
- resource ownership
- failure propagation
- cancellation behavior
- determinism impact
- reproducibility impact

Do not introduce concurrency solely to make implementation appear more sophisticated.

---

# 26. Research and Benchmark Engineering

Research changes should begin with a clear question.

Where applicable, document:

- hypothesis
- baseline
- dataset
- preprocessing
- configuration
- metrics
- evaluation method
- limitations
- failure examples

Do not promote experimental code into core merely because it works on one example.

---

## 26.1 Benchmark Integrity

Never:

- invent results
- hand-edit benchmark output
- hide failed examples
- cherry-pick successful cases and present them as general performance
- change metric definitions silently
- change test pairs silently
- change preprocessing silently
- compare incompatible experiments without disclosure

Any methodology change affecting comparability must be documented.

---

## 26.2 Metric Changes

Before modifying a metric, determine:

- mathematical definition
- coordinate system
- units
- evaluated population
- invalid-case handling
- whether it measures fit points or independent evaluation
- which historical results depend on it

A metric implementation change may invalidate historical benchmark comparisons.

---

## 26.3 Randomness

Where randomness affects results:

- expose/configure seeds when appropriate
- record seeds in experiments
- avoid accidental nondeterminism where reproducibility matters

Do not claim complete determinism when underlying hardware or libraries do not guarantee it.

---

## 26.4 Benchmark Reproducibility

A meaningful benchmark run should be traceable, where appropriate, to:

- code revision
- configuration
- benchmark dataset/pair manifest
- model/checkpoint
- random seed
- dependency/environment versions
- hardware when runtime matters

---

# 27. Testing and Validation

Tests should validate behavior rather than implementation trivia.

Use the narrowest relevant validation first.

```text
Changed Function
      ↓
Relevant Unit Tests
      ↓
Affected Module Tests
      ↓
Relevant Integration Tests
      ↓
Broader Suite if justified
      ↓
Scientific Benchmark only when necessary
```

Do not run massive scientific datasets for every local code change.

---

## 27.1 Unit Tests

Unit tests may cover:

- preprocessing
- metadata
- geometry helpers
- feature utilities
- transforms
- metric calculations
- validation
- configuration

Prefer deterministic compact inputs.

Avoid overmocking numerical behavior when direct testing is practical.

---

## 27.2 Integration Tests

Integration tests may validate:

- source/reference registration
- retrieval → local matching
- serialization
- CLI workflows
- API workflows
- pipeline configuration

Use compact fixtures where practical.

---

## 27.3 Regression Tests

Bug fixes should include regression tests when practical.

A meaningful regression test should:

1. reproduce the previous failure
2. fail against the defective behavior
3. pass after the fix

Do not add a test that fails to exercise the original bug.

---

## 27.4 Failure Tests

Relevant failure cases may include:

- blank input
- corrupt data
- missing metadata
- unsupported sensor
- empty descriptors
- insufficient matches
- zero geometric inliers
- degenerate transform
- no overlap
- unavailable model
- malformed configuration
- invalid manifest

Failure behavior should be explicit and predictable.

---

## 27.5 Test Commands

Do not invent test commands.

Inspect actual repository tooling and configuration before executing tests.

Relevant evidence may exist in:

- project configuration
- package metadata
- Makefiles
- scripts
- CI definitions

Use repository-supported commands.

---

## 27.6 No Fake Test Claims

Never say:

> All tests pass.

unless relevant tests actually ran and passed.

If tests were not executed, state that clearly.

If testing was blocked, report:

- which validation was attempted
- why execution was blocked
- what remains unverified

---

# 28. CI Engineering

Inspect existing CI before changing it.

Do not invent CI architecture.

For normal CI, prefer:

- reasonably fast checks
- compact fixtures
- deterministic validation
- no unnecessary mission-scale datasets
- no assumed GPU unless configured

Large scientific benchmarks may belong outside ordinary pull-request CI.

---

## 28.1 CI Security

When modifying CI:

- preserve least privilege
- minimize permissions
- protect secrets from untrusted pull requests
- review third-party actions
- avoid unsafe interpolation
- protect publishing credentials

Do not claim these protections already exist unless verified.

---

# 29. Documentation Rules

Documentation must describe actual project behavior accurately.

Do not describe planned functionality as implemented.

Update relevant documentation when changes materially affect:

- installation
- public API
- CLI behavior
- configuration
- pipeline behavior
- metrics
- datasets
- supported sensors
- benchmark methodology
- architecture

Do not update unrelated documentation for small internal changes.

---

## 29.1 Documentation Responsibilities

Keep responsibilities separated.

| Document                   | Responsibility                          |
| -------------------------- | --------------------------------------- |
| `README.md`                | Current project/user overview           |
| `CONTRIBUTING.md`          | Contributor workflow                    |
| `ROADMAP.md`               | Planned work                            |
| `CHANGELOG.md`             | Completed notable changes               |
| `SECURITY.md`              | Security policy                         |
| `CODE_OF_CONDUCT.md`       | Community behavior                      |
| `CITATION.cff`             | Citation metadata                       |
| `AGENTS.md`                | Repository-wide AI-agent rules          |
| `.ai/README.md`            | AI context navigation                   |
| `.ai/ENGINEERING_RULES.md` | Universal detailed engineering behavior |

Do not duplicate complete documents across multiple locations.

---

## 29.2 Changelog

Consider updating `CHANGELOG.md` for notable changes such as:

- new user-facing capabilities
- significant bug fixes
- breaking changes
- benchmark-methodology changes
- sensor-support changes
- major API changes
- security fixes

Do not add every small internal refactor.

---

## 29.3 Roadmap

Do not mark roadmap work complete merely because one component was implemented.

Completion should match the roadmap item's defined scope or exit criteria.

---

## 29.4 AI Documentation

Update `.ai/` documentation when a change materially affects:

- architecture
- terminology
- module ownership
- benchmark methodology
- engineering conventions
- workflow
- testing strategy
- dataset assumptions

Do not update AI context files for every internal implementation detail.

---

# 30. Third-Party Code and Citations

Before adding copied or adapted third-party code:

- identify the source
- verify the license
- confirm compatibility
- preserve required attribution
- avoid unknown-license code

A published research paper does not automatically grant permission to copy an implementation from an arbitrary repository.

When implementing methods substantially based on published work, preserve appropriate references according to repository conventions.

Never invent:

- paper titles
- authors
- DOI values
- publication venues
- citations

---

# 31. Git and Change Safety

Do not casually:

- force-push
- hard-reset
- rewrite history
- delete user branches
- discard unrelated changes
- clean all untracked files
- delete large data trees

Prefer reversible operations.

---

## 31.1 Deletion

Before deleting a:

- file
- class
- function
- configuration key
- dependency
- endpoint

search for references.

Inspect applicable:

- imports
- call sites
- tests
- scripts
- documentation
- configuration
- CI/deployment usage

Absence from one nearby module does not prove something is unused.

---

## 31.2 Renaming

When renaming something, update applicable:

- imports
- call sites
- tests
- documentation
- configuration
- scripts
- result schemas
- APIs

Avoid partial renames.

---

## 31.3 Breaking Changes

Changes to public:

- CLI
- API
- configuration
- result schemas
- serialization
- benchmark definitions

may be breaking.

For intentional breaking changes:

- document the change
- provide migration guidance where appropriate
- update tests
- update relevant documentation
- update the changelog when warranted

---

## 31.4 Backward Compatibility

Do not preserve backward compatibility blindly when it would maintain a serious correctness or design problem.

Also do not break users unnecessarily.

Consider:

- project maturity
- release policy
- public usage
- migration cost
- scientific benefit
- implementation complexity

---

# 32. Refactoring Rules

Before major refactoring:

1. understand current behavior
2. identify public interfaces
3. identify relevant tests
4. identify benchmark implications
5. isolate behavior changes
6. refactor incrementally
7. rerun relevant validation

Do not combine a major structural refactor and experimental algorithm change unless necessary.

---

## 32.1 Formatting

Avoid formatting the entire repository as a side effect of touching one file.

Keep diffs focused.

When large formatter-driven changes are unavoidable, separate formatting-only changes where practical.

---

# 33. Command Execution

Before running commands:

- inspect project tooling
- understand what the command does
- confirm the working directory
- prefer narrow commands
- avoid destructive operations
- understand whether generated files may change

Do not blindly execute commands copied from unrelated documentation.

---

## 33.1 Installation

Do not install packages that modify project dependency state unless necessary for the task.

When installation is required:

- use repository-supported tooling
- avoid arbitrary global installation
- avoid unintended lockfile changes
- review dependency changes

---

# 34. Completion Validation

Before considering work complete, verify:

- requested behavior is implemented
- relevant code path was exercised
- tests passed or limitations are documented
- compatibility impact is understood
- scientific rules remain valid
- benchmark comparability is preserved or documented
- security concerns are addressed
- documentation is updated where necessary
- no secrets were introduced
- no unrelated changes were added
- reported claims match actual validation

---

## 34.1 Final Diff Review

Review the complete affected diff before completion.

Check for:

- debug prints
- temporary files
- accidental formatting
- secrets
- unintended TODOs
- commented-out code
- unused imports
- accidental benchmark changes
- accidental generated-result changes
- incorrect documentation
- duplicate logic
- leftover experiment paths

---

# 35. Blocked Tasks

If work is blocked by:

- missing dataset
- unavailable GPU
- unavailable model weights
- missing credentials
- unavailable external system
- unresolved requirement ambiguity

do not pretend the task is fully complete.

Where useful, complete the safe portion of the task and clearly identify the blocker and remaining validation.

---

# 36. Engineering Reports

Agent reports should be factual and concise.

Prefer reporting:

- what changed
- files affected
- validation performed
- limitations
- unresolved risks

Avoid:

- exaggerated certainty
- unsupported claims
- unnecessary prose

---

## 36.1 No Fake Execution

Never claim:

> I tested this.

> The build succeeded.

> The benchmark improved.

> Deployment succeeded.

unless those actions actually occurred and their results were observed.

---

## 36.2 No Fake Inspection

Never claim:

> I inspected the tests.

> I checked the README.

> There is no existing helper.

unless those actions were actually performed.

---

## 36.3 Failure Reporting

If something fails during implementation, do not hide it.

Report:

- attempted command/action
- failure
- relevant error
- impact
- reasonable next step

---

## 36.4 Evidence-Based Claims

Keep these categories separate:

- **Known from repository**
- **Measured during execution**
- **Inferred from implementation**
- **Proposed recommendation**

Do not blur them.

---

## 36.5 Scientific Claim Discipline

Do not describe ChandraMap or an algorithm as:

- state-of-the-art
- best
- highest accuracy
- fully invariant
- universally robust

without strong evidence and appropriate comparison.

---

# 37. Prohibited Agent Behaviors

Agents must not:

- invent repository structure
- fabricate files, APIs, commands, or dependencies
- fabricate tests or benchmark results
- claim execution that did not occur
- claim inspection that did not occur
- overwrite user work casually
- run destructive Git operations without justification
- commit secrets
- log secrets
- add unnecessary dependencies
- silently alter benchmark methodology
- silently redefine metrics
- hide failed benchmark cases
- hand-edit result metrics
- treat planned functionality as implemented
- treat experimental functionality as stable without evidence
- treat IIRS as ordinary grayscale data without a defined representation
- claim interpolation recovers missing spatial detail
- call candidate matches verified before geometric verification
- skip final transform refitting without an intentional documented reason
- present fitting-point residuals as independent validation
- confuse benchmark versions with software releases
- blindly use Earth-centric CRS assumptions for lunar data
- change public schemas without inspecting consumers
- add large mission datasets casually
- introduce major architecture changes for small tasks
- suppress exceptions merely to make tests pass
- disable tests instead of fixing the underlying problem
- invent performance improvements
- describe visually convincing overlays as proof of registration accuracy

---

# 38. Before-Change Checklist

Before modifying the repository:

- [ ] I understand the requested behavior.
- [ ] I identified the affected project area.
- [ ] I loaded only the relevant context.
- [ ] I inspected nearby implementation.
- [ ] I inspected relevant tests.
- [ ] I inspected applicable configuration.
- [ ] I searched for existing utilities and patterns.
- [ ] I understand compatibility impact.
- [ ] I understand scientific impact where applicable.
- [ ] I understand benchmark impact where applicable.
- [ ] I understand security implications where applicable.
- [ ] I am not relying on unverified repository assumptions.
- [ ] I am not about to overwrite unrelated user work.

---

# 39. Completion Checklist

Before declaring the task complete:

- [ ] The change solves the requested requirement.
- [ ] The diff is focused.
- [ ] Relevant tests were executed where possible.
- [ ] Test limitations are disclosed.
- [ ] Scientific behavior remains valid.
- [ ] Benchmark comparability is preserved or explicitly documented.
- [ ] Public contracts remain compatible unless intentionally changed.
- [ ] Configuration changes are documented where necessary.
- [ ] Documentation is updated where necessary.
- [ ] No secrets were introduced.
- [ ] No unrelated user work was overwritten.
- [ ] No benchmark or result values were fabricated.
- [ ] No planned features were described as implemented.
- [ ] No experimental feature was presented as stable without evidence.
- [ ] Generated artifacts were not manually falsified.
- [ ] The final affected diff was reviewed.
- [ ] Completion claims match actual validation.

---

# 40. Final Engineering Principle

For every ChandraMap engineering task:

```text
Understand
    ↓
Inspect
    ↓
Reuse
    ↓
Change Minimally
    ↓
Validate
    ↓
Measure
    ↓
Document When Necessary
    ↓
Report Truthfully
```

The purpose of these rules is not to make agents conservative for its own sake.

They exist to ensure that ChandraMap evolves through **correct, scientifically defensible, reproducible, secure, and reviewable engineering changes** rather than through assumptions, unnecessary rewrites, or unverifiable claims.

<!-- Source specification: :contentReference[oaicite:0]{index=0} -->
