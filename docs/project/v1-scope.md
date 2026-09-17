# ChandraMap Benchmark V1 Scope

This document defines the **human-facing scientific and engineering scope of ChandraMap Benchmark V1**.

Benchmark V1 establishes the simplest credible, reproducible classical baseline for known-overlap lunar image correspondence and registration.

In one sentence:

> **Benchmark V1 asks whether a simple classical known-overlap local-registration pipeline can establish geometrically valid lunar correspondences, estimate a useful source-to-reference transformation, produce a registration, measure its quality, and explicitly reject cases where the evidence is insufficient.**

This document defines **what belongs in V1 and what does not**.

It does not describe implementation tasks, exact metric formulas, benchmark results, or software release history.

The canonical AI/engineering contract remains [`.ai/context/V1_SCOPE.md`](../../.ai/context/V1_SCOPE.md). This human-facing document must remain scientifically consistent with it.

---

## 1. Purpose

Benchmark V1 exists to provide ChandraMap with a stable classical reference point.

It should be:

- simple
- classical
- explainable
- reproducible
- CPU-capable where practical
- dependency-light
- measurable
- testable
- easy to diagnose
- explicit about failure
- intentionally limited

V1 is **not** intended to become the strongest possible ChandraMap configuration.

Its value comes from being a trustworthy baseline against which later improvements can be measured.

---

## 2. Why V1 Exists

Without a controlled baseline, it becomes difficult to determine whether later improvements come from:

- sensor-aware preprocessing
- physical scale handling
- reference pyramids
- learned matching
- global retrieval
- sub-pixel refinement
- terrain-aware geometry
- stronger failure handling

or simply from changing many parts of the system at once.

V1 provides the control/reference configuration needed to answer questions such as:

> Did V2 improve scale robustness?

> Did V3 improve correspondence?

> Did V4 improve final registration quality?

A later method is meaningful only when its improvement can be compared against a stable baseline.

---

## 3. Benchmark V1 Is Not Software v1.0.0

Benchmark V1 is a **research and pipeline configuration**.

It is not automatically:

- software `v1.0.0`
- the first GitHub release
- API version 1
- package major version 1
- a release milestone

Benchmark versions and software versions are independent concepts.

```text
Benchmark V1
≠
Software v1.0.0
```

---

## 4. Scientific Question

The core V1 research question is:

> **Given a source lunar image and the correct overlapping reference region, can a classical local-feature pipeline find enough geometrically valid correspondences to estimate a useful registration and report measurable quality or explicit failure?**

V1 therefore focuses on:

1. local feature extraction
2. candidate correspondence generation
3. geometric verification
4. transformation estimation
5. spatial support
6. registration
7. quantitative evaluation
8. accept/reject behavior

It does **not** attempt to solve the complete whole-Moon localization problem.

---

## 5. Core Assumptions

### 5.1 Known Overlap

Canonical V1 assumes that the correct approximate overlapping reference region is already available.

The problem is:

> Given the correct overlapping region, can the source and reference be registered reliably?

It is not:

> Where on the entire Moon does this image belong?

This separates the **local registration problem** from the **global localization/retrieval problem**.

---

### 5.2 Registration-Ready 2D Input

V1 primarily operates on registration-ready 2D representations.

Examples may include:

- panchromatic lunar imagery
- grayscale-compatible scientific imagery
- controlled benchmark crops
- explicitly derived 2D representations where allowed

V1 does not imply direct support for every mission file format or scientific product representation.

---

### 5.3 Local Registration

V1 evaluates local image correspondence and global 2D geometric alignment over a supplied overlapping region.

It does not require:

- whole-Moon search
- global candidate retrieval
- planetary-scale localization

---

### 5.4 Baseline Geometry

V1 uses simple global 2D geometric models such as:

- affine transformation
- homography

according to the canonical benchmark configuration.

These are baseline approximations.

They are not claimed to fully represent arbitrary lunar terrain relief.

---

## 6. Inputs

Conceptually, V1 requires:

### Required

- source image or registration-ready 2D representation
- known overlapping reference image or reference region

### Optional Where Available

- product identity
- sensor identity
- GSD or scale metadata
- valid-data mask
- projection/geospatial metadata
- provenance information
- independent check points

This document does not define concrete schemas or class names.

Actual repository contracts remain authoritative.

---

## 7. Sensor Context in V1

### OHRC

OHRC-derived 2D imagery may be used in V1 where appropriate.

V1 does not require advanced OHRC-specific preprocessing.

---

### TMC-2

Terrain Mapping Camera-2 (**TMC-2**) imagery may be used in controlled known-overlap V1 experiments where appropriate.

Do not silently introduce V2-style scale or sensor optimization specifically for TMC-2.

---

### IIRS

Canonical V1 does **not** claim native full hyperspectral IIRS support.

IIRS is hyperspectral / imaging-infrared data.

A conventional classical 2D local-feature pipeline should not treat an entire IIRS cube as though it were an ordinary grayscale image.

If a deterministic externally prepared 2D IIRS-derived representation is used in a controlled V1 experiment:

- identify it as derived
- record the representation method
- preserve available provenance
- do not describe it as native full hyperspectral V1 support

Advanced IIRS representation research belongs primarily to later benchmark configurations.

---

### LRO Reference Imagery

Controlled V1 reference imagery may use appropriate LRO/LROC products such as:

- NAC
- WAC

depending on the benchmark pair.

NAC and WAC should not be treated as interchangeable.

The benchmark definition should identify the actual reference product.

---

## 8. Expected Outputs

A V1 registration attempt should conceptually produce a structured scientific result containing relevant information such as:

- result status
- source/reference identity
- candidate-match count
- verified-inlier count
- inlier ratio
- transform
- transform model
- transform direction
- residual information
- spatial-coverage information
- evaluation metrics and units
- runtime where measured
- failure/rejection reason
- references to diagnostic artifacts where applicable

These are conceptual responsibilities.

This document does not define exact field names, classes, JSON schemas, or database models.

---

## 9. Canonical V1 Pipeline

The human-facing V1 flow is:

```text
Source Image
     +
Known Overlapping Reference
             ↓
       Input Validation
             ↓
 Minimal Generic Preprocessing
             ↓
      SIFT Feature Baseline
             ↓
 Classical Descriptor Matching
             ↓
 Candidate Match Filtering
             ↓
            RANSAC
             ↓
      Verified Inliers
             ↓
    Affine / Homography
             ↓
 Coverage / Residual Analysis
             ↓
   Registration / Warp
             ↓
         Evaluation
             ↓
      Accept / Reject
```

RootSIFT may be used only where the canonical V1 configuration explicitly defines it as a separate approved variant.

Detailed stage semantics belong in the [Processing Pipeline](../../.ai/architecture/PIPELINE.md).

---

## 10. Included Processing

### 10.1 Input Validation

V1 should validate enough input properties to prevent meaningless downstream processing.

Potential validation includes:

- readable input
- valid dimensions
- non-empty data
- appropriate 2D representation
- valid mask/no-data information where used
- finite values where required

The purpose is to reject structurally invalid input before feature extraction or geometry.

---

### 10.2 Minimal Generic Preprocessing

V1 preprocessing should remain intentionally limited.

Potential baseline operations, where scientifically justified, may include:

- compatible grayscale preparation
- valid-value handling
- no-data/mask handling
- deterministic normalization
- simple generic contrast preparation

V1 should not become a complex image-enhancement pipeline.

---

### 10.3 Reproducible Preprocessing

Any preprocessing used by canonical V1 should be:

- explicit
- reproducible
- fixed by the benchmark configuration
- applied consistently

Avoid hidden pair-specific decisions such as:

```text
Pair A → one enhancement
Pair B → another enhancement
Pair C → manually tuned preprocessing
```

unless such routing is explicitly part of the defined methodology.

---

### 10.4 SIFT Baseline

SIFT is the primary classical local-feature baseline for V1.

Conceptually it produces:

```text
Source
  ↓
Keypoints + Descriptors

Reference
  ↓
Keypoints + Descriptors
```

The important scientific requirement is preservation of:

- source/reference identity
- coordinate meaning
- keypoint/descriptor alignment

---

### 10.5 RootSIFT

RootSIFT may be evaluated only where it is explicitly defined as a named V1 configuration.

Do not silently change:

```text
SIFT
```

into:

```text
RootSIFT
```

while keeping the same benchmark label.

The canonical configuration must make the distinction clear.

---

### 10.6 Descriptor Matching

V1 uses conventional classical descriptor matching.

Possible concepts include:

- nearest-neighbor matching
- KNN matching
- ratio filtering
- mutual/cross consistency

Exact policy and thresholds belong to benchmark configuration.

This scope document does not invent them.

---

### 10.7 Candidate Matches

Before geometry, matcher output is called:

> **candidate matches**

Candidate matches are not yet:

- verified correspondences
- final inliers
- ground truth

This distinction is fundamental to V1.

---

### 10.8 Candidate Filtering

Where the canonical V1 method defines filtering, it should be:

- deterministic where practical
- configuration-driven
- reproducible
- independent of final ground-truth error

Do not tune filtering separately after viewing each pair's final result.

---

### 10.9 RANSAC / Geometric Verification

RANSAC is a core V1 stage.

Conceptually:

```text
Candidate Matches
        ↓
RANSAC
        ↓
Initial Geometric Model
        +
Verified Inliers
        +
Rejected Outliers
```

RANSAC inliers are correspondences consistent with the selected geometric model.

They are not automatically independent physical ground truth.

---

### 10.10 Affine / Homography

Canonical V1 may use affine transformation and/or homography according to the defined configuration.

An affine model can represent relationships such as:

- translation
- rotation
- scale
- shear

A homography provides a more flexible planar projective relationship.

Neither should be described as a universal solution to complex three-dimensional lunar terrain geometry.

---

### 10.11 Explicit Transform Direction

Transformation direction must be unambiguous.

The preferred conceptual convention is:

```text
source coordinates
        ↓
transformation
        ↓
reference coordinates
```

If the actual implementation uses another convention, documentation and evaluation must reflect the real direction consistently.

---

### 10.12 Verified-Inlier Analysis

After geometric verification, V1 should evaluate the quality of the verified correspondence set.

Useful diagnostics may include:

- verified-inlier count
- inlier ratio
- residuals
- spatial coverage

A matrix returned by RANSAC is not, by itself, sufficient evidence of a trustworthy registration.

---

### 10.13 Spatial Coverage

Spatial distribution matters because a transformation supported only by a tightly clustered group can be poorly constrained over the complete overlap.

Conceptually:

```text
100 clustered inliers
```

may provide weaker whole-region support than:

```text
30 well-distributed inliers
```

V1 should report spatial coverage where the benchmark methodology defines it.

Exact formulas belong in metric documentation.

---

### 10.14 Registration / Warp

Once valid geometry has been established, the source may be transformed into the reference coordinate frame.

Potential outputs include:

- registered raster
- registered preview
- overlay
- transformed coordinates

Important:

> **Warping is not validation.**

Producing a transformed image only shows that a transformation was applied.

Evaluation determines whether the transformation is trustworthy.

---

### 10.15 Evaluation

V1 should report scientifically meaningful evidence about the registration.

Metric families may include:

- correspondence support
- geometry/residual quality
- spatial support
- independent accuracy where available
- runtime where explicitly measured
- success/rejection/failure status

Exact metric mathematics belongs elsewhere.

---

### 10.16 Accept / Reject

V1 should have an explicit scientific outcome.

The result should not be accepted solely because:

- descriptors matched
- RANSAC returned a model
- a transform matrix exists
- a registered image was produced

Acceptance should depend on the benchmark's defined quality evidence.

When evidence is insufficient, rejection is valid.

---

## 11. V1 Evaluation

### 11.1 Correspondence Metrics

Potential V1 correspondence evidence includes:

- candidate-match count
- verified-inlier count
- inlier ratio

These describe support for the estimated geometry.

They do not independently prove final registration accuracy.

---

### 11.2 Spatial Coverage

Where defined, V1 should report correspondence distribution using the project's authoritative coverage metric.

Potential approaches may include:

- grid coverage
- convex-hull coverage

This scope document does not select or define the formula.

---

### 11.3 Residuals

Residual information may be used to evaluate consistency between verified correspondences and the estimated model.

Residuals should retain:

- coordinate context
- units
- evaluated population

---

### 11.4 Fit Points and Check Points

Keep these populations distinct.

```text
Fit Points
    ↓
Estimate Transform
```

```text
Independent Check Points
        ↓
Evaluate Final Transform
```

Where valid independent check points exist, they should be preferred for independent registration-accuracy claims.

---

### 11.5 If Independent Check Points Are Unavailable

V1 may still report:

- candidate count
- verified-inlier count
- inlier ratio
- fit residuals
- spatial coverage
- diagnostic visualizations
- success/rejection under available quality logic

But it must not claim independent absolute registration accuracy that was not measured.

---

### 11.6 Pixel Units

A pixel-space error should identify its image coordinate system.

Examples:

- source-image `px`
- reference-image `px`

Do not report an unlabeled value such as:

```text
RMSE = value
```

without saying what coordinate system and unit the value represents.

---

### 11.7 Ground Error

Ground error in metres should be reported only when the conversion is scientifically meaningful.

That may require valid:

- GSD
- projection
- coordinate interpretation
- geometric context

Do not multiply by a broad approximate instrument scale and present the result as precise ground truth.

---

### 11.8 Sub-Pixel Language

If an error is below one pixel, it may be described as:

> **sub-pixel in the stated image coordinate system**

It must not automatically be described as:

> **sub-metre**

---

### 11.9 Failure Metrics

V1 evaluation should preserve:

- successes
- rejections
- failures

rather than reporting accuracy only for favorable examples without context.

---

## 12. Explicit V1 Inclusions

Canonical V1 generally includes:

- known-overlap local registration
- registration-ready 2D source/reference imagery
- input validation
- minimal generic preprocessing
- SIFT as the primary classical feature baseline
- RootSIFT only where explicitly defined
- conventional descriptor matching
- candidate-match filtering
- RANSAC/geometric verification
- candidate/inlier separation
- rejected-outlier handling
- affine and/or homography according to configuration
- explicit transformation direction
- verified-inlier analysis
- residual analysis
- spatial coverage where defined
- registration/warp
- numerical evaluation
- explicit metric units
- fit/check-point distinction
- explicit success/rejection/failure
- reproducible benchmark configuration
- relevant provenance
- diagnostic visualizations where useful

If the canonical [V1 Scope](../../.ai/context/V1_SCOPE.md) changes, that authoritative definition takes precedence.

---

## 13. Explicit V1 Exclusions

Canonical V1 generally excludes:

- whole-Moon image search
- global visual localization
- reference-database retrieval
- global descriptors
- FAISS
- vector indexing
- Top-K retrieval
- learned global retrieval
- ALIKED
- LightGlue
- LoFTR
- SuperPoint
- SuperGlue
- learned local correspondence as canonical V1
- RIFT/CFOG as canonical V1 unless separately defined by an approved benchmark
- native full IIRS hyperspectral processing
- automatic IIRS band-selection research
- advanced sensor-specific preprocessing
- automatic GSD-aware reference-pyramid search
- advanced scale-routing logic
- advanced multi-scale search
- terrain/DEM-aware registration
- local or piecewise warping
- dense optical flow as canonical V1
- learned confidence estimation
- calibrated confidence
- adaptive matcher selection
- bundle adjustment
- global mosaic generation as a core V1 output
- web map/UI as a V1 requirement
- Mars/Venus/general planetary support

V1 deliberately leaves these capabilities outside the baseline so their future value can be measured.

---

## 14. IIRS Boundary

V1 is not intended to solve the complete hyperspectral correspondence problem.

The canonical baseline works with registration-ready 2D representations.

Therefore:

```text
Native IIRS Cube
```

should not be silently treated as:

```text
Ordinary Grayscale V1 Image
```

If a deterministic externally derived 2D representation is used, the experiment should record how that representation was produced.

Advanced questions such as:

- optimal band selection
- PCA/component selection
- spectral representation design
- sensor-specific hyperspectral preprocessing

belong primarily to later research configurations.

---

## 15. Scale and Sensor-Awareness Boundary

V1 may encounter significant scale differences.

However, canonical V1 does not attempt to solve them using advanced automatic scale-aware routing.

V1 should expose how the classical baseline behaves under those conditions.

More advanced capabilities such as:

- GSD-driven scale selection
- reference pyramids
- automatic comparable-scale search
- sensor-specific processing

belong primarily to Benchmark V2.

This separation allows V2's contribution to be measured.

---

## 16. Retrieval Boundary

Canonical V1 already receives the correct overlapping reference region.

Therefore V1 does not require:

```text
Source
  ↓
Global Descriptor
  ↓
Vector Index / FAISS
  ↓
Top-K Candidates
  ↓
Reference Selection
```

That is a separate retrieval/localization problem.

Keeping it outside V1 allows ChandraMap to test the fundamental local registration problem independently.

---

## 17. Learned Matcher Boundary

Canonical V1 does not use learned local matchers as its primary method.

Methods such as:

- ALIKED + LightGlue
- LoFTR
- other neural correspondence systems

belong to later controlled comparisons.

The relevant future question is:

> Do these methods improve correspondence relative to the classical baseline?

If they are silently added to V1, that comparison becomes impossible.

---

## 18. Refinement Boundary

Advanced sub-pixel tie-point refinement is normally outside canonical V1 when excluded by the authoritative V1 contract.

The reason is methodological:

> refinement should remain measurable as a later improvement rather than being hidden inside the baseline.

Do not add advanced refinement to V1 solely because it lowers residual error.

If the authoritative canonical V1 definition is intentionally revised to include a minimal refinement stage, this human-facing document must be updated accordingly.

---

## 19. Failure and Rejection

Failure is a valid V1 result.

Potential scientific failure/rejection conditions may include:

- invalid input
- insufficient features
- missing descriptors
- insufficient candidate matches
- insufficient geometrically consistent matches
- degenerate geometry
- non-finite transformation
- poor spatial support
- evaluation evidence below configured acceptance criteria

No numerical thresholds are defined here.

---

### 19.1 Expected Scientific Failure

Example:

> insufficient geometrically valid correspondences to estimate trustworthy registration.

This may be correct baseline behavior.

---

### 19.2 Software Error

Example:

> unexpected indexing, shape, or implementation exception.

This is an engineering defect.

Scientific failure and software failure should not be treated as the same thing.

---

### 19.3 Difficult Cases May Fail

A correct V1 implementation may perform poorly or reject cases involving:

- extreme scale gaps
- severe illumination changes
- cross-modality differences
- low-feature terrain
- repetitive lunar terrain
- complex relief

These cases are useful benchmark evidence.

They help identify what later versions need to improve.

---

## 20. Success Definition

A successful V1 registration should conceptually provide:

1. valid source/reference input handling
2. local candidate correspondences
3. geometric verification
4. sufficient verified support according to benchmark policy
5. explicit transformation model
6. explicit transformation direction
7. registration or coordinate mapping
8. measurable quality evidence
9. explicit metric units
10. reproducible configuration
11. no hidden manual correction

This scope does not define a minimum RMSE, minimum inlier count, or other numeric success threshold.

Those values belong in the authoritative benchmark configuration or metric specification when defined.

---

## 21. Reproducibility Requirements

A canonical V1 run should ideally remain traceable to:

- source/reference identity
- benchmark-pair identity
- effective configuration
- preprocessing configuration
- SIFT/RootSIFT choice
- matching/filtering configuration
- geometry configuration
- transform model
- random seed where meaningful
- metric definitions
- code revision where available
- execution environment where relevant

No exact provenance schema is required by this document.

---

### 21.1 Randomness

Where RANSAC or another dependency uses randomness:

- control seeds where practical
- record them where meaningful
- do not overclaim bit-for-bit determinism across every environment

Reproducibility and absolute determinism are related but not identical.

---

### 21.2 No Manual Result Editing

Canonical V1 results should come from reproducible computation.

Do not manually alter:

- candidate matches
- verified inliers
- transformations
- RMSE values
- coverage values
- benchmark summaries

to improve presentation.

---

## 22. Testing Expectations

At a scope level, V1 should have validation protecting high-risk behavior such as:

- input validation
- source/reference direction
- `(x, y)` versus `(row, column)`
- empty features/descriptors
- candidate-match handling
- RANSAC behavior
- degenerate geometry
- inlier-mask alignment
- transform application
- metric behavior
- explicit failure/rejection

Detailed test design belongs in [Testing Rules](../../.ai/development/TESTING_RULES.md).

---

### 22.1 Integration Expectation

A controlled integration path should eventually validate the conceptual flow:

```text
Input
  ↓
Preprocessing
  ↓
SIFT
  ↓
Matching
  ↓
RANSAC
  ↓
Transform
  ↓
Registration
  ↓
Evaluation
  ↓
Structured Result
```

This scope document does not prescribe test filenames or test-framework commands.

---

### 22.2 Real Lunar Validation

Where appropriate verified data exists, V1 should eventually be exercised on real known-overlap lunar image pairs.

This document does not define:

- a required pair count
- product IDs
- benchmark results

Real-data validation must not be claimed unless it was actually performed.

---

## 23. Baseline Stability

Once canonical V1 becomes a validated benchmark baseline, it should remain stable enough for longitudinal comparison.

Stability matters because later versions are measured relative to it.

---

### 23.1 Bug Fix

A bug fix restores the existing intended V1 behavior.

Examples include:

- source/reference order was reversed
- inlier mask became misaligned
- transform was applied in the wrong direction
- metric units were incorrect

A bug fix does not necessarily redefine V1 methodology.

However, affected benchmark results may still require rerunning.

---

### 23.2 Methodology Change

A methodology change alters what V1 actually does.

Examples include:

- replacing SIFT with a learned matcher
- adding automatic reference-pyramid search
- adding global retrieval
- introducing advanced sensor-specific preprocessing
- adding advanced sub-pixel refinement
- changing the transform-selection policy

Such a change may require:

- benchmark revision
- result reruns
- updated documentation
- a comparability note

---

### 23.3 Baseline Freeze

Once the canonical V1 methodology is established and validated, avoid repeatedly changing it after inspecting V2/V3/V4 performance.

Otherwise the baseline becomes a moving target.

---

### 23.4 Do Not Intentionally Weaken V1

Baseline stability does not mean deliberately making V1 poor.

Do not choose obviously unreasonable settings merely to make later versions appear stronger.

V1 should be a competent and defensible classical baseline.

---

### 23.5 Do Not Quietly Strengthen V1

The opposite is also inappropriate.

Do not silently add advanced capabilities to V1 after seeing later-version results merely to make the baseline more competitive.

V1 should be:

> neither artificially weak nor quietly upgraded.

---

## 24. Same-Pair Comparison Principle

When V1 is compared directly with later benchmark configurations, use the same defined pair population where the research question requires a controlled comparison.

Conceptually:

```text
Same Pair Set
+
Same Evaluation Definition
+
Same Units
+
Controlled Configuration
        ↓
Compare V1 vs Later Method
```

If later experiments use a different population, the changed population should be reported.

Do not imply direct equivalence between results from different evaluation populations.

---

## 25. Metric and Configuration Stability

Once a canonical baseline is established, casually changing benchmark definitions can break historical comparability.

Examples include changing:

- RMSE population
- source/reference error domain
- coverage definition
- preprocessing
- SIFT versus RootSIFT choice
- matcher/filter policy
- RANSAC configuration
- transform policy
- success/rejection definition

Methodological changes should be documented rather than hidden behind the same V1 label.

---

## 26. What V1 Should Teach Us

V1 should help answer questions such as:

- How far can a classical sparse-feature baseline go on lunar imagery?
- Which known-overlap pairs are straightforward?
- Which pairs are difficult?
- How much does scale mismatch affect the baseline?
- How much does illumination difference affect correspondence?
- When do repetitive craters generate unreliable candidates?
- When does spatial coverage become insufficient?
- When does a global affine or homography approximation become weak?
- Which cases fail before advanced methods are introduced?

V1 does not need to produce favorable answers.

Its value comes from producing controlled evidence.

---

## 27. Handoff to V2

V1 should reveal limitations that V2 can investigate.

Potential V2 questions include:

- Does sensor-aware preprocessing improve correspondence?
- Does physically meaningful GSD handling help?
- Does a reference pyramid improve large-scale-gap registration?
- Do better structural representations improve difficult sensor pairs?
- Can derived IIRS representations be handled more systematically?

Do not solve those questions invisibly inside V1.

---

## 28. Handoff to V3

V3 may investigate questions such as:

- Do advanced local matchers outperform the classical baseline?
- Do learned correspondence methods help under severe appearance changes?
- When is global retrieval necessary?
- Can global descriptors retrieve the correct reference region?
- How well does Top-K retrieval integrate with local geometric registration?

These capabilities should remain measurable against earlier benchmark configurations.

---

## 29. Handoff to V4

V4 may investigate more advanced limitations such as:

- sub-pixel tie-point refinement
- local/piecewise geometry
- terrain-aware/DEM-informed processing
- advanced failure classification
- uncertainty estimation
- confidence calibration
- adaptive method selection
- scalable retrieval

These should not be pulled backward into V1 without an explicit methodological revision.

---

## 30. V1 Scope Decision Test

Before adding a new capability to canonical V1, ask:

1. **Is it necessary to make the classical baseline scientifically valid?**
2. **Is it already part of the approved canonical V1 definition?**
3. **Is this a bug fix or a methodological enhancement?**
4. **Would adding it make later V2/V3/V4 improvements harder to measure?**
5. **Does it introduce learned models, retrieval, sensor-specific routing, advanced scale handling, refinement, or terrain-aware geometry?**
6. **Could the capability instead be evaluated as a later-version experiment?**
7. **Would the change break comparability with existing V1 benchmark results?**

If the change introduces meaningful new research capability, it probably does not belong in canonical V1.

---

## 31. Definition of Done

At a scope level, canonical V1 is ready to serve as a benchmark baseline when the applicable project implementation satisfies the following:

- [ ] V1 is clearly defined as known-overlap local registration.
- [ ] Input assumptions are explicit.
- [ ] Registration-ready 2D input behavior is defined.
- [ ] Minimal generic preprocessing is defined.
- [ ] SIFT is the primary classical baseline.
- [ ] RootSIFT status is explicit where used.
- [ ] Classical descriptor matching is defined.
- [ ] Candidate filtering is reproducible.
- [ ] Candidate matches and verified inliers remain distinct.
- [ ] RANSAC/geometric verification is included.
- [ ] RANSAC inliers are not treated as independent ground truth.
- [ ] Transform model policy is explicit.
- [ ] Transform direction is explicit.
- [ ] Registration output is defined.
- [ ] Spatial-support expectations are defined where applicable.
- [ ] Evaluation expectations are defined.
- [ ] Metric units are explicit.
- [ ] Fit points and independent check points are distinguished.
- [ ] Failure/rejection is a valid result.
- [ ] Whole-Moon/global retrieval is excluded.
- [ ] FAISS/vector retrieval is excluded.
- [ ] Learned local matching is excluded.
- [ ] Native full IIRS cube processing is excluded.
- [ ] Advanced sensor routing is excluded.
- [ ] Advanced automatic scale search is excluded.
- [ ] Advanced terrain/local geometry is excluded.
- [ ] Advanced refinement remains outside V1 unless the canonical contract explicitly includes it.
- [ ] Reproducibility expectations are defined.
- [ ] Baseline-change policy is defined.
- [ ] No hidden pair-specific tuning is required.
- [ ] No fake thresholds or benchmark results are part of the scope.

This checklist describes the desired baseline contract.

It does not claim current completion.

---

## 32. Status Semantics

Keep V1 status terms separate.

| Status                   | Meaning                                                     |
| ------------------------ | ----------------------------------------------------------- |
| **Specified**            | The V1 methodology and scope are documented                 |
| **Implemented**          | Required V1 code exists                                     |
| **Tested**               | Relevant software tests were actually executed successfully |
| **Domain Validated**     | V1 was actually executed on appropriate real lunar data     |
| **Benchmarked**          | The defined scientific benchmark was actually executed      |
| **Published / Reported** | Results were deliberately recorded or released              |

These states do not imply one another.

For example:

```text
Specified
≠
Implemented
≠
Tested
≠
Benchmarked
```

The existence of this document proves only that the scope is documented.

It does not prove current implementation or benchmark status.

---

## 33. V1 Non-Goal Summary

Benchmark V1 is **not** trying to:

- solve whole-Moon localization
- solve every sensor/modality combination
- maximize benchmark accuracy at any cost
- use the newest available model
- eliminate every scale mismatch
- model full three-dimensional terrain
- provide advanced sensor-specific routing
- provide native full-cube IIRS processing
- use global retrieval
- use FAISS
- use learned local matchers
- provide advanced terrain-aware correction
- produce a global mosaic as its scientific output
- provide a complete web application
- become the final ChandraMap architecture

Its purpose is narrower:

> **Provide a trustworthy, reproducible, classical reference baseline for known-overlap lunar image registration.**

---

## 34. Related Documents

- [Project Overview](./overview.md) — explains what ChandraMap is
- [Problem Statement](./problem-statement.md) — defines the broader lunar correspondence and registration challenge
- [Project Goals](./goals.md) — defines the outcomes ChandraMap aims to achieve
- [Project Non-Goals](./non-goals.md) — defines broad project boundaries
- [Canonical AI V1 Scope](../../.ai/context/V1_SCOPE.md) — authoritative detailed V1 contract
- [V1 Implementation Task](../../.ai/tasks/V1_IMPLEMENTATION.md) — implementation guidance for the defined V1 scope
- [Processing Pipeline](../../.ai/architecture/PIPELINE.md) — detailed scientific stage ordering
- [System Overview](../../.ai/architecture/SYSTEM_OVERVIEW.md) — major architectural boundaries
- [Data Flow](../../.ai/architecture/DATA_FLOW.md) — scientific data, coordinates, transform, and result semantics
- [Module Map](../../.ai/architecture/MODULE_MAP.md) — repository responsibility ownership
- [Dataset Context](../../.ai/context/DATASETS.md) — product, metadata, provenance, and dataset guidance
- [Terminology](../../.ai/context/TERMINOLOGY.md) — canonical ChandraMap vocabulary
- [Testing Rules](../../.ai/development/TESTING_RULES.md) — software and scientific validation expectations
- [Benchmark Rules](../../.ai/development/BENCHMARK_RULES.md) — controlled benchmark governance
- [Documentation Rules](../../.ai/development/DOCUMENTATION_RULES.md) — documentation and status-claim standards

Benchmark V1 should remain intentionally simple enough to expose the difficulty of lunar registration honestly and stable enough that improvements in V2, V3, and V4 can be measured rather than assumed.

<!-- Source specification: :contentReference[oaicite:0]{index=0} -->
