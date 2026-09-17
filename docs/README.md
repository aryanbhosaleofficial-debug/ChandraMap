# ChandraMap Documentation

This directory is the human-facing navigation hub for the **ChandraMap** documentation.

ChandraMap is an open-source lunar image correspondence and registration research project focused on matching and aligning observations of the same lunar region across differences in sensor, spatial scale, illumination, viewing conditions, and modality.

Its core scientific outputs are:

```text
Source Lunar Image
        +
Reference Image
        ↓
Correspondence
        ↓
Geometric Verification
        ↓
Registration
        ↓
Evaluation
```

Reliable correspondences, validated geometry, measurable registration quality, and explicit failure handling are the scientific core. Mosaics, maps, dashboards, and other visual applications are downstream uses of that core.

> **New to ChandraMap? Start with the [root README](../README.md).**

The principle of this documentation index is:

> **Navigate, do not duplicate.**

This file tells you what documentation to read, why it matters, and where it lives. Detailed scientific, architectural, benchmark, and development rules remain in their canonical documents.

---

## 1. Start Here

| If you want to...                                              | Start with                                                   |
| -------------------------------------------------------------- | ------------------------------------------------------------ |
| Understand what ChandraMap is                                  | [Root README](../README.md)                                  |
| See the long-term project direction                            | [Roadmap](../ROADMAP.md)                                     |
| Contribute to the repository                                   | [Contributing Guide](../CONTRIBUTING.md)                     |
| Understand the system architecture                             | [System Overview](../.ai/architecture/SYSTEM_OVERVIEW.md)    |
| Understand the scientific processing order                     | [Processing Pipeline](../.ai/architecture/PIPELINE.md)       |
| Understand repository/module responsibilities                  | [Module Map](../.ai/architecture/MODULE_MAP.md)              |
| Understand how scientific information moves through the system | [Data Flow](../.ai/architecture/DATA_FLOW.md)                |
| Understand lunar registration constraints                      | [Domain Context](../.ai/context/DOMAIN_CONTEXT.md)           |
| Look up ChandraMap terminology                                 | [Terminology](../.ai/context/TERMINOLOGY.md)                 |
| Understand scientific datasets and provenance                  | [Dataset Context](../.ai/context/DATASETS.md)                |
| Understand canonical Benchmark V1                              | [V1 Scope](../.ai/context/V1_SCOPE.md)                       |
| Understand benchmark methodology                               | [Benchmark Rules](../.ai/development/BENCHMARK_RULES.md)     |
| Understand testing expectations                                | [Testing Rules](../.ai/development/TESTING_RULES.md)         |
| Understand coding expectations                                 | [Coding Rules](../.ai/development/CODING_RULES.md)           |
| Work on the V1 implementation task                             | [V1 Implementation Task](../.ai/tasks/V1_IMPLEMENTATION.md)  |
| Work with AI coding agents                                     | [AGENTS.md](../AGENTS.md) and [.ai README](../.ai/README.md) |
| Report or review security concerns                             | [Security Policy](../SECURITY.md)                            |
| Cite ChandraMap                                                | [CITATION.cff](../CITATION.cff)                              |
| Review project changes                                         | [Changelog](../CHANGELOG.md)                                 |

---

## 2. About This Documentation

ChandraMap documentation is divided across three main locations.

### Root Documentation

Root-level files provide the main project-facing and governance information.

Examples include:

- `README.md`
- `ROADMAP.md`
- `CONTRIBUTING.md`
- `SECURITY.md`
- `CHANGELOG.md`
- `CODE_OF_CONDUCT.md`
- `CITATION.cff`
- `AGENTS.md`

These are the first documents most users and contributors should encounter.

---

### `docs/`

The `docs/` directory is the human-facing documentation area.

This file:

```text
docs/README.md
```

acts as the documentation index and navigation hub.

Human-facing documentation should remain usable without requiring readers to understand the entire internal AI-agent context system.

---

### `.ai/`

The `.ai/` directory contains structured technical context, engineering rules, architecture references, and implementation-task guidance intended primarily for AI coding agents and repository maintainers.

Many of those files are also useful to human developers and researchers as deeper technical references.

However:

> `.ai/` does not replace human-facing project documentation.

Start with the root README and this documentation index unless you specifically need deeper engineering or scientific context.

---

## 3. Project Documentation

The following root-level files own major project concerns.

| Document                                    | Purpose                                                      |
| ------------------------------------------- | ------------------------------------------------------------ |
| [README.md](../README.md)                   | Main project introduction, orientation, and entry point      |
| [ROADMAP.md](../ROADMAP.md)                 | Planned project evolution and research/engineering direction |
| [CONTRIBUTING.md](../CONTRIBUTING.md)       | Contributor workflow and contribution expectations           |
| [CHANGELOG.md](../CHANGELOG.md)             | Notable completed project changes                            |
| [SECURITY.md](../SECURITY.md)               | Security policy and vulnerability-reporting guidance         |
| [CODE_OF_CONDUCT.md](../CODE_OF_CONDUCT.md) | Community participation expectations                         |
| [CITATION.cff](../CITATION.cff)             | Canonical repository citation metadata                       |
| [AGENTS.md](../AGENTS.md)                   | Repository-level instructions for AI coding agents           |

The root README remains the canonical project landing page.

This `docs/README.md` should not become a second copy of it.

---

## 4. Architecture Documentation

Current detailed architecture references live under:

```text
.ai/architecture/
```

These documents are AI-oriented technical references but are also useful for engineers and reviewers.

### Recommended Architecture Reading Order

```text
System Overview
      ↓
Processing Pipeline
      ↓
Module Map
      ↓
Data Flow
```

| Document                                                  | Read this when...                                                                              |
| --------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| [System Overview](../.ai/architecture/SYSTEM_OVERVIEW.md) | You need to understand the major ChandraMap subsystems and their architectural boundaries      |
| [Processing Pipeline](../.ai/architecture/PIPELINE.md)    | You need to understand the order in which scientific processing stages execute                 |
| [Module Map](../.ai/architecture/MODULE_MAP.md)           | You need to determine which repository area should own a responsibility                        |
| [Data Flow](../.ai/architecture/DATA_FLOW.md)             | You need to understand scientific data, coordinate, transform, provenance, and result movement |

These documents have different responsibilities.

### System Overview

Answers:

> What major system components exist conceptually?

### Processing Pipeline

Answers:

> In what scientific order are processing stages used?

### Module Map

Answers:

> Where should implementation responsibilities live?

### Data Flow

Answers:

> What information moves between those responsibilities, and what scientific meaning must be preserved?

Do not treat these documents as interchangeable.

---

## 5. Scientific and Domain Documentation

Detailed scientific context currently lives under:

```text
.ai/context/
```

These files are primarily structured technical context, but they are valuable references for human researchers and developers.

| Document                                             | Purpose                                                                                          |
| ---------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
| [Project Context](../.ai/context/PROJECT_CONTEXT.md) | Defines ChandraMap's scientific purpose, scope, outputs, and research direction                  |
| [Domain Context](../.ai/context/DOMAIN_CONTEXT.md)   | Explains lunar imaging, scale, illumination, modality, geometry, and scientific constraints      |
| [Terminology](../.ai/context/TERMINOLOGY.md)         | Defines canonical ChandraMap scientific and engineering vocabulary                               |
| [Dataset Context](../.ai/context/DATASETS.md)        | Defines dataset roles, missions, sensors, metadata, provenance, and data-governance expectations |
| [V1 Scope](../.ai/context/V1_SCOPE.md)               | Defines the authoritative scientific boundary of canonical Benchmark V1                          |

If you encounter an unfamiliar term such as:

- GSD
- candidate match
- verified inlier
- tie point
- check point
- global retrieval
- local matching
- source-image pixel error

start with the [Terminology](../.ai/context/TERMINOLOGY.md) reference.

---

## 6. Datasets and Data

The primary dataset reference is:

[Dataset Context](../.ai/context/DATASETS.md)

It covers the scientific roles and handling expectations for relevant lunar data, including contexts such as:

- Chandrayaan-2 OHRC
- Chandrayaan-2 TMC-2
- Chandrayaan-2 IIRS
- LRO/LROC NAC
- LRO/LROC WAC
- terrain/elevation data where relevant
- benchmark data
- fixtures
- derived products
- provenance
- metadata
- storage and data-governance principles

For exact processing values:

> **Product-specific metadata takes precedence over broad instrument-level approximations.**

This documentation hub intentionally does not repeat sensor specifications or dataset-download procedures.

Use the canonical dataset documentation for those topics.

---

## 7. Benchmarking

Benchmarking is a central part of ChandraMap because later methods must be compared against controlled baselines rather than judged only from visual examples.

ChandraMap uses conceptual research configurations:

| Benchmark        | High-Level Role                                              |
| ---------------- | ------------------------------------------------------------ |
| **Benchmark V1** | Classical known-overlap registration baseline                |
| **Benchmark V2** | Sensor-aware and scale-aware extension                       |
| **Benchmark V3** | Advanced correspondence and optional retrieval               |
| **Benchmark V4** | Research-grade robustness, refinement, and advanced geometry |

> **Benchmark V1–V4 are research/pipeline configurations, not ChandraMap software release numbers.**

Do not interpret:

```text
Benchmark V1
```

as:

```text
software v1.0.0
```

---

### 7.1 Benchmark V1

The authoritative V1 scope is:

[V1 Scope](../.ai/context/V1_SCOPE.md)

V1 establishes a controlled classical baseline based around known-overlap local registration.

Its purpose is baseline value, not maximum possible ChandraMap accuracy.

---

### 7.2 Benchmark Methodology

Read:

[Benchmark Rules](../.ai/development/BENCHMARK_RULES.md)

for requirements covering topics such as:

- controlled comparison
- benchmark identity
- data selection
- ground truth
- fit/check-point separation
- failure visibility
- data leakage
- runtime comparisons
- aggregation
- reproducibility
- negative results
- scientific reporting

---

### 7.3 V1 Implementation

If you are implementing the first benchmark baseline, read:

[V1 Implementation Task](../.ai/tasks/V1_IMPLEMENTATION.md)

after reading:

1. [V1 Scope](../.ai/context/V1_SCOPE.md)
2. [Module Map](../.ai/architecture/MODULE_MAP.md)
3. [Processing Pipeline](../.ai/architecture/PIPELINE.md)
4. [Testing Rules](../.ai/development/TESTING_RULES.md)
5. [Benchmark Rules](../.ai/development/BENCHMARK_RULES.md)

---

## 8. Metrics and Scientific Evaluation

ChandraMap evaluation may involve concepts such as:

- candidate match count
- verified inlier count
- inlier ratio
- residual error
- spatial coverage
- source-image pixel error
- independent check-point RMSE
- ground error where scientifically justified
- retrieval Recall@K
- runtime
- success/rejection/failure

This documentation index does not define the equations.

Where authoritative metric documentation exists, use that document for exact:

- formulas
- units
- aggregation
- edge-case behavior
- benchmark semantics

Until then, metric usage should remain consistent with:

- [Data Flow](../.ai/architecture/DATA_FLOW.md)
- [Testing Rules](../.ai/development/TESTING_RULES.md)
- [Benchmark Rules](../.ai/development/BENCHMARK_RULES.md)

Important scientific distinction:

> Fit-point residual is not automatically independent registration accuracy.

Likewise:

> Sub-pixel error does not automatically mean sub-metre ground error.

---

## 9. Development Documentation

Detailed engineering guidance currently lives under:

```text
.ai/development/
```

These are primarily engineering/AI-agent references but are also useful to human contributors.

| Document                                                         | Purpose                                                                                                          |
| ---------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| [Coding Rules](../.ai/development/CODING_RULES.md)               | Source-code conventions, scientific coding safety, coordinates, units, numerical behavior, and module discipline |
| [Testing Rules](../.ai/development/TESTING_RULES.md)             | Unit, integration, regression, failure, scientific, benchmark, and validation expectations                       |
| [Documentation Rules](../.ai/development/DOCUMENTATION_RULES.md) | Documentation accuracy, structure, status language, links, examples, and scientific-claim standards              |
| [Benchmark Rules](../.ai/development/BENCHMARK_RULES.md)         | Scientific benchmark governance and comparability                                                                |

For normal contribution workflow, begin with:

[CONTRIBUTING.md](../CONTRIBUTING.md)

then consult the deeper development references relevant to your change.

---

## 10. Testing

ChandraMap distinguishes software testing from scientific benchmarking.

### Software Testing

Answers:

> Is the implementation behaving correctly?

Examples include:

- coordinate convention
- transform direction
- metric mathematics
- failure handling
- serialization
- matcher/geometry integration

Read:

[Testing Rules](../.ai/development/TESTING_RULES.md)

---

### Scientific Benchmarking

Answers:

> How well does the method perform under controlled lunar-registration conditions?

Read:

[Benchmark Rules](../.ai/development/BENCHMARK_RULES.md)

A passing software test suite does not prove strong registration performance.

A scientifically difficult benchmark failure does not automatically mean the software implementation is incorrect.

---

## 11. Research and Experiments

Research work should remain distinguishable from stable project behavior.

The repository architecture includes research-oriented areas such as:

```text
research/
experiments/
notebooks/
```

when present in the working tree.

Use those areas for activities such as:

- research questions
- controlled experiments
- ablations
- exploratory representations
- algorithm comparisons
- literature-driven prototypes
- result analysis

Research or notebook code should not automatically be treated as stable or supported ChandraMap behavior.

For experimental methodology, consult:

- [Benchmark Rules](../.ai/development/BENCHMARK_RULES.md)
- [Testing Rules](../.ai/development/TESTING_RULES.md)
- [Documentation Rules](../.ai/development/DOCUMENTATION_RULES.md)

Negative results are valid research outcomes and should not be hidden merely because they weaken a preferred narrative.

---

## 12. Backend, API, and Applications

ChandraMap's scientific core should remain independent of presentation and transport layers.

Where backend, API, or application components exist, their architecture should follow the responsibility boundaries described in:

- [System Overview](../.ai/architecture/SYSTEM_OVERVIEW.md)
- [Module Map](../.ai/architecture/MODULE_MAP.md)
- [Data Flow](../.ai/architecture/DATA_FLOW.md)

Conceptually:

```text
Application / API / CLI
        ↓
Scientific Core
        ↓
Structured Scientific Result
```

Application layers may present or serialize scientific results.

They should not independently redefine:

- RMSE
- inlier membership
- transformation validity
- scientific success/rejection
- benchmark confidence

This index does not invent API or application documentation that has not been defined elsewhere.

---

## 13. Frontend, Visualization, Maps, and Mosaics

Visual interfaces are downstream consumers of the scientific registration system.

Potential presentation outputs include:

- correspondence visualization
- verified-inlier visualization
- residual visualization
- registered previews
- overlays
- lunar-map demonstrations
- mosaics

These are useful for interpretation and demonstration.

They are not substitutes for:

- geometric verification
- numerical evaluation
- benchmark evidence

ChandraMap's core deliverable remains trustworthy correspondence and registration with measurable quality and explicit failure handling.

---

## 14. Security and Governance

Use the root governance documents for repository-wide policies.

| Document                                    | Purpose                                      |
| ------------------------------------------- | -------------------------------------------- |
| [SECURITY.md](../SECURITY.md)               | Security and vulnerability-reporting policy  |
| [CONTRIBUTING.md](../CONTRIBUTING.md)       | Contribution workflow                        |
| [CODE_OF_CONDUCT.md](../CODE_OF_CONDUCT.md) | Community standards                          |
| [AGENTS.md](../AGENTS.md)                   | Repository-level AI-agent operating guidance |

Security-sensitive implementation details should remain consistent with the project's security and engineering policies.

Do not duplicate vulnerability-reporting instructions in this documentation index.

---

## 15. Releases, History, and Project Direction

Use:

[CHANGELOG.md](../CHANGELOG.md)
for notable completed project changes.

Use:

[ROADMAP.md](../ROADMAP.md)
for planned project evolution.

These documents serve different purposes.

```text
CHANGELOG
→ what changed

ROADMAP
→ what is intended next
```

Do not interpret roadmap items as already implemented functionality.

---

## 16. Citation

Researchers and users who need citation information should consult:

[CITATION.cff](../CITATION.cff)

Do not infer publication, DOI, release, or author metadata from other documentation when the canonical citation metadata is available.

---

## 17. AI-Agent Documentation

The `.ai/` directory contains structured repository context intended primarily for AI coding agents and maintainers.

Start with:

[`.ai/README.md`](../.ai/README.md)

then load only the context required for the task.

High-level structure:

```text
.ai/
├── README.md
├── ENGINEERING_RULES.md
├── context/
├── architecture/
├── development/
└── tasks/
```

Important references include:

- [Engineering Rules](../.ai/ENGINEERING_RULES.md)
- [Project Context](../.ai/context/PROJECT_CONTEXT.md)
- [Domain Context](../.ai/context/DOMAIN_CONTEXT.md)
- [Terminology](../.ai/context/TERMINOLOGY.md)
- [Datasets](../.ai/context/DATASETS.md)
- [V1 Scope](../.ai/context/V1_SCOPE.md)
- [System Overview](../.ai/architecture/SYSTEM_OVERVIEW.md)
- [Pipeline](../.ai/architecture/PIPELINE.md)
- [Module Map](../.ai/architecture/MODULE_MAP.md)
- [Data Flow](../.ai/architecture/DATA_FLOW.md)
- [Coding Rules](../.ai/development/CODING_RULES.md)
- [Testing Rules](../.ai/development/TESTING_RULES.md)
- [Documentation Rules](../.ai/development/DOCUMENTATION_RULES.md)
- [Benchmark Rules](../.ai/development/BENCHMARK_RULES.md)
- [V1 Implementation Task](../.ai/tasks/V1_IMPLEMENTATION.md)

Human contributors may use these documents as deeper technical references, but they should not need to read every AI-specific file to understand the project.

---

## 18. Documentation Ownership Map

Use this table to find the canonical owner for a topic.

| I need information about...     | Canonical starting point                                         |
| ------------------------------- | ---------------------------------------------------------------- |
| What ChandraMap is              | [Root README](../README.md)                                      |
| Future direction                | [ROADMAP.md](../ROADMAP.md)                                      |
| Contribution process            | [CONTRIBUTING.md](../CONTRIBUTING.md)                            |
| Change history                  | [CHANGELOG.md](../CHANGELOG.md)                                  |
| Security                        | [SECURITY.md](../SECURITY.md)                                    |
| Community standards             | [CODE_OF_CONDUCT.md](../CODE_OF_CONDUCT.md)                      |
| Citation                        | [CITATION.cff](../CITATION.cff)                                  |
| AI-agent repository rules       | [AGENTS.md](../AGENTS.md)                                        |
| Scientific project context      | [Project Context](../.ai/context/PROJECT_CONTEXT.md)             |
| Lunar imaging constraints       | [Domain Context](../.ai/context/DOMAIN_CONTEXT.md)               |
| Terminology                     | [Terminology](../.ai/context/TERMINOLOGY.md)                     |
| Datasets and provenance         | [Dataset Context](../.ai/context/DATASETS.md)                    |
| Canonical V1 boundary           | [V1 Scope](../.ai/context/V1_SCOPE.md)                           |
| System architecture             | [System Overview](../.ai/architecture/SYSTEM_OVERVIEW.md)        |
| Scientific processing order     | [Processing Pipeline](../.ai/architecture/PIPELINE.md)           |
| Repository/module ownership     | [Module Map](../.ai/architecture/MODULE_MAP.md)                  |
| Scientific information movement | [Data Flow](../.ai/architecture/DATA_FLOW.md)                    |
| Coding standards                | [Coding Rules](../.ai/development/CODING_RULES.md)               |
| Testing                         | [Testing Rules](../.ai/development/TESTING_RULES.md)             |
| Documentation standards         | [Documentation Rules](../.ai/development/DOCUMENTATION_RULES.md) |
| Benchmark methodology           | [Benchmark Rules](../.ai/development/BENCHMARK_RULES.md)         |
| Implementing V1                 | [V1 Implementation Task](../.ai/tasks/V1_IMPLEMENTATION.md)      |

---

## 19. Documentation Map

A concise view of the currently referenced documentation structure is:

```text
ChandraMap/
├── README.md
├── ROADMAP.md
├── CONTRIBUTING.md
├── CHANGELOG.md
├── SECURITY.md
├── CODE_OF_CONDUCT.md
├── CITATION.cff
├── AGENTS.md
│
├── docs/
│   └── README.md
│
└── .ai/
    ├── README.md
    ├── ENGINEERING_RULES.md
    │
    ├── context/
    │   ├── PROJECT_CONTEXT.md
    │   ├── DOMAIN_CONTEXT.md
    │   ├── TERMINOLOGY.md
    │   ├── DATASETS.md
    │   └── V1_SCOPE.md
    │
    ├── architecture/
    │   ├── SYSTEM_OVERVIEW.md
    │   ├── PIPELINE.md
    │   ├── MODULE_MAP.md
    │   └── DATA_FLOW.md
    │
    ├── development/
    │   ├── CODING_RULES.md
    │   ├── TESTING_RULES.md
    │   ├── DOCUMENTATION_RULES.md
    │   └── BENCHMARK_RULES.md
    │
    └── tasks/
        └── V1_IMPLEMENTATION.md
```

This is a navigation map, not a complete repository tree.

---

## 20. Recommended Reading Paths

### New Visitor

1. [Root README](../README.md)
2. [System Overview](../.ai/architecture/SYSTEM_OVERVIEW.md)
3. [Processing Pipeline](../.ai/architecture/PIPELINE.md)

This provides project purpose, architecture, and scientific flow without requiring detailed engineering rules.

---

### New Contributor

1. [Root README](../README.md)
2. [CONTRIBUTING.md](../CONTRIBUTING.md)
3. [System Overview](../.ai/architecture/SYSTEM_OVERVIEW.md)
4. [Module Map](../.ai/architecture/MODULE_MAP.md)
5. [Coding Rules](../.ai/development/CODING_RULES.md)
6. [Testing Rules](../.ai/development/TESTING_RULES.md)

Load additional scientific context only when relevant to the change.

---

### Researcher

1. [Project Context](../.ai/context/PROJECT_CONTEXT.md)
2. [Domain Context](../.ai/context/DOMAIN_CONTEXT.md)
3. [Dataset Context](../.ai/context/DATASETS.md)
4. [Processing Pipeline](../.ai/architecture/PIPELINE.md)
5. [Benchmark Rules](../.ai/development/BENCHMARK_RULES.md)
6. [V1 Scope](../.ai/context/V1_SCOPE.md)

For measured results, use only actual benchmark outputs generated under the defined methodology.

---

### Computer Vision / Registration Developer

1. [Domain Context](../.ai/context/DOMAIN_CONTEXT.md)
2. [Terminology](../.ai/context/TERMINOLOGY.md)
3. [Processing Pipeline](../.ai/architecture/PIPELINE.md)
4. [Data Flow](../.ai/architecture/DATA_FLOW.md)
5. [Module Map](../.ai/architecture/MODULE_MAP.md)
6. [Testing Rules](../.ai/development/TESTING_RULES.md)
7. [Benchmark Rules](../.ai/development/BENCHMARK_RULES.md)

If working specifically on V1, then read:

[V1 Implementation Task](../.ai/tasks/V1_IMPLEMENTATION.md)

---

### Benchmark / Reproducibility Developer

1. [Dataset Context](../.ai/context/DATASETS.md)
2. [Benchmark Rules](../.ai/development/BENCHMARK_RULES.md)
3. [Testing Rules](../.ai/development/TESTING_RULES.md)
4. [Data Flow](../.ai/architecture/DATA_FLOW.md)
5. [V1 Scope](../.ai/context/V1_SCOPE.md)

Keep software testing and scientific benchmarking distinct.

---

### Reviewer / Evaluator

1. [Root README](../README.md)
2. [Project Context](../.ai/context/PROJECT_CONTEXT.md)
3. [System Overview](../.ai/architecture/SYSTEM_OVERVIEW.md)
4. [Processing Pipeline](../.ai/architecture/PIPELINE.md)
5. [V1 Scope](../.ai/context/V1_SCOPE.md)
6. [Benchmark Rules](../.ai/development/BENCHMARK_RULES.md)
7. [Dataset Context](../.ai/context/DATASETS.md)

This path helps distinguish proposed architecture, implemented capability, and measured benchmark evidence.

---

### AI Coding Agent

1. [AGENTS.md](../AGENTS.md)
2. [`.ai/README.md`](../.ai/README.md)
3. [Engineering Rules](../.ai/ENGINEERING_RULES.md)
4. Load only the specialized context required by the task.

Do not load the entire repository context unnecessarily.

---

## 21. Contributing to Documentation

For contribution workflow, start with:

[CONTRIBUTING.md](../CONTRIBUTING.md)

For documentation-specific standards, consult:

[Documentation Rules](../.ai/development/DOCUMENTATION_RULES.md)

When implementation, architecture, configuration, benchmark methodology, dataset handling, or public behavior changes, update the relevant authoritative documentation in the same change where practical.

Avoid duplicating information across multiple files when one canonical document can own it.

---

## 22. Documentation Principles

When adding or updating ChandraMap documentation:

- document verified behavior rather than assumptions
- distinguish current, experimental, target, and planned work
- avoid invented commands, paths, APIs, schemas, or benchmark results
- use canonical terminology
- include units and coordinate context where scientifically relevant
- keep benchmark V1–V4 separate from software release numbering
- treat failure/rejection as valid scientific outcomes
- use relative repository links
- keep code fences and Markdown standard
- avoid unnecessary duplication
- preserve scientific provenance and limitations
- update affected documentation when repository behavior changes

Detailed documentation policy belongs in:

[Documentation Rules](../.ai/development/DOCUMENTATION_RULES.md)

---

## 23. Related Repository Files

- [Project README](../README.md)
- [Roadmap](../ROADMAP.md)
- [Contributing Guide](../CONTRIBUTING.md)
- [Security Policy](../SECURITY.md)
- [Code of Conduct](../CODE_OF_CONDUCT.md)
- [Changelog](../CHANGELOG.md)
- [Citation Metadata](../CITATION.cff)
- [AI-Agent Instructions](../AGENTS.md)
- [AI Context Index](../.ai/README.md)

This file should remain a concise navigation layer rather than becoming the canonical source for architecture, scientific methodology, development rules, or benchmark definitions.

<!-- Source specification: :contentReference[oaicite:0]{index=0} -->
