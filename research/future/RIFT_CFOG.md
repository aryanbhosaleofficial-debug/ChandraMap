# RIFT + CFOG Research

> **Research area:** Illumination-, radiation-, and structure-focused image correspondence for lunar registration
> **Repository:** ChandraMap
> **Path:** `research/future/RIFT_CFOG.md`
> **Status:** Proposed future research
> **Implementation status:** `[Not implemented]`
> **Validation status:** `[Not evaluated]`

---

## 1. Purpose

This document specifies a future research investigation of **RIFT** and **CFOG-style representations** for ChandraMap.

The purpose is to determine whether structure-focused and radiation/illumination-robust representations can improve lunar image correspondence under difficult appearance conditions compared with simpler intensity- and gradient-based representations.

This document is a **research specification**, not an implementation report.

It does **not** claim that:

- RIFT is currently implemented in ChandraMap.
- CFOG is currently implemented in ChandraMap.
- RIFT has been validated on ChandraMap lunar imagery.
- CFOG has been validated on ChandraMap lunar imagery.
- either method is more accurate than SIFT.
- either method solves lunar illumination changes.
- either method solves cross-sensor correspondence.
- either method should replace the current baseline.
- either method should be used in production.

All such conclusions require controlled experiments and independent evaluation.

---

## 2. Core Research Question

The central research question is:

> **Can illumination- and structure-focused representations such as RIFT and CFOG provide more reliable lunar image correspondences than raw grayscale representations under strong illumination, appearance, and cross-sensor differences?**

This question should be decomposed into measurable sub-questions:

1. Does structural representation improve correspondence stability?
2. Does it reduce sensitivity to illumination changes?
3. Does it improve matching when shadows change?
4. Does it improve spatial coverage?
5. Does it improve geometric verification?
6. Does it reduce independent registration error?
7. How does CFOG compare with the existing gradient representation experiment?
8. Does RIFT provide additional robustness beyond simpler gradient/structural representations?
9. How do these methods behave across OHRC and TMC-2?
10. Can they support IIRS-derived 2D representations?
11. What computational cost do they introduce?
12. Which lunar image conditions cause them to fail?

The answer should be established from ChandraMap experiments rather than inferred from performance on unrelated remote-sensing datasets.

---

## 3. ChandraMap Context

ChandraMap is a lunar image correspondence and registration research system focused on aligning imagery of the same lunar region acquired under different:

- instruments,
- spatial resolutions,
- illumination conditions,
- viewing conditions,
- and potentially different sensing modalities.

Relevant project imagery includes:

- Chandrayaan-2 OHRC,
- Chandrayaan-2 TMC-2,
- Chandrayaan-2 IIRS.

The project follows a versioned, benchmark-driven research approach emphasizing:

- measurable experiments,
- reproducibility,
- explicit ground truth,
- geometric verification,
- quantitative evaluation,
- controlled comparisons,
- scientific validity,
- separation of research from production.

RIFT/CFOG research belongs to the broader **representation and correspondence robustness** direction.

---

## 4. Current ChandraMap Foundation

The current research progression includes:

```text
Known Source / Reference Pair
            ↓
SIFT Baseline
            ↓
Reference-Image Scale Pyramid
            ↓
Gradient / Structural Representation
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

Relevant established research includes:

- `experiments/v1/baseline/EXP-001-sift-baseline/README.md`
- `experiments/v1/preprocessing/EXP-002-scale-pyramid/README.md`
- `experiments/v1/preprocessing/EXP-003-gradient-representation/README.md`
- `experiments/v1/geometry/EXP-004-affine-vs-homography/README.md`
- `experiments/v1/geometry/EXP-005-residual-analysis/README.md`
- `experiments/v1/refinement/EXP-006-subpixel-refinement/README.md`

RIFT/CFOG should therefore be investigated as extensions of the existing structure-focused research rather than as isolated algorithms.

---

# 5. Why Illumination and Radiation Robustness Matters

Lunar images can differ substantially because of illumination geometry.

Changes in illumination can affect:

- local intensity,
- contrast,
- shadow extent,
- shadow direction,
- visible surface structure,
- apparent boundaries,
- local feature strength.

A simple brightness transformation cannot reproduce these changes.

For example:

```text
Same terrain
     ↓
Different Sun geometry
     ↓
Different shadow position and extent
     ↓
Different local image appearance
```

Therefore:

> **Brightness normalization is not equivalent to illumination invariance.**

Likewise, contrast normalization cannot move a shadow boundary back to its previous physical location.

This is directly relevant to:

`research/notes/illumination-invariance.md`

and motivates investigation of representations that emphasize structural information rather than raw intensity.

---

## 6. Raw Intensity vs Structural Representation

### Raw Grayscale

```text
Image
 ↓
Intensity
 ↓
Local Features / Matching
```

Raw intensity directly represents image brightness.

This can be useful when corresponding regions preserve similar radiometric appearance.

However, under strong illumination or modality changes:

```text
Same physical structure
        ↓
Different intensity
        ↓
Reduced direct similarity
```

---

### Gradient / Structural Representation

```text
Image
 ↓
Gradient / Structural Information
 ↓
Features / Matching
```

This shifts the representation toward:

- local edges,
- orientation,
- structural transitions,
- local shape information.

This is the direction already explored by:

`EXP-003-gradient-representation`

---

### RIFT-Style Representation

RIFT uses phase congruency rather than ordinary image intensity for feature detection and uses a maximum-index representation derived from log-Gabor responses for description. The original RIFT research was specifically designed for multimodal matching under nonlinear radiation differences. ([PubMed][1])

Conceptually:

```text
Image
 ↓
Phase Congruency
 ↓
Feature Detection
 ↓
Log-Gabor Responses
 ↓
Maximum Index Map
 ↓
Local Description
 ↓
Matching
```

---

### CFOG-Style Representation

CFOG is a pixel-wise structural representation based on oriented-gradient information. The published framework extends pixel-wise HOG-style representation and combines the resulting feature representation with frequency-domain matching for multimodal remote-sensing registration. ([DOI][2])

Conceptually:

```text
Image
 ↓
Oriented Gradients
 ↓
Pixel-wise Feature Representation
 ↓
Structural Similarity
 ↓
Correspondence / Registration
```

CFOG and RIFT therefore belong to related structural-robustness research, but they are **not interchangeable algorithms**.

---

# 7. RIFT at a Research Level

RIFT stands for **Radiation-Variation Insensitive Feature Transform**.

The published method was designed for multimodal image matching under nonlinear radiation differences. Its principal ideas include:

- phase congruency for feature detection,
- detection of both corner and edge features,
- a maximum index map derived from log-Gabor filter responses for description,
- rotation handling through analysis of the descriptor representation. ([PubMed][1])

The important research idea for ChandraMap is the shift from direct intensity dependence toward image structures that may be less sensitive to radiometric differences.

RIFT was evaluated by its authors across several multimodal remote-sensing image types, including optical-optical, infrared-optical, SAR-optical, depth-optical, map-optical, and day-night imagery. Those results provide motivation for investigation, but they do **not** establish performance on lunar imagery. ([ResearchGate][3])

---

## 8. RIFT Research Relevance to ChandraMap

RIFT is relevant because ChandraMap may encounter:

- different illumination,
- nonlinear appearance changes,
- different sensor responses,
- different spatial resolutions,
- potentially different sensing modalities.

A potential research hypothesis is:

> Representations based on phase congruency and related structural information may preserve useful correspondence information when intensity relationships between lunar images become unreliable.

This remains a hypothesis.

RIFT should not be described as automatically illumination-invariant for every lunar scene.

---

# 9. CFOG at a Research Level

CFOG stands for **Channel Features of Oriented Gradients**.

The published CFOG framework represents an image using pixel-wise oriented-gradient features. The method was proposed for multimodal remote-sensing image registration and extends the idea of pixel-wise HOG-like representations. The associated framework uses frequency-domain similarity and template matching to locate correspondences. ([DOI][2])

The central idea relevant to ChandraMap is:

```text
Raw intensity
       ↓
Oriented gradient channels
       ↓
Structural representation
       ↓
Similarity / correspondence
```

This can potentially reduce direct dependence on absolute intensity values.

However, ChandraMap must determine experimentally whether this representation provides an advantage over the simpler gradient representation already under investigation.

---

# 10. RIFT and CFOG Are Distinct

RIFT and CFOG should not be treated as two names for the same method.

| Aspect                  | RIFT                                                | CFOG                                              |
| ----------------------- | --------------------------------------------------- | ------------------------------------------------- |
| Primary idea            | Radiation-variation-robust local feature matching   | Pixel-wise oriented-gradient representation       |
| Structural basis        | Phase congruency + log-Gabor-derived representation | Oriented gradient channels                        |
| Feature philosophy      | Local feature detection + description               | Dense/pixel-wise structural representation        |
| Main motivation         | Nonlinear radiation / multimodal differences        | Structural similarity for multimodal registration |
| Correspondence strategy | Local feature correspondence                        | Representation-based similarity / matching        |
| ChandraMap role         | Future research candidate                           | Future research candidate                         |
| Current status          | `[Not implemented]`                                 | `[Not implemented]`                               |

The exact implementation selected for ChandraMap must be documented separately.

---

# 11. RIFT vs CFOG vs Existing Research

The relationship to the current ChandraMap representation research is:

```text
Raw Grayscale
      ↓
Gradient / Structural Representation
      ↓
CFOG-style Representation
      ↓
Phase-Congruency / RIFT-style Representation
```

This is a conceptual research progression, not a mandatory implementation order.

The important question is:

> **Does the added complexity of RIFT or CFOG produce measurable benefit over simpler representations?**

If a simpler gradient representation performs equally well on the relevant benchmark, a more complicated representation may not provide sufficient research justification for integration.

---

# 12. Relationship to SIFT

SIFT remains the established classical V1 baseline.

SIFT uses local intensity/gradient-derived information and provides a strong reference point for evaluating alternative representations.

The comparison should therefore be:

```text
SIFT
  ↓
Established baseline

RIFT
  ↓
Radiation / phase-congruency research candidate

CFOG
  ↓
Gradient-structure research candidate
```

The experiment should not assume either alternative will outperform SIFT.

---

# 13. Relationship to ALIKED

ALIKED represents a learned local feature direction.

RIFT/CFOG represent structure-focused, non-identical alternatives.

Conceptually:

```text
SIFT
│
├── Classical local feature baseline
│
ALIKED
│
├── Learned local feature direction
│
RIFT
│
├── Phase-congruency / radiation-robust local feature direction
│
CFOG
│
└── Dense oriented-gradient structural representation
```

This separation is useful because the research questions differ.

### ALIKED asks:

> Can a learned local feature representation improve correspondence?

### RIFT asks:

> Can phase-congruency and radiation-robust local representations improve correspondence?

### CFOG asks:

> Can a pixel-wise oriented-gradient structural representation improve multimodal registration?

---

# 14. Relationship to LightGlue

LightGlue is a matcher, not a replacement for the representation itself.

A future research system could conceptually investigate:

```text
ALIKED → LightGlue
```

whereas RIFT and CFOG require their own appropriate matching mechanisms.

A fair experiment should not compare:

```text
RIFT + specialized matcher
```

against:

```text
SIFT + baseline matcher
```

and attribute every difference to RIFT.

Matcher and representation effects must be separated where practical.

---

# 15. Relationship to LoFTR

LoFTR represents a different learned correspondence direction.

Conceptually:

```text
RIFT / CFOG
→ structure-focused correspondence

ALIKED
→ learned local features

LightGlue
→ learned local feature matching

LoFTR
→ learned detector-free correspondence
```

These approaches should have separate controlled experiments.

The purpose of RIFT/CFOG research is not to establish that one correspondence paradigm is universally superior.

---

# 16. Research Hypotheses

## H1 — Structural Robustness

RIFT/CFOG-style representations may provide more stable correspondence under strong radiometric differences than raw grayscale matching.

## H2 — Illumination Robustness

Structure-focused representations may reduce sensitivity to some illumination-induced appearance changes.

This does not imply that they can reconstruct changed shadow geometry.

## H3 — Cross-Sensor Robustness

RIFT/CFOG-style representations may provide useful correspondences when sensor intensity relationships differ substantially.

## H4 — Spatial Coverage

A structural representation may produce more spatially useful correspondences than raw intensity under selected difficult conditions.

## H5 — Registration Accuracy

Improved correspondence quality may translate into lower independent registration error.

This must be measured.

## H6 — Complexity Trade-off

Any robustness improvement may come with additional computational or implementation complexity.

The benchmark must measure whether that additional complexity is justified.

---

# 17. Experimental Identity

The first controlled investigation can be organized as:

**`RIFT-CFOG-EXP-001` — Structural and Radiation-Robust Representation Evaluation**

Primary comparison:

```text
SIFT / Existing Representation
        vs
Gradient Representation
        vs
CFOG-style Representation
        vs
RIFT-style Representation
```

The exact experiment decomposition may later be split into separate experiment IDs if the implementation becomes sufficiently complex.

---

# 18. Experimental Objective

The primary objective is:

> Determine whether RIFT- or CFOG-style structural representations provide measurable improvements in lunar image correspondence and registration under controlled illumination, appearance, scale, and sensor differences.

Secondary objectives include:

- determining where simpler gradient representations are sufficient,
- identifying failure modes,
- measuring computational cost,
- evaluating spatial coverage,
- testing cross-sensor conditions,
- determining whether further implementation is justified.

---

# 19. Experimental Controls

The following should remain controlled where practical:

- image pair,
- image crop,
- image resolution,
- preprocessing,
- benchmark split,
- ground-truth points,
- independent check points,
- geometric model,
- RANSAC configuration,
- evaluation metrics,
- runtime hardware.

Only the major representation/method variable should change during a basic comparison.

---

# 20. Staged Experimental Progression

## Stage 1 — Reproduce SIFT Baseline

Run the established SIFT baseline.

Record:

- keypoints,
- candidate matches,
- verified inliers,
- spatial coverage,
- transformation,
- residuals,
- independent checkpoint error,
- runtime.

---

## Stage 2 — Reproduce Existing Gradient Experiment

Use the established:

`EXP-003-gradient-representation`

configuration.

This provides the simplest structure-focused comparison.

---

## Stage 3 — CFOG-Style Representation

Implement or integrate a documented CFOG-style representation.

Record:

- representation configuration,
- orientation configuration,
- spatial representation parameters,
- matching mechanism,
- runtime.

The exact implementation details remain:

`[TBD]`

until selected and documented.

---

## Stage 4 — RIFT-Style Representation

Evaluate a documented RIFT implementation or controlled reimplementation.

Record:

- phase-congruency configuration,
- filter configuration,
- feature detection configuration,
- descriptor configuration,
- matching configuration,
- runtime.

Any implementation detail not established in the project should remain:

`[TBD]`

---

## Stage 5 — Geometric Verification

Apply the established geometric verification pipeline.

Conceptually:

```text
Representation
      ↓
Candidate Correspondences
      ↓
RANSAC
      ↓
Verified Inliers
      ↓
Transformation
```

---

## Stage 6 — Independent Evaluation

Use independent ground-truth/check-point information.

Measure:

- checkpoint RMSE,
- median error,
- error distribution,
- spatial coverage,
- failure rate.

---

## Stage 7 — Illumination Stress Testing

Evaluate increasingly difficult illumination differences.

---

## Stage 8 — Cross-Sensor Evaluation

Evaluate supported sensor combinations.

---

## Stage 9 — Decision

Determine whether the evidence supports additional research or implementation.

---

# 21. Candidate Matches vs Verified Correspondences

The ChandraMap distinction must remain explicit:

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

A candidate match is a proposed correspondence.

A verified inlier is a candidate correspondence that is consistent with the selected geometric model.

Neither is automatically ground truth.

The benchmark must not treat:

- raw descriptor similarity,
- number of matches,
- RANSAC inlier count,

as equivalent to registration accuracy.

---

# 22. Ground Truth

RIFT/CFOG evaluation should use the project's ground-truth principles.

The evaluation chain should be:

```text
Candidate Correspondences
        ↓
Transformation Fitting
        ↓
Estimated Registration
        ↓
Independent Ground Truth
        ↓
Registration Error
```

Independent check points should not be used to fit the transformation when they are intended for unbiased evaluation.

The same evaluation points should be used across methods wherever possible.

---

# 23. Feature-Level Metrics

Potential feature/representation metrics include:

- number of detected features,
- feature distribution,
- repeatability,
- candidate correspondence count,
- descriptor/representation similarity,
- verified inlier count,
- inlier ratio,
- spatial coverage.

However:

> **More features do not necessarily mean better registration.**

Likewise:

> **More inliers do not necessarily mean better global registration.**

Spatial distribution and independent evaluation remain essential.

---

# 24. Registration-Level Metrics

The primary registration evaluation should include:

- reprojection error,
- independent checkpoint RMSE,
- median checkpoint error,
- error percentiles,
- verified inlier count,
- inlier ratio,
- spatial coverage,
- registration success rate,
- runtime,
- memory/resource usage.

Feature-level metrics should be treated as diagnostic evidence.

Registration-level independent evaluation should determine whether the representation provides practical value.

---

# 25. Spatial Coverage

Spatial coverage is particularly important for structure-based methods.

A representation may generate many correspondences around:

- crater rims,
- high-contrast boundaries,
- shadow edges,
- strong terrain structures.

These features may be locally reliable but geographically concentrated.

A transformation estimated from clustered points may fail elsewhere.

Therefore evaluate:

- feature density,
- correspondence distribution,
- inlier distribution,
- grid coverage,
- convex-hull coverage,
- edge concentration,
- regional support.

No universal coverage threshold should be introduced without benchmark evidence.

---

# 26. Illumination Evaluation

Illumination experiments should not be reduced to:

```text
Brightness + constant
```

or:

```text
Contrast × constant
```

because lunar illumination changes can modify shadow geometry.

A more realistic conceptual condition is:

```text
Same lunar terrain
        ↓
Different illumination geometry
        ↓
Different shadow structure
        ↓
Different local appearance
```

The experiment should distinguish:

### Radiometric variation

Changes in brightness, contrast, or intensity relationship.

### Geometric appearance variation

Changes caused by shadows and illumination geometry.

A method may be robust to the first while still struggling with the second.

---

# 27. Shadow-Related Evaluation

Shadow boundaries require particular care.

A shadow edge in one image may not represent the same physical boundary in another image if illumination has changed.

Therefore:

> A method that matches shadow edges successfully is not automatically demonstrating physical terrain correspondence.

Evaluation should determine whether correspondences are associated with stable surface structures or transient illumination structures.

Where possible, benchmark analysis should categorize:

- morphology-dominated regions,
- shadow-dominated regions,
- mixed regions.

This categorization should be documented rather than inferred after seeing results.

---

# 28. RIFT and Phase Congruency

Phase congruency is a structural image concept based on local phase relationships.

The RIFT paper uses phase congruency for feature detection instead of relying directly on intensity. It then uses a maximum-index representation from log-Gabor responses for feature description. ([PubMed][1])

For ChandraMap, the research hypothesis is that such representations may retain useful structural information when intensity relationships are unstable.

However, phase congruency should not be treated as a guarantee of:

- perfect illumination invariance,
- perfect shadow invariance,
- perfect modality invariance,
- perfect geometric invariance.

Those properties must be evaluated experimentally.

---

# 29. RIFT and Rotation

RIFT explicitly analyzes rotation effects on its maximum-index-map representation and incorporates rotation handling. ([ResearchGate][3])

For ChandraMap, rotation should still be treated as an experimental condition rather than assumed to be completely solved.

Relevant conditions include:

- image orientation differences,
- map-projection differences,
- local geometric rotation,
- larger geometric distortions.

The experiment should separate:

```text
Rotation robustness
```

from:

```text
Scale robustness
```

and:

```text
General registration robustness
```

---

# 30. CFOG and Structural Representation

CFOG represents oriented-gradient information in a pixel-wise feature representation. The published method was designed for multimodal remote-sensing registration and uses frequency-domain matching to improve computational efficiency. ([DOI][2])

For ChandraMap, the key question is whether such a structural representation improves correspondence compared with the existing gradient representation.

The comparison should therefore include:

```text
Raw Grayscale
      ↓
Gradient Representation
      ↓
CFOG-style Representation
```

This avoids assuming that adding a more elaborate representation automatically produces better registration.

---

# 31. CFOG vs Existing Gradient Experiment

The most important early CFOG experiment should compare it directly with:

`EXP-003-gradient-representation`

Questions include:

1. Does CFOG preserve more useful structural information?
2. Does it improve candidate correspondence quality?
3. Does it improve geometric verification?
4. Does it improve spatial coverage?
5. Does it reduce independent registration error?
6. What additional computational cost does it introduce?

If the simpler gradient representation performs similarly, the incremental value of CFOG must be carefully justified.

---

# 32. RIFT vs Existing Gradient Experiment

Similarly:

```text
Gradient Representation
        vs
RIFT-Style Representation
```

should be compared under controlled conditions.

This isolates the question:

> Does phase-congruency-based representation provide measurable benefit beyond ordinary gradient-based structural information?

---

# 33. Scale Robustness

RIFT/CFOG research should connect with:

`research/notes/scale-invariance.md`

and:

`EXP-002-scale-pyramid`.

Potential conditions include:

- native resolution,
- controlled downsampling,
- upsampling,
- reference-image pyramid,
- different source/reference resolutions.

The experiment should distinguish:

```text
Representation robustness to scale
```

from:

```text
Complete registration robustness to scale
```

No method should be assumed to solve scale differences merely because it uses structural information.

---

# 34. Cross-Sensor Evaluation

Potential future combinations include:

```text
OHRC ↔ TMC-2
OHRC ↔ IIRS-derived 2D representation
TMC-2 ↔ IIRS-derived 2D representation
```

where supported by the available project data and research roadmap.

Cross-sensor evaluation introduces:

- different spatial resolutions,
- different sensor responses,
- different contrast,
- different modality characteristics,
- different preprocessing requirements.

The benchmark should therefore report results by sensor pair rather than only aggregate all pairs.

---

# 35. IIRS Relationship

The relationship to:

`research/future/IIRS.md`

should remain modular.

Potential IIRS-derived 2D inputs include:

- selected spectral bands,
- band combinations,
- PCA-derived representations,
- structural representations,
- gradient representations.

A conceptual pipeline is:

```text
IIRS
 ↓
2D Representation
 ↓
RIFT / CFOG
 ↓
Correspondence
 ↓
Geometric Verification
 ↓
Registration
```

The representation and matching questions should remain separate.

RIFT or CFOG should not be presented as a solution to the underlying IIRS representation-selection problem.

---

# 36. Geometric Model

RIFT/CFOG experiments should connect with:

`experiments/v1/geometry/EXP-004-affine-vs-homography/`

The initial representation comparison should preferably use a fixed geometric model.

Potential models include:

- affine,
- homography.

The model should be treated as a separate variable.

A representation that produces more geometrically consistent matches should not be penalized by silently changing the transformation model between methods.

Likewise, a flexible transformation should not be used to conceal poor correspondence quality.

---

# 37. Residual Analysis

The experiments should connect with:

`experiments/v1/geometry/EXP-005-residual-analysis/`

Residual analysis should determine:

- where errors occur,
- whether residuals cluster,
- whether errors increase toward boundaries,
- whether residual vectors have systematic direction,
- whether the transformation explains local and global structure,
- whether structural representations produce more stable geometric support.

A single RMSE value is insufficient to characterize these effects.

---

# 38. Sub-pixel Refinement

The experiments should connect with:

`experiments/v1/refinement/EXP-006-subpixel-refinement/`

The conceptual sequence remains:

```text
RIFT / CFOG Representation
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

Sub-pixel refinement must not be used to compensate for incorrect correspondence.

The refinement stage should remain downstream of correspondence quality.

---

# 39. Computational Cost

The research should measure:

### Preprocessing

- representation-generation time,
- image filtering time,
- phase-congruency computation where applicable.

### Feature extraction

- detection time,
- description time,
- feature-map generation.

### Matching

- correspondence search,
- similarity computation,
- template matching where applicable.

### Geometry

- RANSAC,
- transformation estimation,
- residual computation.

### End-to-end

- total runtime,
- memory consumption,
- storage requirements,
- hardware requirements.

Published computational performance should not be copied and presented as ChandraMap performance.

---

# 40. Implementation Complexity

RIFT/CFOG-style methods may introduce additional parameters compared with a simple grayscale baseline.

Potential configuration dimensions include:

### RIFT

- phase-congruency parameters,
- log-Gabor filter parameters,
- feature detection parameters,
- descriptor configuration,
- rotation handling,
- matching strategy.

### CFOG

- gradient computation,
- orientation channels,
- spatial aggregation,
- feature representation parameters,
- similarity measure,
- matching strategy.

Exact ChandraMap values remain:

`[TBD]`

until implementation.

---

# 41. Failure Modes

## 41.1 Weak structural response

Some lunar regions may contain insufficient stable structure.

**Detection:**

- low feature density,
- poor repeatability,
- poor spatial coverage.

---

## 41.2 Shadow-dominated correspondence

A method may strongly respond to shadows rather than stable terrain.

**Detection:**

- visual overlays,
- illumination-specific subsets,
- residual analysis,
- correspondence classification.

---

## 41.3 Repetitive terrain

Similar structures may produce ambiguous matches.

**Detection:**

- candidate-match analysis,
- RANSAC rejection,
- independent checkpoint error.

---

## 41.4 Sparse features

A structural representation may suppress useful low-contrast regions.

**Detection:**

- keypoint/feature density maps,
- regional coverage,
- registration failure analysis.

---

## 41.5 Excessive edge dependence

A representation may overemphasize strong boundaries.

**Detection:**

- feature distribution,
- boundary concentration,
- regional error analysis.

---

## 41.6 Cross-sensor structural mismatch

Structures may appear differently across sensors.

**Detection:**

- sensor-pair breakdown,
- correspondence quality,
- independent registration error.

---

## 41.7 Scale sensitivity

Structural representations can still change with image resolution.

**Detection:**

- controlled scale experiments,
- multi-resolution benchmark subsets.

---

## 41.8 Rotation sensitivity

The representation or matching stage may behave differently under orientation changes.

**Detection:**

- controlled rotation conditions,
- image-pair metadata,
- registration success by rotation condition.

---

## 41.9 False geometric consensus

Incorrect correspondences may form a plausible transformation.

**Detection:**

- independent check points,
- residual maps,
- spatial coverage,
- held-out evaluation.

---

## 41.10 Computational cost

A more robust representation may be substantially slower.

**Detection:**

- feature-generation runtime,
- matching runtime,
- total runtime,
- memory consumption.

---

## 41.11 Parameter sensitivity

Performance may depend strongly on implementation parameters.

**Detection:**

- controlled parameter sweeps,
- sensitivity analysis,
- repeated benchmark runs.

Parameter tuning must not use independent test data.

---

# 42. Spatial Coverage

Spatial coverage must remain a first-class metric.

A large number of correspondences may still be concentrated around:

- crater rims,
- strong edges,
- shadow boundaries,
- high-contrast terrain.

Therefore report:

- feature distribution,
- candidate-match distribution,
- verified-inlier distribution,
- region-level coverage,
- clustering,
- convex-hull coverage,
- grid coverage where appropriate.

No specific coverage threshold should be introduced without experimental justification.

---

# 43. Registration Error

For predicted point:

$$
\hat{p}_i = T(p_i)
$$

and independent reference point:

$$
p_i^*
$$

the residual is:

$$
e_i =
\left\|
\hat{p}_i-p_i^*
\right\|_2
$$

and RMSE is:

$$
RMSE =
\sqrt{
\frac{1}{N}
\sum_{i=1}^{N}
e_i^2
}
$$

The coordinate system must be stated.

For image-coordinate evaluation, this is normally expressed in pixels.

Conversion to physical units should only be performed when the necessary spatial metadata and uncertainty justify it.

---

# 44. Error Distribution

In addition to RMSE, report where appropriate:

- median error,
- mean error,
- error percentiles,
- maximum error,
- failure rate,
- spatial error distribution.

RMSE alone can hide:

- outliers,
- localized failures,
- systematic spatial patterns.

---

# 45. Benchmark Design

A useful benchmark should include controlled variation in:

### Illumination

- similar illumination,
- moderate illumination difference,
- strong illumination difference.

Exact categories remain:

`[TBD]`

### Scale

- similar resolution,
- moderate scale difference,
- strong scale difference.

### Sensor

- same-sensor pairs,
- cross-sensor pairs where appropriate.

### Terrain

Where data permit:

- crater-dominated regions,
- smooth regions,
- rugged terrain,
- shadow-heavy regions,
- low-texture regions.

These categories should be assigned from documented image characteristics rather than created only after inspecting results.

---

# 46. Benchmark Metadata

Each pair should record, where available:

```text
pair_id
source_image_id
target_image_id
source_sensor
target_sensor
source_representation
target_representation
image_dimensions
resolution_or_gsd
illumination_metadata
scale_condition
overlap_information
coordinate_convention
ground_truth_version
ground_truth_provenance
fit_or_check_definition
benchmark_version
```

Unknown information should be explicitly recorded as:

`[Not provided]`

rather than guessed.

---

# 47. Fair Comparison

For a basic representation comparison, use:

- same source/reference pair,
- same preprocessing where possible,
- same resolution,
- same benchmark split,
- same ground-truth points,
- same independent check points,
- same geometric model,
- same RANSAC configuration,
- same success criteria,
- same hardware for runtime comparisons.

When a method requires a different processing pipeline, the difference must be documented.

---

# 48. Ablation Structure

A useful future ablation matrix is:

| Experiment | Representation | Feature/Matching Strategy                  | Geometry |
| ---------- | -------------- | ------------------------------------------ | -------- |
| A          | Raw grayscale  | SIFT baseline                              | Fixed    |
| B          | Gradient       | Existing gradient experiment               | Fixed    |
| C          | CFOG-style     | CFOG matching strategy                     | Fixed    |
| D          | RIFT-style     | RIFT feature matching                      | Fixed    |
| E          | RIFT-style     | Controlled alternative matcher where valid | Fixed    |
| F          | CFOG-style     | Controlled alternative matcher where valid | Fixed    |

The exact combinations should depend on technical compatibility.

Not every representation supports the same matcher architecture.

Fair comparison does not require forcing incompatible algorithms into an artificial identical interface.

---

# 49. Representation vs Matcher Separation

The experiment should explicitly identify:

```text
Representation
       ↓
Matching
       ↓
Geometry
```

A representation improvement should not be confused with a matcher improvement.

For example:

```text
RIFT + Matcher A
```

versus:

```text
Gradient + Matcher B
```

changes two variables.

A controlled comparison should first isolate the representation as much as possible.

---

# 50. RIFT/CFOG vs Raw Grayscale

The fundamental comparison should be:

```text
Raw Intensity
      vs
Structural Representation
```

The goal is not to prove that structural representations are universally superior.

The goal is to determine:

> Under which lunar image conditions does structural information provide measurable correspondence benefits?

Possible outcomes include:

- structural representation helps,
- structural representation does not help,
- structural representation helps only under specific conditions,
- structural representation helps but costs too much,
- RIFT helps while CFOG does not,
- CFOG helps while RIFT does not,
- simpler gradients perform similarly.

All outcomes are scientifically useful.

---

# 51. RIFT/CFOG vs Illumination Normalization

Illumination normalization should remain a separate variable.

The benchmark may eventually compare:

```text
Raw Grayscale
      ↓
Normalization
```

against:

```text
Raw Grayscale
      ↓
Structural Representation
```

and potentially:

```text
Normalized Image
      ↓
Structural Representation
```

However, these should be staged experiments.

Changing both normalization and representation simultaneously makes attribution difficult.

---

# 52. Important Physical Limitation

RIFT/CFOG-style structural representations may reduce sensitivity to intensity changes.

They cannot guarantee recovery of information that is physically absent or occluded.

For example:

```text
Different illumination
        ↓
Different shadow geometry
        ↓
Surface region hidden in one image
```

No intensity normalization or descriptor can guarantee a correct correspondence when the relevant surface structure is not visible.

This is a physical limitation rather than simply an algorithmic limitation.

---

# 53. Ground Truth Quality

Ground truth should be treated as:

> **Reference information with documented provenance and uncertainty.**

The benchmark should not assume that a manually identified point is exact.

Potential metadata include:

- source point,
- target point,
- uncertainty,
- provenance,
- confidence,
- feature type,
- annotation method,
- fit/check status.

This is especially important for:

- low-resolution imagery,
- cross-sensor imagery,
- shadowed regions,
- ambiguous terrain.

---

# 54. Independent Check Points

The transformation should be fitted using one set of correspondences and evaluated using independent points where possible.

Conceptually:

```text
Fit Points
    ↓
Transformation
    ↓
Independent Check Points
    ↓
Registration Error
```

Do not evaluate the model on the same points used to fit it and call that independent accuracy.

---

# 55. Residual Analysis

Residual analysis should distinguish between:

### Random error

Small, non-systematic deviations.

### Systematic error

Residual patterns that indicate:

- transformation mismatch,
- spatial distortion,
- projection effects,
- feature bias.

### Local failure

A region where correspondences fail despite broader registration success.

### Global failure

A transformation that does not correctly explain the image relationship.

These distinctions are important when comparing RIFT/CFOG against simpler representations.

---

# 56. Research Artifacts

A future implementation may produce:

- experiment README,
- configuration files,
- representation maps,
- phase-congruency visualizations,
- CFOG feature maps,
- keypoint maps,
- correspondence visualizations,
- RANSAC/inlier visualizations,
- spatial coverage plots,
- residual plots,
- checkpoint-error reports,
- runtime tables,
- benchmark summaries,
- failure-case reports.

These artifacts are proposed.

They should not be represented as existing until generated.

---

# 57. Reproducibility Requirements

Every future RIFT/CFOG experiment should record:

- method name,
- implementation source,
- implementation version/commit,
- parameter configuration,
- image identifiers,
- preprocessing,
- image dimensions,
- representation configuration,
- matching configuration,
- geometric model,
- RANSAC parameters,
- random seeds where applicable,
- benchmark version,
- ground-truth version,
- hardware,
- operating system,
- software versions,
- dependency versions,
- output artifacts.

A generic label such as `RIFT` or `CFOG` is insufficient for exact reproduction.

---

# 58. Proposed Experiment Configuration

A future experiment could use a structure such as:

```yaml
experiment_id: RIFT-CFOG-EXP-001

baseline:
  method: SIFT
  configuration: "[existing ChandraMap configuration]"

representations:
  - name: gradient
    configuration: "[existing EXP-003 configuration]"
  - name: CFOG
    configuration: "[TBD]"
  - name: RIFT
    configuration: "[TBD]"

dataset:
  source_images: "[TBD]"
  reference_images: "[TBD]"
  benchmark_split: "[TBD]"

preprocessing:
  configuration: "[TBD]"

matching:
  method: "[TBD]"
  configuration: "[TBD]"

geometry:
  model: "[TBD]"
  verification: RANSAC
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
  hardware: "[TBD]"
  software_environment: "[TBD]"
  random_seed: "[TBD]"
```

This is a proposed research configuration, not an existing project configuration.

---

# 59. Failure Analysis Protocol

Every failed pair should be categorized where possible.

Suggested categories:

```text
NO_FEATURES
LOW_COVERAGE
AMBIGUOUS_MATCHES
WRONG_CORRESPONDENCES
RANSAC_FAILURE
GEOMETRIC_MODEL_MISMATCH
ILLUMINATION_FAILURE
SHADOW_DOMINANCE
SCALE_FAILURE
CROSS_SENSOR_FAILURE
GROUND_TRUTH_AMBIGUITY
RESOURCE_FAILURE
OTHER
```

These labels are proposed and should be adapted to the final benchmark.

The original failed data should be retained.

---

# 60. What Would Count as Improvement?

An improvement should be considered meaningful only when supported by multiple independent observations.

Potential evidence includes:

- lower independent checkpoint error,
- improved correspondence repeatability,
- higher-quality verified correspondences,
- improved spatial coverage,
- better performance under difficult illumination,
- better cross-sensor behavior,
- acceptable runtime,
- stable performance across multiple scenes.

No single metric should determine the conclusion.

---

# 61. What Would Not Count as Sufficient Evidence?

The following should not independently justify adopting RIFT or CFOG:

- more detected features,
- more candidate matches,
- higher RANSAC inlier count alone,
- better performance on one image pair,
- external benchmark results,
- visually convincing overlays,
- lower error on fitting points,
- a more complicated representation without measurable improvement.

---

# 62. External Research Evidence

The published RIFT work reports evaluation across multiple multimodal remote-sensing conditions and motivates phase-congruency and maximum-index representations for nonlinear radiation differences. ([PubMed][1])

The published CFOG work describes a pixel-wise oriented-gradient representation for multimodal remote-sensing registration and reports experiments comparing it with other feature representations and matching methods. ([DOI][2])

These publications establish that the methods are legitimate research directions for multimodal/remote-sensing image matching.

They do **not** establish:

- lunar performance,
- ChandraMap performance,
- OHRC performance,
- TMC-2 performance,
- IIRS performance,
- ChandraMap benchmark accuracy,
- ChandraMap runtime.

Those must be experimentally established.

---

# 63. Research Status

| Component               | Status                         | Evidence / Next Step                            |
| ----------------------- | ------------------------------ | ----------------------------------------------- |
| SIFT baseline           | Existing baseline              | Use as controlled reference                     |
| Gradient representation | Existing V1 research direction | Use as simpler structural reference             |
| RIFT investigation      | Proposed                       | Design controlled experiment                    |
| RIFT implementation     | `[Not implemented]`            | Future work                                     |
| RIFT benchmark          | `[Not evaluated]`              | Requires lunar benchmark                        |
| CFOG investigation      | Proposed                       | Compare against simpler gradient representation |
| CFOG implementation     | `[Not implemented]`            | Future work                                     |
| CFOG benchmark          | `[Not evaluated]`              | Requires lunar benchmark                        |
| Cross-sensor RIFT/CFOG  | Future research                | Requires suitable benchmark                     |
| IIRS + RIFT/CFOG        | Future research                | Requires IIRS representation study              |
| Production integration  | Deferred                       | Requires experimental evidence                  |

---

# 64. Decision Criteria

The research should ask:

### Correspondence

- Does the representation improve correspondence stability?
- Does it increase useful verified correspondences?
- Does it improve spatial coverage?

### Illumination

- Does it improve performance under realistic illumination differences?
- Does it remain useful when shadow geometry changes?

### Scale

- Does performance remain stable across resolution differences?

### Cross-sensor

- Does it improve correspondence across appropriate sensor pairs?

### Accuracy

- Does it reduce independent checkpoint error?

### Practicality

- What is the computational cost?
- What are the implementation dependencies?
- Is the method reproducible?

### Scientific value

- Does it outperform simpler representations under the conditions that matter to ChandraMap?
- Is the improvement consistent across scenes?

---

# 65. Possible Research Outcomes

The experiment may produce any of the following outcomes.

### Outcome A — RIFT demonstrates measurable benefit

This could justify a larger RIFT investigation.

### Outcome B — CFOG demonstrates measurable benefit

This could justify further CFOG research.

### Outcome C — Both provide limited benefit

The additional complexity may not justify integration.

### Outcome D — Benefits are condition-specific

The method may be useful only for particular:

- illumination conditions,
- sensor pairs,
- scene types,
- scale ranges.

This can still be scientifically valuable.

### Outcome E — Simpler gradient representation is sufficient

This would indicate that additional complexity may not provide enough benefit.

### Outcome F — Neither method is useful for ChandraMap

This is also a valid research outcome.

No outcome should be predetermined.

---

# 66. Relationship to Future Research Roadmap

RIFT/CFOG belongs to the future **illumination-, modality-, and structure-robust representation** direction.

A conceptual roadmap is:

```text
SIFT Baseline
      ↓
Gradient / Structural Representation
      ↓
CFOG-Style Research
      ↓
RIFT / Phase-Congruency Research
      ↓
Controlled Illumination Evaluation
      ↓
Cross-Sensor Evaluation
      ↓
Independent Registration Evaluation
      ↓
Complexity / Benefit Analysis
      ↓
Validation / Integration Decision
```

This roadmap is exploratory.

It does not imply that every method will be implemented.

---

# 67. Recommended Research Sequence

## Phase 1 — Baseline

Reproduce SIFT.

## Phase 2 — Simple structure

Reproduce the established gradient representation experiment.

## Phase 3 — CFOG

Test whether a richer oriented-gradient representation provides additional value.

## Phase 4 — RIFT

Test whether phase-congruency-based representation provides additional value.

## Phase 5 — Illumination stress testing

Use controlled and naturally occurring illumination differences.

## Phase 6 — Scale stress testing

Use controlled resolution differences.

## Phase 7 — Cross-sensor testing

Evaluate supported sensor combinations.

## Phase 8 — Independent evaluation

Measure checkpoint accuracy and residual behavior.

## Phase 9 — Computational evaluation

Measure runtime and resource requirements.

## Phase 10 — Decision

Determine whether either direction deserves further implementation.

---

# 68. Reproducibility Checklist

Before considering the research reproducible:

- [ ] Image identifiers are recorded.
- [ ] Sensor metadata are recorded.
- [ ] Image dimensions are recorded.
- [ ] Preprocessing is documented.
- [ ] Representation configuration is documented.
- [ ] RIFT implementation/configuration is documented if used.
- [ ] CFOG implementation/configuration is documented if used.
- [ ] Matching configuration is documented.
- [ ] Geometric model is documented.
- [ ] RANSAC configuration is documented.
- [ ] Ground-truth version is recorded.
- [ ] Independent check points are recorded.
- [ ] Benchmark split is fixed.
- [ ] Hardware is recorded.
- [ ] Software environment is recorded.
- [ ] Dependencies are recorded.
- [ ] Runtime measurement methodology is documented.
- [ ] Failed cases are retained.
- [ ] Results are traceable to a configuration.
- [ ] No independent test data were used for tuning.

---

# 69. Common Research Mistakes

### Mistake 1 — Calling structural representation automatically illumination invariant

Structural representations can reduce sensitivity to radiometric changes but cannot guarantee invariance to all illumination-induced geometric appearance changes.

### Mistake 2 — Treating shadows as stable terrain

A shadow boundary may move when illumination changes.

### Mistake 3 — Comparing only match counts

More matches do not necessarily mean better registration.

### Mistake 4 — Ignoring spatial coverage

A transformation supported by one small region may fail elsewhere.

### Mistake 5 — Comparing different geometric models

This introduces an uncontrolled variable.

### Mistake 6 — Comparing different preprocessing

Representation comparisons become confounded.

### Mistake 7 — Using RANSAC inliers as ground truth

Inliers participate in model fitting.

### Mistake 8 — Removing failed pairs

Failures are part of the research evidence.

### Mistake 9 — Importing external benchmark results

Remote-sensing benchmark performance does not establish lunar registration performance.

### Mistake 10 — Assuming cross-sensor success

A representation designed for multimodal imagery still requires lunar cross-sensor validation.

### Mistake 11 — Assuming structural information is always better than intensity

Some image pairs may contain useful radiometric information that structural representations discard.

### Mistake 12 — Adding complexity without an ablation

If CFOG or RIFT improves results, the experiment should identify which component caused the improvement.

---

# 70. Important Scientific Boundary

RIFT/CFOG research should not be framed as:

> "Intensity is bad and structure is good."

A more scientifically accurate framing is:

> Different representations preserve different information. The appropriate representation depends on the imaging conditions, sensor relationship, geometric differences, and registration objective.

Raw intensity may be useful when radiometric relationships are stable.

Gradient representations may help when structural transitions are more stable.

CFOG may provide a richer oriented-gradient representation.

RIFT may provide stronger radiation-robust structural information.

The benchmark must determine which behavior occurs for ChandraMap.

---

# 71. Open Research Questions

1. Does phase congruency improve lunar feature repeatability?
2. Does RIFT remain useful when lunar shadows change substantially?
3. Does CFOG improve over the existing gradient representation?
4. Does RIFT improve over the existing gradient representation?
5. Does either method improve independent checkpoint accuracy?
6. Does either method improve spatial coverage?
7. How do the methods behave across OHRC and TMC-2?
8. How do they behave on IIRS-derived representations?
9. Which representation works best in low-texture terrain?
10. Which representation fails in shadow-dominated regions?
11. How sensitive are the methods to scale?
12. How sensitive are they to rotation?
13. What computational cost do they introduce?
14. Which parameters have the greatest effect?
15. Can a simpler representation provide equivalent performance?
16. How should uncertainty be incorporated into evaluation?
17. Which failure modes dominate?
18. What benchmark size is sufficient?
19. Does the benefit justify implementation complexity?
20. Should either method progress into a later ChandraMap version?

---

# 72. Implementation Boundary

Until experimental evidence exists, RIFT and CFOG should remain within the future research layer.

Conceptually:

```text
Current ChandraMap V1
        │
        ├── SIFT baseline
        ├── Scale research
        ├── Gradient representation
        ├── Geometry research
        ├── Residual analysis
        └── Sub-pixel refinement
                    │
                    ▼
             Future Research
                    │
          ┌─────────┴─────────┐
          ↓                   ↓
       RIFT                 CFOG
          │                   │
          └─────────┬─────────┘
                    ↓
             Benchmark Evidence
                    ↓
             Integration Decision
```

Neither method should enter the production path solely because it is theoretically suited to multimodal registration.

---

# 73. Research Integrity Principle

The RIFT/CFOG investigation should follow the ChandraMap principle:

> **Build small. Measure honestly. Keep the failures.**

This means:

- preserve difficult image pairs,
- report failures,
- separate fitting from evaluation,
- measure spatial coverage,
- document uncertainty,
- avoid unsupported accuracy claims,
- avoid importing external benchmark results as lunar evidence,
- keep representation and matcher variables separate,
- compare against the simplest meaningful baseline,
- record computational cost,
- make every conclusion traceable to measured evidence.

---

# 74. Final Research Position

RIFT and CFOG are promising **future research directions** for ChandraMap because they focus on structural information and, in the case of RIFT, phase-congruency-based representations intended to reduce sensitivity to nonlinear radiometric differences. Published remote-sensing research provides motivation for studying these approaches in multimodal image matching and registration. ([PubMed][1])

However:

```text
Published Remote-Sensing Evidence
              ↓
Research Motivation
              ↓
ChandraMap Lunar Experiment
              ↓
Independent Ground-Truth Evaluation
              ↓
Failure Analysis
              ↓
Reproducibility Check
              ↓
Integration Decision
```

The current ChandraMap position should therefore remain:

```text
SIFT
→ Existing V1 baseline

Gradient Representation
→ Existing structure-focused research

CFOG
→ Future structural-representation candidate

RIFT
→ Future radiation/phase-congruency candidate

LightGlue
→ Future learned matcher

LoFTR
→ Future learned correspondence direction
```

No RIFT or CFOG performance conclusion should be made until the methods have been evaluated on the relevant ChandraMap lunar benchmark.

The central research objective is therefore:

> **Determine experimentally whether RIFT- or CFOG-style structural representations provide measurable, reproducible advantages for lunar image correspondence and registration under illumination, scale, and cross-sensor conditions, and whether those advantages justify their additional implementation complexity.**

[1]: https://pubmed.ncbi.nlm.nih.gov/31869789/?utm_source=chatgpt.com "RIFT: Multi-modal Image Matching Based on Radiation-variation Insensitive Feature Transform - PubMed"
[2]: https://doi.org/10.1109/TGRS.2019.2924684?utm_source=chatgpt.com "Fast and Robust Matching for Multimodal Remote Sensing Image Registration"
[3]: https://www.researchgate.net/publication/337993249_RIFT_Multi-Modal_Image_Matching_Based_on_Radiation-Variation_Insensitive_Feature_Transform?utm_source=chatgpt.com "(PDF) RIFT: Multi-Modal Image Matching Based on Radiation-Variation Insensitive Feature Transform"
