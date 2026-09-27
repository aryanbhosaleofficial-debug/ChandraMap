# ChandraMap V1 Benchmark

**ChandraMap V1** is the foundational benchmark for known-overlap lunar image correspondence and registration.

V1 evaluates whether ChandraMap can take a known overlapping lunar **source/reference image pair**, produce candidate correspondences, reject incorrect matches through geometric verification, estimate a registration transform, generate a registered result, and measure registration accuracy using **independent check points**.

The benchmark is intentionally small and controlled. It establishes a reproducible baseline before introducing global retrieval, learned matchers, cross-modal experiments, or large-scale lunar mapping.

The benchmark follows the project guidance that correspondence and registration quality are the primary outputs; a lunar mosaic is a downstream demonstration rather than the primary benchmark objective.

---

## Table of Contents

- [1. V1 Overview](#1-v1-overview)
- [2. V1 Objective](#2-v1-objective)
- [3. V1 Scope](#3-v1-scope)
- [4. V1 Benchmark Pipeline](#4-v1-benchmark-pipeline)
- [5. Input Definition](#5-input-definition)
- [6. Sensor Considerations](#6-sensor-considerations)
- [7. V1 Baseline Method](#7-v1-baseline-method)
- [8. Geometric Verification](#8-geometric-verification)
- [9. Independent Evaluation Points](#9-independent-evaluation-points)
- [10. V1 Metrics](#10-v1-metrics)
- [11. Metric Definitions](#11-metric-definitions)
- [12. V1 Success Criteria](#12-v1-success-criteria)
- [13. V1 Evaluation Protocol](#13-v1-evaluation-protocol)
- [14. Benchmark Reproducibility](#14-benchmark-reproducibility)
- [15. V1 Result Format](#15-v1-result-format)
- [16. Benchmark Artifacts](#16-benchmark-artifacts)
- [17. Failure Handling](#17-failure-handling)
- [18. Scientific Limitations](#18-scientific-limitations)
- [19. V1 vs Future Versions](#19-v1-vs-future-versions)
- [20. Benchmark Design Principles](#20-benchmark-design-principles)
- [21. Current V1 Status](#21-current-v1-status)

---

## 1. V1 Overview

### What is V1?

V1 is the **foundational known-overlap registration benchmark** for ChandraMap.

It evaluates a controlled source/reference pair where the overlapping lunar region is already known or otherwise constrained sufficiently to avoid making global lunar retrieval the primary problem.

The benchmark focuses on the complete registration path:

```text
Known source/reference pair
        ↓
Input validation
        ↓
Sensor / metadata check
        ↓
Preprocessing
        ↓
SIFT features
        ↓
Descriptor matching
        ↓
Candidate matches
        ↓
RANSAC geometric verification
        ↓
Verified inliers
        ↓
Transformation estimation
        ↓
Registered image
        ↓
Independent check-point evaluation
        ↓
Metrics + benchmark artifacts
```

### Why does V1 exist?

The project should first demonstrate one measurable end-to-end result before attempting to solve the entire lunar search and registration problem.

The recommended build order explicitly starts with:

```text
One known pair
    ↓
SIFT
    ↓
RANSAC
    ↓
Transformation
    ↓
Registered overlay
    ↓
Metrics
```

The first milestone is considered complete when a source/reference pair progresses from input through candidate matches, verified inliers, a final transformation, a registered overlay, and a numerical error measured on independent check points.

### Scientific question

V1 asks:

> **Can ChandraMap reliably establish image correspondences and quantitatively register a known overlapping lunar source/reference pair using a reproducible SIFT + geometric verification baseline?**

### What counts as a successful V1 run?

A valid V1 run should demonstrate that:

- the input pair is accepted and validated;
- candidate correspondences can be generated;
- incorrect correspondences can be rejected geometrically;
- verified inliers can be identified;
- a valid transformation can be estimated;
- a registered result can be produced;
- independent check-point error can be measured;
- spatial distribution of verified correspondences can be evaluated;
- configuration and execution information can be recorded.

### What V1 does not attempt to solve

V1 is **not** the full ChandraMap system.

It does not automatically solve:

- whole-Moon image retrieval;
- large-scale reference indexing;
- global descriptor retrieval;
- FAISS-based retrieval;
- full cross-modal hyperspectral registration;
- large learned matching systems;
- complete lunar mosaicking;
- universal Sun-angle invariance;
- universal viewpoint invariance;
- production-scale processing;
- unsupported sub-pixel accuracy claims.

The project feedback specifically recommends proving one measurable end-to-end result before expanding to the whole Moon.

---

## 2. V1 Objective

### Primary objective

Evaluate whether ChandraMap can reliably establish image correspondences and register a **known overlapping lunar source/reference pair** using a reproducible SIFT-based local matching and geometric verification baseline.

### V1 evaluates four core capabilities

| Capability              | V1 question                                                             |
| ----------------------- | ----------------------------------------------------------------------- |
| Correspondence          | Can the system generate useful candidate matches?                       |
| Geometric verification  | Can incorrect correspondences be rejected?                              |
| Registration            | Can a transformation produce a registered result?                       |
| Quantitative evaluation | Does the transformation perform accurately on independent check points? |

### Primary output

The primary V1 output is **measurable correspondence and registration quality**.

A registered image or overlay is useful for inspection, but visual alignment alone is not considered sufficient evidence of benchmark success.

The project feedback explicitly emphasizes reliable matched points, transformation/geolocation, registered output, and measurable quality metrics, while treating a final mosaic as a downstream demonstration.

---

## 3. V1 Scope

| Component                          | V1 Status                  | Description                                                          |
| ---------------------------------- | -------------------------- | -------------------------------------------------------------------- |
| Known-overlap image pair           | **Included**               | Primary V1 benchmark input                                           |
| Source/reference images            | **Included**               | Controlled lunar image pair                                          |
| Sensor/product metadata            | **Included**               | Record available sensor, product, scale and geometry information     |
| Basic preprocessing                | **Included**               | Only preprocessing required by the selected input products           |
| Sensor-aware routing               | **Included**               | Inputs must not be treated as physically identical sensors           |
| SIFT feature extraction            | **Included**               | Foundational local-feature baseline                                  |
| Descriptor matching                | **Included**               | Produces candidate correspondences                                   |
| Match filtering                    | **Included**               | Configured filtering before geometric verification                   |
| Candidate matches                  | **Included**               | Explicit intermediate benchmark output                               |
| RANSAC/geometric verification      | **Included**               | Rejects geometrically inconsistent correspondences                   |
| Verified inliers                   | **Included**               | Explicitly separated from candidate matches                          |
| Transformation estimation          | **Included**               | Uses the transformation model defined by the implementation/protocol |
| Registration                       | **Included**               | Produces a registered result                                         |
| Independent check points           | **Included**               | Required for quantitative registration evaluation                    |
| Spatial match distribution         | **Included**               | Measures whether verified points cover the overlap                   |
| Runtime                            | **Included**               | Recorded as a system metric                                          |
| Failure status                     | **Included**               | Failed runs must be recorded rather than silently discarded          |
| Reproducibility metadata           | **Included**               | Configuration and environment information are recorded               |
| Sub-pixel refinement               | **Optional / Deferred**    | Applied only after verified control points are established           |
| Global retrieval                   | **Deferred**               | Not required for the known-overlap V1 problem                        |
| FAISS retrieval                    | **Out of scope**           | Belongs to a later retrieval stage unless explicitly assigned to V1  |
| Global descriptors                 | **Out of scope**           | Not required for known-overlap V1                                    |
| Learned local matchers             | **Deferred**               | ALIKED + LightGlue or LoFTR may be evaluated later                   |
| IIRS full hyperspectral processing | **Out of scope**           | V1 must not silently turn into a full hyperspectral benchmark        |
| Whole-Moon retrieval               | **Out of scope**           | Requires a substantially different benchmark                         |
| Whole-Moon mosaicking              | **Out of scope**           | Downstream capability                                                |
| Final lunar mosaic                 | **Optional demonstration** | Must not replace correspondence and registration metrics             |

The project feedback recommends starting with SIFT, then testing stronger learned methods on the same pairs only after the baseline is measurable.

---

## 4. V1 Benchmark Pipeline

The V1 conceptual pipeline is:

```text
SOURCE + REFERENCE
        │
        ▼
INPUT VALIDATION
        │
        ▼
SENSOR / METADATA CHECK
        │
        ▼
PREPROCESSING
        │
        ▼
SIFT KEYPOINTS + DESCRIPTORS
        │
        ▼
DESCRIPTOR MATCHING
        │
        ▼
MATCH FILTERING
        │
        ▼
CANDIDATE MATCHES
        │
        ▼
RANSAC / GEOMETRIC VERIFICATION
        │
        ▼
VERIFIED INLIERS
        │
        ▼
TRANSFORMATION ESTIMATION
        │
        ▼
REGISTERED IMAGE
        │
        ▼
INDEPENDENT CHECK-POINT EVALUATION
        │
        ▼
METRICS + ARTIFACTS
```

### 4.1 Input validation

Validate the source/reference pair before feature extraction.

Validation should establish, where information is available:

- image readability;
- image dimensions;
- product/sensor identity;
- pixel scale/GSD;
- projection/map metadata;
- approximate overlap;
- viewing geometry;
- illumination metadata.

Missing metadata must be recorded as missing rather than silently fabricated.

---

### 4.2 Sensor / metadata check

V1 should remain sensor-aware.

Metadata may provide information that can constrain or simplify registration, including:

- footprint;
- latitude/longitude;
- pixel scale;
- map projection;
- viewing geometry;
- illumination geometry.

Using reliable metadata to constrain a known-overlap benchmark is part of good engineering and does not replace the correspondence evaluation. The project feedback specifically recommends using metadata when available and retaining an image-based fallback when it is not.

---

### 4.3 Preprocessing

Preprocessing should make the inputs more comparable without creating information that the source sensor does not contain.

Possible operations include:

- calibration/product normalization where required;
- light denoising;
- local contrast normalization;
- grayscale conversion where appropriate;
- gradient/edge or structure-focused representations;
- scale normalization through physically meaningful resampling.

Every preprocessing step should have a measurable purpose.

The benchmark should avoid preprocessing that changes the scientific interpretation of the source data.

---

### 4.4 SIFT feature extraction

V1 uses **SIFT** as the foundational local-feature baseline.

The baseline consists of:

```text
Image
  ↓
SIFT keypoints
  ↓
SIFT descriptors
```

SIFT provides an interpretable starting point for evaluating whether conventional local correspondence can solve the controlled V1 problem.

The project feedback identifies SIFT → descriptor matching as the simple, explainable baseline from which later methods can be compared.

---

### 4.5 Descriptor matching

Descriptors from the source and reference images are compared to generate potential correspondences.

The exact matching configuration must be recorded with the benchmark result.

The matcher may apply the filtering strategy defined by the implementation, but the output at this stage remains:

> **Candidate Matches**

Candidate matches are not yet considered geometrically valid.

---

### 4.6 Match filtering

Configured filtering may remove obviously weak or ambiguous descriptor matches before geometric verification.

Filtering should be deterministic or reproducible when possible.

Any important matching parameters must be recorded in the benchmark configuration.

---

### 4.7 Candidate matches

Candidate matches represent correspondences proposed by the local matching stage.

They may contain incorrect matches.

Therefore:

```text
Candidate Match ≠ Verified Inlier
```

A descriptor-level confidence or similarity score is not sufficient evidence that a correspondence is geometrically correct.

The project feedback specifically recommends calling these **Candidate Matches** and allowing RANSAC/geometric verification to determine which candidates become verified inliers.

---

### 4.8 RANSAC / geometric verification

RANSAC estimates a geometric model while rejecting correspondences that are inconsistent with that model.

The result is:

```text
Candidate Matches
        ↓
RANSAC
        ↓
Verified Inliers
```

The benchmark must record both candidate and verified match counts.

---

### 4.9 Transformation estimation

After geometric verification, the verified correspondences are used to estimate the registration transformation.

The exact transformation model must be defined by the implementation/protocol.

V1 must **not assume a transformation model that has not been established by the project specification**.

For a local, already map-projected pair, affine or homography-based models may be appropriate starting models, but the Moon is not a flat surface and a single global transformation is not guaranteed to explain every real lunar image pair.

---

### 4.10 Registration

The estimated transformation is applied to produce a registered source/reference result.

The benchmark should retain the registered result as an artifact for visual inspection.

However:

> A visually convincing overlay is not sufficient evidence of accurate registration.

Quantitative evaluation must be performed separately.

---

### 4.11 Independent check-point evaluation

The final transformation is evaluated using points that were **not used to fit the transformation**.

This is a mandatory V1 principle.

```text
Verified control points
        ↓
Transformation estimation
        ↓
Final transformation
        ↓
Independent check points
        ↓
Registration error
```

This prevents the benchmark from reporting an artificially low error caused by evaluating the model on the same points used to estimate it.

The project feedback explicitly identifies independent check-point evaluation as necessary for defensible registration measurements.

---

## 5. Input Definition

A valid V1 benchmark input consists of a known overlapping **source/reference image pair**.

### 5.1 Source image

The source image is the lunar image being registered.

The benchmark should record:

- source image identifier;
- sensor/product;
- dimensions;
- pixel scale/GSD, when available;
- projection;
- footprint;
- acquisition information;
- illumination information, when available;
- viewing geometry, when available.

### 5.2 Reference image

The reference image provides the registration target.

The benchmark should record:

- reference image identifier;
- sensor/product;
- dimensions;
- pixel scale/GSD, when available;
- projection;
- footprint;
- acquisition information;
- illumination information, when available;
- viewing geometry, when available.

### 5.3 Overlap

The V1 pair must have known or sufficiently constrained overlap.

The benchmark should record how the overlap is established when such information is available.

The exact V1 dataset filenames and pair identifiers should come from the repository's benchmark dataset definition. They should not be invented in this document.

### 5.4 Metadata availability

Metadata should be categorized explicitly:

| Metadata              | Possible state          |
| --------------------- | ----------------------- |
| Pixel scale/GSD       | Available / unavailable |
| Map projection        | Available / unavailable |
| Geographic footprint  | Available / unavailable |
| Latitude/longitude    | Available / unavailable |
| Viewing geometry      | Available / unavailable |
| Illumination geometry | Available / unavailable |
| Product information   | Available / unavailable |

Unavailable metadata must remain explicitly unavailable.

---

## 6. Sensor Considerations

V1 must not treat every lunar sensor as if it were the same imaging system.

The project materials identify OHRC, TMC-2, and IIRS as substantially different Chandrayaan-2 instruments and recommend separate sensor-aware handling.

### 6.1 OHRC

OHRC provides very high-detail visible panchromatic lunar imagery.

Project feedback cites approximately **0.25–0.32 m/pixel** depending on product/documentation and recommends using the actual challenge product metadata as the authority for a benchmark run.

For V1, OHRC can be treated as a high-detail optical source where the available product supports such processing.

---

### 6.2 TMC-2

TMC-2 provides panchromatic terrain imagery at approximately **5 m/pixel** according to the project materials.

It can provide useful structural overlap with lunar reference imagery and may have associated map-projection or terrain information.

---

### 6.3 IIRS

IIRS is an imaging infrared spectrometer rather than an ordinary panchromatic camera.

The project feedback describes approximately **80 m/pixel** spatial resolution and roughly **250 contiguous spectral bands across approximately 0.8–5.0 μm**.

IIRS should therefore not be silently treated as another grayscale camera.

A registration experiment involving IIRS should first establish an appropriate 2D registration representation, such as:

- selected spectral band;
- PCA/composite representation;
- structural representation.

The exact representation must be recorded.

---

### 6.4 LRO reference imagery

The project materials identify LRO NAC as a potential high-resolution lunar reference, commonly around **0.5–2 m/pixel depending on product and geometry**.

The higher-resolution reference should not be treated as if it creates additional information in a lower-resolution source.

---

### 6.5 Physical scale matters

V1 must compare **physical information**, not simply pixel dimensions.

Do not:

- enlarge a coarse image and treat the result as newly recovered detail;
- claim that upsampling restores missing spatial information;
- infer fine terrain structure that the source sensor cannot resolve.

Instead:

```text
Different GSDs
     ↓
Comparable effective scale
     ↓
Correspondence
     ↓
Fine refinement only where source information supports it
```

The project guidance explicitly states that upsampling changes pixel count rather than recovering missing physical spatial detail.

---

## 7. V1 Baseline Method

The V1 baseline consists of the following sequence.

### Step 1 — Load the source/reference pair

Load the predefined V1 pair.

Record the image identifiers and available metadata.

### Step 2 — Validate metadata

Validate:

- dimensions;
- product/sensor;
- GSD/pixel scale;
- projection;
- overlap information;
- relevant viewing/illumination metadata.

### Step 3 — Perform required preprocessing

Apply only the preprocessing defined for the benchmark configuration.

### Step 4 — Extract SIFT features

Extract:

- keypoint locations;
- SIFT descriptors.

### Step 5 — Perform descriptor matching

Generate candidate correspondences between source and reference descriptors.

### Step 6 — Apply configured filtering

Apply the defined matching/filtering configuration.

### Step 7 — Perform geometric verification

Run RANSAC using the transformation model defined by the V1 implementation/protocol.

### Step 8 — Separate candidates from verified inliers

Record:

```text
candidate_matches
verified_inliers
inlier_ratio
```

### Step 9 — Estimate/refit the final transformation

Estimate the final transformation from the verified control points.

If sub-pixel refinement is enabled by the benchmark configuration:

```text
Candidate matches
      ↓
RANSAC
      ↓
Verified inliers
      ↓
Sub-pixel refinement
      ↓
Refined tie points
      ↓
Final transformation refit
```

Sub-pixel refinement must not be applied as a way to rescue unverified correspondences.

The recommended order is RANSAC → verified inliers → sub-pixel refinement → final transformation refit.

### Step 10 — Produce the registered result

Generate the registered source/reference output.

### Step 11 — Evaluate independent check points

Evaluate the final transformation using independent check points.

### Step 12 — Record metrics

Record all applicable V1 metrics.

### Step 13 — Save benchmark artifacts

Save the outputs necessary to inspect and reproduce the experiment.

---

## 8. Geometric Verification

### Why RANSAC is required

Descriptor similarity alone does not prove that two image locations represent the same physical lunar feature.

A descriptor matcher can produce:

- correct correspondences;
- ambiguous correspondences;
- repeated-feature matches;
- illumination-induced false matches;
- geometrically inconsistent matches.

RANSAC provides a geometric consistency stage.

### Candidate vs verified correspondences

The benchmark must maintain this distinction:

| Stage           | Meaning                                                         |
| --------------- | --------------------------------------------------------------- |
| Candidate match | Proposed by the local matching stage                            |
| Verified inlier | Candidate that is consistent with the estimated geometric model |

A high candidate count is not inherently a successful result.

### Transformation model

The V1 transformation model must be explicitly recorded.

If the repository's benchmark implementation defines a particular model, that implementation definition is authoritative.

If no model is established, this README intentionally does not invent one.

For local map-projected pairs, affine or homography models may be reasonable starting points, but the suitability of a global model must be checked against residual behavior.

### Residual inspection

Residuals should be inspected across the image.

Systematic spatial residual patterns may indicate:

- inadequate transformation geometry;
- perspective/viewing effects;
- terrain relief;
- map-projection issues;
- the need for local or piecewise refinement.

A flexible warp must not be used merely to hide poor correspondences.

---

## 9. Independent Evaluation Points

This section defines a mandatory V1 evaluation principle:

> **Do not fit and evaluate the transformation on exactly the same points.**

### Control points

Verified inliers used to estimate the transformation are **control points**.

### Check points

Independent correspondences are **check points**.

Check points are not used to fit the transformation.

### Evaluation flow

```text
Candidate matches
        ↓
RANSAC
        ↓
Verified control points
        ↓
Transformation
        ↓
Final transformation
        ↓
Independent check points
        ↓
Registration error
```

### Why this matters

If the transformation is estimated and evaluated using the same points, the reported error can be overly optimistic.

Independent check points provide a more meaningful measurement of how well the estimated transformation generalizes across the overlap.

The project feedback explicitly recommends independent check points when challenge ground truth is not otherwise available.

### Ground-truth source

If an official benchmark ground-truth format is defined elsewhere in the repository, that definition must be used.

If it is not yet defined, V1 must not invent a specific ground-truth dataset or file format.

---

## 10. V1 Metrics

V1 should report metrics by category rather than collapsing all behavior into one score.

| Category       | Metric                 | Purpose                                                | Preferred direction        |
| -------------- | ---------------------- | ------------------------------------------------------ | -------------------------- |
| Correspondence | Candidate match count  | Measures number of proposed correspondences            | Context-dependent          |
| Correspondence | Verified inlier count  | Measures geometrically consistent correspondences      | Higher, subject to quality |
| Correspondence | Inlier ratio           | Measures fraction of candidates surviving verification | Higher                     |
| Spatial        | Grid coverage          | Measures spatial distribution of verified points       | Higher                     |
| Spatial        | Convex-hull coverage   | Measures area covered by verified points               | Higher                     |
| Registration   | Check-point RMSE       | Measures registration error on independent points      | Lower                      |
| Geospatial     | Ground error           | Measures physical error when conversion is meaningful  | Lower                      |
| System         | Runtime                | Measures execution cost                                | Lower, context-dependent   |
| System         | Failure/success status | Records whether the run completed successfully         | Successful completion      |

### V1 does not use match count as a standalone success metric

More matches are not necessarily better.

A large number of clustered or geometrically incorrect matches can produce a misleading result.

The project guidance explicitly recommends measuring both inlier quality and spatial distribution.

### Global retrieval metrics

Metrics such as:

- Recall@1;
- Recall@5;

are relevant when global retrieval is introduced.

They are **not required for the known-overlap V1 benchmark** unless the repository's V1 protocol explicitly introduces a retrieval stage.

---

## 11. Metric Definitions

### 11.1 Candidate match count

The number of correspondences produced before geometric verification.

```text
candidate_matches = N_candidate
```

This metric describes the output of the matching stage, not correctness.

---

### 11.2 Verified inlier count

The number of candidate correspondences accepted by the geometric verification stage.

```text
verified_inliers = N_inlier
```

---

### 11.3 Inlier ratio

The fraction of candidate correspondences that survive geometric verification.

$$
\text{Inlier Ratio}
=
\frac{N_{\text{inlier}}}
     {N_{\text{candidate}}}
$$

When no candidate matches exist, the metric should be represented as unavailable rather than forcing a numerical value.

---

### 11.4 Check-point RMSE

For \(N\) independent check points, let the predicted position be:

$$
\hat{\mathbf{p}}_i
$$

and the reference/check-point position be:

$$
\mathbf{p}_i
$$

The source-image pixel error is:

$$
e_i =
\left\|
\hat{\mathbf{p}}_i-\mathbf{p}_i
\right\|_2
$$

The RMSE is:

$$
RMSE =
\sqrt{
\frac{1}{N}
\sum_{i=1}^{N}
e_i^2
}
$$

V1 should report this error in **source-image pixels first**.

---

### 11.5 Conversion to metres

A pixel error may only be converted to physical ground error when:

- the relevant GSD is known;
- the projection/reference geometry makes the conversion meaningful;
- the benchmark has sufficient reference information.

A generic approximation under suitable conditions is:

$$
E_{\text{m}}
\approx
E_{\text{px}}
\times
GSD_{\text{m/px}}
$$

This should not be interpreted as universally valid for every lunar product or viewing geometry.

A source-pixel error must remain the primary reported registration error when physical conversion is not justified.

The project feedback specifically emphasizes reporting sub-pixel error in source-image pixels before converting it to metres.

---

### 11.6 Grid coverage

The overlap can be divided into a predefined spatial grid.

For example:

```text
+----+----+----+----+
|    | XX |    | XX |
+----+----+----+----+
| XX | XX | XX |    |
+----+----+----+----+
|    | XX | XX |    |
+----+----+----+----+
| XX |    | XX | XX |
+----+----+----+----+
```

A cell is considered covered when it contains at least one verified inlier according to the benchmark's coverage definition.

$$
Coverage_{\text{grid}}
=
\frac{N_{\text{covered cells}}}
     {N_{\text{total cells}}}
$$

The exact grid resolution must be part of the benchmark configuration rather than silently changing between experiments.

---

### 11.7 Convex-hull coverage

The verified inlier coordinates can be used to construct a convex hull.

A normalized coverage measure can be defined as:

$$
Coverage_{\text{hull}}
=
\frac{
A_{\text{convex hull}}
}{
A_{\text{evaluation region}}
}
$$

The exact evaluation region and normalization must be defined by the implementation.

Convex-hull coverage should not be interpreted as a substitute for understanding the actual spatial distribution of points.

---

### 11.8 Runtime

Runtime records the execution time of the benchmark according to the defined timing boundary.

The benchmark must record what stages are included in runtime.

Runtime should not be compared across experiments when:

- hardware differs substantially;
- preprocessing boundaries differ;
- data loading is included in one experiment but excluded from another.

---

### 11.9 Failure status

A benchmark run should explicitly indicate whether it succeeded or failed.

Failures should not be silently removed from aggregate reporting.

---

## 12. V1 Success Criteria

V1 intentionally avoids arbitrary numerical thresholds unless those thresholds are formally defined by the project benchmark specification.

A valid V1 run should provide evidence that:

- [ ] The predefined source/reference pair was loaded successfully.
- [ ] Input metadata was validated and recorded.
- [ ] Required preprocessing completed.
- [ ] SIFT features were extracted.
- [ ] Candidate correspondences were produced or a documented matching failure occurred.
- [ ] Geometric verification was attempted.
- [ ] Candidate matches and verified inliers are separately recorded.
- [ ] A transformation was estimated when sufficient verified points existed.
- [ ] A registered result was generated for a successful run.
- [ ] Independent check-point evaluation was performed.
- [ ] Registration error was measured in source-image pixels.
- [ ] Spatial correspondence distribution was evaluated.
- [ ] Runtime was recorded.
- [ ] Failure status was recorded.
- [ ] Configuration and environment information were recorded.
- [ ] Benchmark artifacts were saved.

### Numerical thresholds

No numerical threshold should be invented in this README.

If the repository's formal V1 benchmark specification defines:

- minimum inliers;
- minimum inlier ratio;
- maximum RMSE;
- minimum coverage;
- maximum runtime;

those values must be used exactly by the benchmark implementation.

---

## 13. V1 Evaluation Protocol

The following protocol defines the reproducible V1 experiment.

### Step 1 — Select the predefined V1 pair

Use the image pair defined by the repository's V1 dataset specification.

Do not substitute arbitrary screenshots or undocumented pairs for official benchmark results.

### Step 2 — Record input metadata

Record:

- source identifier;
- reference identifier;
- sensor/product;
- image dimensions;
- GSD/pixel scale;
- projection;
- available footprint;
- available illumination/viewing metadata.

### Step 3 — Load the V1 configuration

Record the complete configuration used for the run.

### Step 4 — Apply preprocessing

Run the configured V1 preprocessing.

### Step 5 — Extract SIFT features

Extract keypoints and descriptors from the source and reference representations.

### Step 6 — Perform descriptor matching

Generate candidate correspondences.

### Step 7 — Apply configured filtering

Apply the benchmark's defined match filtering configuration.

### Step 8 — Estimate the geometric model

Run RANSAC/geometric verification.

### Step 9 — Separate candidates and inliers

Record:

```text
candidate_matches
verified_inliers
inlier_ratio
```

### Step 10 — Refine verified points if enabled

If V1's configuration enables sub-pixel refinement:

```text
verified inliers
        ↓
sub-pixel refinement
        ↓
refined tie points
```

Only verified control points should enter this stage.

### Step 11 — Estimate/refit the final transformation

Use the final control/tie points according to the benchmark configuration.

### Step 12 — Produce the registered result

Generate the registered image/overlay.

### Step 13 — Evaluate independent check points

Calculate registration error on points not used to estimate the transformation.

### Step 14 — Calculate metrics

Calculate all applicable V1 metrics.

### Step 15 — Save artifacts

Save the outputs required for inspection and reproducibility.

### Step 16 — Record the execution environment

Record:

- commit;
- software environment;
- hardware;
- configuration;
- relevant runtime information.

---

## 14. Benchmark Reproducibility

A V1 result should contain enough information for another engineer to understand and reproduce the experiment.

### Required reproducibility information

| Field                    | Requirement              |
| ------------------------ | ------------------------ |
| Benchmark version        | Required                 |
| Dataset version          | Required when defined    |
| Pair identifier          | Required                 |
| Configuration            | Required                 |
| Random seed              | Required when applicable |
| Git commit               | Required                 |
| Runtime/software version | Record when relevant     |
| Dependency versions      | Record when relevant     |
| Hardware                 | Record                   |
| CPU/GPU                  | Record when relevant     |
| Preprocessing settings   | Required                 |
| SIFT settings            | Required                 |
| Matching settings        | Required                 |
| Geometric model settings | Required                 |
| Evaluation configuration | Required                 |

### Reproducibility principle

A benchmark result without its configuration is incomplete.

Two runs should not be treated as directly comparable if they use materially different:

- input pairs;
- preprocessing;
- matching settings;
- geometric models;
- evaluation points;
- hardware/timing boundaries.

---

## 15. V1 Result Format

A V1 result should be machine-readable.

The following schema is a **template**, not a claim that every field is already implemented.

```yaml
benchmark:
  name: ""
  version: "v1"
  track: ""
  dataset: ""
  dataset_version: ""
  pair_id: ""

input:
  source_sensor: ""
  reference_sensor: ""
  source_image: ""
  reference_image: ""
  source_width: null
  source_height: null
  reference_width: null
  reference_height: null
  source_gsd: null
  reference_gsd: null
  source_projection: ""
  reference_projection: ""
  overlap_known: null

method:
  preprocessing: ""
  detector: "SIFT"
  matcher: ""
  match_filter: ""
  geometric_verification: "RANSAC"
  transformation_model: ""
  subpixel_refinement: false

metrics:
  candidate_matches: null
  verified_inliers: null
  inlier_ratio: null
  grid_coverage: null
  convex_hull_coverage: null
  checkpoint_count: null
  checkpoint_rmse_px: null
  ground_error_m: null
  runtime_s: null
  failure: false
  failure_reason: ""

reproducibility:
  commit: ""
  configuration: ""
  random_seed: null
  software_environment: ""
  runtime_version: ""
  dependency_versions: ""
  hardware: ""
  cpu: ""
  gpu: ""

artifacts:
  candidate_matches: ""
  verified_inliers: ""
  registered_image: ""
  residuals: ""
  metrics: ""
```

### Result-format principles

The schema should:

- distinguish input information from method configuration;
- distinguish candidate matches from verified inliers;
- distinguish control points from independent check points;
- preserve units;
- record failures;
- record reproducibility information;
- avoid fake or placeholder benchmark results.

Empty values in the template represent fields that must be populated by an actual benchmark run.

---

## 16. Benchmark Artifacts

A successful V1 experiment should preserve enough evidence to inspect what happened.

Recommended artifact categories include:

### 16.1 Input metadata

Information describing the source/reference pair.

### 16.2 Candidate match visualization

A visualization showing the proposed correspondences before geometric verification.

### 16.3 Verified-inlier visualization

A visualization showing the correspondences retained after RANSAC.

### 16.4 Rejected-match evidence

Where practical, retain evidence of rejected candidate matches.

This makes geometric filtering inspectable rather than opaque.

### 16.5 Transformation

Record the estimated transformation in the machine-readable result.

### 16.6 Registered output

Save the resulting registered image or overlay.

### 16.7 Residual information

Where available, preserve:

- residual vectors;
- residual statistics;
- check-point errors.

### 16.8 Metrics

Save the numerical benchmark result separately from visual artifacts.

### 16.9 Configuration

Preserve the configuration used to generate the result.

---

## 17. Failure Handling

Failure is a valid benchmark outcome and must be recorded.

A failed run should not be silently excluded simply because it does not produce a successful registration.

### Example failure categories

The benchmark may classify failures such as:

- input validation failure;
- missing metadata;
- preprocessing failure;
- insufficient SIFT features;
- insufficient candidate matches;
- insufficient verified inliers;
- geometric model failure;
- transformation failure;
- registration failure;
- independent evaluation unavailable;
- runtime/system failure.

The exact failure taxonomy should follow the implementation once established.

### Why failures matter

Failure behavior is part of system evaluation.

A benchmark that reports only successful examples can hide:

- low-texture failure;
- illumination sensitivity;
- scale sensitivity;
- geometric instability;
- sensor-specific problems.

The project guidance recommends retaining difficult and failed cases as evidence of where the system succeeds and fails.

---

## 18. Scientific Limitations

V1 is intentionally limited.

### 18.1 Known-overlap assumption

V1 does not demonstrate whole-Moon retrieval.

The source/reference region is already known or constrained.

Therefore, V1 does not establish that ChandraMap can locate an unknown lunar region in a global reference database.

---

### 18.2 SIFT limitations

SIFT is an interpretable baseline, but it may struggle with:

- strong illumination differences;
- substantial modality differences;
- extreme scale differences;
- weak or repetitive terrain.

This is one reason V1 is a baseline rather than the final matching system.

---

### 18.3 Global transformation limitations

A single affine or homography model may not adequately describe all lunar image geometry.

Potential causes include:

- terrain relief;
- viewpoint changes;
- sensor geometry;
- projection differences;
- local distortions.

Residual inspection is therefore part of the benchmark interpretation.

---

### 18.4 Illumination limitations

Sun-angle changes can change shadows and apparent terrain structure.

Brightness or contrast normalization cannot necessarily reverse illumination-induced geometric differences.

The project guidance recommends treating illumination handling as an experimentally measurable component rather than assuming normalization makes the images equivalent.

---

### 18.5 Resolution limitations

Upsampling does not recover missing physical detail.

A coarse source cannot legitimately support arbitrarily fine correspondence claims simply because its pixels were enlarged.

---

### 18.6 IIRS limitations

IIRS is a different modality from OHRC and TMC-2.

A simple V1 optical registration pipeline should not silently claim equivalent performance on full hyperspectral IIRS data.

IIRS should be treated as a separate experiment unless the formal V1 specification explicitly includes it.

---

### 18.7 Visual overlay limitations

A visually good overlay can still have poor correspondence quality.

Therefore, V1 requires:

- verified inlier statistics;
- spatial distribution;
- independent check-point error;
- reproducibility information.

---

## 19. V1 vs Future Versions

V1 establishes the foundation for later benchmark versions.

| Capability                 |                   V1 | Future benchmark direction |
| -------------------------- | -------------------: | -------------------------: |
| Known-overlap registration |             **Core** |                  Continued |
| SIFT baseline              |             **Core** |        Baseline comparison |
| RANSAC verification        |             **Core** |                  Continued |
| Independent check points   |             **Core** |                  Continued |
| Registration RMSE          |             **Core** |                  Continued |
| Spatial coverage           |             **Core** |                  Continued |
| Runtime/failure recording  |             **Core** |                  Continued |
| Scale stress               | Limited / controlled |                   Expanded |
| Illumination stress        |              Limited |                   Expanded |
| Global retrieval           |             Deferred |                     Future |
| Reference database         |             Deferred |                     Future |
| FAISS                      |             Deferred |                     Future |
| Global descriptors         |             Deferred |                     Future |
| ALIKED + LightGlue         |             Deferred |          Future comparison |
| LoFTR                      |             Deferred |          Future comparison |
| RIFT/CFOG-style methods    |             Deferred |            Future research |
| Sub-pixel refinement       |  Optional / deferred |                   Expanded |
| IIRS experiments           |    Separate/deferred |                     Future |
| Cross-modal matching       |             Deferred |                     Future |
| Whole-Moon retrieval       |         Out of scope |                     Future |
| Large-scale mosaicking     |         Out of scope |                     Future |

The project build roadmap places retrieval, stronger local matchers, sub-pixel refinement, and additional sensors after the first known-pair milestone.

---

## 20. Benchmark Design Principles

### Principle 1 — Measure before expanding

The first objective is to produce a real numerical baseline.

```text
Working baseline
      ↓
Measured failure modes
      ↓
Targeted improvement
      ↓
Controlled comparison
```

---

### Principle 2 — Keep candidate and verified matches separate

```text
Candidate Matches
        ≠
Verified Inliers
```

Descriptor similarity is not geometric proof.

---

### Principle 3 — Evaluate independently

Transformation-fitting points must not be treated as independent evaluation points.

---

### Principle 4 — Report source-pixel error first

Registration error should be reported in source-image pixels before attempting conversion to physical units.

---

### Principle 5 — Measure spatial distribution

A cluster of correct matches around a single feature does not necessarily provide reliable registration across the complete overlap.

Grid or convex-hull coverage provides additional information.

---

### Principle 6 — Respect sensor physics

OHRC, TMC-2, IIRS, and LRO reference products should not be treated as identical data sources.

Sensor-specific preparation should happen before common structural matching where required.

---

### Principle 7 — Do not manufacture spatial detail

Resampling changes representation; it does not recover information that was never captured by the source sensor.

---

### Principle 8 — Do not let flexible warps hide bad correspondences

The benchmark should establish correspondence quality before introducing increasingly flexible geometric models.

---

### Principle 9 — Compare methods on identical benchmark pairs

When later methods are introduced, they should be evaluated on the same image pairs and under the same evaluation protocol.

This allows changes in performance to be attributed to the method rather than to different test data.

---

### Principle 10 — Keep failures

A benchmark should reveal where the system fails.

Failures provide information about:

- scale;
- illumination;
- modality;
- geometry;
- low-feature terrain;
- matching;
- preprocessing.

---

## 21. Current V1 Status

V1 is defined as the **foundational known-overlap correspondence and registration benchmark**.

The current specification establishes:

- a controlled source/reference pair;
- sensor-aware input handling;
- SIFT-based local matching;
- candidate-match generation;
- RANSAC geometric verification;
- verified-inlier extraction;
- transformation estimation;
- registration;
- independent check-point evaluation;
- spatial coverage measurement;
- runtime/failure recording;
- reproducible result recording.

It deliberately does **not** claim benchmark performance until actual V1 experiments have been executed.

No accuracy, runtime, inlier ratio, coverage value, success rate, or other numerical result should be added to this README unless it comes from an actual recorded benchmark run.

### V1 completion milestone

The foundational V1 milestone is:

```text
Known source/reference pair
        ↓
Candidate matches
        ↓
Verified inliers
        ↓
Final transformation
        ↓
Registered result
        ↓
Independent check-point error
        ↓
Reproducible benchmark record
```

Once this complete path is demonstrated with real lunar imagery and recorded measurements, V1 provides the quantitative foundation for the subsequent ChandraMap benchmark versions. The project feedback identifies this exact end-to-end known-pair result as the first milestone before expanding the system.
