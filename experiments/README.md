# ChandraMap Benchmarks

The `benchmarks/` directory defines the evaluation framework for **ChandraMap**, a lunar image correspondence and registration system developed around **SIH 26166 — Multi-modal, Sun angle and scale invariant image correspondence using Chandrayaan-2 optical images**.

The benchmark system focuses on measurable evidence for:

- lunar image correspondence
- candidate-match quality
- geometric verification
- transformation estimation
- spatially distributed correspondences
- sub-pixel registration accuracy
- cross-resolution and scale handling
- illumination / Sun-angle robustness
- cross-sensor and cross-modal matching
- global retrieval, when required
- runtime and failure behavior

A registered image or final lunar mosaic is a useful downstream output, but **visual appearance alone is not considered sufficient evidence of correspondence or registration quality**.

> **Benchmark status:** The benchmark protocol described here is the project-level specification and organization. This document does not claim that every listed track, metric, dataset, or baseline has already been implemented or experimentally completed.

---

## Table of Contents

- [1. Benchmark Objective](#1-benchmark-objective)
- [2. What Is Being Benchmarked?](#2-what-is-being-benchmarked)
- [3. Benchmark Philosophy](#3-benchmark-philosophy)
- [4. Benchmark Tracks](#4-benchmark-tracks)
- [5. Sensors and Data Sources](#5-sensors-and-data-sources)
- [6. Benchmark Dataset Organization](#6-benchmark-dataset-organization)
- [7. Image Pair Definition](#7-image-pair-definition)
- [8. Ground Truth and Evaluation Protocol](#8-ground-truth-and-evaluation-protocol)
- [9. Metrics](#9-metrics)
- [10. Metric Definitions](#10-metric-definitions)
- [11. Baselines](#11-baselines)
- [12. Retrieval Benchmark](#12-retrieval-benchmark)
- [13. Registration Evaluation Pipeline](#13-registration-evaluation-pipeline)
- [14. Stress-Test Matrix](#14-stress-test-matrix)
- [15. Fair Comparison Protocol](#15-fair-comparison-protocol)
- [16. Benchmark Result Format](#16-benchmark-result-format)
- [17. Result Storage and Artifacts](#17-result-storage-and-artifacts)
- [18. Reproducibility Requirements](#18-reproducibility-requirements)
- [19. Adding a New Benchmark](#19-adding-a-new-benchmark)
- [20. Interpreting Benchmark Results](#20-interpreting-benchmark-results)
- [21. Failure Reporting](#21-failure-reporting)
- [22. Benchmark Status and Implementation Boundaries](#22-benchmark-status-and-implementation-boundaries)
- [23. Recommended Benchmark Build Sequence](#23-recommended-benchmark-build-sequence)
- [24. What Benchmark Results Can and Cannot Establish](#24-what-benchmark-results-can-and-cannot-establish)
- [25. Related Documentation](#25-related-documentation)
- [26. Source Basis and Terminology](#26-source-basis-and-terminology)

---

## 1. Benchmark Objective

The primary objective of the ChandraMap benchmark is to measure whether a system can establish **reliable correspondence and registration between lunar images that differ in sensor, resolution, scale, illumination, viewpoint, modality, or image geometry**.

The benchmark is therefore centered on the complete measurable chain:

```text
Source Image
    ↓
Candidate Region / Retrieval
    ↓
Candidate Correspondences
    ↓
Geometric Verification
    ↓
Verified Inliers
    ↓
Sub-Pixel Refinement
    ↓
Final Transformation
    ↓
Registered Image
    ↓
Independent Evaluation
```

The benchmark evaluates different parts of this chain separately rather than collapsing them into a single visual result.

### Primary benchmark questions

A valid benchmark should help answer questions such as:

1. Can the system identify the correct lunar region?
2. Can it establish reliable local correspondences?
3. Can it reject incorrect candidate matches?
4. Are the verified points spatially distributed across the overlap?
5. Can a geometrically valid transformation be estimated?
6. How accurately does that transformation register independent check points?
7. Does sub-pixel refinement improve measurable registration accuracy?
8. How does performance change under scale differences?
9. How does performance change under Sun-angle and illumination differences?
10. How does performance change across sensors and modalities?
11. How does terrain geometry affect the validity of a simple transformation?
12. How frequently does the system fail?
13. What computational resources and runtime are required?

The project feedback explicitly places reliable correspondence, source-image sub-pixel accuracy, distributed match points, registration, and measurable metrics at the center of the SIH problem.

---

## 2. What Is Being Benchmarked?

The benchmark distinguishes **primary measurable outputs** from **downstream visualization outputs**.

### Primary benchmark outputs

| Output                    | Purpose                                                                      |
| ------------------------- | ---------------------------------------------------------------------------- |
| Candidate correspondences | Measure what the matching stage proposes                                     |
| Verified inliers          | Measure geometrically consistent correspondences                             |
| Inlier count              | Measure quantity of verified correspondences                                 |
| Inlier ratio              | Measure the proportion of candidate matches surviving geometric verification |
| Spatial coverage          | Measure whether verified points cover the overlap                            |
| Transformation            | Describe the estimated geometric relationship                                |
| Check-point error         | Measure registration accuracy on points not used for fitting                 |
| Source-pixel error        | Primary unit for sub-pixel registration evaluation                           |
| Ground error              | Physical error in metres when GSD/projection/truth make it meaningful        |
| Runtime                   | Measure computational cost                                                   |
| Failure information       | Record whether and how the pipeline failed                                   |

### Secondary / downstream outputs

These outputs may be useful for inspection and demonstrations:

- registered image previews
- overlays
- residual-vector visualizations
- rejected-match visualizations
- geospatial visualizations
- lunar mosaics

A visually convincing overlay does **not** by itself establish accurate correspondence.

A mosaic is therefore treated as a downstream demonstration of the registration pipeline rather than the primary benchmark objective.

---

## 3. Benchmark Philosophy

The ChandraMap benchmark follows the principles below.

### 3.1 Correspondence comes before visualization

The benchmark must measure the quality of the underlying correspondences and transformation.

A visually attractive registered image cannot replace numerical evaluation.

### 3.2 Independent evaluation is preferred

If points are used to estimate transformation parameters, those same points should not be the only points used to evaluate the transformation.

The project feedback explicitly recommends using challenge ground truth when available or keeping independently checked tie points as check points that are excluded from transformation fitting.

> **Do not fit and evaluate on exactly the same points.**

### 3.3 Candidate matches are not verified matches

A local matcher produces **candidate matches**.

Geometric verification, such as RANSAC, determines which candidates are consistent with the estimated geometric model.

The benchmark therefore keeps these concepts separate:

```text
Candidate Matches
        ↓
Geometric Verification
        ↓
Verified Inliers
```

A matcher confidence score is not equivalent to geometric correctness.

### 3.4 Source-image pixels remain explicit

Sub-pixel registration accuracy should be reported in **source-image pixels first**.

Conversion to metres should only be performed when the product's GSD, projection, and available reference truth make that conversion meaningful.

### 3.5 Retrieval and local matching are different tasks

Global retrieval asks:

> Did the system find the correct lunar region?

Local matching asks:

> Did the system establish reliable correspondences inside the selected region?

These should not be represented by one combined metric.

### 3.6 Match count is not enough

A large number of matches can still be poor if:

- many are incorrect;
- they are concentrated in one small region;
- they do not constrain the transformation adequately;
- they fail under independent evaluation.

Spatial distribution must therefore be measured separately.

### 3.7 Sensor differences must remain visible

OHRC, TMC-2, and IIRS are not interchangeable image sources.

Sensor-specific preprocessing, spatial resolution, modality, and information content must be preserved in benchmark reporting.

The project feedback specifically recommends separate sensor results rather than hiding sensor differences inside one mixed average.

### 3.8 Physical scale matters

Upsampling an image increases pixel count but does not recover missing spatial information.

Where appropriate, higher-resolution reference imagery should be downsampled or represented as a multi-resolution pyramid so that coarse matching operates at physically meaningful scales.

### 3.9 Illumination is a geometric challenge

Different lunar Sun angles can change shadow geometry, not merely image brightness.

Brightness or contrast normalization alone should therefore not be treated as proof of illumination invariance.

The benchmark should explicitly test illumination differences.

### 3.10 Baselines come before complexity

A simple and explainable SIFT-based baseline should establish a measurable reference point before more complicated learned or multimodal approaches are evaluated.

### 3.11 Advanced methods require measured evidence

Methods such as:

- ALIKED + LightGlue
- LoFTR
- RIFT
- CFOG
- future learned or multimodal methods

should be evaluated using the same benchmark protocol as the baseline.

No method should be described as better merely because it is newer or more sophisticated.

The project feedback explicitly recommends testing stronger approaches on the same image pairs and retaining methods based on measured behavior rather than algorithm names.

### 3.12 Failures are benchmark evidence

Failed registrations and difficult cases must not be silently removed.

A benchmark should show:

- successful cases;
- unsuccessful cases;
- failure conditions;
- metric degradation;
- computational behavior.

This is especially important for stress testing.

---

## 4. Benchmark Tracks

The following tracks define the recommended benchmark structure.

> **Status:** These tracks describe the benchmark protocol. Individual tracks should be marked as implemented only when the corresponding datasets, evaluation procedure, and reproducible execution path actually exist.

### Track A — Known-Overlap Registration

**Purpose:** Evaluate local correspondence and registration when the source/reference overlap is known or approximately known.

Measures:

- candidate matches;
- verified inliers;
- inlier ratio;
- spatial coverage;
- transformation quality;
- independent check-point error;
- runtime;
- failure rate.

Conceptual flow:

```text
Known Source/Reference Pair
        ↓
Local Matching
        ↓
RANSAC
        ↓
Verified Inliers
        ↓
Sub-Pixel Refinement
        ↓
Final Transform
        ↓
Independent Check Points
```

This is the recommended starting point because it isolates the core correspondence problem before introducing global retrieval.

---

### Track B — Scale Stress

**Purpose:** Evaluate robustness to large differences in effective ground scale and spatial resolution.

The benchmark should compare cases where the source and reference images contain different levels of resolvable spatial detail.

The protocol should not treat interpolation as information recovery.

Where appropriate:

```text
High-resolution reference
        ↓
Multi-resolution representation
        ↓
Comparable effective scale
        ↓
Coarse matching
        ↓
Fine refinement where justified
```

Relevant metrics include:

- inlier count;
- inlier ratio;
- spatial coverage;
- check-point RMSE;
- failure rate;
- runtime.

---

### Track C — Illumination / Sun-Angle Stress

**Purpose:** Evaluate performance when the same or comparable lunar terrain is observed under different illumination conditions.

Example categories:

- similar illumination;
- moderately different illumination;
- strongly different illumination.

The project materials specifically recommend a Sun-angle stress test using the same region under similar and very different lighting, followed by measurement of the performance drop.

Relevant metrics include:

- candidate matches;
- inlier ratio;
- spatial coverage;
- check-point RMSE;
- failure rate.

---

### Track D — Cross-Sensor / Cross-Modal Stress

**Purpose:** Measure performance when source and reference imagery come from substantially different sensors or modalities.

Examples include:

- OHRC ↔ LRO NAC
- TMC-2 ↔ LRO NAC
- IIRS-derived 2D representation ↔ lunar reference imagery

IIRS should not be treated as an ordinary grayscale camera image without first defining a suitable registration-oriented 2D representation.

The project materials recommend starting with simple representations such as selected bands, PCA/composites, or structural maps before introducing a more complicated hyperspectral matching architecture.

---

### Track E — Global Retrieval

**Purpose:** Evaluate whether the correct lunar region can be retrieved when the source location is unknown or cannot be sufficiently restricted using reliable metadata.

Measures:

- Recall@1;
- Recall@5;
- retrieval runtime;
- candidate-region quality.

This track is conditional.

If reliable latitude/longitude, footprint, or map-projection metadata can validly restrict the search, that metadata may be used instead of forcing a global retrieval problem.

---

### Track F — Geometry Stress

**Purpose:** Evaluate conditions where a simple global transformation may not fully explain the image relationship.

Relevant cases include:

- relief-rich terrain;
- stronger viewpoint differences;
- raw or non-orthorectified imagery;
- spatially varying residuals.

Affine or homography models may be useful initial models for local map-projected pairs, but lunar terrain is not planar.

Residual vectors should therefore be inspected rather than assuming one global transformation is always sufficient.

---

### Track G — Low-Feature / Repetitive Terrain

**Purpose:** Expose failure modes where local appearance provides weak or ambiguous correspondence evidence.

Examples include:

- smooth terrain;
- repetitive terrain;
- weak crater structure;
- visually ambiguous regions.

The objective is not to hide these cases but to measure how the system behaves when false correspondences become more likely.

---

## 5. Sensors and Data Sources

The benchmark may include the following project-relevant data sources.

| Dataset / Sensor                  | Role                                    | Approx. scale / characteristics                                                                                             | Modality                      | Benchmark use                                                          |
| --------------------------------- | --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------- | ----------------------------- | ---------------------------------------------------------------------- |
| **Chandrayaan-2 OHRC**            | Main source/target imagery              | Approximately `~0.25–0.32 m/pixel` in project documentation; use the actual challenge-product metadata for a benchmark pair | Visible panchromatic          | Fine-scale correspondence and registration                             |
| **Chandrayaan-2 TMC-2**           | Main source/target imagery              | Approximately `~5 m/pixel`                                                                                                  | Panchromatic terrain imagery  | Cross-scale correspondence and terrain-structure matching              |
| **Chandrayaan-2 IIRS**            | Main source/target imagery              | Approximately `~80 m/pixel` in project feedback; spectral instrument with substantially coarser spatial resolution          | Hyperspectral / infrared      | Cross-modal experiment using a registration-friendly 2D representation |
| **LRO NAC**                       | Lunar reference imagery                 | Often approximately `~0.5–2 m/pixel`, depending on product/geometry                                                         | High-resolution lunar imagery | Reference imagery and correspondence evaluation                        |
| **LRO WAC**                       | Additional reference / lunar-scale data | Product-dependent                                                                                                           | Lunar imaging                 | Additional scale and illumination experiments                          |
| **Kaguya / SELENE TC**            | Optional cross-sensor data              | Product-dependent                                                                                                           | Lunar imaging                 | Optional research/training/evaluation data                             |
| **Synthetic lunar augmentations** | Controlled stress generation            | Defined by the experiment                                                                                                   | Derived imagery               | Sun-angle, rotation, scale, and contrast stress testing                |

The SIH project specification identifies Chandrayaan-2 OHRC, TMC-2 and IIRS as primary target/source imagery, LRO NAC and WAC as reference/additional lunar data, Kaguya/SELENE TC as optional cross-sensor data, and synthetic augmentations for controlled stress conditions.

### Sensor-specific considerations

#### OHRC

OHRC provides very high-detail visible panchromatic imagery.

The exact pixel scale used in a benchmark must come from the actual product metadata rather than from a generic sensor-level assumption.

#### TMC-2

TMC-2 provides substantially coarser panchromatic terrain imagery than OHRC.

Its terrain structure and available map/terrain information may be useful for correspondence and geometry evaluation.

#### IIRS

IIRS is fundamentally different from ordinary 2D panchromatic imagery.

A benchmark must explicitly record how IIRS data were converted into a registration-friendly representation.

The benchmark should not claim fine spatial correspondence that the source data cannot physically support.

#### LRO NAC

LRO NAC can provide high-resolution lunar reference imagery.

When the reference is substantially finer than the source, a multi-resolution or downsampled representation may be appropriate for coarse matching.

#### LRO WAC

LRO WAC can provide additional lunar-scale and illumination-related reference data.

Its use should be explicitly recorded in the benchmark configuration.

---

## 6. Benchmark Dataset Organization

The exact benchmark directory structure should follow the actual repository state.

The following is a **recommended conceptual organization**, not a claim that every directory currently exists:

```text
benchmarks/
├── README.md
├── configs/
├── datasets/
├── protocols/
├── baselines/
├── scripts/
├── results/
└── reports/
```

Each component has a distinct responsibility.

| Component    | Recommended responsibility                          |
| ------------ | --------------------------------------------------- |
| `configs/`   | Versioned benchmark configurations                  |
| `datasets/`  | Dataset manifests and benchmark definitions         |
| `protocols/` | Evaluation and stress-test specifications           |
| `baselines/` | Baseline method documentation/configuration         |
| `scripts/`   | Reproducible benchmark execution/evaluation tooling |
| `results/`   | Structured measured outputs                         |
| `reports/`   | Human-readable benchmark summaries                  |

### Dataset metadata

A benchmark dataset should make it possible to identify:

- source image;
- reference image;
- source sensor;
- reference sensor;
- product type;
- effective scale/GSD;
- image dimensions;
- overlap;
- projection;
- geolocation metadata;
- illumination metadata;
- viewpoint/geometry metadata;
- ground truth;
- control points;
- independent check points;
- stress category;
- dataset version.

### Dataset separation

Where training is involved, benchmark data should distinguish:

```text
Training
Validation
Test
```

The test set must not be used for method tuning.

If a benchmark does not require training, this separation may not apply, but the evaluation dataset and its use must still be explicitly documented.

---

## 7. Image Pair Definition

A benchmark pair represents a defined relationship between a source image and a reference image.

At minimum, the benchmark definition should identify the two images and the information required to interpret their relationship.

### Recommended conceptual schema

The following is a **conceptual schema**, not an existing project schema unless implemented elsewhere in the repository:

```yaml
pair:
  id: "<pair-id>"
  source:
    image: "<source-image-reference>"
    sensor: "<source-sensor>"
    product_type: "<product-type>"
    gsd_or_pixel_scale: "<value-or-null>"
    width: "<value-or-null>"
    height: "<value-or-null>"

  reference:
    image: "<reference-image-reference>"
    sensor: "<reference-sensor>"
    product_type: "<product-type>"
    gsd_or_pixel_scale: "<value-or-null>"
    width: "<value-or-null>"
    height: "<value-or-null>"

  geometry:
    overlap: "<known-or-unknown>"
    map_projection: "<value-or-null>"
    geolocation_metadata: "<available-or-not>"
    viewpoint_metadata: "<available-or-not>"

  illumination:
    metadata: "<available-or-not>"
    stress_category: "<category-or-null>"

  ground_truth:
    correspondences: "<reference-or-null>"
    transformation: "<reference-or-null>"
    check_points: "<reference-or-null>"

  benchmark:
    track: "<track>"
    stress_category: "<category>"
```

### Important distinction

The schema above describes information a benchmark **should be able to record**.

It does not establish:

- a final file format;
- a final field naming convention;
- a required coordinate system;
- a required dataset path;
- a specific annotation tool.

Those should be defined only when implemented and versioned.

---

## 8. Ground Truth and Evaluation Protocol

Ground truth is the reference against which correspondence and registration performance are evaluated.

### 8.1 Ground-truth correspondences

When reliable correspondence ground truth exists, benchmark evaluation should use it to determine whether estimated correspondences and transformations are correct.

### 8.2 Control points

Control points provide geometric control for transformation estimation.

They may originate from:

- verified correspondences;
- manually checked tie points;
- externally established reference information;
- another validated control-point process.

A control point should not automatically be treated as independent evaluation data.

### 8.3 Independent check points

Independent check points are withheld from transformation fitting and used to evaluate the final transformation.

Conceptually:

```text
Ground-truth / verified points
        │
        ├── Control / fitting points
        │        ↓
        │    Estimate transform
        │
        └── Independent check points
                 ↓
          Evaluate transform
```

This distinction prevents artificially optimistic evaluation.

### 8.4 Transformation fitting

A recommended geometry sequence is:

```text
Candidate Matches
        ↓
RANSAC + Initial Model
        ↓
Verified Inliers
        ↓
Sub-Pixel Refinement
        ↓
Refit Final Transform
        ↓
Independent Check-Point Evaluation
```

The project feedback explicitly recommends refining verified inlier coordinates and then estimating the final transformation again from the refined points.

### 8.5 Source-pixel evaluation

Sub-pixel accuracy should first be reported in source-image pixels.

For a check point \(i\), if the reference position is \((x_i, y_i)\) and the transformed prediction is \((\hat{x}\_i, \hat{y}\_i)\), the point error in source-image pixel coordinates can be expressed as:

$$
e_i =
\sqrt{
(x_i-\hat{x}_i)^2 +
(y_i-\hat{y}_i)^2
}
$$

The benchmark should report the evaluation convention explicitly.

### 8.6 Ground error

Conversion to metres is appropriate only when:

- the GSD is meaningful for the measurement;
- the projection is understood;
- the reference geometry supports the conversion;
- the ground truth supports a physical-distance interpretation.

A pixel error of the same numerical value does not imply the same physical error across sensors with different spatial scales.

---

## 9. Metrics

The benchmark should report metrics by evaluation stage.

| Stage          | Metric                | Definition                                                                       | Interpretation                                                 |
| -------------- | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------------------- |
| Retrieval      | Recall@1              | Fraction of cases where the correct region is ranked first                       | Higher is generally better                                     |
| Retrieval      | Recall@5              | Fraction of cases where the correct region appears in the top five               | Higher is generally better                                     |
| Local matching | Candidate match count | Number of proposed correspondences before geometric verification                 | Descriptive; not sufficient alone                              |
| Local matching | Inlier count          | Number of correspondences accepted by geometric verification                     | Higher can be useful, but quality/distribution matter          |
| Local matching | Inlier ratio          | Verified inliers divided by candidate matches                                    | Higher is generally better                                     |
| Distribution   | Grid coverage         | Fraction of spatial cells containing qualifying verified points                  | Higher is generally better                                     |
| Distribution   | Convex-hull coverage  | Area covered by the correspondence convex hull relative to the evaluation region | Higher is generally better                                     |
| Registration   | Check-point RMSE      | RMSE over independent evaluation points                                          | Lower is better                                                |
| Registration   | Source-pixel error    | Registration error measured in source-image pixels                               | Lower is better                                                |
| Registration   | Sub-pixel error       | Error evaluated at sub-pixel precision where supported                           | Lower is better                                                |
| Geospatial     | Ground error          | Physical registration error in metres when meaningful                            | Lower is better                                                |
| System         | Runtime               | Execution time under a defined environment                                       | Lower is generally better                                      |
| System         | Failure rate          | Fraction of benchmark cases that fail according to the defined failure rule      | Lower is better                                                |
| System         | Memory / GPU usage    | Resource consumption when tracked                                                | Lower may be preferable, but must be interpreted with accuracy |

The project feedback identifies Recall@1/Recall@5, inlier count, inlier ratio, grid or convex-hull coverage, check-point RMSE, ground error where meaningful, runtime, and failure rate as relevant evaluation measures.

---

## 10. Metric Definitions

### 10.1 Recall@K

For retrieval:

$$
Recall@K =
\frac{
\text{number of queries where the correct region appears in top }K
}{
\text{number of evaluated queries}
}
$$

Examples:

- `Recall@1`
- `Recall@5`

Higher is generally better.

Retrieval recall measures **region retrieval**, not local correspondence accuracy.

---

### 10.2 Inlier count

Let:

- \(N_c\) = number of candidate correspondences;
- \(N_i\) = number of geometrically verified inliers.

Then:

$$
\text{Inlier Count} = N_i
$$

A larger count can indicate more usable correspondences, but count alone does not establish correctness or spatial coverage.

---

### 10.3 Inlier ratio

$$
\text{Inlier Ratio}
=
\frac{N_i}{N_c}
$$

Higher is generally better.

The ratio must always be interpreted together with:

- inlier count;
- spatial coverage;
- independent registration error.

A very high ratio with only a few clustered points may still be inadequate.

---

### 10.4 Grid coverage

Divide the evaluation region into a defined spatial grid.

For example, a benchmark may use a grid such as:

```text
+----+----+----+----+
|    |    |    |    |
+----+----+----+----+
|    |    |    |    |
+----+----+----+----+
|    |    |    |    |
+----+----+----+----+
|    |    |    |    |
+----+----+----+----+
```

A cell is considered covered when it contains at least one qualifying verified correspondence according to the benchmark's defined rule.

A conceptual formulation is:

$$
\text{Grid Coverage}
=
\frac{\text{covered cells}}
{\text{total eligible cells}}
$$

Higher is generally better.

The exact grid size and qualifying-point rule must be versioned by the benchmark protocol rather than assumed.

The project feedback uses spatial coverage as an explicit metric and gives a `4 × 4` grid as an example of a possible measurement convention.

---

### 10.5 Convex-hull coverage

Given verified correspondence locations, construct their spatial convex hull.

A conceptual coverage measure is:

$$
\text{Convex Hull Coverage}
=
\frac{
A_{\text{hull}}
}{
A_{\text{evaluation region}}
}
$$

where:

- \(A\_{\text{hull}}\) is the convex-hull area;
- \(A\_{\text{evaluation region}}\) is the defined overlap/evaluation area.

Higher is generally better when the denominator and eligibility rules are consistent.

The exact treatment of image boundaries and non-overlap regions must be defined by the benchmark protocol.

---

### 10.6 Check-point RMSE

For \(N\) independent check points:

$$
RMSE =
\sqrt{
\frac{1}{N}
\sum_{i=1}^{N}
e_i^2
}
$$

where \(e_i\) is the registration error for check point \(i\).

Lower is better.

The benchmark should specify whether the RMSE is reported in:

- source-image pixels;
- reference-image pixels;
- metres;
- another explicitly defined unit.

For ChandraMap, **source-image pixels should be the primary sub-pixel reporting unit**.

---

### 10.7 Ground error

When physical conversion is justified:

$$
e_{\text{ground}}
$$

may be reported in metres.

This should not be calculated merely by multiplying a pixel error by an assumed sensor resolution when the projection or geometry makes that interpretation invalid.

---

### 10.8 Runtime

Runtime should be reported with the relevant execution context.

At minimum, the benchmark should identify:

- measured stage or end-to-end scope;
- hardware;
- software environment;
- whether preprocessing is included;
- whether model/index loading is included;
- whether retrieval and local matching are included.

Runtime values from different hardware environments should not be directly compared without qualification.

---

### 10.9 Failure rate

A benchmark must define what constitutes a failure.

Examples may include:

- no valid candidate region retrieved;
- insufficient candidate correspondences;
- geometric verification failure;
- transformation estimation failure;
- invalid registration output;
- inability to evaluate required check points.

The exact failure rule should be versioned with the benchmark.

---

### 10.10 No automatic composite score

The benchmark does **not** define an overall score by default.

Metrics should remain interpretable.

For example:

- recall measures retrieval;
- inlier ratio measures geometric filtering;
- coverage measures distribution;
- RMSE measures registration accuracy;
- runtime measures computational cost.

A single aggregate score should only be introduced if a formal scoring protocol is explicitly defined and documented.

---

## 11. Baselines

### Baseline 1 — SIFT

The primary initial accuracy baseline is a SIFT-based correspondence pipeline.

Conceptual flow:

```text
Input
  ↓
Sensor-specific preprocessing
  ↓
Scale handling / multi-resolution representation
  ↓
SIFT keypoints + descriptors
  ↓
Descriptor matching
  ↓
Filtering
  ↓
RANSAC
  ↓
Transformation
  ↓
Registration
  ↓
Independent check-point evaluation
```

The project feedback recommends:

```text
SIFT
→ descriptor matching
→ ratio/cross-check filtering
→ RANSAC
→ affine/homography
→ residual error
```

as the initial baseline.

### Why SIFT is useful as a baseline

A baseline should be:

- understandable;
- reproducible;
- relatively simple;
- measurable;
- strong enough to provide a meaningful reference point.

The purpose is not to claim that SIFT is the final method.

The purpose is to determine whether later modifications produce measurable improvement.

---

### Experimental Baseline 2 — ALIKED + LightGlue

Potential experimental path:

```text
ALIKED
   ↓
Sparse learned features
   ↓
LightGlue
   ↓
Geometric verification
   ↓
Registration evaluation
```

The benchmark should measure this path against the same image pairs used for the baseline.

The project materials caution that pretrained terrestrial features are not automatically lunar-invariant.

---

### Experimental Baseline 3 — LoFTR

LoFTR provides a detector-free matching approach that may be useful where repeatable local keypoints are weak.

It should remain a separately evaluated matching path rather than being treated as a feature extractor inside the SIFT pipeline.

Its performance must be measured under the same benchmark protocol.

---

### Experimental Baseline 4 — RIFT / CFOG-style approaches

RIFT and CFOG-style approaches may be considered research baselines for difficult multimodal or structural matching cases.

Their inclusion should depend on actual implementation and reproducible evaluation.

---

### Baseline comparison rule

The benchmark should compare methods on the **same image pairs** and use the **same evaluation definitions**.

A recommended comparison is:

```text
Same Image Pairs
      │
      ├── SIFT baseline
      │
      ├── Stronger local matcher
      │
      └── Full sensor-aware / multi-scale pipeline
```

This helps isolate whether improvements come from:

- preprocessing;
- scale handling;
- local matching;
- geometric verification;
- sub-pixel refinement;
- sensor-specific processing.

The project feedback explicitly recommends this comparison structure.

---

## 12. Retrieval Benchmark

Global retrieval is an optional benchmark stage.

It becomes especially relevant when:

- source location is unknown;
- reliable geolocation metadata is unavailable;
- search cannot be sufficiently restricted using valid metadata.

### Recommended retrieval architecture

```text
Offline
────────────────────────────────────

Reference Images
      ↓
Tiles + Scales
      ↓
Global Descriptors
      ↓
FAISS Index
      +
Metadata


Online
────────────────────────────────────

Source Image
      ↓
Source Global Descriptor
      ↓
FAISS Search
      ↓
Top-K Candidate Tiles
      ↓
Local Matching
      ↓
Geometric Verification
```

The project feedback explicitly separates the offline reference index from online source-image retrieval and emphasizes that FAISS performs vector similarity search rather than local image matching.

### Retrieval evaluation

Primary retrieval metrics:

- Recall@1
- Recall@5

Additional reporting may include:

- retrieved candidate identifiers;
- retrieval runtime;
- correct candidate rank;
- examples of incorrect candidate regions.

### Metadata-assisted search

When reliable metadata exists, search may be constrained using:

- footprint;
- latitude/longitude;
- map projection;
- other valid geospatial information.

This should be recorded in the benchmark configuration.

Metadata-based restriction is not equivalent to global image retrieval and should not be silently mixed into retrieval results.

---

## 13. Registration Evaluation Pipeline

The recommended evaluation sequence is:

### Step 1 — Input selection

Select a versioned source/reference pair from the benchmark dataset.

### Step 2 — Sensor-specific preprocessing

Prepare the imagery according to the sensor and product characteristics.

Do not force OHRC, TMC-2, and IIRS through an identical preprocessing path.

### Step 3 — Effective-scale handling

Bring the images into a physically meaningful comparison scale where appropriate.

This may involve:

- reference pyramids;
- downsampling;
- multi-scale search;
- sensor-specific representations.

Upsampling must not be interpreted as recovering missing spatial detail.

### Step 4 — Candidate retrieval

If required:

```text
Source
→ global descriptor
→ retrieval
→ Top-K candidates
```

If valid metadata already restricts the search, record that instead.

### Step 5 — Local matching

Generate local candidate correspondences.

### Step 6 — Candidate-match recording

Record candidate correspondences separately from verified inliers.

### Step 7 — Geometric verification

Use RANSAC or another explicitly defined geometric verification method.

### Step 8 — Verified inliers

Record:

- inlier count;
- inlier ratio;
- spatial locations;
- geometric model;
- residual information where available.

### Step 9 — Sub-pixel refinement

Refine verified correspondence coordinates locally where the source information supports such precision.

### Step 10 — Final transform refit

Estimate the final transformation using the refined control/tie points.

### Step 11 — Registration

Generate the registered output.

### Step 12 — Independent evaluation

Evaluate the transformation using independent check points.

### Step 13 — Spatial coverage evaluation

Measure the distribution of verified points.

### Step 14 — Runtime and failure measurement

Record execution time and whether the run completed successfully.

### Step 15 — Result recording

Store the configuration, metrics, environment, provenance, and artifacts required to reproduce and interpret the result.

The project materials describe the core geometry flow as:

```text
Local Matches
→ RANSAC
→ Inliers
→ Sub-Pixel Tie Points
→ Final Model
→ Registered Image
```

and recommend independent check-point evaluation.

---

## 14. Stress-Test Matrix

The benchmark should maintain a controlled stress-test matrix.

| Stress case         | Main variable                                             | What it tests                             | Key metrics                                |
| ------------------- | --------------------------------------------------------- | ----------------------------------------- | ------------------------------------------ |
| Easy pair           | Moderate scale/illumination difference with known overlap | End-to-end pipeline validity              | Inlier ratio, coverage, RMSE, runtime      |
| Sun-angle stress    | Illumination / shadow geometry                            | Robustness to changing lunar illumination | Inlier ratio, coverage, RMSE, failure rate |
| Scale stress        | Effective GSD / resolution difference                     | Multi-scale matching                      | Inlier ratio, coverage, RMSE, runtime      |
| Modality stress     | Sensor / representation difference                        | Cross-modal correspondence                | Inlier ratio, coverage, RMSE, failure rate |
| Geometry stress     | Viewpoint / relief / spatial geometry                     | Validity of transformation model          | RMSE, residuals, failure rate              |
| Low-feature terrain | Weak or repetitive terrain structure                      | False-match susceptibility                | Inlier ratio, coverage, RMSE, failure rate |

### Easy pair

Purpose:

> Prove that the complete pipeline works end-to-end.

A useful first milestone is:

```text
Known source/reference pair
→ SIFT
→ RANSAC
→ transform
→ registration
→ independent check-point error
```

The project feedback recommends this as the first end-to-end milestone.

### Sun-angle stress

Use comparable lunar terrain under substantially different illumination conditions.

The benchmark should report performance degradation rather than simply labeling the system "Sun-angle invariant."

### Scale stress

Evaluate increasingly different effective scales.

The benchmark should distinguish:

- actual spatial information;
- interpolation;
- multi-resolution representation;
- genuine correspondence performance.

### Modality stress

Measure performance when source and reference images differ in sensing modality or representation.

IIRS should be treated as a dedicated experiment rather than simply another grayscale input.

### Geometry stress

Evaluate terrain and viewing conditions where a simple global model may produce spatially varying residuals.

Residual vectors should be inspected.

### Low-feature terrain

Retain difficult cases even when the pipeline fails.

These cases are valuable for understanding the limits of the correspondence system.

---

## 15. Fair Comparison Protocol

Two methods should be compared under the same evaluation conditions wherever practical.

### Required comparison rules

1. Use the same image pairs.
2. Use the same ground truth.
3. Use the same independent check points.
4. Use the same train/validation/test split where applicable.
5. Use the same metric definitions.
6. Apply the same failure-handling rules.
7. Keep sensor-specific preprocessing explicit.
8. Record differences in preprocessing.
9. Do not tune the method using the test set.
10. Report failed cases.
11. Report sensor/path-specific results.
12. Use the same or clearly documented hardware when comparing runtime.
13. Keep benchmark versions fixed.
14. Preserve the exact configuration used for each result.

### What must not happen

Do not compare:

```text
Method A → easy pairs
Method B → difficult pairs
```

and then interpret the difference as a method improvement.

Instead:

```text
Same Pair Set
      │
      ├── Method A
      │
      ├── Method B
      │
      └── Method C
```

The same principle applies to preprocessing experiments.

---

## 16. Benchmark Result Format

Benchmark results should contain enough information to identify:

- what was run;
- on what data;
- with which method;
- under which protocol;
- using which configuration;
- under which environment;
- with which measured results.

The following is a **conceptual result schema**. It is not a claim that this exact YAML schema has already been implemented.

```yaml
benchmark:
  name: "<benchmark-name>"
  version: "<benchmark-version>"
  track: "<track-name>"
  dataset: "<dataset-version>"

source_sensor: "<source-sensor>"
reference_sensor: "<reference-sensor>"

method:
  name: "<method-name>"
  version: "<method-version>"

preprocessing:
  description: "<configuration-reference>"

scale_strategy:
  description: "<configuration-reference>"

matcher:
  name: "<matcher-name>"

geometric_model:
  name: "<model-name>"

subpixel_refinement:
  enabled: "<true-or-false>"
  method: "<method-or-null>"

metrics:
  recall_at_1: "<value-or-null>"
  recall_at_5: "<value-or-null>"
  candidate_matches: "<value-or-null>"
  inlier_count: "<value-or-null>"
  inlier_ratio: "<value-or-null>"
  grid_coverage: "<value-or-null>"
  convex_hull_coverage: "<value-or-null>"
  checkpoint_rmse_px: "<value-or-null>"
  ground_error_m: "<value-or-null>"
  runtime_s: "<value-or-null>"
  failure: "<true-or-false>"

environment:
  hardware: "<hardware-description>"
  software: "<software-description>"
  commit: "<repository-commit>"
```

### Placeholder policy

Values such as:

```text
<value-or-null>
<benchmark-name>
<dataset-version>
```

are placeholders and must not be interpreted as benchmark results.

No fabricated scores should be committed to the repository.

---

## 17. Result Storage and Artifacts

Benchmark results should be separated from benchmark definitions.

A recommended conceptual organization is:

```text
benchmarks/
├── README.md
├── ...
├── results/
│   └── <versioned-results>
└── reports/
    └── <human-readable-reports>
```

This structure is **recommended**, not a statement that these directories already exist.

### A reproducible result should preserve

- benchmark version;
- dataset version;
- image-pair identifiers;
- method name/version;
- preprocessing configuration;
- scale strategy;
- matching configuration;
- geometric model;
- refinement configuration;
- measured metrics;
- failure state;
- runtime;
- hardware;
- software environment;
- repository commit;
- relevant output artifacts.

### Useful artifacts

Depending on the benchmark, artifacts may include:

- candidate-match visualization;
- rejected outlier visualization;
- verified-inlier visualization;
- registered preview;
- residual vectors;
- metric summaries;
- machine-readable result records.

A visualization should support the numerical result rather than replace it.

---

## 18. Reproducibility Requirements

A benchmark result should be reproducible from versioned inputs and configuration.

### Minimum reproducibility information

Record:

1. Benchmark name and version.
2. Dataset version.
3. Source/reference pair identifiers.
4. Sensor identities.
5. Preprocessing configuration.
6. Scale-handling configuration.
7. Matching method.
8. Geometric verification method.
9. Transformation model.
10. Sub-pixel refinement configuration.
11. Evaluation-point definition.
12. Hardware.
13. Software environment.
14. Repository commit.
15. Random seed when applicable.
16. Failure state.
17. Result artifacts.

### Reproducibility principle

A result should answer:

> What exactly was executed to produce this number?

If that cannot be determined from the recorded configuration and provenance, the result is incomplete as a research benchmark artifact.

### No hidden preprocessing

If preprocessing differs between methods, the difference must be documented.

For example:

```text
SIFT:
  raw grayscale
```

and

```text
Method B:
  sensor-specific structural representation
```

are not equivalent experimental conditions.

The benchmark must record such differences rather than hiding them.

---

## 19. Adding a New Benchmark

A new benchmark should be added through a controlled process.

### Step 1 — Define the scientific question

State exactly what the benchmark is intended to measure.

Examples:

- scale robustness;
- illumination robustness;
- cross-modal matching;
- geometry robustness;
- retrieval;
- sub-pixel refinement.

### Step 2 — Define the dataset

Document:

- source;
- reference;
- sensor;
- product type;
- scale;
- overlap;
- metadata;
- ground truth;
- evaluation points.

### Step 3 — Define the protocol

Specify:

- preprocessing;
- scale handling;
- matching;
- geometric verification;
- transformation;
- refinement;
- evaluation.

### Step 4 — Define the metrics

Only include metrics relevant to the scientific question.

### Step 5 — Define failure handling

Specify what constitutes:

- success;
- partial success;
- failure;
- invalid evaluation.

### Step 6 — Version the benchmark

Assign a benchmark version when the protocol becomes stable.

A protocol change that can affect numerical results should not silently reuse the old benchmark version.

### Step 7 — Run a baseline

Use the established SIFT baseline where applicable.

### Step 8 — Evaluate alternatives

Run candidate methods on the same benchmark cases.

### Step 9 — Preserve failures

Do not remove cases simply because a method fails.

### Step 10 — Record the result

Store the configuration and measured output using the repository's result format.

---

## 20. Interpreting Benchmark Results

Benchmark metrics should be interpreted together.

### High inlier count

Does not automatically mean:

> accurate registration

Check:

- inlier ratio;
- spatial distribution;
- independent check-point error.

### High inlier ratio

Does not automatically mean:

> broad spatial correspondence

A small number of clustered points can produce a high ratio.

Check:

- inlier count;
- grid coverage;
- convex-hull coverage.

### Low training/fitting error

Does not automatically mean:

> good independent registration

Check-point evaluation is required.

### Good visual overlay

Does not automatically mean:

> scientifically accurate correspondence

Check:

- source-pixel error;
- independent check-point RMSE;
- residual vectors;
- spatial coverage.

### Good retrieval recall

Does not automatically mean:

> good local registration

Retrieval and local matching are different benchmark stages.

### Lower runtime

Does not automatically mean:

> better overall system

Runtime must be interpreted together with:

- accuracy;
- failure rate;
- coverage;
- sensor path;
- benchmark conditions.

---

## 21. Failure Reporting

Failures are first-class benchmark results.

A benchmark report should distinguish at least:

```text
Successful
Failed Retrieval
Failed Matching
Failed Geometric Verification
Failed Transformation
Failed Evaluation
Invalid / Missing Ground Truth
```

The exact categories should be defined by the benchmark implementation.

### Failure reports should preserve context

When possible, record:

- image-pair ID;
- sensor pair;
- stress category;
- method;
- number of candidate matches;
- number of verified inliers;
- spatial coverage;
- transformation status;
- evaluation status;
- runtime;
- error information.

### Do not silently discard failures

Removing difficult cases from the benchmark can create misleading results.

A robust research benchmark should make failure behavior visible.

---

## 22. Benchmark Status and Implementation Boundaries

This README defines the benchmark architecture and evaluation protocol. It does not imply that every component has already been implemented.

The current project materials establish the following technical direction:

| Component                             | Status interpretation                                                         |
| ------------------------------------- | ----------------------------------------------------------------------------- |
| Correspondence as primary objective   | **Specified**                                                                 |
| Registration as measurable output     | **Specified**                                                                 |
| Mosaic as downstream output           | **Specified**                                                                 |
| Independent check-point evaluation    | **Specified**                                                                 |
| Source-pixel-first accuracy reporting | **Specified**                                                                 |
| Spatial coverage evaluation           | **Specified**                                                                 |
| Scale stress testing                  | **Specified**                                                                 |
| Sun-angle stress testing              | **Specified**                                                                 |
| Cross-modality stress testing         | **Specified**                                                                 |
| Geometry stress testing               | **Specified**                                                                 |
| Low-feature stress testing            | **Specified**                                                                 |
| SIFT baseline                         | **Specified as recommended baseline**                                         |
| ALIKED + LightGlue                    | **Experimental candidate**                                                    |
| LoFTR                                 | **Experimental candidate**                                                    |
| RIFT/CFOG-style approaches            | **Research candidate**                                                        |
| Global retrieval                      | **Conditional / benchmark track**                                             |
| FAISS retrieval indexing              | **Architecture specified; implementation status must be verified separately** |
| Formal universal result schema        | **Not established by the source materials**                                   |
| Formal benchmark versioning mechanism | **Not established by the source materials**                                   |
| Exact dataset manifests               | **Not established by the source materials**                                   |
| Exact evaluation thresholds           | **Not established**                                                           |
| Completed benchmark scores            | **Not established by these sources**                                          |

This distinction is intentional.

A benchmark specification should not present planned work as completed experimental evidence.

---

## 23. Recommended Benchmark Build Sequence

The project materials recommend building the benchmark incrementally rather than beginning with a whole-Moon system.

### Phase A — One known pair

```text
Known source/reference pair
        ↓
SIFT
        ↓
RANSAC
        ↓
Transform
        ↓
Registration
        ↓
Independent check-point error
```

Save:

- candidate matches;
- rejected outliers;
- registered overlay;
- inlier statistics;
- check-point error.

### Phase B — Scale + illumination

Add:

- reference pyramid;
- one structure-focused preprocessing experiment;
- controlled illumination stress;
- scale stress.

Compare before/after metrics.

### Phase C — Small retrieval database

Introduce:

```text
Reference tiles
→ multiple scales
→ global descriptors
→ FAISS
→ Top-K
```

Measure:

- Recall@1;
- Recall@5;
- correct and incorrect candidate regions.

### Phase D — Stronger local matcher

Compare:

- SIFT;
- ALIKED + LightGlue;
- LoFTR;

using the same benchmark pairs.

### Phase E — Sub-pixel refinement

Refine verified inlier coordinates and refit the final transformation.

Compare source-pixel check-point RMSE before and after refinement.

### Phase F — Additional sensors

Expand gradually.

The project feedback specifically recommends starting with OHRC/TMC-2 and treating IIRS as its own experiment rather than mixing all sensors into one result.

---

## 24. What Benchmark Results Can and Cannot Establish

### A benchmark can establish

When the protocol and data are valid, benchmark results can provide evidence about:

- correspondence quality;
- geometric verification behavior;
- spatial distribution;
- registration error;
- retrieval performance;
- scale robustness;
- illumination robustness;
- cross-sensor behavior;
- failure behavior;
- runtime under the tested environment.

### A benchmark cannot automatically establish

A result should not be used to claim more than the benchmark measures.

For example:

- A good overlay does not prove sub-pixel accuracy.
- Many matches do not prove correct registration.
- High inlier ratio does not prove broad spatial coverage.
- Good performance on one image pair does not establish general robustness.
- Performance on synthetic perturbations does not automatically establish performance on all naturally difficult lunar scenes.
- A pretrained terrestrial model is not automatically lunar-invariant.
- Upsampling does not recover missing physical detail.
- A single global homography does not prove that lunar geometry is globally planar.
- Good retrieval recall does not prove local correspondence accuracy.
- A good benchmark average does not eliminate sensor-specific failure modes.

### Improvement claims

A method should only be described as improving the system when:

1. it is evaluated on the same benchmark cases;
2. the protocol is held constant;
3. the relevant metrics improve;
4. failures are considered;
5. the improvement is reproducible.

---

## 25. Related Documentation

The benchmark documentation should be read together with the repository's data, ground-truth, baseline, and stress-test documentation.

Where these files exist in the repository, useful related documentation includes:

- [`benchmarks/README.md`](./README.md)
- [`benchmarks/v1/`](./v1/)
- [`benchmarks/baselines/`](./baselines/)
- [`data/`](../data/)
- [`data/ground_truth/`](../data/ground_truth/)
- [`data/samples/`](../data/samples/)

> The links above assume the corresponding paths exist in the repository. If a path has not yet been created, it should not be treated as an implemented component.

### Recommended documentation relationship

```text
Repository Documentation
        │
        ├── Benchmark definition
        │       ↓
        ├── Dataset definition
        │       ↓
        ├── Ground truth / control points
        │       ↓
        ├── Baselines
        │       ↓
        ├── Stress tests
        │       ↓
        └── Measured results
```

The benchmark README defines the evaluation framework; detailed dataset, ground-truth, baseline, and stress-test documents should define their respective components without duplicating the entire benchmark specification.

---

## 26. Source Basis and Terminology

This benchmark specification preserves the technical direction established in the project materials supplied for ChandraMap.

The primary source materials establish:

- reliable correspondence and registration as the core problem;
- OHRC, TMC-2, and IIRS as distinct Chandrayaan-2 sensing paths;
- LRO NAC and LRO WAC as relevant reference data;
- Kaguya/SELENE TC as optional cross-sensor data;
- synthetic augmentation for controlled stress experiments;
- multi-scale search instead of treating upsampling as information recovery;
- illumination-aware structural matching;
- global retrieval as distinct from local matching;
- FAISS as a vector retrieval/indexing mechanism;
- candidate matches as distinct from verified inliers;
- RANSAC before final transformation refinement;
- source-pixel-first sub-pixel evaluation;
- independent check-point evaluation;
- spatial coverage measurement;
- SIFT as an initial explainable baseline;
- ALIKED + LightGlue, LoFTR, RIFT, and CFOG-style methods as experimental/research directions;
- controlled stress testing across illumination, scale, modality, geometry, and low-feature terrain.

The SIH project specification identifies the principal datasets and synthetic augmentation categories used in the proposed system.

The registration feedback defines the evaluation logic around independent check points, inlier statistics, spatial coverage, source-pixel RMSE, ground error where meaningful, runtime, and failure rate.

### Terminology rules

The benchmark should consistently use:

- **Candidate matches** — proposed correspondences before geometric verification.
- **Verified inliers** — correspondences accepted by the geometric verification process.
- **Control points** — reliable points used for geometric control or transformation fitting.
- **Independent check points** — points withheld from fitting and used for evaluation.
- **Transformation** — estimated geometric relationship between source and reference coordinates.
- **Registration** — application of the estimated relationship to align the imagery.
- **Retrieval** — selection of candidate lunar regions before local correspondence.
- **Spatial coverage** — measurement of how correspondence points are distributed over the relevant region.
- **Stress test** — controlled evaluation under a deliberately more difficult condition.

These terms should not be used interchangeably.

---

## Benchmark Quality Checklist

Before accepting a benchmark result, verify:

### Dataset

- [ ] Source image is identified.
- [ ] Reference image is identified.
- [ ] Sensor identities are recorded.
- [ ] Product information is recorded.
- [ ] Effective scale/GSD is recorded where available.
- [ ] Overlap information is defined.
- [ ] Relevant metadata is preserved.
- [ ] Dataset version is recorded.

### Ground truth

- [ ] Ground truth source is documented.
- [ ] Control/fitting points are identified.
- [ ] Independent check points are identified where available.
- [ ] Evaluation points were not used to fit the reported transformation.
- [ ] Coordinate conventions are documented.

### Matching

- [ ] Candidate matches are recorded separately from inliers.
- [ ] Geometric verification is documented.
- [ ] Transformation model is recorded.
- [ ] Spatial distribution is measured.

### Evaluation

- [ ] Source-pixel error is reported where applicable.
- [ ] Check-point RMSE is reported where applicable.
- [ ] Ground error is only reported when physically meaningful.
- [ ] Retrieval metrics are separated from local matching metrics.
- [ ] Runtime is measured under a documented environment.
- [ ] Failures are retained and reported.

### Comparison

- [ ] Methods use the same benchmark pairs.
- [ ] Ground truth is identical.
- [ ] Evaluation points are identical.
- [ ] Metric definitions are identical.
- [ ] Test-set tuning is avoided.
- [ ] Sensor-specific results are visible.
- [ ] Preprocessing differences are documented.

### Reproducibility

- [ ] Benchmark version is recorded.
- [ ] Dataset version is recorded.
- [ ] Repository commit is recorded.
- [ ] Configuration is preserved.
- [ ] Hardware/software environment is recorded.
- [ ] Random seeds are recorded where applicable.
- [ ] Relevant output artifacts are preserved.

---

## Core Benchmark Principle

> **Build small. Measure honestly. Keep the failures.**

For ChandraMap, the benchmark is not primarily a demonstration of how well a final lunar mosaic looks.

It is evidence that the system can:

```text
Find the right region
        ↓
Find reliable correspondences
        ↓
Reject incorrect matches
        ↓
Estimate valid geometry
        ↓
Refine verified points
        ↓
Register the imagery
        ↓
Measure independent error
        ↓
Report spatial coverage
        ↓
Expose failure conditions
```

That evaluation chain is the basis for comparing ChandraMap methods across sensors, scales, illumination conditions, modalities, terrain geometry, and retrieval conditions.
