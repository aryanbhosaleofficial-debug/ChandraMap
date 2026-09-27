# IIRS Registration Research

## Overview

ChandraMap is a lunar image correspondence and registration research system for aligning imagery of the same lunar region across differences in:

- sensor or instrument
- spatial resolution
- image scale
- illumination conditions
- image representation
- sensing modality
- viewing and geometric conditions

The Chandrayaan-2 Imaging Infrared Spectrometer (IIRS) introduces an important future research direction because hyperspectral observations cannot be assumed to be directly interchangeable with conventional 2D grayscale or panchromatic imagery.

The central research problem is:

> **How can hyperspectral IIRS observations be transformed into robust, geometrically meaningful representations that can be registered against lunar imagery from other instruments?**

This document defines a future research direction for IIRS registration. It does **not** document an implemented or validated IIRS registration pipeline.

The research should determine, through controlled experiments, which IIRS-derived representations preserve sufficient spatial and structural information for reliable correspondence and geometric registration.

---

## Why IIRS Requires Separate Research

The existing ChandraMap V1 direction establishes a controlled registration workflow beginning with a known source/reference pair, a classical feature-matching baseline, geometric verification, quantitative evaluation, and progressively stronger registration experiments.

IIRS introduces an additional representation problem.

A conventional local feature matcher generally operates on a 2D image representation. An IIRS observation, however, contains hyperspectral information rather than a single conventional grayscale image.

Therefore, the future IIRS pipeline should not simply assume that the complete hyperspectral cube can be passed directly to an existing 2D feature-matching method.

Instead, the research should investigate how IIRS data can be transformed into scientifically defensible 2D representations while preserving registration-relevant spatial structure.

Candidate representations include:

- selected spectral bands
- band combinations
- band ratios or normalized combinations
- PCA-derived representations
- other dimensionality-reduced representations
- spectral composites
- gradient or edge representations
- local-contrast representations
- texture or structural representations
- other experimentally justified representations

No representation should be considered preferable without controlled experimental evidence.

The representation-selection problem is itself a research problem.

---

## IIRS-Specific Registration Challenges

Future IIRS registration research should explicitly investigate the interaction between spectral, spatial, radiometric, and geometric differences.

Potential challenges include:

### 1. Hyperspectral-to-2D Representation

The hyperspectral observation must be converted into a representation compatible with the selected correspondence method.

The conversion may preserve some information while discarding other spectral or spatial information.

The effect of this information loss must be measured rather than assumed.

### 2. Spectral Appearance Differences

Different spectral representations can produce different spatial appearance patterns.

A representation that emphasizes spectral variation may not necessarily emphasize the structures required for geometric correspondence.

### 3. Spatial Resolution Differences

IIRS-to-OHRC and IIRS-to-TMC-2 registration may involve substantial differences in spatial resolution and image scale.

The useful representation may therefore depend on the spatial scale at which correspondence is attempted.

### 4. Illumination and Reflectance

Lunar illumination affects observed appearance, while spectral measurements introduce additional wavelength-dependent appearance differences.

Normalization may reduce some intensity differences, but it cannot necessarily recover structures that are hidden or displaced by different shadow geometry.

### 5. Feature Repeatability

A feature detector may respond differently to the same lunar region after the IIRS observation has been transformed into different 2D representations.

Feature repeatability should therefore be measured as part of representation evaluation.

### 6. Cross-Sensor Geometry

IIRS-to-OHRC or IIRS-to-TMC-2 registration should not automatically be treated as identical to same-sensor registration.

The appropriate geometric model must be evaluated empirically.

### 7. Ground-Truth Availability

Cross-sensor research requires independently established reference information.

An apparent geometric consensus among matches is not sufficient evidence of correct registration.

---

## Research Objectives

The future IIRS research program should investigate:

1. How IIRS data can be converted into registration-compatible 2D representations.
2. Which representations preserve useful spatial structures.
3. Whether a single spectral band can provide sufficient geometric information.
4. Whether band combinations improve correspondence.
5. Whether PCA or related dimensionality reduction preserves registration-relevant structure.
6. Whether structural representations improve robustness to appearance differences.
7. How representation choice affects feature repeatability.
8. How representation choice affects geometric verification.
9. How IIRS-to-OHRC registration differs from IIRS-to-TMC-2 registration.
10. How scale and resolution differences affect IIRS correspondence.
11. How illumination and spectral appearance affect correspondence.
12. Whether classical local features provide a useful cross-modal baseline.
13. When stronger multimodal or learned matching methods should be investigated.
14. What ground-truth strategy is required for credible IIRS evaluation.
15. What evidence is required before an IIRS approach can be considered validated.

---

## Representation Strategies

The following representations are research candidates. None should be treated as validated by this document.

### Single-Band Representation

A single IIRS spectral band can be used to construct a conventional 2D image.

#### Concept

Select one spectral band and use its spatial image as the input to a conventional feature detector and descriptor.

#### Potential Benefits

- Simple implementation.
- Produces a conventional 2D image.
- Compatible with classical feature extractors.
- Provides a clear baseline for representation research.
- Makes controlled comparisons easier.

#### Potential Limitations

- Discards most spectral information.
- The selected band may not contain sufficient spatial structure.
- Different bands may exhibit different appearance characteristics.
- The representation may be sensitive to illumination and reflectance.
- A band that is useful spectrally is not necessarily useful geometrically.

#### Registration Hypothesis

A carefully selected IIRS band may contain sufficient spatial structure for conventional feature matching in some scenes.

This hypothesis requires experimental validation.

#### Required Experiment

Compare one or more controlled band selections under identical:

- image-pair conditions
- preprocessing
- feature detector
- descriptor
- matcher
- geometric verification
- evaluation protocol

The exact band selection strategy is `[TBD]`.

#### Evaluation Considerations

Measure:

- feature count
- candidate correspondence count
- verified inlier count
- inlier ratio
- spatial coverage
- independent checkpoint error
- residual distribution
- registration success
- runtime

#### Status

**Proposed**

---

### Band-Combination Representation

Multiple spectral bands may be combined into a 2D representation.

Possible approaches include:

- band ratios
- normalized band combinations
- multispectral-style composites
- physically motivated combinations
- experimentally selected combinations

#### Potential Benefits

Band combinations may retain information that is lost when using a single band.

#### Potential Limitations

A combination may:

- amplify noise
- create unstable contrast
- depend strongly on normalization
- emphasize spectral differences rather than geometric structure
- introduce arbitrary representation choices

#### Registration Hypothesis

Some combinations of spectral information may produce more registration-relevant spatial structure than individual bands.

The exact combinations must be determined experimentally.

#### Required Experiment

A controlled representation sweep should compare candidate combinations while keeping the downstream registration pipeline fixed.

#### Evaluation Considerations

The comparison should evaluate independent geometric accuracy rather than relying only on feature-match count.

#### Status

**Proposed**

---

### PCA / Dimensionality Reduction

Principal Component Analysis (PCA) or another dimensionality-reduction method can be investigated as a way to convert hyperspectral observations into compact representations.

#### Concept

Transform the hyperspectral representation into a lower-dimensional representation and use one or more resulting components as 2D registration inputs.

#### Potential Benefits

- Reduces dimensionality.
- Can produce compact representations.
- May capture dominant variation.
- Can provide multiple candidate spatial components.
- May simplify downstream processing.

#### Important Scientific Caveat

The component containing the largest variance is **not automatically the component containing the most registration-relevant information**.

Spectral variance may arise from effects that are not useful for geometric correspondence.

Therefore:

> PCA variance explained must not be treated as a proxy for registration quality.

#### Potential Limitations

- Useful spatial/spectral information may be discarded.
- Dominant variance may correspond to irrelevant variation.
- Results may depend on preprocessing and normalization.
- Interpretation can be less direct than a physically selected band.
- Computation may increase with large hyperspectral inputs.

#### Registration Hypothesis

A dimensionality-reduced IIRS representation may provide compact spatial information suitable for correspondence while retaining useful information from multiple spectral dimensions.

This requires controlled validation.

#### Required Experiment

Compare:

- selected individual bands
- PCA components
- potentially multiple PCA configurations

using the same downstream registration pipeline.

#### Status

**Proposed**

---

### Spectral Composite Representation

A spectral composite can combine selected spectral channels into a 2D visual or computational representation.

The exact construction should be defined by the experiment rather than assumed in advance.

Potential considerations include:

- selected-band combinations
- normalized channel combinations
- spectral contrast
- physically motivated combinations

The representation should be documented precisely enough for reproduction.

**Status:** Proposed

---

### Structural / Gradient Representation

The existing ChandraMap research direction already investigates structure-focused representations.

IIRS research can extend this direction by constructing representations based on:

- gradients
- edges
- local contrast
- texture
- structural maps

#### Potential Benefits

A structural representation may reduce dependence on absolute intensity and emphasize spatial patterns that are more useful for geometric correspondence.

#### Potential Limitations

- Spectral information may be discarded.
- Weakly textured regions may remain difficult.
- Noise may be amplified by gradient operations.
- Different spectral representations may produce different structural maps.

#### Connection to Existing Research

This direction should be evaluated alongside the existing gradient-representation work in:

[EXP-003 — Gradient Representation](../../experiments/v1/preprocessing/EXP-003-gradient-representation/README.md)

The existing research should serve as methodological context rather than evidence that the IIRS representation has already been validated.

**Status:** Proposed

---

### Other Candidate Representations

Future investigation may consider:

- local-normalized representations
- multi-scale structural maps
- texture descriptors
- learned 2D projections
- physically motivated spectral indices
- cross-modal representations
- other dimensionality-reduction techniques

Each new representation should have:

1. a clearly stated hypothesis,
2. a reproducible construction procedure,
3. a controlled comparison,
4. quantitative evaluation,
5. documented failure cases.

**Status:** Exploratory

---

## Representation Selection Strategy

Representation selection should be treated as a controlled scientific comparison rather than an implementation preference.

A conceptual comparison framework is:

| Representation        | Expected Benefit                          | Main Risk                                       | Required Experiment             | Status      |
| --------------------- | ----------------------------------------- | ----------------------------------------------- | ------------------------------- | ----------- |
| Single band           | Simple 2D input                           | Spectral information loss                       | Controlled single-band baseline | Proposed    |
| Band combination      | Uses multiple spectral signals            | Combination may be arbitrary                    | Representation sweep            | Proposed    |
| PCA                   | Compact multi-spectral representation     | Variance may not equal geometric usefulness     | PCA benchmark                   | Proposed    |
| Spectral composite    | Flexible multi-band representation        | Representation-dependent appearance             | Composite comparison            | Proposed    |
| Gradient / structural | Focuses on spatial structure              | May discard spectral cues                       | Structural benchmark            | Proposed    |
| Other representations | May address specific modality limitations | Increased complexity or unsupported assumptions | Research-specific experiment    | Exploratory |

Comparisons should hold downstream components constant where practical.

For example:

> IIRS representation → same feature detector → same descriptor → same matcher → same geometric model → same evaluation protocol

This isolates representation choice as the primary experimental variable.

---

## Cross-Sensor Registration Scenarios

## IIRS ↔ OHRC

IIRS-to-OHRC registration should be treated as a future cross-sensor benchmark scenario.

Potential challenges include:

- different sensing modalities
- spectral versus conventional image representation
- spatial-resolution differences
- image-scale differences
- illumination differences
- local texture differences
- surface-reflectance differences
- feature-repeatability differences
- geometric differences

The experiment should determine whether an IIRS-derived representation contains structures that correspond reliably to structures visible in OHRC imagery.

The research should not assume that a representation that works for IIRS-only processing will necessarily work for IIRS-to-OHRC registration.

### Proposed Research Questions

- Which IIRS representation produces repeatable structures in OHRC imagery?
- How does spatial-resolution difference affect feature localization?
- Does structural preprocessing improve cross-sensor correspondence?
- Which geometric model adequately describes the observed correspondence?
- How does independent checkpoint error change across representations?
- How does performance vary across lunar scenes?

### Status

**Planned**

No successful IIRS-to-OHRC registration result is established by this document.

---

## IIRS ↔ TMC-2

IIRS-to-TMC-2 registration is another future cross-sensor scenario.

The research should investigate:

- modality compatibility
- resolution differences
- scale variation
- structural correspondence
- representation compatibility
- illumination differences
- geometric-model requirements

### Proposed Research Questions

- Can IIRS-derived spatial structures be matched reliably against TMC-2 imagery?
- Does a single-band representation provide sufficient information?
- Do multi-band or dimensionality-reduced representations improve correspondence?
- Does gradient or structural preprocessing improve robustness?
- How does scale variation affect correspondence?
- Which geometric model provides an adequate approximation?

### Status

**Planned**

No successful IIRS-to-TMC-2 registration result is established by this document.

---

## IIRS ↔ Other Lunar Imagery

Future work may investigate registration against other lunar image products where:

- the image geometry is sufficiently documented,
- the spatial reference is available,
- ground truth can be established,
- acquisition metadata can be preserved,
- the product is appropriate for the intended experiment.

The exact future datasets are:

- `[Not provided]`
- `[TBD]`

No external dataset should be treated as part of the benchmark until it is explicitly documented and versioned.

---

## Proposed Experimental Roadmap

The recommended research progression is:

```text
IIRS Data Validation
        ↓
Simple 2D Representation
        ↓
Classical Feature Baseline
        ↓
Geometric Verification
        ↓
Quantitative Evaluation
        ↓
Representation Comparison
        ↓
IIRS ↔ OHRC / TMC-2
        ↓
Scale + Illumination Studies
        ↓
Multimodal Feature Research
        ↓
Learned Matching
        ↓
Validation / Implementation Decision
```

The progression intentionally begins with simple, measurable baselines before introducing more complex learned methods.

---

## Baseline Strategy

A conservative IIRS baseline should proceed in stages.

### Stage 1 — Data and Metadata Validation

Validate:

- IIRS input availability
- product identifiers
- acquisition metadata
- image dimensions
- spatial metadata
- spectral metadata
- coordinate/reference information where available
- missing-data handling
- preprocessing assumptions

Any missing information should be explicitly recorded as `[TBD]` or `[Not provided]`.

### Stage 2 — Controlled Scene Selection

Select a controlled IIRS scene or image pair.

The exact dataset is:

`[TBD]`

The scene should have sufficient reference information for quantitative evaluation.

### Stage 3 — Simple 2D Representation

Generate one deliberately simple representation.

A single-band representation is an appropriate candidate baseline.

### Stage 4 — Classical Feature Matching

Apply the established classical registration pipeline where compatible.

The existing SIFT baseline provides methodological context:

[EXP-001 — SIFT Baseline](../../experiments/v1/baseline/EXP-001-sift-baseline/README.md)

The IIRS experiment should document any adaptations required by the modality.

### Stage 5 — Geometric Verification

Perform robust geometric verification and distinguish:

```text
Candidate Matches
        ↓
Geometrically Verified Inliers
        ↓
Estimated Transformation
        ↓
Final Registration
        ↓
Independent Check-Point Evaluation
```

### Stage 6 — Quantitative Evaluation

Measure registration quality using the established benchmark philosophy.

### Stage 7 — Representation Comparison

Compare:

- single band
- band combination
- PCA or dimensionality reduction
- structural/gradient representation
- other justified candidates

### Stage 8 — Cross-Sensor Evaluation

Investigate:

- IIRS ↔ OHRC
- IIRS ↔ TMC-2

### Stage 9 — Scale and Illumination Studies

Evaluate robustness under controlled variations in:

- scale
- spatial resolution
- illumination
- representation

### Stage 10 — Advanced Multimodal Methods

Only after classical behavior is characterized should stronger multimodal methods be introduced.

---

## Proposed Experiments

The following experiments are proposed and have **not** been executed unless separately documented elsewhere in the repository.

### IIRS-EXP-001 — Single-Band Baseline

| Field                   | Definition                                                                              |
| ----------------------- | --------------------------------------------------------------------------------------- |
| Experiment ID           | `IIRS-EXP-001`                                                                          |
| Research Question       | Can a single IIRS band provide sufficient spatial structure for classical registration? |
| Hypothesis              | At least some bands may contain useful registration-relevant spatial structure.         |
| Input Data              | `[TBD]`                                                                                 |
| IIRS Representation     | Single selected band                                                                    |
| Comparison Method       | Classical feature-matching baseline                                                     |
| Control Condition       | Fixed preprocessing, matcher, geometric model, and evaluation                           |
| Variables               | Band selection                                                                          |
| Metrics                 | Checkpoint error, inliers, inlier ratio, coverage, runtime, success                     |
| Expected Interpretation | Determine whether individual bands provide a usable baseline                            |
| Failure Criteria        | Insufficient correspondence or unacceptable independent registration error              |
| Status                  | Planned                                                                                 |

---

### IIRS-EXP-002 — PCA Representation

| Field                   | Definition                                                                |
| ----------------------- | ------------------------------------------------------------------------- |
| Experiment ID           | `IIRS-EXP-002`                                                            |
| Research Question       | Can PCA-derived representations preserve registration-relevant structure? |
| Hypothesis              | Some PCA components may contain useful spatial structures.                |
| Input Data              | `[TBD]`                                                                   |
| Representation          | PCA-derived 2D component(s)                                               |
| Control                 | Same downstream registration pipeline as IIRS-EXP-001                     |
| Variables               | PCA configuration/component selection                                     |
| Metrics                 | Independent error, inlier statistics, spatial coverage, runtime           |
| Expected Interpretation | Determine whether PCA preserves useful correspondence structure           |
| Failure Criteria        | Reduced geometric quality or unstable correspondence                      |
| Status                  | Planned                                                                   |

---

### IIRS-EXP-003 — Band-Combination Representation

| Field                   | Definition                                                                      |
| ----------------------- | ------------------------------------------------------------------------------- |
| Experiment ID           | `IIRS-EXP-003`                                                                  |
| Research Question       | Can combinations of spectral bands improve geometric correspondence?            |
| Hypothesis              | Some combinations may preserve complementary registration-relevant information. |
| Input Data              | `[TBD]`                                                                         |
| Representation          | Band combinations                                                               |
| Variables               | Selected bands and combination method                                           |
| Metrics                 | Independent registration accuracy, coverage, inliers, runtime                   |
| Expected Interpretation | Determine whether multi-band combinations improve measured performance          |
| Failure Criteria        | No measurable benefit or unstable behavior                                      |
| Status                  | Planned                                                                         |

---

### IIRS-EXP-004 — Structural Representation

| Field                   | Definition                                                                          |
| ----------------------- | ----------------------------------------------------------------------------------- |
| Experiment ID           | `IIRS-EXP-004`                                                                      |
| Research Question       | Do gradient or structural representations improve cross-sensor correspondence?      |
| Hypothesis              | Emphasizing spatial structure may reduce dependence on absolute spectral intensity. |
| Input Data              | `[TBD]`                                                                             |
| Representation          | Gradient / edge / structural map                                                    |
| Control                 | Same downstream matching and geometric evaluation                                   |
| Variables               | Structural representation and preprocessing parameters                              |
| Metrics                 | Independent error, residuals, coverage, inliers, runtime                            |
| Expected Interpretation | Determine whether structural representation provides measurable benefit             |
| Failure Criteria        | Loss of useful information or unstable feature localization                         |
| Status                  | Planned                                                                             |

---

### IIRS-EXP-005 — Representation Comparison

| Field                   | Definition                                                                 |
| ----------------------- | -------------------------------------------------------------------------- |
| Experiment ID           | `IIRS-EXP-005`                                                             |
| Research Question       | How do candidate IIRS representations compare under identical conditions?  |
| Hypothesis              | Representation choice will affect registration quality and robustness.     |
| Input Data              | Controlled IIRS benchmark                                                  |
| Representations         | Single band, band combination, PCA, structural, other justified candidates |
| Control                 | Fixed downstream pipeline                                                  |
| Variables               | Representation                                                             |
| Metrics                 | Independent error, inliers, coverage, success, runtime                     |
| Expected Interpretation | Characterize representation-dependent behavior                             |
| Failure Criteria        | Insufficient repeatability or inadequate evidence                          |
| Status                  | Planned                                                                    |

---

### IIRS-EXP-006 — IIRS ↔ OHRC

| Field                   | Definition                                                                      |
| ----------------------- | ------------------------------------------------------------------------------- |
| Experiment ID           | `IIRS-EXP-006`                                                                  |
| Research Question       | Can IIRS-derived representations support registration against OHRC imagery?     |
| Hypothesis              | Some representations may preserve structures observable across both modalities. |
| Input Data              | `[TBD]`                                                                         |
| Representation          | Selected from prior representation experiments                                  |
| Comparison              | Classical cross-sensor baseline                                                 |
| Variables               | Representation, scale, scene conditions                                         |
| Metrics                 | Independent checkpoint error, coverage, inliers, success, runtime               |
| Expected Interpretation | Characterize IIRS-to-OHRC feasibility                                           |
| Failure Criteria        | Insufficient reliable correspondences or independent accuracy                   |
| Status                  | Planned                                                                         |

---

### IIRS-EXP-007 — IIRS ↔ TMC-2

| Field                   | Definition                                                                                      |
| ----------------------- | ----------------------------------------------------------------------------------------------- |
| Experiment ID           | `IIRS-EXP-007`                                                                                  |
| Research Question       | Can IIRS-derived representations support registration against TMC-2 imagery?                    |
| Hypothesis              | Cross-sensor structural correspondence may be possible for suitable scenes and representations. |
| Input Data              | `[TBD]`                                                                                         |
| Representation          | Selected from prior representation experiments                                                  |
| Comparison              | Classical cross-sensor baseline                                                                 |
| Variables               | Representation, scale, scene conditions                                                         |
| Metrics                 | Independent checkpoint error, coverage, inliers, success, runtime                               |
| Expected Interpretation | Characterize IIRS-to-TMC-2 feasibility                                                          |
| Failure Criteria        | Insufficient reliable correspondence or independent accuracy                                    |
| Status                  | Planned                                                                                         |

---

### IIRS-EXP-008 — Scale and Resolution Robustness

| Field             | Definition                                                             |
| ----------------- | ---------------------------------------------------------------------- |
| Experiment ID     | `IIRS-EXP-008`                                                         |
| Research Question | How does spatial-resolution difference affect IIRS registration?       |
| Hypothesis        | Registration performance changes with scale and resampling conditions. |
| Input Data        | `[TBD]`                                                                |
| Variables         | Scale, resampling, image pyramid                                       |
| Metrics           | Independent error, coverage, inlier statistics, runtime                |
| Status            | Planned                                                                |

Relevant existing research:

[EXP-002 — Scale Pyramid](../../experiments/v1/preprocessing/EXP-002-scale-pyramid/README.md)

---

### IIRS-EXP-009 — Illumination Robustness

| Field             | Definition                                                                                 |
| ----------------- | ------------------------------------------------------------------------------------------ |
| Experiment ID     | `IIRS-EXP-009`                                                                             |
| Research Question | How do illumination and shadow differences affect IIRS correspondence?                     |
| Hypothesis        | Illumination variation can alter representation appearance and correspondence reliability. |
| Input Data        | `[TBD]`                                                                                    |
| Variables         | Illumination condition, representation                                                     |
| Metrics           | Independent error, coverage, inliers, residual structure, success                          |
| Status            | Planned                                                                                    |

Relevant research:

[Illumination Invariance](../notes/illumination-invariance.md)

---

### IIRS-EXP-010 — Multimodal Matching

| Field             | Definition                                                                                                           |
| ----------------- | -------------------------------------------------------------------------------------------------------------------- |
| Experiment ID     | `IIRS-EXP-010`                                                                                                       |
| Research Question | Can multimodal descriptors or learned matching improve cross-sensor correspondence?                                  |
| Hypothesis        | Methods explicitly designed for appearance or modality differences may address limitations of classical descriptors. |
| Input Data        | `[TBD]`                                                                                                              |
| Comparison        | Classical baseline                                                                                                   |
| Variables         | Matching method                                                                                                      |
| Metrics           | Independent registration accuracy, coverage, success, runtime/resource usage                                         |
| Status            | Planned                                                                                                              |

---

## Evaluation Protocol

IIRS experiments should follow the established ChandraMap evaluation philosophy.

The fundamental evaluation sequence is:

```text
Input Images
    ↓
Candidate Correspondences
    ↓
Geometric Verification
    ↓
Verified Inliers
    ↓
Transformation Estimation
    ↓
Final Registration
    ↓
Independent Check-Point Evaluation
```

### Candidate Matches

Candidate matches indicate potential correspondence.

They do **not** establish registration correctness.

A high candidate-match count can coexist with:

- incorrect correspondences
- clustered features
- poor geometric coverage
- false geometric consensus
- large independent registration error

### Verified Inliers

Geometric verification should identify correspondences that are consistent with the estimated geometric model under the selected verification procedure.

The experiment must document:

- geometric model
- RANSAC or equivalent robust-estimation configuration
- inlier threshold
- confidence
- iteration limits
- minimum inlier requirements
- random seed where applicable

### Final Registration

The final transformation should be estimated according to the documented experimental procedure.

If sub-pixel refinement is used, it should occur after geometric verification and before final independent evaluation.

### Independent Evaluation

The points used for fitting the transformation should not be treated as independent accuracy measurements.

Where independent check points are available:

> Fit on control/verified correspondences → evaluate on independent check points.

If independent check points are unavailable, the limitation must be explicitly reported.

---

## Ground-Truth Requirements

IIRS research requires an explicit ground-truth or reference strategy.

The relevant project research is:

[Ground Truth Design](../notes/ground-truth-design.md)

Potential sources of evaluation control include:

- manually established control points
- independently established check points
- trusted reference products
- geometrically controlled image products
- independently verified correspondences

The exact IIRS ground-truth dataset is:

`[Not provided]`

The future benchmark should document:

- dataset identifier
- image/product identifiers
- source and reference image
- control points
- independent check points
- coordinate conventions
- image coordinate systems
- projection information where applicable
- GSD information where applicable
- annotation procedure
- annotation uncertainty where available
- version of the ground-truth asset

Ground truth should be treated as a versioned scientific evaluation asset rather than an informal collection of matching points.

---

## Metrics

The following metrics should be considered for IIRS experiments.

| Metric                    | Purpose                                             |
| ------------------------- | --------------------------------------------------- |
| Reprojection error        | Measures geometric discrepancy                      |
| Check-point RMSE          | Measures independent registration accuracy          |
| Median error              | Describes typical independent error                 |
| Maximum error             | Identifies severe local errors when meaningful      |
| Inlier count              | Measures geometrically consistent correspondences   |
| Inlier ratio              | Measures verified correspondence proportion         |
| Spatial coverage          | Measures geographic distribution of correspondences |
| Registration success rate | Measures reliability across test cases              |
| Runtime                   | Measures computational cost                         |
| Memory/resource usage     | Important for hyperspectral processing              |

Errors should be reported in source-image pixels first.

Conversion to physical units should only be performed when the required:

- GSD
- projection
- coordinate reference
- reference geometry

are sufficiently documented.

### Spatial Coverage

Spatial coverage should be reported where practical.

Correspondences concentrated in one small region should not automatically be interpreted as successful global registration.

Possible coverage analyses include:

- grid occupancy
- convex-hull coverage
- spatial distribution plots
- correspondence density maps

The exact implementation is `[TBD]`.

### Residual Analysis

Residuals should be inspected for:

- magnitude
- direction
- spatial distribution
- clusters
- systematic patterns
- localized large errors

A low average residual can hide local registration problems.

---

## Cross-Sensor Geometry

IIRS-to-OHRC and IIRS-to-TMC-2 registration may require investigation of different geometric models.

Potential candidates include:

- translation
- similarity transformation
- affine transformation
- homography
- more advanced geometric models

No model should be assumed to be universally correct.

The existing geometric research provides the relevant methodological foundation:

[EXP-004 — Affine vs Homography](../../experiments/v1/geometry/EXP-004-affine-vs-homography/README.md)

The IIRS experiments should empirically investigate:

1. whether the correspondence geometry is adequately represented by the selected model,
2. whether residuals show systematic spatial structure,
3. whether the model remains stable across image regions,
4. whether independent check-point error supports the model,
5. whether increased model complexity produces genuine improvement or simply fits noisy correspondences.

Model selection should not be based solely on fitting RMSE or inlier count.

---

## Illumination and Spectral Effects

IIRS research should be connected to:

[Illumination Invariance](../notes/illumination-invariance.md)

Illumination normalization can reduce some intensity differences, but it cannot necessarily undo changes in shadow geometry.

This distinction is particularly important for lunar registration.

### Relevant IIRS Effects

Future experiments should investigate:

- spectral reflectance differences
- illumination-dependent spectral appearance
- local contrast changes
- shadow differences
- wavelength-dependent appearance
- representation-specific sensitivity

A representation that is stable under one illumination condition may behave differently under another.

Therefore, illumination robustness should be measured rather than assumed.

### Important Distinction

The following should not be treated as equivalent:

- photometric normalization
- illumination robustness
- structural robustness
- geometric robustness

A transformation model cannot recover correspondence information that has disappeared or changed because of appearance or visibility differences.

---

## Scale and Resolution

IIRS research should connect to:

[Scale Invariance](../notes/scale-invariance.md)

Future experiments should investigate:

- different native resolutions
- image pyramids
- feature-scale changes
- resampling effects
- fine-detail loss
- spectral/spatial-resolution interaction
- representation stability across scale

The existing scale-pyramid research provides an appropriate methodological starting point:

[EXP-002 — Scale Pyramid](../../experiments/v1/preprocessing/EXP-002-scale-pyramid/README.md)

A representation that is useful at one spatial scale may not remain equally useful after resampling.

This should be measured through controlled experiments.

---

## Feature Repeatability

Feature repeatability should be considered an intermediate research measurement.

A useful representation should ideally preserve identifiable spatial structures across the relevant image pair.

Future experiments may measure:

- number of detected features
- spatial distribution of features
- repeatability across representations
- localization stability
- descriptor matchability
- geometric verification rate

Feature count alone should not be treated as registration quality.

A representation may produce many features that do not correspond reliably across sensors.

---

## Baseline Matching Methods

The first IIRS experiments should use a controlled classical baseline wherever technically appropriate.

The existing SIFT baseline provides a reference:

[EXP-001 — SIFT Baseline](../../experiments/v1/baseline/EXP-001-sift-baseline/README.md)

A conceptual baseline is:

```text
IIRS Data
   ↓
2D Representation
   ↓
Feature Detection
   ↓
Descriptor Extraction
   ↓
Candidate Matching
   ↓
Match Filtering
   ↓
Geometric Verification
   ↓
Transformation Estimation
   ↓
Independent Evaluation
```

Any adaptation required because of the IIRS modality should be explicitly documented.

---

## Advanced Methods

Advanced methods should be investigated only after the classical baseline is sufficiently characterized.

Potential future categories include:

### Stronger Handcrafted Descriptors

These may provide different behavior under:

- scale changes
- illumination changes
- texture differences
- cross-sensor appearance differences

They should be compared against the established classical baseline.

### Multimodal Feature Descriptors

Descriptors specifically designed for cross-modal appearance differences may be useful when direct intensity correspondence is weak.

The research should measure whether they improve independent registration accuracy rather than merely match count.

### Learned Local Features

Potential future research directions include:

- ALIKED
- other learned local-feature extractors

These should remain research candidates unless explicitly implemented and measured.

### Learned Matching

Potential future matching research includes:

- LightGlue
- other learned matching frameworks

The comparison should use the same benchmark data and evaluation criteria as the classical baseline wherever practical.

### Dense Correspondence

Dense methods may be useful when sparse local features fail in weakly textured regions.

Potential research directions include dense cross-modal correspondence and transformer-based matching.

### LoFTR

LoFTR may be investigated as a future matching direction for cases where conventional sparse local features are insufficient.

It should not be considered validated for IIRS registration without dedicated experimental evidence.

### Cross-Modal Representation Learning

Longer-term research may investigate learned representations explicitly designed to map different sensor modalities into a more compatible feature space.

Such approaches would require:

- representative training data
- carefully separated evaluation data
- reproducible training configuration
- independent ground truth
- ablation studies
- computational-resource documentation

---

## Failure Modes and Risks

IIRS research should preserve failed cases and investigate why they fail.

| Failure Mode                           | Possible Cause                                     | Experimental Detection                          |
| -------------------------------------- | -------------------------------------------------- | ----------------------------------------------- |
| Insufficient spatial texture           | Region lacks distinctive structures                | Feature-density and coverage analysis           |
| Spectral representation loses geometry | Representation discards useful spatial information | Representation comparison                       |
| Poor band selection                    | Selected band lacks stable structure               | Controlled band sweep                           |
| PCA emphasizes irrelevant variance     | Dominant variance is not registration-relevant     | Compare PCA quality against independent error   |
| Cross-sensor appearance mismatch       | Modalities produce different local appearance      | Match/inlier analysis and failure visualization |
| Shadow displacement                    | Different illumination geometry                    | Spatial residual and illumination analysis      |
| Scale mismatch                         | Different image resolutions                        | Scale-controlled experiments                    |
| Incorrect geometric model              | Model cannot represent observed correspondence     | Residual field analysis                         |
| Clustered correspondences              | Features concentrated in one region                | Spatial coverage analysis                       |
| Insufficient inliers                   | Weak or unreliable correspondence                  | Inlier statistics                               |
| False geometric consensus              | Incorrect points accidentally fit a model          | Independent check-point evaluation              |
| Poor ground truth                      | Evaluation points are unreliable                   | Ground-truth audit                              |
| Registration ambiguity                 | Multiple regions have similar appearance           | Spatial/geometric verification                  |
| Resolution-induced information loss    | Fine structures disappear after resampling         | Resolution sweep                                |
| Sensor-specific artifacts              | Product-specific image effects                     | Scene/product stratification                    |
| Unstable localization                  | Feature position changes across representations    | Repeatability/localization analysis             |

---

## Research Decision Criteria

IIRS methods should progress based on measured evidence.

Important questions include:

- Does the representation improve independent checkpoint error?
- Does it maintain adequate spatial coverage?
- Does geometric verification remain stable?
- Does it work across representative scenes?
- Does it remain useful under illumination variation?
- Does it remain useful across resolution differences?
- Does it provide reproducible results?
- Is computational cost acceptable?
- Is the representation sufficiently interpretable for the research objective?
- Does the method improve over the controlled baseline under the same conditions?

### Evidence-Based Status Labels

The following labels may be used for IIRS research artifacts:

| Status                   | Meaning                                                              |
| ------------------------ | -------------------------------------------------------------------- |
| Exploratory              | Initial research direction                                           |
| Proposed                 | Defined but not yet executed                                         |
| Planned                  | Scheduled future work                                                |
| Experiment Ready         | Data, method, and evaluation procedure are sufficiently specified    |
| Under Evaluation         | Experiment execution is in progress                                  |
| Validated                | Supported by documented experimental evidence                        |
| Implementation Candidate | Evidence supports consideration for implementation                   |
| Implemented              | Confirmed implementation exists                                      |
| Deferred                 | Intentionally postponed                                              |
| Rejected                 | Experimental evidence or project constraints justify discontinuation |

No method should be marked **Validated** or **Implemented** without supporting repository evidence.

---

## Research Status

The current IIRS research status is intentionally conservative.

| Area                        | Status          | Evidence / Next Step                       |
| --------------------------- | --------------- | ------------------------------------------ |
| IIRS data handling          | Planned         | Define data and metadata requirements      |
| Single-band representation  | Proposed        | Execute controlled baseline                |
| PCA representation          | Proposed        | Evaluate dimensionality-reduced components |
| Band combinations           | Proposed        | Conduct representation sweep               |
| Structural representation   | Proposed        | Compare against raw representation         |
| IIRS ↔ OHRC                 | Planned         | Establish cross-sensor benchmark           |
| IIRS ↔ TMC-2                | Planned         | Establish cross-sensor benchmark           |
| Scale robustness            | Planned         | Controlled resolution/scale experiments    |
| Illumination robustness     | Planned         | Controlled illumination study              |
| Learned multimodal matching | Exploratory     | Introduce only after classical baseline    |
| IIRS validation             | Not established | Requires measured benchmark evidence       |

---

## Reproducibility Requirements

Every IIRS experiment should record enough information for another researcher to reconstruct the processing and evaluation procedure.

### Dataset

Record:

- dataset identifier
- product identifier
- scene identifier
- source/reference relationship
- acquisition information available in the source
- file version or checksum where applicable

### IIRS Representation

Record:

- selected spectral bands
- band identifiers
- band-combination formula
- normalization procedure
- dimensionality-reduction method
- PCA configuration
- selected components
- missing-value handling
- clipping or scaling
- resampling parameters
- interpolation method

### Image Preprocessing

Record:

- image normalization
- filtering
- gradient computation
- edge extraction
- contrast processing
- image pyramids
- crop/window definitions

### Feature Extraction

Record:

- detector
- detector parameters
- descriptor
- descriptor parameters
- image scale
- feature limits where applicable

### Matching

Record:

- matcher
- distance metric
- ratio-test configuration where applicable
- cross-check configuration
- matching thresholds
- filtering procedure

### Geometric Verification

Record:

- transformation model
- RANSAC configuration
- threshold
- confidence
- iteration limit
- minimum inlier requirements
- random seed

### Evaluation

Record:

- control points
- independent check points
- ground-truth version
- evaluation coordinate system
- metric definitions
- success criteria
- spatial-coverage calculation

### Environment

Record:

- operating-system information
- programming-language version
- library versions
- GPU/CPU information where relevant
- hardware constraints where relevant
- container/environment specification where available

### Randomness

Record:

- random seeds
- stochastic training configuration
- randomized sampling procedures

### Output Artifacts

Record:

- configuration file
- experiment log
- match visualization
- geometric verification visualization
- transformation parameters
- residual analysis
- checkpoint-error table
- benchmark summary
- failure cases

---

## Expected Research Artifacts

Future IIRS research may produce:

- IIRS preprocessing scripts
- IIRS representation-generation modules
- configuration files
- experiment READMEs
- benchmark manifests
- representation visualizations
- feature-match visualizations
- geometric-verification outputs
- residual plots
- checkpoint-error tables
- spatial-coverage visualizations
- benchmark summaries
- reproducibility records
- failure-analysis reports
- cross-sensor comparison reports

These are expected research outputs and are **not claims that the artifacts already exist**.

---

## Scientific Integrity Requirements

All IIRS research should follow these principles:

- Do not fabricate experiment results.
- Do not fabricate IIRS specifications.
- Do not invent datasets.
- Do not invent benchmark scores.
- Do not claim implementation that does not exist.
- Do not claim validation without measured evidence.
- Do not claim one representation is superior without controlled comparison.
- Clearly distinguish hypotheses from results.
- Clearly distinguish proposed work from implemented work.
- Preserve failed experiments.
- Report limitations.
- Identify missing information explicitly.
- Use `[TBD]`, `[Not provided]`, `[Planned]`, or `[Not implemented]` where necessary.

A research hypothesis should remain a hypothesis until supported by measured evidence.

---

## Relationship to Existing ChandraMap Research

IIRS research extends the existing ChandraMap registration research rather than replacing it.

### Lunar Registration

The foundational registration problem is documented in:

[lunar-registration.md](../notes/lunar-registration.md)

IIRS adds a cross-modal representation problem to the existing correspondence and geometric-registration problem.

### Illumination

IIRS research should extend the questions described in:

[illumination-invariance.md](../notes/illumination-invariance.md)

### Scale

IIRS research should connect to:

[scale-invariance.md](../notes/scale-invariance.md)

### Ground Truth

IIRS evaluation should follow the principles described in:

[ground-truth-design.md](../notes/ground-truth-design.md)

### Existing V1 Research

The V1 experiment sequence provides the methodological foundation:

- [V1 Experiments](../../experiments/v1/README.md)
- [EXP-001 — SIFT Baseline](../../experiments/v1/baseline/EXP-001-sift-baseline/README.md)
- [EXP-002 — Scale Pyramid](../../experiments/v1/preprocessing/EXP-002-scale-pyramid/README.md)
- [EXP-003 — Gradient Representation](../../experiments/v1/preprocessing/EXP-003-gradient-representation/README.md)
- [EXP-004 — Affine vs Homography](../../experiments/v1/geometry/EXP-004-affine-vs-homography/README.md)
- [EXP-005 — Residual Analysis](../../experiments/v1/geometry/EXP-005-residual-analysis/README.md)
- [EXP-006 — Subpixel Refinement](../../experiments/v1/refinement/EXP-006-subpixel-refinement/README.md)

These experiments establish methodological context. They do not constitute evidence that IIRS registration has already been solved.

---

## Relationship to ChandraMap Versions

### V1 — Registration Foundations

The established V1 direction focuses on controlled registration research:

- known source/reference pair
- classical feature baseline
- scale variation
- structure-focused preprocessing
- geometric verification
- affine versus homography evaluation
- residual analysis
- sub-pixel refinement
- quantitative evaluation
- explicit ground truth

IIRS should not be represented as part of the completed V1 capability unless repository evidence explicitly establishes it.

### Future Research

Future ChandraMap development may extend toward:

- IIRS representation research
- IIRS-to-OHRC registration
- IIRS-to-TMC-2 registration
- multimodal descriptors
- learned correspondence
- more advanced geometric models
- larger cross-sensor benchmarks
- terrain-aware registration where justified by evidence

A specific future-version assignment is:

`[Not provided]`

Therefore, this document treats IIRS as a future research direction rather than assigning it to an unsupported version.

---

## Research Roadmap

The recommended high-level progression is:

### Phase 1 — IIRS Data Validation

- establish supported input products
- preserve metadata
- validate dimensions and coordinate information
- document preprocessing assumptions

### Phase 2 — Simple Representation

- generate controlled single-band representation
- establish visualization and quality checks
- document representation parameters

### Phase 3 — Classical Baseline

- apply compatible classical feature extraction
- generate candidate correspondences
- perform geometric verification
- evaluate independently

### Phase 4 — Representation Comparison

Compare:

- single band
- band combinations
- PCA
- spectral composites
- structural/gradient representations

### Phase 5 — Cross-Sensor Registration

Investigate:

- IIRS ↔ OHRC
- IIRS ↔ TMC-2

### Phase 6 — Scale and Illumination

Study:

- spatial-resolution differences
- image scale
- resampling
- illumination variation
- shadow changes
- representation sensitivity

### Phase 7 — Multimodal Matching

Investigate:

- stronger handcrafted descriptors
- multimodal descriptors
- learned local features
- learned matching
- dense correspondence

### Phase 8 — Validation and Implementation Decision

Only after sufficient benchmark evidence should a method be considered for integration into the main ChandraMap implementation.

---

## Open Research Questions

The following questions remain open:

1. Which IIRS spectral representations preserve the most registration-relevant spatial structure?
2. Is a single band sufficient for useful cross-sensor correspondence?
3. Which band-combination strategies are scientifically justified?
4. Does PCA preserve registration-relevant information?
5. Which PCA components, if any, are useful for correspondence?
6. Does structural processing improve cross-modal feature repeatability?
7. How does representation choice affect geometric verification?
8. How does IIRS-to-OHRC registration differ from IIRS-to-TMC-2 registration?
9. How much spatial-resolution difference can the baseline tolerate?
10. How does illumination variation interact with spectral representation?
11. Which geometric model adequately describes the observed correspondence?
12. How much does spatial coverage affect independent registration accuracy?
13. Can classical descriptors provide a sufficient baseline across the modality gap?
14. Under what conditions do learned multimodal methods provide measurable improvement?
15. What benchmark size is required to establish generalization?
16. What ground-truth uncertainty is acceptable for IIRS evaluation?
17. How should failures be categorized and reported?
18. Which representation provides the best balance of accuracy, robustness, interpretability, and computational cost?
19. Can terrain-aware geometric models provide measurable benefit for difficult cross-sensor cases?
20. What evidence is sufficient to move an IIRS approach from research into the production registration pipeline?

These questions should remain open until supported by experimental evidence.

---

## Research Decision Framework

A future IIRS method should move toward implementation only after the following evidence has been collected:

```text
Representation Defined
        ↓
Reproducible Processing
        ↓
Controlled Baseline
        ↓
Geometric Verification
        ↓
Independent Ground-Truth Evaluation
        ↓
Representation Comparison
        ↓
Cross-Sensor Testing
        ↓
Scale / Illumination Testing
        ↓
Failure Analysis
        ↓
Reproducibility Check
        ↓
Implementation Decision
```

The decision should be based on measured evidence rather than feature count, visual appeal, or a single successful registration example.

---

## References / Further Reading

### Internal ChandraMap Research

- [Research Documentation](../README.md)
- [Lunar Registration](../notes/lunar-registration.md)
- [Illumination Invariance](../notes/illumination-invariance.md)
- [Scale Invariance](../notes/scale-invariance.md)
- [Ground-Truth Design](../notes/ground-truth-design.md)

### Existing V1 Experiments

- [V1 Experiment Overview](../../experiments/v1/README.md)
- [EXP-001 — SIFT Baseline](../../experiments/v1/baseline/EXP-001-sift-baseline/README.md)
- [EXP-002 — Scale Pyramid](../../experiments/v1/preprocessing/EXP-002-scale-pyramid/README.md)
- [EXP-003 — Gradient Representation](../../experiments/v1/preprocessing/EXP-003-gradient-representation/README.md)
- [EXP-004 — Affine vs Homography](../../experiments/v1/geometry/EXP-004-affine-vs-homography/README.md)
- [EXP-005 — Residual Analysis](../../experiments/v1/geometry/EXP-005-residual-analysis/README.md)
- [EXP-006 — Subpixel Refinement](../../experiments/v1/refinement/EXP-006-subpixel-refinement/README.md)

### Literature

The broader IIRS, hyperspectral-imaging, cross-modal-registration, local-feature, and learned-matching literature should be maintained through the project's literature documentation:

[Research Literature](../literature/README.md)

Specific external references for IIRS registration are:

`[TBD]`

They should be added only after being reviewed and verified for relevance to the ChandraMap research question.

---

## Implementation Status

**Overall status: Planned / Research Direction**

This document defines future IIRS research.

The following are **not established by this document**:

- an implemented IIRS processing pipeline
- a validated IIRS representation
- a validated IIRS-to-OHRC registration method
- a validated IIRS-to-TMC-2 registration method
- an IIRS benchmark dataset
- an IIRS benchmark score
- a universally suitable geometric model
- a validated learned multimodal matcher

Any future implementation should update this document with links to the relevant experiment, configuration, code, benchmark, and measured results.

---

## Validation Checklist

Before considering an IIRS registration method validated, verify:

- [ ] IIRS input data is uniquely identified.
- [ ] Relevant product metadata is preserved.
- [ ] The IIRS representation is explicitly defined.
- [ ] All representation parameters are recorded.
- [ ] Preprocessing is reproducible.
- [ ] The feature detector and descriptor are documented.
- [ ] Matching parameters are documented.
- [ ] Geometric verification parameters are documented.
- [ ] Candidate matches are distinguished from verified inliers.
- [ ] Independent check points are available or their absence is explicitly reported.
- [ ] Check-point error is quantitatively measured.
- [ ] Spatial coverage is evaluated.
- [ ] Failure cases are preserved.
- [ ] Scale effects are investigated where relevant.
- [ ] Illumination effects are investigated where relevant.
- [ ] Cross-sensor behavior is evaluated.
- [ ] Runtime/resource usage is measured where relevant.
- [ ] Results are reproducible under the documented configuration.
- [ ] Comparisons use controlled experimental conditions.
- [ ] Claims are supported by measured evidence.
- [ ] The method is not marked validated solely from visual registration examples.

---

## Summary

IIRS introduces a distinct future research problem within ChandraMap: **how to convert hyperspectral lunar observations into representations that preserve sufficient spatial and geometric information for cross-sensor registration**.

The key research principle is to treat representation selection as an experimentally testable problem.

The recommended progression is:

```text
IIRS Data Validation
        ↓
2D Representation
        ↓
Classical Baseline
        ↓
Geometric Verification
        ↓
Independent Evaluation
        ↓
Representation Comparison
        ↓
IIRS ↔ OHRC / TMC-2
        ↓
Scale + Illumination Studies
        ↓
Multimodal / Learned Matching
        ↓
Failure Analysis
        ↓
Reproducibility Assessment
        ↓
Implementation Decision
```

No representation, geometric model, or matching method should be considered successful until it demonstrates reproducible improvement or adequate performance under controlled, independently evaluated experiments.

**Current conclusion:** IIRS registration remains a **future research direction** for ChandraMap, with representation design, cross-sensor correspondence, geometric modeling, ground truth, and reproducible quantitative evaluation forming the core research problems.
