# ChandraMap AI Context

The `.ai/` directory is ChandraMap's structured context layer for AI coding agents, autonomous software-engineering assistants, repository-aware LLM tools, AI-assisted code review, testing, documentation, and research engineering.

Its purpose is to help an agent understand **only the parts of the project required for the current task** without repeatedly loading the entire repository or duplicating human-facing documentation.

> **Core principle: load only the context required for the task.**

The normal entry path is:

```text
Task
  ↓
AGENTS.md
  ↓
.ai/README.md
  ↓
Relevant .ai/ document(s)
  ↓
Relevant implementation
  ↓
Relevant tests / configuration
  ↓
Expand context only when necessary
```

Correctness has higher priority than context or token reduction.

---

## Purpose

The `.ai/` directory provides structured context for areas such as:

- project purpose and scope
- lunar and remote-sensing domain knowledge
- canonical terminology
- datasets and sensors
- system architecture
- scientific pipeline design
- module ownership
- engineering conventions
- testing and validation
- benchmark methodology
- research experiments
- reproducibility
- task-specific workflows
- AI-specific security concerns

This `README.md` is the **navigation and orientation layer** for those documents.

It should help an agent answer:

1. What is the `.ai/` directory?
2. Which document should I read for this task?
3. Which documents can I safely ignore?
4. Where does implementation truth live?
5. What project facts must not be assumed?
6. How should conflicts between documentation and implementation be handled?
7. When should more context be loaded?
8. Where should new AI-specific documentation belong?

This file is not intended to reproduce the full contents of the documents it indexes.

---

## How to Use This Directory

### Quick Start

For most engineering tasks:

1. Read the root `AGENTS.md`.
2. Read this `.ai/README.md`.
3. Classify the task.
4. Load only the relevant `.ai/` documents.
5. Inspect the affected implementation.
6. Inspect nearby tests and configuration.
7. Search for existing utilities and patterns.
8. Make the smallest correct change.
9. Run the narrowest relevant validation first.
10. Expand validation or documentation only when required.

---

### Progressive Context Loading

Use progressively deeper context instead of loading everything at once.

```text
Task
  ↓
Repository-wide rules
  ↓
Task-specific AI context
  ↓
Affected code
  ↓
Relevant tests
  ↓
Relevant configuration
  ↓
Additional architecture / domain / benchmark context if needed
```

Context depth should scale with:

- task scope
- scientific risk
- architectural impact
- benchmark impact
- compatibility impact
- security impact

Use the following priority when deciding whether more context is necessary:

```text
Correctness
    ↓
Scientific validity
    ↓
Project consistency
    ↓
Reproducibility
    ↓
Security
    ↓
Maintainability
    ↓
Minimal change scope
    ↓
Context / token efficiency
```

Do not omit necessary context merely to reduce token usage.

---

### Do Not Load Everything

Do not treat `.ai/` as a directory that must be fully loaded before every task.

Examples:

A documentation typo does not require:

- every benchmark specification
- every architecture document
- all source code
- all sensor documentation

A small frontend style change should not require detailed lunar imaging context.

A benchmark metric change may require:

- metric definitions
- benchmark rules
- reproducibility guidance
- evaluation implementation
- relevant tests
- compatibility with historical results

The correct target is:

> **minimum sufficient context**

not minimum context at any cost.

---

## Relationship with `AGENTS.md`

`AGENTS.md` and `.ai/README.md` have different responsibilities.

```text
AGENTS.md
    ↓
Repository-wide mandatory agent rules

.ai/README.md
    ↓
Navigation and task-to-context routing

Detailed .ai/ documents
    ↓
Specialized context and instructions
```

### Root `AGENTS.md`

`AGENTS.md` contains repository-wide rules that generally apply regardless of task type.

These may include:

- understand before modifying
- do not invent repository facts
- preserve scientific validity
- make focused changes
- preserve compatibility
- protect user work
- test appropriately
- protect secrets
- preserve benchmark integrity
- never fabricate execution or results

Detailed `.ai/` documents supplement these rules; they do not replace them.

### `.ai/README.md`

This file answers:

> **Where should the agent look next?**

It is primarily a:

- navigation layer
- context-loading guide
- AI documentation index

### Detailed `.ai/` Documents

Detailed files provide specialized context for subjects such as:

- project intent
- lunar domain knowledge
- architecture
- datasets
- benchmarks
- testing
- experiments
- reproducibility
- workflows

Load them selectively.

---

## Documentation Map

The repository itself is the source of truth for which files currently exist.

The paths below describe the intended responsibility of ChandraMap's structured AI documentation. Before opening or linking to a file, verify that it exists in the current checkout.

| Area                   | Purpose                                                       | Read When                                   |
| ---------------------- | ------------------------------------------------------------- | ------------------------------------------- |
| `ENGINEERING_RULES.md` | Detailed universal engineering rules for AI agents            | Most code-changing tasks                    |
| `context/`             | Project, domain, terminology, and dataset context             | Domain-sensitive or project-wide work       |
| `architecture/`        | System, pipeline, module ownership, and data flow             | Architectural or cross-module work          |
| `development/`         | Coding, testing, failure, configuration, and dependency rules | Implementation and validation               |
| `benchmarks/`          | Benchmark configurations and metric definitions               | Benchmark-affecting work                    |
| `research/`            | Research discipline, experiments, and reproducibility         | Research and model/matcher experiments      |
| `workflows/`           | Task-specific operational procedures                          | Features, bugs, refactors, benchmarks, docs |
| `security/`            | AI-specific secure-engineering guidance                       | Security-sensitive engineering              |

Do not assume a path exists merely because it is documented as part of the intended structure.

---

## Core Context Documents

### `ENGINEERING_RULES.md`

When present, `ENGINEERING_RULES.md` is the detailed engineering rulebook for AI software agents.

Typical subjects include:

- understand before modifying
- requirement analysis
- progressive context loading
- inspect relevant repository evidence
- reuse existing architecture
- minimal changes
- compatibility
- dependency discipline
- testing
- validation
- documentation
- security
- truthful task reporting

It should normally be one of the first `.ai/` documents loaded for implementation work.

It supplements the root `AGENTS.md`; it does not replace it.

---

### `context/PROJECT_CONTEXT.md`

Purpose:

> Explain what ChandraMap is, why it exists, its scope, and how its major concepts fit together.

It may describe:

- project mission
- goals
- scope
- major components
- benchmark philosophy
- repository purpose
- current development direction

Read it when:

- starting substantial project work
- changing multiple subsystems
- project intent is unclear
- designing a major capability
- evaluating whether work belongs in project scope

A small isolated bug fix should not automatically require it.

---

### `context/DOMAIN_CONTEXT.md`

Purpose:

> Explain lunar, computer-vision, remote-sensing, and planetary-registration concepts needed to modify ChandraMap correctly.

It may cover:

- lunar illumination
- Sun-angle differences
- sensor modality differences
- ground sampling distance
- scale mismatch
- terrain correspondence
- planetary registration
- geometric verification
- geospatial constraints

Read it for:

- computer-vision work
- geospatial work
- sensor preprocessing
- registration algorithms
- scale handling
- illumination handling
- scientific evaluation
- new sensor support

---

### `context/TERMINOLOGY.md`

Purpose:

> Define canonical ChandraMap vocabulary.

Important distinctions may include:

- correspondence
- candidate match
- verified inlier
- registration
- source image
- reference image
- tie point
- fit point
- check point
- GSD
- RMSE
- RANSAC
- coverage
- retrieval
- global descriptor
- local descriptor
- sub-pixel refinement

Read it when:

- naming APIs
- designing result schemas
- writing documentation
- defining metrics
- terminology is ambiguous

Use canonical project terms instead of creating competing terminology.

---

### `context/DATASETS.md`

Purpose:

> Describe project datasets, sensor roles, expected metadata, provenance, storage expectations, and data-handling constraints.

Relevant data may include:

- Chandrayaan-2 OHRC
- Chandrayaan-2 TMC-2
- Chandrayaan-2 IIRS
- LRO NAC
- LRO WAC
- Kaguya / SELENE products
- DEM / DTM products
- LOLA-derived products
- synthetic lunar augmentations

Read it before modifying:

- dataset ingestion
- sensor routing
- file readers
- preprocessing
- benchmark data
- metadata handling
- dataset download tooling

Do not infer product formats, licenses, metadata fields, or local availability without repository evidence.

---

## Architecture Context

### `architecture/SYSTEM_OVERVIEW.md`

Purpose:

> Describe ChandraMap's high-level architecture and major subsystem responsibilities.

Read it for:

- major feature implementation
- cross-module changes
- backend integration
- architecture reviews
- service boundaries
- significant structural changes

---

### `architecture/PIPELINE.md`

Purpose:

> Define ChandraMap's scientific and technical processing pipeline.

The project may conceptually involve stages such as:

```text
Input
  ↓
Metadata
  ↓
Sensor Routing
  ↓
Preprocessing
  ↓
Scale Handling
  ↓
Retrieval / Candidate Search
  ↓
Local Matching
  ↓
Geometric Verification
  ↓
Tie-Point Refinement
  ↓
Final Transform Refit
  ↓
Registration
  ↓
Evaluation
  ↓
Accept / Refine / Reject
  ↓
Output
```

This sequence is orientation, not evidence that every stage is currently implemented.

Read the actual pipeline documentation before changing:

- pipeline ordering
- stage responsibilities
- matching logic
- retrieval
- geometric verification
- refinement
- registration
- quality gates

---

### `architecture/MODULE_MAP.md`

Purpose:

> Map architectural responsibilities to repository modules.

Use it to determine:

- where implementation belongs
- which module owns a responsibility
- dependency direction
- reusable components
- stable versus experimental boundaries

Do not place code in a directory merely because its name appears appropriate.

Verify ownership.

---

### `architecture/DATA_FLOW.md`

Purpose:

> Explain how information moves through the system.

It may describe:

- image inputs
- metadata
- descriptors
- candidate matches
- verified inliers
- transforms
- metrics
- result objects
- artifacts

Read it when changing:

- interfaces
- serialization
- backend jobs
- pipeline boundaries
- result contracts
- data representations

---

## Development Context

### `development/CODING_RULES.md`

Purpose:

> Define implementation conventions beyond the universal repository rules.

It may cover:

- language conventions
- naming
- typing
- module boundaries
- numerical code
- comments
- docstrings
- configuration use
- error handling

Actual repository tooling remains authoritative for formatting, linting, type checking, and package management.

---

### `development/TESTING_RULES.md`

Purpose:

> Define ChandraMap's testing and validation strategy.

Potential categories include:

- unit tests
- integration tests
- regression tests
- failure tests
- scientific benchmark tests
- fixtures

Read it whenever implementation changes require validation.

---

### `development/ERROR_HANDLING.md`

Purpose:

> Define how failures and rejected processing states are represented.

It may cover:

- validation failures
- exception hierarchy
- unsupported inputs
- pipeline rejection
- registration failure
- API error mapping

This is especially important because an unreliable registration should be rejectable instead of always producing a successful-looking result.

---

### `development/CONFIGURATION.md`

Purpose:

> Define project configuration conventions.

It may describe:

- configuration locations
- configuration schemas
- defaults
- overrides
- experiment parameters
- environment-variable usage

Inspect existing configuration before creating new keys.

---

### `development/DEPENDENCIES.md`

Purpose:

> Define dependency policy.

It may cover:

- criteria for new dependencies
- optional AI/ML packages
- GPU dependencies
- geospatial/native packages
- licensing
- reproducibility
- platform impact

Read it before adding or replacing dependencies.

---

## Benchmark Context

ChandraMap uses benchmark-oriented development.

Benchmark configurations are conceptually different from Git tags or software releases.

```text
Benchmark V1 ≠ software release v1.0.0
Benchmark V2 ≠ software release v2.0.0
Benchmark V3 ≠ software release v3.0.0
Benchmark V4 ≠ software release v4.0.0
```

Never infer release numbering from benchmark numbering.

Do not claim that a benchmark configuration is implemented, complete, or validated unless repository evidence supports that status.

---

### Benchmark V1 — Classical Baseline

V1 is intended as a simple, reproducible, explainable baseline.

Potential concepts include:

- SIFT / RootSIFT
- descriptor matching
- match filtering
- RANSAC
- affine or homography estimation
- registration
- baseline metrics

Its role is to provide a controlled reference point for later benchmark configurations.

---

### Benchmark V2 — Sensor-Aware and Multi-Scale

V2 may introduce:

- OHRC-specific preprocessing
- TMC-2-specific preprocessing
- IIRS-derived registration representations
- multi-scale search
- reference pyramids
- physically meaningful scale handling
- structural preprocessing
- illumination stress handling
- better spatial coverage evaluation

---

### Benchmark V3 — Advanced Matching and Retrieval

V3 may introduce:

- ALIKED + LightGlue
- LoFTR
- remote-sensing matching approaches
- global descriptor retrieval
- Top-K reference candidate search
- vector indexing such as FAISS

Global retrieval and local matching are different tasks.

```text
Global Retrieval
→ find likely lunar region

Local Matching
→ establish image correspondences within candidate region
```

FAISS performs vector similarity search/indexing.

It does not itself extract image descriptors.

---

### Benchmark V4 — Research-Grade Robustness

V4 may explore:

- improved IIRS handling
- local or piecewise geometry
- DEM-aware processing
- uncertainty estimation
- confidence calibration
- quality gates
- accept / refine / reject behavior
- failure classification
- matcher selection
- scalable retrieval

"Advanced" does not automatically mean "add more neural networks."

Research additions should address an identified weakness or explicit question.

---

### Benchmark Metrics

Where a metric-definition document exists, treat it as required context for metric or evaluation changes.

Relevant metrics may include:

- candidate match count
- inlier count
- inlier ratio
- reprojection residuals
- independent check-point RMSE
- source-image pixel error
- grid coverage
- convex-hull coverage
- Recall@1
- Recall@5
- runtime
- success rate
- failure rate

Changing a metric definition may invalidate comparison with historical results.

Do not change metric semantics silently.

---

## Research Context

Research tasks require additional discipline beyond ordinary feature work.

Where research documentation exists, use it for:

- matcher experiments
- preprocessing experiments
- retrieval experiments
- sensor studies
- model comparisons
- benchmark studies
- metric research

A meaningful experiment should identify, where applicable:

- research question
- hypothesis
- baseline
- dataset
- configuration
- metrics
- evaluation method
- limitations
- failure examples
- reproducibility information

Do not promote a research prototype directly into stable core implementation merely because one experiment produced a good result.

---

### Reproducibility

Load reproducibility guidance when changing:

- benchmark methodology
- experiments
- datasets
- metrics
- learned models
- checkpoints
- performance claims

Relevant information may include:

- random seeds
- configuration
- dataset manifest
- environment
- dependency versions
- model version
- checkpoint identity
- hardware
- code revision
- result location

Do not invent missing reproducibility metadata.

---

## Workflow Context

Where task-specific workflow documentation exists, load only the workflow relevant to the current task.

Typical workflow responsibilities may include:

| Workflow Type | Purpose                                             |
| ------------- | --------------------------------------------------- |
| Feature       | Safely introduce new behavior                       |
| Bug fix       | Diagnose, correct, and prevent regression           |
| Refactor      | Restructure while preserving intended behavior      |
| Benchmark     | Perform controlled scientific comparison            |
| Documentation | Update documentation without changing project truth |

Do not load every workflow document for every change.

---

## Security Context

AI-specific security documentation supplements root `SECURITY.md`; it does not replace it.

AI-assisted engineering may interact with potentially untrusted:

- user files
- scientific rasters
- archives
- PDS products
- hyperspectral data
- model checkpoints
- serialized objects
- configuration files
- external downloads

Security-sensitive work may need additional context covering:

- unsafe deserialization
- archive extraction
- path handling
- model checkpoints
- subprocess execution
- external downloads
- secrets
- dependency additions

Vulnerability reporting and repository security policy remain the responsibility of root `SECURITY.md`.

---

## Task → Context Guide

The paths below should be used only when they exist in the current repository.

| Task                          | Recommended Context                                                                   |
| ----------------------------- | ------------------------------------------------------------------------------------- |
| Small Python bug fix          | `AGENTS.md`, engineering rules, affected code, nearby tests                           |
| New feature                   | Engineering rules, relevant architecture/module context, affected code/tests          |
| Registration algorithm change | Domain context, pipeline, benchmark context, metrics, implementation, tests           |
| RANSAC / geometry change      | Domain context, pipeline, relevant benchmark and metric definitions, geometry tests   |
| Dataset loader                | Dataset context, domain context, module ownership, configuration, tests               |
| IIRS preprocessing            | Domain context, datasets, pipeline, relevant benchmark/research context               |
| OHRC / TMC-2 preprocessing    | Domain context, datasets, pipeline, benchmark context                                 |
| Matcher experiment            | Research rules, experiment guidance, benchmark definitions, metrics, reproducibility  |
| Model/checkpoint change       | Domain context, dependencies, research rules, reproducibility, benchmark context      |
| Global retrieval              | Pipeline, architecture, relevant benchmark context, metrics, retrieval implementation |
| Benchmark metric change       | Benchmark overview, metrics, reproducibility, evaluation code/tests                   |
| New benchmark configuration   | Benchmark overview, metrics, pipeline, reproducibility, relevant implementation       |
| Backend/API change            | System overview, module map, data flow, contracts/configuration, tests                |
| Frontend visualization        | Data flow, terminology, result contracts, relevant API behavior                       |
| Configuration change          | Configuration rules, affected subsystem documentation, tests                          |
| Dependency change             | Dependency policy, affected subsystem, actual dependency files                        |
| Refactor                      | System overview, module map, affected code/tests, relevant workflow                   |
| Security-sensitive change     | `AGENTS.md`, root `SECURITY.md`, applicable security/development guidance             |
| CI/CD change                  | Engineering rules and actual CI/deployment configuration                              |
| Documentation change          | Terminology and authoritative source documentation                                    |
| New sensor support            | Project context, domain context, datasets, pipeline, architecture, benchmarks         |
| New research experiment       | Research rules, experiment guidance, reproducibility, metrics, benchmark context      |
| Performance claim             | Benchmark definitions, metrics, reproducibility, actual measured results              |

Use the table as context-routing guidance, not as evidence that every referenced file currently exists.

---

## Minimum-Context Examples

### Example 1 — Empty SIFT Descriptors

Task:

> Fix SIFT matcher crashing when descriptors are empty.

Load:

1. root `AGENTS.md`
2. engineering rules if present
3. affected matcher implementation
4. nearby matcher tests
5. error-handling rules if the failure contract is affected

Do not automatically load:

- all benchmark specifications
- complete sensor documentation
- frontend documentation
- deployment configuration

Expand context only if the implementation reveals a broader dependency.

---

### Example 2 — IIRS PCA Experiment

Task:

> Add an IIRS PCA preprocessing experiment.

Load:

1. root `AGENTS.md`
2. engineering rules
3. domain context
4. dataset context
5. pipeline architecture
6. relevant benchmark context
7. research/reproducibility guidance
8. current preprocessing implementation
9. relevant tests and configuration

This task requires domain context because IIRS is hyperspectral/imaging-infrared data rather than an ordinary grayscale camera product.

---

### Example 3 — RMSE Definition Change

Task:

> Change the benchmark RMSE definition.

Load:

1. root `AGENTS.md`
2. benchmark overview
3. metric definitions
4. reproducibility rules
5. evaluation implementation
6. relevant tests
7. result compatibility considerations
8. changelog requirements where applicable

A metric-definition change can make previous benchmark values incomparable.

Treat it as a benchmark methodology change, not merely an internal numerical refactor.

---

### Example 4 — Documentation Typo

Task:

> Correct a terminology error in one documentation file.

Load:

1. affected documentation
2. canonical terminology reference if the term is domain-specific

Do not load every source module or benchmark document unless the correction depends on them.

---

## Major-Change Context

Large architectural changes justify broader context.

For example, redesigning the registration architecture may require:

- project context
- domain context
- system overview
- pipeline
- module map
- data flow
- benchmark definitions
- metric definitions
- testing rules
- configuration rules
- affected code
- affected interfaces
- affected tests

Context depth should scale with change scope.

The minimum-context principle must never be used as a reason to skip information necessary for correctness.

---

## When to Expand Context

Load additional documentation when encountering:

- unclear architecture
- undocumented interfaces
- conflicting documentation
- unclear module ownership
- benchmark implications
- scientific assumptions
- security implications
- cross-module dependencies
- public API changes
- dataset or sensor ambiguity
- unexpected test failures
- unclear configuration behavior
- serialized result changes

Do not guess through these situations.

---

## When to Stop Loading Context

An agent generally has sufficient context when it can explain:

- what must change
- where the change belongs
- what behavior must remain
- which rules apply
- which tests validate the change
- whether configuration changes are required
- whether public interfaces are affected
- whether benchmark comparability is affected
- whether documentation must change

Once these questions can be answered using repository evidence, unrelated context should not be loaded.

---

## Core Project Orientation

ChandraMap is a lunar image correspondence and registration project.

Its core purpose is to make observations of the same lunar terrain comparable across different sensors and acquisition conditions, find reliable matching points, verify their geometry, register imagery, and quantify result quality.

Core scientific priorities include:

- correctness
- measurable accuracy
- sensor-aware processing
- physically meaningful scale handling
- geometric verification
- reproducibility
- well-distributed correspondences
- explicit failure reporting

Mosaics, lunar globes, and map interfaces are downstream demonstrations.

A visually convincing overlay does not by itself prove scientifically correct registration.

---

## Scientific Rules at a Glance

This section is intentionally short.

Detailed methodology belongs in domain, pipeline, benchmark, and metric documentation.

### Sensor Modality Matters

OHRC, TMC-2, and IIRS are not interchangeable image sources.

- **OHRC** provides very high-resolution panchromatic lunar imagery.
- **TMC-2** provides panchromatic terrain imagery at a substantially coarser ground scale.
- **IIRS** is hyperspectral/imaging-infrared data and should not automatically be treated as an ordinary grayscale image.

Use **TMC-2** when referring to the Chandrayaan-2 Terrain Mapping Camera-2.

---

### Upsampling Does Not Recover Detail

Increasing the number of image pixels does not recover physical spatial information absent from the source measurement.

Use scientifically meaningful scale handling such as:

- reference pyramids
- downsampling higher-resolution data
- comparable effective ground scales
- coarse-to-fine matching

---

### Sun-Angle Differences Are Geometric

Different lunar Sun angles change:

- shadow placement
- shadow length
- local appearance
- apparent terrain structure

Brightness normalization alone does not make differently illuminated observations equivalent.

---

### Candidate Matches Are Not Verified Inliers

Maintain the distinction:

```text
Candidate Matches
        ↓
Geometric Verification
        ↓
Verified Inliers
```

Matcher confidence alone is not geometric verification.

---

### Geometry and Refinement Order

A suitable high-level order is:

```text
Candidate Matches
        ↓
RANSAC / Initial Model
        ↓
Verified Inliers
        ↓
Sub-Pixel Refinement
        ↓
Final Transform Refit
        ↓
Registration
        ↓
Independent Evaluation
```

Do not alter this logic casually without scientific justification and benchmark evidence.

---

### Match Count Is Not Enough

More inliers do not automatically imply better registration.

Spatial distribution matters.

A large cluster around one feature can provide weaker geometric support than fewer well-distributed points.

---

### Fit Error Is Not Independent Accuracy

Error calculated only on points used to fit the transformation should not automatically be described as independent registration accuracy.

Use independent check points or appropriate ground truth when available.

---

### Learned Matchers Require Evidence

A pretrained terrestrial matcher is not automatically:

- lunar invariant
- scale invariant
- illumination invariant
- cross-modality robust

Benchmark it under the conditions for which claims are made.

---

### Failure Must Be Representable

An unreliable registration should be rejectable.

Do not force every input into an apparent success state.

---

## Scientific Terminology

Use the canonical terminology document when present.

Important distinctions include:

```text
candidate matches
vs.
verified inliers
```

```text
source image
vs.
reference image
```

```text
fit points
vs.
check points
```

```text
pixel error
vs.
ground error
```

```text
global retrieval
vs.
local registration
```

```text
global descriptor
vs.
local descriptor
```

```text
benchmark version
vs.
software release version
```

Do not redefine the full project vocabulary inside this README.

---

## Benchmark Integrity

Load applicable benchmark documentation whenever a task affects:

- preprocessing
- sensor routing
- scale handling
- reference pyramids
- retrieval
- local matching
- RANSAC
- geometry
- sub-pixel refinement
- transform estimation
- metrics
- reference data
- model configuration
- benchmark image-pair definitions
- quality gates

Changes affecting benchmark comparability require explicit documentation.

Agents must not:

- invent benchmark values
- manually improve stored numerical results
- hide failed examples
- silently redefine metrics
- silently redefine benchmark datasets
- present fitting residuals as independent validation
- compare incompatible benchmark setups as if they were equivalent

---

## Documentation Responsibilities

### `AGENTS.md`

Repository-wide operating rules for AI coding agents.

Contains rules that should remain applicable across task categories.

---

### `.ai/`

Provides:

- condensed context
- task routing
- AI-oriented project knowledge
- specialized engineering constraints
- research and benchmark guidance

It should not become a duplicate of every human-facing document.

---

### `docs/`

`docs/` is primarily detailed project documentation for:

- users
- contributors
- maintainers
- researchers
- developers

It may contain:

- project specifications
- architecture documentation
- pipeline documentation
- benchmark documentation
- design decisions
- API documentation
- scientific explanations

The `.ai/` layer should reference or summarize essential constraints rather than reproduce entire `docs/` files.

---

### Tool-Specific Files

The repository may contain tool-specific instructions such as `CLAUDE.md` or equivalents.

Do not assume such files exist.

When they do:

- shared project truth should remain tool-independent where practical
- tool-specific files should focus on tool-specific behavior
- scientific and architectural truth should not diverge between agent systems

---

## Root Documentation Map

| File / Area          | Responsibility                                              |
| -------------------- | ----------------------------------------------------------- |
| `README.md`          | User-facing project overview, installation, and usage       |
| `AGENTS.md`          | Repository-wide AI-agent rules                              |
| `.ai/README.md`      | AI-context navigation and routing                           |
| `CONTRIBUTING.md`    | Contributor workflow and development expectations           |
| `ROADMAP.md`         | Planned project and research direction                      |
| `CHANGELOG.md`       | Completed notable changes                                   |
| `SECURITY.md`        | Security policy and vulnerability handling                  |
| `CODE_OF_CONDUCT.md` | Community behavior expectations                             |
| `CITATION.cff`       | Software citation metadata                                  |
| `LICENSE`            | Legal permissions and conditions                            |
| `docs/`              | Detailed human-readable project and technical documentation |
| `.ai/`               | Structured AI-oriented context and guidance                 |

Verify that a file exists before depending on it.

---

## Source of Truth

No single source should be used mechanically to resolve every repository question.

A useful investigation order is:

```text
Current Human Task
        ↓
Applicable Repository Policies
        ↓
AGENTS.md
        ↓
Applicable .ai/ Context
        ↓
Current Project Documentation
        ↓
Actual Repository Configuration
        ↓
Current Implementation
        ↓
Tests / Executable Expectations
```

Use this as an investigation guide, not an absolute precedence algorithm.

For example:

- `ROADMAP.md` describes planned work.
- architecture documents may describe intended design.
- implementation shows current code behavior.
- tests may define expected behavior.
- configuration determines actual tooling.

If these conflict materially, investigate rather than silently choosing one.

---

## Implemented vs Documented vs Planned

Always distinguish implementation status.

| Status           | Meaning                                                                       |
| ---------------- | ----------------------------------------------------------------------------- |
| **Implemented**  | Supported by current repository code and behavior                             |
| **Documented**   | Described in documentation; implementation status still requires verification |
| **Planned**      | Intended future work described in roadmap/design material                     |
| **Experimental** | Present as research or prototype behavior without stable guarantees           |
| **Proposed**     | An idea or design not yet implemented                                         |

Do not describe planned work as implemented.

Do not describe an experiment as a stable supported feature without repository evidence.

`ROADMAP.md` is not proof that functionality currently exists.

---

## Handling Conflicts

If two `.ai/` documents conflict:

1. determine which is more specific to the affected area
2. check whether one clearly supersedes the other
3. inspect relevant implementation
4. inspect relevant tests/configuration
5. avoid combining contradictory instructions silently
6. surface the inconsistency when it affects correctness

If code and documentation disagree, investigate whether:

- the documentation is stale
- the implementation is incomplete
- the code contains a regression
- the documentation describes future behavior
- tests clarify intended current behavior

Do not automatically assume either side is correct.

---

## Repository Discovery

Inspect the actual repository before relying on development-tool assumptions.

Before assuming a:

- package manager
- dependency command
- test runner
- linter
- formatter
- type checker
- framework
- environment variable
- module path
- service
- database
- deployment target
- CI/CD system

inspect relevant repository evidence.

Potential configuration files may include:

- `pyproject.toml`
- `package.json`
- `Makefile`
- Docker files
- CI workflow files
- configuration directories

Their presence and contents must still be verified.

Do not invent commands or tooling.

---

## No-Fabrication Rule

AI agents must never fabricate:

- files
- directories
- functions
- classes
- APIs
- endpoints
- commands
- package-manager usage
- configuration keys
- environment variables
- dependency versions
- test results
- benchmark values
- dataset products
- model checkpoints
- release versions
- repository state
- services
- databases
- deployment platforms
- completed roadmap items

If information is unknown:

1. inspect or search the repository, or
2. state that it could not be verified.

Do not replace missing evidence with confident assumptions.

---

## Context Efficiency Rules

Efficient context loading means reading **less irrelevant information**, not avoiding information required for correctness.

Good:

```text
Matcher Bug
    ↓
Engineering Rules
    ↓
Matcher Implementation
    ↓
Matcher Tests
    ↓
Relevant Error Contract
```

Bad:

```text
Matcher Bug
    ↓
Read Every Source File
    ↓
Read Every Benchmark
    ↓
Read Every Research Document
    ↓
Read Every Frontend File
```

Also bad:

```text
Matcher Bug
    ↓
Edit Immediately
    ↓
No Test Inspection
    ↓
No Repository Evidence
```

The goal is minimum sufficient context.

---

## Updating `.ai/` Documentation

AI context should evolve when repository changes materially affect:

- system architecture
- pipeline behavior
- module ownership
- terminology
- supported datasets
- sensor handling
- benchmark methodology
- metric definitions
- configuration conventions
- testing strategy
- development rules
- reproducibility requirements
- workflow structure

Do not update `.ai/` for every small implementation detail.

AI documentation should capture stable context, rules, and navigation—not mirror every line of code.

---

## Adding New `.ai/` Files

Before creating a new AI-specific document, determine:

1. Does the information already exist?
2. Should an existing `.ai/` document be updated instead?
3. Does the information belong in `docs/` rather than `.ai/`?
4. Is the subject stable enough for a dedicated file?
5. Does the proposed document have one clear responsibility?
6. Will it reduce ambiguity instead of creating overlapping sources?

Avoid creating many small files with duplicated responsibilities.

Prefer clear ownership.

Typical responsibility boundaries may look like:

| Document             | Responsibility                     |
| -------------------- | ---------------------------------- |
| `PROJECT_CONTEXT.md` | Project purpose and scope          |
| `DOMAIN_CONTEXT.md`  | Lunar and scientific domain        |
| `TERMINOLOGY.md`     | Canonical vocabulary               |
| `DATASETS.md`        | Data sources and product roles     |
| `SYSTEM_OVERVIEW.md` | High-level architecture            |
| `PIPELINE.md`        | Processing sequence                |
| `MODULE_MAP.md`      | Repository/module responsibilities |
| `TESTING_RULES.md`   | Validation expectations            |
| `METRICS.md`         | Metric definitions                 |
| `REPRODUCIBILITY.md` | Experiment reproducibility         |

Do not repeat the same detailed rule across many documents.

---

## AI Documentation Quality

Documents under `.ai/` should be:

- concise
- factual
- structured
- task-oriented
- easy to search
- stable
- explicit about assumptions
- useful to both agents and maintainers

Avoid:

- marketing language
- decorative prose
- excessive emoji
- unnecessary project history
- vague advice
- repeated project summaries
- duplicated human documentation
- fake implementation status
- obsolete architecture
- assumed tooling
- unsupported scientific claims

---

## Link Style

Prefer relative repository links **after confirming the target file exists**.

For example, when present:

```markdown
[Engineering Rules](ENGINEERING_RULES.md)
[Project Context](context/PROJECT_CONTEXT.md)
[Domain Context](context/DOMAIN_CONTEXT.md)
[Pipeline](architecture/PIPELINE.md)
```

Do not create links to files that are not present in the current repository.

Do not invent external URLs.

---

## Git and User-Work Safety

Detailed Git rules belong in `AGENTS.md` and engineering documentation, but agents using this directory should remember:

- existing user changes should be treated as intentional
- unrelated work should not be overwritten
- destructive Git operations should not be performed casually
- generated data should not be confused with source code
- large scientific datasets should not be committed casually
- benchmark results should not be hand-edited to improve appearance

---

## Agent Checklist

### Before Modifying the Repository

- [ ] I read the applicable root `AGENTS.md`.
- [ ] I identified the task category.
- [ ] I loaded only the relevant `.ai/` context.
- [ ] I verified that referenced context files actually exist.
- [ ] I inspected the affected implementation.
- [ ] I inspected nearby tests/configuration where relevant.
- [ ] I searched for existing utilities and patterns.
- [ ] I understand scientific implications where applicable.
- [ ] I understand benchmark implications where applicable.
- [ ] I understand compatibility implications where applicable.
- [ ] I am not relying on unverified repository assumptions.

### Before Finishing

- [ ] The change matches the requested scope.
- [ ] The change is located in the appropriate project area.
- [ ] Relevant tests or validation were run where possible.
- [ ] I did not claim unexecuted validation succeeded.
- [ ] No unrelated user work was overwritten.
- [ ] No fabricated benchmark or confidence values were introduced.
- [ ] Documentation remains consistent with implementation.
- [ ] Planned features were not described as implemented.
- [ ] Scientific claims remain evidence-based.
- [ ] Benchmark comparability was preserved or changes were documented.
- [ ] Security-sensitive behavior was handled appropriately.
- [ ] Additional context was loaded only when necessary.

---

## Final Principle

The `.ai/` directory should make ChandraMap easier to understand **without requiring every agent to read everything**.

Use it as:

```text
Navigation Layer
        +
Task-to-Context Router
        +
Condensed AI Engineering Context
```

not as:

```text
A duplicate copy of the entire repository documentation
```

Load the smallest context sufficient for correctness, expand it when evidence requires more context, and never substitute assumptions for repository truth.
