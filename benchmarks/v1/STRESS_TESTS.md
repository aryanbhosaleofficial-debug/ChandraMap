# ChandraMap V1 — Stress Tests

> **Benchmark document:** `benchmarks/v1/STRESS_TESTS.md`
> **Benchmark version:** V1
> **Status:** Proposed stress-testing framework
> **Project:** ChandraMap
> **Problem:** SIH 26166 — Multi-modal, Sun angle and scale invariant image correspondence using Chandrayaan-2 optical images

---

## 1. Purpose

This document defines the stress-testing framework for the ChandraMap V1 benchmark.

The purpose of stress testing is to determine how the lunar image correspondence and registration pipeline behaves when one or more input conditions become difficult, degraded, unusual, or close to the expected operating limits.

Stress testing extends ordinary benchmark evaluation by deliberately introducing controlled difficulty while preserving the underlying evaluation protocol.

The core V1 pipeline is:

```text
Lunar source/reference imagery
        ↓
Preprocessing
        ↓
Feature / correspondence detection
        ↓
Candidate correspondences
        ↓
Geometric verification
        ↓
RANSAC / robust model estimation
        ↓
Verified correspondences
        ↓
Optional sub-pixel refinement
        ↓
Final transformation
        ↓
Registration
        ↓
Quantitative evaluation
        ↓
Stress testing
```

The stress-test framework is concerned primarily with:

- illumination changes;
- scale differences;
- sensor/modality differences;
- geometric differences;
- low-feature or repetitive terrain;
- robustness of geometric verification;
- spatial distribution of correspondences;
- registration accuracy;
- failure behavior;
- runtime behavior where applicable.

The project feedback explicitly recommends a small stress-test matrix covering easy pairs, Sun-angle stress, scale stress, modality stress, geometry stress, and low-feature terrain.

---

# 2. Status and Scope

The stress-test categories defined in this document are a **benchmark framework**, not evidence that every category has already been implemented or executed.

Unless implementation or execution evidence exists elsewhere in the repository, individual tests defined here are considered:

- **Proposed** — defined as part of the benchmark design but not established as implemented.
- **Recommended** — recommended for implementation/evaluation.
- **Planned** — explicitly scheduled by the project.
- **Implemented** — only when repository evidence confirms implementation.
- **Executed** — only when benchmark artifacts/results confirm execution.

This document contains **no benchmark results**.

No robustness claim should be inferred from the existence of a stress-test definition.

---

# 3. Stress Testing vs Ordinary Benchmark Evaluation

Stress testing and ordinary benchmark evaluation answer different questions.

## 3.1 Ordinary Benchmark

The ordinary V1 benchmark asks:

> Can the pipeline perform the correspondence and registration task under the defined benchmark conditions?

The benchmark establishes a controlled reference point.

## 3.2 Stress Test

A stress test asks:

> How does the pipeline behave when a specific condition becomes more difficult?

The stress test therefore focuses on **degradation, failure behavior, and robustness**, not merely absolute performance.

The fundamental relationship is:

```text
Baseline
   ↓
Apply controlled stress
   ↓
Run identical evaluation pipeline
   ↓
Compare metrics
   ↓
Analyze degradation / failure
```

The baseline and stressed experiment should use the same evaluation definitions wherever possible.

---

# 4. Stress-Test Principles

The V1 stress-testing framework follows these principles.

## 4.1 Reproducible Baseline

Every stress experiment MUST have a clearly identified baseline configuration.

The baseline should correspond to the ordinary V1 benchmark configuration without the additional stress perturbation.

## 4.2 One Major Variable at a Time

Whenever practical, change one major stress factor at a time.

For example:

```text
Baseline
   ↓
Same image pair
   ↓
Different illumination
   ↓
Evaluate
```

is preferable to simultaneously changing:

```text
illumination
+ scale
+ sensor
+ viewpoint
+ preprocessing
```

unless the experiment is explicitly designed as a combined-stress test.

## 4.3 Preserve Ground Truth

The stress operation MUST NOT invalidate or silently modify the ground-truth definition.

When synthetic degradation is applied to an image, the corresponding ground truth MUST remain geometrically consistent with the transformed image.

## 4.4 Independent Evaluation Points

Transformation fitting points MUST remain separate from independent check points.

The final registration error MUST NOT be evaluated only on the same points used to estimate the transformation.

The project feedback explicitly warns that evaluating RMSE on the same points used for transformation fitting can make registration appear better than it actually is.

## 4.5 Preserve Failures

A failed stress-test run is an informative result.

It MUST NOT be silently removed from the benchmark.

## 4.6 Quantitative Measurement

Stress testing should measure changes in:

- inlier count;
- inlier ratio;
- spatial coverage;
- independent check-point RMSE;
- ground error where meaningful;
- runtime;
- failure status/rate.

The project feedback identifies these as the core measurable evaluation outputs.

## 4.7 No Post-Hoc Tuning

A method MUST NOT be tuned specifically against the stress-test result and then reported as though it were an unchanged robustness evaluation.

If adaptation is studied, it MUST be documented as a separate experiment.

## 4.8 Separate Natural and Synthetic Difficulty

The benchmark MUST distinguish between:

- naturally difficult lunar image pairs; and
- artificially degraded/synthetic test cases.

These provide different types of evidence.

---

# 5. Baseline Definition

The **baseline** is the standard V1 benchmark configuration without an additional stress perturbation.

The exact implementation configuration is defined by the V1 benchmark specification and repository configuration.

Conceptually:

```text
Source image
     +
Reference image
     ↓
Standard V1 preprocessing
     ↓
Standard V1 local matching
     ↓
Candidate correspondences
     ↓
RANSAC / geometric verification
     ↓
Verified inliers
     ↓
Optional V1 refinement
     ↓
Final transformation
     ↓
Registration
     ↓
Independent evaluation
```

The baseline MUST be recorded for every stress experiment.

At minimum, the stress-test record should identify:

- benchmark pair;
- source/reference products;
- preprocessing configuration;
- matching method;
- geometric model;
- refinement configuration;
- evaluation configuration.

---

# 6. Stress-Test Definition

A stress-test instance consists of:

```text
Baseline configuration
        +
Controlled perturbation
        ↓
Stressed configuration
        ↓
Same evaluation protocol
        ↓
Comparison
```

Each stress-test instance MUST document:

| Field               | Description                                                |
| ------------------- | ---------------------------------------------------------- |
| `stress_id`         | Unique identifier for the stress experiment                |
| `stress_category`   | Illumination, scale, modality, geometry, low-feature, etc. |
| `baseline_id`       | Baseline used for comparison                               |
| `pair_id`           | Benchmark image pair                                       |
| `variable_changed`  | Condition intentionally modified                           |
| `fixed_variables`   | Conditions intentionally held constant                     |
| `stress_definition` | Exact description of the perturbation                      |
| `ground_truth`      | Ground-truth source                                        |
| `evaluation_points` | Independent check-point definition                         |
| `method`            | Matching/registration method                               |
| `configuration`     | Relevant parameters                                        |
| `status`            | Proposed/implemented/executed                              |
| `results`           | Measurements, when execution evidence exists               |
| `failure_reason`    | Failure classification, when applicable                    |

---

# 7. Stress-Test Matrix

The V1 stress-test framework consists of the following primary categories.

| ID    | Stress category                  | Primary question                                                              | Status      |
| ----- | -------------------------------- | ----------------------------------------------------------------------------- | ----------- |
| ST-01 | Easy-pair baseline               | Does the complete pipeline work end-to-end?                                   | Proposed    |
| ST-02 | Sun-angle / illumination         | How does correspondence degrade under changed shadow geometry?                | Proposed    |
| ST-03 | Scale                            | How does performance change with large ground-scale differences?              | Proposed    |
| ST-04 | Modality / sensor                | How does the pipeline behave across different sensor characteristics?         | Proposed    |
| ST-05 | Geometry                         | How robust is the transformation model under relief/viewpoint differences?    | Proposed    |
| ST-06 | Low-feature / repetitive terrain | How does the pipeline behave where stable local features are scarce?          | Proposed    |
| ST-07 | Combined stress                  | How does the system behave when multiple difficult conditions occur together? | Recommended |

These categories follow the project feedback's proposed stress matrix.

The statuses above describe this document's benchmark-definition state, not implementation or execution results.

---

# 8. ST-01 — Easy-Pair Baseline

**Status:** Proposed

## 8.1 Purpose

The easy-pair experiment establishes the reference behavior against which harder cases can be compared.

It should represent a known-overlap pair with relatively compatible conditions.

The project feedback defines the easy pair as a case with:

- known overlap;
- similar illumination;
- moderate scale difference.

## 8.2 Variables

The primary stress factor is:

```text
None
```

This is a baseline rather than a degradation experiment.

## 8.3 Expected Role

The experiment should demonstrate the complete path:

```text
Known overlap
    ↓
SIFT / selected local matcher
    ↓
Candidate matches
    ↓
RANSAC
    ↓
Verified inliers
    ↓
Transformation
    ↓
Registration
    ↓
Independent check-point evaluation
```

## 8.4 Measurements

Collect:

- candidate-match count;
- inlier count;
- inlier ratio;
- spatial coverage;
- check-point RMSE;
- ground error where meaningful;
- runtime;
- success/failure;
- failure reason.

The existence of this baseline does not constitute a successful result.

---

# 9. ST-02 — Sun-Angle / Illumination Stress

**Status:** Proposed

## 9.1 Purpose

This stress test measures how correspondence and registration behave when the same lunar terrain is observed under different illumination conditions.

The key issue is that lunar illumination changes can alter shadow geometry, not merely image brightness.

The project feedback specifically states that brightness or contrast normalization cannot make a shadow caused by different Sun geometry appear in the same physical location.

## 9.2 Test Construction

Where suitable real imagery exists, construct a comparison such as:

```text
Same lunar region
        ↓
Similar illumination pair
        +
Very different illumination pair
```

The geographic region should remain as comparable as possible.

The evaluation protocol should remain unchanged.

## 9.3 Variables Changed

Primary variable:

- illumination / Sun angle.

Possible observable consequences include:

- changed shadow position;
- changed shadow extent;
- altered local intensity;
- changed apparent feature structure.

The exact illumination metadata should be retained when available.

## 9.4 Variables Held Fixed

Where possible, keep fixed:

- lunar region;
- source/reference product definitions;
- ground truth;
- evaluation points;
- matching method;
- geometric model;
- evaluation metrics;
- benchmark configuration.

## 9.5 Representation Variants

A controlled illumination experiment may compare:

```text
Raw grayscale
       vs
Gradient / edge representation
       vs
Structure-focused representation
```

Only representations actually implemented and configured in the benchmark should be reported as experimental results.

The project feedback recommends testing raw grayscale against gradients, edges, or other structure-focused representations for strong Sun-angle differences.

## 9.6 Measurements

Report:

- inlier count;
- inlier ratio;
- spatial coverage;
- independent check-point RMSE;
- ground error where meaningful;
- runtime;
- failure status.

## 9.7 Degradation Measurement

For a metric where lower is better, such as RMSE:

$$
\Delta RMSE = RMSE_{stress} - RMSE_{baseline}
$$

Relative degradation may be reported as:

$$
D_{RMSE} =
\frac{RMSE_{stress} - RMSE_{baseline}}
     {RMSE_{baseline}}
$$

only when the baseline value makes this calculation meaningful.

For a metric where higher is better, such as inlier ratio:

$$
\Delta IR = IR_{stress} - IR_{baseline}
$$

The benchmark MUST report the raw values alongside any derived degradation measure.

## 9.8 Interpretation

A performance drop under changed illumination should be reported as an observed sensitivity under the tested conditions.

It MUST NOT automatically be described as failure of the entire illumination strategy.

Similarly, one successful strongly illuminated pair MUST NOT establish general Sun-angle invariance.

---

# 10. ST-03 — Scale Stress

**Status:** Proposed

## 10.1 Purpose

Scale stress evaluates correspondence when source and reference imagery have substantially different effective ground scales.

The project feedback emphasizes that scale handling should compare information at physically meaningful scales rather than simply changing pixel dimensions.

## 10.2 Test Construction

A scale-stress experiment may use:

```text
Source
  ↓
Lower / coarser effective spatial scale

Reference
  ↓
Higher / finer spatial scale
```

and evaluate a controlled scale-compatible representation.

A reference pyramid or controlled downsampling may be used where supported by the implementation.

## 10.3 Variables Changed

Primary variable:

- effective source/reference scale relationship.

## 10.4 Variables Held Fixed

Where possible:

- lunar region;
- illumination;
- modality;
- ground truth;
- evaluation points;
- matching method;
- geometric model.

## 10.5 Physical-Information Rule

Upsampling MUST NOT be treated as recovering missing spatial information.

For example:

```text
Low-resolution source
       ↓
Upsampling
       ↓
More pixels
```

does not mean:

```text
Low-resolution source
       ↓
New physical terrain detail
```

The project feedback explicitly warns against enlarging IIRS imagery to a finer reference resolution and treating the interpolation as recovered detail.

## 10.6 Reference Pyramid

Where a reference image is substantially finer than the source, a scale-aware experiment may construct:

```text
Reference
    ↓
Fine scale
    ↓
Intermediate scale
    ↓
Coarse scale
```

The selected scale should be recorded.

The goal is to make the comparison physically meaningful before fine correspondence is attempted.

## 10.7 Measurements

Collect:

- candidate-match count;
- inlier count;
- inlier ratio;
- spatial coverage;
- check-point RMSE in source pixels;
- ground error when meaningful;
- runtime;
- failure status.

## 10.8 Interpretation

A scale-stress result should answer:

> How much correspondence and registration performance changes as the effective scale difference increases under the tested conditions.

It MUST NOT be interpreted as evidence that a method is universally scale invariant.

---

# 11. ST-04 — Modality / Sensor Stress

**Status:** Proposed

## 11.1 Purpose

Modality stress evaluates correspondence when the source and reference images differ in sensor characteristics.

The project materials explicitly state that OHRC, TMC-2, and IIRS should not be treated as identical images.

## 11.2 Relevant Sensor/Data Types

The supplied project materials identify:

- Chandrayaan-2 OHRC;
- Chandrayaan-2 TMC-2;
- Chandrayaan-2 IIRS;
- LRO NAC;
- LRO WAC;
- Kaguya/SELENE TC;
- synthetic lunar augmentations.

The SIH problem material identifies OHRC, TMC-2, and IIRS as main target/source images and LRO NAC as reference/training imagery.

This does **not** mean that every listed source is required for V1 stress testing.

## 11.3 Sensor-Specific Processing

The benchmark should preserve sensor-specific processing routes.

Conceptually:

```text
OHRC ──────→ Sensor-specific preparation ──┐
                                           │
TMC-2 ─────→ Sensor-specific preparation ──┤
                                           ├→ Common structural comparison
IIRS ──────→ Sensor-specific 2D preparation┘
```

The project feedback recommends using a common structural representation only after sensor-specific preparation rather than forcing all sensors through one identical path.

## 11.4 IIRS Handling

If IIRS is evaluated, its representation MUST be explicitly documented.

Potential representations identified by the project feedback include:

- selected spectral band;
- PCA/composite representation;
- structural representation.

These are experimental alternatives, not claims that all are currently implemented.

The project feedback specifically recommends starting with a simple 2D representation rather than immediately treating the full hyperspectral cube as a conventional 2D camera image.

## 11.5 Variables Changed

Depending on the experiment:

- sensor;
- spectral modality;
- representation;
- effective spatial scale.

The experiment MUST clearly distinguish which variable is being stressed.

## 11.6 Variables Held Fixed

Where possible:

- lunar region;
- ground truth;
- evaluation points;
- evaluation metrics;
- geometric protocol.

## 11.7 Measurements

Report:

- inlier count;
- inlier ratio;
- spatial coverage;
- source-image check-point RMSE;
- ground error where meaningful;
- runtime;
- failure status.

## 11.8 Sensor-Specific Reporting

Results from materially different sensors SHOULD be reported separately.

The project feedback specifically recommends separate sensor results rather than hiding different sensor behaviors inside one mixed average.

## 11.9 Physical Scale Warning

Pixel error must remain tied to the source sensor.

The project feedback explicitly notes that the same numerical pixel error does not represent the same physical ground error across sensors.

Therefore:

```text
Source-image pixel error
        ↓
Primary registration metric
        ↓
Ground metres only when conversion is meaningful
```

---

# 12. ST-05 — Geometry Stress

**Status:** Proposed

## 12.1 Purpose

Geometry stress evaluates the robustness of the transformation model when image geometry becomes more difficult.

Relevant conditions include:

- relief-rich terrain;
- stronger viewpoint differences;
- residual geometric variation;
- conditions where a single planar model may become insufficient.

The project feedback explicitly recommends geometry stress using relief-rich terrain or stronger viewpoint differences.

## 12.2 Transformation Principle

The benchmark should use:

> The simplest transformation that explains the residuals.

Affine transformation or homography may be reasonable first models for local, already map-projected pairs.

However, the benchmark MUST NOT assume that a single global transformation is universally valid for lunar imagery.

The Moon is not a flat planar scene, and raw imagery may contain sensor/viewing geometry effects.

## 12.3 Test Construction

A geometry stress experiment should compare:

```text
Ordinary geometry
        ↓
Baseline transformation
```

against:

```text
More difficult terrain/viewing geometry
        ↓
Same transformation protocol
        ↓
Residual analysis
```

## 12.4 Residual Analysis

Residual vectors should be inspected across the image.

A systematic pattern such as:

```text
left side → small residual
middle    → moderate residual
right side → large residual
```

may indicate that the selected global model is not adequately describing the geometry.

The benchmark MUST preserve such evidence rather than hiding it with an increasingly flexible warp.

## 12.5 Local/Piecewise Models

A local or piecewise model may be studied when residual structure justifies it.

It should only be introduced after:

1. reliable correspondences are established;
2. spatial distribution is measured;
3. the residual structure is understood;
4. the final model is independently evaluated.

Flexible warping MUST NOT be used solely to improve visual appearance.

The project feedback specifically warns that flexible warping can make an overlay appear good even when the underlying correspondences are weak.

## 12.6 Sensor Geometry and DEM

Where appropriate project data contains usable geometry information, the experiment may investigate:

- sensor geometry;
- map projection;
- orthorectification;
- DEM information.

Such information should be recorded as part of the experiment.

---

# 13. ST-06 — Low-Feature / Repetitive Terrain Stress

**Status:** Proposed

## 13.1 Purpose

This stress test evaluates performance where the terrain contains fewer distinctive local structures or contains repetitive patterns that may encourage false correspondences.

The project feedback identifies smooth/repetitive terrain as a specific stress category because false matches are more likely there.

## 13.2 Test Construction

Candidate regions may contain:

- smooth terrain;
- weakly textured terrain;
- repetitive terrain;
- visually similar local structures.

The benchmark SHOULD preserve the actual terrain characteristics rather than artificially labeling a region as difficult without evidence.

## 13.3 Variables

Primary stress factor:

- availability and distinctiveness of local terrain structure.

## 13.4 Measurements

Collect:

- candidate-match count;
- inlier count;
- inlier ratio;
- spatial coverage;
- check-point RMSE;
- ground error where meaningful;
- runtime;
- failure classification.

## 13.5 Spatial Distribution

Spatial distribution becomes particularly important for low-feature terrain.

A large number of correspondences clustered around one repeated structure MUST NOT automatically be treated as strong registration evidence.

The benchmark should visualize and measure where verified correspondences occur.

The project feedback explicitly emphasizes that good points should be distributed across the overlap rather than clustered around a single crater or local structure.

## 13.6 Failure Analysis

Useful failure observations include:

- insufficient candidate correspondences;
- many candidates but few inliers;
- high inlier ratio with poor spatial coverage;
- unstable transformation;
- high independent check-point error;
- transformation failure.

The benchmark MUST record the actual observed failure mode rather than assigning a generic "low confidence" label.

---

# 14. ST-07 — Combined Stress

**Status:** Recommended

Combined stress evaluates interactions between multiple difficult conditions.

Examples include:

```text
Scale
+
Illumination
```

or:

```text
Scale
+
Modality
+
Illumination
```

or:

```text
Geometry
+
Low-feature terrain
```

Combined stress should be performed only after the individual stress categories are sufficiently controlled to make the interaction interpretable.

## 14.1 Why Combined Stress Is Different

A single-factor experiment answers:

> What happens when factor X becomes difficult?

A combined experiment asks:

> What happens when several difficult conditions occur simultaneously?

The latter is useful for system-level robustness but is harder to diagnose.

## 14.2 Reporting Requirement

Combined-stress results MUST clearly identify every changed factor.

Do not attribute a performance change to one factor when several factors changed simultaneously.

---

# 15. Synthetic Stress Tests

**Status:** Recommended

Synthetic stress can be useful when naturally occurring examples are limited.

However, synthetic degradation MUST be clearly separated from naturally difficult lunar imagery.

## 15.1 Suitable Synthetic Categories

The project materials identify synthetic lunar augmentations involving:

- Sun-angle-related conditions;
- rotation;
- scale;
- contrast.

The SIH problem material lists synthetic lunar augmentations for Sun-angle, rotation, scale, and contrast experiments.

## 15.2 Synthetic Transformation Rules

Every synthetic transformation MUST record:

- original image/pair;
- transformation applied;
- transformation parameters;
- random seed where applicable;
- resulting image;
- transformed ground truth;
- evaluation configuration.

## 15.3 Ground-Truth Consistency

Synthetic transformations MUST preserve a mathematically consistent relationship between the transformed image and its ground truth.

For example, if an image is rotated synthetically, the corresponding ground-truth coordinates must be transformed consistently.

## 15.4 Synthetic vs Natural Reporting

Results SHOULD be reported separately:

```text
Natural difficult cases
```

and:

```text
Synthetic stress cases
```

A model performing well on synthetic contrast changes does not automatically demonstrate robustness to real lunar illumination geometry.

---

# 16. Stress Severity

V1 does not define universal numerical severity thresholds unless such thresholds are explicitly established by the repository.

Stress severity should therefore be represented by the actual controlled parameter or metadata.

Examples:

```text
Illumination:
baseline metadata → stressed metadata

Scale:
baseline effective scale → stressed effective scale

Geometry:
baseline viewing condition → stressed viewing condition
```

Do not invent labels such as:

- "mild";
- "medium";
- "severe";

unless the benchmark configuration explicitly defines what those labels mean.

If severity labels are later introduced, each label MUST correspond to documented measurable conditions.

---

# 17. Variables to Hold Constant

For a one-factor stress test, the following should remain fixed where practical:

| Variable                  | Treatment                       |
| ------------------------- | ------------------------------- |
| Lunar region              | Fixed                           |
| Ground truth              | Fixed                           |
| Independent check points  | Fixed                           |
| Evaluation metrics        | Fixed                           |
| Transformation evaluation | Fixed                           |
| Matching method           | Fixed                           |
| Software version          | Fixed                           |
| Hardware                  | Fixed where runtime is compared |
| Random seed               | Fixed where applicable          |
| Output definitions        | Fixed                           |

If any variable changes, the benchmark record MUST disclose it.

---

# 18. Metrics

Stress testing uses the V1 benchmark metrics.

## 18.1 Candidate Match Count

Number of candidate correspondences produced before geometric verification.

This should be reported because degradation can occur before RANSAC.

---

## 18.2 Inlier Count

Number of correspondences that survive geometric verification.

$$
N_{inlier}
$$

A high count does not by itself establish accurate registration.

---

## 18.3 Inlier Ratio

$$
IR =
\frac{N_{inlier}}
     {N_{candidate}}
$$

If no candidate matches exist, the metric should be reported as unavailable rather than assigned an arbitrary value.

---

## 18.4 Spatial Coverage

Spatial coverage measures whether verified correspondences are distributed across the relevant overlap.

Possible project-supported forms include:

- grid coverage;
- convex-hull coverage.

The feedback gives grid coverage as an example using grid cells containing at least one good inlier.

The exact grid definition, if used, MUST be recorded.

---

## 18.5 Independent Check-Point RMSE

For \(N\) independent check points:

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

- \(g_i\) is the ground-truth position;
- \(\hat{g}\_i\) is the position predicted by the final transformation.

The benchmark should report source-image pixel error first when evaluating source-image sub-pixel registration.

---

## 18.6 Ground Error

Ground error in metres may be reported only when:

- GSD is known;
- projection/reference geometry supports the conversion;
- the ground-truth coordinate system supports the interpretation.

Ground error MUST NOT be fabricated from an unsupported pixel-to-metre conversion.

---

## 18.7 Runtime

Runtime should be recorded when relevant.

If runtime comparisons are made, use equivalent:

- hardware;
- software environment;
- input;
- configuration.

The project feedback identifies runtime as a system-level metric.

---

## 18.8 Failure Rate

When multiple test cases are available:

$$
FailureRate =
\frac{N_{failed}}
     {N_{total}}
$$

The benchmark MUST define what constitutes failure for the experiment before calculating the rate.

A failed run MUST remain in the denominator.

No failure rate should be reported for an experiment where execution evidence is unavailable.

---

# 19. Degradation Analysis

Stress tests should report both absolute measurements and changes relative to baseline.

## 19.1 Absolute Reporting

Example structure:

| Metric            |       Baseline |         Stress |
| ----------------- | -------------: | -------------: |
| Candidate matches | measured value | measured value |
| Inliers           | measured value | measured value |
| Inlier ratio      | measured value | measured value |
| Spatial coverage  | measured value | measured value |
| Check-point RMSE  | measured value | measured value |
| Runtime           | measured value | measured value |
| Status            | measured value | measured value |

No values should be populated until the experiment has actually been executed.

## 19.2 Absolute Difference

For metric \(M\):

$$
\Delta M = M_{stress} - M_{baseline}
$$

The direction of interpretation depends on whether larger or smaller values are desirable.

## 19.3 Relative Change

Where meaningful:

$$
RelativeChange =
\frac{M_{stress}-M_{baseline}}
     {M_{baseline}}
$$

The raw baseline and stressed values MUST accompany the relative value.

## 19.4 RMSE Degradation

For RMSE:

$$
\Delta RMSE =
RMSE_{stress}-RMSE_{baseline}
$$

Positive \(\Delta RMSE\) indicates increased measured error.

This is a mathematical comparison, not a universal robustness threshold.

## 19.5 Inlier-Ratio Change

$$
\Delta IR =
IR_{stress}-IR_{baseline}
$$

A negative value indicates a lower inlier ratio under the tested stress condition.

---

# 20. Robustness Is Not a Single Number

A stress-test result MUST NOT be reduced to a single "robustness score" unless such a metric is explicitly defined, validated, and documented by the benchmark.

A pipeline can exhibit:

```text
Higher inlier ratio
+
Lower spatial coverage
+
Higher RMSE
```

or:

```text
Lower inlier count
+
Similar spatial coverage
+
Similar RMSE
```

These cases have different interpretations.

Therefore, stress testing should retain the full metric profile.

---

# 21. Failure Taxonomy

Failures should be classified according to the stage at which the pipeline breaks.

```text
INPUT
 ├── invalid image
 └── missing required metadata

PREPROCESSING
 ├── preprocessing failure
 └── invalid representation

MATCHING
 ├── insufficient features
 └── insufficient candidate matches

GEOMETRY
 ├── insufficient inliers
 ├── RANSAC failure
 └── unstable transformation

REFINEMENT
 ├── refinement failure
 └── invalid refined points

REGISTRATION
 ├── transformation/warp failure
 └── invalid registered output

EVALUATION
 ├── missing check points
 ├── invalid ground truth
 └── invalid metric

SYSTEM
 ├── runtime failure
 ├── resource failure
 └── unexpected execution failure
```

The actual implementation may extend this taxonomy.

---

# 22. Failure Recording Rules

A failed run MUST contain:

- `status`;
- failure category;
- failure reason where known;
- stress configuration;
- baseline configuration;
- pair identifier;
- available intermediate metrics.

For example, if matching succeeds but RANSAC fails, the benchmark should preserve:

```text
candidate_count
inlier_count = unavailable
registration = failed
failure_stage = geometry
failure_reason = ...
```

It MUST NOT convert the failure into a successful registration result.

---

# 23. Spatial Distribution Analysis

Stress testing MUST consider not only how many correspondences survive, but where they occur.

A useful representation is:

```text
+---------+---------+---------+---------+
|         |         |         |         |
|    •    |         |    •    |         |
+---------+---------+---------+---------+
|         |    •    |         |         |
|         |         |         |    •    |
+---------+---------+---------+---------+
|    •    |         |    •    |         |
|         |         |         |         |
+---------+---------+---------+---------+
|         |    •    |         |    •    |
|         |         |         |         |
+---------+---------+---------+---------+
```

The exact grid size should come from the benchmark configuration.

The objective is to detect cases where apparently strong correspondence is concentrated in a small portion of the overlap.

The project feedback identifies grid coverage or convex-hull coverage specifically for this purpose.

---

# 24. Residual Analysis

Residual vectors should be inspected for geometry-related stress tests.

Conceptually:

```text
Verified control points
        ↓
Fit transformation
        ↓
Compute residual vectors
        ↓
Inspect spatial pattern
```

Possible observations include:

- approximately uniform residuals;
- systematic directional residuals;
- increasing residual magnitude across the image;
- localized residual clusters.

A systematic residual pattern may indicate that the transformation model does not adequately represent the image geometry.

The benchmark should preserve these observations as diagnostic evidence.

---

# 25. Sub-Pixel Stress Evaluation

Sub-pixel refinement is evaluated only after reliable geometric verification.

The intended sequence is:

```text
Candidate matches
       ↓
RANSAC
       ↓
Verified inliers
       ↓
Sub-pixel refinement
       ↓
Final transform refit
       ↓
Independent check-point evaluation
```

The project feedback explicitly recommends refining verified control points and then refitting the final transformation.

## 25.1 Before/After Refinement

If refinement is implemented, a controlled experiment may compare:

```text
Without refinement
```

against:

```text
With refinement
```

while preserving:

- image pair;
- preprocessing;
- matching method;
- geometric verification;
- check points.

## 25.2 Required Measurements

Where implemented:

- check-point RMSE before refinement;
- check-point RMSE after refinement;
- spatial coverage;
- inlier count;
- inlier ratio;
- runtime.

The benchmark MUST NOT claim sub-pixel accuracy merely because a refinement stage exists.

---

# 26. Visual Evidence

Visualizations are useful for diagnosing stress behavior but MUST NOT replace numerical evaluation.

Recommended visual artifacts include:

1. Source/reference pair.
2. Candidate matches.
3. RANSAC inliers.
4. Rejected correspondences.
5. Spatial distribution of inliers.
6. Registered overlay.
7. Residual vectors.
8. Stress-vs-baseline comparison.

The project feedback recommends preserving match plots, rejected outliers, registered overlays, inlier statistics, and check-point error as evidence for the end-to-end milestone.

---

# 27. Visual Overlay Rules

A visually good overlay is useful evidence but does not prove scientific correctness.

The benchmark MUST NOT:

- declare success solely from visual alignment;
- hide poor correspondences behind a flexible warp;
- omit numerical error;
- omit independent check-point evaluation;
- use overlay appearance as a substitute for spatial coverage.

The project feedback explicitly warns that a visually good overlay can still be scientifically wrong.

---

# 28. Method Comparison Under Stress

When comparing algorithms, use the same stress-test pairs.

A recommended comparison structure is:

```text
Same image pairs
       │
       ├── SIFT baseline
       │
       ├── stronger matcher
       │
       └── sensor-aware + multi-scale pipeline
                ↓
          Same metrics
                ↓
       Stress-case comparison
```

The project feedback recommends comparing:

1. SIFT baseline;
2. stronger matcher only;
3. full sensor-aware + multi-scale pipeline.

The purpose is to determine which component produces the observed change rather than simply reporting that a larger pipeline is better.

---

# 29. Advanced Matchers in Stress Tests

Potential alternative local matching paths identified by the project feedback include:

- SIFT;
- ALIKED + LightGlue;
- LoFTR;
- RIFT/CFOG-style approaches.

These are **method candidates**, not evidence that every method is implemented.

The feedback recommends starting with SIFT and then testing a learned path on the same image pairs.

## 29.1 Fair Comparison

Each method comparison should preserve:

- same source/reference pairs;
- same ground truth;
- same independent check points;
- same stress condition;
- same evaluation metrics.

## 29.2 Domain-Shift Warning

Pretrained learned matchers MUST NOT automatically be described as lunar-invariant.

The project feedback explicitly warns that pretrained terrestrial models are not automatically robust to lunar imagery.

---

# 30. Stress-Test Reproducibility

Every stress configuration MUST be versioned or otherwise uniquely identifiable.

A reproducible stress test should allow an engineer to determine:

- which image pair was used;
- which stress condition was applied;
- which baseline was used;
- which parameters changed;
- which parameters remained fixed;
- which preprocessing was used;
- which matcher was used;
- which geometric model was used;
- which evaluation points were used;
- which software/environment was used;
- whether randomness was involved.

---

# 31. Randomness

If a stress test uses stochastic processing:

- record the random seed where supported;
- distinguish deterministic and stochastic stages;
- preserve enough information to repeat the experiment.

If multiple random seeds are evaluated, the seed policy MUST be documented.

A single favorable random run MUST NOT be presented as general robustness evidence.

---

# 32. Dataset Leakage and Tuning

Stress-test data MUST NOT be used to tune a model and then reported as an independent robustness evaluation without disclosure.

The benchmark should distinguish:

```text
Development / tuning
```

from:

```text
Stress-test evaluation
```

If a method is adapted after observing stress-test failures, the adapted method should be evaluated as a separate configuration.

---

# 33. Natural vs Synthetic Stress

Stress tests should identify the source of difficulty.

| Stress source | Meaning                                                        |
| ------------- | -------------------------------------------------------------- |
| Natural       | Difficulty occurs naturally in real lunar imagery              |
| Synthetic     | Difficulty is deliberately generated from an existing image    |
| Mixed         | Natural difficult pair with additional controlled perturbation |

Natural cases are especially important because synthetic transformations may not reproduce all physical effects of lunar imaging.

---

# 34. Benchmark Artifact Requirements

Each executed stress-test case should preserve, where applicable:

```text
stress configuration
        +
baseline configuration
        +
source/reference metadata
        +
candidate correspondences
        +
verified inliers
        +
transformation
        +
registration output
        +
independent check-point results
        +
spatial coverage
        +
runtime
        +
failure information
```

The exact repository artifact paths are intentionally not specified here unless they are defined elsewhere in the project.

---

# 35. Recommended Stress-Test Record

A structured stress-test record should contain fields equivalent to:

```yaml
stress_id:
stress_category:
status:

baseline:
  benchmark_id:
  configuration:

pair:
  pair_id:
  source:
  reference:

stress:
  variable:
  description:
  parameters:
  fixed_variables:

evaluation:
  ground_truth:
  fitting_points:
  check_points:

method:
  matcher:
  preprocessing:
  transform:
  refinement:

metrics:
  candidate_count:
  inlier_count:
  inlier_ratio:
  spatial_coverage:
  checkpoint_rmse_px:
  ground_error_m:
  runtime:

result:
  status:
  failure_stage:
  failure_reason:

reproducibility:
  seed:
  software_environment:
```

This is a logical reporting schema. It does not claim that the repository currently implements this exact serialization format.

---

# 36. Result Reporting Template

When an experiment has actually been executed, report it using a structure such as:

## Stress Test: `<STRESS-ID>`

**Status:** Executed

**Stress condition:** `<description>`

**Baseline:** `<baseline identifier>`

**Image pair:** `<pair identifier>`

### Configuration

| Variable     | Baseline  | Stress    |
| ------------ | --------- | --------- |
| `<variable>` | `<value>` | `<value>` |

### Metrics

| Metric            | Baseline | Stress | Change |
| ----------------- | -------: | -----: | -----: |
| Candidate matches |        — |      — |      — |
| Inliers           |        — |      — |      — |
| Inlier ratio      |        — |      — |      — |
| Spatial coverage  |        — |      — |      — |
| Check-point RMSE  |        — |      — |      — |
| Ground error      |        — |      — |      — |
| Runtime           |        — |      — |      — |

### Failure Behavior

- **Baseline:** `<status>`
- **Stress:** `<status>`
- **Failure stage:** `<stage if applicable>`
- **Failure reason:** `<reason if known>`

### Interpretation

Describe only observations supported by the measured results.

Do not convert the result into a generalized robustness claim without sufficient test coverage.

---

# 37. Cross-Stress Summary

When multiple stress tests have been executed, report each category separately.

Recommended structure:

| Stress category | Cases | Success/failure | Inlier behavior | Coverage behavior | RMSE behavior | Runtime behavior |
| --------------- | ----: | --------------- | --------------- | ----------------- | ------------- | ---------------- |
| Easy pair       |     — | —               | —               | —                 | —             | —                |
| Sun-angle       |     — | —               | —               | —                 | —             | —                |
| Scale           |     — | —               | —               | —                 | —             | —                |
| Modality        |     — | —               | —               | —                 | —             | —                |
| Geometry        |     — | —               | —               | —                 | —             | —                |
| Low-feature     |     — | —               | —               | —                 | —             | —                |
| Combined        |     — | —               | —               | —                 | —             | —                |

Empty cells are intentional until execution evidence exists.

---

# 38. What Counts as Evidence of Robustness?

Robustness should be supported by repeated controlled evidence.

Evidence becomes stronger when:

- multiple image pairs are tested;
- the same stress condition is reproduced;
- independent check points are used;
- failures are retained;
- multiple terrain types are represented;
- sensor-specific behavior is reported;
- baseline and stress configurations are clearly controlled;
- results remain consistent across the tested cases.

A single successful difficult image pair is not sufficient to claim general robustness.

---

# 39. What Does Not Count as Robustness Evidence?

The following MUST NOT be treated as sufficient evidence by themselves:

- one successful overlay;
- a high matcher confidence score;
- a high number of candidate matches;
- a high inlier count without spatial analysis;
- one successful stress example;
- synthetic testing alone for a claim about natural lunar conditions;
- upsampling a low-resolution image;
- visual improvement without independent check-point measurements;
- a flexible warp that hides poor control points;
- a placeholder percentage;
- a star rating;
- an undocumented "confidence" score.

The project feedback explicitly recommends removing unmeasured percentage and star ratings and replacing them with real RMSE, inlier ratio, coverage, and runtime measurements.

---

# 40. Stress-Test Acceptance Rules

A stress-test framework is considered properly executed only when:

- the baseline is defined;
- the stress condition is documented;
- the changed variable is identified;
- important fixed variables are identified;
- ground truth is preserved;
- independent check points are used;
- the same evaluation protocol is applied;
- failures are retained;
- quantitative metrics are recorded;
- execution status is documented;
- conclusions are limited to the tested evidence.

No universal numerical pass/fail threshold is defined in this document.

If the repository later defines explicit acceptance thresholds, those thresholds should be incorporated into the benchmark specification and referenced here rather than invented independently.

---

# 41. Recommended Execution Order

The recommended order is:

```text
1. Easy-pair baseline
        ↓
2. Sun-angle stress
        ↓
3. Scale stress
        ↓
4. Modality stress
        ↓
5. Geometry stress
        ↓
6. Low-feature terrain
        ↓
7. Combined stress
```

This order allows individual failure modes to be understood before interactions between multiple stress factors are introduced.

The project build guidance similarly recommends establishing one measurable baseline before progressively adding scale/illumination handling, stronger matching, refinement, and additional sensors.

---

# 42. Stress-Test Development Workflow

A controlled stress experiment should follow:

```text
Define baseline
      ↓
Select benchmark pair
      ↓
Define stress factor
      ↓
Define fixed variables
      ↓
Define ground truth
      ↓
Define independent check points
      ↓
Apply controlled stress
      ↓
Run unchanged evaluation pipeline
      ↓
Collect metrics
      ↓
Preserve artifacts
      ↓
Record failures
      ↓
Compare against baseline
      ↓
Analyze degradation
      ↓
Document limitations
```

---

# 43. Diagnostic Decision Tree

Stress results should be used to identify where degradation occurs.

```text
Performance degraded?
        |
        +---- No
        |      ↓
        |   Continue testing
        |
        +---- Yes
               |
               +---- Candidate matches decreased?
               |          ↓
               |       Matching sensitivity
               |
               +---- Inlier ratio decreased?
               |          ↓
               |       Geometric verification sensitivity
               |
               +---- Coverage decreased?
               |          ↓
               |       Spatial correspondence problem
               |
               +---- RMSE increased?
               |          ↓
               |       Registration accuracy degradation
               |
               +---- Residuals systematic?
               |          ↓
               |       Geometry/model limitation
               |
               +---- Complete failure?
                          ↓
                       Failure analysis
```

This is a diagnostic framework, not a claim about any particular executed ChandraMap result.

---

# 44. Illumination-Specific Diagnostic Path

For Sun-angle stress:

```text
Performance drop
      ↓
Inspect candidate matches
      ↓
Inspect verified inliers
      ↓
Inspect spatial coverage
      ↓
Inspect residual vectors
      ↓
Compare intensity vs structural representations
      ↓
Determine whether degradation is associated with
illumination-sensitive appearance or geometry
```

Brightness normalization should not be assumed to solve shadow-geometry differences.

The project feedback specifically recommends treating illumination handling as an experiment rather than a vague invariance claim.

---

# 45. Scale-Specific Diagnostic Path

For scale stress:

```text
Performance drop
      ↓
Check effective source/reference scales
      ↓
Check reference pyramid/downsampling
      ↓
Check candidate correspondences
      ↓
Check inlier distribution
      ↓
Check independent RMSE
      ↓
Determine whether the failure occurs during
coarse correspondence or final refinement
```

The experiment should evaluate information at comparable physical scales before asking the matcher to perform fine alignment.

---

# 46. Modality-Specific Diagnostic Path

For sensor/modality stress:

```text
Performance drop
      ↓
Verify sensor-specific preprocessing
      ↓
Verify selected 2D representation
      ↓
Check effective scale
      ↓
Check candidate correspondences
      ↓
Check geometric inliers
      ↓
Check spatial coverage
      ↓
Check source-pixel RMSE
```

IIRS should be treated as a distinct experiment when evaluated because its data characteristics differ materially from visible panchromatic imagery.

---

# 47. Geometry-Specific Diagnostic Path

For geometry stress:

```text
Performance drop
      ↓
Inspect inlier distribution
      ↓
Fit initial model
      ↓
Inspect residual vectors
      ↓
Check for systematic spatial residuals
      ↓
Evaluate whether model is adequate
      ↓
Consider geometry/DEM/local refinement
      ↓
Re-evaluate on independent check points
```

A flexible warp should not be introduced merely because the overlay looks visually better.

---

# 48. Low-Feature Diagnostic Path

For low-feature/repetitive terrain:

```text
Low-feature terrain
      ↓
Candidate matches
      ↓
Geometric verification
      ↓
Are inliers spatially distributed?
      |
      +---- No → localized / repetitive correspondence
      |
      +---- Yes
             ↓
        Independent RMSE
             ↓
        Final registration
```

This category is particularly useful for identifying cases where match counts appear healthy but correspondences are not sufficiently distributed.

---

# 49. Sensor-Aware Stress Reporting

Because the project explicitly identifies different sensor characteristics, stress-test reports SHOULD retain sensor identity.

For example:

```text
Source sensor: OHRC
Reference sensor: LRO NAC
Stress: Sun angle
```

should remain distinguishable from:

```text
Source sensor: IIRS
Reference sensor: LRO NAC
Stress: Modality + scale
```

The benchmark should not collapse these into an unexplained single aggregate value.

---

# 50. No False Precision

Stress-test results MUST preserve the precision supported by the underlying measurements.

Do not report:

```text
0.2374913821 px
```

unless the measurement system and data justify that precision.

Likewise, do not report:

```text
92% robust
```

unless "92%" is an explicitly defined and measured benchmark metric.

The project feedback specifically warns against decorative confidence percentages and ratings that have not been measured.

---

# 51. No Unsupported Thresholds

This document intentionally does not define thresholds such as:

- minimum inlier count;
- minimum inlier ratio;
- maximum RMSE;
- minimum spatial coverage;
- maximum runtime;
- maximum degradation percentage.

Such thresholds should only be introduced when justified by:

- project requirements;
- benchmark design;
- validated experimental evidence;
- documented scientific criteria.

Until then, stress tests should report measured values and observed failure behavior.

---

# 52. Relationship to V1 Benchmark Specification

`STRESS_TESTS.md` extends the evaluation framework defined by the V1 benchmark specification.

The relationship is:

```text
BENCHMARK_SPEC.md
        │
        ├── Dataset / pair definition
        ├── Ground truth
        ├── Matching
        ├── Geometric verification
        ├── Transformation
        ├── Registration
        └── Evaluation metrics
                    │
                    ↓
             STRESS_TESTS.md
                    │
                    ├── Illumination stress
                    ├── Scale stress
                    ├── Modality stress
                    ├── Geometry stress
                    ├── Low-feature stress
                    └── Combined stress
```

Stress tests MUST reuse the benchmark's core evaluation definitions rather than silently creating incompatible metrics.

---

# 53. Relationship to V1 Pipeline

The stress-test framework operates after the standard correspondence and registration pipeline is defined.

```text
Input
  ↓
Sensor route
  ↓
Preprocessing
  ↓
Multi-scale representation
  ↓
Local matching
  ↓
Candidate matches
  ↓
RANSAC / geometric verification
  ↓
Verified inliers
  ↓
Sub-pixel refinement
  ↓
Final transform
  ↓
Registration
  ↓
Independent evaluation
  ↓
Stress analysis
```

Not every V1 configuration necessarily contains every optional stage.

The benchmark record MUST identify which stages were actually executed.

---

# 54. Research Interpretation Rules

Stress-test conclusions MUST distinguish between:

### Measured

Directly observed benchmark quantities:

- inlier count;
- inlier ratio;
- spatial coverage;
- check-point RMSE;
- ground error where meaningful;
- runtime;
- failure status.

### Observed

Patterns visible in the experiment:

- systematic residuals;
- clustered inliers;
- increased failures;
- reduced correspondence under changed illumination.

### Inferred

Reasoned explanations for observed behavior.

These should be clearly presented as interpretations rather than direct measurements.

### Unverified

Claims requiring additional experiments.

Examples:

- universal illumination invariance;
- universal scale invariance;
- universal cross-sensor robustness;
- generalization to unseen lunar sensors;
- generalization to all lunar terrain.

---

# 55. Recommended Reporting Language

Use:

> "The stress experiment measured a change in..."

instead of:

> "The system is completely robust to..."

Use:

> "Under the tested illumination conditions..."

instead of:

> "The method is illumination invariant."

Use:

> "The evaluated configuration showed..."

instead of:

> "ChandraMap always..."

Use:

> "No execution result is available for this proposed test."

instead of inventing a result.

---

# 56. Implementation Status Rules

Every stress-test category SHOULD have an explicit status.

Recommended vocabulary:

| Status      | Meaning                                   |
| ----------- | ----------------------------------------- |
| Proposed    | Defined in the benchmark design           |
| Recommended | Recommended for implementation/evaluation |
| Planned     | Explicitly scheduled                      |
| Implemented | Implementation evidence exists            |
| Executed    | Execution evidence exists                 |
| Blocked     | Defined but cannot currently be executed  |
| Deprecated  | No longer part of the benchmark           |

A stress-test category MUST NOT be marked `Implemented` or `Executed` merely because it is described in this file.

---

# 57. Minimum Evidence for an Executed Stress Test

To label a stress test **Executed**, preserve enough evidence to establish:

- test identity;
- baseline identity;
- image pair;
- stress condition;
- configuration;
- metrics;
- evaluation status;
- output artifacts;
- failure information where applicable.

If these artifacts are unavailable, retain the test as `Proposed`, `Recommended`, or another appropriate status.

---

# 58. Final Stress-Test Checklist

Before recording a stress-test result:

### Test Definition

- [ ] Stress category is defined.
- [ ] Baseline is identified.
- [ ] Primary changed variable is identified.
- [ ] Fixed variables are documented.
- [ ] Stress parameters are recorded.

### Data

- [ ] Source image is identified.
- [ ] Reference image is identified.
- [ ] Sensor/product identity is recorded.
- [ ] Relevant GSD/pixel-scale information is recorded.
- [ ] Ground truth is preserved.
- [ ] Natural vs synthetic stress is identified.

### Matching

- [ ] Candidate correspondences are recorded.
- [ ] Matching method is recorded.
- [ ] Preprocessing is recorded.
- [ ] Effective scale is recorded.

### Geometry

- [ ] RANSAC/geometric verification is recorded.
- [ ] Transformation model is recorded.
- [ ] Inlier count is recorded.
- [ ] Inlier ratio is recorded.
- [ ] Spatial coverage is evaluated.
- [ ] Residuals are inspected where relevant.

### Registration

- [ ] Final transformation is recorded.
- [ ] Sub-pixel refinement status is recorded.
- [ ] Independent check points are used.
- [ ] Check-point RMSE is reported where valid.
- [ ] Ground error is reported only when meaningful.

### Failure Handling

- [ ] Failed cases are retained.
- [ ] Failure stage is recorded.
- [ ] Failure reason is recorded where known.
- [ ] Failure rate is calculated only when sufficient execution data exists.

### Reporting

- [ ] Baseline and stress metrics are compared.
- [ ] Raw values are retained.
- [ ] Degradation is quantified where meaningful.
- [ ] No unsupported threshold is used.
- [ ] No robustness claim exceeds the tested evidence.
- [ ] Reproducibility information is preserved.

---

# 59. Core Stress-Test Principle

The purpose of this benchmark is not to make every stress test pass.

The purpose is to determine:

```text
Where does the pipeline remain reliable?
              ↓
Where does performance degrade?
              ↓
Which metric changes first?
              ↓
Where does the pipeline fail?
              ↓
Why does it fail?
              ↓
Which engineering change addresses that failure?
```

A difficult case that fails reproducibly can be more scientifically useful than a carefully selected successful example.

The project feedback explicitly emphasizes:

> Build small. Measure honestly. Keep the failures.

The stress-test framework therefore treats failure analysis as a first-class benchmark output rather than an undesirable result.

---

# 60. Source Basis

This document preserves the stress-testing principles and terminology from the supplied ChandraMap/SIH 26166 materials.

The project feedback identifies the primary stress matrix as:

- easy pair;
- Sun-angle stress;
- scale stress;
- modality stress;
- geometry stress;
- low-feature terrain.

It also identifies inlier count, inlier ratio, spatial coverage, independent check-point RMSE, ground error where meaningful, runtime, and failure rate as relevant evaluation measures.

The supplied technical feedback emphasizes that illumination changes lunar shadow geometry, that scale must be handled using physically meaningful representations, and that IIRS requires sensor-aware 2D representation rather than being treated as an ordinary camera image.

The geometry guidance requires RANSAC-based verification, independent check points, residual inspection, controlled sub-pixel refinement, and caution against using flexible warps to hide poor correspondences.

The supplied SIH problem material identifies Chandrayaan-2 OHRC, TMC-2, and IIRS as main target/source imagery, with LRO NAC as reference/training imagery and synthetic lunar augmentations as a possible source for Sun-angle, rotation, scale, and contrast experiments.

The benchmark framework deliberately does not introduce execution results, robustness percentages, dataset sizes, performance thresholds, implementation claims, or commands that are not established by the project evidence.
