# ADR-0003: Independent Checkpoints for Registration Evaluation

> **Decision summary:** ChandraMap evaluates final registration accuracy using independent checkpoints or authoritative external ground truth whenever suitable evaluation data can be established. Points used to estimate the transformation must not be the sole points used to claim independent registration accuracy.

## Status

| Field                   | Value                                                                               |
| ----------------------- | ----------------------------------------------------------------------------------- |
| **ADR**                 | ADR-0003                                                                            |
| **Title**               | Independent Checkpoints for Registration Evaluation                                 |
| **Status**              | Accepted                                                                            |
| **Date**                | `<YYYY-MM-DD>`                                                                      |
| **Decision Scope**      | V1 and shared benchmark architecture                                                |
| **Architecture Area**   | Registration Evaluation / Benchmarking                                              |
| **Sensor Scope**        | OHRC, TMC-2, IIRS-derived representations, LRO reference imagery                    |
| **Supersedes**          | N/A                                                                                 |
| **Superseded By**       | N/A                                                                                 |
| **Related ADRs**        | [ADR-0001](0001-v1-known-overlap-first.md), [ADR-0002](0002-sift-as-v1-baseline.md) |
| **Related Issues**      | N/A                                                                                 |
| **Related PRs**         | N/A                                                                                 |
| **Related Experiments** | TBD                                                                                 |
| **Related Benchmarks**  | ChandraMap registration benchmark / TBD                                             |

**Accepted** means that ChandraMap has adopted independent-checkpoint evaluation as the preferred architecture for measuring registration quality.

It does **not** mean that:

* authoritative checkpoint datasets already exist for every sensor or pair;
* benchmark RMSE values have already been measured;
* all sensors have completed independent evaluation;
* accuracy thresholds have been finalized;
* a final checkpoint-generation procedure has been standardized.

---

## Context

ChandraMap is a lunar image correspondence and registration system intended to establish reliable relationships between imagery of the same lunar region captured under different sensors, resolutions, Sun angles, viewing geometries, and potentially different modalities.

Primary Chandrayaan-2 source contexts include:

* OHRC — Orbiter High Resolution Camera;
* TMC-2 — Terrain Mapping Camera-2;
* IIRS — Imaging Infrared Spectrometer.

Reference imagery may include LRO NAC and LRO WAC products where scientifically appropriate.

Approximate instrument-scale context differs substantially:

* OHRC is approximately 0.25–0.32 m/pixel depending on product/documentation;
* TMC-2 is approximately 5 m/pixel;
* IIRS is approximately 80 m/pixel and is hyperspectral/imaging-infrared;
* LRO NAC resolution is product-dependent and often substantially finer than TMC-2 or IIRS;
* LRO WAC provides broader-scale lunar reference/context imagery.

Specific product metadata takes precedence over generic instrument summaries.

This sensor diversity has an important consequence for evaluation: the same numerical error in pixels does not represent the same physical ground error across sensors.

ChandraMap's core output is not merely a visually aligned overlay. The project aims to produce defensible correspondence and registration evidence, including:

1. candidate correspondences;
2. geometrically verified inliers;
3. fitting tie/control points;
4. refined fitting points where applicable;
5. a final transformation;
6. a registered product;
7. spatial-support information;
8. independent registration metrics where suitable truth exists;
9. reproducible benchmark evidence.

[ADR-0001](0001-v1-known-overlap-first.md) established the V1 **known-overlap-first** direction. V1 therefore begins with source/reference pairs whose overlap is already known rather than requiring whole-Moon retrieval.

[ADR-0002](0002-sift-as-v1-baseline.md) established SIFT as the V1 local-matching baseline.

The resulting conceptual V1 flow is therefore:

```text
Known Overlapping Source / Reference Pair
        ↓
SIFT Local Feature Extraction
        ↓
Descriptor Matching
        ↓
Candidate Correspondences
        ↓
Match Filtering
        ↓
Geometric Verification
        ↓
Verified Inliers
        ↓
Transformation Estimation
        ↓
Registration
        ↓
Evaluation
```

ADR-0003 defines what **evaluation** means in this architecture.

The central problem is that the transformation is estimated from correspondence observations. If the project measures its primary registration accuracy only on those same observations, the metric primarily describes how well the chosen model fits its fitting data. It does not provide a fully independent measurement of how well that transformation aligns locations that were not used to estimate it.

---

## Problem

Suppose an affine transform, homography, or another registration model is estimated from a collection of verified correspondences.

A process such as:

```text
Points A, B, C, D
        ↓
Estimate Transformation
        ↓
Measure Error on A, B, C, D
```

answers:

> How closely does the fitted transformation explain the observations that contributed to its estimation?

It does not necessarily answer:

> How accurately does the fitted transformation register previously unused locations across the overlap?

The distinction matters because model-fitting residuals can be optimistic relative to performance away from the fitting observations.

The problem becomes more serious when:

* fitting points are spatially clustered;
* the transform model is flexible;
* the same points influence RANSAC/model selection and final evaluation;
* parameters are repeatedly tuned against the same evaluation data;
* high-error evaluation points are removed after results are inspected;
* visual overlays are treated as quantitative validation.

Without a deliberate separation between transformation fitting and final evaluation, benchmark results can overstate registration quality and make later method comparisons difficult to defend.

ADR-0003 therefore establishes a permanent architectural separation between **fit/control information** and **independent evaluation information** wherever suitable checkpoints or external truth can be established.

---

## Decision Drivers

The decision is driven by:

* scientific validity;
* honest registration-accuracy reporting;
* separation between fitting quality and generalization quality;
* reproducibility;
* benchmark credibility;
* fair comparison between matching methods;
* fair comparison between scientific versions;
* resistance to evaluation leakage;
* meaningful sub-pixel claims;
* spatially interpretable registration error;
* future publication-quality experimentation;
* preservation of negative and high-error evidence;
* compatibility with multiple ChandraMap sensors and future registration methods.

---

## Terminology

### Candidate Match

A **candidate match** is a proposed source/reference correspondence produced by a local matching method.

Candidate correspondences may originate from methods such as:

* SIFT;
* ALIKED + LightGlue;
* LoFTR;
* future local or multimodal matchers.

A candidate match is not automatically geometrically correct.

---

### Verified Inlier

A **verified inlier** is a candidate correspondence accepted as consistent with a geometric model under the project's verification procedure, commonly using RANSAC or another robust-estimation approach.

A verified inlier is:

* model-consistent;
* potentially suitable for transformation fitting;

but it is not automatically:

* independent ground truth;
* an independent checkpoint;
* proof that the correspondence is physically correct in every relevant sense.

> **RANSAC inliers are model-consistent observations, not independent truth.**

---

### Tie / Control Point

Within ADR-0003, a **tie point** or **control point** means a source/reference correspondence selected to contribute to transformation estimation or refinement.

These are **fitting points**.

Where another geospatial or photogrammetric context uses *control point* to mean an externally surveyed or independently known point, that stricter role must be documented explicitly rather than inferred from the name.

---

### Independent Checkpoint

An **independent checkpoint** is a source/reference point correspondence reserved for evaluating the final transformation.

Its evaluation location must not contribute to estimation of the final transform.

Where strong independence is claimed, the checkpoint must also remain outside model-selection, threshold-tuning, and other decisions that would leak its final error back into fitting.

---

### Ground Truth

**Ground truth** is externally established information considered reliable enough for the specific evaluation purpose.

Potential sources may include:

* official challenge-provided annotations;
* independently validated correspondence data;
* trusted geospatial control information;
* manually validated points;
* another scientifically justified source.

ADR-0003 does not assert that authoritative ground truth already exists for all ChandraMap pairs.

LRO reference imagery is not automatically ground truth merely because it is used as the reference image.

---

### Residual

A **residual** is the difference between a location predicted by the estimated registration model and the corresponding expected location.

The coordinate system and units of every residual metric must be explicit.

---

### Fit Residual

A **fit residual** measures error on observations that contributed to transformation estimation.

Fit residuals are useful diagnostics.

They are not independent registration accuracy.

---

### Checkpoint Residual

A **checkpoint residual** measures the final transformation against a point reserved from fitting.

Checkpoint residuals provide stronger evidence about registration performance away from the fitting observations.

---

### Checkpoint RMSE

**Checkpoint RMSE** is Root Mean Square Error computed over independent checkpoint residuals in a clearly defined coordinate system and unit.

---

## Control Points vs Checkpoints

| Point Type                                | Used to Fit Transform? | Used for Independent Final Accuracy Evaluation? |
| ----------------------------------------- | ---------------------: | ----------------------------------------------: |
| Candidate match                           |                     No |                                              No |
| RANSAC / verified inlier                  |            Potentially |                               Not automatically |
| Tie/control point                         |                    Yes |                                              No |
| Independent checkpoint                    |                     No |                                             Yes |
| External authoritative ground truth point |          No by default |                                             Yes |

Ground-truth points may be deliberately used for transformation fitting in some research designs. If that occurs, those same points are no longer independent evaluation points for that transform.

The role of a point must therefore be determined by how it is used, not merely by its filename or label.

---

## Why Fitting and Evaluation Must Be Separated

Transformation estimation is a parameter-fitting process.

Using the same observations for both parameter estimation and the sole final evaluation creates evaluation leakage.

The principle is analogous to the broader separation:

```text
Fitting Data
        ≠
Independent Evaluation Data
```

This analogy does not imply that geometric transformation estimation is the same as training a neural network.

The relevant principle is narrower:

> **Data used to estimate parameters should not be the only data used to claim independent accuracy.**

Consider:

```text
Verified Correspondences
├── Point A
├── Point B
├── Point C
├── Point D
└── Point E

Fit Transform Using:
A, B, C, D

Evaluate Independently Using:
E
```

This illustrates the distinction but does not imply that one checkpoint is sufficient for a real benchmark.

A meaningful benchmark should use enough scientifically valid and spatially useful checkpoints to support the claims it makes. ADR-0003 deliberately does not define that count.

---

## Considered Options

### Option A — Evaluate on Fitting Points Only

**Description**

Estimate the transformation from verified fitting correspondences and calculate final error on those same correspondences.

**Advantages**

* simple to implement;
* does not require a separate checkpoint set;
* useful for transformation-fit diagnostics;
* useful for inspecting RANSAC/model behavior;
* useful for comparing how different transform models fit the same control observations.

**Disadvantages / Trade-offs**

* introduces optimistic-evaluation risk;
* does not independently test transformation generalization;
* confuses model-fitting quality with registration quality;
* can hide weak spatial support;
* can make flexible transformations appear stronger than they generalize;
* provides weak evidence for publication-quality accuracy claims.

**Assessment**

Retained as a **diagnostic metric**, but rejected as the sole architecture for final registration accuracy.

---

### Option B — Independent Checkpoints

**Description**

Use one set of points for transformation fitting and a separate set of independently reserved points for final evaluation.

**Advantages**

* separates estimation from evaluation;
* provides stronger registration evidence;
* helps reveal spatial generalization error;
* reduces optimistic fitting bias;
* enables fairer matcher and version comparisons;
* strengthens benchmark credibility;
* provides a more defensible basis for sub-pixel accuracy claims;
* exposes cases where low fit residual does not generalize across the overlap.

**Trade-offs**

* requires additional checkpoint preparation;
* reduces the observations available for fitting when points are split from a limited set;
* checkpoint quality must itself be validated;
* manual annotation can be time-consuming;
* poor checkpoint placement can still bias evaluation;
* small datasets may make strict separation difficult.

**Assessment**

Selected as the default evaluation architecture.

---

### Option C — Visual Evaluation Only

**Description**

Judge registration primarily through overlays or other visual inspection.

**Advantages**

* easy to inspect;
* useful for debugging;
* useful for qualitative demonstrations;
* useful for detecting obvious gross failures.

**Disadvantages / Trade-offs**

* subjective;
* difficult to reproduce;
* insensitive to some local or edge-region errors;
* display scale can hide quantitative misregistration;
* flexible warping can produce visually attractive but scientifically weak results;
* unsuitable as the primary benchmark metric.

**Assessment**

Retained as supporting qualitative evidence only.

---

### Option D — External Authoritative Ground Truth

**Description**

Evaluate the final transformation against independently established authoritative truth when a suitable source exists.

**Advantages**

* strongest independence from ChandraMap's own matching process;
* highly suitable for benchmark evaluation;
* avoids relying on the same correspondence-generation process for both fitting and truth;
* supports more credible method comparison.

**Trade-offs**

* may not exist for every image pair;
* projection and coordinate compatibility must be verified;
* authoritative products can still have uncertainty;
* annotation or truth provenance must remain documented.

**Assessment**

Preferred over internally held-out matcher correspondences whenever suitable authoritative ground truth exists.

---

## Option Comparison

| Criterion                        | Option A — Fitting Points       | Option B — Independent Checkpoints | Option C — Visual Only | Option D — External Ground Truth  |
| -------------------------------- | ------------------------------- | ---------------------------------- | ---------------------- | --------------------------------- |
| Independent accuracy evidence    | Weak                            | Stronger                           | Weak                   | Strongest when valid              |
| Implementation simplicity        | High                            | Moderate                           | High                   | Depends on truth availability     |
| Reproducibility                  | Good if recorded                | Good if provenance is preserved    | Limited                | Good if truth is versioned        |
| Spatial error detection          | Limited by fitting distribution | Stronger                           | Subjective             | Strong when spatially distributed |
| Benchmark credibility            | Limited                         | High                               | Low                    | High                              |
| Ground-truth dependency          | None                            | Requires checkpoint preparation    | None                   | Requires external truth           |
| Suitable as primary final metric | No                              | Yes                                | No                     | Yes                               |
| Diagnostic usefulness            | High                            | High                               | High qualitatively     | High                              |

---

## Decision

ChandraMap adopts **Option B — Independent Checkpoints** as the standard registration-evaluation architecture.

**Option D — External Authoritative Ground Truth** is preferred whenever suitable, scientifically defensible external truth is available.

The decision establishes the following rules:

1. Final registration accuracy should use independent checkpoints whenever suitable checkpoints can be established.
2. Points used to fit the final transformation must not simultaneously be presented as independent evaluation points.
3. Fit residuals and independent checkpoint residuals must be labeled separately.
4. Checkpoint RMSE should be a major final-registration metric when suitable independent checkpoints exist.
5. Authoritative external truth is preferred over internally held-out matcher-generated correspondences.
6. Held-out matcher correspondences may be used as a fallback evaluation source, but their weaker independence must be documented.
7. Checkpoints should be spatially meaningful and reasonably distributed over the valid overlap.
8. Checkpoint provenance must be preserved.
9. Source-image pixel error is the primary coordinate unit for sub-pixel claims.
10. Conversion to ground distance must occur only when the required geospatial context makes that conversion scientifically valid.
11. Evaluation methodology must remain reproducible.
12. Checkpoint-generation and exclusion procedures must be stated in benchmark results.
13. Visual overlays remain supporting artifacts and do not replace independent quantitative evaluation.
14. Negative and high-error checkpoint results remain valid scientific evidence.

---

## What This ADR Does Not Decide

ADR-0003 intentionally does not define:

* exact checkpoint count;
* exact control/checkpoint split percentage;
* exact checkpoint-density requirement;
* exact checkpoint-coverage threshold;
* exact automatic splitting algorithm;
* exact annotation workflow;
* exact annotation software;
* final checkpoint file schema;
* final benchmark dataset;
* exact RMSE acceptance threshold;
* exact sensor-specific accuracy threshold;
* final transformation model;
* final outlier threshold;
* final geospatial-error threshold;
* final sub-pixel refinement algorithm;
* final local/piecewise warp model;
* final V2/V3/V4 benchmark protocol.

These require separate specifications, experiments, or future architectural decisions.

---

## Rationale

Independent checkpoints provide a clearer separation between:

```text
How well does the model fit its fitting observations?
```

and:

```text
How well does the fitted transformation register locations that were not used
to estimate it?
```

That distinction is essential for ChandraMap because later scientific versions are intended to be benchmarked against earlier methods.

If all methods are judged primarily on their own fitting observations, a method can appear strong simply because:

* it fitted a flexible model;
* it retained an easy subset of correspondences;
* its inliers clustered in one favorable region;
* its fitting process minimized those exact residuals.

Independent checkpoints reduce this ambiguity.

The decision also establishes a stable benchmark concept that is independent of the local matcher. SIFT, a later learned matcher, or another registration approach can all be evaluated against the same external checkpoint definition where scientifically valid.

This makes ADR-0003 part of ChandraMap's **evaluation contract**, not a SIFT-specific rule.

---

## Evaluation Priority

ChandraMap uses the following evaluation priority.

### Priority 1 — Official or Authoritative External Ground Truth

Use official or externally validated evaluation truth when:

* its provenance is known;
* coordinate semantics are understood;
* its uncertainty is acceptable for the intended claim;
* it is compatible with the source/reference products.

This is the preferred evaluation source.

---

### Priority 2 — Independently Verified Checkpoints

Use manually or independently established point correspondences that:

* are not used for transformation fitting;
* have documented provenance;
* are accurately localized;
* represent valid corresponding lunar features;
* are sufficiently distributed for the intended evaluation.

---

### Priority 3 — Held-Out Matcher-Derived Correspondences

When stronger truth does not exist, a subset of reliable correspondences may be reserved for evaluation.

This is weaker than independent external truth because the evaluation points still originate from the same correspondence-generation process.

The benchmark must state this limitation explicitly.

Where practical, the evaluation subset should be reserved before model-selection or parameter-tuning decisions that could leak evaluation information back into the fitted method.

If held-out points were themselves selected using a model estimated from the full correspondence set, they must not be described as fully independent authoritative checkpoints.

---

### Priority 4 — Fit Residuals

Fit residuals remain valid diagnostic evidence.

They can help evaluate:

* transformation fit;
* RANSAC behavior;
* model mismatch;
* systematic residual patterns;
* possible geometric insufficiency.

They must not be mislabeled as independent final registration accuracy.

---

## Evaluation Architecture

The preferred architecture is:

```text
Candidate Correspondences
        ↓
Geometric Verification
        ↓
Verified Correspondences
        │
        ├───────────────────────────────┐
        │                               │
        ▼                               ▼
Fit / Tie-Point Set              Evaluation Source
        │                         (independent whenever possible)
        ▼                               │
Initial Transform                       │
        │                               │
Optional Tie-Point Refinement           │
        │                               │
Final Transform Refit                   │
        │                               │
        └──────────────┬────────────────┘
                       ▼
              Independent Evaluation
                       ↓
              Checkpoint Residuals
                       ↓
                Checkpoint Metrics
```

When external truth exists, the preferred structure is stronger:

```text
ChandraMap Correspondence Pipeline
        ↓
Verified Fitting Tie Points
        ↓
Fit / Refine / Refit Transform
        │
        │
        └───────────────────┐
                            ▼
                    Final Transformation
                            │
External Independent        │
Ground Truth / Checkpoints ─┘
        ↓
Evaluate Predicted vs Expected Locations
        ↓
Independent Registration Error
```

The external evaluation branch does not depend on ChandraMap's matcher for its definition.

---

## Benchmark Architecture

For each image pair:

```text
Source / Reference Pair
        │
        ├── Fitting Tie / Control Points
        │         ↓
        │   Estimate Transformation
        │
        └── Independent Checkpoints
                  ↓
            Evaluate Transformation
```

For multiple methods:

```text
Same Source / Reference Pair
        │
        ├── Method A
        │      ↓
        │   Transform A
        │
        ├── Method B
        │      ↓
        │   Transform B
        │
        └── Method C
               ↓
           Transform C

Same Independent Checkpoint Definition
        ↓
Evaluate Transform A
Evaluate Transform B
Evaluate Transform C
        ↓
Comparable Registration Evidence
```

Where scientifically valid, competing methods should use the same checkpoint set.

If different checkpoint sets are unavoidable, the benchmark must state that direct metric comparison is limited.

---

## Fit Metrics vs Independent Metrics

Fit metrics and checkpoint metrics answer different questions.

| Metric Class             | Population                                   | Primary Interpretation                                  |
| ------------------------ | -------------------------------------------- | ------------------------------------------------------- |
| Fit residual             | Points used during transformation estimation | How closely the model explains its fitting observations |
| Fit RMSE                 | Transformation-fitting points                | Aggregate model-fit error                               |
| Checkpoint residual      | Points excluded from fitting                 | Error at independently evaluated locations              |
| Checkpoint RMSE          | Independent checkpoints                      | Aggregate independent registration error                |
| Spatial residual pattern | Fit or checkpoint points, clearly labeled    | Whether error varies systematically over the image      |

The names **Fit RMSE** and **Independent Checkpoint RMSE** must not be used interchangeably.

---

## Independent Checkpoint RMSE

For \(N\) independent checkpoints, let the evaluation residual magnitude for checkpoint \(i\) in the declared metric coordinate system be:

$$
e_i = \left\lVert \hat{\mathbf{p}}_i - \mathbf{p}_i \right\rVert_2
$$

where:

* \(\hat{\mathbf{p}}_i\) is the location predicted by the final transformation;
* \(\mathbf{p}_i\) is the independently established expected location.

Checkpoint RMSE is:

$$
\mathrm{RMSE}_{\mathrm{checkpoint}}
=
\sqrt{
\frac{1}{N}
\sum_{i=1}^{N}
e_i^2
}
$$

Every reported RMSE must identify:

* the evaluated point population;
* coordinate space;
* unit;
* checkpoint source;
* number of usable checkpoints;
* handling of excluded or invalid checkpoints.

No numerical RMSE value is defined by this ADR.

---

## RMSE Limitations

RMSE is retained as a core registration metric, but it is not a complete description of registration quality.

Because squaring residuals gives larger errors greater influence, RMSE can be sensitive to large-error checkpoints.

Where useful, ChandraMap may also preserve supporting metrics such as:

* mean checkpoint error;
* median checkpoint error;
* maximum checkpoint error;
* percentile error where justified;
* individual residual vectors;
* spatial residual distribution;
* valid/evaluated checkpoint count.

ADR-0003 does not require every supporting metric for every experiment.

Raw or sufficiently detailed checkpoint residual data should be preserved where practical so that aggregate values can be interpreted correctly.

---

## Source-Pixel Error

> **Registration and sub-pixel accuracy should be reported in source-image pixels first when the evaluation is defined in source space.**

This is necessary because the same fractional pixel error has different physical meaning for different sensors.

Conceptually:

```text
Checkpoint Error
        ↓
Interpret in Explicit Image Coordinate System
        ↓
Report Source-Image Pixel Error
        ↓
Convert to Ground Distance Only When Scientifically Valid
```

For a source checkpoint \(\mathbf{s}_i\), reference checkpoint \(\mathbf{r}_i\), and an invertible final source-to-reference transform \(T\), a source-space evaluation may be defined as:

$$
\hat{\mathbf{s}}_i = T^{-1}(\mathbf{r}_i)
$$

$$
e^{(\mathrm{source})}_i
=
\left\lVert
\hat{\mathbf{s}}_i - \mathbf{s}_i
\right\rVert_2
$$

The benchmark specification must state the exact evaluation direction used.

If a transformation cannot be meaningfully inverted into the source coordinate system, the metric must instead name its actual coordinate space explicitly. A reference-space residual must not be labeled as source pixels.

---

## Sensor-Aware Interpretation

### OHRC

A fractional OHRC source-pixel residual represents a substantially smaller ground distance than the same fractional pixel value on coarse imagery.

A low OHRC pixel error still requires independent checkpoint evidence before it can support a registration-accuracy claim.

---

### TMC-2

TMC-2 pixel errors must be interpreted in the TMC-2 source coordinate system and with the metadata of the specific product.

They must not inherit OHRC or NAC ground-scale assumptions.

---

### IIRS

IIRS is a coarse-resolution hyperspectral/imaging-infrared sensor.

A sub-pixel result on an approximately 80 m/pixel source product must not be described as sub-metre registration merely because the numerical value is below one pixel.

Its physical information content remains much coarser.

Evaluation must also state which IIRS-derived 2D representation was registered.

---

### LRO Reference Imagery

The spatial resolution, projection, and processing state of the LRO reference product affect interpretation of cross-sensor residuals.

Reference imagery does not automatically constitute independent truth.

---

## Ground Error in Metres

Ground-distance error may be reported only when the conversion is scientifically meaningful.

Relevant requirements may include:

* valid product-specific GSD;
* known coordinate/projection semantics;
* reliable map geometry;
* appropriate reference truth;
* correct source/reference transformation direction;
* a documented conversion procedure.

Do not assume:

```text
Ground Error = Pixel Error × Nominal Instrument Resolution
```

is universally valid.

Nominal instrument resolution may not match the local scale of a specific processed product.

If metre conversion is reported, the benchmark or result record must document how the conversion was performed.

---

## Sub-Pixel Accuracy Claims

A statement such as:

> The system achieves sub-pixel accuracy.

is incomplete unless it specifies:

* which coordinate system;
* which sensor;
* which scientific version;
* which checkpoint source;
* which dataset or benchmark population;
* which metric;
* how the transform was fitted;
* whether the evaluation points were independent.

A defensible result record should conceptually contain:

| Field                   | Value                       |
| ----------------------- | --------------------------- |
| **Metric**              | Independent Checkpoint RMSE |
| **Unit**                | Source-image pixels         |
| **Sensor**              | `<sensor>`                  |
| **Dataset / Benchmark** | `<dataset>`                 |
| **Checkpoint Source**   | `<checkpoint source>`       |
| **Checkpoint Count**    | `<count>`                   |
| **Result**              | `<value>`                   |

ADR-0003 defines no result values.

---

## Checkpoint Selection Principles

Independent checkpoints should ideally be:

* excluded from final transformation fitting;
* accurately localized;
* unambiguously associated with the same physical lunar feature;
* distributed across the usable overlap;
* representative of the evaluated region;
* documented;
* reproducible;
* traceable to their source;
* unaffected by cherry-picking based on their final residual.

Checkpoint selection should be defined by the benchmark/evaluation protocol rather than improvised after method results are visible.

---

## Spatial Distribution

Independent checkpoints should not all occupy one small part of the overlap.

Poor distribution:

```text
+-------------------------+
|                         |
|    X X X                |
|    X X                  |
|                         |
|                         |
+-------------------------+
```

More informative distribution:

```text
+-------------------------+
| X                   X   |
|        X                |
|              X          |
|   X                 X   |
+-------------------------+
```

The second arrangement can provide stronger evidence about transformation performance across the overlap.

ADR-0003 does not establish a numeric checkpoint-coverage threshold.

---

## Inlier Coverage vs Checkpoint Coverage

Two distinct spatial concepts must remain separate.

### Inlier Coverage

Describes how fitting correspondences are distributed across the overlap.

It helps assess whether the transformation is being estimated from spatially representative support.

### Checkpoint Coverage

Describes how independent evaluation points are distributed across the overlap.

It helps assess whether reported evaluation error represents more than one local region.

These measures may be related, but they must not be merged into a single quantity without an explicit metric definition.

---

## Checkpoint Cherry-Picking

Checkpoints must not be selected or removed solely after observing their final registration residual in order to improve reported results.

An invalid process would be:

```text
Create Checkpoint Set
        ↓
Run Registration
        ↓
Inspect Errors
        ↓
Remove High-Error Checkpoints
        ↓
Report Only Remaining Low-Error Points
```

unless removal follows a pre-established and scientifically justified quality-control rule.

Potentially legitimate exclusion causes may include:

* ambiguous physical feature identity;
* documented annotation error;
* checkpoint outside the valid overlap;
* corrupted source or reference data.

The actual exclusion rules must be defined by the benchmark/evaluation protocol rather than invented per result.

Excluded points and their reasons should remain traceable where practical.

---

## RANSAC and Checkpoint Independence

RANSAC serves geometric verification and robust model estimation.

The typical fitting branch is:

```text
Candidate Matches
        ↓
RANSAC / Robust Geometric Verification
        ↓
Verified Inliers
        ↓
Select Fitting Tie Points
        ↓
Estimate Transformation
```

A RANSAC inlier is not automatically an independent checkpoint.

If a purported checkpoint participated in RANSAC estimation, model selection, or final fitting, it is not independent of that process.

For authoritative externally defined checkpoints, the checkpoint data should remain outside the matching and geometric-fitting branch.

For fallback matcher-derived holdouts, the evaluation protocol must describe when the holdout occurs and acknowledge any remaining dependence on the correspondence-generation procedure.

---

## Sub-Pixel Refinement and Checkpoints

When fitting-point refinement is used, the required conceptual order is:

```text
Candidate Matches
        ↓
RANSAC / Geometric Verification
        ↓
Verified Inliers
        ↓
Select Fit Tie Points
        ↓
Refine Fit Tie-Point Coordinates
        ↓
Refit Final Transformation
        ↓
Apply Final Transformation to Independent Checkpoints
        ↓
Measure Independent Error
```

Two requirements follow.

First, the final transform must correspond to the refined fitting coordinates. Returning the stale pre-refinement transformation would make the refinement scientifically ineffective at the transformation level.

Second, checkpoint independence must be preserved.

If checkpoint locations themselves require localization refinement, that procedure must be documented independently. The final fitted transformation must not be used to force checkpoint coordinates into agreement with the expected result.

---

## Evaluation Leakage Safeguards

Common leakage modes include the following.

### Leakage 1 — Checkpoints Participate in Fitting

Checkpoint coordinates are included in RANSAC, final transformation fitting, or later model refitting.

**Consequence:** the points are no longer independent evaluation data.

---

### Leakage 2 — Model Choice Uses the Final Checkpoints

Several transform models are compared directly on the same checkpoint set, the best one is selected, and the same checkpoint results are then presented as untouched final-test evidence.

**Consequence:** the checkpoint set has become model-selection data.

---

### Leakage 3 — Repeated Parameter Tuning Against Checkpoints

Thresholds are repeatedly modified until the reported checkpoint metric improves.

**Consequence:** the checkpoint set effectively becomes development data.

---

### Leakage 4 — Residual-Based Checkpoint Removal

High-error points are removed after results are seen without an independent quality reason.

**Consequence:** benchmark error becomes optimistically biased.

Small scientific datasets may make perfect development/test separation difficult. When that happens, the limitation must be reported rather than hidden.

---

## Visual Overlays

Visualizations remain useful ChandraMap artifacts.

Possible examples include:

* source/reference alpha blends;
* checkerboard comparisons;
* edge overlays;
* before/after registration views;
* checkpoint residual arrows.

However:

> **Visual agreement is supporting evidence, not the primary accuracy metric.**

A visually convincing registration can still contain significant local or spatially systematic errors.

Flexible local warping can further improve appearance without proving that the underlying correspondence or transformation is scientifically correct.

---

## Spatial Warping Caution

If future ChandraMap versions introduce local or piecewise warps:

* independent checkpoints remain required for strong evaluation;
* evaluation checkpoints must remain outside the warp-fitting process;
* visual improvement alone is insufficient;
* local residual behavior should be evaluated;
* checkpoint coordinate provenance must remain unchanged.

ADR-0003 does not select a future local-warp architecture.

---

## Benchmark Contract

ADR-0003 establishes the following benchmark contract for registration evaluation.

### Fitting Population

The benchmark must identify which points contribute to:

* initial transformation fitting;
* geometric verification where relevant;
* refinement;
* final refit.

### Evaluation Population

The benchmark must identify:

* checkpoint origin;
* checkpoint coordinates;
* checkpoint role;
* whether checkpoints are external truth or held-out matcher-derived points;
* which checkpoints are valid/excluded;
* exclusion reasons where applicable.

### Metric Semantics

Each registration metric must identify:

* point population;
* coordinate system;
* unit;
* aggregation;
* availability.

### Method Comparison

Competing methods should use the same independent checkpoint definition where scientifically valid.

A method must not receive an easier evaluation population merely because it produces different fitting correspondences.

### Failure Retention

Methods that fail before a valid transform or checkpoint metric can be produced must retain that failure state.

An unavailable checkpoint metric must not be encoded as zero.

---

## Benchmark Comparability

Changing the checkpoint set can change benchmark meaning.

Historical results may no longer be directly comparable when the benchmark changes:

* checkpoint coordinates;
* checkpoint source;
* annotation method;
* inclusion/exclusion policy;
* coordinate convention;
* unit;
* truth version;
* spatial distribution.

Checkpoint changes should therefore be versioned or documented through the benchmark/evaluation governance.

Do not silently add easier points, remove difficult points, or adjust coordinates while treating the benchmark as unchanged.

---

## Checkpoint Provenance

Every benchmark should preserve enough information to identify how evaluation points were created and used.

Where applicable, provenance should include:

* checkpoint identifier;
* source image/product identifier;
* reference image/product identifier;
* pair identifier;
* sensor;
* source coordinates;
* reference coordinates;
* coordinate-space definitions;
* annotation origin;
* validation status;
* manual/automatic origin;
* refinement status;
* uncertainty where available;
* exclusion status;
* exclusion reason;
* benchmark/truth version.

ADR-0003 does not define the final serialized checkpoint schema.

The architectural requirement is that this provenance must be recoverable.

---

## Checkpoint Uncertainty

Checkpoints themselves are measurements and may contain uncertainty.

Potential sources include:

* manual localization precision;
* sensor resolution;
* ambiguous feature identity;
* projection error;
* reference-product uncertainty;
* map-registration uncertainty;
* transformation between coordinate systems.

Checkpoint error must not be presented as absolute truth beyond the reliability of the underlying checkpoint source.

Where uncertainty estimates exist, they should be preserved and considered in interpretation.

ADR-0003 does not define a final uncertainty-propagation model.

---

## Reproducibility Requirements

A benchmark evaluation should preserve enough information to reproduce its registration metric.

Where applicable, this includes:

* scientific version;
* source product identifier;
* reference product identifier;
* sensor;
* source/reference dimensions;
* source/reference coordinate spaces;
* product-specific GSD where relevant;
* projection metadata where relevant;
* transformation model;
* transformation direction;
* fitting-point identifiers;
* checkpoint identifiers;
* checkpoint source;
* checkpoint coordinates;
* checkpoint uncertainty where available;
* refinement configuration;
* evaluation direction;
* metric definition;
* metric unit;
* checkpoint exclusions and reasons;
* software/code revision;
* experiment configuration;
* random seed where relevant;
* benchmark/truth version;
* output metrics.

A metric value without sufficient provenance should not be treated as a fully reproducible scientific result.

---

## Benchmark Result Format

A benchmark report may use a structure such as:

| Method               | Sensor     | Fit Points | Checkpoints |  Fit RMSE | Checkpoint RMSE | Unit      |
| -------------------- | ---------- | ---------: | ----------: | --------: | --------------: | --------- |
| SIFT baseline        | `<sensor>` |  `<count>` |   `<count>` | `<value>` |       `<value>` | source px |
| `<candidate method>` | `<sensor>` |  `<count>` |   `<count>` | `<value>` |       `<value>` | source px |

The table is illustrative.

It does not define current benchmark values or checkpoint counts.

Two requirements are permanent:

1. `Fit RMSE` and `Checkpoint RMSE` must remain separate.
2. The metric unit and coordinate space must be clear.

---

## Fit / Checkpoint Splitting

When no external truth exists and a verified correspondence population must be divided into fitting and evaluation subsets, the split procedure must be documented.

Relevant considerations include:

* preserving useful spatial distribution;
* preventing accidental duplicate points across sets;
* preventing evaluation points from being reintroduced during final fitting;
* making the split reproducible;
* recording random seeds when random selection is used;
* documenting whether the same matcher generated both populations.

ADR-0003 deliberately does not define a universal split ratio.

A rule such as `80/20` must not be treated as a ChandraMap standard unless a benchmark specification later justifies it.

---

## Fair Method Comparison

For a fixed benchmark pair:

```text
Method A
Method B
Method C
```

should, where scientifically valid, all be evaluated against:

```text
The Same Independent Checkpoint Set
```

This prevents the evaluation population from changing with the method being evaluated.

If a method cannot be evaluated using the common checkpoint definition, that compatibility limitation must be recorded.

---

## Residual Vector Analysis

Checkpoint evaluation should preserve more than one aggregate number where practical.

For each checkpoint:

```text
Expected Reference / Source Location
        ↓
Compare With
        ↓
Predicted Location
        ↓
Residual Vector
```

Useful residual information may include:

* horizontal component;
* vertical component;
* magnitude;
* image location;
* checkpoint identifier;
* pair identifier;
* sensor;
* metric coordinate space.

A residual-vector field may reveal patterns hidden by RMSE, including:

* systematic direction;
* increasing error toward image edges;
* local distortion;
* inadequacy of a global transform;
* projection mismatch.

ADR-0003 does not mandate a visualization library.

---

## Failure Cases

Independent checkpoint evaluation should expose weak registrations rather than hide them.

Potential patterns include:

* low fit RMSE but high checkpoint RMSE;
* strong alignment near control points but poor alignment elsewhere;
* systematic residual direction;
* increasing residual magnitude across the image;
* sensor-specific error behavior;
* larger residuals over high-relief terrain.

Such observations may motivate hypotheses involving:

* insufficient fitting-point distribution;
* transformation-model mismatch;
* terrain relief;
* projection inconsistency;
* viewpoint effects;
* reference uncertainty.

ADR-0003 does not assert that any of these failure patterns have already been observed in ChandraMap.

---

## Testing and Validation Plan

### Stage 1 — Synthetic Sanity Test

Create controlled synthetic geometry where the expected transformation and checkpoint behavior are known.

Purpose:

* verify residual calculations;
* verify coordinate direction;
* verify RMSE implementation;
* verify that fitting points and checkpoint points remain separate;
* test known perturbations.

A synthetic sanity test validates evaluator correctness. It does not establish real lunar registration robustness.

---

### Stage 2 — One Real Known-Overlap Pair

For a suitable real lunar pair, establish:

```text
Fitting Tie Points
+
Independent Checkpoints
```

and run the full evaluation path:

```text
Fit Transform
→ Final Transform
→ Independent Checkpoint Evaluation
→ Report Fit and Checkpoint Metrics Separately
```

No result value is defined by this ADR.

---

### Stage 3 — Multiple Lunar Pairs

Evaluate the architecture across multiple suitable known-overlap lunar pairs.

The purpose is to ensure that checkpoint handling is not accidentally dependent on one pair.

---

### Stage 4 — Compare Fit and Checkpoint Error

Retain both metric classes and examine whether they tell different stories.

The purpose is not to guarantee that checkpoint RMSE is always larger.

The purpose is to maintain the distinction between fitting performance and independent evaluation performance.

---

### Stage 5 — Compare Matching Methods

Use the same valid checkpoint definitions to compare:

* V1 SIFT baseline;
* later candidate methods when those methods are formally evaluated.

This stage validates benchmark comparability across matching approaches.

---

### Stage 6 — Sensor-Aware Evaluation

Where suitable truth exists, evaluate sensor paths separately for:

* OHRC;
* TMC-2;
* IIRS-derived registration representations.

Do not collapse their pixel metrics into one physical interpretation.

---

## Acceptance Criteria

ADR-0003 is considered implemented when the relevant evaluation pipeline satisfies the following architectural requirements:

* [ ] Fitting points are distinguishable from independent checkpoints
* [ ] Independent checkpoints are excluded from final transformation fitting
* [ ] Checkpoint provenance is retained
* [ ] Fit residuals and checkpoint residuals are reported separately
* [ ] Fit RMSE is not labeled as independent registration accuracy
* [ ] Checkpoint error records an explicit coordinate system
* [ ] Source-image pixel error is available for applicable sub-pixel claims
* [ ] Metre conversion, when present, documents its geospatial assumptions
* [ ] Benchmark reports identify how checkpoints were generated or obtained
* [ ] Evaluation exclusions have documented reasons
* [ ] Missing checkpoint metrics remain unavailable rather than zero
* [ ] Visual overlays do not replace quantitative registration evaluation
* [ ] Benchmark failures remain visible
* [ ] Evaluation configuration can be reconstructed sufficiently for reproducibility

This ADR defines no numerical pass/fail accuracy threshold.

Such thresholds require benchmark evidence or separate project requirements.

---

## Consequences

### Positive Consequences

Adopting independent-checkpoint evaluation provides:

* more credible registration metrics;
* reduced optimistic evaluation bias;
* a clear separation between fitting and validation;
* stronger method comparisons;
* stronger scientific-version comparisons;
* improved benchmark reproducibility;
* better detection of spatially varying error;
* more defensible sub-pixel claims;
* improved support for scientific reporting and publication;
* a stable evaluation architecture independent of the local matcher.

---

### Negative Consequences / Trade-offs

The decision introduces costs:

* checkpoint preparation requires additional work;
* independent truth may be difficult to obtain;
* manual validation can be time-consuming;
* reserving points for evaluation may reduce fitting data;
* checkpoint uncertainty must itself be tracked;
* benchmark preparation becomes more complex;
* small datasets may make strong separation difficult;
* benchmark revisions require careful truth/checkpoint versioning.

These costs are accepted because accurate scientific evaluation is a core ChandraMap requirement.

---

### Neutral Consequences

* fit residuals remain useful diagnostic metrics;
* RANSAC remains useful for geometric verification;
* visual overlays remain useful debugging and presentation artifacts;
* fitting-point coverage and checkpoint coverage remain separate concepts;
* later methods can continue using different correspondence-generation strategies while sharing the same evaluation contract.

---

## Risks and Mitigations

| Risk                                                        | Why It Matters                              | Mitigation                                                                          |
| ----------------------------------------------------------- | ------------------------------------------- | ----------------------------------------------------------------------------------- |
| Checkpoints accidentally used during fitting                | Invalidates claimed independence            | Maintain distinct fit/evaluation populations and verify data flow                   |
| Checkpoints influence model selection or repeated tuning    | Turns evaluation data into development data | Separate tuning from final evaluation where possible and disclose unavoidable reuse |
| Checkpoints cluster spatially                               | Can hide error elsewhere                    | Track and report checkpoint spatial distribution                                    |
| Ground truth contains annotation error                      | Can distort measured registration accuracy  | Preserve provenance, validation state, and uncertainty                              |
| High-error checkpoints are removed after results are seen   | Biases reported metrics                     | Predefine quality-control rules and retain exclusion reasons                        |
| Pixel error is converted incorrectly to metres              | Creates misleading geospatial claims        | Require documented GSD/projection/reference context                                 |
| Fit RMSE is presented as final accuracy                     | Can produce overly optimistic claims        | Label fit and checkpoint metrics separately                                         |
| Different methods use different checkpoints                 | Weakens direct comparison                   | Reuse common checkpoints whenever scientifically valid                              |
| Too few checkpoints are available                           | Weakens confidence in evaluation            | Report checkpoint count and limitation without inventing certainty                  |
| Flexible warp uses checkpoints during fitting               | Invalidates evaluation                      | Keep benchmark checkpoints outside warp estimation                                  |
| Matcher-derived holdouts are treated as authoritative truth | Overstates independence                     | Label their origin and evaluation limitation explicitly                             |
| Checkpoint coordinates change without benchmark versioning  | Breaks historical comparability             | Version or document truth/checkpoint changes                                        |

These are architectural risks. This ADR does not claim that they have already occurred.

---

## Deferred Decisions

The following remain outside ADR-0003:

* exact checkpoint count;
* exact checkpoint-density requirement;
* exact coverage threshold;
* exact fitting/checkpoint split ratio;
* exact automatic checkpoint-selection algorithm;
* exact manual annotation procedure;
* exact annotation tool;
* exact checkpoint file schema;
* exact checkpoint uncertainty model;
* exact accuracy pass/fail threshold;
* exact authoritative ground-truth dataset;
* final transformation model;
* local/piecewise warp architecture;
* final sub-pixel refinement method;
* final sensor-specific benchmark thresholds;
* final publication benchmark protocol.

These should be addressed through benchmark specifications, research methodology, or future ADRs when evidence is sufficient.

---

## Relationship to ADR-0001

[ADR-0001](0001-v1-known-overlap-first.md) answers:

> What registration problem should V1 solve first?

Its answer is:

> Begin with source/reference pairs already known to overlap.

ADR-0003 builds on that scope.

Known overlap allows ChandraMap to evaluate local correspondence and registration without first solving global retrieval.

However, known overlap does not remove the need for independent registration evaluation.

Conceptually:

```text
ADR-0001
Known Overlap First
        ↓
Controlled Source / Reference Pair
        ↓
Local Registration
        ↓
ADR-0003
Independent Evaluation
```

---

## Relationship to ADR-0002

[ADR-0002](0002-sift-as-v1-baseline.md) answers:

> Which local matching method establishes the initial V1 baseline?

Its answer is:

> SIFT.

ADR-0003 answers a different question:

> How should registration produced by that baseline be evaluated?

Its answer is:

> Using independent checkpoints or authoritative ground truth whenever possible.

The architecture is:

```text
ADR-0001
Known Overlap First
        ↓
ADR-0002
SIFT Baseline
        ↓
Candidate Correspondences
        ↓
Geometric Verification
        ↓
Fitting Tie Points
        ↓
Final Transformation
        ↓
ADR-0003
Independent Checkpoint Evaluation
```

ADR-0003 therefore does not alter the matcher selected by ADR-0002.

It defines how the resulting registration is measured.

---

## Relationship to Future Versions

Independent-checkpoint evaluation is intended to remain useful beyond V1.

Conceptually:

```text
V1 Method
        ↓
Independent Checkpoints
        ↓
V1 Registration Evidence
```

A later method can use:

```text
Later Scientific Method
        ↓
Same Compatible Independent Checkpoints
        ↓
Later Registration Evidence
```

which enables:

```text
V1 Evidence
        vs
Later-Version Evidence
```

under a shared evaluation definition.

The intent is not to freeze every future metric forever.

The intent is to preserve a stable principle:

> **Scientific-version improvements should be evaluated using evidence independent from the observations used to fit their transformations.**

If future geometry or uncertainty models require a fundamentally different evaluation architecture, that change should be documented explicitly and should preserve historical benchmark interpretation.

---

## Revisit Conditions

ADR-0003 should be revisited or superseded if:

* an official benchmark ground-truth source becomes available;
* a more authoritative lunar geospatial truth source is adopted;
* a standardized external checkpoint protocol is introduced;
* the benchmark moves to fundamentally different geometric representations;
* uncertainty-aware registration evaluation becomes a first-class requirement;
* three-dimensional or sensor-model evaluation replaces the current 2D registration assumptions;
* cross-validation or another formal data-partitioning design becomes necessary;
* checkpoint-generation methodology becomes standardized by an external scientific protocol;
* evidence demonstrates that the current evaluation architecture is inadequate for a defined scientific version.

A substantial change should produce a new ADR that supersedes ADR-0003 rather than rewriting this accepted historical decision.

---

## References

### Architecture Decision Records

* [ADR index](README.md)
* [ADR template](ADR_TEMPLATE.md)
* [ADR-0001 — V1 Known-Overlap First](0001-v1-known-overlap-first.md)
* [ADR-0002 — SIFT as the V1 Baseline](0002-sift-as-v1-baseline.md)

### Related ChandraMap Research Documentation

* [Research Questions](../research/research-questions.md)
* [Known Research Limitations](../research/known-limitations.md)
* [Experiment Methodology](../research/experiment-methodology.md)

### Related ChandraMap Architecture Documentation

* [V1 Pipeline](../architecture/v1-pipeline.md)
* [System Overview](../architecture/system-overview.md)
* [Core Engine Architecture](../architecture/core-engine-architecture.md)

### Relevant External Reference Areas

Where needed during implementation or benchmark design, consult authoritative sources such as:

* USGS ISIS image-registration/coregistration documentation;
* OpenCV geometric-transformation and robust-estimation documentation;
* ISRO Chandrayaan-2 instrument/product documentation;
* LROC product and geometric documentation;
* primary scientific literature on image-registration evaluation and independent control/check-point methodology.

Exact external URLs are intentionally not fabricated in this ADR.

---

## Final Decision Statement

ChandraMap permanently distinguishes:

```text
Points Used to Fit the Registration
```

from:

```text
Points Used to Independently Evaluate the Registration
```

The resulting benchmark architecture is:

```text
Correspondence Generation
        ↓
Geometric Verification
        ↓
Verified Fitting Tie Points
        ↓
Fit / Refine / Refit Transformation
        │
        │
        └─────────────────────┐
                              ▼
                     Final Transformation
                              │
Independent Checkpoints ──────┘
        ↓
Compare Predicted and Expected Locations
        ↓
Independent Checkpoint Residuals
        ↓
Defensible Registration Metrics
```

Therefore:

> **Low error on fitted points is evidence of good model fit. Low error on independent checkpoints is stronger evidence of registration accuracy. ChandraMap will not treat those claims as equivalent.**

