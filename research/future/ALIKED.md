# ALIKED Research

> **Research area:** Learned local feature detection and description for lunar image correspondence
> **Repository:** ChandraMap
> **Path:** `research/future/ALIKED.md`
> **Status:** Proposed future research
> **Experiment identity:** `ALIKED-EXP-001`
> **Implementation status:** `[Not implemented]`
> **Validation status:** `[Not evaluated]`

---

## 1. Purpose

This document specifies a future research investigation of **ALIKED** as a potential learned local feature detector and descriptor for ChandraMap.

The purpose is to determine whether ALIKED can provide more useful, repeatable, and geometrically consistent local features for lunar image correspondence than the existing classical SIFT baseline under conditions relevant to ChandraMap.

This document is a **research specification**, not an implementation report.

It does **not** claim that:

- ALIKED is currently implemented in ChandraMap.
- ALIKED has been validated on the project's lunar imagery.
- ALIKED is more accurate than SIFT for ChandraMap.
- ALIKED is more robust to lunar illumination changes.
- ALIKED solves cross-sensor correspondence.
- ALIKED should replace SIFT.
- ALIKED + LightGlue is the final matching architecture.

All such conclusions require controlled experiments and independent evaluation.

---

## 2. Research Question

The primary research question is:

> **Can ALIKED provide more repeatable and geometrically useful local features for lunar image correspondence than the existing classical baseline under the conditions relevant to ChandraMap?**

The question should be evaluated experimentally rather than answered from ALIKED's performance on unrelated datasets.

The investigation should consider both:

1. **feature-level behavior**, and
2. **registration-level behavior**.

A feature detector that produces many keypoints is not necessarily useful for lunar registration.

The important question is whether the resulting features lead to:

- reliable correspondences,
- spatially distributed geometric support,
- stable transformations,
- lower independent registration error,
- and reproducible performance across relevant image conditions.

---

## 3. ChandraMap Context

ChandraMap is a lunar image correspondence and registration research system focused on aligning imagery of the same lunar region acquired under different:

- instruments,
- resolutions,
- illumination conditions,
- viewing conditions,
- and potentially different sensing modalities.

Relevant project imagery includes:

- Chandrayaan-2 OHRC
- Chandrayaan-2 TMC-2
- Chandrayaan-2 IIRS

The V1 research foundation follows a controlled progression:

```text
Known Source / Reference Pair
            ↓
SIFT Baseline
            ↓
Scale Handling
            ↓
Structure / Gradient Representation
            ↓
Local Correspondence
            ↓
Geometric Verification
            ↓
Transformation
            ↓
Residual Analysis
            ↓
Sub-pixel Refinement
            ↓
Independent Evaluation
```

ALIKED belongs to a later research direction in which the **local feature extraction stage** is replaced or augmented by a learned method while keeping the rest of the evaluation framework controlled.

---

## 4. Scientific Position

The roles of the relevant methods should remain separate.

| Method    | ChandraMap role                                                         |
| --------- | ----------------------------------------------------------------------- |
| SIFT      | Existing classical baseline                                             |
| ALIKED    | Future learned local-feature research candidate                         |
| LightGlue | Potential future learned feature matcher                                |
| LoFTR     | Potential future detector-free / dense learned correspondence direction |

The distinction is important because changing the detector, descriptor, matcher, geometric model, and refinement stage simultaneously makes it difficult to determine which component caused an observed improvement or degradation.

The first ALIKED experiment should therefore isolate the contribution of the **feature extraction stage** as much as practical.

---

## 5. What Is ALIKED?

ALIKED stands for **A Lighter Keypoint and Descriptor Extraction Network via Deformable Transformation**.

ALIKED is a learned local feature method designed to detect keypoints and produce local descriptors. The published work describes ALIKED as an evolution of ALIKE and introduces a Sparse Deformable Descriptor Head intended to efficiently extract deformable descriptors. ([GitHub][1])

At a research-system level, ALIKED can be viewed as providing:

```text
Input Image
    ↓
Learned Feature Processing
    ↓
Keypoint Detection / Localization
    ↓
Local Descriptor Extraction
    ↓
Keypoints + Descriptors
    ↓
Feature Matching
    ↓
Geometric Verification
```

The important ChandraMap question is not whether ALIKED is a modern learned feature method, but whether its learned representation is useful for the specific appearance and geometric challenges encountered in lunar imagery.

---

## 6. Why Investigate Learned Local Features?

The existing SIFT baseline provides an essential classical reference.

However, ChandraMap involves image conditions that may be difficult for hand-crafted local descriptors, including:

- substantial scale differences,
- illumination changes,
- shadow changes,
- contrast variation,
- weak local texture,
- repetitive terrain,
- cross-sensor appearance differences,
- different spatial resolutions,
- structural changes in local image appearance.

These are **research motivations**, not evidence that SIFT fails or that ALIKED succeeds.

A learned feature method may potentially learn representations that differ from hand-crafted gradient-based descriptors.

The relevant hypothesis is therefore:

> A learned local representation may provide useful correspondence behavior under some lunar imaging conditions that are difficult for the existing classical baseline.

This must be tested against the same benchmark and evaluation procedure.

---

## 7. Potential Advantages to Investigate

Potential research advantages include:

### 7.1 Learned representations

ALIKED learns feature representations rather than relying exclusively on a hand-crafted descriptor formulation.

The investigation should determine whether this learned representation transfers effectively to lunar imagery.

### 7.2 Keypoint repeatability

A useful local feature should be detectable at corresponding physical locations across relevant image conditions.

The experiment should measure repeatability rather than assume it.

### 7.3 Descriptor distinctiveness

A descriptor should distinguish corresponding local structures from nearby or repetitive structures.

This is particularly important for lunar terrain containing:

- repeated crater structures,
- similar slopes,
- repetitive texture,
- shadow boundaries,
- low-texture areas.

### 7.4 Computational efficiency

ALIKED was designed as a lightweight learned keypoint and descriptor extraction method. The original project describes it as an improvement over ALIKE with a more efficient deformable descriptor approach. ([GitHub][1])

ChandraMap must nevertheless measure actual runtime and resource requirements in its own environment.

---

## 8. Potential Limitations

ALIKED should not be assumed to solve the fundamental challenges of lunar registration.

Potential limitations include:

- training-domain mismatch,
- limited transfer to lunar imagery,
- unstable keypoints under extreme appearance changes,
- weak features in low-texture regions,
- repetitive terrain ambiguity,
- sensitivity to scale or rotation conditions,
- sensitivity to cross-modal appearance differences,
- computational and dependency requirements,
- model-weight availability,
- geometric-model limitations after feature extraction.

The original ALIKED repository also notes that ALIKED remains a detector-based method and can have difficulty with large image rotations. ([GitHub][1])

These limitations should be treated as hypotheses and implementation considerations rather than assumptions about ChandraMap's results.

---

## 9. SIFT Baseline vs ALIKED Candidate

| Aspect                       | SIFT Baseline                 | ALIKED Research Candidate |
| ---------------------------- | ----------------------------- | ------------------------- |
| Feature type                 | Classical local feature       | Learned local feature     |
| Detector                     | Documented SIFT configuration | ALIKED                    |
| Descriptor                   | SIFT descriptor               | ALIKED descriptor         |
| Training dependency          | Classical handcrafted method  | Learned model             |
| Compute requirements         | `[To be measured/documented]` | `[To be measured]`        |
| Repeatability                | `[Not measured]`              | `[Not measured]`          |
| Matching quality             | `[Not measured]`              | `[Not measured]`          |
| Runtime                      | `[Not measured]`              | `[Not measured]`          |
| Robustness                   | `[Not measured]`              | `[Not measured]`          |
| Cross-sensor behavior        | `[Not measured]`              | `[Not evaluated]`         |
| Independent checkpoint error | `[To be measured]`            | `[To be measured]`        |
| Status                       | Existing baseline             | Future research           |

The purpose of this table is to establish a comparison framework, not to establish a performance ranking.

---

## 10. Research Hypotheses

### H1 — Feature Repeatability

> ALIKED may produce more repeatable local features than SIFT under selected difficult lunar imaging conditions.

This must be tested using corresponding image pairs and an explicitly defined repeatability evaluation.

### H2 — Geometric Verification

> ALIKED may produce a higher-quality set of geometrically verified correspondences.

Quality should be evaluated using:

- verified inlier count,
- inlier ratio,
- spatial distribution,
- independent registration error.

### H3 — Cross-Condition Robustness

> ALIKED may provide useful correspondences under stronger scale or illumination differences.

This should be tested using controlled condition changes and real lunar image pairs where available.

### H4 — Computational Cost

> ALIKED may introduce additional computational, model-loading, dependency, or hardware requirements.

The actual cost must be measured.

### H5 — Registration-Level Benefit

> Improvements at the feature level may or may not translate into improved independent registration accuracy.

This is a critical hypothesis because feature-level metrics alone are insufficient.

---

## 11. Core Experimental Principle

The experiment should preserve the existing ChandraMap evaluation structure:

```text
Feature Detection
       ↓
Feature Description
       ↓
Candidate Correspondences
       ↓
Geometric Verification
       ↓
Verified Inliers
       ↓
Transformation Estimation
       ↓
Independent Check-Point Evaluation
```

The fundamental distinction remains:

```text
Candidate Correspondence
        ↓
Geometric Verification
        ↓
Verified Inlier
        ↓
Transformation
        ↓
Independent Evaluation
```

Raw ALIKED matches are **not ground truth**.

RANSAC inliers are **not automatically ground truth**.

The final transformation should be evaluated using independent reference information according to the project's ground-truth design.

---

## 12. Experiment Identity

The proposed first controlled experiment is:

**`ALIKED-EXP-001` — ALIKED vs SIFT Local Feature Evaluation**

### Objective

Determine whether ALIKED changes local-feature and registration behavior relative to the existing SIFT baseline when evaluated on the same benchmark conditions.

### Baseline

SIFT.

### Candidate

ALIKED.

### Primary comparison

```text
SIFT → controlled matching → geometric verification → registration evaluation

ALIKED → controlled matching → geometric verification → registration evaluation
```

The experiment should change the feature extractor while keeping other components controlled wherever technically practical.

---

## 13. Experimental Variables

The experiment should document the following variables.

| Variable                  | Requirement                         |
| ------------------------- | ----------------------------------- |
| Source image              | Explicit image identifier           |
| Reference image           | Explicit image identifier           |
| Sensor/product            | Explicit metadata                   |
| Image resolution          | Record actual dimensions            |
| GSD                       | Record if available and trustworthy |
| Preprocessing             | Fixed and documented                |
| Scale condition           | Explicitly recorded                 |
| Illumination condition    | Explicitly recorded where available |
| Feature extractor         | SIFT or ALIKED                      |
| Descriptor                | Corresponding extractor descriptor  |
| Matcher                   | Controlled configuration            |
| Geometric model           | Fixed for initial comparison        |
| RANSAC configuration      | Recorded                            |
| Ground-truth/check points | Fixed across methods                |
| Runtime environment       | Recorded                            |
| Hardware                  | Recorded                            |
| Software environment      | Recorded                            |

---

## 14. Controlled Comparison

A fair comparison should use, where practical:

- the same image pair,
- the same image preprocessing,
- the same image resolution,
- the same benchmark split,
- the same geometric model,
- the same evaluation points,
- the same success definition,
- the same runtime hardware,
- the same reporting format.

Not every variable can necessarily be identical.

For example, ALIKED may have model-specific input processing or feature configuration requirements.

Such differences must be explicitly documented rather than hidden.

The principle is:

> **Control everything that should not be part of the research question.**

---

## 15. Experimental Progression

### Stage 1 — Reproduce Existing Baseline

Run the established SIFT baseline using the documented ChandraMap configuration.

Record:

- keypoints,
- candidate matches,
- verified inliers,
- spatial coverage,
- transformation,
- residuals,
- independent checkpoint error,
- runtime.

This establishes the reference result.

### Stage 2 — ALIKED Feature Extraction

Run ALIKED on the same source/reference images.

Record:

- keypoint count,
- keypoint coordinates,
- detector configuration,
- descriptor dimensionality/configuration,
- extraction runtime,
- spatial distribution.

No conclusion should be drawn from keypoint count alone.

### Stage 3 — Matching

Evaluate ALIKED using a controlled matching strategy.

Where practical, begin with a matcher that allows the feature-extractor contribution to be isolated.

The exact matcher configuration should be documented.

### Stage 4 — Geometric Verification

Apply the same appropriate geometric verification framework used for the controlled baseline comparison.

Record:

- candidate correspondences,
- verified inliers,
- inlier ratio,
- spatial coverage,
- geometric-model configuration.

### Stage 5 — Independent Evaluation

Evaluate the resulting transformation using independent check points or the established ChandraMap evaluation protocol.

The check points must not be used to fit the transformation if they are intended to represent independent evaluation.

### Stage 6 — Stress Testing

Evaluate across controlled conditions including:

- scale variation,
- illumination variation,
- different lunar scenes,
- cross-sensor conditions where appropriate.

### Stage 7 — Decision

Use measured evidence to determine whether further ALIKED research is justified.

The experiment must not define a winner in advance.

---

## 16. Matcher Separation

ALIKED and LightGlue represent different components.

### ALIKED

ALIKED provides learned local features:

```text
Image
 ↓
Keypoints
 ↓
Descriptors
```

### LightGlue

LightGlue is a learned feature matcher operating on local features. Its published implementation supports feature types including ALIKED and SIFT, allowing these components to be investigated separately. ([GitHub][2])

Conceptually:

```text
Image
 ↓
ALIKED
 ↓
Keypoints + Descriptors
 ↓
LightGlue
 ↓
Matches
```

Therefore, a future research matrix should distinguish:

1. **ALIKED + controlled/classical matcher**
2. **ALIKED + LightGlue**
3. **SIFT + existing baseline matcher**
4. **SIFT + LightGlue**, if useful as an additional controlled comparison

The purpose is to determine whether observed changes arise from:

- the feature extractor,
- the matcher,
- or their interaction.

---

## 17. Why Matcher Separation Matters

Suppose:

```text
SIFT + Matcher A
```

performs differently from:

```text
ALIKED + Matcher B
```

An observed improvement cannot automatically be attributed to ALIKED.

The research question becomes confounded because both feature extraction and matching have changed.

A more informative progression is:

```text
SIFT + controlled matcher
        ↓
ALIKED + same/controlled matcher
        ↓
ALIKED + LightGlue
```

This allows the contribution of the learned feature extractor and the learned matcher to be investigated separately.

---

## 18. Feature-Level Metrics

Feature-level evaluation should include, where appropriate:

### 18.1 Keypoint count

Number of detected keypoints.

This is descriptive rather than a direct quality metric.

More keypoints do not necessarily mean better registration.

### 18.2 Spatial distribution

Measure whether keypoints cover:

- the overlap region,
- different terrain areas,
- central regions,
- boundaries,
- edges,
- other relevant areas.

A highly clustered keypoint set may provide poor geometric support despite a large keypoint count.

### 18.3 Repeatability

Measure whether corresponding physical/image structures generate features at consistent locations under controlled image changes.

The definition of repeatability must account for:

- scale,
- coordinate transformations,
- image resolution,
- annotation uncertainty.

### 18.4 Descriptor matching quality

Measure the quality of proposed correspondences using an independent reference when available.

### 18.5 Candidate match count

Report the number of proposed correspondences.

This must not be treated as a success metric by itself.

### 18.6 Verified inlier count

Measure how many candidate correspondences remain geometrically consistent with the selected transformation model.

### 18.7 Inlier ratio

A useful diagnostic is:

$$
\text{Inlier Ratio}
=
\frac{N_{\text{inliers}}}
{N_{\text{candidate matches}}}
$$

provided the numerator and denominator are defined consistently.

### 18.8 Spatial coverage

Measure how well verified correspondences cover the registration region.

---

## 19. Registration-Level Metrics

Feature-level improvement does not automatically imply registration-level improvement.

The primary evaluation should therefore include registration-level metrics such as:

- reprojection error,
- independent checkpoint RMSE,
- median checkpoint error,
- error percentiles where appropriate,
- verified inlier count,
- inlier ratio,
- spatial coverage,
- registration success rate,
- runtime,
- memory/resource usage where relevant.

For a predicted checkpoint position:

$$
\hat{p}_i = T(p_i)
$$

and an independent reference point:

$$
p_i^*
$$

the Euclidean residual is:

$$
e_i =
\left\|
\hat{p}_i - p_i^*
\right\|_2
$$

and checkpoint RMSE is:

$$
RMSE =
\sqrt{
\frac{1}{N}
\sum_{i=1}^{N}
e_i^2
}
$$

The coordinate system must always be stated.

For image-space evaluation, the result is typically expressed in pixels.

Ground-unit error should only be reported when the underlying spatial reference, projection, GSD, and uncertainty justify that interpretation.

---

## 20. Ground Truth and Independent Evaluation

ALIKED experiments must follow the ChandraMap ground-truth principles.

The essential distinction is:

```text
Feature Matches
      ↓
Model Fitting
      ↓
Estimated Transformation
      ↓
Independent Ground Truth
      ↓
Registration Error
```

Model-fit points are not automatically ground truth.

RANSAC inliers are not automatically ground truth.

Independent check points should be withheld from:

- transformation fitting,
- hyperparameter selection,
- method selection,
- threshold tuning,

when they are intended to provide an unbiased evaluation.

The same ground-truth points should be used across compared methods whenever the benchmark design permits this.

---

## 21. Geometric Model

ALIKED should initially be evaluated using an established and controlled geometric verification framework.

Relevant ChandraMap research includes:

`experiments/v1/geometry/EXP-004-affine-vs-homography/`

Potential transformation models include:

- affine transformation,
- homography.

The geometric model is a separate experimental variable.

Therefore:

> A better feature detector should not receive credit for an unsuitable geometric model, and a feature detector should not be blamed for a geometric model that cannot represent the underlying image relationship.

For the first controlled ALIKED comparison, the geometric model should preferably be fixed across SIFT and ALIKED.

Later experiments can investigate interaction effects.

---

## 22. Affine vs Homography Considerations

An affine model provides a lower-complexity geometric relationship than a homography.

A homography provides greater projective flexibility.

Neither should automatically be interpreted as the physical truth for lunar imagery.

Potential causes of model mismatch include:

- terrain relief,
- orthorectification differences,
- sensor geometry,
- residual projection errors,
- spatially varying distortion,
- local versus global scene geometry.

Therefore, if ALIKED produces a different residual pattern from SIFT, the result should not immediately be interpreted as a feature-quality difference.

The geometric model must also be examined.

---

## 23. Residual Analysis

The ALIKED experiment should connect directly with:

`experiments/v1/geometry/EXP-005-residual-analysis/`

Residual analysis should investigate:

- where registration errors occur,
- whether errors are spatially clustered,
- whether residuals increase toward image boundaries,
- whether residual direction is systematic,
- whether the transformation explains the correspondence set,
- whether local regions behave differently,
- whether ALIKED provides stable geometric support.

A scalar RMSE can hide important spatial behavior.

For example:

```text
Low average error
+
large localized error region
```

may be scientifically important even if the aggregate metric appears acceptable.

Residual maps and spatial diagnostics should therefore complement scalar metrics.

---

## 24. Sub-pixel Refinement

The ALIKED investigation should connect with:

`experiments/v1/refinement/EXP-006-subpixel-refinement/`

The conceptual sequence is:

```text
ALIKED Features
      ↓
Candidate Correspondences
      ↓
Geometric Verification
      ↓
Verified Inliers
      ↓
Sub-pixel Refinement
      ↓
Final Transformation
      ↓
Independent Evaluation
```

Sub-pixel refinement should remain downstream of reliable correspondence.

It must not be used to compensate for fundamentally incorrect matches.

A refinement method that improves numerical precision on incorrect correspondences does not create correct correspondence.

---

## 25. Scale Robustness

ALIKED should be evaluated against the scale conditions documented in:

`research/notes/scale-invariance.md`

Potential conditions include:

- native resolution,
- controlled downsampling,
- upsampling,
- reference-image scale pyramids,
- source/reference resolution differences,
- controlled scale perturbations.

The experiment should distinguish:

### Feature-scale robustness

Whether ALIKED continues to generate useful corresponding local features under scale changes.

### Registration-scale robustness

Whether the complete pipeline continues to estimate an accurate transformation under those changes.

These are not equivalent.

A feature detector may remain repeatable while the final registration still fails because of:

- poor spatial coverage,
- incorrect matches,
- unsuitable geometry,
- insufficient overlap,
- or other pipeline effects.

Upsampling must also not be interpreted as recovering spatial information that was absent from the original image.

---

## 26. Illumination Robustness

ALIKED should be evaluated using the principles in:

`research/notes/illumination-invariance.md`

Lunar illumination changes can affect:

- shadows,
- local contrast,
- visible surface structure,
- feature appearance,
- apparent boundaries.

This is more complicated than a simple brightness offset.

For example, a shadow boundary may move because the illumination geometry changed.

Therefore:

> Illumination normalization should not be assumed to restore the same physical image structure.

ALIKED experiments should include realistic appearance changes rather than only synthetic brightness or contrast adjustments.

Stable terrain morphology may provide useful correspondence information, but no feature type should be assumed to remain invariant in every illumination condition.

---

## 27. Cross-Sensor Research

Future ALIKED research may investigate:

```text
OHRC ↔ TMC-2
OHRC ↔ IIRS-derived 2D representation
TMC-2 ↔ IIRS-derived 2D representation
```

where supported by the ChandraMap research roadmap and available data.

Cross-sensor matching introduces additional variables, including:

- spatial-resolution differences,
- sensor response differences,
- contrast differences,
- modality differences,
- preprocessing differences,
- representation differences.

Therefore, cross-sensor experiments should not simply be treated as more difficult versions of same-sensor matching.

They represent a distinct research condition.

No cross-sensor ALIKED success should be claimed until independently measured.

---

## 28. IIRS Relationship

The relationship between ALIKED and IIRS should remain modular.

Relevant future research:

`research/future/IIRS.md`

IIRS-derived 2D representations could potentially include:

- a selected spectral band,
- a band combination,
- a PCA-derived image,
- a structural representation,
- a gradient representation.

The exact representation must be defined by the IIRS research experiment.

The architecture should remain conceptually:

```text
IIRS Data
   ↓
2D Representation
   ↓
ALIKED
   ↓
Keypoints + Descriptors
   ↓
Matcher
   ↓
Geometric Verification
```

ALIKED should not be treated as a solution to the IIRS representation problem.

Two independent questions exist:

1. Is the chosen IIRS representation useful for correspondence?
2. Is ALIKED useful for extracting local features from that representation?

These should be experimentally separable.

---

## 29. Spatial Coverage

ChandraMap must preserve the principle:

> **A large number of matches does not necessarily mean good registration.**

ALIKED may detect many features while concentrating them in a small image region.

This can produce a transformation that appears geometrically strong locally but generalizes poorly elsewhere.

Spatial evaluation should therefore consider:

- keypoint distribution,
- candidate-match distribution,
- verified-inlier distribution,
- overlap coverage,
- clustering,
- edge concentration,
- region-level support,
- convex-hull coverage where appropriate,
- grid-based coverage where appropriate.

No universal coverage threshold should be assumed unless explicitly established by the benchmark.

---

## 30. Feature Clustering

Feature clustering should be treated as a potential failure condition.

Example:

```text
+-----------------------+
|                       |
|                       |
|        XXXXXXX        |
|       XXXXXXXX        |
|        XXXXXXX        |
|                       |
|                       |
+-----------------------+
```

A large number of matches concentrated around one structure may provide less geometric information than fewer well-distributed matches.

The experiment should therefore report both:

```text
How many?
```

and:

```text
Where?
```

---

## 31. Candidate Matches vs Verified Correspondences

The evaluation pipeline must preserve the following distinction:

```text
Candidate Matches
        ↓
Geometric Verification
        ↓
Verified Inliers
        ↓
Final Transformation
        ↓
Independent Evaluation
```

### Candidate match

A descriptor/matching stage proposes that two features correspond.

### Verified inlier

The candidate is consistent with the selected geometric model under the verification procedure.

### Ground-truth correspondence

An independently established reference correspondence used to evaluate correctness.

These concepts must not be conflated.

In particular:

> A high candidate-match count is not evidence of registration accuracy.

---

## 32. Failure Modes

### 32.1 Insufficient repeatability

ALIKED may detect different points under different lunar appearance conditions.

**Detection:**

- repeatability measurements,
- correspondence visualization,
- independent reference points,
- controlled condition tests.

---

### 32.2 Unstable keypoint localization

A feature may appear approximately correct but move significantly between images.

**Detection:**

- coordinate residuals,
- repeatability analysis,
- localization error distributions.

---

### 32.3 Weak features in low-texture regions

Low-texture terrain may produce few useful keypoints.

**Detection:**

- keypoint density maps,
- regional coverage analysis,
- failure-case visualization.

---

### 32.4 Repetitive terrain

Similar terrain structures can create ambiguous matches.

**Detection:**

- candidate-to-ground-truth correspondence analysis,
- RANSAC rejection,
- spatial consistency analysis,
- independent checkpoint error.

---

### 32.5 Clustered features

Features may concentrate around visually strong regions.

**Detection:**

- keypoint heatmaps,
- convex-hull coverage,
- grid coverage,
- regional feature counts.

---

### 32.6 Modality mismatch

ALIKED may respond differently to imagery from different sensors or representations.

**Detection:**

- sensor-specific benchmark breakdown,
- correspondence quality by modality pair,
- independent registration error.

---

### 32.7 Illumination sensitivity

Features may be unstable when shadow geometry changes.

**Detection:**

- controlled illumination subsets,
- shadow-heavy versus morphology-dominated regions,
- residual and correspondence visualization.

---

### 32.8 Scale sensitivity

Feature repeatability may degrade under strong resolution differences.

**Detection:**

- controlled scale experiments,
- pyramid experiments,
- repeatability by scale factor,
- registration error by scale condition.

---

### 32.9 Excessive computational cost

A learned feature may improve accuracy while introducing unacceptable resource requirements.

**Detection:**

- extraction runtime,
- peak memory,
- model loading time,
- total pipeline runtime,
- hardware requirements.

---

### 32.10 Dependency or model-weight problems

Research results may not be reproducible if model weights, custom operators, or software dependencies are unavailable or poorly documented.

**Detection:**

- clean-environment reproduction,
- explicit dependency recording,
- model/checkpoint hash or identifier,
- installation verification.

---

### 32.11 Unstable performance across scenes

A method may work on one lunar region but fail on another.

**Detection:**

- scene-level reporting,
- fixed benchmark splits,
- failure-rate analysis,
- per-pair metrics.

---

### 32.12 Geometric-model mismatch

The local features may be correct while the selected transformation model is insufficient.

**Detection:**

- residual maps,
- spatial residual patterns,
- affine/homography comparison,
- independent checkpoint evaluation.

---

### 32.13 False geometric consensus

A large group of incorrect matches can sometimes form a plausible geometric consensus.

**Detection:**

- independent ground truth,
- spatial coverage,
- residual analysis,
- held-out check points.

---

## 33. Computational Cost

The experiment should measure, where practical:

### Feature extraction

- image loading time,
- preprocessing time,
- ALIKED inference time,
- feature post-processing time.

### Matching

- matcher runtime,
- candidate correspondence generation time.

### Geometric verification

- RANSAC runtime,
- transformation estimation time.

### End-to-end

- total runtime,
- model-loading overhead,
- memory consumption.

### Environment

Record:

- CPU,
- GPU where applicable,
- accelerator configuration,
- RAM,
- operating system,
- Python version,
- framework version,
- dependency versions.

No runtime value should be copied from an external benchmark and presented as ChandraMap performance.

---

## 34. Reproducibility Requirements

Every ALIKED experiment should record:

- ALIKED implementation/version,
- model/checkpoint identifier,
- model configuration,
- feature configuration,
- maximum keypoint configuration,
- detection configuration,
- input image identifiers,
- input image dimensions,
- preprocessing,
- image scaling/resizing,
- coordinate conventions,
- matcher configuration,
- geometric model,
- RANSAC parameters,
- random seeds where applicable,
- hardware,
- software environment,
- dependency versions,
- evaluation-point definitions,
- benchmark split,
- experiment configuration,
- output artifacts.

A model checkpoint name should not be invented.

If the exact checkpoint is not yet selected:

```text
checkpoint: [TBD]
```

should be used.

---

## 35. Model and Implementation Provenance

The original ALIKED repository provides pretrained models and describes several model configurations. ([GitHub][1])

The LightGlue implementation also exposes ALIKED as a supported feature extractor and provides ALIKED-specific integration. ([GitHub][3])

For ChandraMap, the selected implementation must be explicitly recorded.

At minimum:

```text
implementation:
    source: [TBD]
    version_or_commit: [TBD]
    model_name: [TBD]
    checkpoint_identifier: [TBD]
    checksum: [TBD]
```

This prevents a future experiment from becoming impossible to reproduce because "ALIKED" alone is not sufficient provenance.

---

## 36. Experimental Artifacts

A future implementation should produce artifacts such as:

- ALIKED experiment README,
- experiment configuration,
- feature extraction logs,
- keypoint visualizations,
- keypoint-density maps,
- correspondence visualizations,
- RANSAC/inlier visualizations,
- residual plots,
- spatial coverage plots,
- checkpoint-error reports,
- benchmark tables,
- runtime measurements,
- memory measurements,
- failure-analysis reports.

These are expected research artifacts.

They do **not** imply that the artifacts currently exist.

---

## 37. Proposed Experiment Configuration

A future configuration could conceptually contain:

```yaml
experiment_id: ALIKED-EXP-001

baseline:
  method: SIFT
  configuration: "[documented ChandraMap configuration]"

candidate:
  method: ALIKED
  implementation: "[TBD]"
  model_name: "[TBD]"
  checkpoint: "[TBD]"

dataset:
  source_images: "[TBD]"
  reference_images: "[TBD]"
  benchmark_split: "[TBD]"

preprocessing:
  method: "[TBD]"
  image_size: "[TBD]"

matching:
  method: "[TBD]"
  configuration: "[TBD]"

geometry:
  model: "[TBD]"
  verification: "RANSAC"
  parameters: "[TBD]"

evaluation:
  ground_truth: "[TBD]"
  independent_check_points: true
  metrics:
    - candidate_matches
    - verified_inliers
    - inlier_ratio
    - spatial_coverage
    - checkpoint_rmse
    - median_error
    - runtime

reproducibility:
  seed: "[TBD]"
  hardware: "[TBD]"
  software_environment: "[TBD]"
```

This is a proposed schema, not an existing ChandraMap configuration.

---

## 38. Benchmark Requirements

The ALIKED benchmark should contain enough variation to distinguish genuine robustness from performance on a small set of favorable examples.

Each image pair should ideally have documented metadata including:

- source image identifier,
- target image identifier,
- source sensor,
- target sensor,
- acquisition metadata where available,
- overlap information,
- image dimensions,
- GSD where known,
- illumination information where available,
- scale condition,
- preprocessing,
- coordinate convention,
- ground-truth provenance,
- independent check-point information,
- benchmark version.

No benchmark should silently remove difficult pairs because they produce failures.

---

## 39. Benchmark Splitting

The benchmark should distinguish between:

### Development / fitting information

Information allowed for:

- method configuration,
- debugging,
- transformation estimation,
- parameter selection where explicitly permitted.

### Independent evaluation information

Information withheld from fitting and method tuning when it is intended to represent independent evaluation.

The benchmark must prevent leakage through:

- duplicate points,
- near-duplicate points,
- repeated physical structures,
- overlapping evaluation regions,
- repeated image pairs,
- method-specific point selection.

A fixed benchmark partition should be preferred for controlled comparisons.

---

## 40. Registration Success

Registration success should not be defined solely by:

- number of keypoints,
- number of candidate matches,
- number of RANSAC inliers,
- or inlier ratio.

A meaningful registration evaluation should consider multiple pieces of evidence:

```text
Correspondence quality
        +
Geometric consistency
        +
Spatial coverage
        +
Independent checkpoint accuracy
        +
Reproducibility
```

The exact acceptance thresholds remain:

`[TBD]`

until established by the benchmark methodology.

---

## 41. Feature-Level vs Registration-Level Improvement

A critical research distinction is:

```text
Feature Improvement
        ≠
Registration Improvement
```

For example, ALIKED might produce:

- more keypoints,
- more candidate matches,
- higher inlier counts,

while still producing worse independent checkpoint accuracy.

Conversely, it might produce fewer features but provide better spatially distributed correspondences.

Therefore, registration-level evaluation should remain the primary scientific test of practical usefulness.

---

## 42. Residual Interpretation

Suppose ALIKED produces a transformation with systematic residuals.

Possible interpretations include:

1. incorrect correspondences,
2. feature localization error,
3. poor spatial coverage,
4. unsuitable geometric model,
5. terrain-related geometric effects,
6. sensor or projection differences,
7. ground-truth uncertainty.

The experiment should not automatically assign all residual error to ALIKED.

Residual analysis must consider the entire measurement chain.

---

## 43. Ground-Truth Uncertainty

Ground truth itself may contain uncertainty.

Potential sources include:

- image resolution,
- blur,
- ambiguous feature location,
- cross-sensor appearance,
- illumination differences,
- interpolation,
- manual annotation uncertainty,
- geometric reference uncertainty.

Where meaningful, ground-truth records should support uncertainty information.

Conceptually:

```text
Ground Truth Point
      +
Uncertainty
      ↓
Evaluation
```

This prevents the benchmark from implying false precision.

---

## 44. Illumination-Specific Ground Truth Considerations

A visible point in one image may not correspond to a visually identical pixel in another image when illumination changes.

Examples include:

- crater rims,
- shadow boundaries,
- slope boundaries,
- small bright/dark structures.

A shadow boundary should not automatically be treated as a stable physical landmark across different solar illumination conditions.

Ground-truth construction should prefer correspondences whose physical interpretation is defensible under the specific image pair.

---

## 45. Cross-Sensor Ground Truth Considerations

A correspondence between OHRC and TMC-2, for example, may involve substantially different image scales and appearances.

A correspondence between an IIRS-derived representation and an optical image introduces additional representation differences.

Therefore, the benchmark should record:

```text
source_sensor
target_sensor
source_representation
target_representation
coordinate_convention
ground_truth_provenance
uncertainty
```

rather than treating all image pairs as equivalent.

---

## 46. ALIKED and Representation Research

ALIKED should be evaluated separately from representation design.

For example:

```text
Raw / Selected Representation
          ↓
ALIKED
          ↓
Matcher
          ↓
Geometry
```

If a new IIRS representation improves performance, this does not automatically demonstrate that ALIKED caused the improvement.

Similarly, if ALIKED fails on an IIRS representation, the failure may originate from the representation rather than the feature extractor.

Controlled experiments should therefore vary one major component at a time where practical.

---

## 47. ALIKED and Scale-Pyramid Research

The existing scale-pyramid experiment:

`experiments/v1/preprocessing/EXP-002-scale-pyramid/`

should remain a separate research variable.

A future comparison may investigate:

```text
SIFT + Scale Pyramid
vs
ALIKED + Same Scale Pyramid
```

but the scale-pyramid effect should first be understood independently.

The experiment should distinguish:

- ALIKED's intrinsic scale behavior,
- external image-pyramid assistance,
- and the combined pipeline behavior.

---

## 48. ALIKED and Gradient/Structural Representation

The existing representation research:

`experiments/v1/preprocessing/EXP-003-gradient-representation/`

can inform future ALIKED studies.

Potential research questions include:

> Does ALIKED benefit from structural or gradient-based input representations?

and:

> Does a representation that helps SIFT also help ALIKED?

These should remain separate experimental questions.

A representation change must not be introduced simultaneously with an ALIKED change if the goal is to measure the contribution of ALIKED itself.

---

## 49. Fair Ablation Structure

A useful future ablation matrix is:

| Experiment | Feature | Representation | Matcher    | Geometry |
| ---------- | ------- | -------------- | ---------- | -------- |
| A          | SIFT    | Baseline       | Existing   | Fixed    |
| B          | ALIKED  | Same           | Controlled | Fixed    |
| C          | ALIKED  | Same           | LightGlue  | Fixed    |
| D          | SIFT    | Alternative    | Existing   | Fixed    |
| E          | ALIKED  | Alternative    | Controlled | Fixed    |
| F          | ALIKED  | Alternative    | LightGlue  | Fixed    |

Only combinations justified by the benchmark should be executed.

The goal is not to maximize the number of experiments.

The goal is to identify causal differences between system components.

---

## 50. Decision Questions

The ALIKED investigation should answer:

### Accuracy

- Does ALIKED reduce independent registration error?
- Does it improve checkpoint RMSE?
- Does it reduce systematic residuals?

### Correspondence

- Does it produce useful candidate matches?
- Does it increase geometrically verified correspondences?
- Does it maintain useful spatial coverage?

### Robustness

- Does it remain stable across lunar scenes?
- Does it help under scale variation?
- Does it help under illumination variation?
- Does it help across sensor pairs?

### Practicality

- What is the feature extraction cost?
- What is the total runtime?
- What hardware is required?
- Can the environment be reproduced?

### Scientific value

- Is the improvement repeatable?
- Is it statistically and experimentally meaningful for the benchmark?
- Does the added complexity produce measurable benefit?

---

## 51. Decision Framework

The experiment should use evidence-based status labels rather than predetermined rankings.

Suggested statuses:

- `Exploratory`
- `Proposed`
- `Planned`
- `Experiment Ready`
- `Under Evaluation`
- `Validated`
- `Implementation Candidate`
- `Implemented`
- `Deferred`
- `Rejected`

ALIKED should **not** be marked:

- `Validated`
- `Implementation Candidate`
- `Implemented`

until the repository contains the required experimental evidence.

---

## 52. Research Status

| Component                   | Status              | Evidence / Next Step                     |
| --------------------------- | ------------------- | ---------------------------------------- |
| SIFT baseline               | Existing baseline   | Use as controlled reference              |
| ALIKED investigation        | Proposed            | Design and execute controlled experiment |
| ALIKED implementation       | `[Not implemented]` | Future work                              |
| ALIKED benchmark            | `[Not evaluated]`   | Requires benchmark                       |
| ALIKED + controlled matcher | Future research     | Isolate feature contribution             |
| ALIKED + LightGlue          | Future research     | Evaluate matcher interaction separately  |
| Cross-sensor ALIKED         | Future research     | Requires appropriate benchmark           |
| IIRS + ALIKED               | Future research     | Requires representation study            |
| ALIKED validation           | `[Not validated]`   | Requires independent evaluation          |

---

## 53. Reproducibility Checklist

Before considering an ALIKED experiment reproducible, verify:

- [ ] Input image identifiers are recorded.
- [ ] Source and target sensors are recorded.
- [ ] Image dimensions are recorded.
- [ ] Preprocessing is documented.
- [ ] Coordinate convention is documented.
- [ ] ALIKED implementation is identified.
- [ ] ALIKED model/checkpoint is identified.
- [ ] Model configuration is recorded.
- [ ] Matcher configuration is recorded.
- [ ] Geometric model is recorded.
- [ ] RANSAC parameters are recorded.
- [ ] Random seeds are recorded where applicable.
- [ ] Ground-truth version is recorded.
- [ ] Independent check points are identified.
- [ ] Hardware is recorded.
- [ ] Software environment is recorded.
- [ ] Dependency versions are recorded.
- [ ] Runtime measurement methodology is documented.
- [ ] Failure cases are retained.
- [ ] Results can be traced back to a specific experiment configuration.

---

## 54. Expected Experiment Artifacts

A future implementation may create:

```text
ALIKED-EXP-001/
├── README.md
├── config/
│   └── experiment.yaml
├── features/
│   ├── source/
│   └── reference/
├── matches/
├── geometry/
├── evaluation/
├── visualizations/
└── results/
```

This is a **conceptual artifact structure only**.

The actual repository structure should follow the project's established experiment conventions when the experiment is implemented.

---

## 55. Visualization Requirements

Visual inspection should complement quantitative metrics.

Recommended visualizations include:

### Keypoint maps

Show feature locations over source and reference images.

### Candidate correspondence maps

Display raw proposed matches.

### Verified correspondence maps

Display geometrically consistent matches separately.

### Spatial coverage

Show whether correspondences cover the registration region.

### Residual vectors

Plot:

```text
Ground Truth Point
        ↓
Predicted Point
```

with the residual vector visible.

### Failure-case overlays

Preserve examples where:

- features cluster,
- correspondences are incorrect,
- geometry fails,
- illumination changes cause ambiguity,
- cross-sensor appearance becomes difficult.

Visualizations must not replace quantitative evaluation.

---

## 56. Common Experimental Mistakes

### Mistake 1 — Comparing only keypoint counts

More keypoints do not necessarily produce better registration.

### Mistake 2 — Comparing only inlier counts

A large inlier count can still be spatially clustered.

### Mistake 3 — Evaluating on RANSAC inliers

Those points influenced the fitted transformation and are therefore not independent evaluation points.

### Mistake 4 — Changing the matcher and detector together

This prevents attribution of performance changes.

### Mistake 5 — Changing preprocessing between methods

This introduces an uncontrolled variable.

### Mistake 6 — Selecting the best method after seeing all test results

This creates benchmark leakage.

### Mistake 7 — Removing failed pairs

Failure cases are part of the evidence.

### Mistake 8 — Treating external benchmark results as lunar evidence

Performance on HPatches, MegaDepth, or another dataset does not establish lunar registration performance.

### Mistake 9 — Treating LightGlue performance as ALIKED performance

The matcher and feature extractor are different research components.

### Mistake 10 — Assuming learned means illumination invariant

Learned representations still require evaluation under the actual appearance changes relevant to the task.

---

## 57. External Benchmark Results

ALIKED has been evaluated in the broader local-feature research ecosystem, and its implementation is available in research repositories. ([GitHub][1])

However, external benchmark results should be treated as **background evidence**, not as ChandraMap validation.

The relevant question remains:

> How does ALIKED behave on the lunar image pairs and benchmark conditions defined by ChandraMap?

External results cannot replace:

- lunar benchmark evaluation,
- independent checkpoint evaluation,
- sensor-specific analysis,
- illumination analysis,
- scale analysis,
- spatial coverage analysis.

---

## 58. Relationship to LightGlue

LightGlue is a learned local-feature matcher rather than a replacement for ALIKED's feature-extraction role. Its implementation supports ALIKED and SIFT feature inputs, which makes it useful for future controlled feature/matcher studies. ([GitHub][2])

The research progression should therefore remain:

```text
SIFT
  ↓
Controlled baseline

ALIKED
  ↓
Feature-level research

ALIKED + LightGlue
  ↓
Feature + learned matching research
```

This separation makes it possible to ask:

1. Does ALIKED improve feature extraction?
2. Does LightGlue improve matching?
3. Does their combination improve registration?
4. Does that improvement persist on independent lunar evaluation?

---

## 59. Relationship to LoFTR

LoFTR represents a different research direction from ALIKED because the correspondence strategy is not based on the same detector-plus-local-descriptor pipeline.

Therefore:

```text
ALIKED
→ local keypoint + descriptor research

LoFTR
→ detector-free / learned correspondence research
```

These should not be merged into a single experiment.

If LoFTR is investigated later, it should have its own controlled research specification and benchmark comparison.

---

## 60. Future Research Roadmap

ALIKED belongs to the future learned-feature and matching research direction represented by:

`research/future/README.md`

A conceptual progression is:

```text
SIFT Baseline
      ↓
Controlled Evaluation
      ↓
ALIKED Feature Research
      ↓
Feature-Level Evaluation
      ↓
Geometric Verification
      ↓
Registration-Level Evaluation
      ↓
ALIKED + Advanced Matching
      ↓
Cross-Sensor Evaluation
      ↓
Validation / Integration Decision
```

The roadmap does not imply that every stage will succeed or that every method will be integrated.

Each stage should be justified by experimental evidence.

---

## 61. Recommended Research Sequence

A practical research sequence is:

### Phase 1 — Baseline reproduction

Reproduce the existing SIFT result on the selected benchmark.

### Phase 2 — ALIKED extraction

Evaluate keypoint count, distribution, repeatability, and runtime.

### Phase 3 — Controlled matching

Compare ALIKED against SIFT using a controlled matching configuration.

### Phase 4 — Geometry

Apply the established geometric verification pipeline.

### Phase 5 — Independent evaluation

Measure checkpoint error and spatial coverage.

### Phase 6 — Scale

Evaluate controlled scale differences.

### Phase 7 — Illumination

Evaluate realistic illumination differences.

### Phase 8 — Scene diversity

Expand to multiple lunar scenes.

### Phase 9 — Cross-sensor

Evaluate appropriate sensor pairs.

### Phase 10 — LightGlue

Investigate whether learned matching adds measurable value.

### Phase 11 — Decision

Determine whether ALIKED deserves further implementation or integration.

---

## 62. What Would Constitute Strong Evidence?

Evidence supporting further ALIKED research would ideally show consistent improvement across multiple independent dimensions rather than a single metric.

Relevant evidence could include:

- lower independent checkpoint error,
- improved correspondence repeatability,
- useful verified-inlier counts,
- better spatial coverage,
- improved robustness across controlled scale changes,
- improved robustness across illumination conditions,
- useful performance across multiple scenes,
- useful cross-sensor behavior,
- acceptable computational cost,
- reproducible results.

No single metric should automatically determine the decision.

---

## 63. What Would Not Constitute Sufficient Evidence?

The following should not independently justify adopting ALIKED:

- more keypoints,
- more candidate matches,
- a visually attractive correspondence image,
- a higher RANSAC inlier count alone,
- a high inlier ratio alone,
- results from unrelated datasets,
- a successful single lunar image pair,
- lower training or inference cost claimed by an external source,
- a transformation that has only been evaluated on its fitting points.

The benchmark must evaluate the complete registration objective.

---

## 64. Limitations of This Research Specification

This document does not establish:

- a ChandraMap ALIKED implementation,
- a selected ALIKED checkpoint,
- a selected matcher,
- a final feature configuration,
- a benchmark dataset,
- a performance threshold,
- a measured runtime,
- a measured memory requirement,
- an ALIKED accuracy result,
- cross-sensor success,
- IIRS success,
- production readiness.

Those items require future experimental work.

---

## 65. Open Research Questions

The following questions remain open:

1. How repeatable are ALIKED features on lunar terrain?
2. How does ALIKED behave under strong resolution differences?
3. How does it behave under changed shadow geometry?
4. How does it behave on weak-texture terrain?
5. Does ALIKED provide better spatially distributed geometric support than SIFT?
6. Does feature-level improvement translate into lower independent registration error?
7. Which ALIKED model configuration is appropriate for ChandraMap?
8. Which input resolution should be used?
9. How should keypoint limits be selected?
10. Which matcher provides the fairest first comparison?
11. Does LightGlue provide measurable benefit after ALIKED extraction?
12. How does ALIKED behave across OHRC and TMC-2 imagery?
13. How should ALIKED be evaluated on IIRS-derived representations?
14. How much computational overhead is acceptable for ChandraMap?
15. What benchmark size is sufficient for a reliable comparison?
16. Which failure modes dominate?
17. How should uncertainty be incorporated into evaluation?
18. What evidence is sufficient to justify integration into a later ChandraMap version?

---

## 66. Acceptance Questions for `ALIKED-EXP-001`

Before closing the experiment, the researcher should be able to answer:

### Data

- [ ] Were the same benchmark image pairs used for the baseline and candidate?
- [ ] Were benchmark conditions documented?
- [ ] Were difficult cases retained?

### Feature extraction

- [ ] Was the ALIKED configuration recorded?
- [ ] Was the model/checkpoint recorded?
- [ ] Were keypoint distributions measured?
- [ ] Was repeatability evaluated where possible?

### Matching

- [ ] Was the matcher configuration recorded?
- [ ] Were candidate matches reported?
- [ ] Were candidate matches distinguished from verified inliers?

### Geometry

- [ ] Was the geometric model fixed or explicitly varied?
- [ ] Were RANSAC parameters recorded?
- [ ] Were residuals analyzed?

### Evaluation

- [ ] Were independent check points used?
- [ ] Was checkpoint error measured?
- [ ] Was spatial coverage measured?
- [ ] Were failure cases retained?

### Reproducibility

- [ ] Was the environment recorded?
- [ ] Was hardware recorded?
- [ ] Were dependencies recorded?
- [ ] Could another researcher reproduce the experiment?

---

## 67. Implementation Boundary

Until the experiment provides evidence, ALIKED should remain outside the core ChandraMap production pipeline.

The appropriate boundary is:

```text
Current ChandraMap V1
        │
        ├── SIFT baseline
        │
        └── Established experiments
                    │
                    ▼
             Future Research
                    │
                    └── ALIKED
```

An implementation should move toward the main pipeline only after the research results justify that transition.

---

## 68. Reproducibility and Research Integrity Principle

The ALIKED investigation should follow the ChandraMap principle:

> **Build small. Measure honestly. Keep the failures.**

This means:

- do not cherry-pick successful image pairs,
- do not hide failed registrations,
- do not tune against independent evaluation points,
- do not claim improvement from one metric,
- do not confuse external benchmark performance with lunar performance,
- do not claim robustness without testing the relevant condition,
- do not silently change preprocessing between methods,
- do not report unsupported numerical values.

---

## 69. Summary

ALIKED is a potential learned local feature detector and descriptor for future ChandraMap research.

Its role should be investigated systematically:

```text
ALIKED
  ↓
Keypoints + Descriptors
  ↓
Candidate Correspondences
  ↓
Geometric Verification
  ↓
Verified Inliers
  ↓
Transformation
  ↓
Independent Check Points
  ↓
Registration Error
```

The central research question is not:

> "Is ALIKED better than SIFT?"

Instead, the scientifically useful question is:

> **Under which ChandraMap conditions, if any, does ALIKED provide measurable advantages over the established SIFT baseline, and are those advantages sufficient to justify its additional complexity?**

That question can only be answered through controlled experiments, independent evaluation, failure analysis, and reproducible benchmarking.

---

## 70. Source Basis and Related Research

### ChandraMap repository context

This document is based on the established ChandraMap research structure and the project materials provided for this research effort, including:

- `research/README.md`
- `research/literature/README.md`
- `research/notes/lunar-registration.md`
- `research/notes/illumination-invariance.md`
- `research/notes/scale-invariance.md`
- `research/notes/ground-truth-design.md`
- `research/future/README.md`
- `research/future/IIRS.md`
- `experiments/v1/README.md`
- `experiments/templates/EXPERIMENT_TEMPLATE.md`
- `experiments/v1/baseline/EXP-001-sift-baseline/README.md`
- `experiments/v1/preprocessing/EXP-002-scale-pyramid/README.md`
- `experiments/v1/preprocessing/EXP-003-gradient-representation/README.md`
- `experiments/v1/geometry/EXP-004-affine-vs-homography/README.md`
- `experiments/v1/geometry/EXP-005-residual-analysis/README.md`
- `experiments/v1/refinement/EXP-006-subpixel-refinement/README.md`

### ALIKED

The original ALIKED implementation describes the method as:

> _ALIKED: A Lighter Keypoint and Descriptor Extraction Network via Deformable Transformation_

and documents its relationship to ALIKE, its learned local feature extraction design, pretrained models, and implementation limitations. ([GitHub][1])

### LightGlue

LightGlue is a separate learned local-feature matching component. Its implementation explicitly supports ALIKED and SIFT feature inputs, making it relevant to future controlled feature-versus-matcher experiments. ([GitHub][2])

---

## 71. Final Research Position

**ALIKED status in ChandraMap:**

```text
Future Research Candidate
        ↓
Not Yet Validated
        ↓
Requires Controlled Benchmark
        ↓
Requires Independent Evaluation
        ↓
Integration Decision Only After Evidence
```

The current scientific position is therefore:

> **SIFT remains the established ChandraMap V1 classical baseline. ALIKED is a proposed learned local-feature candidate whose value for lunar image correspondence must be established through controlled, reproducible, ground-truth-based experiments.**

[1]: https://github.com/Shiaoming/ALIKED?utm_source=chatgpt.com "GitHub - Shiaoming/ALIKED: ALIKED: A Lighter Keypoint and Descriptor Extraction Network via Deformable Transformation · GitHub"
[2]: https://github.com/rairang/lightglue?utm_source=chatgpt.com "GitHub - rairang/lightglue: LightGlue: Local Feature Matching at Light Speed (ICCV 2023) · GitHub"
[3]: https://github.com/cvg/LightGlue/blob/main/lightglue/aliked.py?utm_source=chatgpt.com "LightGlue/lightglue/aliked.py at main · cvg/LightGlue · GitHub"
