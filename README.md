<div align="center">

# 🌕 ChandraMap

### Lunar Image Correspondence, Registration & Geospatial Localization

**A research-oriented computer vision system for matching and aligning multi-sensor lunar imagery across large differences in resolution, illumination, viewpoint, and sensor modality.**

<br>

![Python](https://img.shields.io/badge/Python-3.11+-3776AB?logo=python&logoColor=white)
![Computer Vision](https://img.shields.io/badge/Computer%20Vision-Lunar%20Registration-blue)
![Research](https://img.shields.io/badge/Status-Active%20Research-orange)
![Open Source](https://img.shields.io/badge/Open%20Source-Research%20Project-success)
![License](https://img.shields.io/badge/License-See%20LICENSE-lightgrey)

<br>

**Chandrayaan-2 OHRC · TMC-2 · IIRS × NASA LRO NAC/WAC**

[Overview](#-overview) •
[Problem](#-problem-statement) •
[Pipeline](#-system-pipeline) •
[Versions](#-benchmark-versions) •
[Benchmarks](#-benchmarking-and-evaluation) •
[Installation](#-installation) •
[Documentation](#-documentation) •
[Roadmap](#-roadmap)

</div>

---

## 🌌 Overview

**ChandraMap** is an open-source research and engineering project focused on **lunar image correspondence and registration**.

The main problem is simple to describe but difficult to solve:

> Given two images showing the same region of the Moon, determine which points correspond to the same physical locations and accurately align the images — even when they were captured by different instruments, at different resolutions, from different viewing geometries, and under different illumination conditions.

ChandraMap is designed around imagery such as:

- 🇮🇳 **Chandrayaan-2 OHRC**
- 🇮🇳 **Chandrayaan-2 TMC-2**
- 🇮🇳 **Chandrayaan-2 IIRS**
- 🇺🇸 **NASA LRO NAC**
- 🇺🇸 **NASA LRO WAC**
- 🇯🇵 **Kaguya / SELENE TC** as an optional future research source

The system is being developed as a **benchmarkable research platform**, where increasingly capable registration approaches can be compared under the same datasets, metrics, and evaluation conditions.

---

# 🎯 Problem Statement

Images of the same lunar location may look very different because of:

### ☀️ Illumination changes

The Moon has no atmosphere to diffuse sunlight.

A change in Sun angle can create:

- very different shadows,
- reversed-looking crater structures,
- large brightness changes,
- hidden or revealed terrain features.

This means normal brightness normalization alone cannot completely solve the problem.

---

### 🔍 Scale and resolution differences

Different lunar instruments observe the surface at dramatically different spatial resolutions.

| Instrument              |                    Approximate Spatial Scale | Primary Role                               |
| ----------------------- | -------------------------------------------: | ------------------------------------------ |
| **Chandrayaan-2 OHRC**  |                         ~0.25–0.32 m/pixel\* | Very high-resolution terrain imaging       |
| **Chandrayaan-2 TMC-2** |                                   ~5 m/pixel | Terrain mapping                            |
| **Chandrayaan-2 IIRS**  |                                  ~80 m/pixel | Hyperspectral / mineralogical observations |
| **LRO NAC**             | commonly ~0.5–2 m/pixel depending on product | High-resolution reference imagery          |
| **LRO WAC**             |           lower-resolution wide-area imagery | Large-scale lunar context                  |

> \*Exact values depend on the product and official documentation used. Product metadata should remain the final authority.

A critical design principle of ChandraMap is:

> **Resizing an image does not create missing physical detail.**

For example, upscaling an ~80 m/pixel IIRS representation to match a sub-meter reference creates more pixels, but it does **not** create new terrain information.

ChandraMap therefore uses **physically meaningful multi-scale comparison** instead of treating scale differences as a normal image-resize problem.

---

### 📷 Viewpoint and geometric differences

Images may also differ because of:

- spacecraft viewing angle,
- terrain relief,
- orbital geometry,
- map projection,
- sensor geometry,
- perspective differences.

A single homography may work for some local map-projected image pairs, but it is not guaranteed to explain every lunar registration problem.

---

### 🌈 Sensor modality differences

Not every instrument produces the same type of imagery.

OHRC and TMC-2 are primarily useful as panchromatic terrain images.

IIRS is fundamentally different:

> **IIRS is an imaging infrared hyperspectral spectrometer, not simply a lower-resolution grayscale camera.**

A normal 2D feature matcher should therefore not blindly process an entire hyperspectral cube.

Possible IIRS representations include:

- selected spectral bands,
- PCA components,
- spectral composites,
- structural representations,
- gradient-based representations.

These approaches must be tested experimentally.

---

# 🧠 Core Research Question

ChandraMap investigates:

> **How can reliable lunar correspondences be obtained across differences in sensor modality, spatial resolution, Sun angle, viewing geometry, and image scale while producing measurable and scientifically interpretable registration quality?**

---

# 📥 Inputs

A typical ChandraMap experiment contains:

```text
Source Lunar Image
│
├── OHRC
├── TMC-2
└── IIRS-derived representation

+

Reference Lunar Image
│
├── LRO NAC
└── LRO WAC / other reference product

+

Optional Metadata
│
├── Latitude / Longitude
├── Image Footprint
├── Ground Sample Distance
├── Map Projection
├── Sun Geometry
├── Viewing Geometry
└── Sensor Information
```

Metadata is used when available.

Using valid geospatial metadata to reduce the search region is **good engineering**, not cheating.

A pure image-based retrieval path can be used when reliable geolocation is unavailable.

---

# 📤 Core Outputs

The main output of ChandraMap is **not simply a lunar mosaic**.

The core outputs are:

### 📍 1. Corresponding Points

Pairs of coordinates representing the same physical lunar features.

```text
Source Image                 Reference Image

(x₁, y₁)  ───────────────→  (x₁', y₁')
(x₂, y₂)  ───────────────→  (x₂', y₂')
(x₃, y₃)  ───────────────→  (x₃', y₃')
...
```

---

### ✅ 2. Verified Inliers

Candidate matches are geometrically verified.

Bad or inconsistent matches are rejected before the final transformation is estimated.

---

### 🧮 3. Transformation Model

Depending on the experiment:

- affine transform,
- homography,
- local/piecewise transformation,
- map-based transformation,
- terrain-aware refinement.

---

### 🗺️ 4. Registered Image

The source image is transformed into alignment with the reference image.

---

### 📍 5. Geospatial Location

When valid projection and reference metadata are available, image coordinates can be related to:

- latitude,
- longitude,
- lunar map coordinates.

---

### 📊 6. Quality Metrics

Registration quality should be measurable using metrics such as:

- inlier count,
- inlier ratio,
- reprojection residuals,
- independent check-point RMSE,
- spatial coverage,
- retrieval Recall@K,
- runtime,
- failure rate,
- ground error when meaningful.

---

### 🌕 7. Lunar Mosaic / Map

Mosaics and interactive lunar maps are useful downstream demonstrations.

However:

> **Correspondence and registration remain the core research objective.**

---

# 🏗️ System Architecture

ChandraMap separates the system into two major stages:

```text
┌─────────────────────────────────────────────────────────┐
│              OFFLINE REFERENCE PREPARATION              │
└─────────────────────────────────────────────────────────┘

LRO / Reference Imagery
        │
        ▼
Map Projection / Standardization
        │
        ▼
Reference Pyramid
        │
        ▼
Tiles at Multiple Scales
        │
        ├──────────────► Geospatial Metadata Index
        │
        ▼
Global Descriptor Extraction
        │
        ▼
Vector Search Index
        │
        ▼
Searchable Lunar Reference Database


┌─────────────────────────────────────────────────────────┐
│                 ONLINE REGISTRATION                     │
└─────────────────────────────────────────────────────────┘

OHRC / TMC-2 / IIRS
        │
        ▼
Sensor Identification
        │
        ▼
Sensor-Aware Preprocessing
        │
        ▼
Metadata Search OR Image Retrieval
        │
        ▼
Top-K Candidate Reference Regions
        │
        ▼
Multi-Scale Comparison
        │
        ▼
Local Feature Matching
        │
        ▼
Candidate Matches
        │
        ▼
Geometric Verification
        │
        ▼
Verified Inliers
        │
        ▼
Sub-Pixel Tie-Point Refinement
        │
        ▼
Refit Final Transformation
        │
        ▼
Independent Quality Evaluation
        │
        ▼
Registered Image + Metrics + Geolocation
```

---

# 🔬 System Pipeline

## Stage 1 — 📥 Data Ingestion

Load:

- source imagery,
- reference imagery,
- metadata,
- projection information,
- sensor information.

The system should preserve scientifically useful metadata instead of stripping it during preprocessing.

---

## Stage 2 — 🛰️ Sensor Routing

Different sensors follow different preprocessing paths.

```text
                  INPUT
                    │
          ┌─────────┼─────────┐
          │         │         │
        OHRC      TMC-2      IIRS
          │         │         │
          ▼         ▼         ▼
        Path A    Path B     Path C
          │         │         │
          └─────────┼─────────┘
                    ▼
        Common Structural Space
```

ChandraMap intentionally avoids forcing every sensor through one identical pipeline.

---

## Stage 3 — 🧹 Sensor-Aware Preprocessing

### OHRC / TMC-2

Potential experiments include:

- light denoising,
- radiometric normalization,
- local contrast normalization,
- gradient extraction,
- edge representations,
- structural descriptors,
- map projection.

Preprocessing should only remain in the pipeline when benchmark results show that it helps.

---

### IIRS

The IIRS path is separate.

Candidate representations can include:

```text
IIRS Hyperspectral Cube
        │
        ├── Selected Band
        ├── PCA Component
        ├── Spectral Composite
        ├── Gradient Representation
        └── Structural Representation
                │
                ▼
        Registration-Friendly 2D Image
```

The representation providing the most stable terrain correspondence should be selected through experiments.

---

## Stage 4 — 🔎 Search-Space Reduction

There are two modes.

### Mode A — Metadata-Assisted Search

If reliable:

- footprint,
- latitude/longitude,
- projection,
- image geometry

are available, ChandraMap can restrict the search area directly.

### Mode B — Global Image Retrieval

If the source location is unknown:

```text
Source Image
     │
     ▼
Global Descriptor
     │
     ▼
Vector Similarity Search
     │
     ▼
Top-K Candidate Lunar Tiles
```

A vector database such as **FAISS** can perform similarity search.

Important distinction:

```text
GLOBAL RETRIEVAL

Global Descriptor
      ↓
FAISS
      ↓
Candidate Region


LOCAL REGISTRATION

Local Features
      ↓
Feature Matcher
      ↓
Corresponding Points
```

FAISS finds candidate regions.

It does **not** perform geometric image registration.

---

## Stage 5 — 🔬 Multi-Scale Search

Reference imagery is compared at physically meaningful ground scales.

```text
High-Resolution Reference
        │
        ├── Scale 0
        ├── Scale 1
        ├── Scale 2
        ├── Scale 3
        └── Scale N
```

Higher-resolution imagery should generally be downsampled toward the effective ground scale of the coarser source for initial comparison.

Fine refinement is performed only when the source actually contains enough spatial information.

---

# 🔗 Local Matching

ChandraMap uses multiple matching approaches as **benchmark alternatives**, not necessarily as one giant combined matcher.

## Path A — SIFT

```text
SIFT
 ↓
Descriptor Matching
 ↓
Ratio / Cross-Check Filtering
 ↓
Candidate Matches
```

SIFT provides:

- a strong classical baseline,
- scale awareness,
- rotation awareness,
- explainability,
- easy benchmarking.

---

## Path B — ALIKED + LightGlue

```text
ALIKED
   │
   ▼
Sparse Features
   │
   ▼
LightGlue
   │
   ▼
Candidate Matches
```

This represents a modern learned sparse matching pipeline.

It must still be benchmarked against lunar imagery because models trained primarily on terrestrial imagery are not automatically invariant to lunar domain differences.

---

## Path C — LoFTR

```text
Image A + Image B
        │
        ▼
      LoFTR
        │
        ▼
Dense / Semi-Dense Correspondences
```

LoFTR is detector-free and can be useful when reliable keypoints are difficult to detect.

It is a **matcher**, not merely a feature extractor.

---

## Research Paths

Future experiments may include:

- RIFT,
- CFOG,
- multimodal remote-sensing registration methods,
- lunar-specific learned descriptors,
- terrain-aware matching,
- DEM-assisted matching.

---

# 🛡️ Geometric Verification

Raw feature matches cannot be trusted directly.

ChandraMap applies geometric verification:

```text
Candidate Matches
       │
       ▼
     RANSAC
       │
       ├── Reject Outliers
       │
       ▼
Initial Geometric Model
       │
       ▼
Verified Inliers
```

Possible models include:

- affine transformation,
- homography.

More complex models should only be introduced when residual analysis shows that a simple model is insufficient.

---

# 🎯 Sub-Pixel Refinement

The correct processing order is:

```text
Candidate Matches
       ↓
RANSAC
       ↓
Verified Inliers
       ↓
Sub-Pixel Tie-Point Refinement
       ↓
Refit Final Transformation
       ↓
Registered Image
```

Sub-pixel refinement should improve already-verified control points.

The system should **not** refine obvious outliers before geometric verification.

---

# 📐 Geometry Reality

The Moon is not a flat poster.

A single homography may work well when:

- the region is relatively small,
- imagery is already map-projected,
- viewpoint differences are moderate.

For more difficult cases, ChandraMap can investigate:

- local residual modelling,
- piecewise warping,
- DEM-assisted registration,
- sensor geometry,
- planetary photogrammetry tools.

A flexible warp must not be used merely to hide weak correspondences.

---

# 📊 Benchmarking and Evaluation

ChandraMap is structured around reproducible benchmarking.

A better algorithm is accepted only when it demonstrates measurable improvement.

---

## Core Metrics

| Stage        | Metric                    | Purpose                                                 |
| ------------ | ------------------------- | ------------------------------------------------------- |
| Retrieval    | **Recall@1**              | Is the correct region ranked first?                     |
| Retrieval    | **Recall@5**              | Is the correct region in the Top-5?                     |
| Matching     | **Candidate Match Count** | Number of proposed correspondences                      |
| Verification | **Inlier Count**          | Number of geometrically consistent matches              |
| Verification | **Inlier Ratio**          | Fraction of proposed matches that survive verification  |
| Distribution | **Grid Coverage**         | Whether matches are distributed over the overlap        |
| Distribution | **Convex-Hull Coverage**  | Spatial area covered by reliable matches                |
| Registration | **Check-Point RMSE**      | Error on points not used to estimate the transformation |
| Geospatial   | **Ground Error**          | Physical error where reliable GSD/projection exists     |
| System       | **Runtime**               | Computational cost                                      |
| System       | **Failure Rate**          | Robustness across difficult image pairs                 |

---

## ⚠️ Evaluation Rule

The same points should not be used both to:

1. estimate the transformation, and
2. claim final registration accuracy.

Where possible:

```text
Tie / Training Control Points
          │
          ▼
  Estimate Transformation


Independent Check Points
          │
          ▼
     Measure Error
```

This produces a more meaningful estimate of real registration accuracy.

---

# 🧪 Stress-Test Matrix

ChandraMap should not be evaluated only on easy examples.

| Test                   | Purpose                                                  |
| ---------------------- | -------------------------------------------------------- |
| 🟢 Easy Pair           | Verify complete pipeline operation                       |
| ☀️ Sun-Angle Stress    | Test shadow/illumination robustness                      |
| 🔍 Scale Stress        | Test large GSD differences                               |
| 🌈 Modality Stress     | Test IIRS-derived vs visible imagery                     |
| 📐 Geometry Stress     | Test viewpoint and relief differences                    |
| 🌑 Low-Feature Terrain | Measure false-match behaviour                            |
| 💥 Failure Case        | Verify reliable rejection instead of forced registration |

A scientifically useful system should be able to say:

> **“Registration rejected — confidence insufficient.”**

rather than always returning an apparently successful result.

---

# 🧬 Benchmark Versions

ChandraMap is developed as a **four-version benchmark architecture**.

Each version should remain independently runnable so improvements can be measured against earlier versions.

---

## 🟢 V1 — Classical Baseline

**Goal:** Establish a simple, reproducible baseline.

```text
Known Image Pair
      ↓
Preprocessing
      ↓
SIFT
      ↓
Descriptor Matching
      ↓
RANSAC
      ↓
Affine / Homography
      ↓
Registered Image
      ↓
Metrics
```

### Primary purpose

Answer:

> How well can a standard classical computer-vision pipeline solve lunar registration?

V1 establishes the number future versions must beat.

---

## 🔵 V2 — Sensor-Aware Multi-Scale Registration

Adds:

- sensor-specific preprocessing,
- resolution-aware comparison,
- reference pyramids,
- structural representations,
- improved coverage analysis.

```text
Sensor Route
    ↓
Sensor Preprocessing
    ↓
Multi-Scale Reference
    ↓
SIFT / Selected Matcher
    ↓
Geometry
    ↓
Evaluation
```

### Research question

> Does sensor-aware preprocessing and physically meaningful scale handling improve registration?

---

## 🟣 V3 — Learned / Advanced Correspondence

Adds experiments with:

- ALIKED + LightGlue,
- LoFTR,
- multimodal matching approaches,
- improved correspondence filtering,
- difficult illumination conditions.

### Research question

> Can modern matching methods outperform the classical baseline on difficult lunar pairs?

Algorithms remain only if measured results justify their inclusion.

---

## 🔴 V4 — Advanced Lunar-Aware Registration

Research-focused version.

Potential additions include:

- lunar-specific feature learning,
- DEM-assisted geometry,
- local/piecewise registration,
- photometric modelling,
- multi-sensor fusion,
- learned confidence estimation,
- uncertainty modelling,
- failure prediction.

### Research question

> How far can lunar-specific geometric and sensor knowledge improve correspondence reliability beyond generic computer-vision methods?

---

# 📈 Benchmark Philosophy

The main comparison should resemble:

| Method               | RMSE ↓ | Inlier Ratio ↑ | Coverage ↑ | Success Rate ↑ | Runtime ↓ |
| -------------------- | -----: | -------------: | ---------: | -------------: | --------: |
| V1 Classical         |    TBD |            TBD |        TBD |            TBD |       TBD |
| V2 Sensor-Aware      |    TBD |            TBD |        TBD |            TBD |       TBD |
| V3 Advanced Matching |    TBD |            TBD |        TBD |            TBD |       TBD |
| V4 Lunar-Aware       |    TBD |            TBD |        TBD |            TBD |       TBD |

> **No fabricated benchmark numbers are used.**

Results should be added only after experiments are reproducible.

---

# 🗂️ Project Structure

The repository is organized to keep research code, applications, benchmarks, documentation, data definitions, experiments, and generated artifacts separate.

```text
ChandraMap/
│
├── README.md
├── LICENSE
├── CITATION.cff
├── CODE_OF_CONDUCT.md
├── CONTRIBUTING.md
├── SECURITY.md
├── CHANGELOG.md
├── ROADMAP.md
├── AGENTS.md
│
├── .github/
│   ├── workflows/
│   ├── ISSUE_TEMPLATE/
│   └── PULL_REQUEST_TEMPLATE.md
│
├── .ai/
│   ├── context/
│   ├── architecture/
│   ├── development/
│   └── research/
│
├── apps/
│   ├── web/
│   └── demo/
│
├── services/
│   └── api/
│
├── src/
│   └── chandramap/
│       ├── data/
│       ├── preprocessing/
│       ├── sensors/
│       ├── retrieval/
│       ├── features/
│       ├── matching/
│       ├── geometry/
│       ├── refinement/
│       ├── registration/
│       ├── geospatial/
│       ├── metrics/
│       └── visualization/
│
├── configs/
│   ├── sensors/
│   ├── pipelines/
│   ├── experiments/
│   └── benchmarks/
│
├── benchmarks/
│   ├── v1/
│   ├── v2/
│   ├── v3/
│   └── v4/
│
├── experiments/
│
├── research/
│
├── notebooks/
│
├── data/
│   ├── raw/
│   ├── interim/
│   ├── processed/
│   ├── reference/
│   └── samples/
│
├── results/
│
├── artifacts/
│
├── scripts/
│
├── tests/
│   ├── unit/
│   ├── integration/
│   └── benchmark/
│
├── docs/
│   ├── getting-started/
│   ├── architecture/
│   ├── pipeline/
│   ├── sensors/
│   ├── datasets/
│   ├── benchmarking/
│   ├── experiments/
│   ├── research/
│   └── development/
│
└── deploy/
```

---

# 🛠️ Technology Stack

ChandraMap may use the following technologies depending on the benchmark version.

### 🐍 Core

- Python
- NumPy
- SciPy
- Pandas

### 👁️ Computer Vision

- OpenCV
- SIFT
- feature matching
- RANSAC
- geometric transformations

### 🤖 Deep / Learned Matching

Research modules may use:

- PyTorch
- ALIKED
- LightGlue
- LoFTR

### 🌍 Geospatial Processing

Potential tooling includes:

- GDAL
- Rasterio
- GeoPandas
- PROJ
- planetary mapping utilities
- USGS ISIS where appropriate

### 🔎 Retrieval

Potential components:

- FAISS
- global visual descriptors
- tile metadata indices

### 🌐 Backend / Application Layer

Depending on the project stage:

- FastAPI
- REST APIs
- asynchronous processing
- experiment/result services

### 🖥️ Frontend

The visualization layer can display:

- uploaded lunar imagery,
- candidate reference regions,
- matched points,
- rejected matches,
- overlays,
- quality metrics,
- geospatial results.

The UI is a demonstration layer and does not replace quantitative evaluation.

---

# 📚 Data Sources

## 🇮🇳 Chandrayaan-2

Primary source imagery can include:

### OHRC

**Orbiter High Resolution Camera**

Best suited to:

- detailed terrain,
- fine correspondence,
- crater structure,
- high-resolution registration experiments.

---

### TMC-2

**Terrain Mapping Camera-2**

Approximately ~5 m/pixel.

Useful for:

- structural lunar features,
- terrain mapping,
- medium-scale correspondence,
- multi-scale experiments.

---

### IIRS

**Imaging Infrared Spectrometer**

Approximately ~80 m/pixel with roughly 250 contiguous spectral bands over approximately 0.8–5.0 µm.

Useful for:

- hyperspectral experiments,
- cross-modality registration,
- coarse structural correspondence.

It requires its own preprocessing/representation strategy.

---

## 🇺🇸 NASA Lunar Reconnaissance Orbiter

### LRO NAC

Useful as a high-resolution lunar reference product.

### LRO WAC

Useful for:

- wider lunar coverage,
- lower-resolution reference imagery,
- large-scale context,
- coarse localization experiments.

---

# 🧭 Development Strategy

ChandraMap follows one central engineering rule:

> **Build the smallest measurable system first.**

The recommended development sequence is:

```text
1. One known overlapping pair
        ↓
2. SIFT + RANSAC baseline
        ↓
3. Independent accuracy measurement
        ↓
4. Multi-scale handling
        ↓
5. Sensor-aware preprocessing
        ↓
6. Small reference retrieval database
        ↓
7. Advanced matcher benchmark
        ↓
8. Sub-pixel refinement
        ↓
9. More sensors
        ↓
10. Larger lunar-scale experiments
        ↓
11. UI / map demonstrations
```

The project should not begin by attempting to solve the entire Moon globally.

A correct, measurable registration of one real image pair is more valuable than a large architecture that has never been validated.

---

# 🚀 Installation

> Installation instructions may evolve as the implementation matures.

Clone the repository:

```bash
git clone https://github.com/<YOUR_USERNAME>/ChandraMap.git
cd ChandraMap
```

Create a virtual environment:

```bash
python -m venv .venv
```

Activate it.

### Linux / macOS

```bash
source .venv/bin/activate
```

### Windows PowerShell

```powershell
.venv\Scripts\Activate.ps1
```

Install the project dependencies according to the dependency configuration used by the repository.

For example:

```bash
pip install -e .
```

For development dependencies:

```bash
pip install -e ".[dev]"
```

> Update these commands if the project ultimately uses Poetry, uv, Conda, or another dependency-management workflow.

---

# ▶️ Usage

The final command-line interface should expose independent stages instead of hiding the entire research pipeline behind one opaque command.

Conceptually:

```bash
chandramap register \
  --source path/to/source.tif \
  --reference path/to/reference.tif \
  --config configs/pipelines/v1.yaml
```

Benchmark execution should use version-specific configurations:

```bash
chandramap benchmark \
  --version v1 \
  --dataset configs/benchmarks/lunar_pairs.yaml
```

> The commands above represent the intended interface. Keep only commands that are actually implemented in the repository.

---

# 📊 Example Result Structure

A registration experiment should save enough information to reproduce and inspect the result.

```text
results/
└── experiment_001/
    ├── config.yaml
    ├── metadata.json
    ├── candidate_matches.csv
    ├── verified_inliers.csv
    ├── transform.json
    ├── metrics.json
    ├── matches.png
    ├── inliers.png
    ├── registered.png
    ├── overlay.png
    └── report.md
```

Example metrics:

```json
{
  "candidate_matches": null,
  "inlier_count": null,
  "inlier_ratio": null,
  "coverage_score": null,
  "checkpoint_rmse_px": null,
  "ground_error_m": null,
  "runtime_seconds": null,
  "status": "not_measured"
}
```

Results should remain `null`, `TBD`, or clearly marked as examples until experimentally measured.

---

# 🧪 Reproducibility

Every important experiment should record:

- dataset identifiers,
- source image,
- reference image,
- image dimensions,
- GSD,
- preprocessing configuration,
- matcher,
- model parameters,
- RANSAC threshold,
- random seed where relevant,
- software version,
- hardware,
- runtime,
- metric definitions,
- output artifacts.

The same image pairs should be reused when comparing versions whenever possible.

---

# ✅ Testing

The repository should include:

### Unit tests

For isolated modules such as:

- transformations,
- coordinate conversion,
- feature filtering,
- metrics,
- configuration loading.

### Integration tests

For complete flows such as:

```text
Load Pair
   ↓
Match
   ↓
Verify
   ↓
Transform
   ↓
Evaluate
```

### Benchmark tests

Used to detect regressions in:

- accuracy,
- retrieval performance,
- runtime,
- reliability.

Run tests with:

```bash
pytest
```

---

# 📖 Documentation

Project documentation lives under:

```text
docs/
```

Important documentation areas include:

| Documentation           | Purpose                           |
| ----------------------- | --------------------------------- |
| `docs/getting-started/` | Setup and first experiment        |
| `docs/architecture/`    | System architecture               |
| `docs/pipeline/`        | Registration pipeline             |
| `docs/sensors/`         | OHRC, TMC-2, IIRS and LRO details |
| `docs/datasets/`        | Data acquisition and preparation  |
| `docs/benchmarking/`    | Metrics and benchmark protocol    |
| `docs/experiments/`     | Experiment documentation          |
| `docs/research/`        | Research notes and papers         |
| `docs/development/`     | Developer documentation           |

---

# ⚠️ Scientific Limitations

ChandraMap explicitly avoids several misleading claims.

### Upsampling ≠ detail recovery

Increasing the dimensions of an IIRS-derived image does not create high-resolution terrain information.

### More matches ≠ better registration

Hundreds of clustered incorrect correspondences can be worse than a smaller number of reliable, well-distributed matches.

### Learned matcher ≠ lunar invariant

A model trained primarily on terrestrial imagery is not automatically robust to lunar images.

### Contrast normalization ≠ Sun-angle invariance

Brightness normalization cannot completely remove physical shadow changes caused by different illumination geometry.

### Good overlay ≠ accurate registration

A visually convincing warped image can still contain incorrect control points.

### Sub-pixel ≠ sub-meter

Sub-pixel error depends on the source sensor.

For example:

```text
0.2 pixels × 5 m/pixel

is physically very different from

0.2 pixels × 80 m/pixel.
```

Ground accuracy should only be reported when the required metadata and reference truth make the conversion meaningful.

---

# 🗺️ Roadmap

The high-level research progression is:

### Phase 1 — Baseline

- [ ] Establish real lunar image pairs
- [ ] Implement SIFT baseline
- [ ] Implement geometric verification
- [ ] Produce registered overlay
- [ ] Implement independent metrics

### Phase 2 — Multi-Scale

- [ ] Build reference image pyramid
- [ ] Compare physically meaningful scales
- [ ] Add scale stress benchmark

### Phase 3 — Sensor Awareness

- [ ] OHRC preprocessing path
- [ ] TMC-2 preprocessing path
- [ ] IIRS representation experiments
- [ ] Sensor-specific benchmark results

### Phase 4 — Retrieval

- [ ] Tile reference imagery
- [ ] Generate global descriptors
- [ ] Build vector search index
- [ ] Measure Recall@K

### Phase 5 — Advanced Matching

- [ ] ALIKED + LightGlue experiments
- [ ] LoFTR experiments
- [ ] Remote-sensing matcher experiments
- [ ] Compare against SIFT

### Phase 6 — Fine Registration

- [ ] Sub-pixel tie-point refinement
- [ ] Final transformation refitting
- [ ] Residual-field analysis
- [ ] Local/piecewise refinement experiments

### Phase 7 — Application Layer

- [ ] Backend API
- [ ] Experiment dashboard
- [ ] Match visualization
- [ ] Registration overlay
- [ ] Geospatial visualization
- [ ] Optional lunar mosaic demonstration

See [`ROADMAP.md`](ROADMAP.md) for the complete development roadmap.

---

# 🔐 Security

Do not commit:

- credentials,
- access tokens,
- private dataset credentials,
- `.env` files,
- cloud secrets,
- deployment keys.

See [`SECURITY.md`](SECURITY.md) for the project's security policy.

---

# 🤝 Contributing

Contributions are welcome as the project grows.

Useful contribution areas include:

- computer vision,
- remote sensing,
- lunar science,
- geospatial engineering,
- feature matching,
- planetary image processing,
- benchmarking,
- backend development,
- visualization,
- documentation.

Before contributing, read:

- [`CONTRIBUTING.md`](CONTRIBUTING.md)
- [`CODE_OF_CONDUCT.md`](CODE_OF_CONDUCT.md)

---

# 📜 Citation

If ChandraMap becomes useful to your research, project, or experiment, citation metadata will be maintained in:

[`CITATION.cff`](CITATION.cff)

---

# 📝 Changelog

Project changes and release history are documented in:

[`CHANGELOG.md`](CHANGELOG.md)

---

# 📄 License

This project is distributed under the terms specified in the [`LICENSE`](LICENSE) file.

Dataset licenses remain governed by their respective data providers.

Chandrayaan, ISRO, NASA, LRO, LROC, and other mission names remain the property of their respective organizations.

---

# ⚖️ Project Disclaimer

ChandraMap is an independent open-source research/portfolio project.

It is **not an official ISRO, NASA, LROC, USGS, or Chandrayaan software product**.

References to space-agency missions and instruments describe datasets and research applications only.

---

# 💡 Project Philosophy

ChandraMap is not designed around adding as many algorithms as possible.

The project follows a simpler rule:

> **Build → Measure → Compare → Keep what works.**

Every major component should answer a measurable question.

```text
Does sensor-aware preprocessing help?

Does multi-scale matching reduce failures?

Does LightGlue outperform SIFT?

Does sub-pixel refinement reduce independent check-point error?

Does the system know when registration has failed?
```

If an algorithm does not improve a meaningful benchmark, it does not automatically deserve a place in the final pipeline.

---

# 🌕 Long-Term Vision

The long-term objective is to turn ChandraMap into a reproducible research platform for:

```text
Lunar Image Retrieval
        +
Multi-Sensor Correspondence
        +
Geometric Verification
        +
Precise Registration
        +
Geospatial Localization
        +
Benchmarking
        ↓
Reliable Lunar Mapping Infrastructure
```

The aim is not merely to produce attractive Moon maps.

The aim is to understand:

> **Where two lunar observations correspond, how accurately they can be aligned, how confident that alignment is, and why the system succeeds or fails.**

---

<div align="center">

## 🌑 ChandraMap

### **Matching the Moon — one reliable correspondence at a time.**

Made for research, experimentation, benchmarking, and planetary computer vision.

⭐ If this project helps your work, consider starring the repository.

</div>
