# ChandraMap Documentation

ChandraMap is an open-source research and engineering project for **lunar image correspondence and registration**. It focuses on identifying the same physical lunar features across observations captured with different sensors, spatial scales, illumination conditions, viewing geometries, and sensing modalities, then using those correspondences to align the imagery and measure registration quality.

> New to the project? Start with the [root README](../README.md).
> Looking for a detailed documentation directory map? See [`docs/README.md`](./README.md).

ChandraMap prioritizes **reliable correspondences, verified geometry, measurable registration quality, reproducibility, and explicit failure handling**. Mosaics, interactive maps, and visual demonstrations are downstream applications of successful registration.

---

## Start Here

| Goal                                   | Recommended documentation                                                                                   |
| -------------------------------------- | ----------------------------------------------------------------------------------------------------------- |
| Understand ChandraMap                  | [Root README](../README.md)                                                                                 |
| Understand the scientific problem      | [Project Context](../.ai/context/PROJECT_CONTEXT.md) and [Domain Context](../.ai/context/DOMAIN_CONTEXT.md) |
| Understand the system design           | [System Overview](../.ai/architecture/SYSTEM_OVERVIEW.md)                                                   |
| Understand the processing stages       | [Processing Pipeline](../.ai/architecture/PIPELINE.md)                                                      |
| Understand repository responsibilities | [Module Map](../.ai/architecture/MODULE_MAP.md)                                                             |
| Understand scientific data movement    | [Data Flow](../.ai/architecture/DATA_FLOW.md)                                                               |
| Understand lunar datasets and sensors  | [Dataset Context](../.ai/context/DATASETS.md)                                                               |
| Look up project terminology            | [Terminology](../.ai/context/TERMINOLOGY.md)                                                                |
| Understand Benchmark V1                | [V1 Scope](../.ai/context/V1_SCOPE.md)                                                                      |
| Understand benchmark methodology       | [Benchmark Rules](../.ai/development/BENCHMARK_RULES.md)                                                    |
| Implement Benchmark V1                 | [V1 Implementation Task](../.ai/tasks/V1_IMPLEMENTATION.md)                                                 |
| Contribute code                        | [CONTRIBUTING.md](../CONTRIBUTING.md)                                                                       |
| Understand testing expectations        | [Testing Rules](../.ai/development/TESTING_RULES.md)                                                        |
| Review future project direction        | [ROADMAP.md](../ROADMAP.md)                                                                                 |
| Work with AI coding agents             | [AGENTS.md](../AGENTS.md) and [`.ai/README.md`](../.ai/README.md)                                           |

---

## What Is ChandraMap?

Two images can observe the same part of the Moon while appearing substantially different.

Differences may come from:

- spatial resolution and ground sampling distance
- illumination direction and shadow geometry
- sensor characteristics
- spectral modality
- viewing geometry
- terrain relief
- partial overlap
- repetitive crater patterns
- low-feature terrain

The goal is therefore not simply to make two images look similar.

ChandraMap aims to establish **physically meaningful image correspondences**, verify those correspondences geometrically, estimate a transformation, register the imagery, and report evidence describing the quality or failure of that registration.

---

## What ChandraMap Produces

The scientific output of a registration workflow may include:

- candidate correspondences
- geometrically verified inliers
- rejected outliers
- transformation or geolocation relationship
- registered image or preview
- residual and spatial-coverage information
- registration quality metrics
- explicit success, rejection, or failure information

A trustworthy system must also be able to say:

> **There is not enough evidence to accept this registration.**

Forcing a transformation in every case would make evaluation less trustworthy.

---

## How It Works

At a high level:

```text
Chandrayaan / Source Image
            +
       LRO Reference
            ↓
       Validate Inputs
            ↓
      Read Metadata
            ↓
      Prepare Imagery
            ↓
       Handle Scale
            ↓
   Determine Reference Region
            ↓
       Local Matching
            ↓
    Candidate Matches
            ↓
 Geometric Verification
       / RANSAC
            ↓
    Verified Inliers
            ↓
 Transform / Registration
            ↓
        Evaluation
            ↓
    Accept / Refine / Reject
```

Candidate matches are only proposals from a matcher.

They become **verified inliers** only after geometric verification.

For the full processing sequence, including later-version retrieval and refinement paths, read the [Processing Pipeline](../.ai/architecture/PIPELINE.md).

---

## Known Region vs Unknown Region

ChandraMap separates local registration from global search.

### Known or Constrained Region

When the overlap is already known or reliable metadata restricts the search area:

```text
Known Candidate Region
        ↓
Local Matching
        ↓
Geometry
        ↓
Registration
```

This is the core setting for canonical **Benchmark V1**.

### Unknown Region

Later benchmark configurations may investigate:

```text
Source Image
      ↓
Global Retrieval
      ↓
Top-K Candidate Regions
      ↓
Local Matching
      ↓
Geometric Verification
      ↓
Registration
```

These responsibilities are intentionally distinct:

- **Global retrieval** decides where to look.
- **Local matching** proposes which points correspond.
- **Geometric verification** determines which matches are mutually consistent.
- **Registration** applies the accepted geometry.

Global retrieval is not a requirement of canonical V1.

---

## Benchmark Architecture

ChandraMap uses a sequence of research benchmark configurations so that added complexity can be measured against a simpler baseline.

| Benchmark | Primary goal                                             |
| --------- | -------------------------------------------------------- |
| **V1**    | Classical reproducible known-overlap baseline            |
| **V2**    | Sensor-aware and scale-aware processing                  |
| **V3**    | Advanced matching and optional global retrieval          |
| **V4**    | Advanced robustness, refinement, and research extensions |

> **Benchmark V1–V4 are research/pipeline configurations. They are independent of ChandraMap software release numbers.**

They should be compared using controlled data, configuration, metrics, and visible failure behavior.

---

### Benchmark V1 — Classical Baseline

V1 establishes the simplest trustworthy registration baseline.

Conceptually:

```text
SIFT / RootSIFT
      ↓
Descriptor Matching
      ↓
Candidate Matches
      ↓
RANSAC
      ↓
Affine / Homography
      ↓
Registration
      ↓
Evaluation
```

Canonical V1 focuses on **known-overlap local registration**.

It intentionally avoids absorbing later-version techniques simply to improve results.

Read:

- [V1 Scope](../.ai/context/V1_SCOPE.md)
- [V1 Implementation Task](../.ai/tasks/V1_IMPLEMENTATION.md)

---

### Benchmark V2 — Sensor and Scale Awareness

V2 conceptually extends the baseline with research areas such as:

- sensor-aware preprocessing
- physically meaningful scale handling
- reference pyramids
- structural representations
- derived 2D representations for hyperspectral data

These are benchmark directions, not implementation claims.

---

### Benchmark V3 — Advanced Matching and Retrieval

V3 may investigate:

- ALIKED + LightGlue
- LoFTR
- remote-sensing correspondence methods
- global image descriptors
- vector retrieval
- FAISS-based indexing/search
- Top-K reference candidates

Retrieval and local geometric registration remain separate tasks.

---

### Benchmark V4 — Research-Grade Robustness

V4 may investigate more advanced research topics such as:

- sub-pixel tie-point refinement
- local or piecewise geometry
- terrain/DEM-aware correction
- advanced hyperspectral processing
- uncertainty estimation
- confidence calibration
- stronger rejection logic
- scalable retrieval

V4 is not automatically the "best" configuration. Additional complexity must justify itself through controlled evaluation.

---

## Scientific Data

ChandraMap's main scientific data context includes Chandrayaan-2 imagery and LRO/LROC reference imagery.

| Instrument / Product | Role in ChandraMap                                  |
| -------------------- | --------------------------------------------------- |
| **OHRC**             | Very high-resolution panchromatic lunar imagery     |
| **TMC-2**            | Panchromatic terrain-scale lunar imagery            |
| **IIRS**             | Hyperspectral / imaging-infrared lunar observations |
| **LRO/LROC NAC**     | Detailed high-resolution lunar reference imagery    |
| **LRO/LROC WAC**     | Wider-area lunar reference and context imagery      |

IIRS is not simply a low-resolution camera. It is hyperspectral/imaging-infrared data, so conventional 2D registration may require a deliberately derived 2D representation.

Images from different sensors may also represent dramatically different physical ground scales.

> Resizing a coarse image increases its raster dimensions; it does not recover physical terrain detail that the sensor never measured.

Likewise, changing the Sun angle can change lunar shadow geometry and apparent feature structure. Brightness normalization alone should not be described as full Sun-angle invariance.

For detailed sensor, metadata, provenance, and data-governance guidance, read the [Dataset Context](../.ai/context/DATASETS.md).

---

## Explore the Documentation

### Architecture

The detailed architecture documents currently live under `.ai/architecture/`. They are structured primarily as engineering context but are also useful to human developers and researchers.

Recommended order:

```text
System Overview
      ↓
Pipeline
      ↓
Module Map
      ↓
Data Flow
```

- [System Overview](../.ai/architecture/SYSTEM_OVERVIEW.md) — major subsystems and responsibility boundaries.
- [Processing Pipeline](../.ai/architecture/PIPELINE.md) — scientific processing order and branches.
- [Module Map](../.ai/architecture/MODULE_MAP.md) — repository ownership and dependency direction.
- [Data Flow](../.ai/architecture/DATA_FLOW.md) — scientific information, coordinates, transforms, provenance, and result semantics.

---

### Scientific Context and Terminology

- [Project Context](../.ai/context/PROJECT_CONTEXT.md) — project purpose, scope, scientific outputs, and research direction.
- [Domain Context](../.ai/context/DOMAIN_CONTEXT.md) — lunar imaging, GSD, illumination, modality, geometry, and terrain constraints.
- [Terminology](../.ai/context/TERMINOLOGY.md) — canonical ChandraMap vocabulary.
- [Dataset Context](../.ai/context/DATASETS.md) — scientific products, sensors, metadata, provenance, and derived data.
- [V1 Scope](../.ai/context/V1_SCOPE.md) — canonical Benchmark V1 boundary.

These files are deeper technical references; new readers can begin with the root README and return to them when needed.

---

### Benchmarking and Evaluation

Start with:

1. [Benchmark Rules](../.ai/development/BENCHMARK_RULES.md)
2. [V1 Scope](../.ai/context/V1_SCOPE.md)
3. [Dataset Context](../.ai/context/DATASETS.md)
4. [Data Flow](../.ai/architecture/DATA_FLOW.md)

The benchmark rules cover topics including:

- controlled comparisons
- pair/data selection
- ground truth
- fit-point vs check-point separation
- failure visibility
- data leakage
- retrieval evaluation
- runtime comparison
- reproducibility
- negative results

Exact metric equations belong in dedicated metric definitions when such documentation is established; this landing page does not reproduce them.

---

### Development

Human contributors should start with:

[CONTRIBUTING.md](../CONTRIBUTING.md)

Deeper engineering references include:

- [Coding Rules](../.ai/development/CODING_RULES.md)
- [Testing Rules](../.ai/development/TESTING_RULES.md)
- [Documentation Rules](../.ai/development/DOCUMENTATION_RULES.md)
- [Benchmark Rules](../.ai/development/BENCHMARK_RULES.md)

These define technical expectations without replacing the normal contributor workflow.

---

### Testing

ChandraMap distinguishes two related but different activities.

**Software testing** asks:

> Does the implementation behave correctly?

**Scientific benchmarking** asks:

> How well does the method perform under controlled lunar-registration conditions?

Read:

- [Testing Rules](../.ai/development/TESTING_RULES.md)
- [Benchmark Rules](../.ai/development/BENCHMARK_RULES.md)

Passing tests does not prove strong scientific performance, and a difficult registration failure does not automatically imply a software defect.

---

## Recommended Reading Paths

### New to ChandraMap

1. [Root README](../README.md)
2. [Project Context](../.ai/context/PROJECT_CONTEXT.md)
3. [System Overview](../.ai/architecture/SYSTEM_OVERVIEW.md)
4. [Processing Pipeline](../.ai/architecture/PIPELINE.md)
5. [Benchmark Rules](../.ai/development/BENCHMARK_RULES.md)

---

### Implementing the Scientific Pipeline

1. [Domain Context](../.ai/context/DOMAIN_CONTEXT.md)
2. [Processing Pipeline](../.ai/architecture/PIPELINE.md)
3. [Module Map](../.ai/architecture/MODULE_MAP.md)
4. [Data Flow](../.ai/architecture/DATA_FLOW.md)
5. [Coding Rules](../.ai/development/CODING_RULES.md)
6. [Testing Rules](../.ai/development/TESTING_RULES.md)

For V1 specifically, continue with the [V1 Implementation Task](../.ai/tasks/V1_IMPLEMENTATION.md).

---

### Researcher

1. [Project Context](../.ai/context/PROJECT_CONTEXT.md)
2. [Domain Context](../.ai/context/DOMAIN_CONTEXT.md)
3. [Dataset Context](../.ai/context/DATASETS.md)
4. [Processing Pipeline](../.ai/architecture/PIPELINE.md)
5. [Benchmark Rules](../.ai/development/BENCHMARK_RULES.md)
6. [V1 Scope](../.ai/context/V1_SCOPE.md)

---

### Reproducing or Extending Benchmarks

1. [Dataset Context](../.ai/context/DATASETS.md)
2. [Benchmark Rules](../.ai/development/BENCHMARK_RULES.md)
3. [Testing Rules](../.ai/development/TESTING_RULES.md)
4. [Data Flow](../.ai/architecture/DATA_FLOW.md)
5. [V1 Scope](../.ai/context/V1_SCOPE.md)
6. [V1 Implementation Task](../.ai/tasks/V1_IMPLEMENTATION.md), when working on V1

Use only measured results produced by the defined benchmark methodology.

---

### Contributing Code

1. [Root README](../README.md)
2. [CONTRIBUTING.md](../CONTRIBUTING.md)
3. [System Overview](../.ai/architecture/SYSTEM_OVERVIEW.md)
4. [Module Map](../.ai/architecture/MODULE_MAP.md)
5. [Coding Rules](../.ai/development/CODING_RULES.md)
6. [Testing Rules](../.ai/development/TESTING_RULES.md)

Load additional scientific context only when the change requires it.

---

## Project Principles

### Correspondence First

Reliable point correspondences and defensible geometry are the scientific foundation.

A visually attractive mosaic does not compensate for unreliable registration.

### Measure, Don't Guess

Registered overlays are useful diagnostics, but registration quality should be evaluated quantitatively.

Independent check points should be preferred for independent accuracy assessment when valid check-point data exists.

### Candidate Does Not Mean Verified

A matcher proposes candidate correspondences.

Geometric verification determines which candidates are model-consistent inliers.

### Failure Is a Valid Result

A trustworthy registration system should reject insufficient or contradictory evidence instead of always forcing an alignment.

### Reproducibility Matters

A scientific result should remain traceable, where practical, to:

```text
Data
+
Configuration
+
Code
+
Method
+
Metric Definition
```

### Baseline Before Complexity

Benchmark V1 intentionally establishes a simple classical baseline before later benchmark configurations introduce additional sensor awareness, learned methods, retrieval, or advanced refinement.

### Scientific Claims Must Match Evidence

ChandraMap should describe measured robustness on evaluated data rather than claiming universal scale, illumination, or modality invariance without evidence.

---

## Repository and Community

| Resource                                    | Purpose                                        |
| ------------------------------------------- | ---------------------------------------------- |
| [README.md](../README.md)                   | Main GitHub project landing page               |
| [ROADMAP.md](../ROADMAP.md)                 | Future project and research direction          |
| [CONTRIBUTING.md](../CONTRIBUTING.md)       | Contribution workflow                          |
| [SECURITY.md](../SECURITY.md)               | Security and vulnerability reporting           |
| [CODE_OF_CONDUCT.md](../CODE_OF_CONDUCT.md) | Community participation standards              |
| [CHANGELOG.md](../CHANGELOG.md)             | Notable completed project changes              |
| [CITATION.cff](../CITATION.cff)             | Repository citation metadata                   |
| [AGENTS.md](../AGENTS.md)                   | Repository-level guidance for AI coding agents |

Roadmap items describe intended future work and should not be interpreted as already implemented capabilities.

---

## AI-Assisted Development

ChandraMap maintains structured AI-agent context under:

[`.ai/README.md`](../.ai/README.md)

The `.ai/` area contains:

- project and domain context
- architecture guidance
- engineering rules
- development standards
- benchmark rules
- implementation-task guidance

It exists primarily to support safe, consistent AI-assisted engineering.

Human contributors may consult it for deeper technical detail, but they should not need to understand the entire `.ai/` hierarchy simply to navigate the project.

Repository-level AI instructions are defined in:

[AGENTS.md](../AGENTS.md)

---

## Documentation Index

This file and [`docs/README.md`](./README.md) have different responsibilities:

```text
docs/index.md
→ documentation landing page
→ project orientation
→ scientific overview
→ recommended reading paths

docs/README.md
→ documentation directory guide
→ detailed documentation map
→ file and topic navigation
```

Use this page when you are deciding **what ChandraMap is and where to begin**.

Use [`docs/README.md`](./README.md) when you already know the topic and want to locate the appropriate documentation quickly.

---

## Contributing to Documentation

For the repository contribution process, read:

[CONTRIBUTING.md](../CONTRIBUTING.md)

For detailed documentation standards, read:

[Documentation Rules](../.ai/development/DOCUMENTATION_RULES.md)

When architecture, public behavior, benchmark methodology, dataset handling, scientific terminology, or supported workflows change, update the corresponding authoritative documentation in the same change where practical.

Documentation should describe repository reality, distinguish current work from target research, and avoid invented commands, paths, APIs, benchmark results, or implementation claims.

<!-- Source specification: :contentReference[oaicite:0]{index=0} -->
