# V1 Benchmark Specification

This document defines the Version 1 benchmark protocol for evaluating known-overlap lunar image correspondence and registration in ChandraMap.

V1 is the foundational measurement benchmark for the project. Its purpose is to establish a reproducible baseline for taking a known overlapping lunar source/reference image pair through local correspondence, geometric verification, transformation estimation, registration, and quantitative evaluation on independent check points.

The benchmark prioritizes **measurable correspondence and registration accuracy** over visual mosaic quality. A mosaic or joined lunar visualization is considered a downstream product, not the primary benchmark objective. This follows the SIH 26166 framing and the project-specific technical feedback.

---

# 1. Specification Status

| Field                | Value                                                     |
| -------------------- | --------------------------------------------------------- |
| Benchmark            | ChandraMap V1                                             |
| Specification        | `BENCHMARK_SPEC.md`                                       |
| Scope                | Known-overlap lunar image correspondence and registration |
| Primary task         | Local correspondence and geometric registration           |
| Primary baseline     | SIFT + descriptor matching + RANSAC                       |
| Evaluation principle | Independent check-point evaluation                        |
| Status               | Not specified                                             |
| Last updated         | Not specified                                             |

No benchmark result, threshold, dataset size, or acceptance value is implied by this document unless explicitly defined by the repository or benchmark dataset.

---

# 2. Scope

## 2.1 Included in V1

V1 evaluates the following workflow:

1. Known overlapping source/reference image pair.
2. Input validation.
3. Sensor and metadata identification.
4. Reproducible preprocessing.
5. Local feature extraction.
6. Descriptor matching.
7. Candidate correspondence generation.
8. Geometric verification.
9. Transformation estimation.
10. Optional controlled sub-pixel refinement where supported by the V1 implementation.
11. Final transformation refitting.
12. Image registration.
13. Independent check-point evaluation.
14. Spatial distribution of verified correspondences.
15. Runtime and failure recording.
16. Reproducible result reporting.

The intended first milestone is explicitly a real end-to-end result:

```text
Known source/reference pair
        ↓
SIFT
        ↓
Candidate matches
        ↓
RANSAC
        ↓
Verified inliers
        ↓
Transformation
        ↓
Registered overlay
        ↓
Independent check-point error
```

This matches the project feedback recommendation to establish one measurable known pair before expanding the system.

## 2.2 Explicitly outside the core V1 task

The following are not automatically part of V1:

- Whole-Moon image retrieval.
- Large-scale global retrieval.
- A complete multi-sensor benchmark.
- A full learned-matcher comparison.
- A complete hyperspectral registration benchmark.
- Full-lunar mosaic generation.
- Production-scale search infrastructure.
- Unmeasured claims of illumination invariance.
- Unmeasured claims of sensor invariance.
- Unmeasured claims of sub-pixel ground accuracy.
- Arbitrary local or nonlinear warping used to improve visual appearance without independent validation.

These may be introduced in later benchmark versions or as explicitly controlled V1 experiments only when the repository defines them as part of V1.

---

# 3. Scientific Question

The primary V1 scientific question is:

> **Can ChandraMap take a known overlapping lunar source/reference image pair, produce reliable local correspondences, reject incorrect matches through geometric verification, estimate a valid transformation, produce a registered result, and demonstrate quantitative accuracy on independent check points?**

V1 is the first measurable research milestone because it isolates the fundamental correspondence and registration problem before introducing global retrieval, larger datasets, or multiple advanced matching methods.

A successful V1 experiment should therefore produce evidence for:

- whether local correspondences can be established;
- whether candidate correspondences survive geometric verification;
- whether the verified points are spatially distributed;
- whether a transformation explains the observed geometry;
- whether the registered result is quantitatively accurate on points not used for fitting;
- whether the pipeline succeeds or fails under the tested conditions.

The benchmark must not equate visual alignment with scientific correctness. A visually convincing overlay can still contain incorrect correspondences or systematic geometric residuals.

---

# 4. Benchmark Inputs

## 4.1 Source Image

Each benchmark pair MUST identify the source image and, where available, provide:

| Property                  | Requirement                     |
| ------------------------- | ------------------------------- |
| Image data                | Required                        |
| Sensor/product identifier | Required                        |
| Image dimensions          | Required                        |
| Pixel scale / GSD         | Conditional on product metadata |
| Projection/map metadata   | Conditional                     |
| Geographic footprint      | Conditional                     |
| Illumination metadata     | Conditional                     |
| Viewing geometry          | Conditional                     |
| Product processing level  | Conditional                     |

The benchmark must preserve the distinction between the physical characteristics of different sensors.

The project materials identify Chandrayaan-2 OHRC, TMC-2, and IIRS as materially different imaging sources and recommend sensor-specific preprocessing rather than forcing all three through an identical path.

## 4.2 Reference Image

The reference image MUST provide the same metadata categories where applicable:

- image data;
- sensor/product identifier;
- dimensions;
- GSD/pixel scale;
- projection;
- footprint;
- illumination information;
- viewing geometry;
- processing information.

LRO NAC is identified in the project materials as a principal high-resolution reference source, while LRO WAC is identified as an additional lunar-scale/illumination data source.

## 4.3 Image Pair

Each benchmark pair MUST have:

- a unique pair identifier;
- a source image;
- a reference image;
- known or documented overlap;
- source/reference sensor identifiers;
- available ground-truth information;
- independent check-point information where required by the evaluation protocol.

The benchmark must not invent overlap, ground truth, or check points from an image merely because the images appear visually similar.

---

# 5. Data Contract

The following defines the logical V1 benchmark contract. It is a benchmark specification contract, not a claim that the current repository already implements every field.

| Field                    |                                          Required | Description                                    |
| ------------------------ | ------------------------------------------------: | ---------------------------------------------- |
| `pair_id`                |                                               Yes | Unique identifier for the benchmark pair       |
| `source_image`           |                                               Yes | Reference to the source image                  |
| `reference_image`        |                                               Yes | Reference to the reference image               |
| `source_sensor`          |                                               Yes | Source sensor/product identifier               |
| `reference_sensor`       |                                               Yes | Reference sensor/product identifier            |
| `source_dimensions`      |                                               Yes | Source image dimensions                        |
| `reference_dimensions`   |                                               Yes | Reference image dimensions                     |
| `source_gsd`             |                                       Conditional | Source ground sampling distance/pixel scale    |
| `reference_gsd`          |                                       Conditional | Reference ground sampling distance/pixel scale |
| `source_projection`      |                                       Conditional | Source projection/map information              |
| `reference_projection`   |                                       Conditional | Reference projection/map information           |
| `source_footprint`       |                                       Conditional | Source geographic footprint                    |
| `reference_footprint`    |                                       Conditional | Reference geographic footprint                 |
| `illumination_metadata`  |                                       Conditional | Sun/illumination information when available    |
| `viewing_metadata`       |                                       Conditional | Viewing geometry when available                |
| `ground_truth`           |                                       Conditional | Ground-truth correspondence information        |
| `fit_points`             |                                       Conditional | Points permitted for transformation estimation |
| `check_points`           | Required for quantitative registration evaluation | Independent points reserved for evaluation     |
| `preprocessing_metadata` |                                               Yes | Reproducible record of preprocessing           |
| `method_metadata`        |                                               Yes | Algorithm and configuration information        |
| `result_metadata`        |                                               Yes | Benchmark result and failure information       |

## 5.1 Ground Truth

Ground truth MAY consist of:

- externally supplied correspondence points;
- independently verified tie points;
- geometrically derived reference correspondences;
- another documented source of positional truth.

The source of ground truth MUST be recorded.

A benchmark MUST NOT silently treat model-generated matches as independent ground truth.

## 5.2 Fit Points and Check Points

The benchmark MUST distinguish between:

- points used to estimate a transformation; and
- points used to evaluate the final transformation.

The same points MUST NOT be used as both the fitting set and the independent evaluation set.

This separation is necessary because evaluating a transformation on the same points used to estimate it can make the apparent registration error artificially optimistic.

---

# 6. Dataset and Pair Selection Rules

A V1 benchmark pair qualifies when the pair satisfies the applicable conditions below.

## 6.1 Required Conditions

A pair SHOULD have:

- known overlap;
- sufficient common lunar terrain;
- usable image quality;
- identifiable source/reference products;
- enough information to establish correspondence;
- independent check-point information for quantitative registration evaluation.

## 6.2 Pair Exclusion

A pair SHOULD be excluded from quantitative evaluation when:

- overlap cannot be established;
- the source/reference relationship is unknown;
- required evaluation points cannot be independently established;
- metadata required for a claimed metric is unavailable;
- image corruption prevents reproducible processing;
- the pair has leaked into tuning or method-development decisions in a way that invalidates the intended test.

## 6.3 No Invented Thresholds

V1 does not define numerical image-quality, overlap, keypoint-count, or accuracy thresholds unless they are explicitly established elsewhere in the repository or benchmark dataset.

A method MUST NOT be declared successful merely because it exceeds an undocumented threshold.

---

# 7. Sensor and Scale Rules

## 7.1 Sensor Awareness

OHRC, TMC-2, and IIRS MUST be treated as different sensor/data types.

The project feedback identifies approximately:

| Sensor | Characteristics relevant to V1                                            |
| ------ | ------------------------------------------------------------------------- |
| OHRC   | High-detail visible panchromatic imagery                                  |
| TMC-2  | Panchromatic terrain imagery at substantially coarser scale               |
| IIRS   | Imaging IR hyperspectral data with substantially lower spatial resolution |

The exact product metadata is authoritative for the benchmark instance.

Approximate mission-level values MUST NOT replace product-specific metadata.

The project feedback specifically notes that OHRC documentation can indicate different pixel scales depending on document/product and recommends using the challenge product metadata as the final authority.

## 7.2 Physical Scale

The benchmark MUST distinguish:

- source-image pixel error;
- reference-image pixel error;
- physical ground error.

These quantities are not interchangeable.

For example, the same numerical pixel error at different sensor GSDs represents different physical distances.

## 7.3 Reference Downsampling

When the reference image has substantially finer spatial resolution than the source, the benchmark MAY use:

- reference-image downsampling;
- a multi-resolution reference pyramid;
- scale-specific representations.

The selected representation MUST be recorded.

The purpose is to compare information at physically meaningful scales rather than simply matching different pixel counts.

## 7.4 Upsampling

Upsampling MUST NOT be interpreted as recovering missing physical spatial information.

In particular:

> Increasing the number of pixels in an IIRS image does not create spatial detail that was not captured by the sensor.

The benchmark MUST NOT claim fine ground accuracy merely because a lower-resolution image was resized to a larger pixel array.

## 7.5 Fine Registration Limits

Fine registration MUST remain consistent with the spatial information actually present in the source sensor.

A source sensor with coarse GSD MUST NOT be represented as containing fine-scale terrain information merely because the image has been interpolated.

---

# 8. Preprocessing Contract

Preprocessing is part of the benchmark configuration and MUST be reproducible.

Every preprocessing operation that can affect benchmark output MUST be recorded.

## 8.1 Permitted Categories

Depending on the sensor and available product:

- standard/calibrated product preparation;
- grayscale conversion where appropriate;
- light denoising;
- local contrast normalization;
- edge representation;
- gradient representation;
- structural representation;
- scale normalization;
- reference-image pyramid generation;
- sensor-specific 2D representation.

## 8.2 Reproducibility Requirement

A result MUST record enough information to identify:

- whether preprocessing was applied;
- preprocessing operations;
- relevant parameters;
- sensor-specific route;
- effective matching scale;
- representation used for local matching.

A preprocessing operation MUST NOT be described as beneficial unless the benchmark contains a controlled measurement supporting that conclusion.

## 8.3 OHRC / TMC-2

For OHRC and TMC-2, the benchmark MAY evaluate:

- calibrated or standard products;
- grayscale/intensity representations;
- edge/gradient representations;
- structural representations;
- comparable reference scales.

Denoising MUST avoid unnecessarily removing crater rims, ridges, or other structures useful for correspondence.

## 8.4 IIRS

IIRS MUST NOT automatically be treated as an ordinary single-band camera image.

If IIRS is evaluated, the benchmark MUST explicitly record the selected representation.

Possible representations include:

- selected spectral band;
- PCA/composite representation;
- structural representation;
- another explicitly documented 2D representation.

The benchmark MUST NOT assume that a representation is registration-suitable without measurement.

The project feedback specifically recommends beginning with a simple 2D representation and determining which representation preserves stable terrain structure rather than immediately building a complex hyperspectral matcher.

---

# 9. V1 Baseline Algorithm

The V1 baseline follows the simplest measurable correspondence-to-registration pipeline.

```text
Input pair
    ↓
Input validation
    ↓
Sensor / metadata handling
    ↓
Preprocessing
    ↓
SIFT keypoints + descriptors
    ↓
Descriptor matching
    ↓
Match filtering
    ↓
Candidate matches
    ↓
RANSAC / geometric verification
    ↓
Verified inliers
    ↓
Initial transformation
    ↓
Optional sub-pixel refinement
    ↓
Final transformation refit
    ↓
Registration
    ↓
Independent check-point evaluation
    ↓
Benchmark metrics
```

## 9.1 Input Validation

The benchmark MUST verify that:

- both images are readable;
- source and reference identities are known;
- dimensions are available;
- required metadata is present or explicitly marked unavailable;
- the pair is eligible for V1 evaluation.

## 9.2 SIFT Feature Extraction

The baseline uses SIFT as the initial local feature method.

SIFT is used because the project feedback identifies it as a simple, explainable baseline suitable for establishing a measurable number before evaluating more advanced methods.

The exact SIFT implementation and parameters MUST be recorded by the implementation when applicable.

## 9.3 Descriptor Matching

SIFT descriptors are matched between source and reference representations.

The implementation MUST record the matching strategy used.

Where filtering is applied, the filtering method and parameters MUST be recorded.

Candidate matches remain **candidate correspondences** at this stage.

They MUST NOT be reported as geometrically verified correspondences.

## 9.4 Geometric Verification

Candidate correspondences MUST be subjected to geometric verification.

RANSAC or another explicitly documented robust geometric estimation method may be used according to the V1 implementation.

The benchmark MUST distinguish:

```text
Candidate matches
        ↓
Geometric verification
        ↓
Verified inliers
```

A descriptor confidence score alone is not sufficient evidence that a correspondence is geometrically correct.

## 9.5 Initial Transformation

The initial transformation MUST be estimated from geometrically verified correspondences.

For local, already map-projected image pairs, affine or homography models may be appropriate first models.

The benchmark MUST record which model was used.

The specification does not assume that one global transform is universally valid for lunar imagery.

## 9.6 Residual Inspection

Residuals SHOULD be inspected spatially across the image.

If residual vectors show systematic spatial variation, the result SHOULD be flagged for investigation rather than hidden through an increasingly flexible warp.

Possible causes include:

- lunar relief;
- sensor viewing geometry;
- projection differences;
- insufficient transformation model;
- local terrain effects.

The project feedback explicitly recommends inspecting residual vectors and considering local/piecewise warping, sensor geometry, or DEM information when systematic residuals remain.

## 9.7 Sub-Pixel Refinement

Sub-pixel refinement is a controlled refinement stage, not a substitute for reliable matching.

The conceptual order is:

```text
LOCAL MATCHES
      ↓
RANSAC / INITIAL MODEL
      ↓
VERIFIED INLIERS
      ↓
SUB-PIXEL TIE-POINT REFINEMENT
      ↓
FINAL TRANSFORM REFIT
      ↓
REGISTERED IMAGE
```

If sub-pixel refinement is enabled, the benchmark MUST record:

- that refinement was enabled;
- the refinement method;
- the points refined;
- the final transform refit after refinement.

Sub-pixel refinement MUST be applied to verified correspondences rather than to arbitrary candidate matches.

If the V1 implementation does not contain a sub-pixel refinement stage, the benchmark MUST record that it was not used rather than implying that the baseline achieved sub-pixel accuracy.

## 9.8 Final Transformation

The final transformation MUST be refitted using the final verified/refined control points.

The final model MUST be the model used for registration and independent evaluation.

## 9.9 Registration

The source image is registered into the reference coordinate system using the final transformation.

The benchmark MUST retain an interpretable registered output.

The registered image is evidence of the transformation result, but visual quality alone is not a benchmark metric.

---

# 10. Correspondence Classification

V1 MUST maintain distinct correspondence states.

| State             | Meaning                                                             |
| ----------------- | ------------------------------------------------------------------- |
| Candidate match   | Proposed correspondence before geometric verification               |
| Verified inlier   | Candidate correspondence accepted by geometric verification         |
| Refined tie point | Verified point whose location was refined, if refinement is enabled |
| Check point       | Independent point reserved for final evaluation                     |

These states MUST NOT be conflated.

In particular:

> More candidate matches do not necessarily indicate better registration.

Incorrect or spatially clustered matches can increase match count while reducing registration reliability.

---

# 11. Transformation and Geometry Rules

## 11.1 Model Selection

The benchmark SHOULD use the simplest geometric model that adequately explains the observed residuals.

Potential models include:

- affine transformation;
- homography;
- explicitly documented local/piecewise model where justified.

The benchmark MUST record the selected model.

## 11.2 Global Model Limitation

A single global transform MUST NOT be assumed to be universally valid for lunar imagery.

The Moon is a three-dimensional body, and image geometry may contain effects that cannot be fully represented by a simple planar transform.

## 11.3 Map-Projected Products

If source and reference products are already map-projected or orthorectified, the benchmark SHOULD use that information rather than forcing the computer-vision pipeline to rediscover geometry already represented by the products.

## 11.4 Flexible Warping

Flexible or local warping MUST NOT be used solely to make an overlay visually attractive.

A local warp SHOULD only be introduced after:

1. correspondence quality has been established;
2. control points are accurate;
3. control points are spatially distributed;
4. residual structure indicates that a global model is insufficient;
5. the resulting model is evaluated on independent check points.

---

# 12. Ground Truth and Evaluation Points

## 12.1 Evaluation Principle

Final registration accuracy MUST be evaluated on points that were not used to fit the final transformation.

Preferred evaluation order:

```text
Ground truth / independent correspondences
                    ↓
             Split points
              ↙          ↘
       Fitting points   Check points
              ↓             ↓
        Final transform   Evaluation
              ↘             ↙
             V1 metrics
```

## 12.2 Fitting Points

Fitting points are used to estimate the transformation.

They may originate from:

- verified feature matches;
- refined verified tie points;
- documented control points.

## 12.3 Check Points

Check points MUST remain independent of transformation fitting.

They are used to measure final registration accuracy.

The benchmark MUST report the number and source of check points where such information is available.

## 12.4 Ground-Truth Provenance

The benchmark MUST record how the check points were established.

The benchmark MUST distinguish:

- externally supplied ground truth;
- independently verified tie points;
- derived ground truth;
- unavailable ground truth.

---

# 13. V1 Metrics

V1 metrics are grouped by evaluation stage.

| Stage               | Metric               | Purpose                                                             |
| ------------------- | -------------------- | ------------------------------------------------------------------- |
| Local matching      | Inlier count         | Number of candidate matches surviving geometric verification        |
| Local matching      | Inlier ratio         | Fraction of candidate matches retained as verified inliers          |
| Distribution        | Grid coverage        | Spatial distribution of verified points                             |
| Distribution        | Convex-hull coverage | Area coverage represented by verified points                        |
| Registration        | Check-point RMSE     | Independent registration error                                      |
| Geospatial accuracy | Ground error         | Physical error when GSD/projection/truth make conversion meaningful |
| System              | Runtime              | Processing time                                                     |
| System              | Failure rate         | Frequency of unsuccessful benchmark runs                            |

Global retrieval metrics such as Recall@1 or Recall@5 are **not required for core V1** because V1 is a known-overlap benchmark. They become applicable only if a retrieval component is explicitly included in a controlled V1 experiment.

The project feedback identifies Recall@1/Recall@5 as retrieval metrics, while check-point RMSE, inlier statistics, spatial coverage, runtime, and failure rate address the correspondence and registration stages.

---

# 14. Metric Definitions

## 14.1 Inlier Count

Let:

- \(N_c\) = number of candidate correspondences;
- \(N_i\) = number of verified inliers.

Then:

$$
\text{Inlier Count} = N_i
$$

The count MUST be reported together with the candidate count.

A high inlier count alone MUST NOT be interpreted as proof of accurate registration.

---

## 14.2 Inlier Ratio

$$
\text{Inlier Ratio}
=
\frac{N_i}{N_c}
$$

where:

- \(N_i\) = verified inliers;
- \(N_c\) = candidate correspondences.

If \(N_c = 0\), the metric is undefined and MUST be recorded as unavailable rather than assigned an arbitrary value.

---

## 14.3 Check-Point Reprojection Error

For a check point \(p_j\), let:

- \(g_j\) = ground-truth/reference position;
- \(\hat{g}\_j\) = position predicted by the final transformation.

The point error is:

$$
e_j = \left\|g_j - \hat{g}_j\right\|_2
$$

The benchmark MUST report the coordinate system and unit.

---

## 14.4 Check-Point RMSE

For \(N\) independent check points:

$$
RMSE =
\sqrt{
\frac{1}{N}
\sum_{j=1}^{N}
\left\|g_j-\hat{g}_j\right\|_2^2
}
$$

The primary reported registration error SHOULD be in **source-image pixels** when the benchmark is evaluating source-image sub-pixel registration.

The benchmark MUST NOT report an apparently precise physical-ground error unless the GSD, projection, and reference geometry support that conversion.

---

## 14.5 Ground Error

Ground error in metres MAY be reported when:

- source GSD is known;
- reference geometry is known;
- the coordinate conversion is meaningful;
- the relevant projection/reference information is available.

The conversion method MUST be documented.

A source-pixel error MUST NOT automatically be interpreted as an equivalent ground-distance error across different sensors.

The project feedback explicitly notes that the same pixel error on TMC-2 and IIRS represents different physical ground errors.

---

## 14.6 Spatial Coverage

Verified correspondences MUST be evaluated for spatial distribution.

Acceptable V1 representations include:

- grid coverage;
- convex-hull coverage.

If grid coverage is used, the benchmark MUST record the grid definition.

If convex-hull coverage is used, the benchmark MUST record the reference area against which the hull is evaluated.

The metric exists to distinguish:

```text
Many matches clustered in one small region
```

from:

```text
Verified matches distributed across the overlap
```

The project feedback explicitly identifies well-distributed matches as a required evaluation concern rather than merely a visual property.

---

## 14.7 Runtime

Runtime MUST be reported with enough context to interpret it.

Where available, record:

- hardware;
- software environment;
- CPU/GPU availability;
- processing stage;
- total runtime;
- retrieval time if retrieval is enabled;
- local matching time;
- registration/evaluation time.

Runtime comparisons MUST use equivalent hardware and comparable processing configurations.

---

## 14.8 Failure Rate

A run is considered a failure when the benchmark cannot produce a valid result required by the evaluation protocol.

Failure categories SHOULD be recorded when identifiable, such as:

- input failure;
- preprocessing failure;
- insufficient candidate matches;
- geometric verification failure;
- transformation estimation failure;
- registration failure;
- missing evaluation points;
- invalid output;
- runtime/resource failure.

A failed run MUST NOT be silently removed from aggregate reporting.

---

# 15. Stress-Test Categories

Stress categories MAY be introduced as controlled V1 experiments where the required data exists.

The project materials identify the following categories:

| Stress case         | Purpose                                              |
| ------------------- | ---------------------------------------------------- |
| Easy pair           | Establish end-to-end functionality                   |
| Sun-angle stress    | Measure sensitivity to illumination/shadow changes   |
| Scale stress        | Measure effects of large GSD differences             |
| Modality stress     | Evaluate sensor-specific representations             |
| Geometry stress     | Examine stronger viewpoint/relief effects            |
| Low-feature terrain | Expose false matches and weak correspondence regions |

The project feedback recommends these categories as a small controlled stress matrix rather than attempting to solve every possible condition at once.

## 15.1 Easy Pair

The pair should represent:

- known overlap;
- usable terrain structure;
- relatively compatible illumination;
- manageable scale difference.

Purpose:

> Establish that the complete V1 pipeline can operate end-to-end.

## 15.2 Sun-Angle Stress

Use the same or comparable lunar region under substantially different illumination conditions where available.

The benchmark MUST report performance changes rather than claiming illumination invariance.

Brightness or contrast normalization MUST NOT be interpreted as automatically removing illumination geometry differences.

## 15.3 Scale Stress

Use pairs with meaningful differences in effective ground scale.

The benchmark SHOULD use multi-scale representations or reference downsampling rather than simply upsampling the lower-resolution source.

## 15.4 Modality Stress

If IIRS is evaluated, the benchmark MUST record the 2D representation used to compare it with the reference.

The result MUST be reported separately from visible-camera results when the sensor characteristics make direct aggregation misleading.

## 15.5 Geometry Stress

Evaluate cases where:

- terrain relief is significant;
- viewpoint differences are stronger;
- a simple planar transformation may be insufficient.

Residual structure MUST be inspected.

## 15.6 Low-Feature Terrain

Use smooth or repetitive terrain where false correspondences are more likely.

The purpose is to expose failure modes rather than conceal them through filtering.

---

# 16. Benchmark Variants

V1 should preserve a simple baseline and introduce improvements as controlled variants.

A recommended comparison structure is:

| Variant                    | Purpose                                               |
| -------------------------- | ----------------------------------------------------- |
| Baseline SIFT              | Establish the foundational local-matching baseline    |
| Stronger matcher           | Measure the effect of replacing the baseline matcher  |
| Sensor-aware + multi-scale | Measure the contribution of sensor and scale handling |
| Optional refinement        | Measure the contribution of sub-pixel refinement      |

The same benchmark pairs MUST be used when comparing variants.

The same ground truth and check points MUST be used.

The same metric definitions MUST be used.

Preprocessing differences MUST be explicitly recorded.

The project feedback recommends comparing the same test pairs through a baseline, a stronger matcher, and the full sensor-aware/multi-scale pipeline so that improvements can be attributed to actual system changes.

---

# 17. Fair Comparison Rules

For two methods to be considered directly comparable, they MUST use:

- the same image pairs;
- the same source/reference definitions;
- the same ground truth;
- the same independent check points;
- the same metric definitions;
- the same evaluation protocol;
- equivalent failure accounting.

The following MUST be disclosed:

- preprocessing differences;
- sensor-specific routes;
- scale representations;
- transformation models;
- matching parameters;
- refinement stages;
- hardware differences;
- excluded or failed cases.

## 17.1 Test-Set Isolation

Benchmark test data MUST NOT be used to tune the method being evaluated.

Any development/tuning data MUST be distinguishable from final evaluation data.

## 17.2 No Selective Reporting

A method MUST NOT be evaluated only on successful pairs.

Failures MUST remain part of the benchmark record.

## 17.3 No Unmeasured Improvement Claims

A change MUST NOT be described as an improvement unless the benchmark provides measurements supporting that statement.

Decorative confidence values, ratings, or placeholder percentages MUST NOT be presented as benchmark results.

The project feedback specifically recommends removing unmeasured values such as placeholder confidence percentages and replacing them with actual RMSE, inlier ratio, coverage, and runtime measurements.

---

# 18. Result Record

Every completed V1 benchmark run SHOULD produce a structured result containing at least:

| Result field           | Description                                         |
| ---------------------- | --------------------------------------------------- |
| `pair_id`              | Evaluated benchmark pair                            |
| `method`               | Matching/registration method                        |
| `source_sensor`        | Source sensor/product                               |
| `reference_sensor`     | Reference sensor/product                            |
| `preprocessing`        | Applied preprocessing                               |
| `effective_scale`      | Matching scale/representation                       |
| `candidate_count`      | Number of candidate matches                         |
| `inlier_count`         | Number of verified inliers                          |
| `inlier_ratio`         | Verified inliers / candidates                       |
| `coverage`             | Spatial coverage metric                             |
| `transform_model`      | Transformation model                                |
| `refinement_used`      | Whether sub-pixel refinement was used               |
| `check_point_count`    | Independent evaluation point count                  |
| `check_point_rmse_px`  | RMSE in source-image pixels                         |
| `ground_error_m`       | Ground error where meaningful                       |
| `runtime`              | Runtime information                                 |
| `status`               | Success/failure                                     |
| `failure_reason`       | Failure classification when applicable              |
| `environment`          | Relevant execution environment                      |
| `method_configuration` | Parameters/configuration needed for reproducibility |

No field should be populated with fabricated values.

---

# 19. Required Benchmark Artifacts

A valid V1 run SHOULD preserve interpretable artifacts sufficient to diagnose the result.

Recommended artifacts include:

1. Source/reference image pair.
2. Candidate-match visualization.
3. Rejected-match visualization where available.
4. Verified-inlier visualization.
5. Final registered overlay.
6. Transformation information.
7. Residual information.
8. Spatial coverage information.
9. Independent check-point error.
10. Benchmark metrics.
11. Runtime information.
12. Failure information where applicable.
13. Configuration/preprocessing metadata.

The project build guidance specifically identifies match plots, rejected outliers, registered overlays, inlier statistics, and check-point error as useful evidence for the first end-to-end milestone.

---

# 20. Reproducibility Requirements

A V1 benchmark result is reproducible only when another engineer can reconstruct the evaluation conditions without relying on undocumented assumptions.

The benchmark record MUST identify, where applicable:

- dataset/pair identity;
- source/reference products;
- image dimensions;
- GSD;
- preprocessing;
- matching method;
- matching parameters;
- geometric model;
- RANSAC configuration;
- refinement configuration;
- evaluation points;
- metric definitions;
- execution environment;
- random seeds when applicable;
- software/library versions when available.

## 20.1 Determinism

If the implementation contains stochastic processing, the benchmark SHOULD record the random seed or equivalent reproducibility control.

If deterministic execution cannot be guaranteed, the result SHOULD state that explicitly.

## 20.2 Configuration Separation

Benchmark configuration MUST be distinguishable from measured results.

Changing the configuration after seeing test results MUST be treated as tuning and MUST NOT be presented as an independent test.

---

# 21. Failure Handling

Failures are benchmark outcomes.

A failure MUST be recorded rather than silently converted into:

- zero error;
- zero runtime;
- zero matches;
- an omitted row;
- a successful-looking placeholder.

## 21.1 Failure Classification

Where possible, classify failures as:

```text
INPUT
  ├── invalid image
  └── missing required information

PREPROCESSING
  ├── preprocessing failure
  └── representation failure

MATCHING
  ├── insufficient features
  └── insufficient candidate matches

GEOMETRY
  ├── RANSAC failure
  ├── insufficient inliers
  └── unstable transformation

REGISTRATION
  ├── warp failure
  └── invalid registered output

EVALUATION
  ├── missing check points
  └── invalid ground truth

SYSTEM
  ├── runtime/resource failure
  └── unexpected execution failure
```

The exact failure taxonomy MAY be extended by the implementation, but extensions MUST remain documented.

---

# 22. Aggregate Reporting

When multiple benchmark pairs are evaluated, results SHOULD be reported both per pair and in aggregate.

The aggregate report SHOULD include:

- number of evaluated pairs;
- number of successful runs;
- number of failed runs;
- failure rate;
- inlier statistics;
- spatial coverage;
- check-point RMSE;
- ground error where meaningful;
- runtime.

Aggregate values MUST NOT hide important sensor-specific or stress-specific failures.

Results SHOULD be separated by relevant categories such as:

- sensor;
- stress condition;
- method;
- preprocessing route.

The benchmark SHOULD avoid combining fundamentally different sensor regimes into one number when that number would obscure the underlying behavior.

The project guidance specifically recommends separate sensor results rather than automatically producing one mixed average, especially when adding IIRS.

---

# 23. Interpretation Rules

V1 results MUST be interpreted according to what the benchmark actually measures.

## 23.1 What Low RMSE Indicates

A low independent check-point RMSE indicates that the evaluated transformation predicts the independent check points with low measured positional error under the benchmark's coordinate definition.

It does not by itself prove:

- global lunar accuracy;
- universal sensor invariance;
- illumination invariance;
- robustness outside the tested image conditions;
- correctness of all candidate correspondences.

## 23.2 What High Inlier Ratio Indicates

A high inlier ratio indicates that a large fraction of candidate correspondences survived the chosen geometric verification procedure.

It does not by itself prove low registration error.

## 23.3 What High Inlier Count Indicates

A high inlier count indicates many geometrically consistent correspondences under the selected model.

It does not guarantee that those points are distributed across the complete overlap.

## 23.4 What High Spatial Coverage Indicates

High spatial coverage indicates that verified correspondences extend across more of the evaluated region.

It does not by itself guarantee accurate transformation parameters.

## 23.5 What a Good Overlay Indicates

A visually good overlay provides qualitative evidence.

It MUST NOT replace independent numerical evaluation.

---

# 24. V1 Acceptance Criteria

V1 acceptance is protocol-based rather than dependent on an invented numerical performance threshold.

A V1 implementation satisfies the benchmark protocol when it can:

- accept a documented known-overlap source/reference pair;
- identify the source and reference products;
- apply a reproducible preprocessing route;
- produce candidate local correspondences;
- distinguish candidate matches from verified inliers;
- perform geometric verification;
- estimate and record a transformation;
- produce a registered output;
- evaluate the final transformation using independent check points;
- report source-pixel registration error where check-point ground truth permits it;
- report inlier count and inlier ratio;
- report spatial distribution/coverage;
- record runtime;
- record failures;
- preserve sufficient metadata for reproducibility.

A benchmark run MUST NOT be declared successful solely because an image overlay looks visually acceptable.

---

# 25. What a Successful V1 Benchmark Proves

A successful V1 benchmark demonstrates that the ChandraMap pipeline can perform a measurable known-overlap correspondence and registration task under the tested conditions.

Specifically, it can establish evidence that:

```text
Known overlap
    ↓
Local correspondence
    ↓
Geometric verification
    ↓
Transformation estimation
    ↓
Registration
    ↓
Independent quantitative evaluation
```

works for the evaluated data.

It also establishes a baseline against which later improvements can be measured.

---

# 26. What V1 Does Not Prove

A successful V1 result does **not** prove:

- whole-Moon retrieval capability;
- universal lunar image correspondence;
- universal Sun-angle invariance;
- universal scale invariance;
- universal cross-sensor robustness;
- hyperspectral registration performance;
- performance on unseen sensors;
- performance on unseen terrain;
- global geolocation accuracy;
- production-scale retrieval performance;
- suitability of every advanced matcher;
- suitability of a single global transformation for all lunar imagery;
- that upsampling recovers missing spatial detail;
- that a visually good mosaic is scientifically accurate.

These claims require separate evidence and, where appropriate, later benchmark versions.

---

# 27. V1 Extension Boundaries

The following extensions SHOULD be treated as controlled additions rather than silently changing the definition of V1:

## 27.1 Global Retrieval

If global retrieval is introduced:

```text
Reference images
    ↓
Tiling / scale representation
    ↓
Global descriptor
    ↓
Reference index
    ↓
Query descriptor
    ↓
Top-K candidates
    ↓
Local matching
```

Global retrieval and local correspondence MUST remain distinct stages.

If FAISS is used, an offline reference index and compatible global descriptor representation are required before online retrieval. FAISS performs vector similarity search; it does not itself create the reference image index or global descriptor.

Global retrieval SHOULD be conditional when reliable geolocation, footprint, or map-projection metadata can already constrain the search.

## 27.2 Learned Matchers

Methods such as:

- ALIKED + LightGlue;
- LoFTR;
- RIFT-style approaches;
- CFOG-style approaches;

may be evaluated as controlled alternatives.

They MUST be measured on the same benchmark pairs and evaluation protocol.

The project feedback recommends establishing SIFT first and then testing a stronger learned or multimodal approach rather than placing every algorithm into one pipeline without evidence.

## 27.3 Additional Sensors

OHRC and TMC-2 can establish the initial optical registration workflow.

IIRS SHOULD be treated as its own experiment when introduced because its spectral and spatial characteristics differ substantially from conventional visible panchromatic imagery.

The benchmark MUST preserve sensor-specific results.

---

# 28. Scientific Reporting Rules

Every V1 report SHOULD make the following distinctions explicit:

### Measured

Values directly produced by the benchmark, such as:

- inlier count;
- inlier ratio;
- coverage;
- check-point RMSE;
- runtime;
- failure rate.

### Configuration

Choices used to generate the result, such as:

- preprocessing;
- scale;
- matcher;
- transformation model;
- refinement.

### Observed

Qualitative findings supported by the experiment, such as systematic residual patterns or failure modes.

### Interpretation

Scientific conclusions drawn from the measurements.

### Unverified

Claims that require additional experiments and therefore MUST NOT be presented as established benchmark conclusions.

---

# 29. Recommended V1 Experiment Sequence

The recommended progression is:

```text
Experiment A
Known pair
    ↓
SIFT
    ↓
RANSAC
    ↓
Transformation
    ↓
Registration
    ↓
Independent check-point error
```

Then:

```text
Experiment B
Reference multi-scale representation
+
Structure-focused preprocessing
    ↓
Same benchmark pair
    ↓
Compare measurements
```

Then:

```text
Experiment C
Optional stronger matcher
    ↓
Same benchmark pairs
    ↓
Same evaluation protocol
    ↓
Compare measurements
```

Then:

```text
Experiment D
Sub-pixel refinement
    ↓
Verified inliers
    ↓
Refined tie points
    ↓
Final transform refit
    ↓
Independent check-point RMSE
```

Then:

```text
Experiment E
Additional sensors / stress conditions
    ↓
Separate sensor-specific reporting
```

This follows the project guidance to build one measurable end-to-end result before expanding toward retrieval, stronger matchers, refinement, and additional sensors.

---

# 30. Benchmark Decision Rules

The following rules are mandatory for interpretation:

1. **Candidate matches are not verified inliers.**
2. **Verified inliers are not independent check points.**
3. **Training/development points are not test points.**
4. **A visual overlay is not an accuracy metric.**
5. **More matches are not automatically better.**
6. **More inliers are not automatically better if they are spatially clustered.**
7. **Upsampling does not create physical spatial information.**
8. **Brightness normalization does not guarantee illumination invariance.**
9. **A single global transformation is not assumed to model every lunar image pair.**
10. **Flexible warping must not hide inaccurate correspondences.**
11. **Source-pixel error and ground error must remain distinct.**
12. **Different sensors must not be treated as physically identical.**
13. **Failed runs must be recorded.**
14. **Unmeasured improvements must not be claimed.**
15. **Test-set tuning is prohibited.**
16. **Benchmark comparisons must use the same evaluation protocol.**

---

# 31. Reference Sensor/Data Context

The project source material identifies the following data sources and intended uses:

| Dataset                       | Intended use                                         |
| ----------------------------- | ---------------------------------------------------- |
| Chandrayaan-2 OHRC            | Main target/source imagery                           |
| Chandrayaan-2 TMC-2           | Main target/source imagery                           |
| Chandrayaan-2 IIRS            | Main target/source imagery                           |
| LRO NAC                       | Reference and training pairs                         |
| LRO WAC                       | Additional lunar-scale/illumination training         |
| Kaguya/SELENE TC              | Optional additional cross-sensor training            |
| Synthetic lunar augmentations | Sun-angle, rotation, scale, and contrast experiments |

These sources are project-context data sources, not a statement that every dataset is required by the V1 benchmark.

---

# 32. Benchmark Integrity Checklist

Before accepting a V1 result, verify:

### Input

- [ ] Source image is identified.
- [ ] Reference image is identified.
- [ ] Sensor/product metadata is recorded.
- [ ] Image dimensions are recorded.
- [ ] GSD is recorded when available.
- [ ] Projection/footprint metadata is recorded when available.
- [ ] Known overlap is documented.

### Preprocessing

- [ ] Sensor-specific preprocessing is recorded.
- [ ] Effective matching scale is recorded.
- [ ] Reference downsampling/pyramid configuration is recorded where used.
- [ ] No artificial spatial-detail claim is made from upsampling.
- [ ] IIRS representation is documented separately when applicable.

### Matching

- [ ] SIFT baseline configuration is recorded.
- [ ] Candidate matches are recorded.
- [ ] Matching filters are recorded.
- [ ] Candidate matches are distinguished from verified inliers.

### Geometry

- [ ] RANSAC/geometric verification is recorded.
- [ ] Transformation model is recorded.
- [ ] Inlier count is recorded.
- [ ] Inlier ratio is recorded.
- [ ] Spatial distribution is evaluated.
- [ ] Residuals are inspected.
- [ ] Sub-pixel refinement is explicitly marked as used or not used.
- [ ] Final transformation is refitted after refinement when refinement is used.

### Evaluation

- [ ] Fit points are separated from check points.
- [ ] Check points are independent.
- [ ] Check-point RMSE is calculated where ground truth permits.
- [ ] Error unit is explicitly stated.
- [ ] Ground error is reported only when physically meaningful.
- [ ] Runtime is recorded.
- [ ] Failures are recorded.

### Reproducibility

- [ ] Configuration is preserved.
- [ ] Dataset/pair identity is preserved.
- [ ] Relevant software/environment information is preserved.
- [ ] Randomness is controlled or documented.
- [ ] No test-set tuning was performed.
- [ ] No unmeasured performance claims are presented.

---

# 33. Benchmark Output Summary

The minimum scientifically meaningful V1 result is:

```text
SOURCE IMAGE
    +
REFERENCE IMAGE
    ↓
LOCAL CANDIDATE MATCHES
    ↓
GEOMETRICALLY VERIFIED INLIERS
    ↓
FINAL TRANSFORMATION
    ↓
REGISTERED OUTPUT
    ↓
INDEPENDENT CHECK-POINT ERROR
```

The corresponding benchmark record should make it possible to answer:

- How many candidate correspondences were produced?
- How many survived geometric verification?
- How were those points distributed?
- What transformation was estimated?
- Was sub-pixel refinement used?
- What was the independent check-point RMSE?
- Was ground error meaningful and reported?
- How long did the run take?
- Did the method fail?
- If it failed, why?
- Can another engineer reproduce the result?

That measurement chain is the core of the ChandraMap V1 benchmark.

---

# 34. Source Basis

This specification is grounded in the project-provided SIH 26166 problem material and the project-specific lunar correspondence and registration feedback.

The source materials emphasize that the primary deliverable is reliable correspondence, source-image sub-pixel accuracy, well-distributed matches, registered output, and measurable metrics such as RMSE and inlier statistics, with the mosaic treated as a downstream demonstration.

The updated technical review further specifies sensor-aware preprocessing, multi-scale handling, geometric verification, sub-pixel refinement, spatial coverage, and independent check-point evaluation as the core measurement direction.

The SIH project data identifies Chandrayaan-2 OHRC, TMC-2, IIRS, LRO NAC, LRO WAC, Kaguya/SELENE TC, and synthetic lunar augmentations as relevant data sources with different intended uses.

Where the repository does not define a concrete implementation value, filename, threshold, command, dataset size, or completed experiment, this specification intentionally does not invent one.
