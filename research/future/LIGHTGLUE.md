# LightGlue Research

> **Status:** Future research specification
> **Implementation status:** Not implemented or experimentally validated in ChandraMap
> **Research maturity:** Research direction / candidate matcher
> **Primary role:** Learned sparse feature matching
> **Related research:** ALIKED, SIFT, LoFTR, RIFT, CFOG, geometric verification, sub-pixel refinement

---

## 1. Purpose

This document defines a future research direction for evaluating **LightGlue** as a learned feature-matching component within ChandraMap.

The purpose is to determine whether LightGlue can provide more reliable and geometrically useful correspondences for lunar image registration than the current classical matching strategy.

This document does **not** establish LightGlue as:

- a current ChandraMap component
- a validated lunar matcher
- a production dependency
- the default matching method
- a replacement for SIFT
- a replacement for geometric verification
- a solution to lunar cross-sensor registration by itself

LightGlue should only move toward implementation or inclusion in a future ChandraMap version after controlled experiments provide sufficient evidence.

The research principle is:

> **A learned matcher must earn its place through measured improvement on the ChandraMap benchmark.**

---

## 2. ChandraMap Context

ChandraMap is a lunar image correspondence and registration research system focused on aligning imagery of the same lunar region acquired under different:

- sensors
- spatial resolutions
- illumination conditions
- viewpoints
- image representations
- sensing modalities
- geometric conditions

Relevant Chandrayaan-2 imagery includes:

- **OHRC**
- **TMC-2**
- **IIRS**

The current research architecture emphasizes:

1. measurable experiments
2. explicit ground truth
3. candidate correspondence generation
4. geometric verification
5. independent accuracy evaluation
6. spatial coverage
7. residual analysis
8. sub-pixel refinement
9. reproducibility
10. controlled baseline comparisons

LightGlue belongs specifically to the **local feature matching** stage.

It should not be treated as the complete registration solution.

---

## 3. Research Status

| Item                                   | Status             |
| -------------------------------------- | ------------------ |
| LightGlue implementation in ChandraMap | Not implemented    |
| LightGlue lunar benchmark results      | Not available      |
| LightGlue production selection         | Not selected       |
| Lunar-specific LightGlue training      | Not established    |
| ALIKED + LightGlue experiment          | Future research    |
| SIFT + LightGlue experiment            | Future research    |
| Geometric verification after LightGlue | Required           |
| Independent check-point evaluation     | Required           |
| Sub-pixel refinement after LightGlue   | Future integration |
| Sensor-specific evaluation             | Required           |
| V1 replacement decision                | Not made           |

Any future implementation must preserve the distinction between **research candidate** and **validated system component**.

---

# 4. What Is LightGlue?

LightGlue is a learned sparse feature matcher designed to establish correspondences between local features extracted from two images.

In the ChandraMap context, the important architectural distinction is:

```text
Image
  ↓
Local Feature Extractor
  ↓
Keypoints + Descriptors
  ↓
LightGlue
  ↓
Matched Feature Pairs
```

LightGlue is therefore primarily a **matcher**, not a general-purpose image registration system.

The local feature extractor and matcher should be treated as separate experimental variables.

Examples of feature extractors include:

- SIFT
- ALIKED
- other future local feature extractors

Examples of matching strategies include:

- classical descriptor matching
- LightGlue
- other future learned matchers

The distinction is important because an observed improvement may come from:

- better feature extraction
- better feature matching
- better preprocessing
- better scale handling
- better geometric verification

rather than from the matcher alone.

---

# 5. Why LightGlue Is Relevant to ChandraMap

The current ChandraMap baseline uses a classical local correspondence pipeline.

A simplified baseline is:

```text
Source Image
     ↓
SIFT
     ↓
Descriptors
     ↓
Classical Descriptor Matching
     ↓
Candidate Matches
     ↓
RANSAC
     ↓
Geometric Inliers
     ↓
Transformation
```

This baseline is valuable because it is:

- understandable
- reproducible
- relatively simple
- easy to inspect
- suitable for controlled experiments
- a concrete number to beat

However, lunar imagery can create difficult correspondence conditions:

- large scale differences
- different illumination
- changing shadows
- different image resolutions
- different sensor characteristics
- different radiometric responses
- low-feature terrain
- repetitive terrain structures
- viewpoint differences
- geometric distortions

The research question is therefore whether a learned matcher can establish more useful correspondences when descriptor similarity alone becomes unreliable.

The source feedback specifically identifies **ALIKED + LightGlue** as a modern sparse matching path worth evaluating against the SIFT baseline, while emphasizing that pretrained terrestrial models are not automatically robust to lunar imagery.

This makes domain-specific evaluation essential.

---

# 6. Core Research Question

The central research question is:

> **Can a learned feature matcher such as LightGlue produce more reliable geometrically useful correspondences for lunar image registration than the current classical matching strategy under the conditions relevant to ChandraMap?**

This should be decomposed into measurable questions.

### 6.1 Correspondence quality

- Does LightGlue produce a higher-quality set of candidate correspondences?
- Does it reduce incorrect correspondences?
- Does it increase geometrically verified correspondences?
- Does it improve inlier ratio?

### 6.2 Registration quality

- Does LightGlue improve independent check-point RMSE?
- Does it improve spatial coverage?
- Does it reduce registration failures?
- Does it improve the stability of the estimated transformation?

### 6.3 Robustness

- How does LightGlue behave under scale stress?
- How does it behave under illumination stress?
- How does it behave on low-feature terrain?
- How does it behave under geometry stress?
- How does it behave across sensor modalities?

### 6.4 Feature-extractor dependence

- Does LightGlue work differently with SIFT and ALIKED?
- Does ALIKED + LightGlue provide a measurable benefit over SIFT + classical matching?
- Is any improvement caused primarily by ALIKED or by LightGlue?
- Does LightGlue remain useful when the input feature extractor changes?

### 6.5 Practicality

- What computational overhead does LightGlue introduce?
- What hardware is required?
- Is inference reproducible?
- Is runtime acceptable for the intended ChandraMap workflow?
- Does the method remain useful after accounting for preprocessing and feature-extraction costs?

---

# 7. Position in the ChandraMap Pipeline

The intended conceptual pipeline is:

```text
Input Images
     ↓
Image Representation / Preprocessing
     ↓
Local Feature Extraction
     ↓
Feature Descriptors
     ↓
LightGlue Matching
     ↓
Candidate Correspondences
     ↓
Geometric Verification
     ↓
Verified Inliers
     ↓
Transformation Estimation
     ↓
Residual / Check-Point Evaluation
     ↓
Optional Sub-Pixel Refinement
     ↓
Final Registration
```

The important boundary is:

```text
LightGlue
   ↓
Candidate Correspondences
   ↓
RANSAC / Geometric Verification
   ↓
Verified Inliers
```

A LightGlue match should **not** automatically be considered a geometrically correct correspondence.

The matcher produces candidates.

Geometric verification determines which candidates are consistent with the estimated transformation.

---

# 8. Candidate Matches vs Verified Inliers

ChandraMap must maintain a strict distinction between:

### Candidate correspondence

A pair of feature locations proposed by the matching stage.

### Verified inlier

A candidate correspondence that remains consistent with the selected geometric model during geometric verification.

The pipeline should therefore remain:

```text
Feature Extraction
        ↓
LightGlue
        ↓
Candidate Matches
        ↓
RANSAC + Initial Model
        ↓
Verified Inliers
        ↓
Sub-Pixel Refinement
        ↓
Refit Final Transform
```

A matcher confidence value is not a substitute for geometric verification.

This is particularly important for lunar imagery because visually plausible local matches can still be geometrically incorrect.

---

# 9. Feature Extraction and Matching Must Be Separated

LightGlue should not be evaluated as though it were a feature detector and descriptor simultaneously.

The following paths should be treated as distinct experimental configurations.

## Path A — Existing Baseline

```text
SIFT
  ↓
Classical Descriptor Matching
  ↓
Geometric Verification
```

Purpose:

- establish the baseline
- provide a number to beat
- maintain interpretability

---

## Path B — SIFT + LightGlue

```text
SIFT
  ↓
LightGlue
  ↓
Geometric Verification
```

Purpose:

- isolate the contribution of the matcher
- test whether LightGlue improves matching while keeping the feature extractor approximately fixed

This is an especially useful controlled experiment because it reduces the number of simultaneously changing components.

---

## Path C — ALIKED + LightGlue

```text
ALIKED
  ↓
LightGlue
  ↓
Geometric Verification
```

Purpose:

- evaluate a modern learned sparse feature pipeline
- investigate the combined effect of learned feature extraction and learned matching

This is a future research candidate, not a validated ChandraMap configuration.

---

## Path D — Other Learned Matchers

Examples may include:

- LoFTR
- other future learned correspondence methods

These should be evaluated separately rather than mixed into a single pipeline.

---

# 10. Primary Experimental Comparison

The first serious LightGlue experiment should avoid changing too many variables simultaneously.

A useful progression is:

```text
Experiment A
SIFT + Classical Matching
        ↓
Baseline

Experiment B
SIFT + LightGlue
        ↓
Matcher-only comparison

Experiment C
ALIKED + LightGlue
        ↓
Learned feature + learned matcher

Experiment D
Full Lunar-Aware Pipeline
        ↓
Preprocessing + scale handling + matching + verification
```

The experiments should use the **same benchmark pairs**, ground truth, evaluation procedure, and reporting metrics wherever possible.

This allows the contribution of each component to be measured.

---

# 11. Hypotheses

The following hypotheses should be treated as testable hypotheses rather than conclusions.

### H1 — Matching quality

LightGlue may produce a higher proportion of geometrically useful correspondences than classical descriptor matching on difficult image pairs.

### H2 — Scale robustness

LightGlue may improve correspondence robustness when the two images contain significant scale differences, provided the underlying features contain sufficient information.

### H3 — Illumination robustness

LightGlue may improve matching under some appearance changes, but different lunar Sun angles and shadow geometry may remain difficult.

### H4 — Feature-extractor interaction

The effectiveness of LightGlue may depend strongly on the feature extractor supplied to it.

### H5 — Learned pipeline benefit

ALIKED + LightGlue may outperform SIFT + classical matching on some difficult cases.

### H6 — Domain shift

Pretrained models developed using non-lunar imagery may exhibit degraded performance on lunar data.

### H7 — Computational trade-off

Any accuracy improvement must be evaluated together with feature extraction cost, matching cost, memory use, and overall runtime.

None of these hypotheses should be reported as findings until supported by ChandraMap experiments.

---

# 12. Benchmark Design

LightGlue must be evaluated on a controlled benchmark.

The benchmark should contain representative conditions rather than only easy image pairs.

Recommended categories are:

| Test Category       | Research Purpose                                     |
| ------------------- | ---------------------------------------------------- |
| Easy pair           | Establish basic end-to-end functionality             |
| Sun-angle stress    | Test robustness to changing shadows and illumination |
| Scale stress        | Test large effective resolution differences          |
| Modality stress     | Test different sensor/image representations          |
| Geometry stress     | Test stronger geometric differences                  |
| Low-feature terrain | Expose correspondence ambiguity                      |
| Repetitive terrain  | Test false-match susceptibility                      |

The same test pairs should be used for baseline and LightGlue comparisons whenever the experimental question requires a direct comparison.

---

# 13. Sensor-Specific Evaluation

ChandraMap should not treat OHRC, TMC-2, and IIRS as identical image sources.

## 13.1 OHRC

OHRC provides high-detail visible imagery suitable for fine terrain correspondence.

Research should investigate:

- keypoint repeatability
- local descriptor quality
- scale differences against the reference
- illumination changes
- fine geometric registration

The actual product metadata should remain authoritative for pixel scale.

---

## 13.2 TMC-2

TMC-2 provides a different spatial scale and sensor context from OHRC.

Research should investigate:

- larger-scale terrain structures
- scale differences
- map-projection effects
- illumination differences
- geometric consistency
- spatial coverage of verified matches

---

## 13.3 IIRS

IIRS requires special treatment because it is hyperspectral/infrared data rather than a conventional single-band visible image.

LightGlue should not simply be given an unexamined hyperspectral cube as if it were an ordinary 2D image.

A future experiment should first establish a documented 2D representation, potentially involving:

- selected spectral bands
- PCA/composite representation
- structural representation
- another experimentally justified representation

The representation itself becomes an experimental variable.

A LightGlue result on IIRS therefore cannot be interpreted independently of the chosen IIRS representation.

---

# 14. Scale Handling

LightGlue should not be expected to solve physically impossible scale differences by itself.

A fundamental ChandraMap principle is:

> **Upsampling changes pixel count; it does not recover missing spatial information.**

The benchmark should therefore distinguish:

- image resizing
- effective ground scale
- physical spatial resolution
- available terrain detail

For large resolution differences, the research pipeline should investigate:

```text
High-resolution reference
        ↓
Reference pyramid / downsampling
        ↓
Comparable effective scale
        ↓
Local matching
        ↓
Fine registration where information supports it
```

The goal is to determine whether LightGlue improves correspondence under meaningful scale conditions, not whether arbitrary image resizing makes the images numerically similar.

---

# 15. Illumination Handling

Lunar illumination is more complicated than simple brightness variation.

A different Sun angle can change:

- shadow position
- shadow length
- local contrast
- apparent crater structure
- visibility of terrain boundaries

Therefore:

```text
Brightness normalization
        ≠
Physical illumination invariance
```

LightGlue experiments should compare conditions such as:

- similar illumination
- moderately different illumination
- strongly different illumination

Possible representations may include:

- grayscale
- normalized intensity
- gradient representation
- edge representation
- other structure-focused representations

The representation should be treated as an experimental variable.

---

# 16. Geometric Verification

LightGlue must be followed by geometric verification.

A candidate pipeline is:

```text
LightGlue Matches
       ↓
Candidate Correspondences
       ↓
RANSAC
       ↓
Initial Geometric Model
       ↓
Verified Inliers
       ↓
Residual Analysis
       ↓
Optional Sub-Pixel Refinement
       ↓
Refit Final Transform
```

Potential initial models include:

- affine
- homography

The simplest model that adequately explains the measured residual structure should be preferred.

A homography should not automatically be interpreted as a physically complete lunar model.

The Moon is not a flat planar object, and residuals can vary because of:

- relief
- viewpoint differences
- sensor geometry
- map-projection differences
- local terrain effects

---

# 17. Residual Analysis

Residual analysis is essential for evaluating LightGlue.

For a transformation

$$
T: p_i^s \rightarrow p_i^r
$$

where:

- \(p_i^s\) is a source-image point
- \(p_i^r\) is the corresponding reference-image point

the residual vector can be represented as:

$$
r_i = p_i^r - T(p_i^s)
$$

with residual magnitude:

$$
e_i = \|r_i\|
$$

The registration error can then be summarized using RMSE:

$$
RMSE =
\sqrt{
\frac{1}{N}
\sum_{i=1}^{N}
e_i^2
}
$$

The exact evaluation protocol must follow the ChandraMap benchmark and ground-truth definitions.

Residual vectors should be visualized or inspected spatially.

For example:

```text
Uniform small residuals
        ↓
Potentially consistent model

Systematic residual drift
        ↓
Possible geometry/model limitation

Clustered low residuals
        ↓
Potentially insufficient spatial coverage

Large scattered residuals
        ↓
Possible correspondence or model failure
```

---

# 18. Independent Check Points

A transformation must not be fitted and evaluated on exactly the same points when the objective is to measure independent registration accuracy.

The experiment should distinguish:

```text
Correspondence points
        ↓
Geometric fitting
        ↓
Final transformation
```

from:

```text
Independent check points
        ↓
Accuracy evaluation
```

The preferred source of truth is the project benchmark ground truth when available.

Otherwise, independently checked points should be held out from transformation fitting.

This is necessary because fitting error can underestimate actual registration error.

---

# 19. Metrics

LightGlue should be evaluated using the same measurable metrics used by the ChandraMap benchmark.

## 19.1 Candidate match count

Number of correspondences returned by the matching stage.

This is descriptive only.

More matches do not automatically mean better registration.

---

## 19.2 Verified inlier count

Number of candidate matches that survive geometric verification.

This is more informative than raw candidate count.

---

## 19.3 Inlier ratio

$$
InlierRatio =
\frac{N_{inliers}}
{N_{candidate}}
$$

This measures the fraction of candidate correspondences consistent with the estimated geometric model.

---

## 19.4 Spatial coverage

A good correspondence set should cover the overlap rather than cluster around a single feature.

Possible measurements include:

- grid coverage
- convex-hull coverage
- spatial distribution statistics

The benchmark should use one documented definition consistently.

---

## 19.5 Independent check-point RMSE

Primary registration accuracy should be measured using independent points.

Source-image pixels should remain the primary unit where appropriate.

Ground error in metres should only be reported when:

- GSD is known
- projection is meaningful
- reference geometry is appropriate
- ground truth supports the conversion

---

## 19.6 Failure rate

Measure how often the pipeline fails to produce a usable registration.

A failure should be defined before experiments begin.

Possible failure conditions may include:

- insufficient verified correspondences
- degenerate geometric estimation
- unacceptable independent check-point error
- insufficient spatial coverage
- no valid transformation

The exact criterion should be registered in the benchmark configuration rather than changed after seeing results.

---

## 19.7 Runtime

Report runtime separately for relevant stages where practical:

- preprocessing
- feature extraction
- matching
- geometric verification
- refinement
- complete registration

Hardware and software configuration must also be recorded.

---

# 20. Metrics That Must Not Be Used Alone

The following should not be used as evidence of successful lunar registration by themselves:

- matcher confidence
- number of candidate matches
- visual attractiveness of an overlay
- a single example image
- a percentage score without a defined metric
- a fitted-point RMSE without independent evaluation
- computational speed without accuracy
- accuracy on only easy pairs

The project should prefer measured evidence over decorative confidence scores.

---

# 21. Controlled Experiment Matrix

A useful first matrix is:

| Experiment         | Feature Extractor              | Matcher           | Purpose                          |
| ------------------ | ------------------------------ | ----------------- | -------------------------------- |
| Baseline           | SIFT                           | Classical         | Existing reference               |
| Matcher study      | SIFT                           | LightGlue         | Isolate matcher contribution     |
| Learned pipeline   | ALIKED                         | LightGlue         | Evaluate learned sparse pipeline |
| Scale study        | Same extractor                 | Same matcher      | Test scale strategy              |
| Illumination study | Same extractor                 | Same matcher      | Test illumination stress         |
| Sensor study       | Sensor-specific representation | LightGlue         | Test cross-sensor behavior       |
| Geometry study     | Same correspondences           | Same verification | Study residual/model behavior    |

Only one major research variable should change at a time when the experiment is intended to isolate a component.

---

# 22. Recommended First LightGlue Experiment

The first experiment should remain deliberately small.

### Objective

Determine whether LightGlue provides measurable improvement over the existing classical matcher on a known source/reference benchmark.

### Configuration

```text
Known source/reference pair
        ↓
Common preprocessing
        ↓
SIFT
        ↓
        ├── Classical Matching
        │
        └── LightGlue
        ↓
Same geometric verification
        ↓
Same transformation estimation
        ↓
Same independent check points
        ↓
Same metrics
```

This experiment isolates the matcher as much as possible.

### Required outputs

- candidate match count
- verified inlier count
- inlier ratio
- spatial coverage
- independent check-point RMSE
- runtime
- success/failure status
- residual visualization
- registered overlay

No production decision should be made from one image pair.

---

# 23. ALIKED + LightGlue Research

ALIKED and LightGlue should be treated as a coupled learned local-feature path.

Conceptually:

```text
Image
  ↓
ALIKED
  ↓
Learned Keypoints + Descriptors
  ↓
LightGlue
  ↓
Candidate Correspondences
  ↓
Geometric Verification
```

This path is especially relevant because the project feedback identifies ALIKED + LightGlue as a modern sparse pipeline worth testing.

However, a strong result from ALIKED + LightGlue does not establish that LightGlue alone caused the improvement.

To separate contributions, ChandraMap should consider comparisons such as:

```text
SIFT + Classical Matcher
SIFT + LightGlue
ALIKED + LightGlue
```

Where technically meaningful and supported by the implementation, additional combinations can be investigated.

---

# 24. Relationship to ALIKED.md

`research/future/ALIKED.md` should describe ALIKED as a future local feature extraction direction.

This document focuses specifically on LightGlue as the matching component.

The conceptual separation is:

```text
ALIKED
Feature extraction
        ↓
LightGlue
Feature matching
        ↓
Geometric verification
```

This separation should be maintained in documentation, experiments, benchmark tables, and future implementation.

If ALIKED + LightGlue is eventually evaluated as one pipeline, the experiment should still identify the individual roles of the two methods.

---

# 25. Relationship to LoFTR and Other Matchers

LightGlue should not become the default learned matcher simply because it is a well-known method.

Other research directions include:

- LoFTR
- RIFT
- CFOG
- future lunar-specific methods

These methods solve related but not identical problems.

For example:

```text
SIFT
Sparse detected features
        ↓
Descriptor matching

ALIKED + LightGlue
Sparse learned features
        ↓
Learned sparse matching

LoFTR
Image pair
        ↓
Detector-free correspondence estimation
```

The benchmark should compare methods according to the actual research question.

There is no requirement to place every method into one pipeline.

---

# 26. Lunar Domain Shift

One of the most important risks is domain shift.

A pretrained learned matcher may have been developed and evaluated using imagery substantially different from ChandraMap's target imagery.

Differences can include:

- lunar terrain versus terrestrial scenes
- illumination behavior
- crater morphology
- sensor characteristics
- spatial resolution
- radiometric response
- image statistics
- viewpoint geometry

Therefore:

> **Pretrained performance on terrestrial benchmarks must not be interpreted as evidence of lunar robustness.**

The relevant evidence must come from lunar benchmark data.

---

# 27. Failure Modes to Investigate

LightGlue research should explicitly record failure cases.

## 27.1 Low-texture terrain

Possible issue:

- insufficient stable local features

Investigate:

- number of detected features
- candidate correspondences
- spatial coverage
- geometric stability

---

## 27.2 Repetitive terrain

Possible issue:

- multiple visually similar local structures

Investigate:

- false correspondence patterns
- geometric consistency
- spatial clustering

---

## 27.3 Strong illumination difference

Possible issue:

- shadow boundaries and local appearance change

Investigate:

- correspondence stability
- inlier ratio
- residual distribution

---

## 27.4 Large scale difference

Possible issue:

- feature representation lacks comparable physical detail

Investigate:

- effective image scale
- pyramid level
- feature count
- spatial coverage
- registration error

---

## 27.5 Cross-sensor modality

Possible issue:

- visible and infrared/spectral representations may not produce equivalent local appearance

Investigate:

- selected IIRS representation
- structural representation
- match quality
- geometric consistency

---

## 27.6 Geometric distortion

Possible issue:

- a single global transformation may not adequately explain correspondence

Investigate:

- residual vectors
- spatial residual trends
- transformation model
- map projection
- available sensor geometry
- DEM information where appropriate

---

## 27.7 False confidence

Possible issue:

- high matcher confidence but incorrect geometry

Investigate:

- confidence versus verified-inlier status
- independent check-point error
- residual distribution

---

# 28. More Matches Are Not Necessarily Better

A major evaluation rule is:

> **More correspondences are useful only when they are correct and spatially informative.**

For example:

```text
100 candidate matches
20 verified inliers
clustered in one crater
```

may be less useful for registration than:

```text
40 candidate matches
30 verified inliers
distributed across the overlap
```

Therefore, LightGlue should be evaluated using:

- inlier ratio
- inlier count
- spatial coverage
- independent registration error

rather than candidate count alone.

---

# 29. Spatial Distribution

Correspondences should be inspected spatially.

A useful diagnostic is:

```text
Source image
+-----------------------+
| *        *            |
|                       |
|       *       *       |
|                       |
|  *             *      |
|                       |
|       *        *      |
+-----------------------+

Reference image
+-----------------------+
| *        *            |
|                       |
|       *       *       |
|                       |
|  *             *      |
|                       |
|       *        *      |
+-----------------------+
```

The purpose is not to maximize the number of points.

The purpose is to establish enough reliable, distributed control points to estimate and evaluate the registration geometry.

---

# 30. Sub-Pixel Refinement

LightGlue does not remove the need for downstream sub-pixel refinement.

The future pipeline may be:

```text
LightGlue
   ↓
Candidate Matches
   ↓
RANSAC
   ↓
Verified Inliers
   ↓
Sub-Pixel Tie-Point Refinement
   ↓
Refit Final Transform
   ↓
Independent Evaluation
```

Sub-pixel refinement should operate on verified correspondences rather than blindly refining all candidates.

Potential refinement approaches are a separate research topic and should not be conflated with LightGlue itself.

---

# 31. Transformation Models

LightGlue produces correspondences, not the final physical registration model.

The downstream geometry may use:

- affine transformation
- homography
- another validated geometric model
- future local or sensor-aware geometry

The model should be selected based on residual evidence and the properties of the image products.

A flexible warp should not be used merely to make the visual overlay look better.

If a flexible model substantially improves visual appearance but the underlying control points remain unreliable, the correspondence problem has not been solved.

---

# 32. Ground Truth Requirements

LightGlue experiments require a documented ground-truth protocol.

Ground truth should support evaluation of:

- correspondence correctness
- geometric transformation
- independent check points
- registration error
- spatial coverage where applicable

The experiment should record:

- ground-truth source
- ground-truth representation
- point selection method
- fitting points
- independent check points
- evaluation coordinate system
- uncertainty or limitations where known

Ground truth must remain independent from the method being evaluated.

---

# 33. Reproducibility Requirements

A LightGlue experiment should record enough information for another researcher to reproduce the result.

At minimum, document:

### Data

- source image identifier
- reference image identifier
- sensor
- product type
- image dimensions
- pixel scale/GSD when available
- projection information when available
- illumination/viewing metadata when available

### Feature extraction

- extractor
- extractor configuration
- number of keypoints
- preprocessing
- image scale
- pyramid level

### Matching

- matcher
- model/version
- configuration
- confidence/filter settings
- hardware

### Geometry

- geometric model
- RANSAC configuration
- inlier definition
- refinement procedure

### Evaluation

- ground-truth version
- independent check-point set
- metric definitions
- failure criteria

### Runtime

- CPU/GPU
- software environment
- total runtime
- stage-level runtime where available

---

# 34. Experimental Controls

To isolate LightGlue's contribution, experiments should control as many variables as possible.

For example:

```text
Same images
Same preprocessing
Same keypoints
Same descriptors
Different matcher
```

This is the cleanest matcher comparison.

A second experiment can then compare:

```text
SIFT + Classical
        vs
ALIKED + LightGlue
```

but this comparison changes both:

- feature extractor
- matcher

Therefore, it answers a different research question.

The experiment documentation must state which question is being answered.

---

# 35. Ablation Strategy

Potential ablations include:

### A. Matcher ablation

```text
SIFT + Classical
vs
SIFT + LightGlue
```

Question:

> Does the learned matcher improve matching?

### B. Feature extractor ablation

```text
SIFT + LightGlue
vs
ALIKED + LightGlue
```

Question:

> How much does the feature extractor influence the learned matching pipeline?

### C. Scale ablation

```text
Native scale
vs
Reference pyramid
```

Question:

> Does physically meaningful scale handling improve LightGlue correspondence?

### D. Representation ablation

```text
Intensity
vs
Gradient / structural representation
```

Question:

> Does a structure-focused representation improve difficult illumination cases?

### E. Geometry ablation

```text
Affine
vs
Homography
```

Question:

> Which model adequately explains the observed residuals?

---

# 36. Stress-Test Evaluation

A LightGlue experiment should not report only an average.

A useful result table should separate conditions.

| Condition   | Candidate Matches | Inliers | Inlier Ratio | Coverage | Check-Point RMSE | Runtime | Failure |
| ----------- | ----------------: | ------: | -----------: | -------: | ---------------: | ------: | ------- |
| Easy        |               TBD |     TBD |          TBD |      TBD |              TBD |     TBD | TBD     |
| Sun-angle   |               TBD |     TBD |          TBD |      TBD |              TBD |     TBD | TBD     |
| Scale       |               TBD |     TBD |          TBD |      TBD |              TBD |     TBD | TBD     |
| Modality    |               TBD |     TBD |          TBD |      TBD |              TBD |     TBD | TBD     |
| Geometry    |               TBD |     TBD |          TBD |      TBD |              TBD |     TBD | TBD     |
| Low-feature |               TBD |     TBD |          TBD |      TBD |              TBD |     TBD | TBD     |

All `TBD` values should remain unfilled until measured.

---

# 37. Baseline Comparison Table

The eventual benchmark should contain a comparison such as:

| Pipeline         | Feature Extractor | Matcher   | Geometric Verification | Check-Point RMSE | Inlier Ratio | Coverage | Runtime | Failure Rate |
| ---------------- | ----------------- | --------- | ---------------------- | ---------------: | -----------: | -------: | ------: | -----------: |
| Baseline         | SIFT              | Classical | Yes                    |              TBD |          TBD |      TBD |     TBD |          TBD |
| Matcher Study    | SIFT              | LightGlue | Yes                    |              TBD |          TBD |      TBD |     TBD |          TBD |
| Learned Sparse   | ALIKED            | LightGlue | Yes                    |              TBD |          TBD |      TBD |     TBD |          TBD |
| Future Candidate | TBD               | TBD       | Yes                    |              TBD |          TBD |      TBD |     TBD |          TBD |

This table should not be populated with estimated or expected values.

---

# 38. What Would Count as Evidence of Improvement?

A useful LightGlue result should demonstrate improvement across meaningful metrics rather than only one number.

Strong evidence would involve some combination of:

- higher verified inlier ratio
- sufficient or improved spatial coverage
- lower independent check-point RMSE
- lower failure rate
- stable behavior across multiple image pairs
- improvement on difficult stress categories
- acceptable runtime
- reproducible results

An increase in candidate matches alone is not sufficient.

A visually better overlay alone is not sufficient.

A lower training or inference cost alone is not sufficient.

---

# 39. What Would Not Justify Adoption?

LightGlue should not be promoted based on:

- one successful image pair
- a visual demo without metrics
- terrestrial benchmark performance
- a high matcher confidence score
- more raw matches
- an unverified claim of lunar invariance
- a fitted-point-only RMSE
- an improvement caused by changing several pipeline components simultaneously
- results without reproducible configuration
- results on an undocumented dataset
- results without failure analysis

---

# 40. Promotion Criteria

LightGlue should progress through the research lifecycle:

```text
Research Idea
     ↓
Controlled Prototype
     ↓
Small Lunar Experiment
     ↓
Benchmark Evaluation
     ↓
Stress Testing
     ↓
Independent Accuracy Evaluation
     ↓
Failure Analysis
     ↓
Reproducibility Check
     ↓
Research Finding
     ↓
Candidate Integration
     ↓
Future ChandraMap Version
```

Promotion should require documented evidence.

A useful decision structure is:

| Research Outcome                              | Action                         |
| --------------------------------------------- | ------------------------------ |
| Clear improvement                             | Continue validation            |
| Improvement only on specific stress cases     | Keep as conditional candidate  |
| Similar accuracy with useful runtime benefits | Investigate practical role     |
| Higher accuracy but unacceptable cost         | Investigate optimization       |
| No meaningful improvement                     | Do not promote                 |
| Worse performance                             | Record failure and investigate |
| Inconclusive                                  | Collect more evidence          |

The decision should be based on benchmark evidence rather than the reputation of the method.

---

# 41. Possible Future Integration

If LightGlue demonstrates a reproducible benefit, a future ChandraMap architecture could contain:

```text
Sensor Input
     ↓
Sensor-Aware Representation
     ↓
Scale-Aware Preparation
     ↓
Local Feature Extraction
     ↓
LightGlue
     ↓
Candidate Correspondences
     ↓
Geometric Verification
     ↓
Residual Analysis
     ↓
Sub-Pixel Refinement
     ↓
Final Transformation
     ↓
Independent Evaluation
```

LightGlue would still be only one component of the complete system.

It would not replace:

- sensor-aware preprocessing
- scale handling
- geometric verification
- residual analysis
- ground truth
- independent evaluation
- registration refinement

---

# 42. Potential Future Version Role

LightGlue could become part of a future ChandraMap version only after validation.

A possible progression is:

```text
V1
Classical measurable baseline
        ↓
Future research
LightGlue experiments
        ↓
Measured evidence
        ↓
Validated learned matching component
        ↓
Future ChandraMap version
```

The exact version assignment should be decided after the benchmark results are available.

This document must therefore remain a **future research specification**, not a promise of future implementation.

---

# 43. Computational Considerations

LightGlue introduces learned-model computation into the correspondence pipeline.

Runtime should therefore be measured rather than assumed.

The evaluation should consider:

- feature extraction time
- matcher time
- GPU availability
- CPU fallback
- memory requirements
- number of keypoints
- image dimensions
- batch behavior if applicable
- preprocessing overhead
- total end-to-end registration time

A matcher that improves accuracy but requires disproportionate computation may have a different role from a matcher that improves accuracy while maintaining practical runtime.

The comparison should therefore report both:

```text
Accuracy
+
Robustness
+
Coverage
+
Runtime
```

rather than treating accuracy as the only system metric.

---

# 44. Retrieval Interaction

Global retrieval and local matching are separate tasks.

If ChandraMap eventually performs global retrieval:

```text
Source Image
     ↓
Global Retrieval
     ↓
Top-K Candidate Regions
     ↓
Local Feature Extraction
     ↓
LightGlue
     ↓
Geometric Verification
```

LightGlue should operate after candidate-region retrieval.

If reliable metadata such as:

- footprint
- latitude/longitude
- map projection
- other geographic constraints

already restricts the search, global retrieval may not be necessary.

LightGlue should therefore not be used as a substitute for geographic information that is already available and reliable.

---

# 45. Expected Failure Analysis

Every LightGlue experiment should produce a failure-analysis section.

For failed pairs, record the likely failure category:

- insufficient features
- incorrect correspondences
- spatial clustering
- scale mismatch
- illumination difference
- sensor modality difference
- geometric distortion
- insufficient overlap
- weak ground truth
- preprocessing failure
- runtime or resource failure

The purpose is not merely to report that a pair failed.

The purpose is to determine **why** it failed.

---

# 46. Research Questions After the First Experiment

If the initial LightGlue experiment is promising, subsequent research can investigate:

### Question 1

Does LightGlue consistently improve the hard cases rather than only easy pairs?

### Question 2

Does ALIKED + LightGlue outperform SIFT + LightGlue?

### Question 3

Does LightGlue benefit from the reference-image scale pyramid?

### Question 4

Does structure-focused preprocessing improve learned matching under different Sun angles?

### Question 5

Does performance vary significantly between OHRC and TMC-2?

### Question 6

Can a carefully designed IIRS representation produce useful sparse correspondences?

### Question 7

Are LightGlue confidence values correlated with geometric correctness?

### Question 8

Does LightGlue improve registration accuracy after independent check-point evaluation?

### Question 9

Does it maintain sufficient spatial coverage?

### Question 10

What computational cost is introduced by the learned matcher?

---

# 47. Research Limitations

The following limitations must remain explicit.

### 47.1 No assumption of lunar invariance

A pretrained learned model should not be assumed to be invariant to lunar conditions.

### 47.2 No assumption of cross-sensor robustness

Performance on one sensor pair does not establish performance across all sensors.

### 47.3 No assumption of scale sufficiency

A learned matcher cannot create terrain detail that is absent from a lower-resolution sensor.

### 47.4 No assumption of geometric correctness

Matcher output must still undergo geometric verification.

### 47.5 No assumption of physical accuracy

A numerically precise correspondence does not automatically imply physically meaningful sub-pixel accuracy.

### 47.6 No assumption of production readiness

Research success does not automatically imply integration into the main ChandraMap pipeline.

---

# 48. Reproducible Research Record

A future LightGlue experiment should record:

```text
Experiment ID:
Date:
Dataset version:
Source image(s):
Reference image(s):
Sensor:
Image representation:
Preprocessing:
Effective scale:
Feature extractor:
Feature extractor configuration:
Matcher:
Matcher configuration:
Number of keypoints:
Candidate matches:
Verified inliers:
Inlier ratio:
Spatial coverage:
Transformation model:
Check-point RMSE:
Ground error:
Runtime:
Hardware:
Software environment:
Failure status:
Failure reason:
Residual analysis:
Notes:
```

The exact experiment schema should follow the project's established experiment template.

---

# 49. Expected Artifacts

A validated experiment should preserve appropriate research evidence, including:

- experiment configuration
- input-pair identifiers
- feature statistics
- candidate correspondences
- verified inliers
- transformation parameters
- residual statistics
- independent check-point results
- spatial coverage results
- runtime measurements
- failure records
- registered overlays
- match visualizations
- benchmark tables
- environment information

The exact storage structure should follow the repository's established experiment conventions.

---

# 50. Scientific Interpretation

LightGlue should be interpreted as a **correspondence-generation component**.

The complete scientific chain is:

```text
Feature Representation
        ↓
Feature Extraction
        ↓
Feature Matching
        ↓
Geometric Verification
        ↓
Transformation Estimation
        ↓
Independent Accuracy Evaluation
```

An improvement at the matching stage matters only if it survives the later stages.

For example:

```text
More matches
     ↓
But poor geometric consistency
     ↓
No registration improvement
```

or:

```text
Fewer matches
     ↓
Higher inlier ratio
     ↓
Better spatial coverage
     ↓
Lower check-point RMSE
```

The second result may be more scientifically useful even though it produces fewer candidate correspondences.

---

# 51. Decision Framework

After sufficient experiments, LightGlue should be placed into one of four research states.

## State A — Promote

Evidence shows a reproducible and practically useful improvement.

Possible next step:

- controlled integration into a future ChandraMap version

## State B — Conditional Use

LightGlue helps specific conditions.

Possible next step:

- sensor-specific or stress-case-specific use

## State C — Research Only

Results are interesting but insufficient for integration.

Possible next step:

- additional experiments
- lunar-specific training research
- representation research

## State D — Reject or Defer

Evidence does not justify continued integration effort.

The negative result should still be documented because it is valuable research evidence.

---

# 52. Recommended Research Sequence

The preferred sequence is:

```text
1. Lock benchmark pairs
        ↓
2. Run existing SIFT baseline
        ↓
3. Add SIFT + LightGlue
        ↓
4. Keep geometry identical
        ↓
5. Measure independent registration quality
        ↓
6. Analyze failure cases
        ↓
7. Test ALIKED + LightGlue
        ↓
8. Evaluate scale stress
        ↓
9. Evaluate illumination stress
        ↓
10. Evaluate sensor-specific cases
        ↓
11. Evaluate runtime
        ↓
12. Reproduce the strongest findings
        ↓
13. Decide whether to promote
```

This sequence minimizes unnecessary engineering before the matcher has demonstrated measurable value.

---

# 53. Relationship to the V1 Research Foundation

LightGlue should build on, rather than bypass, the V1 foundation.

The research progression is:

```text
V1 Classical Baseline
        ↓
Controlled Experiments
        ↓
Measured Failure Modes
        ↓
Learned Matching Research
        ↓
LightGlue Evaluation
        ↓
Benchmark Comparison
        ↓
Potential Future Version
```

The existing V1 work remains important because it provides:

- a baseline
- a benchmark structure
- geometric verification
- residual analysis
- ground-truth methodology
- sub-pixel evaluation
- reproducibility requirements

Without the baseline, it would be difficult to determine whether LightGlue actually improves ChandraMap.

---

# 54. Relationship to Existing Experiments

LightGlue research should connect to the established experiment structure.

Relevant existing research includes:

- `experiments/v1/README.md`
- `experiments/templates/EXPERIMENT_TEMPLATE.md`
- `experiments/v1/baseline/EXP-001-sift-baseline/README.md`
- `experiments/v1/preprocessing/EXP-002-scale-pyramid/README.md`
- `experiments/v1/preprocessing/EXP-003-gradient-representation/README.md`
- `experiments/v1/geometry/EXP-004-affine-vs-homography/README.md`
- `experiments/v1/geometry/EXP-005-residual-analysis/README.md`
- `experiments/v1/refinement/EXP-006-subpixel-refinement/README.md`

LightGlue should be evaluated within the same scientific discipline:

```text
Experiment
    ↓
Controlled variables
    ↓
Measured outputs
    ↓
Failure analysis
    ↓
Conclusion
```

It should not be added merely as another software dependency.

---

# 55. Relationship to Research Notes

LightGlue research should remain consistent with:

- `research/notes/lunar-registration.md`
- `research/notes/illumination-invariance.md`
- `research/notes/scale-invariance.md`
- `research/notes/ground-truth-design.md`

These documents establish the broader scientific context around:

- registration
- illumination
- scale
- ground truth
- evaluation

`research/future/LIGHTGLUE.md` narrows that context to one specific future matching direction.

---

# 56. Relationship to Future Research

This document is part of the broader future-research layer.

Conceptually:

```text
Current Research
      ↓
V1 Experiments
      ↓
Measured Evidence
      ↓
Research Findings
      ↓
Future Research
      ↓
New Experiments
      ↓
Validation
      ↓
Future ChandraMap Version
```

LightGlue is currently located at:

```text
Future Research
```

It should move toward:

```text
Validated Research Finding
```

only after controlled lunar experiments.

---

# 57. What Must Remain Future Work

Until experimentally validated, the following must remain future work:

- LightGlue integration into the production pipeline
- claims of lunar-specific robustness
- claims of illumination invariance
- claims of cross-sensor robustness
- claims of superior accuracy
- claims of improved sub-pixel registration
- claims of reduced failure rate
- claims of production-level runtime
- lunar-specific LightGlue training
- automatic matcher selection
- sensor-specific learned matcher routing
- large-scale deployment
- replacement of the SIFT baseline

These should not be presented as completed capabilities.

---

# 58. Final Research Position

LightGlue is a credible future research direction for ChandraMap because it directly addresses the local feature matching stage of the correspondence pipeline.

However, its value must be established empirically.

The correct research sequence is:

```text
SIFT Baseline
      ↓
Controlled LightGlue Comparison
      ↓
Geometric Verification
      ↓
Independent Check-Point Evaluation
      ↓
Stress Testing
      ↓
Failure Analysis
      ↓
ALIKED + LightGlue Evaluation
      ↓
Sensor-Specific Evaluation
      ↓
Runtime / Reproducibility Assessment
      ↓
Promotion Decision
```

The central principle remains:

> **Do not assume that a learned matcher is better because it is learned. Measure whether it produces better lunar registration evidence.**

A successful LightGlue experiment should ultimately answer:

> **Does LightGlue produce more reliable, better-distributed, geometrically consistent correspondences that lead to measurable improvements in independent lunar image registration accuracy under ChandraMap's benchmark conditions?**

Until the benchmark provides that evidence, LightGlue remains a **future research candidate**, not a validated ChandraMap component.
