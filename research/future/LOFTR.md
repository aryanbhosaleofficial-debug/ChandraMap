# LoFTR Research

## Overview

LoFTR (**Local Feature TRansformer**) is a learned image correspondence method that approaches local matching differently from the conventional keypoint-detector → descriptor → sparse matcher pipeline.

The method is relevant to ChandraMap because lunar image registration can contain conditions where sparse local features may be difficult to detect or repeat reliably, including:

- weakly textured terrain
- repetitive terrain
- substantial scale differences
- illumination changes
- appearance changes between sensors
- differences in spatial resolution
- cross-sensor or cross-modal appearance differences

LoFTR should therefore be investigated as a **future learned correspondence research direction**, not as an already implemented or validated component of ChandraMap.

The central research question is:

> **Can LoFTR provide reliable image correspondences for lunar image registration in cases where sparse local-feature pipelines struggle because of weak texture, scale differences, illumination differences, or cross-sensor appearance changes?**

The answer must be established through controlled experiments using ChandraMap's benchmark and ground-truth methodology.

---

## Research Status

**Overall status: Planned / Future Research**

LoFTR is not established as:

- implemented in ChandraMap
- trained on lunar imagery
- validated on Chandrayaan-2 imagery
- validated for OHRC
- validated for TMC-2
- validated for IIRS-derived representations
- selected for production
- superior to the existing SIFT baseline

Any future claim of effectiveness must be supported by measured experiments.

| Research Area                  | Status          | Required Evidence                 |
| ------------------------------ | --------------- | --------------------------------- |
| LoFTR literature understanding | Exploratory     | Reviewed research literature      |
| ChandraMap integration         | Planned         | Reproducible implementation       |
| Lunar inference                | Planned         | Controlled lunar benchmark        |
| OHRC registration              | Planned         | Independent evaluation            |
| TMC-2 registration             | Planned         | Independent evaluation            |
| IIRS-derived representation    | Exploratory     | Dedicated cross-modal experiment  |
| Scale robustness               | Planned         | Controlled scale benchmark        |
| Illumination robustness        | Planned         | Controlled illumination benchmark |
| Geometric verification         | Planned         | Residual and checkpoint analysis  |
| Production adoption            | Not established | Reproducible benchmark evidence   |

---

## Why LoFTR Requires Separate Research

The established ChandraMap V1 pipeline is based on a conventional correspondence process:

```text
Input Images
     ↓
Image Representation / Preprocessing
     ↓
Local Feature Extraction
     ↓
Feature Descriptors
     ↓
Feature Matching
     ↓
Candidate Correspondences
     ↓
Geometric Verification
     ↓
Transformation Estimation
     ↓
Residual / Check-Point Evaluation
     ↓
Optional Sub-Pixel Refinement
     ↓
Final Registration
```

This pipeline is intentionally useful as a classical baseline because each stage can be independently inspected and measured.

LoFTR changes the correspondence stage.

Conceptually, the future LoFTR pipeline is:

```text
Input Images
     ↓
Image Representation / Preprocessing
     ↓
LoFTR Correspondence Inference
     ↓
Coarse Correspondences
     ↓
Fine Correspondence Refinement
     ↓
Confidence / Match Filtering
     ↓
Geometric Verification
     ↓
Transformation Estimation
     ↓
Residual / Check-Point Evaluation
     ↓
Final Registration
```

The important distinction is that LoFTR is not simply another descriptor matcher.

Its research motivation is to establish correspondences without relying on the same sequential sparse keypoint-detection and descriptor-matching process used by classical pipelines.

---

## LoFTR at a Research Level

LoFTR is a learned correspondence architecture based on Transformer attention.

At a conceptual level, it uses information from both images to establish correspondence and follows a coarse-to-fine matching strategy.

The important research concepts for ChandraMap are:

- detector-free correspondence
- image-pair-conditioned feature representations
- self-attention
- cross-attention
- coarse correspondence
- fine correspondence refinement
- dense or semi-dense correspondence behavior
- learned matching confidence

The original LoFTR research specifically investigates the problem of matching regions where conventional feature detectors may have difficulty producing repeatable interest points.

For ChandraMap, this motivates a measurable question:

> Does detector-free correspondence provide useful advantages for lunar terrain where sparse keypoint extraction produces insufficient or spatially concentrated correspondences?

This remains a hypothesis.

---

## SIFT vs ALIKED vs LightGlue vs LoFTR

These methods should not be treated as interchangeable components.

### SIFT

SIFT is the initial classical sparse local-feature baseline in ChandraMap.

Conceptually:

```text
Image
 ↓
Keypoint Detection
 ↓
SIFT Descriptor
 ↓
Descriptor Matching
 ↓
Geometric Verification
```

SIFT provides an interpretable and reproducible baseline for establishing the behavior of the registration pipeline.

Relevant experiment:

[EXP-001 — SIFT Baseline](../../experiments/v1/baseline/EXP-001-sift-baseline/README.md)

---

### ALIKED

ALIKED is a learned local-feature research direction.

Conceptually:

```text
Image
 ↓
Learned Keypoint Detection
 ↓
Learned Local Description
 ↓
Sparse Features
```

It therefore remains within the broader sparse-feature paradigm, although the feature extraction process is learned.

Relevant future research:

[ALIKED Research](./ALIKED.md)

---

### LightGlue

LightGlue is a learned sparse feature matching research direction.

Its conceptual role is downstream of local feature extraction:

```text
Image A → Local Features ┐
                         ├→ LightGlue → Correspondences
Image B → Local Features ┘
```

Therefore, an ALIKED + LightGlue pipeline can be conceptually understood as:

```text
ALIKED
  ↓
Learned Sparse Features
  ↓
LightGlue
  ↓
Sparse Feature Correspondences
```

Relevant future research:

[LightGlue Research](./LIGHTGLUE.md)

---

### LoFTR

LoFTR addresses correspondence differently:

```text
Image A ─┐
         ├→ Joint Correspondence Inference
Image B ─┘
                ↓
        Coarse Correspondences
                ↓
        Fine Correspondences
```

It should therefore not be described as:

> "another descriptor matcher."

The research comparison is more appropriately:

| Method             | Feature Detection            | Sparse Descriptor Pipeline        | Learned Matching | Correspondence Style              |
| ------------------ | ---------------------------- | --------------------------------- | ---------------- | --------------------------------- |
| SIFT               | Classical                    | Yes                               | No               | Sparse                            |
| ALIKED             | Learned                      | Yes                               | Not necessarily  | Sparse                            |
| ALIKED + LightGlue | Learned                      | Yes                               | Yes              | Sparse                            |
| LoFTR              | Detector-free correspondence | No conventional keypoint pipeline | Yes              | Dense / semi-dense correspondence |

This distinction is central to the ChandraMap research roadmap.

---

## Core Research Questions

The primary question is:

> **Can LoFTR provide reliable image correspondences for lunar image registration in cases where sparse local-feature pipelines struggle?**

This can be decomposed into measurable questions.

### Texture

- Can LoFTR establish useful correspondences in weakly textured lunar regions?
- Does it increase spatial coverage where sparse detectors produce few features?
- Does additional correspondence density translate into better independent registration accuracy?

### Repetitive Terrain

- How does LoFTR behave on repetitive crater, ridge, or terrain structures?
- Can high correspondence density produce false geometric consensus?
- Does geometric verification adequately reject ambiguous matches?

### Illumination

- How does LoFTR behave when the same lunar region is observed under different illumination?
- Are shadow changes associated with increased correspondence errors?
- Does preprocessing improve or degrade performance?

### Scale

- How does LoFTR behave under substantial scale differences?
- What image-resolution range is practical?
- Does image resizing affect correspondence quality?

### Cross-Sensor Registration

- How does LoFTR behave on OHRC-to-TMC-2 imagery?
- Can it support IIRS-derived 2D representations?
- How large is the modality gap that can be tolerated?

### Geometry

- Does LoFTR produce correspondences that support stable affine estimation?
- When is homography more appropriate?
- Are residuals spatially structured?
- Does correspondence density improve geometric estimation or simply increase false matches?

### Comparison

- How does LoFTR compare with SIFT?
- How does LoFTR compare with ALIKED + LightGlue?
- Does a difference in match count correspond to an improvement in independent registration accuracy?

### Computation

- What inference time does LoFTR require?
- What memory resources are required?
- How does image resolution affect computational cost?
- Is the cost appropriate for the intended ChandraMap use case?

---

## LoFTR in the ChandraMap Pipeline

The existing registration pipeline separates correspondence generation from geometric verification.

A future LoFTR integration should preserve that separation.

```text
Source Image
      │
      ▼
Image Representation
      │
      ▼
LoFTR Correspondence Inference
      │
      ▼
Candidate / High-Confidence Correspondences
      │
      ▼
Geometric Verification
      │
      ▼
Transformation Estimation
      │
      ▼
Residual Analysis
      │
      ▼
Optional Sub-Pixel Refinement
      │
      ▼
Independent Check-Point Evaluation
      │
      ▼
Final Registration
```

LoFTR should therefore be evaluated as a **correspondence-generation component**, not as an automatic registration solution.

A successful correspondence model does not by itself guarantee:

- correct global geometry
- adequate spatial coverage
- low independent checkpoint error
- robustness to lunar illumination
- robustness to sensor differences

---

## Why Detector-Free Correspondence Is Relevant to Lunar Imagery

Sparse feature pipelines depend on finding repeatable local structures.

Lunar imagery can contain areas with:

- relatively uniform terrain
- smooth slopes
- large shadow regions
- repetitive terrain
- weak local texture
- resolution-dependent structures

In such cases, a detector may produce too few useful keypoints.

LoFTR's research direction is relevant because it attempts to establish correspondence using broader image context rather than relying on the same interest-point detection process.

However, the original motivation and published results do not establish that LoFTR will behave similarly on lunar imagery.

That must be experimentally determined.

---

## Potential Advantages for ChandraMap

Potential research benefits include:

### Increased Correspondence Coverage

If LoFTR can establish useful correspondences in regions where sparse feature detectors fail, it may provide more spatially distributed observations for geometric estimation.

### Weak-Texture Investigation

LoFTR can specifically be evaluated on regions where SIFT or other sparse methods generate insufficient repeatable features.

### Context-Aware Correspondence

The use of image-pair-conditioned features and attention provides a different correspondence mechanism from independent local descriptors.

### Cross-Sensor Research

LoFTR can be investigated as a possible method for challenging appearance differences.

This does **not** establish that it is cross-modal by default.

### Alternative to Sparse Feature Pipelines

LoFTR provides a useful experimental contrast against:

- SIFT
- ALIKED
- ALIKED + LightGlue

The comparison can reveal whether detector-free correspondence is beneficial for ChandraMap's specific conditions.

---

## Potential Limitations

LoFTR should also be treated with caution.

Potential concerns include:

- computational cost
- GPU requirements
- sensitivity to input resolution
- dependence on the characteristics of training data
- domain shift from terrestrial training data to lunar imagery
- possible failure under strong appearance changes
- false correspondences in repetitive terrain
- correspondence concentration
- limited interpretability compared with classical features
- difficulty determining whether a match is physically meaningful
- potential mismatch between pretrained assumptions and lunar imagery

These are research questions rather than established failure results for ChandraMap.

---

## Domain Shift

One of the most important research concerns is domain shift.

A model trained on terrestrial imagery may encounter substantially different image statistics when applied to lunar imagery.

Potential differences include:

- terrain morphology
- texture statistics
- illumination geometry
- shadow behavior
- sensor characteristics
- spatial resolution
- image preprocessing
- contrast
- noise characteristics
- image modality

Therefore:

> Successful inference on a standard LoFTR benchmark cannot be treated as evidence of successful lunar registration.

The ChandraMap benchmark must provide the relevant evidence.

---

## OHRC Registration

IIRS and TMC-2 are not the only future applications.

A major research scenario is:

```text
OHRC Image A
     ↕
   LoFTR
     ↕
Reference Image B
```

The exact reference image source and benchmark configuration must be explicitly documented.

Potential research questions include:

- Does LoFTR produce sufficient correspondences?
- Are correspondences spatially distributed?
- Does geometric verification remain stable?
- How does performance change with scale?
- How does performance change with illumination?
- Does LoFTR outperform SIFT in independent checkpoint accuracy?
- Does it outperform SIFT only in correspondence count, or also in final registration quality?

No successful OHRC LoFTR result is established here.

**Status:** Planned.

---

## TMC-2 Registration

TMC-2 provides another future registration scenario.

Potential challenges include:

- resolution differences
- scale differences
- image appearance
- terrain texture
- illumination
- geometric differences

The experiment should compare LoFTR against the established classical baseline under controlled conditions.

**Status:** Planned.

---

## IIRS-Derived Representations

The IIRS research direction introduces a separate representation problem.

Relevant research:

[IIRS Registration Research](./IIRS.md)

A future LoFTR experiment could evaluate IIRS-derived 2D representations such as:

- selected spectral band
- band combination
- PCA component
- structural representation
- gradient representation

The important experimental distinction is:

```text
IIRS Hyperspectral Data
        ↓
2D Representation
        ↓
LoFTR
        ↓
Correspondences
        ↓
Geometric Verification
```

LoFTR should not be assumed to accept an arbitrary hyperspectral cube directly.

The IIRS representation must be defined and controlled before comparing correspondence performance.

---

## Geometric Verification

LoFTR correspondence output should not be treated as ground truth.

The future pipeline should continue to use robust geometric verification:

```text
LoFTR Correspondences
        ↓
Filtering / Confidence Selection
        ↓
RANSAC or Equivalent Robust Estimation
        ↓
Verified Inliers
        ↓
Transformation
```

Relevant ChandraMap geometry research:

[EXP-004 — Affine vs Homography](../../experiments/v1/geometry/EXP-004-affine-vs-homography/README.md)

The experiment should determine whether the correspondence distribution supports:

- translation
- similarity
- affine transformation
- homography
- another justified geometric model

The appropriate model should not be assumed in advance.

---

## Residual Analysis

Correspondence count alone is insufficient.

Relevant research:

[EXP-005 — Residual Analysis](../../experiments/v1/geometry/EXP-005-residual-analysis/README.md)

For a correspondence:

$$
r_i = x_i' - f(x_i;\theta)
$$

where:

- \(x_i\) is the source point,
- \(x_i'\) is the observed corresponding point,
- \(f(x_i;\theta)\) is the predicted location under transformation parameters \(\theta\),
- \(r_i\) is the residual vector.

The scalar residual magnitude can be represented as:

$$
e_i = \|r_i\|_2
$$

Future LoFTR experiments should inspect:

- mean residual
- RMSE
- median residual
- P90/P95 residual where available
- maximum residual where meaningful
- residual vector direction
- spatial residual distribution
- clusters of large residuals
- systematic residual patterns

A low average residual does not guarantee good global registration.

---

## Independent Check-Point Evaluation

The transformation should be evaluated on points that were not used to fit the final transformation.

The preferred structure is:

```text
LoFTR Correspondences
        ↓
Geometric Verification
        ↓
Control / Fit Points
        ↓
Transformation
        ↓
Independent Check Points
        ↓
Registration Error
```

This distinction is critical.

If the same points are used for both transformation fitting and evaluation, the resulting fitting error should not be described as independent registration accuracy.

If independent check points are unavailable, that limitation must be explicitly documented.

---

## Metrics

The following metrics should be considered.

| Metric                         | Purpose                                           |
| ------------------------------ | ------------------------------------------------- |
| Candidate correspondence count | Measures generated correspondence volume          |
| Verified inlier count          | Measures geometrically consistent correspondences |
| Inlier ratio                   | Measures proportion of verified matches           |
| Spatial coverage               | Measures distribution of correspondences          |
| Check-point RMSE               | Measures independent registration accuracy        |
| Median error                   | Measures typical independent error                |
| P90/P95 error                  | Measures upper-tail error                         |
| Maximum error                  | Identifies severe local errors where meaningful   |
| Registration success rate      | Measures reliability across test cases            |
| Runtime                        | Measures inference and processing cost            |
| Memory/resource usage          | Measures computational requirements               |

### Source-Image Error

Registration error should be reported in source-image pixels first.

Conversion to physical units should only be performed when GSD, projection, and reference information justify the conversion.

### Spatial Coverage

Spatial coverage should be evaluated because dense correspondence can still be geographically concentrated.

Possible measures include:

- grid occupancy
- convex-hull coverage
- correspondence density
- spatial distribution plots

### Success Rate

Success criteria should be defined before the experiment.

They should use measurable thresholds based on:

- independent checkpoint error
- minimum coverage
- minimum verified correspondence requirements
- geometric consistency

No decorative confidence score should be introduced.

---

## Correspondence Count Is Not Registration Quality

A central evaluation principle is:

> More matches do not automatically mean better registration.

A method may produce many correspondences that are:

- incorrect
- spatially clustered
- repetitive
- geometrically ambiguous
- concentrated in a small region

Therefore the research chain must remain:

```text
Candidate Correspondences
        ↓
Verified Inliers
        ↓
Transformation
        ↓
Independent Evaluation
```

The final judgment should rely primarily on independently measured registration quality and robustness.

---

## Scale and Resolution

Scale is a major ChandraMap research dimension.

Relevant research:

[Scale Invariance](../notes/scale-invariance.md)

Existing preprocessing research:

[EXP-002 — Scale Pyramid](../../experiments/v1/preprocessing/EXP-002-scale-pyramid/README.md)

Future LoFTR experiments should investigate:

- native image scale
- image resizing
- resolution differences
- reference-image pyramids
- feature localization under scale changes
- computational cost as resolution changes

Potential questions include:

- Does LoFTR remain stable when one image is downsampled?
- Does excessive resizing remove useful lunar structures?
- Does increased resolution substantially increase computation?
- Is correspondence quality sensitive to image dimensions?

No fixed scale limit should be claimed without measurement.

---

## Illumination Robustness

Relevant research:

[Illumination Invariance](../notes/illumination-invariance.md)

Lunar illumination can change:

- shadow direction
- shadow extent
- visible boundaries
- local contrast
- apparent texture
- gradient structure

LoFTR should therefore be evaluated under controlled illumination variation.

A key distinction is:

```text
Photometric Normalization
        ≠
Illumination Geometry Invariance
        ≠
Reliable Cross-Illumination Correspondence
```

Normalization may reduce intensity differences, but it cannot necessarily recover information affected by changing shadow geometry.

---

## Cross-Sensor Research

LoFTR should eventually be investigated for:

```text
OHRC ↔ TMC-2
OHRC ↔ IIRS-derived representation
TMC-2 ↔ IIRS-derived representation
```

The experiments should document:

- sensor pair
- image resolution
- representation
- preprocessing
- image scale
- illumination condition
- geometric model
- correspondence configuration
- evaluation points
- independent error

Cross-sensor performance should not be inferred from same-sensor performance.

---

## Proposed Experimental Roadmap

The recommended research sequence is:

```text
Existing SIFT Baseline
        ↓
Controlled LoFTR Inference
        ↓
Geometric Verification
        ↓
Independent Evaluation
        ↓
SIFT vs LoFTR
        ↓
Weak-Texture Evaluation
        ↓
Scale Evaluation
        ↓
Illumination Evaluation
        ↓
OHRC / TMC-2 Cross-Sensor Evaluation
        ↓
IIRS Representation Evaluation
        ↓
ALIKED + LightGlue Comparison
        ↓
Failure Analysis
        ↓
Reproducibility Assessment
        ↓
Implementation Decision
```

The purpose of this sequence is to avoid introducing LoFTR into the main system before its behavior is understood.

---

# Proposed Experiments

The following experiments are proposed research activities. They do not represent completed experiments.

## LOFTR-EXP-001 — Controlled LoFTR Baseline

| Field             | Definition                                                                                   |
| ----------------- | -------------------------------------------------------------------------------------------- |
| Experiment ID     | `LOFTR-EXP-001`                                                                              |
| Research Question | Can LoFTR establish reproducible correspondences on the controlled ChandraMap baseline pair? |
| Hypothesis        | LoFTR can produce usable correspondences under at least some controlled conditions.          |
| Input             | `[TBD]`                                                                                      |
| Representation    | `[TBD]`                                                                                      |
| Comparison        | Existing SIFT baseline                                                                       |
| Variables         | LoFTR configuration                                                                          |
| Metrics           | Inliers, coverage, checkpoint error, runtime                                                 |
| Failure Criteria  | Insufficient or unreliable geometric registration                                            |
| Status            | Planned                                                                                      |

---

## LOFTR-EXP-002 — Weak-Texture Evaluation

| Field                   | Definition                                                                        |
| ----------------------- | --------------------------------------------------------------------------------- |
| Experiment ID           | `LOFTR-EXP-002`                                                                   |
| Research Question       | Does LoFTR provide useful correspondences where sparse feature detection is weak? |
| Input                   | Controlled weak-texture regions                                                   |
| Comparison              | SIFT baseline                                                                     |
| Variables               | Terrain texture level                                                             |
| Metrics                 | Feature/correspondence density, inliers, coverage, independent error              |
| Expected Interpretation | Determine whether detector-free correspondence provides measurable benefit        |
| Status                  | Planned                                                                           |

---

## LOFTR-EXP-003 — Repetitive-Terrain Evaluation

| Field             | Definition                                                                   |
| ----------------- | ---------------------------------------------------------------------------- |
| Experiment ID     | `LOFTR-EXP-003`                                                              |
| Research Question | How does LoFTR behave in repetitive or ambiguous terrain?                    |
| Input             | `[TBD]`                                                                      |
| Variables         | Terrain repetition / ambiguity                                               |
| Metrics           | False-match rate where measurable, geometric verification, independent error |
| Failure Analysis  | False consensus and spatial ambiguity                                        |
| Status            | Planned                                                                      |

---

## LOFTR-EXP-004 — Scale Robustness

| Field             | Definition                                                               |
| ----------------- | ------------------------------------------------------------------------ |
| Experiment ID     | `LOFTR-EXP-004`                                                          |
| Research Question | How does LoFTR behave under controlled scale and resolution differences? |
| Input             | Controlled image pair                                                    |
| Variables         | Scale factor, resampling method, resolution                              |
| Metrics           | Correspondence quality, inliers, coverage, checkpoint error, runtime     |
| Comparison        | SIFT baseline                                                            |
| Status            | Planned                                                                  |

---

## LOFTR-EXP-005 — Illumination Robustness

| Field             | Definition                                                          |
| ----------------- | ------------------------------------------------------------------- |
| Experiment ID     | `LOFTR-EXP-005`                                                     |
| Research Question | How does LoFTR behave under lunar illumination variation?           |
| Input             | `[TBD]`                                                             |
| Variables         | Illumination condition                                              |
| Metrics           | Inliers, spatial coverage, independent error, residual distribution |
| Comparison        | SIFT baseline                                                       |
| Status            | Planned                                                             |

---

## LOFTR-EXP-006 — OHRC Cross-Sensor Registration

| Field             | Definition                                                        |
| ----------------- | ----------------------------------------------------------------- |
| Experiment ID     | `LOFTR-EXP-006`                                                   |
| Research Question | Can LoFTR provide useful OHRC cross-sensor correspondences?       |
| Input             | `[TBD]`                                                           |
| Sensor Pair       | OHRC ↔ `[TBD]`                                                    |
| Variables         | Representation, scale, illumination                               |
| Metrics           | Independent checkpoint error, coverage, inliers, success, runtime |
| Status            | Planned                                                           |

---

## LOFTR-EXP-007 — TMC-2 Cross-Sensor Registration

| Field             | Definition                                                        |
| ----------------- | ----------------------------------------------------------------- |
| Experiment ID     | `LOFTR-EXP-007`                                                   |
| Research Question | Can LoFTR provide useful TMC-2 cross-sensor correspondences?      |
| Input             | `[TBD]`                                                           |
| Sensor Pair       | TMC-2 ↔ `[TBD]`                                                   |
| Variables         | Representation, scale, illumination                               |
| Metrics           | Independent checkpoint error, coverage, inliers, success, runtime |
| Status            | Planned                                                           |

---

## LOFTR-EXP-008 — IIRS Representation Compatibility

| Field             | Definition                                                                  |
| ----------------- | --------------------------------------------------------------------------- |
| Experiment ID     | `LOFTR-EXP-008`                                                             |
| Research Question | Can LoFTR operate meaningfully on selected IIRS-derived 2D representations? |
| Input             | IIRS data `[TBD]`                                                           |
| Representations   | Single band, PCA, band combination, structural representation               |
| Comparison        | Classical correspondence baseline                                           |
| Metrics           | Independent error, inliers, coverage, runtime                               |
| Status            | Planned                                                                     |

Relevant research:

[IIRS Registration Research](./IIRS.md)

---

## LOFTR-EXP-009 — Geometric Model Comparison

| Field             | Definition                                                                                     |
| ----------------- | ---------------------------------------------------------------------------------------------- |
| Experiment ID     | `LOFTR-EXP-009`                                                                                |
| Research Question | Which geometric model adequately represents LoFTR correspondences for the tested lunar scenes? |
| Models            | Affine, homography, other justified models                                                     |
| Variables         | Geometric model                                                                                |
| Metrics           | Fit residual, independent error, coverage, stability                                           |
| Reference         | EXP-004                                                                                        |
| Status            | Planned                                                                                        |

---

## LOFTR-EXP-010 — ALIKED + LightGlue Comparison

| Field             | Definition                                                                                  |
| ----------------- | ------------------------------------------------------------------------------------------- |
| Experiment ID     | `LOFTR-EXP-010`                                                                             |
| Research Question | How does detector-free LoFTR correspondence compare with a learned sparse-feature pipeline? |
| Methods           | LoFTR vs ALIKED + LightGlue                                                                 |
| Control           | Same image pairs and evaluation protocol                                                    |
| Metrics           | Independent error, coverage, inliers, runtime/resource usage                                |
| Status            | Planned                                                                                     |

The comparison should remain descriptive and evidence-based.

---

## LOFTR-EXP-011 — Failure Analysis

| Field             | Definition                                                                  |
| ----------------- | --------------------------------------------------------------------------- |
| Experiment ID     | `LOFTR-EXP-011`                                                             |
| Research Question | Under what conditions does LoFTR fail to produce useful registration?       |
| Input             | All relevant benchmark failures                                             |
| Analysis          | Correspondence visualization, residuals, spatial coverage, scene conditions |
| Output            | Failure taxonomy                                                            |
| Status            | Planned                                                                     |

---

## Benchmark Design

LoFTR evaluation should use the same general benchmark principles established by ChandraMap.

Each benchmark case should have:

- fixed source image
- fixed reference image
- known image relationship
- documented preprocessing
- explicit ground truth
- independent check points where available
- reproducible configuration
- defined success criteria

The benchmark should preserve failures.

Failed cases should not be removed simply because they reduce average performance.

---

## Ground-Truth Requirements

Relevant research:

[Ground-Truth Design](../notes/ground-truth-design.md)

The benchmark should distinguish:

```text
Candidate Correspondences
        ↓
Verified Correspondences
        ↓
Control / Fit Points
        ↓
Estimated Transformation
        ↓
Independent Check Points
        ↓
Measured Registration Error
```

Potential ground-truth sources include:

- independently established control points
- trusted reference products
- geometrically controlled image products
- independently verified correspondences

The exact LoFTR lunar benchmark dataset is:

`[Not provided]`

The benchmark should explicitly document:

- image identifiers
- acquisition information
- coordinate systems
- image resolution
- GSD where available
- projection information
- control points
- independent check points
- annotation methodology
- uncertainty where available
- dataset version

---

## Model Configuration

A future LoFTR experiment must record the exact configuration used.

At minimum:

- LoFTR implementation/version
- pretrained checkpoint
- model configuration
- image preprocessing
- image normalization
- image dimensions
- resizing
- grayscale/color representation
- confidence thresholds
- correspondence filtering
- geometric verification configuration
- transformation model
- RANSAC parameters
- random seeds where applicable
- hardware
- software environment

The exact ChandraMap configuration is:

`[TBD]`

No undocumented default should be treated as a reproducible experimental configuration.

---

## Computational Requirements

LoFTR is expected to introduce greater computational requirements than the initial classical baseline.

Future experiments should measure:

- inference time
- preprocessing time
- geometric-verification time
- total runtime
- GPU memory
- CPU memory where relevant
- batch size
- input resolution
- hardware configuration

Runtime should be reported separately from registration accuracy.

A method that produces useful correspondences but is computationally impractical for the intended application should be documented accordingly.

No specific hardware requirement should be assumed until measured in the ChandraMap environment.

---

## Image Resolution Considerations

Input resolution is particularly important for correspondence methods.

Future research should investigate:

- native-resolution inference
- controlled downsampling
- image tiling
- image pyramids
- overlapping tiles
- resolution-dependent correspondence density

Tiling may be relevant for large lunar images, but it should not be introduced without considering:

- boundary effects
- duplicate correspondences
- loss of global context
- inconsistent transformations between tiles
- computational overhead

Any tiling strategy should be experimentally validated.

---

## Spatial Coverage

LoFTR's correspondence density should be evaluated spatially.

For each test case, future analysis should consider:

- total correspondence count
- correspondence density
- grid coverage
- convex-hull coverage
- correspondence distribution
- concentration in high-texture areas

A high number of correspondences concentrated in a small region should not be interpreted as successful global registration.

---

## Failure Modes

### Weak Texture

Possible issue:

- insufficient discriminative information

Detection:

- low correspondence density
- low spatial coverage
- unstable geometric estimation
- high independent checkpoint error

---

### Repetitive Terrain

Possible issue:

- ambiguous correspondence

Detection:

- multiple spatially plausible matches
- high candidate correspondence count but poor independent error
- unstable transformation estimates
- spatially repeated residual patterns

---

### Illumination Change

Possible issue:

- appearance and shadow geometry changes

Detection:

- correspondence degradation across illumination conditions
- increased residuals near shadow boundaries
- lower geometric verification stability

---

### Scale Mismatch

Possible issue:

- image structures occur at substantially different scales

Detection:

- decreasing correspondence quality with scale factor
- unstable feature localization
- reduced coverage

---

### Cross-Sensor Appearance Gap

Possible issue:

- different sensor responses produce different image appearance

Detection:

- lower inlier ratio
- poor spatial distribution
- high independent error
- systematic failure on specific sensor pairs

---

### False Geometric Consensus

Possible issue:

- incorrect correspondences form a plausible transformation

Detection:

- low fitting error but high independent checkpoint error
- spatially clustered inliers
- inconsistent residual field

---

### Incorrect Geometric Model

Possible issue:

- correspondence relationship cannot be represented adequately by the selected model

Detection:

- systematic residual structure
- region-dependent error
- unstable extrapolation
- large independent errors despite many inliers

---

### Domain Shift

Possible issue:

- pretrained learned features behave differently on lunar imagery

Detection:

- significant degradation relative to controlled reference data
- inconsistent performance across lunar scenes
- sensor-specific failure patterns

---

### Resolution Loss

Possible issue:

- downsampling removes registration-relevant structures

Detection:

- correspondence degradation as resolution decreases
- reduced coverage
- increasing checkpoint error

---

### Computational Cost

Possible issue:

- inference becomes impractical at high image resolution

Detection:

- runtime profiling
- GPU memory measurements
- resource scaling with image dimensions

---

## Comparison With the Classical Baseline

The SIFT baseline should remain the reference point.

Relevant experiment:

[EXP-001 — SIFT Baseline](../../experiments/v1/baseline/EXP-001-sift-baseline/README.md)

A controlled comparison should use:

| Comparison Dimension          | SIFT             | LoFTR                                               |
| ----------------------------- | ---------------- | --------------------------------------------------- |
| Feature-generation paradigm   | Classical sparse | Learned detector-free correspondence                |
| Keypoint detector             | Yes              | Not based on the same traditional detector pipeline |
| Conventional descriptor stage | Yes              | Not the same sparse descriptor pipeline             |
| Learned correspondence        | No               | Yes                                                 |
| Correspondence density        | Sparse           | Potentially denser                                  |
| Geometric verification        | Required         | Required                                            |
| Independent evaluation        | Required         | Required                                            |
| Lunar validation              | To be measured   | To be measured                                      |
| Computational cost            | To be measured   | To be measured                                      |

The purpose is not to declare a universal winner.

The purpose is to determine where each method succeeds or fails under the same benchmark conditions.

---

## Comparison With ALIKED + LightGlue

A particularly useful future comparison is:

```text
                 ┌→ ALIKED → LightGlue ─┐
Input Images ────┤                       ├→ Geometric Verification
                 └→ LoFTR ──────────────┘
```

This comparison separates two learned research directions:

### Sparse Learned Pipeline

```text
Image
 ↓
Learned Keypoints
 ↓
Learned Descriptors
 ↓
Sparse Learned Matching
 ↓
Correspondences
```

### Detector-Free Pipeline

```text
Image Pair
 ↓
Joint Transformer Correspondence
 ↓
Coarse-to-Fine Matches
 ↓
Correspondences
```

The benchmark should determine whether the difference produces measurable changes in:

- independent registration error
- spatial coverage
- inlier ratio
- robustness
- runtime
- memory usage
- failure behavior

---

## Sub-Pixel Refinement

Existing ChandraMap research includes sub-pixel refinement:

[EXP-006 — Subpixel Refinement](../../experiments/v1/refinement/EXP-006-subpixel-refinement/README.md)

If LoFTR correspondences are refined to sub-pixel accuracy, the experimental order should remain explicit:

```text
LoFTR Correspondences
        ↓
Geometric Verification
        ↓
Verified Correspondences
        ↓
Sub-Pixel Refinement
        ↓
Final Transformation
        ↓
Independent Evaluation
```

Sub-pixel refinement should not be assumed to improve accuracy.

Its effect must be measured independently.

---

## Representation and Preprocessing

LoFTR experiments should control preprocessing carefully.

Potential variables include:

- grayscale conversion
- intensity normalization
- contrast normalization
- gradient representation
- image resizing
- image pyramid
- denoising
- cropping
- tiling

Relevant existing research includes:

[EXP-003 — Gradient Representation](../../experiments/v1/preprocessing/EXP-003-gradient-representation/README.md)

A preprocessing experiment should not change simultaneously with the LoFTR model if the goal is to isolate LoFTR's effect.

---

## Research Controls

A fair comparison should control as many variables as practical.

For example:

| Variable               | Control               |
| ---------------------- | --------------------- |
| Image pair             | Same                  |
| Ground truth           | Same                  |
| Preprocessing          | Same where compatible |
| Geometric verification | Same                  |
| Transformation model   | Same                  |
| Evaluation points      | Same                  |
| Success criteria       | Same                  |
| Hardware reporting     | Same methodology      |
| Runtime measurement    | Same methodology      |

The primary independent variable should be the correspondence method when comparing methods.

---

## Experimental Interpretation

A future experiment should distinguish at least four outcomes.

### More Matches + Lower Independent Error

This provides evidence that the method may improve registration for the tested conditions.

### More Matches + Similar Independent Error

This suggests increased correspondence density does not necessarily translate into better registration.

### More Matches + Higher Independent Error

This may indicate additional correspondences are ambiguous or geometrically incorrect.

### Fewer Matches + Lower Independent Error

This may indicate that correspondence quantity is not the primary determinant of registration quality.

The final interpretation should be based on measured evidence.

---

## Research Decision Criteria

LoFTR should only progress toward implementation if experiments provide sufficient evidence.

Important criteria include:

### Accuracy

- Does independent checkpoint error improve or remain acceptable?
- Are residuals stable?
- Does performance generalize across representative cases?

### Coverage

- Are correspondences spatially distributed?
- Does the method cover useful parts of the image?

### Robustness

- Does performance remain acceptable across scale?
- Does performance remain acceptable under illumination changes?
- Does it remain useful across sensor pairs?

### Geometric Stability

- Does geometric verification remain stable?
- Are false geometric consensus cases controlled?

### Reproducibility

- Can the experiment be repeated?
- Are model checkpoints and configurations documented?
- Are preprocessing parameters fixed?

### Computational Cost

- Is runtime acceptable?
- Is memory usage acceptable?
- Does performance remain practical at required image sizes?

### Scientific Utility

- Does LoFTR address a failure mode that matters to ChandraMap?
- Is the benefit measurable rather than merely visual?

---

## Evidence Thresholds

No universal numeric threshold should be invented here.

Future benchmark definitions should establish thresholds based on:

- application requirements
- ground-truth uncertainty
- image resolution
- GSD
- expected registration accuracy
- spatial coverage requirements

The exact thresholds are:

`[TBD]`

---

## Reproducibility Requirements

Every LoFTR experiment should record:

### Dataset

- dataset identifier
- image identifiers
- sensor
- acquisition metadata available
- ground-truth version

### Representation

- image representation
- grayscale/color conversion
- normalization
- resizing
- cropping
- tiling
- pyramid configuration

### Model

- LoFTR implementation
- repository revision/version
- checkpoint identifier
- model configuration
- pretrained/fine-tuned status

### Matching

- inference configuration
- confidence thresholds
- correspondence filtering
- minimum correspondence requirements

### Geometry

- geometric model
- RANSAC configuration
- threshold
- confidence
- iteration limit
- random seed

### Evaluation

- control points
- independent check points
- metric definitions
- spatial-coverage methodology
- success criteria

### Environment

- operating system
- Python version
- framework versions
- CUDA/runtime versions where relevant
- GPU model
- CPU
- memory
- execution mode

### Artifacts

- configuration file
- logs
- correspondence files
- match visualization
- geometric verification visualization
- transformation parameters
- residual analysis
- checkpoint-error report
- runtime report
- failure cases

---

## Expected Research Artifacts

Future LoFTR research may produce:

- experiment README files
- model configuration files
- reproducible inference scripts
- benchmark manifests
- correspondence files
- match visualizations
- geometric verification outputs
- transformation estimates
- residual plots
- checkpoint-error tables
- spatial-coverage visualizations
- runtime/resource reports
- failure-analysis reports
- comparison tables
- reproducibility records

These are expected research artifacts and are not claims that they currently exist.

---

## Implementation Strategy

If experimental evidence supports further investigation, implementation should proceed incrementally.

### Stage 1 — Isolated Research Prototype

Create an isolated LoFTR research implementation without modifying the production registration pipeline.

### Stage 2 — Benchmark Adapter

Add a reproducible adapter that converts ChandraMap benchmark inputs into the required LoFTR input format.

### Stage 3 — Standardized Output

Store correspondence outputs in a format compatible with existing geometric verification and evaluation.

### Stage 4 — Evaluation Integration

Connect LoFTR outputs to:

- geometric verification
- transformation estimation
- residual analysis
- independent checkpoint evaluation

### Stage 5 — Controlled Comparison

Run the same benchmark against:

- SIFT
- LoFTR
- ALIKED + LightGlue where available

### Stage 6 — Integration Decision

Only after sufficient evidence should LoFTR be considered for broader ChandraMap integration.

---

## Training vs Pretrained Inference

A future LoFTR investigation should initially distinguish between:

### Pretrained Inference

Use an existing pretrained LoFTR checkpoint without modifying the model.

Advantages:

- simpler experiment
- lower development complexity
- useful first feasibility test

Limitations:

- possible domain shift
- pretrained behavior may not represent lunar imagery
- no lunar-specific adaptation

### Lunar Fine-Tuning

A later research direction may investigate fine-tuning on lunar correspondence data.

This would require:

- sufficiently representative training data
- ground-truth correspondences
- train/validation/test separation
- reproducible training configuration
- controlled augmentation
- checkpoint versioning
- independent evaluation

Fine-tuning should not be attempted before the baseline behavior is characterized.

---

## Dataset Split Requirements for Learned Adaptation

If future LoFTR training or fine-tuning is investigated, the dataset should be separated into:

```text
Training Data
      ↓
Validation Data
      ↓
Test Data
      ↓
Independent Final Evaluation
```

Images or regions that leak between training and final evaluation could produce misleading results.

The exact dataset split is:

`[TBD]`

---

## Scientific Integrity

The following rules apply to all LoFTR research:

- Do not fabricate lunar benchmark results.
- Do not claim LoFTR is superior to SIFT without controlled evidence.
- Do not claim LoFTR is superior to ALIKED + LightGlue without controlled evidence.
- Do not claim robustness to illumination without illumination experiments.
- Do not claim robustness to scale without scale experiments.
- Do not claim cross-sensor capability without cross-sensor experiments.
- Do not claim IIRS compatibility without IIRS representation experiments.
- Do not equate correspondence count with registration accuracy.
- Do not use fitting error as independent accuracy.
- Do not remove failed cases.
- Do not hide computational costs.
- Do not report unsupported thresholds.
- Do not claim production readiness without reproducibility evidence.
- Clearly distinguish hypotheses from observations.
- Clearly distinguish proposed experiments from completed experiments.

---

## Failure Analysis Framework

Every unsuccessful LoFTR experiment should attempt to answer:

1. Did LoFTR produce enough correspondences?
2. Were the correspondences spatially distributed?
3. Were they geometrically consistent?
4. Did RANSAC reject most correspondences?
5. Did the transformation fit the entire region?
6. Were independent check points accurate?
7. Was the error localized or global?
8. Did scale contribute to the failure?
9. Did illumination contribute to the failure?
10. Did sensor differences contribute to the failure?
11. Was the image representation appropriate?
12. Was computational limitation responsible?
13. Did the model appear affected by domain shift?

A failure should result in a reproducible artifact whenever practical.

---

## Expected Failure Categories

Future benchmark reporting should categorize failures using labels such as:

- `INSUFFICIENT_CORRESPONDENCE`
- `LOW_SPATIAL_COVERAGE`
- `GEOMETRIC_INCONSISTENCY`
- `REPETITIVE_TERRAIN`
- `WEAK_TEXTURE`
- `ILLUMINATION_VARIATION`
- `SCALE_MISMATCH`
- `CROSS_SENSOR_APPEARANCE`
- `GROUND_TRUTH_LIMITATION`
- `COMPUTATIONAL_LIMITATION`
- `UNSTABLE_TRANSFORMATION`
- `HIGH_CHECKPOINT_ERROR`
- `OTHER`

The exact failure taxonomy may evolve as experiments provide evidence.

---

## Relationship to ChandraMap V1

V1 establishes the experimental foundation required before evaluating more complex learned correspondence methods.

The progression is:

```text
SIFT Baseline
    ↓
Scale Investigation
    ↓
Structure-Focused Representation
    ↓
Geometric Model Evaluation
    ↓
Residual Analysis
    ↓
Sub-Pixel Refinement
    ↓
Future Learned Correspondence
```

Relevant experiments:

- [EXP-001 — SIFT Baseline](../../experiments/v1/baseline/EXP-001-sift-baseline/README.md)
- [EXP-002 — Scale Pyramid](../../experiments/v1/preprocessing/EXP-002-scale-pyramid/README.md)
- [EXP-003 — Gradient Representation](../../experiments/v1/preprocessing/EXP-003-gradient-representation/README.md)
- [EXP-004 — Affine vs Homography](../../experiments/v1/geometry/EXP-004-affine-vs-homography/README.md)
- [EXP-005 — Residual Analysis](../../experiments/v1/geometry/EXP-005-residual-analysis/README.md)
- [EXP-006 — Subpixel Refinement](../../experiments/v1/refinement/EXP-006-subpixel-refinement/README.md)

LoFTR should build upon this evaluation foundation rather than bypass it.

---

## Relationship to Other Future Research

### ALIKED

[ALIKED Research](./ALIKED.md)

ALIKED represents a learned sparse local-feature direction.

A future comparison should examine whether learned sparse features provide sufficient robustness compared with detector-free LoFTR correspondence.

### LightGlue

[LightGlue Research](./LIGHTGLUE.md)

LightGlue represents learned sparse feature matching.

A useful future comparison is:

```text
ALIKED + LightGlue
        vs
LoFTR
```

The comparison should focus on measurable registration outcomes.

### IIRS

[IIRS Registration Research](./IIRS.md)

IIRS introduces a hyperspectral representation problem.

LoFTR may eventually be evaluated on selected IIRS-derived 2D representations.

---

## Research Roadmap

```text
SIFT Baseline
      ↓
Controlled LoFTR Inference
      ↓
Geometric Verification
      ↓
Independent Evaluation
      ↓
SIFT ↔ LoFTR Comparison
      ↓
Weak-Texture Study
      ↓
Repetitive-Terrain Study
      ↓
Scale Study
      ↓
Illumination Study
      ↓
OHRC / TMC-2 Cross-Sensor Study
      ↓
IIRS Representation Study
      ↓
ALIKED + LightGlue Comparison
      ↓
Failure Analysis
      ↓
Reproducibility Assessment
      ↓
Implementation Candidate Decision
      ↓
Optional Lunar Fine-Tuning Research
```

---

## Open Research Questions

The following questions remain open until measured experimentally:

1. Does LoFTR provide useful correspondences on lunar imagery?
2. Does it improve independent registration accuracy over SIFT?
3. Does it improve spatial coverage?
4. Does greater correspondence density improve geometric estimation?
5. How does it behave on weakly textured lunar terrain?
6. How does it behave on repetitive terrain?
7. How does it behave under illumination changes?
8. How does it behave under scale changes?
9. How does it behave across OHRC and TMC-2?
10. Can it operate effectively on IIRS-derived 2D representations?
11. How does it compare with ALIKED + LightGlue?
12. What geometric model is appropriate for its correspondence output?
13. What are its dominant failure modes on lunar imagery?
14. What computational resources are required?
15. How sensitive is performance to input resolution?
16. Does pretrained inference generalize sufficiently to lunar imagery?
17. Is lunar-specific fine-tuning necessary?
18. How much training data would be required for reliable adaptation?
19. Can LoFTR improve difficult cases without degrading easy cases?
20. Is its computational cost justified by measurable registration improvements?

---

## Research Decision Matrix

The future decision should be based on evidence rather than a single benchmark result.

| Question                                    | Required Evidence                                       |
| ------------------------------------------- | ------------------------------------------------------- |
| Does LoFTR generate useful correspondences? | Verified inlier and independent checkpoint analysis     |
| Does it improve registration?               | Controlled comparison against baseline                  |
| Does it improve coverage?                   | Spatial-coverage analysis                               |
| Is it robust to scale?                      | Controlled scale experiment                             |
| Is it robust to illumination?               | Controlled illumination experiment                      |
| Does it work cross-sensor?                  | OHRC/TMC-2 benchmark                                    |
| Does it work with IIRS?                     | IIRS representation experiment                          |
| Is it reproducible?                         | Repeated execution with recorded configuration          |
| Is it computationally practical?            | Runtime and resource profiling                          |
| Should it enter production?                 | Combined evidence across representative benchmark cases |

---

## Validation Checklist

Before considering LoFTR validated for a ChandraMap research scenario:

- [ ] LoFTR implementation/version is recorded.
- [ ] Model checkpoint is identified.
- [ ] Input preprocessing is documented.
- [ ] Image representation is documented.
- [ ] Image resolution is recorded.
- [ ] Benchmark images are uniquely identified.
- [ ] Ground truth is versioned.
- [ ] Independent check points are defined.
- [ ] Candidate correspondences are distinguished from verified inliers.
- [ ] Geometric verification is documented.
- [ ] Transformation model is documented.
- [ ] RANSAC configuration is documented.
- [ ] Spatial coverage is measured.
- [ ] Independent checkpoint error is measured.
- [ ] Runtime is measured.
- [ ] Resource usage is measured where relevant.
- [ ] Scale robustness is evaluated where relevant.
- [ ] Illumination robustness is evaluated where relevant.
- [ ] Cross-sensor behavior is evaluated where relevant.
- [ ] Failure cases are preserved.
- [ ] Results are reproducible.
- [ ] Comparison conditions are controlled.
- [ ] Claims are supported by measured evidence.

---

## Implementation Status

**LoFTR: Future Research Direction**

Current status:

- **Literature concept:** Established
- **ChandraMap implementation:** `[Not implemented]`
- **Lunar benchmark evaluation:** `[Not evaluated]`
- **OHRC evaluation:** `[Not evaluated]`
- **TMC-2 evaluation:** `[Not evaluated]`
- **IIRS evaluation:** `[Not evaluated]`
- **Lunar fine-tuning:** `[Not implemented]`
- **Production selection:** `[Not decided]`

These statuses should be updated only when corresponding repository evidence exists.

---

## References / Further Reading

### Internal ChandraMap Research

- [Research Documentation](../README.md)
- [Research Literature](../literature/README.md)
- [Lunar Registration](../notes/lunar-registration.md)
- [Illumination Invariance](../notes/illumination-invariance.md)
- [Scale Invariance](../notes/scale-invariance.md)
- [Ground-Truth Design](../notes/ground-truth-design.md)
- [IIRS Registration Research](./IIRS.md)
- [ALIKED Research](./ALIKED.md)
- [LightGlue Research](./LIGHTGLUE.md)

### Existing V1 Experiments

- [V1 Experiment Overview](../../experiments/v1/README.md)
- [EXP-001 — SIFT Baseline](../../experiments/v1/baseline/EXP-001-sift-baseline/README.md)
- [EXP-002 — Scale Pyramid](../../experiments/v1/preprocessing/EXP-002-scale-pyramid/README.md)
- [EXP-003 — Gradient Representation](../../experiments/v1/preprocessing/EXP-003-gradient-representation/README.md)
- [EXP-004 — Affine vs Homography](../../experiments/v1/geometry/EXP-004-affine-vs-homography/README.md)
- [EXP-005 — Residual Analysis](../../experiments/v1/geometry/EXP-005-residual-analysis/README.md)
- [EXP-006 — Subpixel Refinement](../../experiments/v1/refinement/EXP-006-subpixel-refinement/README.md)

### External LoFTR Literature

Sun et al., **“LoFTR: Detector-Free Local Feature Matching with Transformers,”** CVPR 2021.

The original publication should be treated as the primary technical reference for the LoFTR architecture and its reported experiments. ChandraMap-specific conclusions must come from ChandraMap experiments rather than from results reported on unrelated datasets.

The official LoFTR implementation and project materials should be consulted when a future implementation is started.

---

## Conclusion

LoFTR represents a distinct future correspondence direction for ChandraMap.

Its importance is not simply that it is another learned matcher. The research question is whether a detector-free, coarse-to-fine correspondence approach can address failure modes that arise when sparse local-feature methods do not provide enough reliable, spatially distributed correspondences.

The appropriate research progression is:

```text
Classical Baseline
        ↓
Controlled LoFTR Evaluation
        ↓
Geometric Verification
        ↓
Independent Registration Evaluation
        ↓
Scale / Illumination Testing
        ↓
Cross-Sensor Testing
        ↓
IIRS Representation Testing
        ↓
ALIKED + LightGlue Comparison
        ↓
Failure Analysis
        ↓
Reproducibility Assessment
        ↓
Implementation Decision
```

The central principle is:

> **LoFTR should be considered a candidate correspondence method, not an assumed solution to lunar image registration.**

Its value for ChandraMap must be established through controlled benchmark experiments, explicit ground truth, geometric verification, independent checkpoint evaluation, spatial-coverage analysis, failure analysis, and reproducible computational measurements.
