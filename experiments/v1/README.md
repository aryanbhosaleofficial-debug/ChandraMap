# ChandraMap V1 Experiments

**Path:** `experiments/v1/`
**Version:** V1 — First Measurable End-to-End Baseline
**Status:** Baseline specification / experimental documentation
**Primary objective:** Prove one controlled lunar source/reference registration experiment end-to-end with measurable, independently evaluated results.

---

## 1. Overview

Version 1 (V1) is the **first measurable end-to-end experimental pipeline** for ChandraMap.

V1 is intentionally small.

It is not intended to solve global lunar retrieval, all sensor combinations, illumination invariance, advanced learned matching, or full-Moon registration. Its purpose is to establish a scientifically measurable baseline on a **known overlapping lunar source/reference image pair**.

The core V1 experiment is:

```text
Known Source / Reference Pair
            │
            ▼
      Metadata Inspection
            │
            ▼
Sensor-Aware Preprocessing
            │
            ▼
 Physically Meaningful Scale
            │
            ▼
       SIFT Features
            │
            ▼
    Descriptor Matching
            │
            ▼
      Candidate Matches
            │
            ▼
   RANSAC / Geometric Check
            │
            ▼
     Verified Inliers
            │
            ▼
      Transformation
            │
            ▼
    Registered Overlay
            │
            ▼
 Independent Check Points
            │
            ▼
 Quantitative Evaluation
            │
            ▼
 Reproducible Artifacts
```

The project feedback defines the first milestone similarly: one known source/reference pair should progress through SIFT, RANSAC, transformation and registered overlay, with inlier statistics and independent check-point error.

> **Build small. Measure honestly. Keep the failures.**

---

# 2. What V1 Is

V1 is the first controlled experiment designed to answer:

> **Can ChandraMap take one known overlapping lunar source/reference pair, establish reliable candidate correspondences, geometrically verify them, estimate a transformation, produce a registered result, and measure the registration error independently?**

A successful V1 experiment therefore produces **numerical evidence**, not merely a pipeline diagram or visually convincing overlay.

The project feedback explicitly states that the first milestone becomes meaningful when a source/reference pair produces candidate matches, verified inliers, a final transformation, a registered overlay, and numerical error measured on independent check points.

---

# 3. Why V1 Exists

The complete ChandraMap problem is larger than a single registration experiment.

The project may eventually involve:

- multiple Chandrayaan-2 sensors
- different spatial scales
- different illumination conditions
- cross-modality matching
- reference-image retrieval
- learned local matching
- sub-pixel refinement
- spatial coverage analysis
- geospatial evaluation
- larger benchmark datasets
- potentially broad lunar-scale operation

Attempting all of these simultaneously would make it difficult to determine which part of the system works or fails.

V1 therefore establishes the smallest useful scientific loop:

```text
Input
  ↓
Correspondence
  ↓
Geometric Verification
  ↓
Transformation
  ↓
Registration
  ↓
Independent Measurement
```

This follows the project's recommended build order: first make one real baseline work, then add reference pyramids/retrieval, sensor-aware preprocessing, stronger matchers, refinement, and additional sensors incrementally.

---

# 4. What V1 Is Intended to Prove

V1 is intended to demonstrate that the ChandraMap architecture can produce a **real, measurable registration result** for at least one controlled known-overlap case.

Specifically, V1 should establish whether the experiment can produce:

1. candidate matches
2. geometrically verified inliers
3. a fitted transformation
4. a registered image/overlay
5. independent check-point measurements
6. registration error in source-image pixels
7. useful spatial-coverage information where implemented
8. reproducible experiment artifacts
9. runtime information
10. documented failure behavior

These are functional completion criteria, not claims about overall lunar robustness.

---

# 5. V1 Philosophy

V1 prioritizes:

- **Correctness**
- **Interpretability**
- **Reproducibility**
- **Measurable outputs**
- **Independent evaluation**
- **Failure analysis**
- **Simple experimental design**

V1 does **not** prioritize:

- number of algorithms
- full-Moon coverage
- UI polish
- decorative confidence scores
- premature optimization
- unsupported accuracy claims
- forcing every sensor into one pipeline
- adding advanced methods before the baseline is measured

The project feedback specifically recommends building the simplest measurable version first and becoming more sophisticated only after obtaining actual measurements.

---

# 6. V1 Scope

## In scope

| Area                                 | V1 position                                            |
| ------------------------------------ | ------------------------------------------------------ |
| Known overlapping pair               | **Core V1 scope**                                      |
| Metadata inspection                  | **Core**                                               |
| Basic sensor-aware preprocessing     | **Where applicable**                                   |
| Physically meaningful scale handling | **Core principle**                                     |
| SIFT                                 | **Primary baseline**                                   |
| Descriptor matching                  | **Core**                                               |
| Candidate filtering                  | **Core**                                               |
| RANSAC/geometric verification        | **Core**                                               |
| Verified inliers                     | **Core**                                               |
| Simple transformation                | **Core**                                               |
| Registered output/overlay            | **Core**                                               |
| Independent check points             | **Required methodology**                               |
| Quantitative error                   | **Required**                                           |
| Runtime measurement                  | **Required where execution is available**              |
| Failure recording                    | **Required**                                           |
| Spatial coverage                     | **Required where implemented**                         |
| Sub-pixel refinement                 | **[Planned] unless implementation confirms otherwise** |
| Illumination stress testing          | **[Optional]/[Planned]**                               |
| Multi-sensor evaluation              | **[Planned incremental expansion]**                    |
| Global retrieval                     | **Out of core V1**                                     |
| FAISS retrieval                      | **Later milestone**                                    |
| Learned matching                     | **Later experiment**                                   |

---

# 7. V1 Assumptions

V1 assumes a **known overlapping source/reference pair**.

The experiment is therefore not required to solve the harder question:

> “Where on the Moon is this source image?”

Instead, V1 begins after the relevant source/reference pair has already been identified.

This separation is important because global retrieval and local correspondence are different problems. The project feedback recommends making global search conditional and using available geolocation, footprint, or map-projection metadata where appropriate.

### V1 assumptions

- The source and reference images represent overlapping lunar terrain.
- The relevant image pair is known before local matching begins.
- Relevant metadata should be inspected before processing.
- The images can be converted into a comparison representation appropriate for the selected sensor path.
- A geometric relationship exists that can be approximated by an appropriate local transformation.
- Evaluation reference/check-point information is available or explicitly marked unavailable.
- The experiment records its configuration and outputs.

Where an assumption cannot be established for a particular experiment, it should be recorded rather than silently assumed.

---

# 8. V1 Core Pipeline

## 8.1 Stage 1 — Select a Known Pair

V1 begins with:

```text
Source Image
+
Reference Image
=
Known Overlapping Pair
```

The project feedback recommends using one or two real source/reference pairs rather than screenshots when the files are manageable. Relevant image dimensions, product type, pixel scale/GSD, and available geolocation/map-projection metadata should also be recorded.

---

## 8.2 Stage 2 — Inspect Metadata

Before matching, inspect available metadata.

Where available, record:

- image dimensions
- product type
- sensor
- pixel scale/GSD
- footprint
- map projection
- geolocation
- viewing geometry
- illumination/Sun geometry
- processing state

The exact metadata schema is **[TBD]**.

The project specifically recommends preserving pixel size, footprint, map projection, and lighting/viewing metadata when available.

---

# 9. V1 Sensor Scope

V1 should be developed around a controlled pair rather than requiring every sensor to work immediately.

Potential Chandrayaan-2 source sensors include:

- OHRC
- TMC-2
- IIRS

Potential reference imagery includes:

- LRO NAC
- other explicitly documented lunar reference imagery

The project materials emphasize that OHRC, TMC-2, and IIRS should **not** be treated as identical image inputs.

Therefore:

> V1 does **not** claim complete support for every sensor unless the repository implementation explicitly confirms it.

A first implementation may begin with a smaller sensor subset.

### Current V1 sensor implementation

| Sensor/path              | V1 status                                         |
| ------------------------ | ------------------------------------------------- |
| OHRC                     | **[TBD — implementation confirmation required]**  |
| TMC-2                    | **[TBD — implementation confirmation required]**  |
| IIRS                     | **[Not implemented unless explicitly confirmed]** |
| LRO NAC reference        | **[TBD — experiment/data confirmation required]** |
| Other reference products | **[TBD]**                                         |

The recommended build order is OHRC/TMC-2 first, followed by IIRS as a separate experiment rather than mixing all sensor results into one average.

---

# 10. Sensor Physics

## 10.1 OHRC

OHRC is high-resolution visible/panchromatic lunar imagery.

The project feedback references approximately **0.25–0.32 m/pixel** depending on documentation/product and explicitly recommends using the challenge product metadata as the authoritative pixel-scale value for an actual experiment.

Therefore V1 should record the actual product GSD rather than hard-coding a generic value.

---

## 10.2 TMC-2

TMC-2 is panchromatic terrain imagery in approximately the meter-scale class, with project materials referencing approximately **5 m/pixel**.

It can provide a structural bridge between higher-resolution imagery and coarser lunar products.

The actual experiment metadata remains authoritative.

---

## 10.3 IIRS

IIRS is an imaging infrared hyperspectral sensor with substantially coarser spatial resolution and many spectral bands.

The project feedback describes approximately **80 m/pixel** and hundreds of contiguous spectral bands in the relevant material.

IIRS should **not** be treated as an ordinary single-band 2D camera image.

If IIRS enters a V1 experiment, a documented representation step is required.

Possible representations identified by the project materials include:

- selected band
- PCA/composite
- structural map

These are experiment candidates, not an implemented V1 guarantee.

---

## 10.4 Reference Imagery

Reference imagery may have substantially finer spatial resolution than the source.

The project materials identify LRO NAC as a potential high-resolution lunar reference source and emphasize that reference imagery should be brought to a comparable effective ground scale before coarse matching.

---

# 11. Scale Handling

Scale is a physical-data problem, not merely an image-resizing problem.

> **Upsampling changes pixel count; it does not recover spatial information that was absent from the source sensor.**

The project feedback explicitly warns against enlarging coarse IIRS imagery to high-resolution reference scale and treating the resulting pixels as newly recovered detail.

V1 should record, where available:

- source GSD
- reference GSD
- effective comparison scale
- scale factor
- resizing/downsampling method
- interpolation method
- reference pyramid level, if used

### Preferred conceptual approach

```text
Source
  │
  │ source GSD
  ▼
Comparable Effective Scale
  ▲
  │
Reference Pyramid / Downsampled Reference
  │
  │ reference GSD
  ▼
Local Matching
```

A higher-resolution reference may therefore be downsampled or represented using a pyramid before SIFT matching.

Fine refinement must not imply spatial accuracy beyond what the source image actually contains.

---

# 12. Illumination

Illumination is a known V1 limitation.

The Moon's changing Sun geometry can alter terrain appearance, especially crater shadows.

A simple brightness or contrast normalization cannot recreate shadow geometry produced under a different illumination direction.

Therefore V1 must **not** claim illumination invariance.

Where metadata is available, record:

- Sun geometry
- viewing geometry
- illumination condition
- relevant acquisition information

Potential later experiments may compare:

```text
Similar illumination
        vs
Different illumination
```

The project recommends using terrain structure such as:

- crater rims
- ridge lines
- edges
- gradients
- structural representations

when investigating illumination sensitivity.

### Current V1 status

**General illumination invariance:** `[Not implemented / TBD]`

**Illumination stress experiment:** `[Optional / Planned unless confirmed]`

---

# 13. Preprocessing

Preprocessing should make the images more comparable without pretending missing spatial information exists.

For OHRC/TMC-2-type paths, the project recommends considering:

- calibrated or standard products where available
- preservation of geometry metadata
- light denoising
- local contrast normalization after testing
- raw grayscale versus structural representations
- comparable ground scale before matching

For IIRS, the project recommends sensor-specific preparation and a registration-friendly 2D representation rather than directly feeding a full hyperspectral cube into a normal 2D matcher.

The exact V1 preprocessing implementation is:

> **[TBD — use the implementation/configuration as the authoritative source.]**

Every preprocessing operation used in an experiment should be recorded.

---

# 14. Matching Strategy

## Primary V1 baseline: SIFT

The primary V1 local matching baseline is:

```text
SIFT
 ↓
Descriptor Matching
 ↓
Candidate Filtering
 ↓
RANSAC
```

The project feedback recommends SIFT as the first serious accuracy baseline because it is simple, explainable, and provides a reference number for later methods.

### Why SIFT?

SIFT provides:

- an established baseline
- interpretable keypoints/descriptors
- scale-aware and rotation-aware local features
- a relatively simple experimental path
- a reference against which later methods can be compared

It can nevertheless struggle under strong:

- illumination differences
- modality differences
- extreme scale differences

Therefore SIFT is a **baseline**, not a claim of lunar invariance.

---

# 15. Candidate Matches

The output of descriptor matching should be called:

> **Candidate Matches**

It should **not** be described as verified or correct merely because a matcher assigned a high confidence or similarity value.

The project feedback explicitly recommends renaming such outputs to “Candidate Matches” because matcher confidence does not prove geometric correctness. RANSAC/geometric verification determines which candidates become verified inliers.

Conceptually:

```text
SIFT + Descriptor Matching
            ↓
      Candidate Matches
            ↓
       RANSAC Check
            ↓
     Verified Inliers
```

---

# 16. Candidate Filtering

Possible filtering components include:

- descriptor-distance filtering
- ratio test
- cross-check
- duplicate removal
- other explicitly configured candidate filtering

The project materials describe the baseline as:

```text
SIFT
→ descriptor matching
→ ratio/cross-check filtering
→ RANSAC
→ affine/homography
→ residual error
```

The exact V1 filter configuration is:

- Ratio test: `[TBD]`
- Cross-check: `[TBD]`
- Other filtering: `[TBD]`

Do not claim a filtering method is implemented unless the actual experiment configuration confirms it.

---

# 17. Geometric Verification

Candidate matches must be geometrically verified.

```text
Candidate Matches
        │
        ▼
RANSAC + Initial Geometric Model
        │
        ▼
Verified Inliers
```

RANSAC is used to reject correspondences that are inconsistent with the selected geometric model.

The exact V1 configuration must be recorded.

| Parameter                | V1 value |
| ------------------------ | -------- |
| Geometric model          | `[TBD]`  |
| RANSAC implementation    | `[TBD]`  |
| Reprojection threshold   | `[TBD]`  |
| Confidence               | `[TBD]`  |
| Maximum iterations       | `[TBD]`  |
| Minimum required inliers | `[TBD]`  |
| Random seed              | `[TBD]`  |

No numerical parameter values should be inferred from this README.

---

# 18. Verified Inliers

After geometric verification:

```text
Candidate Matches
        ↓
      RANSAC
        ↓
Verified Inliers
```

The **verified inlier count** and **inlier ratio** should be recorded.

A high number of candidate matches is not sufficient.

Likewise:

> More inliers are not automatically better if the points are incorrect or spatially clustered.

The project specifically recommends measuring spatial coverage in addition to inlier statistics.

---

# 19. Transformation Model

V1 may use a simple local transformation model such as:

- affine
- homography

The actual model must be determined by the experiment implementation.

> **V1 transformation model: `[TBD]`**

The project guidance recommends using the simplest transformation that explains the observed residuals. Affine or homography can be reasonable first models for a local, already map-projected pair, but a single global model should not be assumed to be universally correct for lunar imagery.

---

# 20. Lunar Geometry Limitation

The Moon is not a flat image plane.

A single global homography can therefore be insufficient when the experiment includes:

- relief
- viewpoint differences
- raw sensor geometry
- larger spatial extent
- non-planar terrain effects

Residual vectors should be inspected across the image.

For example:

```text
Residuals
   │
   ├── approximately uniform
   │       ↓
   │   model may be adequate
   │
   └── systematic spatial variation
           ↓
       model may be insufficient
```

The project recommends considering sensor geometry, DEM information, or later local/piecewise warping where systematic residual structure is observed.

Such advanced warping is **not automatically part of V1**.

---

# 21. Map Projection and Orthorectification

If the source/reference products are already:

- map-projected
- orthorectified
- geometrically corrected

that information should be used.

V1 should not force computer vision to rediscover geometric information that is already present in the product metadata or mapping pipeline.

The project feedback explicitly recommends using existing map-projection/orthorectification information before asking the vision algorithm to solve the same geometry.

---

# 22. Sub-Pixel Refinement

The intended refined geometry sequence is:

```text
LOCAL MATCHES
      ↓
RANSAC
      ↓
VERIFIED INLIERS
      ↓
SUB-PIXEL TIE-POINT REFINEMENT
      ↓
FINAL TRANSFORMATION
      ↓
REGISTERED IMAGE
```

The project feedback recommends refining the verified inlier coordinates locally and then refitting the final transformation.

### V1 status

> **Sub-pixel refinement: `[Not implemented / TBD]`**

If it is not implemented in the current V1 code, V1 must not claim that it provides a completed sub-pixel refinement stage.

Potential later methods include patch-based correlation, phase-based refinement, or an appropriate planetary registration tool, but these are future implementation options unless explicitly confirmed.

---

# 23. Registration

Once the transformation has been estimated, V1 produces a registered result.

Conceptually:

```text
Source Image
     +
Estimated Transformation
     ↓
Registered Source
     +
Reference
     ↓
Overlay / Comparison
```

The registered image is an experimental output.

It is not itself proof that the transformation is correct.

Visual inspection should be accompanied by quantitative evaluation.

The project feedback specifically warns that a visually good overlay can still be scientifically wrong.

---

# 24. Evaluation Is Part of V1

Evaluation is not an optional visualization step.

The core V1 question is not:

> “Does the overlay look good?”

It is:

> “How accurately does the estimated transformation predict independently checked correspondence locations?”

The project explicitly prioritizes measurable evaluation over adding more pipeline boxes.

---

# 25. Independent Check Points

This is a required V1 methodology.

> **Do not fit and evaluate the transformation using exactly the same points without clearly identifying the result as fitting error.**

If the transformation is estimated from a set of inliers and RMSE is calculated on those same points, the reported error may look better than actual registration performance.

Preferred structure:

```text
Control / Fitting Points
        │
        ▼
Transformation Estimation
        │
        ▼
Final Transformation
        │
        ▼
Independent Check Points
        │
        ▼
Registration Error
```

The project guidance recommends challenge ground truth where available; otherwise independently checked tie points should be retained as check points and excluded from transformation fitting.

---

# 26. Error Units

Report registration error in **source-image pixels first**.

This is important because the same pixel error can correspond to very different physical ground errors for sensors with different GSDs.

For example, the project feedback explicitly notes that an identical pixel error on TMC-2 and IIRS does not represent the same ground distance.

Ground error in metres should therefore only be reported when:

- GSD is known
- projection/reference geometry makes the conversion meaningful
- appropriate reference/check information exists

---

# 27. V1 Metrics

## 27.1 Matching Metrics

| Metric                | Purpose                                                            |
| --------------------- | ------------------------------------------------------------------ |
| Candidate match count | Measures number of proposed correspondences                        |
| Verified inlier count | Measures geometrically accepted correspondences                    |
| Inlier ratio          | Measures proportion of candidates surviving geometric verification |

---

## 27.2 Spatial Metrics

Where implemented:

| Metric               | Purpose                                           |
| -------------------- | ------------------------------------------------- |
| Grid coverage        | Measures how broadly good matches are distributed |
| Convex-hull coverage | Measures spatial extent of verified points        |
| Spatial density      | Measures distribution of useful correspondences   |

The project specifically recommends grid or convex-hull coverage because good points concentrated around one crater do not necessarily provide reliable registration across the overlap.

---

## 27.3 Registration Metrics

| Metric                    | Primary unit                                |
| ------------------------- | ------------------------------------------- |
| Check-point RMSE          | Source-image pixels                         |
| Median check-point error  | Source-image pixels                         |
| Maximum check-point error | Source-image pixels                         |
| Residuals                 | Source-image pixels                         |
| Ground error              | Metres, only when scientifically meaningful |

---

## 27.4 System Metrics

| Metric       | Purpose                                |
| ------------ | -------------------------------------- |
| Runtime      | Execution cost                         |
| Failure rate | Reliability across defined experiments |

---

# 28. V1 Evaluation Table

A V1 experiment should aim to produce a record similar to:

| Category          | Measurement                                |
| ----------------- | ------------------------------------------ |
| Candidate matches | `[TBD / measured]`                         |
| Verified inliers  | `[TBD / measured]`                         |
| Inlier ratio      | `[TBD / measured]`                         |
| Spatial coverage  | `[TBD / measured / not implemented]`       |
| Check-point RMSE  | `[TBD / measured]`                         |
| Median error      | `[TBD / measured]`                         |
| Maximum error     | `[TBD / measured]`                         |
| Ground error      | `[TBD / not meaningful / not implemented]` |
| Runtime           | `[TBD / measured]`                         |
| Failure status    | `[TBD / measured]`                         |

No placeholder value should be presented as a result.

---

# 29. V1 Outputs

The following outputs are expected or recommended depending on implementation.

| Artifact                         | Status                           |
| -------------------------------- | -------------------------------- |
| Source image reference           | **Required input**               |
| Reference image reference        | **Required input**               |
| Metadata record                  | **Expected**                     |
| Preprocessed image               | **If preprocessing is applied**  |
| Candidate-match visualization    | **Expected**                     |
| Rejected-match visualization     | **Expected where implemented**   |
| RANSAC inlier visualization      | **Expected**                     |
| Transformation parameters/matrix | **Expected**                     |
| Registered image                 | **Expected**                     |
| Registered overlay               | **Expected**                     |
| Residual/error visualization     | **Expected where implemented**   |
| Check-point results              | **Required methodology**         |
| Metrics record                   | **Expected**                     |
| Experiment configuration         | **Expected**                     |
| Execution log                    | **Expected where supported**     |
| Runtime record                   | **Expected**                     |
| Failure record                   | **Required when failure occurs** |

The project feedback specifically recommends saving match plots, rejected outliers, registered overlays, inlier statistics, and check-point error for the first known-pair milestone.

The exact filenames and serialization formats are:

> **[TBD]**

---

# 30. Experiment Progression

V1 should progress incrementally rather than forcing every planned feature into the first experiment.

## V1-A — Known Pair Baseline

### Goal

Establish the first complete measurable path.

```text
Known Pair
   ↓
SIFT
   ↓
Descriptor Matching
   ↓
Candidate Matches
   ↓
RANSAC
   ↓
Verified Inliers
   ↓
Transformation
   ↓
Registered Overlay
   ↓
Independent Check-Point Error
```

### Save

- candidate-match plot
- rejected outliers
- verified inliers
- registered overlay
- inlier statistics
- check-point error
- runtime
- experiment configuration

This is the **primary V1 milestone** described by the project feedback.

---

# 31. V1-B — Scale / Preprocessing Experiment

**Status:** `[Planned / Optional]`

Where the implementation supports it, compare:

```text
Direct Matching
       vs
Physically Meaningful Scale Handling
```

The same source/reference pair should be used where possible.

Record:

- scale configuration
- preprocessing configuration
- candidate matches
- verified inliers
- spatial coverage
- check-point error
- runtime

The purpose is to measure whether scale handling changes the result rather than assuming that it does.

---

# 32. V1-C — Illumination Stress

**Status:** `[Planned / Optional]`

Compare:

```text
Similar Illumination
        vs
Different Illumination
```

The project specifically recommends a Sun-angle stress test using the same region under similar and substantially different lighting, followed by honest reporting of performance change.

If not implemented:

> **Illumination stress testing: Not implemented in V1.**

---

# 33. V1-D — Additional Refinement

**Status:** `[Planned / Optional]`

Potential components:

- sub-pixel refinement
- final transformation refitting
- spatial coverage
- improved residual analysis

The intended geometry order is:

```text
Candidate Matches
       ↓
RANSAC
       ↓
Verified Inliers
       ↓
Sub-Pixel Refinement
       ↓
Final Transformation
       ↓
Registration
```

Do not report this stage as implemented until the implementation exists.

---

# 34. Stress-Test Matrix

V1 should maintain a small stress-test concept rather than claiming general robustness.

| Stress Case          | Purpose                          | V1 Status     |
| -------------------- | -------------------------------- | ------------- |
| Easy pair            | Validate end-to-end operation    | **Core**      |
| Sun-angle difference | Measure illumination sensitivity | **[Planned]** |
| Scale difference     | Measure scale handling           | **[Planned]** |
| Modality difference  | Test sensor representation       | **[Planned]** |
| Geometry difference  | Test transformation assumptions  | **[Planned]** |
| Low-feature terrain  | Expose false-match behavior      | **[Planned]** |

The stress-test categories come directly from the project's technical feedback.

---

# 35. Failure Analysis

V1 must document failures rather than hide them.

Possible failure categories include:

- insufficient features
- repetitive terrain
- incorrect candidate matches
- insufficient verified inliers
- poor spatial distribution
- scale mismatch
- illumination difference
- modality difference
- geometric-model mismatch
- insufficient metadata
- insufficient check points
- invalid evaluation reference
- registration output failure

A failure should identify **where the pipeline failed**.

---

# 36. Failure Record

For each meaningful failure, record:

```text
Experiment ID
Input pair
Sensor / product
Configuration
Failure stage
Observed behavior
Candidate match count
Inlier count
Inlier ratio
Coverage, if available
Check-point result, if available
Runtime
Visualization/artifact
Suspected cause
Next experiment
```

This is important because failure cases identify whether the next improvement should target:

- scale
- illumination
- modality
- retrieval
- geometry
- matching
- sub-pixel refinement

The project feedback explicitly recommends preserving failures and using measured evidence to diagnose the actual failure mode.

---

# 37. V1 Success Criteria

V1 is **functionally complete** when a known source/reference pair can produce the following measurable chain:

- [ ] Candidate matches
- [ ] Verified inliers
- [ ] Fitted transformation
- [ ] Registered output/overlay
- [ ] Quantitative registration error
- [ ] Independent check-point evaluation
- [ ] Spatial coverage information where implemented
- [ ] Reproducible experiment artifacts
- [ ] Runtime information
- [ ] Failure behavior documented

These criteria do not require an arbitrary numerical accuracy threshold unless the benchmark specification explicitly defines one.

If a numerical acceptance threshold is later established:

> **Threshold: `[TBD]`**

Do not invent a target RMSE, inlier ratio, coverage percentage, or runtime.

---

# 38. What V1 Does Not Prove

A successful V1 result does **not** prove:

- full-Moon registration
- global lunar retrieval
- universal illumination invariance
- universal scale invariance
- cross-sensor robustness
- IIRS registration
- learned-matcher superiority
- sub-pixel refinement unless implemented and measured
- geospatial accuracy in metres unless appropriately evaluated
- robustness to all terrain types
- production readiness

V1 proves only what its controlled experiment measures.

---

# 39. Explicitly Out of Scope

The following are explicitly outside the core V1 objective.

## Global lunar retrieval

Global search and candidate retrieval are later-stage capabilities.

The project feedback describes a future architecture using:

```text
Reference Images
      ↓
Tiles + Scales
      ↓
Global Descriptor
      ↓
FAISS Index + Metadata
      ↓
Top-K Candidates
```

and then local matching on selected candidates.

This is not required for the first known-pair V1 experiment.

---

## FAISS retrieval

**Later milestone.**

FAISS is relevant when ChandraMap begins retrieving candidate lunar regions from a reference database.

It is not necessary to establish the first known-pair local correspondence baseline.

---

## Advanced learned matchers

Methods such as:

- ALIKED + LightGlue
- LoFTR

are later comparison candidates.

The project recommends starting with SIFT and then testing stronger methods on the same pairs.

They should not be represented as V1 baseline methods unless explicitly implemented.

---

## RIFT / CFOG

These are research directions for difficult multimodal matching.

They are not assumed to be V1 implementations.

---

## Full IIRS pipeline

IIRS requires a sensor-specific representation step.

A complete hyperspectral registration pipeline is not a prerequisite for the first V1 result.

---

## Full-Moon operation

V1 is not a full-Moon system.

---

## Complete UI

The project feedback recommends building the UI after the core measurable pipeline is working.

---

## Production deployment

V1 is an experimental baseline, not a production deployment specification.

---

# 40. Baseline Comparison

Later improvements should be evaluated under comparable conditions.

The recommended conceptual comparison is:

```text
1. SIFT Baseline
        │
        ▼
2. Stronger Matcher Only
        │
        ▼
3. Full Sensor-Aware + Multi-Scale Pipeline
```

The same image pairs and evaluation conditions should be used when making a comparison.

The project feedback recommends this approach because it helps determine which pipeline change actually produced an improvement.

Do not report comparative improvement until actual measurements exist.

---

# 41. Reproducibility

Every V1 experiment should capture enough information to reproduce or audit the result.

## Experiment identity

| Field               | Value                         |
| ------------------- | ----------------------------- |
| Experiment ID       | `[TBD]`                       |
| V1 experiment stage | `[V1-A / V1-B / V1-C / V1-D]` |
| Git commit          | `[TBD]`                       |
| Dataset version     | `[TBD]`                       |
| Source image ID     | `[TBD]`                       |
| Reference image ID  | `[TBD]`                       |
| Configuration       | `[TBD]`                       |

---

## Software environment

| Field                   | Value   |
| ----------------------- | ------- |
| Python version          | `[TBD]` |
| Dependency versions     | `[TBD]` |
| Operating system        | `[TBD]` |
| OpenCV version          | `[TBD]` |
| Other relevant packages | `[TBD]` |

---

## Hardware environment

| Field        | Value          |
| ------------ | -------------- |
| CPU          | `[TBD]`        |
| GPU          | `[TBD / None]` |
| CUDA version | `[TBD / N/A]`  |
| RAM          | `[TBD]`        |

---

## Execution

| Field            | Value         |
| ---------------- | ------------- |
| Random seed      | `[TBD / N/A]` |
| Command          | `[TBD]`       |
| Output directory | `[TBD]`       |
| Runtime          | `[TBD]`       |

The exact execution command is not established by the available project materials.

Therefore:

```bash
# V1 experiment
[TBD]
```

should be replaced by the actual repository command once implemented.

---

# 42. Reproducibility Principle

A V1 result should be traceable through:

```text
Code
  +
Configuration
  +
Dataset / Image IDs
  +
Ground Truth / Check Points
  +
Environment
  +
Execution
  +
Artifacts
  +
Metrics
```

A visual screenshot without this context is not sufficient for a reproducible scientific result.

---

# 43. Experiment Organization

The confirmed documentation path is:

```text
experiments/
└── v1/
    └── README.md
```

The exact current contents of `experiments/v1/` beyond this README are **[TBD / not confirmed by the available project documentation]**.

Do not assume that scripts, notebooks, results, configurations, or output directories already exist unless confirmed by the repository.

A future organization may be introduced when implementation requires it, but such a structure should be documented when actually created rather than presented as existing infrastructure.

---

# 44. Individual Experiment Reports

The repository contains the common experiment template:

```text
experiments/templates/EXPERIMENT_TEMPLATE.md
```

Individual V1 experiments should use the common template where appropriate.

The division of responsibility should be:

| Documentation                                  | Purpose                                                                                   |
| ---------------------------------------------- | ----------------------------------------------------------------------------------------- |
| `experiments/v1/README.md`                     | Defines V1 methodology, scope, pipeline, criteria, and experimental strategy              |
| `experiments/templates/EXPERIMENT_TEMPLATE.md` | Defines the common structure for recording an individual experiment                       |
| Benchmark documentation                        | Defines controlled benchmark datasets, metrics, evaluation rules, and acceptance criteria |
| Dataset documentation                          | Defines data provenance, organization, preparation, and lifecycle                         |

The V1 README should therefore **not duplicate the complete experiment-report template**.

Instead, individual experiment reports should record the actual:

- input pair
- configuration
- environment
- processing decisions
- metrics
- artifacts
- failures
- conclusions

for that specific run.

---

# 45. V1 and Benchmarking

V1 experiments and formal benchmark evaluation serve related but distinct purposes.

```text
V1 Experiment
    ↓
Prove pipeline works
    ↓
Measure one/few controlled cases
    ↓
Identify failures
    ↓
Stabilize methodology
    ↓
Formal Benchmark
```

A V1 result should not automatically be presented as a benchmark result.

Benchmark claims should follow the project's defined benchmark protocol and independent evaluation rules.

---

# 46. V1 and Ground Truth

Ground truth/check-point information is essential when evaluating registration accuracy.

The project evaluation methodology distinguishes between:

- points used for fitting
- independent check points used for evaluation

The latter should not be used to fit the final transformation.

Conceptually:

```text
Candidate Matches
       ↓
RANSAC
       ↓
Verified Inliers
       ↓
Fit / Refine Transformation
       ↓
Independent Check Points
       ↓
Registration Error
```

Ground-truth definitions and storage belong to the project's ground-truth/benchmark documentation rather than being redefined here.

---

# 47. V1 Data Leakage

Development and evaluation must remain distinguishable.

Avoid tuning the V1 pipeline against independent evaluation points and then reporting those same points as unbiased evidence.

Potential leakage includes:

- using check points to fit the transform
- repeatedly tuning parameters against the final evaluation case
- using benchmark results to select the method and then presenting that same result as an unbiased comparison
- undocumented reuse of evaluation imagery during development
- treating development samples as representative benchmark evidence

When an experiment uses a case for development rather than final evaluation, document that role.

---

# 48. Metadata and Physical Context

A V1 experiment should preserve physical context whenever available.

Relevant fields include:

- source sensor
- reference product
- image dimensions
- GSD/pixel scale
- footprint
- projection
- viewing geometry
- illumination geometry
- processing state

The project feedback emphasizes that these details are necessary to interpret the registration result correctly.

---

# 49. What Counts as Evidence?

A V1 experiment should produce evidence at multiple stages.

### Matching evidence

```text
Candidate Match Plot
```

### Verification evidence

```text
Rejected Candidates
+
Verified Inliers
```

### Geometry evidence

```text
Transformation
+
Residuals
```

### Registration evidence

```text
Registered Overlay
```

### Scientific evidence

```text
Independent Check-Point Error
+
Inlier Statistics
+
Spatial Coverage
+
Runtime
```

The project feedback specifically asks for actual numbers such as inliers, coverage, check-point RMSE/error, and runtime rather than decorative percentages or ratings.

---

# 50. Avoid Decorative Metrics

V1 must not contain unsupported statements such as:

- “92% accurate”
- “high confidence”
- “excellent registration”
- “production-ready”
- “robust to all illumination”
- “works across all sensors”

unless the claim is supported by actual documented experiments.

The project feedback specifically recommends removing unmeasured percentage/star ratings and replacing them with real RMSE, inlier ratio, coverage, and runtime measurements.

---

# 51. V1 Failure Is a Valid Result

A V1 experiment that fails is not automatically a useless experiment.

A failure can identify:

```text
Failure
  ↓
Failure stage
  ↓
Measured evidence
  ↓
Likely cause
  ↓
Next experiment
```

For example:

```text
Insufficient Inliers
        ↓
Check candidate distribution
        ↓
Check scale
        ↓
Check illumination
        ↓
Check modality
        ↓
Check preprocessing
```

The objective is to understand the system, not to hide difficult cases.

---

# 52. V1 Limitations

V1 has deliberate limitations.

## Known limitations

### Limited experiment size

V1 begins with one known overlapping pair rather than a large lunar-scale dataset.

### Limited local baseline

SIFT is used as the initial local matching baseline.

### Illumination sensitivity

SIFT and simple preprocessing are not assumed to solve strong Sun-angle differences.

### Scale limitations

Resizing cannot recover spatial information absent from the source.

### Geometric-model limitations

Affine/homography models may be insufficient for complex lunar geometry.

### Sensor limitations

Different sensors may require different processing paths.

### Evaluation limitations

Ground error in metres is only meaningful when the necessary GSD, projection, and reference information exist.

### Retrieval limitation

Global reference retrieval is outside the core V1 known-pair experiment.

### Sub-pixel limitation

Sub-pixel refinement is **[Not implemented / TBD]** unless explicitly confirmed by the implementation.

---

# 53. V1 Status Matrix

| Capability               | V1 status                                             |
| ------------------------ | ----------------------------------------------------- |
| Known-pair experiment    | **Core V1**                                           |
| SIFT baseline            | **Primary planned/required baseline**                 |
| Descriptor matching      | **Core**                                              |
| Candidate filtering      | **Core / configuration TBD**                          |
| RANSAC verification      | **Core**                                              |
| Verified inliers         | **Core**                                              |
| Transformation           | **Core / model TBD**                                  |
| Registered overlay       | **Core**                                              |
| Independent check points | **Required methodology**                              |
| Source-pixel error       | **Required evaluation**                               |
| Spatial coverage         | **Required where implemented**                        |
| Runtime                  | **Required measurement where execution is available** |
| Failure logging          | **Required**                                          |
| Reference pyramid        | **Planned / experiment-dependent**                    |
| Illumination stress      | **Planned / optional**                                |
| Sub-pixel refinement     | **[Not implemented / TBD]**                           |
| Global retrieval         | **Later milestone**                                   |
| FAISS                    | **Later milestone**                                   |
| ALIKED + LightGlue       | **Later comparison**                                  |
| LoFTR                    | **Later comparison**                                  |
| RIFT                     | **Research direction**                                |
| CFOG                     | **Research direction**                                |
| Full IIRS path           | **Separate later experiment**                         |
| Full-Moon operation      | **Out of V1 scope**                                   |
| Production deployment    | **Out of V1 scope**                                   |

---

# 54. V1 → Later Versions

V1 is the foundation for subsequent ChandraMap development.

The recommended progression is:

```text
V1
Known Pair
SIFT
RANSAC
Transform
Independent Evaluation
        │
        ▼
V2 / Later
Scale + Illumination
Reference Pyramid
Sensor-Aware Processing
        │
        ▼
Retrieval
Reference Database
Global Descriptors
FAISS / Top-K
        │
        ▼
Stronger Matching
ALIKED + LightGlue
LoFTR
Other Research Methods
        │
        ▼
Refinement
Sub-Pixel Tie Points
Final Refitting
Spatial Coverage
        │
        ▼
Additional Sensors
OHRC
TMC-2
IIRS
        │
        ▼
Broader Benchmarking
Stress Tests
Multiple Regions
Failure Analysis
```

This progression follows the practical build order in the project feedback.

---

# 55. V1 Completion Gate

Before moving aggressively into later pipeline components, V1 should answer:

### Data

- [ ] Is the source/reference pair known?
- [ ] Is provenance recorded?
- [ ] Are image dimensions recorded?
- [ ] Is pixel scale/GSD recorded where available?
- [ ] Is relevant projection/geolocation metadata recorded?

### Matching

- [ ] Does SIFT produce candidate matches?
- [ ] Are candidate matches clearly distinguished from verified inliers?
- [ ] Is candidate filtering recorded?

### Geometry

- [ ] Does RANSAC produce verified inliers?
- [ ] Is the transformation model documented?
- [ ] Are residuals available?
- [ ] Is spatial distribution inspected?

### Registration

- [ ] Is a registered output produced?
- [ ] Is an overlay available?
- [ ] Is the result visually inspectable?

### Evaluation

- [ ] Are independent check points available?
- [ ] Were they excluded from transformation fitting?
- [ ] Is source-image pixel error reported?
- [ ] Is spatial coverage reported where implemented?
- [ ] Is ground error reported only where meaningful?
- [ ] Is runtime recorded?

### Reproducibility

- [ ] Is the Git commit recorded?
- [ ] Is the experiment configuration recorded?
- [ ] Are image IDs recorded?
- [ ] Is the environment recorded?
- [ ] Are artifacts preserved?

### Failure analysis

- [ ] Are failures retained?
- [ ] Is the failure stage recorded?
- [ ] Are actual measurements preserved?
- [ ] Is the next experiment identifiable?

---

# 56. V1 Definition of Done

V1 is ready to serve as the project's first end-to-end baseline when:

```text
A known overlapping lunar source/reference pair
                    │
                    ▼
             SIFT matching
                    │
                    ▼
            Candidate matches
                    │
                    ▼
       RANSAC geometric verification
                    │
                    ▼
            Verified inliers
                    │
                    ▼
           Transformation
                    │
                    ▼
          Registered output
                    │
                    ▼
        Independent check points
                    │
                    ▼
      Quantitative registration error
                    │
                    ▼
      Reproducible experiment record
```

can be executed and documented with actual measurements.

No arbitrary accuracy threshold is introduced here.

If the benchmark specification establishes numerical acceptance criteria, those criteria should be referenced from the benchmark documentation rather than invented in this README.

---

# 57. Core V1 Principle

The central V1 engineering rule is:

> **Do not build the entire ChandraMap system before proving that one measurable registration path works.**

The project feedback repeatedly emphasizes this build order: start with a real overlapping pair, obtain SIFT/RANSAC/transformation/overlay results, record numerical evidence, and only then expand into retrieval, stronger matchers, refinement, and additional sensors.

The V1 story is therefore deliberately simple:

```text
One Pair
   ↓
One Baseline
   ↓
One Measurable Registration
   ↓
One Independent Evaluation
   ↓
Document What Worked
   ↓
Document What Failed
   ↓
Improve One Component at a Time
```

---

# 58. Final V1 Principle

> **Build small. Measure honestly. Keep the failures.**

V1 is not the complete ChandraMap system.

It is the experimental foundation that makes later claims testable.

A successful V1 result should therefore be described precisely:

> **ChandraMap demonstrated a complete correspondence-and-registration workflow on a defined lunar source/reference pair, with candidate matches, geometrically verified inliers, a fitted transformation, registered output, and independently measured registration error.**

Anything beyond that—global retrieval, multi-sensor robustness, illumination invariance, advanced learned matching, sub-pixel refinement, or broad lunar-scale operation—must be demonstrated separately through measured experiments rather than inferred from the V1 baseline.
