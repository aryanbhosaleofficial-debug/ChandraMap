# ChandraMap Roadmap

> ChandraMap is an open-source research and engineering project for lunar
> image correspondence and registration. This roadmap describes the planned
> technical direction; it does not represent completed work unless explicitly
> stated.

## Vision

ChandraMap aims to reliably identify correspondences between lunar images
captured by different sensors, resolutions, illumination conditions, viewing
geometries, and modalities, and to register those images with measurable,
reproducible accuracy.

The project originated from SIH 26166 (multi-modal, Sun-angle and scale
invariant image correspondence using Chandrayaan-2 optical/scientific
imagery) and is being developed beyond that hackathon context as a longer-term
open-source research and engineering effort.

The central problem ChandraMap addresses is **not** "produce a Moon map."
It is: find reliable correspondences between lunar images and register them
accurately despite differences in scale, ground sampling distance,
illumination, Sun angle, shadows, viewpoint, sensor characteristics, modality,
terrain relief, and image quality. A lunar mosaic, map, or visualization
interface is a downstream demonstration of this capability, not the primary
scientific deliverable.

## Roadmap Principles

1. Measure before optimizing.
2. Build a working baseline before adding advanced AI models.
3. Keep sensor-specific preprocessing where physically necessary.
4. Compare images at physically meaningful scales.
5. Upsampling is never described as recovering missing terrain detail.
6. Use metadata when available rather than solving problems metadata already
   answers.
7. Keep pure image-based retrieval as a fallback where appropriate.
8. Global image retrieval and local correspondence are treated as separate
   problems.
9. Global descriptors and local descriptors serve different purposes and are
   not interchangeable.
10. A matcher confidence value is not proof of geometric correctness.
11. Geometric verification (e.g. RANSAC) determines verified inliers.
12. Sub-pixel refinement operates on geometrically verified tie points, not
    raw candidate matches.
13. The final transformation is refit after tie-point refinement.
14. The transform is not evaluated only on the points used to fit it when
    independent check points or ground truth are available.
15. More matches do not automatically mean better registration.
16. Spatial distribution of matches matters as much as their count.
17. Learned terrestrial models are benchmarked before any claim of lunar
    robustness is made.
18. Failed registrations are rejected rather than forced.
19. Experimental methodology remains reproducible.
20. Research claims are supported by measured evidence, not assumption.

## Scope

### In Scope

- Multi-sensor lunar image correspondence (OHRC, TMC-2, IIRS as source;
  LRO NAC, LRO WAC as reference).
- Classical and learned local feature matching.
- Geometric verification, sub-pixel tie-point refinement, and transform
  estimation.
- Image registration and georeferencing where supported by data quality.
- Reproducible benchmarking across sensor and illumination conditions.
- Quality evaluation metrics (RMSE, inlier statistics, spatial coverage,
  retrieval metrics, runtime, failure reporting).
- Optional global lunar-region retrieval as a candidate-search mechanism.
- Backend services, API, and visualization as downstream layers built on a
  validated core pipeline.

### Out of Scope / Not Yet a Priority

- Building a Google Maps-scale Moon application before registration accuracy
  is validated.
- Building a photorealistic 3D Moon primarily for presentation purposes.
- Supporting every planetary mission or dataset immediately.
- Training large custom neural networks without a benchmark to justify them.
- Claiming universal sensor invariance.
- Producing global mosaics before local registration is validated.
- Adding algorithms or matchers without measurable, benchmark-based
  justification.

## Roadmap Overview

| Phase    | Focus                                         | Status   | Primary Outcome                                                 |
| -------- | --------------------------------------------- | -------- | --------------------------------------------------------------- |
| Phase 0  | Project Foundation                            | Planned  | Repository, tooling, and documentation scaffolding              |
| Phase 1  | Benchmark V1 — Classical Baseline             | Planned  | Reproducible classical registration baseline                    |
| Phase 2  | Benchmark V2 — Sensor-Aware / Multi-Scale     | Planned  | Sensor-specific preprocessing and multi-scale matching          |
| Phase 3  | Benchmark V3 — Advanced Matching / Retrieval  | Planned  | Learned matcher evaluation and optional global retrieval        |
| Phase 4  | Benchmark V4 — Research-Grade Robust System   | Planned  | Confidence-aware, accept/refine/reject registration system      |
| Phase 5  | Benchmarking & Reproducibility Infrastructure | Planned  | Standardized, reproducible benchmark runner and reporting       |
| Phase 6  | Backend and API                               | Planned  | Service layer for registration, retrieval, and results          |
| Phase 7  | Visualization / Web Interface                 | Planned  | Interfaces for inspecting matches, registration, and metrics    |
| Phase 8  | Mosaics and Lunar Mapping Demonstrations      | Planned  | Downstream mosaic/mapping demos built on validated registration |
| Phase 9  | Research Extensions                           | Proposed | Experimental directions beyond the core benchmark architecture  |
| Phase 10 | Stable Open-Source Release                    | Planned  | First stable `1.0.0` software release                           |

Status is tracked honestly: work not yet started or not yet confirmed is
marked **Planned** or **Proposed** rather than assumed complete.

---

## Benchmark Architecture

ChandraMap organizes its research work around **four benchmark configurations**
(Benchmark V1–V4). These are experimental/research pipeline configurations
used to evaluate and compare registration approaches — they are **not**
equivalent to software release versions (e.g. `v0.1.0`, `v1.0.0`). A benchmark
version number is never assumed to correspond to a software release number
unless explicitly stated in project history. Software releases track the
state of the codebase and its interfaces; benchmark versions track research
configurations used for evaluation.

### Benchmark V1 — Classical Baseline

**Purpose:** Establish the simplest reproducible image-registration baseline
against which all later versions are measured — a number to beat.

**Focus:**

- Known overlapping lunar image pairs.
- Classical preprocessing.
- SIFT / RootSIFT feature detection.
- Descriptor matching with ratio test / cross-check where appropriate.
- RANSAC geometric verification.
- Affine or homography transformation.
- Registered overlay output.
- Baseline evaluation metrics.

**Expected outputs:** detected keypoints, candidate matches, verified
inliers, rejected outliers, transformation matrix, registered image,
check-point error, inlier statistics, spatial coverage, runtime.

V1 is intentionally simple, explainable, reproducible, and measurable. It
does not include deep-learning complexity.

### Benchmark V2 — Sensor-Aware and Multi-Scale

**Purpose:** Improve on the baseline by handling major physical differences
between lunar sensors.

**Potential workstreams:**

- Sensor identification and routing.
- Separate preprocessing paths for OHRC and TMC-2.
- Initial IIRS-friendly 2D representations.
- Multi-resolution reference pyramid.
- Ground-scale-aware comparison.
- Gradient / edge / structural representations.
- Illumination-stress handling.
- Improved spatial-coverage evaluation and failure rejection.

V2 results are compared against V1 on the same image pairs where possible, to
answer questions such as: Does sensor-aware preprocessing improve matching?
Does scale-aware processing improve robustness? How much improvement comes
from preprocessing versus matcher choice?

### Benchmark V3 — Advanced Matching and Retrieval

**Purpose:** Introduce stronger local matching and optional lunar-region
retrieval while retaining the V1/V2 benchmarks for comparison.

**Potential local matching approaches (evaluated, not assumed):**

- ALIKED + LightGlue
- LoFTR
- Remote-sensing-specific approaches (e.g. RIFT-inspired, CFOG-inspired
  methods) where feasible

Each candidate matcher earns its place through benchmark evidence on shared
pairs — comparisons are framed as experiments ("evaluate whether X improves
difficult benchmark cases"), not conclusions.

**Potential retrieval architecture:**

Offline:

```
Lunar reference imagery
        ↓
Tile generation
        ↓
Multi-scale tile pyramid
        ↓
Global descriptor extraction
        ↓
FAISS index + metadata index
```

Online:

```
Source image
        ↓
Global descriptor
        ↓
FAISS retrieval
        ↓
Top-K candidate regions
        ↓
Local matching
        ↓
Geometric verification
```

FAISS is used for vector indexing and search over global descriptors; it does
not itself extract lunar image features. Global retrieval features and local
matching features remain distinct concepts throughout the pipeline. Global
retrieval is treated as a separate workstream and is used only when overlap
location is not already constrained by metadata.

**Potential V3 metrics:** Recall@1, Recall@5, candidate ranking quality,
local match inlier count, inlier ratio, spatial coverage, check-point RMSE,
runtime.

### Benchmark V4 — Research-Grade Robust System

**Purpose:** Develop the strongest research configuration after earlier
versions establish what actually works, remaining scientifically measurable
rather than adding complexity for its own sake.

**Potential directions:**

- Robust multi-sensor routing.
- Improved IIRS handling.
- Local/piecewise geometric refinement.
- DEM-aware registration.
- Illumination-aware processing.
- Uncertainty estimation and confidence calibration.
- Automated quality gates and accept/refine/reject decision logic.
- Failure classification.
- Matcher selection informed by benchmark evidence.
- Ensemble approaches, only when experimentally justified.
- Scalable retrieval and reproducible benchmark runner/experiment tracking.

**Potential research questions:** Which features remain stable across large
Sun-angle differences? Which representations work best across sensor
modalities? When does a global homography stop being sufficient, and when is
DEM-aware or local warping required? Which matcher performs best for each
sensor pair? Can system confidence predict actual registration error? Can the
pipeline reliably reject incorrect registrations? How well does it generalize
to unseen lunar terrain?

---

## Target High-Level Pipeline

```
Source Image
      +
Reference Dataset
      ↓
Input Validation
      ↓
Metadata Extraction
      ↓
Sensor Identification
      ↓
Sensor-Aware Preprocessing
      ↓
Map Projection / Coordinate Handling
      ↓
Multi-Resolution Representation
      ↓
Metadata-Constrained Search  OR  Global Image Retrieval
      ↓
Top-K Candidate Regions
      ↓
Local Feature Matching
      ↓
Candidate Correspondences
      ↓
RANSAC / Geometric Verification
      ↓
Verified Inliers
      ↓
Spatial Coverage Analysis
      ↓
Residual Analysis
      ↓
Sub-Pixel Tie-Point Refinement
      ↓
Final Transform Refit
      ↓
Registration
      ↓
Independent Quality Evaluation
      ↓
Accept / Refine / Reject
      ↓
Georeferenced / Registered Output
      ↓
Optional Mosaic / Map / Visualization
```

The phases below describe how ChandraMap progresses toward this architecture
incrementally, from Phase 0 (foundation) through Phase 4 (V4), followed by
supporting infrastructure and downstream layers.

---

## Phase 0 — Project Foundation

**Status:** Planned

**Goal:** Establish the repository, tooling, and documentation scaffolding
needed to support reproducible research and engineering work.

**Planned Work:**

- Repository structure, Python packaging, dependency management.
- Configuration system, logging, error handling, environment configuration.
- Development tooling (linting, formatting, pre-commit hooks).
- Open-source project files: `README.md`, `LICENSE`, `CHANGELOG.md`,
  `ROADMAP.md`, `CONTRIBUTING.md`, `CODE_OF_CONDUCT.md`, `SECURITY.md`,
  `CITATION.cff`, `AGENTS.md`.
- Development setup files: `.gitignore`, `.editorconfig`, `.env.example`,
  `pyproject.toml`, `Makefile`, Docker configuration where useful.
- CI setup: linting, formatting, unit tests, integration tests, packaging
  checks.
- Foundational documentation: project context, terminology, architecture,
  data flow, dataset definitions, benchmark specification, evaluation
  methodology.

**Exit Criteria:**

- Repository builds and installs in a clean environment.
- CI runs linting and tests on every change.
- Core open-source documentation files exist and are internally consistent.

---

## Phase 1 — Benchmark V1: Classical Registration Baseline

**Status:** Planned

**Goal:** Deliver a simple, explainable, reproducible classical registration
baseline that produces a measurable number to beat.

**Planned Work (milestones):**

1. Define one valid source/reference image pair.
2. Load imagery.
3. Validate dimensions and metadata.
4. Normalize inputs.
5. Detect SIFT features.
6. Compute descriptors.
7. Generate candidate matches.
8. Apply filtering (e.g. ratio test / cross-check).
9. Estimate model using RANSAC.
10. Identify verified inliers.
11. Estimate final transform.
12. Warp/register the image.
13. Save visual outputs (matches, inliers, registered overlay).
14. Measure evaluation metrics.
15. Save benchmark results.

**Deliverables:** end-to-end V1 pipeline, saved candidate/verified matches,
saved transformation, registered output, benchmark result artifacts.

**Exit Criteria:**

- Pipeline executes end-to-end on the defined benchmark pair(s).
- Results are reproducible from a documented configuration.
- Candidate and verified matches, final transform, and registered output are
  all saved.
- Check-point error is measured where independent points are available.
- Failure cases are surfaced clearly rather than silently ignored.

**Dependencies:** Phase 0.

---

## Phase 2 — Benchmark V2: Sensor-Aware and Multi-Scale Registration

**Status:** Planned

**Goal:** Extend the V1 baseline to account for physical differences between
lunar sensors and across scales.

**Planned Work (workstreams):**

- OHRC preprocessing.
- TMC-2 preprocessing.
- IIRS experimentation (band selection, PCA, or other 2D representations).
- Illumination robustness.
- Multi-scale matching and reference pyramid generation.
- Sensor metadata parsing.
- Spatial coverage metrics.
- Failure analysis and initial stress tests.

**Deliverables:** sensor-specific preprocessing routes, multi-scale reference
pyramid, expanded evaluation metrics, V1-vs-V2 comparison results.

**Exit Criteria:**

- V2 is evaluated against V1 on shared benchmark pairs.
- Sensor-specific preprocessing paths are documented and validated
  independently for OHRC, TMC-2, and IIRS.
- Coverage and failure-rejection metrics are reported alongside accuracy
  metrics.

**Dependencies:** Phase 1.

---

## Phase 3 — Benchmark V3: Advanced Matching and Global Retrieval

**Status:** Planned

**Goal:** Evaluate stronger local matchers and, where useful, add global
lunar-region retrieval as a candidate-search mechanism — without assuming any
particular method is superior in advance.

**Planned Work:**

- Benchmark ALIKED + LightGlue, LoFTR, and other candidate matchers against
  the V1/V2 baselines on shared pairs.
- If global retrieval is pursued, build it as a separate workstream:
  1. Obtain/reference a lunar mosaic.
  2. Standardize projection.
  3. Generate reference tiles at multiple scales.
  4. Store tile metadata.
  5. Extract global descriptors.
  6. Build a FAISS index.
  7. Implement source-query descriptor extraction.
  8. Retrieve Top-K candidates.
  9. Measure Recall@K.
  10. Feed candidate tiles into local registration.
  11. Compare retrieval-assisted registration against metadata-constrained
      search.

**Deliverables:** matcher comparison report, (optional) retrieval index and
pipeline, Recall@K measurements, updated benchmark results.

**Exit Criteria:**

- Each evaluated matcher has benchmark evidence for or against inclusion.
- If retrieval is implemented, it is used only where metadata does not
  already constrain the search region, and its contribution is measured
  against metadata-constrained search.

**Dependencies:** Phase 2.

---

## Phase 4 — Benchmark V4: Research-Grade Robust Registration

**Status:** Planned

**Goal:** Build the most robust, confidence-aware registration configuration
justified by evidence from V1–V3, rather than by accumulating features.

**Planned Work:**

- Geometry: residual-vector inspection, affine vs. homography experiments,
  piecewise transforms, local warping, terrain/DEM-aware methods.
- Sub-pixel refinement pipeline:
  ```
  RANSAC → Verified Inliers → Local Tie-Point Refinement →
  Final Transform Refit → Independent Evaluation
  ```
- Confidence: quality score design, uncertainty estimation, failure
  prediction, confidence calibration.
- Decision system: **Accept / Refine / Reject**, with explicit rejection
  reasons (insufficient inliers, poor spatial distribution, excessive
  residual, unstable transform, inconsistent geometry, retrieval ambiguity,
  insufficient image information).

**Exit Criteria:**

- The system does not force a registration when evidence is weak.
- Confidence/quality scores are evaluated against actual registration error.
- V4 configuration is benchmarked against V1–V3 on the full stress-test
  suite (see Benchmarking Roadmap below).

**Dependencies:** Phase 3.

---

## Phase 5 — Benchmarking and Reproducibility Infrastructure

**Status:** Planned

**Goal:** Provide standardized, reproducible benchmarking across all
versions and stress conditions.

**Benchmark stress cases:**

- **Easy pair** — known overlap, similar illumination, manageable scale
  difference.
- **Sun-angle stress** — same region under substantially different
  illumination.
- **Scale stress** — large ground-resolution difference.
- **Modality stress** — IIRS-derived representation vs. visible reference
  imagery.
- **Geometry stress** — relief-rich terrain or strong viewing differences.
- **Low-feature terrain** — smooth or repetitive areas prone to false
  matches.
- **Retrieval stress** — unknown source location requiring Top-K candidate
  retrieval.

| Benchmark        | V1            | V2            | V3      | V4      |
| ---------------- | ------------- | ------------- | ------- | ------- |
| Easy pair        | Planned       | Planned       | Planned | Planned |
| Sun-angle stress | Planned       | Planned       | Planned | Planned |
| Scale stress     | Planned       | Planned       | Planned | Planned |
| Modality stress  | Planned       | Planned       | Planned | Planned |
| Geometry stress  | Planned       | Planned       | Planned | Planned |
| Retrieval stress | N/A / Planned | N/A / Planned | Planned | Planned |

**Evaluation metrics:**

- Local matching: candidate matches, verified inliers, inlier ratio.
- Distribution: grid coverage, convex-hull coverage.
- Registration: independent check-point RMSE, source-image pixel error,
  reprojection residuals.
- Retrieval: Recall@1, Recall@5, Top-K accuracy.
- System: runtime, memory usage where useful, success rate, failure rate.
- Geospatial: ground error in metres, reported only when product GSD,
  reference accuracy, and projection make that conversion scientifically
  meaningful.

**Methodological rule:** transform quality is not evaluated only on the
points used to estimate that transform when independent validation is
available.

**Reproducibility work:**

- Deterministic configurations where possible; experiment config files;
  random seeds.
- Dependency locking; dataset and benchmark-pair manifests.
- Model/matcher version tracking; hardware/environment recording.
- Output metadata; reproducible CLI commands; archived configs; dataset
  provenance.

Future CLI commands may conceptually resemble `chandramap register ...`,
`chandramap benchmark ...`, `chandramap retrieve ...`,
`chandramap evaluate ...` — these are illustrative and not implemented
unless stated otherwise in `CHANGELOG.md`.

**Dependencies:** runs alongside Phases 1–4; formalized once V1 exists.

---

## Data Roadmap

**Status:** Planned

- Dataset acquisition documentation (e.g. PRADAN, LROC data workflows).
- File-format inspection and metadata extraction.
- Map-projection inspection, GSD extraction, footprint extraction.
- Illumination metadata and viewing geometry extraction.
- Dataset manifests and checksums.
- Local caching with ignored raw-data directories.
- Small public example datasets included only where licensing permits.

Large raw datasets are not committed to Git. Data is not redistributed
without checking its license/data policy.

---

## Phase 6 — Backend and API

**Status:** Planned (begins after the core registration engine is
measurable)

**Potential future services:** dataset ingestion, registration jobs,
benchmark execution, retrieval queries, result storage, metric retrieval,
experiment history.

**Potential API categories (illustrative, not implemented):** `/health`,
`/images`, `/registration`, `/retrieval`, `/benchmarks`, `/results`.

Backend and API work is deliberately sequenced after Phases 1–5 so that
services are built on a validated, measurable core pipeline rather than
ahead of it.

**Dependencies:** Phase 5.

---

## Phase 7 — Visualization / Web Interface

**Status:** Planned

**Potential views:** source image, reference image, candidate
correspondences, RANSAC inliers, rejected matches, registered overlay,
residual vectors, quality metrics, benchmark comparison, Top-K candidate
locations, lunar coordinate display.

**Potential future map functionality:** lunar reference map, result markers,
layer controls, registered footprints, benchmark regions.

The web UI is a presentation and analysis layer. It is not prioritized ahead
of core pipeline validation.

**Dependencies:** Phase 6 (or a subset of it sufficient to serve results).

---

## Phase 8 — Mosaics and Lunar Mapping Demonstrations

**Status:** Planned

**Potential capabilities:** registered-image stitching, overlap management,
blending, seam handling, lunar map layers, footprint visualization.

Mosaic generation is treated as downstream functionality. Mosaic quality is
never a substitute for correspondence evaluation — a visually attractive
mosaic can still be built from incorrect registration.

**Dependencies:** Phase 4 (validated registration), Phase 7 (optional, for
presentation).

---

## Phase 9 — Research Extensions

**Status:** Proposed / Experimental

Research topics beyond the core V1–V4 architecture, explicitly experimental
and not guaranteed future features:

- Lunar illumination invariance.
- Crater-structure descriptors.
- Gradient-based and phase-based matching representations.
- Multimodal registration.
- Hyperspectral-to-structural representations for IIRS.
- Learned lunar descriptors.
- Local geometric models and DEM-assisted registration.
- Shadow-aware processing.
- Domain adaptation and self-supervised lunar feature learning.
- Uncertainty estimation and failure detection.

**Optional planetary extensions:** after the lunar system is stable, future
research may investigate generalization to other planetary imagery (e.g.
Mars, Venus). This is long-term research and does not distract from solving
lunar registration first.

**Dependencies:** Phase 4 and later, as capacity allows.

---

## Phase 10 — Stable Open-Source Release

**Status:** Planned

### Path to 1.0

Before ChandraMap reaches a stable `1.0.0` software release, the following
should hold:

- Reproducible installation.
- Stable package structure.
- Documented CLI/API.
- Validated V1 baseline, with at least one stable advanced pipeline
  (informed by V2/V3 results).
- Clearly supported sensor definitions (OHRC, TMC-2, IIRS; LRO NAC, LRO WAC).
- Standardized benchmark suite covering the stress cases in Phase 5.
- Reproducible datasets/manifests.
- Consistent, documented metrics.
- Automated tests and CI.
- Documented failure behavior.
- Stable output schema.
- Security documentation, contribution documentation, citation metadata.
- At least one fully reproducible end-to-end example.

A mature benchmark architecture (e.g. a complete Benchmark V4) and a stable
software API are different concepts. Benchmark V4 is not automatically
equated with software release `1.0.0`; the two are tracked and evaluated
independently.

**Dependencies:** Phases 0–9, to the extent each is required by the criteria
above.

---

## Testing Roadmap

**Status:** Planned

- **Unit tests:** preprocessing, feature utilities, coordinate conversions,
  transformation utilities, metric calculations, configuration validation.
- **Integration tests:** a known image pair through the full registration
  pipeline; retrieval → matching; result serialization.
- **Regression tests:** ensure future changes do not silently degrade
  benchmark performance.
- **Failure tests:** blank image, invalid file, corrupted image, unsupported
  sensor, insufficient matches, no overlap, degenerate transform, missing
  metadata, extreme scale mismatch.

## CI/CD Roadmap

**Status:** Planned

- Formatting, linting, and typing checks.
- Unit and integration tests.
- Package build verification.
- Documentation checks.
- Lightweight benchmark smoke tests using small fixtures/samples.

Full scientific benchmarks (large lunar datasets) run separately from normal
CI, not on every commit.

## Documentation Roadmap

**Status:** Planned

Planned documentation structure:

```
docs/
├── architecture/
├── benchmarks/
├── datasets/
├── development/
├── evaluation/
├── research/
├── api/
└── usage/
```

Planned documents include: architecture overview, complete pipeline
description, module map, data flow, sensor documentation, dataset
acquisition guidance, benchmark methodology, metric definitions, failure
modes, experiment methodology, model/matcher comparison reports, API
documentation, CLI usage, and deployment guidance.

---

## Risks and Research Challenges

| Risk                                           | Mitigation Direction                                                                                      |
| ---------------------------------------------- | --------------------------------------------------------------------------------------------------------- |
| Strong illumination/Sun-angle differences      | Illumination-aware representations; benchmark under Sun-angle stress before claiming robustness           |
| Large resolution gaps between sensors          | Multi-scale reference pyramid; scale-aware comparison                                                     |
| Insufficient image overlap                     | Explicit failure detection and rejection rather than forced registration                                  |
| Low-texture or repetitive terrain              | Dedicated low-feature-terrain benchmark case; spatial coverage checks                                     |
| Hyperspectral/visible modality mismatch (IIRS) | Registration-friendly 2D representations (band selection, PCA, composites), evaluated rather than assumed |
| Map projection differences                     | Explicit projection handling in preprocessing                                                             |
| Terrain-relief distortion                      | DEM-aware or local/piecewise geometric refinement, where justified                                        |
| Incomplete metadata                            | Fallback to image-based retrieval; metadata-constrained search preferred when available                   |
| Domain shift in learned matchers               | Benchmark terrestrial-trained matchers on lunar data before trusting them                                 |
| Ground-truth limitations                       | Prefer independent check points; state clearly when only fit-point error is available                     |
| Expensive whole-Moon retrieval                 | Use metadata-constrained search when possible; treat retrieval as a separate, optional workstream         |
| Limited compute resources                      | Lightweight CI fixtures; full benchmarks run separately from CI                                           |

No risk above is described as solved until it has been measured against the
relevant benchmark case.

## Success Criteria

A ChandraMap system is considered successful to the extent it can:

1. Accept supported lunar imagery.
2. Identify and validate sensor and metadata.
3. Find candidate overlap.
4. Generate reliable correspondences.
5. Reject geometrically inconsistent matches.
6. Maintain spatially distributed control points.
7. Refine tie points appropriately.
8. Estimate an appropriate transformation.
9. Register the image.
10. Quantify registration accuracy.
11. Detect low-confidence/failure cases.
12. Reproduce the result from documented configuration.
13. Compare results against baseline benchmarks.

Measurable accuracy is prioritized over visual appearance throughout.

## Definition of Done

A feature or research result is not considered complete merely because code
runs. It should be:

- Implemented
- Documented
- Tested
- Configurable
- Reproducible
- Benchmarked
- Defined in terms of its failure behavior
- Producing saved outputs and reported metrics
- Compared against an appropriate baseline
- Free of unsupported scientific claims

## Contribution Opportunities

Areas where contributors could help, at a project/milestone level (see
`CONTRIBUTING.md` for process details):

- Dataset tooling
- SIFT baseline (V1)
- Sensor-specific preprocessing (V2)
- IIRS representation experiments
- Advanced matching and retrieval (V3)
- Benchmarking and reproducibility infrastructure
- Testing
- Documentation
- Visualization

## Roadmap vs. Changelog vs. README

- **`ROADMAP.md`** (this file) — what is planned, proposed, being
  researched, or targeted.
- **`CHANGELOG.md`** — what has actually changed, release by release.
- **`README.md`** — what the project currently is and how to use it.

Completed work is recorded in `CHANGELOG.md`, not described here as though it
already exists. This roadmap is also kept at the project/milestone level;
fine-grained tasks belong in GitHub Issues (e.g. "Add validation for empty
descriptor arrays in `sift_matcher.py`" is an issue-level task, not a roadmap
item — "Establish reproducible V1 classical registration benchmark" is a
roadmap item).

---

_Status labels used in this document (Planned, Proposed, Experimental) reflect
the best available information at the time of writing. None of the phases
above are marked Complete; completion is recorded in `CHANGELOG.md` once
verified._
