# ChandraMap Benchmarks

> **Benchmark specification for lunar image correspondence and registration**

ChandraMap is evaluated as a **lunar image correspondence and registration system**, not merely as a lunar mosaic generator. The benchmark measures whether imagery from different lunar sensors can be reliably matched, geometrically verified, registered, and evaluated under differences in sensor modality, spatial resolution, illumination, viewpoint, scale, and terrain appearance.

**Current benchmark status:** `Proposed benchmark specification`
**Measured results:** No numerical benchmark results are claimed in this document unless explicitly populated from an experiment.

---

## Table of Contents

- [Overview](#overview)
- [Benchmark Objectives](#benchmark-objectives)
- [What We Measure](#what-we-measure)
- [Benchmark Scope](#benchmark-scope)
- [Dataset and Benchmark Cases](#dataset-and-benchmark-cases)
- [Sensor-Specific Considerations](#sensor-specific-considerations)
- [Evaluation Protocol](#evaluation-protocol)
- [Metrics](#metrics)
- [Baselines](#baselines)
- [Benchmark Categories](#benchmark-categories)
  - [End-to-End Registration](#end-to-end-registration)
  - [Global Retrieval](#global-retrieval)
  - [Local Matching](#local-matching)
  - [Geometric Verification](#geometric-verification)
  - [Sub-Pixel Refinement](#sub-pixel-refinement)
  - [Sensor-Specific Evaluation](#sensor-specific-evaluation)
  - [Scale Stress](#scale-stress)
  - [Illumination Stress](#illumination-stress)
  - [Modality Stress](#modality-stress)
  - [Geometry Stress](#geometry-stress)
  - [Low-Feature and Repetitive Terrain](#low-feature-and-repetitive-terrain)

- [Stress-Test Matrix](#stress-test-matrix)
- [Ablation Studies](#ablation-studies)
- [Benchmark Result Tables](#benchmark-result-tables)
- [Reproducibility](#reproducibility)
- [Benchmark Artifacts](#benchmark-artifacts)
- [Failure Analysis](#failure-analysis)
- [Benchmark Workflow](#benchmark-workflow)
- [Proposed Benchmark Progression](#proposed-benchmark-progression)
- [Dataset and Repository Structure](#dataset-and-repository-structure)
- [Reporting Guidelines](#reporting-guidelines)
- [Benchmark Integrity Rules](#benchmark-integrity-rules)
- [Current Results](#current-results)
- [References and Source Material](#references-and-source-material)

---

## Overview

ChandraMap addresses correspondence between lunar images acquired by different sensors and under different imaging conditions.

The benchmark therefore focuses on the complete chain:

```text
Source Image
    │
    ▼
Sensor-Aware Preprocessing
    │
    ▼
Multi-Scale Representation
    │
    ▼
Metadata-Constrained Search
    │
    ├── Global Retrieval ──► Top-K Candidate Regions
    │
    └── Known Overlap ─────► Candidate Region
                              │
                              ▼
                       Local Matching
                              │
                              ▼
                    Candidate Correspondences
                              │
                              ▼
                    Geometric Verification
                              │
                              ▼
                       Verified Inliers
                              │
                              ▼
                    Sub-Pixel Refinement
                              │
                              ▼
                    Final Transformation
                              │
                              ▼
                    Registration Evaluation
                              │
              ┌───────────────┼────────────────┐
              ▼               ▼                ▼
        Check-Point       Coverage          Runtime
            RMSE          Metrics           / Failure
```

The primary research outputs are:

- candidate correspondences
- verified inlier correspondences
- transformation/model
- registered image
- residual/error measurements
- spatial coverage
- geospatial information where supported
- runtime
- failure information

A final lunar mosaic may be generated as a downstream demonstration, but **mosaic quality must not replace correspondence and registration evaluation**.

---

## Benchmark Objectives

The benchmark is designed to answer measurable questions rather than demonstrate only visual alignment.

### Primary objectives

1. Determine whether the system can establish reliable correspondence between overlapping lunar images.
2. Measure registration accuracy independently of the points used to fit the transformation.
3. Measure whether verified matches are spatially distributed across the overlap.
4. Measure the effect of sensor-aware preprocessing.
5. Measure the effect of multi-scale processing.
6. Measure robustness to illumination and Sun-angle changes.
7. Measure robustness to cross-sensor and cross-modality differences.
8. Compare simple and advanced local matching methods on identical image pairs.
9. Measure the effect of geometric verification and sub-pixel refinement.
10. Record failures rather than removing difficult cases from evaluation.
11. Preserve enough metadata and artifacts to reproduce every reported result.

---

## What We Measure

ChandraMap separates evaluation into independent stages.

| Stage                | Primary measurements                              |
| -------------------- | ------------------------------------------------- |
| Global retrieval     | Recall@1, Recall@5, Recall@K                      |
| Local matching       | Candidate matches, verified inliers, inlier ratio |
| Spatial distribution | Grid coverage, convex-hull coverage               |
| Registration         | Check-point RMSE, source-image pixel error        |
| Refinement           | Error before/after sub-pixel refinement           |
| Geospatial           | Ground error where meaningful                     |
| System               | Runtime, failure rate, resource usage             |
| Stress testing       | Performance change under controlled difficulty    |

This separation is important because:

> A high number of candidate matches does not prove geometric correctness.

Candidate matches must survive geometric verification before they are treated as **verified inliers**.

---

## Benchmark Scope

The benchmark covers the following source/reference combinations where appropriate data are available:

### Primary Chandrayaan-2 sensors

| Sensor           | Data type                         |                             Approximate spatial scale | Benchmark consideration                   |
| ---------------- | --------------------------------- | ----------------------------------------------------: | ----------------------------------------- |
| OHRC             | Visible panchromatic imagery      | ~0.25–0.32 m/pixel depending on product/documentation | Fine terrain correspondence               |
| TMC-2            | Panchromatic terrain imagery      |                                            ~5 m/pixel | Structural terrain correspondence         |
| IIRS             | Imaging IR hyperspectral data     |                                           ~80 m/pixel | Requires an appropriate 2D representation |
| LRO NAC          | High-resolution reference imagery |    Often ~0.5–2 m/pixel depending on product/geometry | Reference imagery                         |
| LRO WAC          | Wider-scale lunar imagery         |                                     Product-dependent | Lunar-scale and illumination experiments  |
| Kaguya/SELENE TC | Optional cross-sensor data        |                                     Product-dependent | Additional evaluation                     |

The exact product metadata must take precedence over approximate values above.

For example, OHRC documentation can report different pixel scales depending on the product/documentation, so benchmark records must preserve the actual GSD associated with each case.

---

## Dataset and Benchmark Cases

A benchmark case represents one controlled evaluation unit.

Each case should identify, where available:

| Field                  | Description                                       |
| ---------------------- | ------------------------------------------------- |
| `case_id`              | Unique benchmark identifier                       |
| `source_sensor`        | OHRC, TMC-2, IIRS, etc.                           |
| `reference_sensor`     | LRO NAC, LRO WAC, etc.                            |
| `source_image`         | Source image/product identifier                   |
| `reference_image`      | Reference image/product identifier                |
| `source_gsd`           | Source ground sampling distance                   |
| `reference_gsd`        | Reference ground sampling distance                |
| `source_dimensions`    | Source image dimensions                           |
| `reference_dimensions` | Reference image dimensions                        |
| `location`             | Approximate location when available               |
| `projection`           | Map projection/product geometry when available    |
| `illumination`         | Relevant Sun/illumination metadata when available |
| `viewing_geometry`     | Viewing geometry when available                   |
| `overlap`              | Known or estimated overlap information            |
| `ground_truth`         | Ground-truth/check-point information              |
| `benchmark_category`   | Primary benchmark category                        |
| `difficulty`           | Easy, moderate, or stress condition               |
| `notes`                | Additional case-specific information              |

### Benchmark case principle

The case definition must contain enough information to determine:

```text
What was tested?
Which data were used?
Which configuration was used?
Which algorithm was used?
What was considered correct?
How was correctness measured?
```

---

## Ground Truth and Evaluation Protocol

Benchmark evaluation must distinguish between:

- training data
- validation data
- benchmark/test data
- transformation-fitting points
- independent check points

### Independent evaluation

The transformation should **not** be fitted and evaluated exclusively on the same points when independent check points are available.

A preferred protocol is:

```text
Candidate Matches
       │
       ▼
Geometric Verification
       │
       ▼
Verified Inliers
       │
       ├──► Fit Initial Transformation
       │
       ▼
Independent Check Points
       │
       ▼
Registration Error
```

The independent check points are not used to fit the transformation.

This avoids reporting an artificially optimistic fit error.

### Ground-truth hierarchy

Where available, use:

1. Official challenge/reference ground truth
2. Independently verified correspondence data
3. Reproducibly created check points
4. Carefully documented manually established correspondences

Manually selected points must not automatically be described as ground truth. Their creation and verification procedure must be documented.

---

## Sensor-Specific Considerations

The sensors are not interchangeable image sources.

### OHRC

OHRC provides very high-detail visible panchromatic imagery and is appropriate for fine terrain correspondence.

Benchmark records should preserve the actual product GSD rather than assuming a single universal OHRC value.

### TMC-2

TMC-2 provides panchromatic terrain imagery at approximately 5 m/pixel.

It can provide structural correspondence between higher-resolution imagery and broader lunar reference imagery.

Where available, map projection and terrain/DEM information should be preserved.

### IIRS

IIRS is an **imaging IR spectrometer / hyperspectral data source**, not simply a low-resolution camera.

The feedback material describes IIRS as approximately 80 m/pixel with roughly 250+ spectral bands across approximately 0.8–5.0 µm.

The benchmark should therefore explicitly record the representation used for registration.

Possible representations include:

- selected spectral band
- PCA representation
- spectral composite
- structural representation
- another explicitly documented 2D representation

If multiple representations are evaluated, the representation itself becomes a benchmark variable.

### LRO NAC

LRO NAC can serve as high-resolution reference imagery.

When the reference is substantially finer than the source, the benchmark should compare information at physically meaningful scales rather than simply resizing the source upward.

---

## Scale Handling

Scale normalization must not be interpreted as resolution recovery.

### Incorrect approach

```text
IIRS
  │
  ▼
Upsample to NAC/OHRC resolution
  │
  ▼
Claim recovered fine detail
```

Upsampling increases pixel count but does not recreate missing spatial information.

### Benchmark approach

```text
High-Resolution Reference
          │
          ▼
Reference Pyramid / Downsampling
          │
          ▼
Comparable Effective Ground Scale
          │
          ▼
Coarse Correspondence
          │
          ▼
Fine Refinement Only Where Supported
```

The benchmark should therefore record:

- original GSD
- effective comparison scale
- pyramid level
- resampling method
- scale ratio
- refinement scale

Fine registration claims must remain consistent with the information actually present in the source sensor.

---

## Evaluation Protocol

Every benchmark run should document:

1. Dataset version
2. Benchmark case ID
3. Source and reference products
4. Sensor types
5. Source/reference GSD
6. Image dimensions
7. Projection/geometry metadata
8. Preprocessing configuration
9. Scale configuration
10. Retrieval configuration, if used
11. Local matching method
12. Matching parameters
13. Geometric model
14. RANSAC configuration
15. Sub-pixel refinement configuration
16. Independent check-point definition
17. Runtime environment
18. Random seed where applicable
19. Output artifacts
20. Failure status/reason

---

# Metrics

## Retrieval Metrics

### Recall@1

Measures whether the correct reference region is returned as the top-ranked candidate.

**Higher is better.**

### Recall@5

Measures whether the correct reference region appears within the first five candidates.

**Higher is better.**

### Recall@K

May be used when another value of `K` is explicitly defined.

The value of `K` must always be reported.

---

## Matching Metrics

### Candidate Match Count

Number of correspondences proposed before geometric verification.

**Interpretation:** Measures how many candidate correspondences the local matcher produces.

A larger number is not automatically better.

### Verified Inlier Count

Number of candidate correspondences accepted by geometric verification.

**Interpretation:** Measures correspondences that are consistent with the estimated geometric model.

### Inlier Ratio

```text
Inlier Ratio =
Verified Inliers / Candidate Matches
```

**Higher is generally better**, but the ratio must be interpreted together with inlier count and spatial coverage.

A very high ratio from only a few clustered matches is not sufficient evidence of successful registration.

---

## Spatial Coverage Metrics

Spatial distribution is a first-class benchmark metric.

### Grid Coverage

Divide the overlap region into a fixed grid, for example:

```text
4 × 4
```

and measure how many cells contain at least one verified inlier.

The exact grid size must be fixed for the benchmark configuration.

**Higher coverage generally indicates better spatial distribution.**

### Convex-Hull Coverage

Compute the convex hull of verified correspondences and compare its area with the relevant overlap region.

This can indicate whether matches cover a meaningful portion of the image rather than concentrating in a small area.

The exact normalization must be documented.

---

## Registration Metrics

### Check-Point RMSE

Root Mean Square Error calculated on independent check points.

```text
RMSE = sqrt(
    Σ(error_i²) / N
)
```

The exact coordinate space must be stated.

For ChandraMap, **source-image pixel error should be reported first** when evaluating source-image sub-pixel accuracy.

**Lower is better.**

### Source-Image Pixel Error

Registration error expressed in source-image pixels.

This is the preferred primary unit for source-image sub-pixel evaluation.

### Error Before and After Refinement

When sub-pixel refinement is enabled, report:

| Stage             |            Error |
| ----------------- | ---------------: |
| Before refinement | Not yet measured |
| After refinement  | Not yet measured |

Do not claim improvement until measured on the same benchmark cases.

---

## Geospatial Metrics

### Ground Error

Ground error may be reported in metres when:

- GSD is known,
- projection information is valid,
- the geometry supports the conversion,
- and an appropriate reference/check point exists.

**Lower is better.**

Pixel error and ground error must not be treated as interchangeable.

For example, the same fractional pixel error can correspond to very different ground distances for OHRC, TMC-2, and IIRS.

---

## System Metrics

### Runtime

Measure:

- preprocessing time
- retrieval time
- local matching time
- geometric verification time
- sub-pixel refinement time
- total runtime

Where possible:

```text
Total Runtime =
Preprocessing
+ Retrieval
+ Matching
+ Verification
+ Refinement
+ Other Measured Stages
```

Hardware and runtime environment must be recorded.

### Failure Rate

```text
Failure Rate =
Failed Benchmark Cases / Total Benchmark Cases
```

**Lower is better.**

Failures must not be silently excluded.

### Resource Usage

When measured, report:

- peak memory
- GPU memory
- CPU utilization
- GPU utilization
- batch size
- relevant hardware information

---

# Baselines

The benchmark uses a progressive baseline hierarchy.

## Baseline 1 — Simple SIFT Registration

```text
SIFT
  ↓
Descriptor Matching
  ↓
Candidate Matches
  ↓
RANSAC
  ↓
Transformation
  ↓
Registration
```

Purpose:

- establish a simple reference point
- verify the benchmark itself
- provide an interpretable baseline

---

## Baseline 2 — SIFT + Geometric Verification

```text
SIFT
  ↓
Descriptor Matching
  ↓
Ratio Test / Cross-Check
  ↓
RANSAC
  ↓
Verified Inliers
  ↓
Affine / Homography
  ↓
Registration Evaluation
```

This should be the first serious accuracy baseline.

---

## Experimental Improvement — Sensor-Aware + Multi-Scale

```text
Sensor-Aware Preprocessing
          ↓
Comparable Effective Scale
          ↓
SIFT / Local Matcher
          ↓
Geometric Verification
          ↓
Registration
```

The benchmark determines whether these additions provide measurable improvement.

---

## Advanced Local Matcher Experiments

Potential experimental paths include:

### ALIKED + LightGlue

ALIKED provides learned sparse keypoints/descriptors, while LightGlue performs sparse feature matching.

### LoFTR

LoFTR is a detector-free local correspondence method that estimates correspondences directly.

### RIFT / CFOG-related approaches

These may be investigated for difficult multimodal or structural matching cases.

These methods are **experimental candidates**, not assumed winners.

Pretrained terrestrial models must not automatically be described as lunar-invariant.

---

# Benchmark Categories

## End-to-End Registration

The end-to-end benchmark evaluates:

```text
Input
→ Preprocessing
→ Candidate Search
→ Local Matching
→ Geometric Verification
→ Sub-Pixel Refinement
→ Final Transform
→ Registered Output
```

Report where applicable:

- success/failure
- candidate match count
- verified inlier count
- inlier ratio
- spatial coverage
- check-point RMSE
- source-image pixel error
- ground error
- runtime
- resource usage
- failure reason

---

## Global Retrieval

Global retrieval is **conditional**.

If reliable metadata already restricts the search region, metadata-based filtering may be used instead of global retrieval.

If location is unknown or insufficiently constrained, global retrieval can identify candidate lunar regions.

### Offline reference preparation

```text
Reference Images
      ↓
Tiles + Scales
      ↓
Global Descriptors
      ↓
FAISS Index
      +
Tile Metadata
```

### Online retrieval

```text
Source Image
      ↓
Compatible Global Descriptor
      ↓
Vector Search
      ↓
Top-K Candidate Tiles
      ↓
Local Matching
```

FAISS is a vector similarity-search/indexing component. It is **not the local feature matcher**.

Global retrieval descriptors and local matching descriptors serve different purposes.

### Retrieval metrics

- Recall@1
- Recall@5
- Recall@K

Also save examples of:

- correct candidate regions
- incorrect candidate regions
- retrieval failures

---

## Local Matching

Local matching should compare methods on the **same benchmark cases**.

### Candidate paths

| Path                  | Role                                | Status            |
| --------------------- | ----------------------------------- | ----------------- |
| SIFT                  | Explainable baseline                | Proposed baseline |
| ALIKED + LightGlue    | Learned sparse pipeline             | Experimental      |
| LoFTR                 | Detector-free matching              | Experimental      |
| RIFT-related approach | Multimodal research direction       | Experimental      |
| CFOG-related approach | Structural remote-sensing direction | Experimental      |
| ORB                   | Optional speed baseline             | Optional          |

Report:

- candidate match count
- verified inlier count
- inlier ratio
- spatial coverage
- check-point RMSE
- runtime
- failure rate

A matcher should not be considered better merely because it generates more matches.

---

## Geometric Verification

Candidate matches become **verified inliers** only after geometric verification.

### Initial flow

```text
Candidate Matches
       ↓
RANSAC
       ↓
Initial Geometric Model
       ↓
Verified Inliers
       ↓
Residual Analysis
```

Potential initial models include:

- affine transformation
- homography
- another explicitly justified model

The simplest model that adequately explains the observed residuals should be preferred.

### Residual inspection

Residual vectors should be inspected spatially.

Systematic residual patterns may indicate:

- local geometric distortion
- relief effects
- viewpoint differences
- sensor geometry
- inadequate map projection
- insufficient transformation model

A single global transformation must not automatically be assumed sufficient for all lunar imagery.

---

## Sub-Pixel Refinement

Sub-pixel refinement occurs **after reliable geometric inliers have been established**.

### Required sequence

```text
LOCAL MATCHES
      ↓
RANSAC / INITIAL MODEL
      ↓
VERIFIED INLIERS
      ↓
SUB-PIXEL TIE-POINT REFINEMENT
      ↓
REFIT FINAL TRANSFORMATION
      ↓
REGISTERED IMAGE
```

Possible refinement approaches include:

- patch-based correlation
- phase-based refinement
- planetary registration tools
- another explicitly documented local refinement method

Report:

- error before refinement
- error after refinement
- independent check-point RMSE
- source-image pixel error

Sub-pixel claims must be supported by numerical evaluation.

---

## Sensor-Specific Evaluation

Sensor results must remain separate.

Do not hide sensor differences behind one overall average.

### Minimum benchmark groups

- OHRC
- TMC-2
- IIRS

### Additional groups where data are available

- LRO NAC
- LRO WAC
- Kaguya/SELENE TC

For IIRS, every result must record the 2D representation used.

Example:

| Sensor | Representation            | Reference | Method | Status            |
| ------ | ------------------------- | --------- | ------ | ----------------- |
| IIRS   | Selected band             | LRO NAC   | SIFT   | Pending benchmark |
| IIRS   | PCA                       | LRO NAC   | SIFT   | Pending benchmark |
| IIRS   | Structural representation | LRO NAC   | SIFT   | Pending benchmark |

---

## Scale Stress

Scale stress evaluates large differences in effective ground sampling distance.

### Test questions

- Does the reference pyramid improve correspondence?
- Does downsampling produce a more physically meaningful comparison?
- Does retrieval remain reliable at large scale differences?
- Does local matching remain stable?
- Does the system avoid treating upsampling as recovered information?

### Key principle

> Compare physical information at meaningful scales, not merely pixel counts.

---

## Illumination Stress

Sun angle can alter:

- shadows
- local brightness
- crater appearance
- ridge appearance
- terrain contrast

Brightness normalization alone cannot recreate terrain geometry changed by illumination.

### Controlled comparison

```text
Same Region
   │
   ├── Similar Illumination
   │
   └── Different Illumination
```

Compare:

- raw grayscale
- normalized intensity
- gradients
- edges
- structural representations
- photometric correction where available

Report the measured performance difference.

---

## Modality Stress

Modality stress evaluates correspondence where source and reference imagery differ substantially in sensor characteristics.

Example cases:

- OHRC ↔ LRO NAC
- TMC-2 ↔ LRO NAC
- IIRS-derived 2D representation ↔ visible reference

Every modality test must identify:

- source sensor
- reference sensor
- representation
- effective scale
- matcher
- geometric model
- evaluation metrics

---

## Geometry Stress

Geometry stress evaluates cases with:

- relief-rich terrain
- stronger viewpoint differences
- geometric distortion
- map-projected versus raw imagery
- terrain-dependent displacement

Evaluate:

- residual vectors
- transformation fit
- check-point RMSE
- spatial coverage
- failure rate

Where systematic residuals appear, investigate:

- local/piecewise warping
- sensor geometry
- DEM information
- improved projection/orthorectification

Flexible warping must not be used to conceal inaccurate control points.

---

## Low-Feature and Repetitive Terrain

Difficult benchmark cases should include:

- smooth terrain
- repetitive terrain
- weak keypoint regions
- low-texture areas
- regions with ambiguous local structure

Measure:

- candidate matches
- verified inliers
- inlier ratio
- spatial coverage
- check-point RMSE
- failure rate

These cases are particularly important because visually impressive examples can hide weak correspondence behavior.

---

# Stress-Test Matrix

| Stress Case                 | Description                                                    | Main Risk                     | Metrics                              |
| --------------------------- | -------------------------------------------------------------- | ----------------------------- | ------------------------------------ |
| Easy pair                   | Known overlap, similar illumination, moderate scale difference | Baseline functionality        | Inliers, RMSE, runtime               |
| Sun-angle stress            | Same region under substantially different illumination         | Shadow/illumination variation | Inlier ratio, RMSE                   |
| Scale stress                | Large GSD difference                                           | Scale mismatch                | Recall, inliers, RMSE                |
| Modality stress             | Cross-sensor correspondence                                    | Modality difference           | Inliers, coverage, RMSE              |
| IIRS representation stress  | Multiple IIRS-derived 2D representations                       | Representation choice         | Inliers, coverage, RMSE              |
| Geometry stress             | Relief-rich terrain/viewpoint difference                       | Nonlinear geometry            | Residuals, RMSE                      |
| Low-feature terrain         | Smooth/repetitive region                                       | False matches                 | Inlier ratio, coverage, failure rate |
| Metadata-constrained search | Reliable geographic metadata available                         | Search efficiency             | Runtime, candidate count             |
| Retrieval stress            | Unknown/weak initial location                                  | Incorrect candidate region    | Recall@K, runtime                    |

---

# Ablation Studies

Ablation studies identify which architectural component actually contributes to measured performance.

## Proposed ablation matrix

| Configuration                      | Sensor-Aware | Multi-Scale | Illumination-Aware | Advanced Matcher | Refinement | Status   |
| ---------------------------------- | -----------: | ----------: | -----------------: | ---------------: | ---------: | -------- |
| SIFT baseline                      |           No |          No |                 No |               No |         No | Proposed |
| SIFT + sensor preprocessing        |          Yes |          No |                 No |               No |         No | Proposed |
| SIFT + multi-scale                 |          Yes |         Yes |                 No |               No |         No | Proposed |
| SIFT + illumination representation |          Yes |         Yes |                Yes |               No |         No | Proposed |
| Advanced matcher only              |      Depends |          No |                 No |              Yes |         No | Proposed |
| Full pipeline                      |          Yes |         Yes |                Yes |         Optional |        Yes | Proposed |

The exact ablation matrix should be updated once implementations exist.

### Key comparison principle

Run the **same image pairs** through each configuration.

This prevents changes in dataset difficulty from being mistaken for algorithmic improvement.

---

# Benchmark Result Tables

No benchmark numbers should be entered until they have been experimentally measured.

## End-to-End Registration

| Benchmark           | Sensor Pair | Method | Inliers | Inlier Ratio | Coverage | Check-Point RMSE (px) | Ground Error (m) | Runtime | Status            |
| ------------------- | ----------- | ------ | ------: | -----------: | -------: | --------------------: | ---------------: | ------: | ----------------- |
| Easy pair           | —           | SIFT   |       — |            — |        — |                     — |              N/A |       — | Pending benchmark |
| Scale stress        | —           | —      |       — |            — |        — |                     — |                — |       — | Pending benchmark |
| Illumination stress | —           | —      |       — |            — |        — |                     — |                — |       — | Pending benchmark |
| Modality stress     | —           | —      |       — |            — |        — |                     — |                — |       — | Pending benchmark |

---

## Retrieval Results

| Dataset | Sensor Pair | Descriptor | Recall@1 | Recall@5 | Recall@K |   K | Runtime | Status       |
| ------- | ----------- | ---------- | -------: | -------: | -------: | --: | ------: | ------------ |
| —       | —           | —          |        — |        — |        — |   — |       — | Not measured |

---

## Local Matching Results

| Case | Sensor Pair | Method             | Candidate Matches | Verified Inliers | Inlier Ratio | Coverage | RMSE (px) | Runtime | Status       |
| ---- | ----------- | ------------------ | ----------------: | ---------------: | -----------: | -------: | --------: | ------: | ------------ |
| —    | —           | SIFT               |                 — |                — |            — |        — |         — |       — | Not measured |
| —    | —           | ALIKED + LightGlue |                 — |                — |            — |        — |         — |       — | Not measured |
| —    | —           | LoFTR              |                 — |                — |            — |        — |         — |       — | Not measured |

---

## Sensor-Specific Results

| Sensor | Representation | Reference | Method | Inliers | Coverage | RMSE (px) | Runtime | Status       |
| ------ | -------------- | --------- | ------ | ------: | -------: | --------: | ------: | ------------ |
| OHRC   | —              | —         | —      |       — |        — |         — |       — | Not measured |
| TMC-2  | —              | —         | —      |       — |        — |         — |       — | Not measured |
| IIRS   | —              | —         | —      |       — |        — |         — |       — | Not measured |

---

## Stress-Test Results

| Stress Case | Method | Recall | Inlier Ratio | Coverage | Check-Point RMSE | Runtime | Failure | Status       |
| ----------- | ------ | -----: | -----------: | -------: | ---------------: | ------: | ------: | ------------ |
| Easy pair   | —      |      — |            — |        — |                — |       — |       — | Not measured |
| Sun-angle   | —      |      — |            — |        — |                — |       — |       — | Not measured |
| Scale       | —      |      — |            — |        — |                — |       — |       — | Not measured |
| Modality    | —      |      — |            — |        — |                — |       — |       — | Not measured |
| Geometry    | —      |      — |            — |        — |                — |       — |       — | Not measured |
| Low-feature | —      |      — |            — |        — |                — |       — |       — | Not measured |

---

## Ablation Results

| Configuration      | Inliers | Inlier Ratio | Coverage | Check-Point RMSE | Runtime | Status       |
| ------------------ | ------: | -----------: | -------: | ---------------: | ------: | ------------ |
| SIFT baseline      |       — |            — |        — |                — |       — | Not measured |
| Sensor-aware       |       — |            — |        — |                — |       — | Not measured |
| Multi-scale        |       — |            — |        — |                — |       — | Not measured |
| Illumination-aware |       — |            — |        — |                — |       — | Not measured |
| Advanced matcher   |       — |            — |        — |                — |       — | Not measured |
| Full pipeline      |       — |            — |        — |                — |       — | Not measured |

---

# Reproducibility

Every published benchmark result should be reproducible from a documented configuration.

Record:

- dataset version
- benchmark case IDs
- source/reference product IDs
- GSD
- preprocessing configuration
- representation
- pyramid levels
- retrieval configuration
- descriptor configuration
- matcher configuration
- geometric model
- RANSAC parameters
- refinement parameters
- random seeds
- software version
- library versions
- operating system
- CPU
- GPU
- available memory
- runtime environment
- benchmark script version

### Configuration principle

The benchmark result should be traceable as:

```text
Result
 ├── Dataset Version
 ├── Case ID
 ├── Configuration
 ├── Software Version
 ├── Hardware
 └── Evaluation Protocol
```

### Example command

Commands should be added only when the actual repository CLI is implemented.

```bash
# Example only — replace with the actual ChandraMap benchmark command.
python -m <benchmark_module> --config <benchmark_config>
```

This command is intentionally an example and is **not an implemented ChandraMap command**.

---

# Benchmark Artifacts

Each benchmark run should save enough information for analysis and debugging.

Recommended artifacts:

```text
benchmark_run/
├── config/
│   └── benchmark_config.*
├── inputs/
│   └── case_metadata.*
├── matches/
│   ├── candidate_matches.*
│   ├── verified_inliers.*
│   └── rejected_matches.*
├── geometry/
│   ├── initial_transform.*
│   ├── refined_tie_points.*
│   └── final_transform.*
├── registration/
│   └── registered_image.*
├── evaluation/
│   ├── metrics.json
│   ├── metrics.csv
│   └── check_points.*
├── figures/
│   ├── candidate_matches.*
│   ├── inliers.*
│   ├── residuals.*
│   └── coverage.*
└── logs/
    └── benchmark.log
```

The exact artifact layout should follow the actual repository implementation.

### Why artifacts matter

Saved artifacts make it possible to:

- reproduce results
- inspect incorrect matches
- inspect RANSAC behavior
- diagnose spatial clustering
- analyze residuals
- compare algorithms
- reproduce failures
- audit published benchmark numbers

---

# Failure Analysis

Failures are benchmark results.

They should not be silently removed from the test set.

## Recommended failure categories

| Category                  | Description                                                    |
| ------------------------- | -------------------------------------------------------------- |
| Insufficient overlap      | Source and reference do not contain enough common terrain      |
| Incorrect retrieval       | Correct region was not returned by search                      |
| Illumination mismatch     | Appearance changed substantially due to Sun geometry           |
| Scale mismatch            | Effective GSD difference prevented reliable matching           |
| Modality mismatch         | Sensor characteristics prevented reliable correspondence       |
| Insufficient keypoints    | Too few stable local features                                  |
| Repetitive terrain        | Multiple locations produce similar local structures            |
| Incorrect geometric model | Initial model cannot explain observed residuals                |
| Insufficient inliers      | Too few geometrically consistent matches                       |
| Poor spatial coverage     | Inliers cluster in a small region                              |
| Refinement failure        | Sub-pixel refinement failed or became unstable                 |
| Metadata/projection issue | Product geometry or metadata was insufficient/inconsistent     |
| Runtime/resource failure  | Processing exceeded available resources or runtime constraints |

### Failed cases should preserve

- case ID
- method
- configuration
- failure stage
- failure reason
- candidate matches if available
- partial metrics
- logs
- diagnostic figures

---

# Benchmark Workflow

The recommended benchmark workflow is:

```text
1. Select benchmark case
        ↓
2. Load source/reference metadata
        ↓
3. Apply sensor-specific preprocessing
        ↓
4. Apply metadata-based search restriction when available
        ↓
5. Perform global retrieval if required
        ↓
6. Generate candidate regions
        ↓
7. Run local matching
        ↓
8. Perform geometric verification
        ↓
9. Refine verified tie points
        ↓
10. Refit final transformation
        ↓
11. Generate registered output
        ↓
12. Evaluate independent check points
        ↓
13. Calculate spatial coverage
        ↓
14. Calculate runtime/failure metrics
        ↓
15. Save benchmark artifacts
        ↓
16. Generate benchmark report
```

### Conditional retrieval

Global retrieval is not mandatory.

If metadata provides reliable:

- latitude/longitude
- footprint
- map projection
- geographic bounds

then that information may restrict the search space before visual retrieval.

If metadata is unavailable or insufficient, image-based global retrieval becomes more important.

---

# Proposed Benchmark Progression

The following progression is proposed based on the project feedback. It does **not** imply that these milestones are already completed.

| Phase | Objective                       | Expected Output                                                  | Status   |
| ----- | ------------------------------- | ---------------------------------------------------------------- | -------- |
| A     | One known source/reference pair | SIFT → RANSAC → transform → registered overlay → numerical error | Proposed |
| B     | Scale + illumination            | Reference pyramid + structure-focused preprocessing              | Proposed |
| C     | Small retrieval database        | Reference tiles → global descriptors → FAISS → Top-K             | Proposed |
| D     | Stronger local matcher          | ALIKED + LightGlue or LoFTR compared with SIFT                   | Proposed |
| E     | Sub-pixel refinement            | Refined tie points + final transform                             | Proposed |
| F     | Additional sensors              | OHRC/TMC-2 followed by IIRS as separate experiment               | Proposed |

---

## Phase A — One Known Pair

Start with one known overlapping source/reference pair.

```text
Input
 → SIFT
 → Candidate Matches
 → RANSAC
 → Verified Inliers
 → Transformation
 → Registered Overlay
 → Independent Check-Point Error
```

Save:

- match plot
- rejected outliers
- registered overlay
- inlier statistics
- check-point error
- runtime

### First milestone definition

A source/reference pair should be able to move from:

```text
Input
→ Candidate Matches
→ Verified Inliers
→ Final Transform
→ Registered Overlay
→ Numerical Check-Point Error
```

before the benchmark expands to a larger dataset.

---

## Phase B — Scale + Illumination

Introduce:

- reference pyramids
- effective-scale comparison
- structure-focused preprocessing
- illumination stress cases

Compare easier and harder illumination conditions using the same underlying region where possible.

---

## Phase C — Small Retrieval Database

Prepare tens or hundreds of reference tiles where the dataset permits.

```text
Reference Tiles
      ↓
Global Descriptors
      ↓
FAISS Index
      ↓
Top-K Retrieval
```

Report:

- Recall@1
- Recall@5
- Recall@K
- retrieval runtime
- correct candidate examples
- incorrect candidate examples

---

## Phase D — Stronger Local Matcher

Compare:

```text
SIFT
vs
ALIKED + LightGlue
```

or:

```text
SIFT
vs
LoFTR
```

using identical benchmark pairs.

The benchmark should determine whether the advanced method improves the difficult cases.

---

## Phase E — Sub-Pixel Refinement

Refine verified inlier coordinates.

Compare:

```text
Before Refinement
        ↓
Sub-Pixel Refinement
        ↓
Final Transformation
```

Report independent check-point RMSE before and after refinement.

---

## Phase F — Additional Sensors

Evaluate:

1. OHRC
2. TMC-2
3. IIRS as a separate sensor/representation experiment

Sensor-specific results should remain separate.

---

# Dataset and Repository Structure

The following is a **recommended/planned structure**, not a claim that these directories already exist:

```text
benchmarks/
├── README.md
├── configs/
├── datasets/
├── cases/
├── baselines/
├── scripts/
├── results/
├── reports/
└── figures/
```

### `configs/`

Benchmark configurations.

Potential contents:

```text
configs/
├── baseline_sift.*
├── retrieval.*
├── stress_scale.*
├── stress_illumination.*
├── stress_modality.*
└── ablations.*
```

These files should only be added once the corresponding benchmark implementations exist.

### `datasets/`

Dataset manifests, metadata, checksums, and benchmark dataset definitions.

Raw mission data should follow the project's data-management policy and licensing requirements.

### `cases/`

Individual benchmark case definitions.

### `baselines/`

Baseline-specific configurations or implementation references.

### `scripts/`

Benchmark execution and evaluation scripts.

### `results/`

Machine-readable benchmark outputs.

### `reports/`

Generated benchmark summaries and experiment reports.

### `figures/`

Benchmark visualizations such as:

- match distributions
- residual vectors
- coverage plots
- registered overlays
- retrieval examples

---

# Reporting Guidelines

A benchmark result should answer four questions:

### 1. What was tested?

Identify:

- dataset
- case
- sensor pair
- scale
- illumination
- method

### 2. How was it configured?

Identify:

- preprocessing
- matcher
- geometric model
- refinement
- retrieval
- relevant parameters

### 3. What happened?

Report measured:

- retrieval metrics
- inliers
- inlier ratio
- coverage
- RMSE
- geospatial error where valid
- runtime
- failure status

### 4. Can someone reproduce it?

Provide:

- case ID
- dataset version
- configuration
- software version
- hardware
- artifact location

---

# Benchmark Integrity Rules

The following rules apply to all published ChandraMap benchmark results.

## 1. No fabricated results

Never invent:

- accuracy
- RMSE
- runtime
- inlier counts
- recall
- coverage
- failure rates
- geospatial error

If a value has not been measured, use:

- `Not yet measured`
- `Pending benchmark`
- `Not applicable`
- `Not implemented`

---

## 2. No decorative confidence scores

Values such as:

```text
92% confidence
95% accuracy
★★★★★
```

must not be presented as benchmark results unless they have a defined experimental basis.

Matcher confidence is not equivalent to geometric correctness.

---

## 3. No unsupported algorithm rankings

Do not describe an algorithm as:

- best
- superior
- state-of-the-art for ChandraMap
- most accurate

unless the project has measured the relevant claim under a defined benchmark protocol.

---

## 4. Same cases for fair comparisons

When comparing methods, use the same:

- image pairs
- benchmark cases
- evaluation points
- metrics
- success criteria

where applicable.

---

## 5. Keep failures

Failed cases remain part of the benchmark unless an explicit and documented exclusion criterion exists.

---

## 6. Separate sensors

Do not collapse OHRC, TMC-2, and IIRS into a single number when that would hide meaningful differences.

---

## 7. Do not confuse candidate matches with verified inliers

Use:

> **Candidate matches**

before geometric verification.

Use:

> **Verified inliers**

after geometric verification.

---

## 8. Do not confuse matcher confidence with correctness

A high matcher confidence score does not establish that a correspondence is geometrically correct.

RANSAC or another documented geometric verification procedure must determine geometric consistency.

---

## 9. Do not treat upsampling as information recovery

Resizing an IIRS image to NAC/OHRC dimensions does not create the spatial information that was absent from the original measurement.

---

## 10. Do not overclaim sub-pixel accuracy

Sub-pixel accuracy must be measured in a defined coordinate system and evaluated independently where possible.

Source-image pixel error should be reported before converting to metres.

---

## 11. Do not hide poor correspondences with flexible warping

A flexible transformation can produce a visually attractive overlay despite weak control points.

Geometric flexibility should only be introduced after correspondence quality and spatial distribution have been established.

---

## 12. Prefer measurable simplicity

Additional algorithms should be retained because they provide measurable value, not because they make the pipeline appear more sophisticated.

---

# Current Results

No experimental benchmark results are claimed in this specification.

| Benchmark Area          | Current Status    |
| ----------------------- | ----------------- |
| End-to-end registration | Pending benchmark |
| Global retrieval        | Pending benchmark |
| SIFT baseline           | Pending benchmark |
| Advanced local matching | Pending benchmark |
| Geometric verification  | Pending benchmark |
| Sub-pixel refinement    | Pending benchmark |
| OHRC evaluation         | Pending benchmark |
| TMC-2 evaluation        | Pending benchmark |
| IIRS evaluation         | Pending benchmark |
| Scale stress            | Pending benchmark |
| Illumination stress     | Pending benchmark |
| Modality stress         | Pending benchmark |
| Geometry stress         | Pending benchmark |
| Low-feature terrain     | Pending benchmark |
| Ablation study          | Pending benchmark |
| Runtime evaluation      | Pending benchmark |
| Failure-rate evaluation | Pending benchmark |

This section should be updated only from actual experiment outputs.

---

# Benchmark Status Vocabulary

Use consistent status values:

| Status              | Meaning                                                |
| ------------------- | ------------------------------------------------------ |
| `Measured`          | Experiment has been executed and the value is recorded |
| `Pending benchmark` | Experiment is defined but not yet measured             |
| `In progress`       | Experiment is currently being implemented/executed     |
| `Not applicable`    | Metric does not apply to the benchmark case            |
| `Failed`            | Experiment executed but the pipeline failed            |
| `Not implemented`   | Required benchmark capability does not yet exist       |
| `Proposed`          | Planned benchmark or architecture element              |

---

# Recommended Result Interpretation

Benchmark results should be interpreted jointly.

For example:

```text
High Inlier Count
        +
High Inlier Ratio
        +
Good Spatial Coverage
        +
Low Check-Point RMSE
        +
Acceptable Runtime
```

provides substantially more information than any single metric.

A large number of matches alone does not demonstrate reliable registration.

Likewise:

```text
Low RMSE
```

measured only on the points used to fit a transformation should not be treated as sufficient evidence of generalization.

---

# Benchmark Design Principles

The ChandraMap benchmark follows these principles:

### Research over presentation

The benchmark measures the correspondence system rather than only its visual output.

### Baseline before complexity

A simple SIFT-based system provides a reference before more complex methods are introduced.

### Same data, controlled comparison

Algorithmic changes should be evaluated on the same cases.

### Sensor-aware evaluation

OHRC, TMC-2, and IIRS are evaluated according to their actual sensing characteristics.

### Physically meaningful scale

Scale handling compares available information rather than pixel counts.

### Geometry-aware evaluation

Residuals and independent check points are preferred over visual overlays alone.

### Failures are data

Difficult and failed cases reveal limitations and should remain visible.

### Reproducibility

Every result should be traceable to its data, configuration, software, hardware, and evaluation protocol.

---

# Benchmark Questions

A complete ChandraMap benchmark should eventually answer:

1. Can a known overlapping pair be registered reliably?
2. How does SIFT perform as the baseline?
3. Does sensor-aware preprocessing improve correspondence?
4. Does multi-scale processing improve large-GSD-difference cases?
5. How much does illumination change affect performance?
6. How does cross-sensor modality affect performance?
7. Which IIRS 2D representation provides the most useful structural correspondence?
8. Does global retrieval return the correct region in the Top-K candidates?
9. Do advanced matchers improve difficult cases over SIFT?
10. Are verified matches spatially distributed?
11. Does sub-pixel refinement reduce independent check-point error?
12. Is a single affine/homography model sufficient for each benchmark class?
13. When does local/piecewise geometry become necessary?
14. What cases consistently fail?
15. What is the runtime on the intended hardware?
16. Which architectural components provide measurable improvement?

The benchmark should answer these questions with measurements rather than assumptions.

---

# Benchmark Outputs

A successful benchmark run should produce a traceable set of outputs:

```text
Benchmark Case
      │
      ├── Candidate Correspondences
      │
      ├── Verified Inliers
      │
      ├── Transformation
      │
      ├── Refined Tie Points
      │
      ├── Registered Image
      │
      ├── Residual Analysis
      │
      ├── Spatial Coverage
      │
      ├── Check-Point Error
      │
      ├── Runtime
      │
      ├── Failure Information
      │
      └── Machine-Readable Metrics
```

These outputs are the evidence behind any future benchmark claims.

---

# Relationship to the ChandraMap System

The benchmark evaluates the following conceptual architecture:

```text
                    ┌──────────────────────────┐
                    │   Source Lunar Image     │
                    │ OHRC / TMC-2 / IIRS      │
                    └────────────┬─────────────┘
                                 │
                                 ▼
                    ┌──────────────────────────┐
                    │   Sensor-Aware Route     │
                    └────────────┬─────────────┘
                                 │
                                 ▼
                    ┌──────────────────────────┐
                    │   Multi-Scale Handling   │
                    └────────────┬─────────────┘
                                 │
                    ┌────────────┴────────────┐
                    │                         │
             Metadata Available        Metadata Insufficient
                    │                         │
                    ▼                         ▼
             Search Restriction       Global Retrieval
                    │                         │
                    └────────────┬────────────┘
                                 ▼
                    ┌──────────────────────────┐
                    │     Candidate Regions    │
                    └────────────┬─────────────┘
                                 ▼
                    ┌──────────────────────────┐
                    │      Local Matching      │
                    │ SIFT / ALIKED+LightGlue  │
                    │        / LoFTR           │
                    └────────────┬─────────────┘
                                 ▼
                    ┌──────────────────────────┐
                    │  Candidate Correspond.   │
                    └────────────┬─────────────┘
                                 ▼
                    ┌──────────────────────────┐
                    │ Geometric Verification   │
                    │      RANSAC + Model      │
                    └────────────┬─────────────┘
                                 ▼
                    ┌──────────────────────────┐
                    │    Verified Inliers      │
                    └────────────┬─────────────┘
                                 ▼
                    ┌──────────────────────────┐
                    │   Sub-Pixel Refinement   │
                    └────────────┬─────────────┘
                                 ▼
                    ┌──────────────────────────┐
                    │   Final Transformation   │
                    └────────────┬─────────────┘
                                 ▼
                    ┌──────────────────────────┐
                    │ Registration Evaluation  │
                    └───────┬─────────┬────────┘
                            │         │
                 ┌──────────┘         └──────────┐
                 ▼                               ▼
        Independent Check                  Spatial Coverage
             Points                              +
                 │                         Runtime / Failure
                 ▼
          RMSE / Accuracy
```

The final lunar mosaic is a downstream visualization/product and is not the sole benchmark target.

---

# Benchmark Data Sources

The project research identifies the following potential data sources:

| Dataset                       | Proposed Benchmark Role                                           |
| ----------------------------- | ----------------------------------------------------------------- |
| Chandrayaan-2 OHRC            | Main source/target imagery                                        |
| Chandrayaan-2 TMC-2           | Main source/target imagery                                        |
| Chandrayaan-2 IIRS            | Main source/target imagery with sensor-specific 2D representation |
| LRO NAC                       | Reference imagery and cross-sensor pairs                          |
| LRO WAC                       | Additional lunar-scale and illumination evaluation                |
| Kaguya/SELENE TC              | Optional cross-sensor evaluation                                  |
| Synthetic lunar augmentations | Controlled Sun-angle, rotation, scale, and contrast experiments   |

Actual benchmark inclusion must depend on data availability, licensing, product compatibility, and the implemented evaluation protocol.

---

# Benchmark Maintenance

When a benchmark changes, record:

- benchmark version
- dataset version
- changed cases
- changed metrics
- changed evaluation rules
- changed configurations
- reason for the change

Do not silently replace benchmark cases after results have been published.

If a benchmark definition changes materially, preserve the previous version where practical.

---

# Source-Derived vs Proposed Information

This document deliberately separates three categories.

## Source-derived

The project materials establish the importance of:

- sensor-aware processing
- multi-scale handling
- illumination-aware evaluation
- geometric verification
- sub-pixel refinement
- spatial coverage
- independent check-point evaluation
- runtime and failure measurement
- controlled stress testing
- baseline versus improved-pipeline comparisons

The feedback specifically recommends a measurable progression from a SIFT baseline toward sensor-aware, multi-scale, retrieval, stronger matching, and refinement experiments.

## Measured benchmark results

These are numerical results produced by actual ChandraMap experiments.

No such values are asserted in this README unless populated from benchmark outputs.

## Proposed benchmark design

Dataset structures, configuration layouts, milestone states, and result-table templates marked as proposed are planning structures rather than claims about the current repository implementation.

---

# References and Source Material

The benchmark specification is based primarily on the project materials supplied for ChandraMap.

### Project technical feedback

- **Aryan_SIH_26166_Lunar_Image_Correspondence_Feedback.pptx** — sensor-aware architecture, scale handling, illumination strategy, matching approaches, and evaluation direction.
- **Aryan_Lunar_Image_Registration_Feedback.pdf** — updated pipeline review, retrieval architecture, geometric verification, sub-pixel refinement, evaluation metrics, stress tests, and milestone progression.
- **SIH26166 Silarlar PS.pdf** — related lunar-image problems and potential space-agency datasets.

### Important technical guidance incorporated

The project feedback specifically distinguishes global retrieval from local matching and recommends preparing an offline reference index containing tiles, scales, global descriptors, FAISS indexing, and metadata.

The evaluation guidance recommends Recall@1/Recall@5 for retrieval, inlier count and inlier ratio for local matching, spatial coverage, independent check-point RMSE, geospatial error where meaningful, and runtime/failure rate.

The geometry guidance recommends the sequence:

```text
LOCAL MATCHES
    ↓
RANSAC
    ↓
INLIERS
    ↓
SUB-PIXEL TIE POINTS
    ↓
FINAL MODEL
    ↓
REGISTERED IMAGE
```

and emphasizes evaluating independent check points rather than relying only on the points used to fit the transformation.

The sensor guidance emphasizes that OHRC, TMC-2, and IIRS have materially different sensing characteristics and that IIRS should first be converted into an appropriate 2D registration representation rather than being treated as an ordinary camera image.

The project data research identifies Chandrayaan-2 OHRC, TMC-2, IIRS, LRO NAC, LRO WAC, Kaguya/SELENE TC, and synthetic lunar augmentations as potential data sources for different benchmark purposes.

---

## Benchmark Principle

> **Build small. Measure honestly. Keep the failures.**

ChandraMap should treat benchmark results as experimental evidence rather than presentation claims. A simple, reproducible baseline with independently evaluated error is more useful than a complex pipeline whose individual components have not been measured.
