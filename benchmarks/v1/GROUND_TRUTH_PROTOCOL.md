# ChandraMap V1 Ground Truth Protocol

This document defines how ground-truth correspondences and independent evaluation points are created, validated, stored, split, protected from evaluation leakage, and used for the ChandraMap V1 lunar image correspondence and registration benchmark.

The protocol is specifically intended for **known-overlap source/reference image pairs** and quantitative evaluation of correspondence, geometric verification, transformation estimation, and registration accuracy.

---

# 1. Protocol Status

| Field     | Value                                                |
| --------- | ---------------------------------------------------- |
| Project   | ChandraMap                                           |
| Benchmark | V1                                                   |
| Document  | `GROUND_TRUTH_PROTOCOL.md`                           |
| Scope     | Ground truth and independent registration evaluation |
| Status    | Not specified                                        |
| Version   | Not specified                                        |

No implementation status, benchmark result, checkpoint count, accuracy value, or acceptance threshold is asserted unless it is defined by the project data or an authoritative benchmark source.

---

# 2. Purpose

Ground truth provides the reference correspondence information required to determine whether a registration system is actually correct.

A visually convincing overlay is not sufficient evidence of accurate lunar image registration. A flexible transformation can produce an attractive result even when the underlying correspondences are weak, incorrect, or spatially clustered.

The V1 ground-truth protocol therefore establishes a controlled separation between:

```text
GROUND TRUTH
      │
      ├──► CONTROL / FITTING POINTS
      │          │
      │          ▼
      │    TRANSFORMATION FIT
      │
      └──► INDEPENDENT CHECK POINTS
                 │
                 ▼
          FINAL EVALUATION
```

The primary purposes are to:

- establish trustworthy source/reference correspondences;
- define how correspondence coordinates are represented;
- distinguish fitting points from evaluation points;
- prevent evaluation leakage;
- support reproducible transformation estimation;
- support independent check-point evaluation;
- measure registration error in source-image pixels;
- permit ground/geospatial error only when the necessary information exists;
- evaluate spatial distribution of correspondences;
- identify ambiguous or unreliable ground-truth points;
- preserve ground-truth provenance and validation information.

The project feedback specifically recommends using challenge ground truth when available and otherwise maintaining independently checked tie points as check points rather than evaluating a transformation on the same points used to fit it.

---

# 3. Scope

The V1 ground-truth protocol primarily supports:

- known-overlap lunar source/reference image pairs;
- ground-truth point correspondences;
- transformation estimation;
- geometric verification;
- independent check-point evaluation;
- registration RMSE;
- spatial correspondence coverage;
- source-image pixel error;
- ground error when geospatial information makes it meaningful;
- failure and ambiguity tracking.

V1 ground truth is **not automatically a protocol for**:

- whole-Moon global retrieval;
- global lunar geolocation;
- a complete lunar GIS control network;
- a complete hyperspectral ground-truth dataset;
- all possible Chandrayaan-2 sensor products;
- global cartographic accuracy assessment.

If another benchmark defines these tasks, its own ground-truth protocol must be used.

---

# 4. Ground Truth Terminology

The following terms have distinct meanings and MUST NOT be used interchangeably.

| Term                            | Definition                                                                                                             |
| ------------------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| **Ground truth**                | Reference correspondence information treated as authoritative for a benchmark case.                                    |
| **Ground-truth correspondence** | A validated relationship between a location in the source image and the corresponding location in the reference image. |
| **Control point**               | A ground-truth correspondence permitted to contribute to transformation estimation.                                    |
| **Check point**                 | An independently held-out ground-truth correspondence reserved for evaluating the fitted transformation.               |
| **Candidate match**             | A correspondence proposed by the evaluated matching system before geometric verification.                              |
| **Verified inlier**             | A candidate match accepted as geometrically consistent with the estimated model.                                       |
| **Prediction**                  | A correspondence or transformed position produced by the evaluated method.                                             |
| **Residual**                    | Difference between a predicted position and its reference position.                                                    |
| **Registration error**          | Error measured against reference/check-point positions.                                                                |
| **Control network**             | A collection of validated correspondences used to describe or constrain geometric relationships.                       |
| **Ambiguous point**             | A point for which the reference correspondence cannot be established with sufficient confidence or uniqueness.         |
| **Rejected point**              | A proposed ground-truth point that fails validation and is excluded from benchmark evaluation.                         |
| **Provenance**                  | Information describing where a ground-truth point came from and how it was established or validated.                   |

### Critical distinction

Candidate matches are **not** ground truth.

RANSAC inliers are **not automatically** independent check points.

A point being accepted by the evaluated model does not make it an authoritative reference point.

---

# 5. Ground-Truth Hierarchy

Ground truth should be selected according to the strongest authoritative source available for the benchmark case.

Preferred hierarchy:

1. **Official/challenge-provided ground truth**
2. **Authoritative reference-product correspondence or control information**
3. **Independently generated and validated tie points**
4. **Synthetic ground truth for controlled synthetic experiments**

The hierarchy does not mean that every source above is available for every benchmark case.

The actual source used for each case must be recorded.

## 5.1 Official or challenge ground truth

If the challenge or benchmark provides an official ground-truth protocol or correspondence dataset, that protocol takes precedence over a project-defined alternative.

The project must not silently replace official ground truth with manually generated points.

## 5.2 Authoritative reference information

Where an authoritative reference product provides sufficient correspondence or geometric information, it may be used according to the product's documented coordinate and geometry conventions.

## 5.3 Independently validated tie points

When official ground truth is unavailable, independently established tie points may be used.

Their provenance and validation procedure must be recorded.

## 5.4 Synthetic ground truth

Synthetic data may be used to test controlled algorithm behavior, such as known transformations or controlled perturbations.

Synthetic ground truth MUST NOT automatically be presented as equivalent to real lunar ground truth.

A successful synthetic experiment demonstrates behavior under the synthetic conditions; it does not establish equivalent performance on real lunar imagery.

---

# 6. Ground-Truth Source Rules

Potential project data sources include:

- SIH/challenge-provided data;
- Chandrayaan-2 products;
- LRO reference products;
- PRADAN data;
- LROC data;
- other project-approved lunar reference products.

The project materials identify Chandrayaan-2 OHRC, TMC-2, and IIRS as important source imagery and LRO products as potential reference imagery. They also identify PRADAN and LROC-related resources as relevant data sources.

However:

> **The authoritative V1 ground-truth dataset is not specified in the current project data available for this protocol.**

Therefore this document does not invent:

- a dataset filename;
- a dataset size;
- a checkpoint count;
- a coordinate list;
- a ground-truth archive;
- a benchmark threshold.

When the repository establishes the authoritative source, that source must become the source of truth for V1.

---

# 7. Ground-Truth Coordinate Systems

Ground truth may involve several coordinate spaces. They must remain explicitly separated.

## 7.1 Pixel coordinates

Pixel coordinates describe positions within an image.

The benchmark implementation MUST document:

- image coordinate origin;
- x-axis direction;
- y-axis direction;
- pixel-center convention;
- coordinate ordering;
- coordinate units;
- image width;
- image height.

The current project materials do not establish whether the implementation uses zero-based or one-based coordinates.

Therefore:

> **The V1 implementation MUST declare one coordinate convention and use it consistently.**

The convention must not be inferred from undocumented assumptions.

---

## 7.2 Projected/geospatial coordinates

Map-projected coordinates may be used when the source/reference product provides an appropriate projection.

The projection definition must be preserved with the benchmark case.

A projected coordinate must not be treated as a simple image pixel coordinate.

---

## 7.3 Geographic coordinates

Latitude/longitude may be recorded when valid product metadata or a documented projection transformation supports them.

Latitude/longitude must not be invented from image coordinates.

---

## 7.4 Ground coordinates

Ground coordinates may be used when an appropriate reference surface, projection, GSD, or other required geospatial information exists.

Ground coordinates and image coordinates must remain separate.

---

# 8. Pixel Coordinate Convention

The following is a normative requirement rather than a claim about the current implementation.

The V1 implementation MUST explicitly declare:

1. coordinate origin;
2. axis orientation;
3. pixel-center interpretation;
4. image coordinate ordering;
5. coordinate units;
6. image dimensions;
7. any transformation between pixel and projected/geographic coordinates.

For example, an implementation must not silently mix:

```text
(x, y)
```

with:

```text
(row, column)
```

without documenting the conversion.

Likewise, it must not silently mix pixel-center and pixel-corner coordinates.

If an existing ChandraMap schema defines the convention, that schema is authoritative.

---

# 9. Point Correspondence Model

A ground-truth correspondence represents the same physical lunar feature or location observed in two images.

Conceptually:

```text
SOURCE IMAGE                         REFERENCE IMAGE

   source point  ─────────────────►  reference point

       (x_s, y_s)                         (x_r, y_r)
```

The correspondence must represent a physically meaningful relationship rather than merely two visually similar image locations.

A correspondence should be associated with:

- source image identity;
- reference image identity;
- source coordinate;
- reference coordinate;
- provenance;
- validation state;
- ambiguity information where applicable.

The exact repository serialization format is **not specified** in the available project data.

Therefore this protocol defines the required logical information but does not invent an implementation-specific file schema.

---

# 10. Ground-Truth Point Types

Ground-truth correspondences are divided according to their role in evaluation.

## 10.1 Control points

Control points may be used to estimate the transformation.

They can contribute to:

- RANSAC model estimation;
- transformation fitting;
- refined transformation estimation;
- other explicitly documented fitting procedures.

Control points are **not independent evaluation points** after being used for fitting.

---

## 10.2 Check points

Check points are held out from transformation fitting.

They are used to evaluate the resulting transformation.

The defining property of a check point is:

> The evaluated transformation was not fitted using that point.

Check points should remain independent throughout the final fitting process.

---

## 10.3 Ambiguous points

A point should be marked ambiguous when correspondence cannot be established reliably or uniquely.

Examples include situations where:

- multiple nearby structures are visually indistinguishable;
- terrain appearance prevents reliable localization;
- the reference image does not contain enough information;
- the feature is obscured or altered by imaging conditions;
- the exact correspondence cannot be independently established.

Ambiguous points must not silently become benchmark check points.

---

## 10.4 Rejected points

A proposed ground-truth point may be rejected when validation determines that it is incorrect or insufficiently reliable.

Rejected points should remain traceable where the implementation supports provenance.

They must not simply disappear without explanation from a maintained ground-truth dataset.

---

# 11. Control Points vs Check Points

The distinction is fundamental.

```text
                 GROUND TRUTH
                      │
          ┌───────────┴───────────┐
          │                       │
          ▼                       ▼
   CONTROL POINTS            CHECK POINTS
          │                       │
          ▼                       │
   Fit transformation             │
          │                       │
          └───────────┐           │
                      ▼           ▼
                 FINAL MODEL → EVALUATION
```

A control point may be correct ground truth while still being unsuitable for final independent accuracy evaluation because it influenced the model.

A check point must remain unused for fitting.

---

# 12. Independence Rule

The central scientific rule of this protocol is:

> **A point used to estimate a transformation MUST NOT be treated as an independent point for final accuracy evaluation.**

If a transformation is estimated from a set of points and RMSE is calculated on those same points, the reported error may primarily describe the fit to those points rather than independent registration performance.

The preferred V1 evaluation is:

```text
GROUND TRUTH
      │
      ├───────────────┐
      │               │
      ▼               ▼
CONTROL POINTS    CHECK POINTS
      │               │
      ▼               │
TRANSFORMATION       │
FITTING              │
      │               │
      ▼               │
FINAL MODEL ──────────┘
      │
      ▼
CHECK-POINT EVALUATION
      │
      ▼
REGISTRATION ERROR
```

The project feedback explicitly identifies this separation as necessary for defensible evaluation.

---

# 13. Ground-Truth Creation

Ground-truth creation must begin from the strongest available reference information.

The process should be:

```text
Reference Source
      ↓
Identify Common Lunar Region
      ↓
Establish Candidate Correspondence
      ↓
Validate Correspondence
      ↓
Record Coordinate Information
      ↓
Assign Ground-Truth Status
      ↓
Split Into Control / Check Roles
      ↓
Freeze Evaluation Ground Truth
```

Ground truth must not be created by simply accepting the output of the system being evaluated.

For example:

```text
ChandraMap prediction
       ↓
Called "ground truth"
```

is invalid evaluation design.

---

# 14. Ground-Truth Validation

Ground-truth points should be validated before they become authoritative benchmark points.

Validation should consider:

- correspondence correctness;
- coordinate correctness;
- image bounds;
- feature identity;
- local terrain consistency;
- ambiguity;
- image quality;
- projection/geometry consistency;
- provenance.

Where independent validation is possible, it should be recorded.

The validation process must not use the evaluated model's prediction as the sole justification for declaring a point correct.

---

# 15. Point Quality and Ambiguity

Not all visually identifiable points have equal correspondence quality.

A benchmark point should represent a location that can be consistently identified in both images.

Useful structures may include:

- crater rims;
- ridge lines;
- stable terrain intersections;
- distinct terrain boundaries;
- other persistent structural features.

The project feedback emphasizes using stable terrain structure rather than relying only on raw brightness, particularly under different Sun angles.

---

# 16. Spatial Distribution of Ground Truth

Ground-truth points should be distributed across the valid overlap rather than concentrated around a single feature.

This is important because a transformation can fit a small local region while remaining inaccurate elsewhere.

Conceptually:

```text
BAD DISTRIBUTION

+-----------------------+
|                       |
|      • • • •          |
|      • • • •          |
|                       |
|                       |
+-----------------------+
```

versus:

```text
BETTER DISTRIBUTION

+-----------------------+
| •                 •   |
|                       |
|        •              |
|                       |
|   •               •   |
+-----------------------+
```

The actual acceptable distribution must follow the benchmark's defined evaluation procedure.

The project feedback specifically identifies **grid coverage** and **convex-hull coverage** as useful ways to quantify spatial distribution.

---

# 17. Spatial Coverage Evaluation

Spatial coverage evaluates whether reliable correspondences span the relevant overlap.

Possible measures include:

- grid-cell coverage;
- convex-hull coverage;
- other explicitly documented spatial-distribution metrics.

The metric definition must be fixed before comparing benchmark runs.

Coverage is complementary to point count.

A large number of points clustered around one crater does not necessarily provide better registration support than fewer points distributed across the overlap.

---

# 18. Ground-Truth Splitting

When a benchmark case contains enough validated ground-truth correspondences for separate fitting and evaluation roles, the roles must be explicitly assigned.

The split must ensure:

```text
Control Set
    ≠
Check Set
```

The exact number of points in either set must not be invented or assumed by this protocol.

If an official challenge specifies the split, the challenge rule takes precedence.

If the benchmark implementation specifies a deterministic split, that implementation must document it.

---

# 19. Spatial Leakage Prevention

A simple random point split may not always provide meaningful independence.

When points are spatially close, nearby points may provide highly redundant information.

Therefore, when the benchmark's design requires stronger spatial independence, the split procedure should consider spatial distribution rather than only point order.

Any spatial split strategy must be documented and reproducible.

This protocol does not prescribe an undocumented numerical separation distance.

---

# 20. Ground-Truth Freeze

Once a benchmark test set has been finalized, its evaluation ground truth should be treated as immutable for that benchmark version.

Changes to:

- point coordinates;
- point identities;
- point roles;
- validation status;
- source/reference associations;

must result in a documented ground-truth revision.

A benchmark result must identify the ground-truth version used.

---

# 21. Evaluation Leakage

Ground-truth leakage occurs when information reserved for evaluation influences the evaluated system.

Examples include:

### Invalid

```text
Check points
   ↓
Transformation fitting
   ↓
Evaluate on same check points
```

### Invalid

```text
Ground-truth coordinates
   ↓
Tune matcher parameters
   ↓
Evaluate on those same coordinates
```

### Invalid

```text
Test correspondences
   ↓
Repeatedly modify algorithm
   ↓
Report final performance on same test set
```

The precise experimental protocol must determine which data are available during development and which remain held out.

---

# 22. Ground Truth and RANSAC

The expected V1 geometry flow is:

```text
LOCAL MATCHES
      ↓
RANSAC
      ↓
VERIFIED INLIERS
      ↓
SUB-PIXEL TIE POINTS
      ↓
FINAL MODEL
      ↓
REGISTERED IMAGE
```

RANSAC is a method for evaluating candidate correspondences against a geometric model.

It does not create authoritative ground truth.

A candidate match becoming a RANSAC inlier means that the match is geometrically consistent with the estimated model. It does not independently prove that the correspondence is correct.

The project feedback explicitly distinguishes candidate matches from verified inliers and recommends geometric verification before refinement.

---

# 23. Ground Truth and Sub-Pixel Refinement

Sub-pixel refinement must occur after reliable geometric inliers have been established.

The recommended sequence is:

```text
Candidate Matches
       ↓
Initial Geometric Model
       ↓
Verified Inliers
       ↓
Sub-Pixel Refinement
       ↓
Refined Tie Points
       ↓
Refit Final Transformation
```

The project feedback explicitly recommends refining verified control/tie points locally and then estimating the final transformation again from the refined points.

Sub-pixel refinement does not remove the need for independent check points.

The final refined transformation must still be evaluated on points that were not used to fit that final transformation.

---

# 24. Transformation Fitting

The transformation model must be documented for each benchmark run.

For a local, already map-projected image pair, an affine transformation or homography may be an appropriate initial model.

However, the Moon is not a flat surface, and raw imagery can contain sensor and viewing geometry effects.

Therefore:

> The protocol does not assume that one global transformation is valid for every benchmark case.

The project feedback recommends using the simplest transformation that adequately explains the observed residuals and inspecting residual behavior across the image.

---

# 25. Residual Analysis

Residuals should be examined spatially rather than reduced immediately to a single number.

Conceptually:

```text
Reference Point
      ↑
      │ residual vector
      │
Predicted Point
```

Residual patterns may indicate:

- inadequate transformation;
- viewpoint effects;
- terrain relief;
- projection differences;
- sensor geometry;
- local deformation;
- poor correspondences.

If residuals vary systematically across the image, the benchmark should investigate whether a local or piecewise model or available sensor/DEM geometry is more appropriate.

A flexible transformation must not be used simply to make the overlay look better.

---

# 26. Independent Check-Point Evaluation

The final transformation is evaluated by applying it to the source-side coordinates of the independent check points and comparing the resulting positions against their reference-side ground-truth coordinates.

Conceptually:

```text
Source Check Point
        │
        ▼
Final Transformation
        │
        ▼
Predicted Reference Position
        │
        ▼
Compare With
Reference Check Point
        │
        ▼
Residual / Error
```

Only check points excluded from final transformation fitting qualify as independent evaluation points.

---

# 27. Registration Error

For a check point, the positional residual can be represented conceptually as:

```text
error = predicted_position - reference_position
```

For two-dimensional image coordinates, the Euclidean positional error can be represented as:

```text
e_i = sqrt(
    (x_pred_i - x_ref_i)^2 +
    (y_pred_i - y_ref_i)^2
)
```

The exact implementation must preserve the declared coordinate convention.

The benchmark must distinguish:

- x-coordinate error;
- y-coordinate error;
- two-dimensional positional error;
- aggregate RMSE.

---

# 28. Check-Point RMSE

For independent check points, positional RMSE may be represented as:

```text
RMSE = sqrt(
    Σ(e_i²) / N
)
```

where:

- `e_i` is the positional error for check point `i`;
- `N` is the number of valid evaluated check points.

The benchmark must report the coordinate space in which the RMSE was calculated.

The project feedback specifically recommends **check-point RMSE in source-image pixels** as the primary registration accuracy measurement.

---

# 29. Source-Image Pixel Error

Source-image pixel error is the primary representation for the V1 sub-pixel registration claim.

The benchmark should report the registration error in source-image pixels before converting it into metres.

This is important because the same numerical pixel error does not represent the same physical ground distance for sensors with different GSDs.

For example, the project feedback explicitly notes that the same fractional-pixel error can correspond to different ground distances for TMC-2 and IIRS.

---

# 30. Ground Error

Ground error in metres may be reported only when the required geospatial information makes the conversion meaningful.

Relevant requirements may include:

- valid source GSD;
- valid reference GSD where relevant;
- valid projection information;
- valid geometric relationship;
- appropriate reference/check-point information.

The protocol must not convert pixel error to metres merely by multiplying by an assumed GSD when the geometry does not support that interpretation.

The project feedback explicitly limits metre-based reporting to cases where GSD, projection, and reference truth make the conversion meaningful.

---

# 31. Pixel Error vs Ground Error

These measurements answer different questions.

| Measurement      | Meaning                                                                            |
| ---------------- | ---------------------------------------------------------------------------------- |
| Pixel error      | How far the predicted registration is from the reference in image-coordinate units |
| Ground error     | Physical/geospatial displacement expressed in ground units when valid              |
| Check-point RMSE | Aggregate independent registration error                                           |
| Spatial coverage | Whether reliable correspondences are distributed across the overlap                |

A benchmark report must never present pixel error and ground error as interchangeable metrics.

---

# 32. GSD Handling

Ground sampling distance must be taken from the authoritative product metadata available for the benchmark case.

The project feedback specifically recommends using the challenge/product metadata as the final authority for pixel scale rather than assuming one universal value for an instrument.

The protocol must preserve the actual GSD associated with each evaluated product when it is available.

---

# 33. Projection and Orthorectification

If the source or reference imagery is already map-projected or orthorectified, that information should be used rather than forcing the computer-vision pipeline to solve geometry that the mapping process has already established.

The benchmark must record relevant projection information when available.

Raw imagery and map-projected imagery must not be silently treated as geometrically equivalent.

---

# 34. Ground Truth for Different Sensors

Ground truth must account for differences between sensors.

The project materials emphasize that OHRC, TMC-2, and IIRS are not equivalent image sources and should not automatically be processed through an identical path.

## 34.1 OHRC

OHRC is high-detail visible panchromatic imagery.

Ground-truth correspondences may therefore include fine terrain structures where the source/reference products genuinely resolve them.

## 34.2 TMC-2

TMC-2 provides coarser panchromatic terrain imagery.

Ground-truth points should be chosen at spatial scales actually supported by the source imagery.

## 34.3 IIRS

IIRS is hyperspectral/infrared data rather than an ordinary single-band camera image.

A benchmark case must identify the 2D representation used for registration.

Potential representations may include:

- selected band;
- PCA/composite representation;
- structural representation.

The protocol must not assume that all spectral information can be directly represented as one ordinary camera image.

---

# 35. Scale and Ground Truth

Ground-truth correspondences must remain physically meaningful under large GSD differences.

Upsampling a coarse image does not create missing spatial information.

Therefore:

```text
Coarse Source
      +
High-Resolution Reference
      ↓
Comparable Effective Scale
      ↓
Correspondence
      ↓
Fine Refinement Only Where Supported
```

The project feedback specifically warns against treating upsampled IIRS imagery as though it contains newly recovered fine terrain detail.

---

# 36. Illumination and Ground Truth

Sun-angle differences can change:

- shadow locations;
- apparent crater structure;
- local brightness;
- terrain contrast.

Brightness normalization alone does not establish correspondence.

Ground-truth points should therefore be based on persistent terrain structure where possible.

The project materials specifically identify crater rims, ridge lines, edges, gradients, and relative geometry as useful structural information under illumination differences.

---

# 37. Ground Truth Validation Checklist

Before a point is accepted as benchmark ground truth, the following should be checked where applicable:

- [ ] Source image identity is known.
- [ ] Reference image identity is known.
- [ ] Source coordinate is valid.
- [ ] Reference coordinate is valid.
- [ ] Coordinates are inside the relevant image bounds.
- [ ] Coordinate convention is known.
- [ ] The correspondence represents the same lunar location.
- [ ] The correspondence is not known to be ambiguous.
- [ ] Projection information is preserved when relevant.
- [ ] GSD information is preserved when available.
- [ ] Provenance is recorded.
- [ ] Validation status is recorded.
- [ ] Control/check role is explicitly assigned.
- [ ] The point is not accidentally reused across incompatible roles.

---

# 38. Check-Point Validation Checklist

Before using a point for final evaluation:

- [ ] It is part of the authoritative ground-truth set.
- [ ] It is independent of the final transformation fit.
- [ ] It was not used to select the final transformation model based on its result.
- [ ] Its source/reference coordinates are validated.
- [ ] It is not marked ambiguous or rejected.
- [ ] Its coordinate convention matches the evaluated transformation.
- [ ] Its evaluation status is recorded.
- [ ] Any missing geospatial metadata is explicitly identified.

---

# 39. Handling Ambiguous Correspondences

Ambiguous points should not be forced into a binary correct/incorrect benchmark label when the reference itself is uncertain.

An ambiguous point should be:

1. identified;
2. documented;
3. excluded from the authoritative evaluation set unless the benchmark explicitly defines another treatment;
4. retained in provenance records where supported.

The exclusion must not be used selectively to remove difficult points after seeing algorithm results.

---

# 40. Handling Occluded or Unobservable Points

A correspondence should not be treated as valid merely because the physical location is known if the source/reference imagery does not contain enough observable information to identify it reliably.

Points affected by:

- missing image coverage;
- unusable image quality;
- severe observational differences;
- invalid coordinate regions;

should be handled according to the benchmark's documented validity state.

No benchmark threshold is defined here for determining when image quality becomes unacceptable.

---

# 41. Handling Duplicate Points

Duplicate or near-duplicate ground-truth points can overrepresent one local feature.

Ground-truth validation should therefore detect duplicate correspondence records where possible.

If duplicates exist, the benchmark must define whether they are:

- merged;
- retained for a documented reason;
- or rejected.

The chosen treatment must remain consistent across compared methods.

---

# 42. Handling Incorrect Ground Truth

Ground truth is not automatically correct merely because it exists in a file.

If a ground-truth error is discovered:

1. do not silently modify the published benchmark;
2. document the issue;
3. determine whether the point is actually incorrect;
4. update the ground-truth version when justified;
5. record the change;
6. identify affected benchmark results.

A corrected ground-truth release must be distinguishable from the previous release.

---

# 43. Ground-Truth Provenance

Every authoritative ground-truth record should be traceable to its source.

At the logical level, provenance should identify, where available:

- source image/product;
- reference image/product;
- origin of correspondence;
- validation process;
- coordinate system;
- creation/review information;
- ground-truth version;
- status.

The exact repository schema is not specified by the current project data and must not be invented here.

---

# 44. Ground-Truth Storage

The benchmark repository should store ground-truth information in the project's established dataset/schema format.

The available project materials do **not** specify an authoritative V1 ground-truth filename or serialization schema.

Therefore this document does not invent one.

The implementation should preserve, at minimum, the logical distinction between:

```text
Benchmark Case
    │
    ├── Source Image
    ├── Reference Image
    ├── Ground-Truth Correspondences
    │
    ├── Control / Fitting Role
    └── Check / Evaluation Role
```

If the repository later defines a machine-readable schema, that schema becomes authoritative for implementation.

---

# 45. Ground-Truth Versioning

Ground truth must be versioned independently from algorithm results when the repository supports benchmark versioning.

A ground-truth revision should occur when there is a material change to:

- correspondence coordinates;
- point validity;
- point roles;
- source/reference association;
- coordinate convention;
- validation status;
- provenance.

Benchmark results must identify the ground-truth version used.

---

# 46. Ground-Truth Immutability During Evaluation

Once a benchmark evaluation begins, the evaluation ground truth must not be modified in response to observed model behavior.

Invalid workflow:

```text
Run model
   ↓
Observe difficult point
   ↓
Remove point
   ↓
Run model again
   ↓
Report improved result
```

Valid workflow:

```text
Freeze ground truth
       ↓
Run benchmark
       ↓
Record failures
       ↓
Investigate independently
       ↓
Revise future benchmark version if correction is justified
```

---

# 47. Benchmark Development vs Final Evaluation

Ground truth may be used differently during development and final evaluation.

During development, control data may support:

- implementation debugging;
- transformation testing;
- visualization;
- algorithm development.

The final evaluation set must remain protected from tuning that would invalidate its independence.

The project must clearly distinguish development data from held-out evaluation data whenever such a distinction exists.

---

# 48. Ground Truth and Model Selection

Ground-truth check points must not become a hidden model-selection mechanism.

Repeatedly comparing many algorithms against the same final evaluation points and selecting the configuration that performs best on those points can gradually leak evaluation information into development.

Therefore, the benchmark process should distinguish:

```text
Development / Experimentation
            ↓
Model or configuration selection
            ↓
Final held-out evaluation
```

The exact dataset split is not specified by the current project data and must follow the repository's eventual benchmark implementation.

---

# 49. Transformation Model Selection

The transformation model should be selected based on the documented benchmark procedure rather than simply choosing the model that produces the lowest evaluation error on the held-out check points.

The project feedback recommends starting with simple models such as affine transformation or homography for appropriate local map-projected cases and inspecting residuals before introducing more flexible geometry.

If residuals show systematic spatial variation, the benchmark should investigate the underlying geometry.

---

# 50. Flexible Warping

Flexible or local warping must not be used to conceal poor correspondences.

The correct order is:

```text
Reliable Correspondences
        ↓
Geometric Verification
        ↓
Spatially Distributed Control Points
        ↓
Appropriate Transformation
        ↓
Optional Local / Flexible Refinement
```

not:

```text
Weak Correspondences
        ↓
Flexible Warp
        ↓
Visually Attractive Overlay
```

The project feedback explicitly warns that flexible warping can make an overlay look good even when correspondences are weak.

---

# 51. Ground Truth Quality and Benchmark Validity

Benchmark validity depends on the quality of its reference information.

A benchmark result can be misleading if:

- ground-truth coordinates are incorrect;
- control and check points are mixed;
- evaluation points influence transformation fitting;
- ambiguous points are treated as exact;
- coordinate conventions are inconsistent;
- projections are misunderstood;
- GSD is incorrectly interpreted;
- points are spatially clustered;
- ground truth changes during experimentation.

Therefore:

> **Ground-truth quality is part of benchmark quality.**

---

# 52. Ground-Truth Failure Modes

Common failure modes include:

| Failure                         | Consequence                            |
| ------------------------------- | -------------------------------------- |
| Wrong correspondence            | Incorrect reference error              |
| Coordinate convention mismatch  | Systematic positional error            |
| Pixel-center mismatch           | Systematic sub-pixel offset            |
| Control/check leakage           | Optimistically biased evaluation       |
| Spatial clustering              | Poor assessment of global registration |
| Ambiguous point                 | Unreliable accuracy measurement        |
| Incorrect projection            | Invalid geospatial comparison          |
| Incorrect GSD                   | Invalid metre conversion               |
| Duplicate points                | Overweighted local structures          |
| Untracked ground-truth revision | Non-reproducible results               |

---

# 53. Recommended Ground-Truth Review

Ground-truth review should occur before final benchmark release.

The review should inspect:

### Correspondence correctness

Does the source point correspond to the same lunar location in the reference?

### Coordinate correctness

Are coordinates consistent with the declared convention?

### Spatial distribution

Are evaluation points distributed across the relevant overlap?

### Independence

Are check points excluded from transformation fitting?

### Provenance

Can the origin of the point be determined?

### Ambiguity

Are uncertain points explicitly marked rather than silently accepted?

### Geometry

Is the chosen ground-truth representation consistent with the product geometry?

---

# 54. Evaluation Flow

The complete V1 ground-truth-aware evaluation flow is:

```text
SOURCE IMAGE + REFERENCE IMAGE
             │
             ▼
      KNOWN OVERLAP CASE
             │
             ▼
       GROUND TRUTH
             │
       ┌─────┴─────┐
       │           │
       ▼           ▼
 CONTROL SET    CHECK SET
       │           │
       ▼           │
 LOCAL MATCHES    │
       │           │
       ▼           │
     RANSAC        │
       │           │
       ▼           │
 VERIFIED INLIERS  │
       │           │
       ▼           │
 SUB-PIXEL REFINE  │
       │           │
       ▼           │
  FINAL MODEL      │
       │           │
       └─────┬─────┘
             ▼
     CHECK-POINT EVALUATION
             │
       ┌─────┴──────────┐
       ▼                ▼
 SOURCE-PIXEL       GROUND ERROR
     ERROR          IF MEANINGFUL
       │
       ▼
      RMSE
```

---

# 55. Ground Truth and Benchmark Metrics

Ground truth directly supports the following V1 measurements:

| Metric                   | Ground-truth dependency                         |
| ------------------------ | ----------------------------------------------- |
| Check-point RMSE         | Requires independent reference positions        |
| Source-image pixel error | Requires reference check-point coordinates      |
| Ground error             | Requires valid geospatial/reference information |
| Spatial coverage         | Requires valid correspondence locations         |
| Transformation residuals | Requires reference correspondence information   |
| Registration success     | Requires defined reference/evaluation criteria  |

Other benchmark metrics, such as runtime, do not inherently require ground truth but must be reported separately.

---

# 56. What Ground Truth Does Not Measure

Ground truth alone does not establish:

- runtime;
- memory usage;
- GPU usage;
- retrieval speed;
- visual quality of a final mosaic;
- software reliability;
- deployment performance.

These require additional benchmark measurements.

---

# 57. Mosaic Evaluation

A final mosaic may be a useful downstream demonstration.

However:

> **A visually good mosaic must not replace quantitative correspondence and registration evaluation.**

The core benchmark remains:

```text
Correspondence
      +
Geometric Verification
      +
Independent Registration Evaluation
```

The project feedback explicitly identifies the mosaic as a downstream demonstration rather than the primary scientific deliverable.

---

# 58. Reproducibility Requirements

A reproducible ground-truth evaluation should preserve, where available:

- benchmark case identity;
- source image identity;
- reference image identity;
- ground-truth version;
- coordinate convention;
- projection information;
- GSD;
- control/check roles;
- transformation configuration;
- evaluation procedure;
- refinement procedure;
- evaluation outputs.

The exact repository commands and paths are not specified in the available project data and are therefore not invented here.

---

# 59. Ground-Truth Audit Trail

For every benchmark revision, the project should be able to answer:

```text
Where did this point come from?
        ↓
How was it validated?
        ↓
Which coordinate system does it use?
        ↓
Was it a control or check point?
        ↓
Which benchmark version contains it?
        ↓
Which results used that version?
```

If these questions cannot be answered, the ground-truth record is not sufficiently traceable for a rigorous benchmark.

---

# 60. Prohibited Ground-Truth Practices

The following practices are prohibited for the V1 benchmark:

- inventing coordinates;
- inventing checkpoint counts;
- inventing benchmark thresholds;
- inventing accuracy;
- inventing RMSE;
- inventing dataset sizes;
- inventing dataset filenames;
- treating candidate matches as ground truth;
- treating RANSAC inliers as independent check points;
- fitting and evaluating on exactly the same points;
- silently changing ground truth after seeing results;
- removing difficult points solely because they reduce performance;
- converting pixel error to metres without valid geospatial justification;
- claiming synthetic performance as equivalent to real lunar performance;
- using flexible warping to conceal poor correspondences;
- reporting visual overlay quality as the sole evidence of registration accuracy.

---

# 61. Ground-Truth Release Checklist

Before a V1 ground-truth release is considered usable, verify:

- [ ] Authoritative source is identified or explicitly marked as unspecified.
- [ ] Source/reference image identities are known.
- [ ] Coordinate convention is declared.
- [ ] Projection information is preserved where applicable.
- [ ] GSD information is preserved where available.
- [ ] Correspondence provenance is recorded.
- [ ] Ground-truth points have validation status.
- [ ] Ambiguous points are identified.
- [ ] Rejected points are handled consistently.
- [ ] Control points are distinguished from check points.
- [ ] Check points are protected from fitting.
- [ ] Spatial distribution is evaluated.
- [ ] Ground-truth version is identifiable.
- [ ] Changes are auditable.
- [ ] No benchmark results have been used to silently modify the evaluation set.

---

# 62. Final Evaluation Checklist

Before reporting a V1 registration result, verify:

- [ ] The benchmark case is identified.
- [ ] The source/reference products are identified.
- [ ] The ground-truth version is identified.
- [ ] The coordinate convention is known.
- [ ] Control points are distinct from check points.
- [ ] The final transformation was not fitted using check points.
- [ ] Sub-pixel refinement, if used, occurred after reliable inlier identification.
- [ ] The final transformation was refit after refinement when applicable.
- [ ] Check-point error is reported in source-image pixels.
- [ ] Ground error is reported only when meaningful.
- [ ] Spatial coverage is reported where applicable.
- [ ] Failures are retained and documented.
- [ ] No unsupported accuracy or confidence claim is presented.

---

# 63. V1 Ground-Truth Decision Rules

The following rules summarize the protocol.

### Rule 1 — Use authoritative ground truth first

If an official challenge ground truth exists, follow it.

### Rule 2 — Do not invent missing ground truth

If the authoritative source is not specified, record that fact rather than creating unsupported benchmark data.

### Rule 3 — Separate fitting from evaluation

Control points may fit the transformation.

Check points evaluate the transformation.

### Rule 4 — Candidate matches are predictions

They become verified inliers only after geometric verification.

### Rule 5 — Inliers are not automatically independent

A RANSAC inlier used in model fitting cannot simultaneously be treated as an independent check point.

### Rule 6 — Refine after verification

Sub-pixel refinement follows reliable geometric verification.

### Rule 7 — Refit after refinement

If refined tie points are used, refit the final transformation from those refined points.

### Rule 8 — Evaluate independently

Final registration accuracy must be measured using points not used to fit the final transformation.

### Rule 9 — Report source-pixel error first

Source-image pixel error is the primary V1 registration accuracy representation.

### Rule 10 — Convert to metres only when justified

Ground error requires valid GSD, projection, geometry, and reference information.

### Rule 11 — Check spatial distribution

Correct correspondences should not be judged only by count; their spatial distribution matters.

### Rule 12 — Do not let warping hide bad matches

Geometric flexibility follows correspondence quality, not the other way around.

---

# 64. V1 Ground-Truth Flow Summary

```text
                 ┌─────────────────────────┐
                 │ Source / Reference Pair │
                 └────────────┬────────────┘
                              │
                              ▼
                 ┌─────────────────────────┐
                 │   Authoritative Ground  │
                 │         Truth            │
                 └────────────┬────────────┘
                              │
                  ┌───────────┴───────────┐
                  │                       │
                  ▼                       ▼
          ┌───────────────┐       ┌───────────────┐
          │ Control Points│       │  Check Points │
          └───────┬───────┘       └───────┬───────┘
                  │                       │
                  ▼                       │
          ┌───────────────┐               │
          │ Local Matching │               │
          └───────┬───────┘               │
                  ▼                       │
          ┌───────────────┐               │
          │    RANSAC     │               │
          └───────┬───────┘               │
                  ▼                       │
          ┌───────────────┐               │
          │ Verified      │               │
          │ Inliers       │               │
          └───────┬───────┘               │
                  ▼                       │
          ┌───────────────┐               │
          │ Sub-Pixel     │               │
          │ Refinement    │               │
          └───────┬───────┘               │
                  ▼                       │
          ┌───────────────┐               │
          │ Final         │               │
          │ Transformation│               │
          └───────┬───────┘               │
                  │                       │
                  └───────────┬───────────┘
                              ▼
                    ┌───────────────────┐
                    │ Independent       │
                    │ Check-Point       │
                    │ Evaluation        │
                    └─────────┬─────────┘
                              ▼
                 ┌─────────────────────────┐
                 │ Source-Pixel Error/RMSE │
                 └────────────┬────────────┘
                              │
                              ▼
                 ┌─────────────────────────┐
                 │ Ground Error When Valid │
                 └─────────────────────────┘
```

---

# 65. Relationship to the V1 Benchmark

This document defines only the **ground-truth and independent evaluation protocol**.

The broader benchmark should separately define:

- benchmark cases;
- algorithms;
- baselines;
- stress tests;
- retrieval;
- local matching;
- runtime;
- reporting;
- result aggregation.

The conceptual separation is:

```text
benchmarks/README.md
        │
        ▼
Overall Benchmark Suite
        │
        ▼
benchmarks/v1/README.md
        │
        ├──► V1 Overview
        │
        ├──► BENCHMARK_SPEC.md
        │       └── Complete V1 benchmark definition
        │
        └──► GROUND_TRUTH_PROTOCOL.md
                └── Ground truth + independent evaluation rules
```

---

# 66. Source-Derived Scientific Principles

The V1 protocol incorporates the following principles from the supplied ChandraMap technical feedback:

- challenge ground truth should be used when available;
- independent check points should otherwise be maintained;
- points used to fit a transformation should not be reused as the sole basis for accuracy reporting;
- sub-pixel refinement should follow reliable RANSAC inliers;
- the final transformation should be refit after refinement;
- source-image pixels should be the primary accuracy unit;
- metre-based ground error requires meaningful GSD/projection/reference information;
- spatial distribution matters in addition to match count;
- visual registration alone is insufficient;
- flexible warping should not conceal poor correspondences.

The project feedback also emphasizes that the benchmark should demonstrate measurable behavior on real lunar image pairs rather than relying on decorative confidence scores or unsupported performance claims.

---

# 67. Protocol Principle

The V1 ground-truth protocol follows one central rule:

> **The benchmark must measure how well ChandraMap registers lunar imagery against trusted reference information, not how well ChandraMap reproduces the points it was allowed to use while fitting its own transformation.**

The essential separation is:

```text
GROUND TRUTH
     │
     ├──► CONTROL → FIT
     │
     └──► CHECK   → EVALUATE
```

This separation is the foundation of trustworthy V1 registration evaluation.
