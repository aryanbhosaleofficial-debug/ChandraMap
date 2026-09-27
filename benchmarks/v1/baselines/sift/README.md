# ChandraMap — SIFT Baseline

> **Benchmark baseline:** SIFT
> **Location:** `benchmarks/baselines/sift/README.md`
> **Project:** ChandraMap
> **Problem:** SIH 26166 — Multi-modal, Sun angle and scale invariant image correspondence using Chandrayaan-2 optical images
> **Status:** Baseline specification documented; exact repository implementation status, commands, configuration schema, and dependency versions are **not confirmed by the supplied project sources**.

---

## 1. Overview

The SIFT baseline is the classical local-feature correspondence baseline for ChandraMap.

Its purpose is to establish a **simple, explainable, reproducible reference pipeline** against which stronger lunar-aware and learned correspondence methods can be evaluated.

The intended baseline flow is:

```text
Source Image
     +
Reference Image
     ↓
Preprocessing
     ↓
SIFT Keypoint Detection
     ↓
SIFT Descriptor Extraction
     ↓
Descriptor Matching
     ↓
Candidate Matches
     ↓
Ratio / Cross-Check Filtering
     ↓
RANSAC Geometric Verification
     ↓
Verified Inliers
     ↓
Affine / Homography Estimation
     ↓
Registration
     ↓
Independent Check-Point Evaluation
     ↓
Benchmark Metrics
```

The supplied project feedback explicitly identifies:

```text
SIFT
  ↓
descriptor matching
  ↓
ratio / cross-check filtering
  ↓
RANSAC
  ↓
affine / homography
  ↓
residual error
```

as the baseline path.

SIFT is intended to be the **baseline**, not the final ChandraMap correspondence method.

---

## 2. Why This Baseline Exists

ChandraMap is intended to match and register lunar imagery that can differ in:

- scale;
- illumination;
- sensor characteristics;
- spatial resolution;
- viewpoint;
- geometric conditions.

A benchmark needs a reference method before more sophisticated methods can be evaluated.

SIFT provides that reference because it is:

- established;
- interpretable;
- relatively simple to reproduce;
- capable of detecting local keypoints;
- capable of producing local descriptors;
- naturally suited to scale and rotation changes;
- suitable for combination with geometric verification.

The project feedback recommends starting with SIFT before evaluating stronger learned methods such as ALIKED + LightGlue or LoFTR.

The baseline therefore answers:

> How far can a conventional local-feature correspondence pipeline go on the ChandraMap benchmark before introducing more specialized or learned correspondence methods?

---

## 3. Scope

This baseline covers the **local correspondence and geometric registration stage** of ChandraMap.

### In scope

- image-pair correspondence;
- SIFT keypoint detection;
- SIFT descriptor extraction;
- descriptor matching;
- candidate-match filtering;
- geometric verification;
- RANSAC-based outlier rejection;
- transformation estimation;
- registration;
- independent evaluation;
- benchmark metric generation;
- failure recording.

### Not inherently part of SIFT

The following are separate ChandraMap components and should not be attributed to SIFT itself:

- global lunar retrieval;
- FAISS indexing;
- global image embeddings;
- sensor-specific IIRS spectral modelling;
- learned feature extraction;
- LightGlue matching;
- LoFTR;
- RIFT;
- CFOG;
- DEM-based geometry correction;
- advanced local/piecewise warping.

Global retrieval, when used, should occur before expensive local matching. The project feedback explicitly distinguishes global retrieval features from local matching features such as SIFT.

---

# 4. Role in the ChandraMap Benchmark

The SIFT baseline occupies the local matching branch of the benchmark architecture.

```text
                    ChandraMap Benchmark
                            │
                            ▼
                 Source / Reference Images
                            │
                            ▼
                  Sensor-Aware Preparation
                            │
                            ▼
                    Scale Preparation
                            │
                            ▼
                  ┌─────────────────────┐
                  │   Local Matching   │
                  └─────────────────────┘
                            │
             ┌──────────────┼──────────────┐
             ▼              ▼              ▼
           SIFT       ALIKED+LightGlue    LoFTR
          Baseline       Candidate       Candidate
             │
             └──────────────┬──────────────┘
                            ▼
                  Geometric Verification
                            │
                            ▼
                         RANSAC
                            │
                            ▼
                       Verified Inliers
                            │
                            ▼
                    Transformation Model
                            │
                            ▼
                       Registration
                            │
                            ▼
                  Independent Evaluation
```

The important architectural distinction is:

> **SIFT is a local correspondence baseline, not the complete ChandraMap system.**

---

# 5. Baseline Objective

The SIFT baseline should establish a measurable end-to-end path:

```text
Input pair
   ↓
Candidate correspondences
   ↓
Geometrically verified inliers
   ↓
Final transformation
   ↓
Registered image
   ↓
Independent numerical error
```

The project feedback defines the first practical milestone in essentially these terms: a known source/reference pair should pass through SIFT, RANSAC, transformation, registration, and numerical evaluation on independent check points.

This is more important than producing a visually attractive overlay without quantitative evidence.

---

# 6. What SIFT Does

SIFT provides two main operations:

1. **Keypoint detection**
2. **Local descriptor extraction**

The resulting descriptors are compared between the source and reference images to produce candidate correspondences.

Conceptually:

```text
Source Image
    │
    ├── SIFT keypoints
    └── SIFT descriptors
             │
             │ descriptor matching
             ▼
Reference Image
    │
    ├── SIFT keypoints
    └── SIFT descriptors
```

The output of this stage is a set of **candidate matches**.

A candidate match is not automatically a geometrically correct correspondence.

The project feedback specifically recommends allowing RANSAC/geometric verification to determine which candidate correspondences become verified inliers.

---

# 7. ChandraMap SIFT Pipeline

## 7.1 High-Level Pipeline

```text
Source / Reference Images
          ↓
Input Validation
          ↓
Preprocessing
          ↓
Comparable Effective Scale
          ↓
SIFT Detection
          ↓
SIFT Descriptors
          ↓
Descriptor Matching
          ↓
Candidate Matches
          ↓
Ratio / Cross-Check Filtering
          ↓
RANSAC
          ↓
Verified Inliers
          ↓
Initial Geometric Model
          ↓
Registration
          ↓
Independent Check Points
          ↓
Metrics + Artifacts
```

The exact implementation of each stage is **not confirmed by the supplied repository data** unless documented elsewhere in the project.

---

# 8. Input

The baseline operates on a source/reference image pair.

Conceptually:

```text
Source image
    +
Reference image
```

The benchmark input should preserve relevant metadata when available, including:

- product identity;
- sensor;
- image dimensions;
- pixel scale / GSD;
- footprint;
- map projection;
- illumination metadata;
- viewing geometry;
- ground-truth information.

The project feedback specifically recommends preserving footprint, pixel scale, map projection, and lighting/viewing metadata where available.

## 8.1 Supported Sensor Context

The broader ChandraMap problem identifies:

- Chandrayaan-2 OHRC;
- Chandrayaan-2 TMC-2;
- Chandrayaan-2 IIRS;
- LRO NAC reference/training imagery;
- additional lunar datasets and synthetic augmentations.

However, the SIFT baseline README does **not** claim that every sensor combination is currently implemented.

The exact supported SIFT input matrix is:

> **Not confirmed by the supplied repository implementation data.**

---

# 9. Sensor Handling

SIFT itself should not be confused with sensor-aware preprocessing.

The project architecture explicitly recommends different preprocessing routes for OHRC, TMC-2, and IIRS.

Therefore:

```text
Sensor-specific preparation
          ↓
Registration-friendly representation
          ↓
SIFT
```

is preferable to assuming that all sensors are directly interchangeable.

## 9.1 OHRC / TMC-2

The project feedback describes OHRC and TMC-2 as panchromatic/visible terrain imagery suitable for intensity and terrain-structure processing after appropriate calibration or standard-product handling.

## 9.2 IIRS

IIRS should not automatically be treated as an ordinary single-band camera image.

The project feedback recommends first determining an appropriate 2D representation, such as:

- selected band;
- PCA/composite;
- structural representation.

These are possible project experiments, not a claim that all are implemented in the SIFT baseline.

---

# 10. Scale Handling

SIFT is scale-aware at the feature level, but this does **not** eliminate the need for physically meaningful scale handling in ChandraMap.

The benchmark should compare information at appropriate effective ground scales before expecting fine correspondence.

Conceptually:

```text
High-resolution reference
          ↓
Reference pyramid / controlled reduction
          ↓
Comparable effective scale
          ↓
SIFT matching
```

The project feedback explicitly warns that upsampling does not recover missing spatial information and recommends using a reference pyramid or downsampling the higher-resolution side for large GSD differences.

Therefore:

> Resizing an image to equal pixel dimensions must not be described as recovering equivalent physical detail.

The exact scale-normalization implementation for this baseline is:

> **Not confirmed by the supplied repository implementation data.**

---

# 11. Illumination Handling

SIFT should be evaluated honestly under illumination variation.

Lunar Sun-angle differences can change shadow geometry, not merely image brightness.

Therefore:

```text
Brightness normalization
        ≠
Removal of Sun-angle geometry
```

The project feedback recommends comparing intensity-based representations with structure-focused representations such as gradients or edges for difficult illumination cases.

The baseline should therefore record whether preprocessing uses:

- raw grayscale;
- normalized intensity;
- gradient/edge representation;
- another project-defined representation.

The exact currently implemented preprocessing configuration is:

> **Not confirmed.**

---

# 12. SIFT Keypoint Detection

The SIFT stage detects local image structures suitable for descriptor extraction.

The benchmark should record the actual SIFT configuration used by the implementation.

Potential parameters include:

- number of features;
- octave configuration;
- contrast threshold;
- edge threshold;
- Gaussian sigma.

However:

> **The exact ChandraMap SIFT parameter values are not specified in the supplied project sources and must not be invented here.**

If the implementation uses library defaults, the benchmark should explicitly record that fact in the actual experiment configuration.

---

# 13. Descriptor Extraction

Each detected SIFT keypoint produces a local descriptor.

Conceptually:

```text
Keypoints
    ↓
Local image patches / scale-space representation
    ↓
SIFT descriptors
```

The descriptors form the representation used by the matching stage.

The exact descriptor data type and storage format are:

> **Not specified by the supplied project data.**

---

# 14. Descriptor Matching

The baseline compares source and reference SIFT descriptors.

The project feedback identifies:

```text
SIFT
  ↓
descriptor matching
  ↓
ratio / cross-check filter
```

as the baseline matching path.

The matching stage produces:

```text
Candidate Matches
```

rather than verified correspondences.

---

# 15. Ratio / Cross-Check Filtering

Candidate matches may be filtered using:

- descriptor-ratio testing;
- cross-check matching;
- or the specific filtering strategy implemented by the repository.

The project feedback identifies ratio/cross-check filtering as part of the intended baseline.

The exact filtering parameters are:

> **Not specified by the supplied project sources.**

They must be obtained from the implementation/configuration before claiming reproducibility.

---

# 16. Candidate Matches vs Verified Inliers

This distinction is fundamental.

```text
SIFT descriptor matching
          ↓
Candidate matches
          ↓
Geometric verification
          ↓
Verified inliers
```

A descriptor match is not proof that the two points represent the same lunar terrain feature.

The project feedback explicitly recommends using RANSAC/geometric verification to determine which candidates become verified inliers.

---

# 17. RANSAC Geometric Verification

RANSAC provides robust geometric verification.

The intended role is:

```text
Candidate Matches
        ↓
Geometric Model
        ↓
RANSAC
        ↓
Outlier Rejection
        ↓
Verified Inliers
```

The baseline should record:

- candidate-match count;
- inlier count;
- inlier ratio;
- selected geometric model;
- residual error;
- transformation status.

The project feedback recommends RANSAC as the initial geometric verification stage.

---

# 18. Transformation Model

The supplied project feedback identifies:

- affine transformation;
- homography

as reasonable initial geometric models for local, already map-projected pairs.

The exact model selected by the current SIFT implementation is:

> **Not confirmed by the supplied repository implementation data.**

The benchmark must record the actual model used.

### Important geometry limitation

The Moon is not a flat planar surface.

A single global transformation should therefore not be assumed to be universally valid.

Residual vectors should be inspected across the image. Systematic spatial residuals can indicate that a simple global model is insufficient.

---

# 19. Registration

After geometric verification and transformation estimation, the transformation is applied to produce a registered output.

Conceptually:

```text
Verified Inliers
       ↓
Final Transformation
       ↓
Source / Reference Registration
       ↓
Registered Output
```

The exact image-warping library, interpolation configuration, coordinate convention, and output format are:

> **Not confirmed by the supplied project data.**

---

# 20. Sub-Pixel Refinement

Sub-pixel refinement is **not inherently part of the basic SIFT detector/matcher**.

The broader ChandraMap pipeline places refinement after geometric verification:

```text
Candidate Matches
       ↓
RANSAC
       ↓
Verified Inliers
       ↓
Sub-pixel Tie-Point Refinement
       ↓
Final Transform Refit
       ↓
Registration
```

The project feedback explicitly recommends refining verified control points and then refitting the final transformation.

Therefore, unless the repository's SIFT baseline explicitly implements this stage:

> **SIFT baseline refinement status: Not confirmed.**

A SIFT result MUST NOT be described as sub-pixel accurate merely because SIFT is used.

---

# 21. Independent Evaluation

Transformation fitting and evaluation should use separate point sets whenever possible.

Incorrect evaluation:

```text
Inlier points
     ↓
Fit transformation
     ↓
Evaluate RMSE on same points
```

Preferred evaluation:

```text
Control / fitting points
     ↓
Fit transformation

Independent check points
     ↓
Evaluate final transformation
```

The project feedback explicitly warns that evaluating on the same points used for transformation fitting can make registration quality appear artificially better.

---

# 22. Benchmark Metrics

The SIFT baseline should report the metrics defined by the ChandraMap V1 benchmark protocol.

## 22.1 Candidate Match Count

Number of candidate correspondences produced before geometric verification.

```text
N_candidate
```

---

## 22.2 Inlier Count

Number of correspondences retained after geometric verification.

```text
N_inlier
```

---

## 22.3 Inlier Ratio

$$
\text{Inlier Ratio}
=
\frac{N_{inlier}}
     {N_{candidate}}
$$

This indicates how many candidate correspondences are geometrically consistent.

It does not independently prove that the final registration is accurate.

---

## 22.4 Spatial Coverage

Spatial coverage measures whether verified correspondences are distributed across the overlap.

Possible benchmark definitions include:

- grid coverage;
- convex-hull coverage.

The project feedback specifically recommends grid or convex-hull coverage.

The exact V1 implementation and grid definition are:

> **Not confirmed by the supplied repository implementation data.**

---

## 22.5 Check-Point RMSE

The primary registration accuracy should be measured using independent check points.

For \(N\) check points:

$$
RMSE =
\sqrt{
\frac{1}{N}
\sum_{i=1}^{N}
\left\|
g_i-\hat{g}_i
\right\|_2^2
}
$$

where:

- \(g_i\) is the reference/ground-truth point;
- \(\hat{g}\_i\) is the point predicted by the final transformation.

The project feedback specifies **source-image pixels** as the primary unit for sub-pixel registration evaluation.

---

## 22.6 Ground Error

Ground error in metres should only be reported when:

- GSD is known;
- projection/reference geometry supports the conversion;
- the ground-truth coordinate system makes the conversion meaningful.

The benchmark must not infer physical accuracy from pixel error without appropriate ground-scale information.

---

## 22.7 Runtime

Runtime is a system-level benchmark metric.

If runtime is compared across methods, the evaluation should preserve equivalent:

- hardware;
- software environment;
- input;
- configuration.

The exact runtime measurement procedure is:

> **Not specified by the supplied repository sources.**

---

## 22.8 Failure Rate

When multiple benchmark cases are executed:

$$
FailureRate =
\frac{N_{failed}}
     {N_{total}}
$$

The failure definition must be established before the result is calculated.

Failed cases must remain part of the evaluation set.

---

# 23. Expected Outputs

The SIFT baseline should produce interpretable benchmark artifacts.

The project feedback identifies the following useful outputs:

- match plot;
- rejected outliers;
- registered overlay;
- inlier statistics;
- check-point error.

A complete execution may therefore conceptually produce:

```text
SIFT Baseline Run
│
├── Candidate matches
├── Verified inliers
├── Rejected matches
├── Transformation
├── Residuals
├── Registered image
├── Spatial coverage
├── Check-point evaluation
└── Benchmark metrics
```

The exact filenames and output directories are:

> **Not confirmed by the supplied repository data.**

Do not invent output paths in scripts, documentation, or benchmark reports.

---

# 24. Visual Outputs

Visualizations should support numerical evaluation.

Recommended diagnostic views include:

### Candidate Matches

```text
Source Image        Reference Image
      \                  /
       \                /
        \--- matches --/
```

### RANSAC Inliers

```text
Candidate Matches
       ↓
Rejected Outliers  ×
Verified Inliers   ✓
```

### Registered Overlay

Used to inspect whether the estimated transformation aligns the imagery.

### Residual Vectors

Used to determine whether errors are spatially systematic.

### Spatial Coverage

Used to determine whether verified points are distributed across the overlap.

A visually convincing overlay is not sufficient evidence of correct registration. The project feedback explicitly warns that visual alignment can look good even when correspondence quality is poor.

---

# 25. Configuration

The SIFT baseline configuration should be treated as a versioned benchmark configuration.

The exact repository configuration file/path is:

> **Not confirmed by the supplied project data.**

At minimum, the configuration should identify:

```text
Input pair
Sensor / product
Preprocessing
Scale handling
SIFT parameters
Descriptor matcher
Match filtering
RANSAC configuration
Transformation model
Registration configuration
Evaluation configuration
Random seed, if applicable
```

Do not change configuration values between baseline comparisons without recording the change.

---

# 26. Dependencies

The exact dependency list and versions for the repository SIFT baseline are:

> **Not confirmed by the supplied project data.**

The project feedback identifies OpenCV SIFT and homography functionality as a practical starting point for this baseline.

Therefore, OpenCV is a project-supported implementation direction, but this README does not claim a specific installed OpenCV version.

The actual dependency specification should remain authoritative in the repository's dependency/configuration files once confirmed.

---

# 27. Running the Baseline

The exact ChandraMap SIFT command is:

> **Not specified in the supplied project sources.**

No command is provided here because inventing a command would make this README appear reproducible when the actual repository interface has not been verified.

Once the implementation command is established, this section should document the exact invocation, for example in the form:

```text
<actual repository command>
```

with:

- input arguments;
- configuration arguments;
- output arguments;
- dataset requirements;
- evaluation options.

The placeholder should be replaced with the actual repository command before marking the baseline as fully executable documentation.

---

# 28. Reproducibility

A SIFT benchmark result should be reproducible from:

```text
Dataset
   +
Pair definition
   +
Configuration
   +
Software environment
   +
Command
   +
Evaluation protocol
```

The benchmark record should preserve:

- source image identifier;
- reference image identifier;
- image dimensions;
- sensor/product metadata;
- pixel scale/GSD where available;
- preprocessing configuration;
- SIFT configuration;
- matching configuration;
- RANSAC configuration;
- transformation model;
- check-point definition;
- random seed where relevant;
- software version/environment;
- output artifacts.

---

# 29. Baseline Comparison Protocol

The SIFT baseline should be evaluated on the **same image pairs** as competing correspondence methods.

Recommended comparison:

```text
Same benchmark pairs
        │
        ├── SIFT baseline
        │
        ├── Stronger matcher
        │
        └── Full lunar-aware pipeline
                 ↓
        Same evaluation protocol
                 ↓
     Compare RMSE / inliers / coverage
```

The project feedback explicitly recommends running the same test pairs through the SIFT baseline, a stronger matcher, and the full sensor-aware + multi-scale pipeline.

This prevents improvements from being attributed merely to different test cases.

---

# 30. Stress-Test Role

The SIFT baseline should participate in the V1 stress-test matrix.

Relevant stress categories include:

| Stress test         | Purpose                                               |
| ------------------- | ----------------------------------------------------- |
| Easy pair           | Establish end-to-end baseline behavior                |
| Sun-angle stress    | Test sensitivity to changed shadow geometry           |
| Scale stress        | Test behavior under large effective scale differences |
| Modality stress     | Test cross-sensor representation                      |
| Geometry stress     | Test transformation robustness                        |
| Low-feature terrain | Test correspondence under weak/repetitive structure   |

These categories are identified in the supplied project feedback.

The baseline should be evaluated under the same stress conditions as later methods.

---

# 31. Known Limitations

SIFT is deliberately retained as a simple baseline, so several limitations are expected.

## 31.1 Illumination Sensitivity

SIFT can struggle under strong illumination changes.

The project feedback specifically identifies strong modality and illumination changes as important limitations of the SIFT baseline.

Lunar Sun-angle changes can alter shadow geometry, which cannot be completely removed through simple brightness normalization.

---

## 31.2 Cross-Modality Difficulty

SIFT descriptors are not designed specifically for every cross-sensor lunar correspondence problem.

Performance should therefore be measured rather than assuming modality invariance.

---

## 31.3 Large Scale Differences

SIFT has scale-aware detection, but this does not mean it can recover spatial information absent from a coarse sensor.

Physical scale differences should be handled through the benchmark's scale-aware preparation.

---

## 31.4 Low-Feature Terrain

Smooth or repetitive terrain can produce:

- fewer distinctive keypoints;
- ambiguous descriptors;
- clustered matches;
- incorrect correspondences.

These cases are therefore important stress tests.

---

## 31.5 Geometric Model Limitations

A single affine transformation or homography may be insufficient for:

- relief-rich terrain;
- stronger viewpoint differences;
- uncorrected sensor geometry;
- larger geographic extents.

The project feedback recommends inspecting residual vectors and considering local/piecewise models or sensor geometry/DEM information where justified.

---

## 31.6 No Automatic Lunar Invariance

SIFT being scale- and rotation-aware does not establish that it is invariant to every lunar imaging condition.

In particular, the benchmark should not claim:

```text
SIFT = lunar invariant
```

without experimental evidence.

---

# 32. What This Baseline Must Not Claim

The SIFT baseline must not claim:

- universal lunar-image invariance;
- universal Sun-angle invariance;
- universal cross-sensor robustness;
- recovery of missing spatial detail through upsampling;
- sub-pixel accuracy merely because SIFT is used;
- successful registration based only on visual overlay;
- high confidence based only on descriptor similarity;
- robustness based on a single successful image pair.

The project materials explicitly caution against overclaiming from resizing, pretrained models, match counts, or visual results.

---

# 33. Failure Handling

Failures are benchmark results and must not be silently discarded.

Possible failure stages include:

```text
Input
  ↓
Preprocessing
  ↓
SIFT Detection
  ↓
Descriptor Extraction
  ↓
Matching
  ↓
Filtering
  ↓
RANSAC
  ↓
Transformation
  ↓
Registration
  ↓
Evaluation
```

For a failed run, record:

- pair identifier;
- configuration;
- failed stage;
- failure reason where known;
- intermediate metrics available before failure;
- output artifacts, if any.

---

# 34. Spatial Distribution Requirement

A large number of matches does not necessarily imply a good registration.

For example:

```text
      x x x x
      x x x x

----------------------

     empty region

----------------------

     empty region
```

is weaker geometric evidence than correspondences distributed throughout the overlap.

The benchmark should therefore report spatial coverage in addition to inlier count and inlier ratio.

The project feedback explicitly recommends grid or convex-hull coverage to detect clustered correspondences.

---

# 35. Residual Analysis

After estimating the geometric model, residuals should be inspected.

Conceptually:

```text
Verified Inliers
      ↓
Fit Transformation
      ↓
Predict Positions
      ↓
Ground Truth
      ↓
Residual Vectors
```

A spatially systematic residual pattern can indicate that the selected global transformation is insufficient.

The project feedback specifically recommends inspecting residual vectors across the image before introducing local/piecewise refinement.

---

# 36. Sub-Pixel Reporting

When sub-pixel registration is evaluated, report the error in **source-image pixels first**.

For example:

```text
Check-point RMSE: <measured value> source pixels
```

Conversion to metres should only be performed when the product GSD and projection/reference information make that conversion meaningful.

The project materials explicitly warn that the same pixel error can correspond to very different ground distances for sensors with different spatial scales.

---

# 37. Results Reporting

This README intentionally contains **no experimental SIFT results**.

When results become available, report measured values rather than qualitative ratings.

Recommended table:

| Metric                       |                SIFT |
| ---------------------------- | ------------------: |
| Candidate matches            |        `<measured>` |
| Verified inliers             |        `<measured>` |
| Inlier ratio                 |        `<measured>` |
| Spatial coverage             |        `<measured>` |
| Check-point RMSE (source px) |        `<measured>` |
| Ground error (m)             | `<measured or N/A>` |
| Runtime                      |        `<measured>` |
| Status                       | `<success/failure>` |

Do not replace measurements with:

```text
92% confidence
★★★★★
Excellent
Strong
Robust
```

unless those terms are explicitly defined benchmark outputs supported by measured evidence.

The project feedback specifically recommends removing decorative percentages and star ratings until they are actually measured.

---

# 38. Result Interpretation

A SIFT result should be interpreted in terms of measurable behavior.

For example:

### Candidate Matches

Answers:

> How many possible correspondences did SIFT produce?

### Inlier Ratio

Answers:

> What fraction survived geometric verification?

### Spatial Coverage

Answers:

> Are verified points distributed across the overlap?

### Check-Point RMSE

Answers:

> How accurately does the final transformation perform on points not used to fit it?

### Runtime

Answers:

> What computational cost was observed under the recorded environment?

These metrics should be considered together.

---

# 39. Implementation Status

The supplied project sources establish SIFT as the recommended initial baseline, but they do not provide verified repository-level evidence for every implementation detail.

| Component                                         | Status from supplied sources         |
| ------------------------------------------------- | ------------------------------------ |
| SIFT as benchmark baseline                        | Specified                            |
| SIFT keypoint detection                           | Specified as baseline method         |
| SIFT descriptor matching                          | Specified                            |
| Ratio/cross-check filtering                       | Specified as intended baseline path  |
| RANSAC geometric verification                     | Specified                            |
| Affine/homography evaluation                      | Specified as initial model direction |
| Registration                                      | Required by baseline milestone       |
| Independent check-point evaluation                | Required                             |
| Spatial coverage                                  | Required benchmark metric            |
| Sub-pixel refinement                              | Separate/optional downstream stage   |
| Exact SIFT parameters                             | Not confirmed                        |
| Exact matcher parameters                          | Not confirmed                        |
| Exact RANSAC parameters                           | Not confirmed                        |
| Exact transformation model used by implementation | Not confirmed                        |
| Exact output paths                                | Not confirmed                        |
| Exact CLI command                                 | Not confirmed                        |
| Exact dependency versions                         | Not confirmed                        |
| Experimental results                              | Not supplied                         |

This distinction is intentional: benchmark documentation must not turn a proposed architecture into a false implementation claim.

---

# 40. Recommended Baseline Development Order

The project feedback recommends building one measurable end-to-end result before expanding the system.

For the SIFT baseline:

```text
1. Select one known overlapping pair
          ↓
2. Validate image metadata
          ↓
3. Run preprocessing
          ↓
4. Detect SIFT keypoints
          ↓
5. Extract descriptors
          ↓
6. Match descriptors
          ↓
7. Filter candidate matches
          ↓
8. Run RANSAC
          ↓
9. Estimate transformation
          ↓
10. Generate registered output
          ↓
11. Evaluate independent check points
          ↓
12. Record inliers / coverage / RMSE / runtime
          ↓
13. Preserve failure cases
          ↓
14. Expand to benchmark pairs
          ↓
15. Run stress tests
```

---

# 41. SIFT Baseline vs Future Methods

SIFT should remain the reference point for later experiments.

Potential later methods identified by the project include:

| Method             | Role                                           |
| ------------------ | ---------------------------------------------- |
| SIFT               | Classical local baseline                       |
| ALIKED + LightGlue | Learned sparse matching candidate              |
| LoFTR              | Detector-free matching candidate               |
| RIFT               | Multimodal remote-sensing direction            |
| CFOG               | Structural multimodal remote-sensing direction |

The project feedback recommends testing a stronger learned path against SIFT on the same image pairs rather than introducing many algorithms simultaneously.

---

# 42. Fair Comparison Rules

When comparing SIFT with another matcher:

### Keep fixed

- image pairs;
- ground truth;
- evaluation points;
- stress conditions;
- evaluation metrics;
- hardware when runtime is compared;
- relevant preprocessing unless preprocessing itself is the experimental variable.

### Change

Only the component being evaluated.

For example:

```text
SIFT
  ↓
same verification
  ↓
same registration
  ↓
same evaluation
```

versus:

```text
Learned matcher
  ↓
same verification
  ↓
same registration
  ↓
same evaluation
```

This makes it possible to determine whether the correspondence method itself caused the measured difference.

---

# 43. Scientific Reproducibility Checklist

Before publishing a SIFT result:

- [ ] Image pair is identified.
- [ ] Source and reference products are identified.
- [ ] Image dimensions are recorded.
- [ ] Pixel scale/GSD is recorded where available.
- [ ] Relevant metadata is preserved.
- [ ] Preprocessing is documented.
- [ ] SIFT configuration is documented.
- [ ] Matching configuration is documented.
- [ ] Filtering configuration is documented.
- [ ] RANSAC configuration is documented.
- [ ] Transformation model is documented.
- [ ] Independent check points are documented.
- [ ] Candidate-match count is recorded.
- [ ] Inlier count is recorded.
- [ ] Inlier ratio is recorded.
- [ ] Spatial coverage is recorded.
- [ ] Check-point RMSE is recorded.
- [ ] Ground error is reported only when meaningful.
- [ ] Runtime is recorded when relevant.
- [ ] Failure cases are preserved.
- [ ] Software environment is recorded.
- [ ] Exact command is recorded once confirmed.
- [ ] No unsupported performance claim is made.

---

# 44. Benchmark Integrity Rules

The SIFT baseline must follow these rules:

1. **Do not invent results.**
2. **Do not remove failures.**
3. **Do not evaluate only on fitted points.**
4. **Do not treat candidate matches as verified correspondences.**
5. **Do not use visual overlays as the only evaluation.**
6. **Do not interpret more matches as automatically better.**
7. **Do not claim physical accuracy without appropriate scale/projection information.**
8. **Do not claim lunar invariance without benchmark evidence.**
9. **Do not change configuration silently between comparisons.**
10. **Do not tune against the test set and present the result as an untouched baseline.**

---

# 45. Relationship to `benchmarks/v1`

The SIFT baseline is a component of the broader V1 benchmark architecture.

Conceptually:

```text
benchmarks/
│
├── baselines/
│   └── sift/
│       └── README.md
│
└── v1/
    ├── benchmark specification
    ├── evaluation protocol
    ├── stress-test framework
    └── V1 benchmark artifacts
```

The exact complete repository tree should be taken from the current repository rather than inferred from this README.

The SIFT baseline should follow the V1 benchmark's:

- dataset protocol;
- ground-truth protocol;
- evaluation metrics;
- stress-test definitions;
- result-reporting conventions.

---

# 46. Relationship to Stress Testing

SIFT should serve as the reference method for V1 stress experiments.

The comparison should follow:

```text
                    Baseline Pair
                         │
          ┌──────────────┴──────────────┐
          │                             │
       SIFT                       Stress Condition
          │                             │
          │                       Same SIFT pipeline
          │                             │
          └──────────────┬──────────────┘
                         ▼
                  Metric Comparison
                         │
              ┌──────────┼──────────┐
              ▼          ▼          ▼
           Inliers    Coverage     RMSE
```

Stress categories include:

- Sun-angle;
- scale;
- modality;
- geometry;
- low-feature terrain.

These categories are part of the project's documented evaluation direction.

---

# 47. Limitations of the Baseline as a Scientific Reference

The SIFT baseline is useful precisely because it is simple.

It should not be interpreted as:

- the final ChandraMap architecture;
- the strongest available matcher;
- a complete sensor-aware solution;
- a universal lunar correspondence algorithm.

Its scientific purpose is to establish a **controlled reference point**.

Later methods should be able to answer:

> What measurable improvement do they provide over SIFT under the same conditions?

---

# 48. Definition of a Valid SIFT Baseline Result

A complete baseline result should demonstrate:

```text
Real source/reference pair
        ↓
SIFT keypoints
        ↓
Candidate correspondences
        ↓
Geometric verification
        ↓
Verified inliers
        ↓
Transformation
        ↓
Registered output
        ↓
Independent check-point error
```

The project feedback defines this end-to-end measurable result as the first important milestone before expanding the architecture.

---

# 49. Source-Derived Technical Principles

The SIFT baseline follows these project-specific principles:

### Correspondence is the primary output

The final mosaic or visualization is downstream. The core deliverable is reliable correspondence, transformation, registration, and measurable quality.

### Candidate matches are not verified matches

Geometric verification must determine which correspondences are reliable.

### RANSAC precedes refinement

The intended order is:

```text
Candidate Matches
      ↓
RANSAC
      ↓
Inliers
      ↓
Sub-pixel refinement
      ↓
Final transformation
```

### Spatial distribution matters

A large number of clustered matches is not equivalent to well-distributed control points.

### Pixel error comes before ground error

Source-image pixel error should be reported first; physical conversion requires meaningful GSD and projection information.

### Scale must respect physical information

Upsampling cannot recreate spatial detail that the source sensor did not resolve.

### Illumination is a real stress condition

Lunar Sun-angle differences alter shadows and therefore terrain appearance and geometry.

---

# 50. Final Baseline Definition

The ChandraMap SIFT baseline is defined as:

```text
SIFT
  ↓
Descriptor Matching
  ↓
Candidate Correspondences
  ↓
Ratio / Cross-Check Filtering
  ↓
RANSAC Geometric Verification
  ↓
Verified Inliers
  ↓
Affine / Homography Evaluation
  ↓
Registration
  ↓
Independent Check-Point Evaluation
  ↓
RMSE + Inlier Statistics + Spatial Coverage + Runtime
```

Its role is to provide a **transparent, measurable reference implementation** for ChandraMap V1.

The baseline should be judged by evidence rather than by assumptions:

```text
Measure
  ↓
Compare
  ↓
Stress
  ↓
Analyze failures
  ↓
Improve
```

The project guidance is explicit about this development philosophy: build a small measurable baseline, measure honestly, and preserve failures.

---

## Source Basis

This README is grounded in the supplied ChandraMap/SIH 26166 project materials, particularly the documented SIFT baseline path, evaluation protocol, build order, sensor considerations, and geometric-verification guidance.

The SIH problem material identifies Chandrayaan-2 OHRC, TMC-2, and IIRS as primary target/source imagery and LRO NAC as reference/training imagery.

The supplied project feedback recommends SIFT as the initial explainable baseline before comparing stronger learned correspondence methods.

Exact repository commands, file paths, configuration values, dependency versions, implementation status, output filenames, and benchmark results are intentionally not asserted where they were not established by the supplied project data.
