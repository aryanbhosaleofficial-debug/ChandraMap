# ChandraMap V1 Metrics

This document defines the quantitative metrics used to evaluate **ChandraMap V1** for lunar image correspondence and registration.

The metrics are designed to measure correspondence quality, geometric verification, spatial distribution, registration accuracy, geospatial accuracy where justified, runtime, and failure behavior. They are tied to the V1 ground-truth and evaluation protocol and must not be interpreted independently of how ground truth, control points, and check points are defined.

The central evaluation principle is that a transformation must **not** be evaluated only on the same points used to fit it. Independent check points should be used for final registration evaluation.

---

# 1. Metric Status

| Field                              | Value                                                                            |
| ---------------------------------- | -------------------------------------------------------------------------------- |
| Project                            | ChandraMap                                                                       |
| Benchmark                          | V1                                                                               |
| Document                           | `METRICS.md`                                                                     |
| Scope                              | Correspondence, geometry, registration, spatial coverage, and diagnostic metrics |
| Status                             | Not specified                                                                    |
| Version                            | Not specified                                                                    |
| Ground-truth dependency            | Yes                                                                              |
| Independent check-point evaluation | Required for registration accuracy                                               |
| Numerical benchmark results        | Not specified                                                                    |

This document defines metric methodology. It does **not** contain measured V1 results.

No numerical accuracy, RMSE, inlier ratio, coverage, runtime, threshold, or success rate should be interpreted as an actual ChandraMap result unless it is produced by a recorded benchmark run.

---

# 2. Evaluation Objectives

V1 evaluation must measure more than whether the final images look visually aligned.

The metric system evaluates the pipeline at several stages:

```text
Candidate Matches
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

The main objectives are:

1. Measure proposed correspondence quality.
2. Measure geometrically consistent correspondences.
3. Measure whether correspondences are spatially distributed across the overlap.
4. Measure transformation accuracy independently from transformation fitting.
5. Measure geospatial error when the available projection, GSD, and reference information make that conversion meaningful.
6. Record runtime and failure behavior.
7. Preserve diagnostic information needed to understand why a registration succeeds or fails.

A single metric cannot characterize the complete correspondence and registration pipeline.

For example:

- Many candidate matches do not necessarily mean many correct matches.
- A high inlier ratio does not guarantee good spatial coverage.
- A visually convincing overlay does not prove numerical accuracy.
- Low fitting residuals do not prove independent registration accuracy.
- Pixel error does not automatically represent physical ground error.
- A sub-pixel numerical value is not sufficient evidence of scientifically meaningful sub-pixel accuracy without appropriate ground truth.

The project feedback identifies inlier count, inlier ratio, spatial coverage, independent check-point RMSE, ground error where meaningful, and runtime/failure behavior as complementary evaluation measurements.

---

# 3. Metric Categories

| Category               | Purpose                                                                     | Metrics                                 |
| ---------------------- | --------------------------------------------------------------------------- | --------------------------------------- |
| Correspondence         | Measure proposed local correspondences                                      | Candidate match count                   |
| Geometric verification | Measure correspondences surviving geometric consistency testing             | Verified-inlier count, inlier ratio     |
| Spatial coverage       | Measure whether verified correspondences are distributed across the overlap | Grid coverage, convex-hull coverage     |
| Registration           | Measure transformation accuracy on independent points                       | Check-point error, check-point RMSE     |
| Geospatial             | Express registration error in physical units when justified                 | Ground error in metres                  |
| Diagnostics            | Explain spatial and geometric failure modes                                 | Residual vectors, residual distribution |
| System                 | Measure operational behavior                                                | Runtime, failure status, failure rate   |
| Retrieval              | Measure global candidate retrieval when a retrieval stage is used           | Recall@1 / Recall@5                     |

Global retrieval metrics are relevant only when V1 includes a global retrieval stage. They are not a substitute for local correspondence and registration metrics.

---

# 4. Primary V1 Metrics

The supplied project material establishes the following evaluation priorities:

- independent check-point error;
- verified-inlier statistics;
- spatial distribution/coverage;
- registration quality;
- failure behavior.

Where the formal V1 benchmark specification does not explicitly classify a metric as an official primary metric, the classification below is a **recommended V1 reporting priority**, not an assertion of an already implemented benchmark status.

## Recommended V1 primary metrics

| Metric                                     | Role                                                  | Why it matters                                                        |
| ------------------------------------------ | ----------------------------------------------------- | --------------------------------------------------------------------- |
| Independent check-point RMSE               | Recommended primary registration metric               | Measures final transformation accuracy on points not used for fitting |
| Independent check-point error distribution | Recommended primary diagnostic                        | Shows whether average error hides localized failures                  |
| Verified-inlier count                      | Recommended primary correspondence metric             | Measures geometrically consistent correspondences                     |
| Inlier ratio                               | Recommended primary correspondence diagnostic         | Measures the fraction of candidates surviving geometric verification  |
| Spatial coverage                           | Recommended primary correspondence-quality diagnostic | Determines whether reliable points are distributed across the overlap |
| Failure status/rate                        | Recommended system-level metric                       | Prevents successful cases from hiding pipeline failures               |

No numerical threshold is defined here unless the benchmark specification establishes one.

---

# 5. Metric Definitions

Every metric should be reported with:

- metric name;
- purpose;
- definition;
- formula where applicable;
- inputs;
- units;
- evaluation set;
- interpretation;
- limitations;
- reporting format.

The distinction between **control points** and **check points** is essential.

### Control points

Points used to estimate or refine the transformation.

### Check points

Independent points used to evaluate the final transformation.

```text
Candidate Matches
        ↓
RANSAC
        ↓
Verified Control Points
        ↓
Final Transformation
        ↓
Independent Check Points
        ↓
Registration Metrics
```

A check point must not be used to fit the transformation whose performance it evaluates.

---

# 6. Registration Error

## 6.1 Point-wise Check-Point Error

### Purpose

Measures the registration error at each independent check point.

### Definition

For check point `i`, let:

- `p_i^pred` be the predicted position produced by the final transformation.
- `p_i^gt` be the corresponding ground-truth position.

The point-wise registration error is:

```text
e_i = ||p_i^pred - p_i^gt||₂
```

### Formula

For two-dimensional image coordinates:

```text
e_i =
sqrt(
    (x_i^pred - x_i^gt)^2 +
    (y_i^pred - y_i^gt)^2
)
```

### Inputs

- Final transformation.
- Independent check-point coordinates.
- Ground-truth check-point coordinates.

### Units

**Source-image pixels** are the primary reporting unit.

### Evaluation set

**Independent check points only.**

Points used to fit the final transformation must not be used as independent check points.

### Interpretation

Lower point-wise error indicates that the final transformation places the predicted point closer to its ground-truth position.

The full distribution should be inspected rather than reporting only a single aggregate number.

### Limitations

Point-wise error depends on the quality and spatial distribution of the ground truth.

A small number of poorly distributed check points may not characterize the entire overlap.

### Reporting format

```text
checkpoint_error_px:
  count: <measured>
  min: <measured>
  median: <measured>
  mean: <measured>
  max: <measured>
```

Additional statistics may be reported when supported by the evaluation implementation.

---

# 7. Check-Point RMSE

### Purpose

Provides an aggregate measurement of final registration accuracy on independent check points.

### Definition

For `N` independent check points:

```text
RMSE =
sqrt(
    (1 / N) *
    Σ(e_i²)
)
```

where `e_i` is the point-wise registration error for check point `i`.

Equivalently:

```text
RMSE =
sqrt(
    (1 / N) *
    Σ(
        (x_i^pred - x_i^gt)² +
        (y_i^pred - y_i^gt)²
    )
)
```

### Inputs

- Independent check-point coordinates.
- Ground-truth check-point coordinates.
- Final transformation.

### Units

**Source-image pixels.**

### Evaluation set

Independent check points.

### Interpretation

Lower RMSE indicates lower average squared registration error on the independent evaluation points.

RMSE gives greater influence to larger errors than metrics based only on the arithmetic mean absolute error.

### Limitations

RMSE is an aggregate metric.

A good RMSE can still hide:

- localized large errors;
- systematic spatial distortion;
- poor coverage;
- a small number of failed regions.

Therefore RMSE must be interpreted together with error distributions and spatial diagnostics.

### Reporting format

```text
checkpoint_rmse_px: <measured>
checkpoint_count: <measured>
```

If independent check points are unavailable, RMSE must not be fabricated or calculated from the fitting points and reported as an independent evaluation result.

---

# 8. Independent Check-Point Error Distribution

### Purpose

Shows how registration error is distributed rather than reducing the result to one number.

### Definition

The benchmark should retain the individual error values:

```text
[e₁, e₂, e₃, ..., eₙ]
```

for all independent check points.

### Inputs

- Independent check points.
- Final transformation.
- Ground-truth positions.

### Units

Source-image pixels.

### Evaluation set

Independent check points.

### Interpretation

The distribution can reveal:

- consistent low error;
- isolated large errors;
- heavy-tailed error;
- spatially localized failures;
- systematic transformation problems.

### Limitations

Distribution statistics cannot identify the cause of an error without examining the spatial locations and residual vectors.

### Reporting format

At minimum:

```text
checkpoint_error_px:
  count: <measured>
  rmse: <measured>
  min: <measured>
  median: <measured>
  max: <measured>
```

If additional percentiles are implemented, they should be reported consistently across benchmark runs.

---

# 9. Verified-Inlier Count

### Purpose

Measures how many candidate correspondences survive geometric verification.

### Definition

Let:

- `N_candidate` = number of candidate correspondences entering geometric verification.
- `N_inlier` = number of correspondences classified as geometric inliers.

Then:

```text
verified_inliers = N_inlier
```

### Inputs

- Candidate correspondences.
- Geometric verification result.

### Units

Count.

### Evaluation set

Correspondences submitted to geometric verification.

### Interpretation

A larger verified-inlier count indicates that more candidate correspondences satisfy the selected geometric model under the configured verification procedure.

However, a larger count is **not automatically better**.

### Limitations

A high inlier count can still be problematic when:

- points are clustered;
- the transformation model is inappropriate;
- the candidate set is unusually large;
- correspondences are spatially redundant;
- the ground truth is not independently verified.

Verified-inlier count must therefore be interpreted together with inlier ratio, spatial coverage, and registration error.

### Reporting format

```text
candidate_matches: <measured>
verified_inliers: <measured>
```

---

# 10. Inlier Ratio

### Purpose

Measures the fraction of candidate correspondences that survive geometric verification.

### Definition

```text
inlier_ratio =
verified_inliers / candidate_matches
```

### Formula

$$
R_{inlier}
=
\frac{N_{inlier}}
     {N_{candidate}}
$$

### Inputs

- Candidate match count.
- Verified-inlier count.

### Units

Dimensionless ratio.

It may also be displayed as a percentage:

```text
inlier_ratio_percent =
inlier_ratio × 100
```

### Evaluation set

Candidate correspondences entering geometric verification.

### Interpretation

A higher inlier ratio means a larger fraction of candidate correspondences survived the geometric verification stage.

### Limitations

Inlier ratio alone does not establish successful registration.

For example, a very small candidate set could produce a high ratio while providing insufficient spatial support for reliable registration.

Therefore:

```text
Inlier Ratio
    +
Inlier Count
    +
Spatial Coverage
    +
Independent Registration Error
```

should be interpreted together.

### Zero-candidate case

If:

```text
candidate_matches = 0
```

the inlier ratio is undefined.

It should be reported as unavailable rather than as a fabricated numerical value.

### Reporting format

```text
inlier_ratio: <measured_or_null>
inlier_ratio_percent: <measured_or_null>
```

---

# 11. Spatial Coverage

### Purpose

Measures whether verified correspondences are distributed across the overlap rather than concentrated in one small region.

The project feedback explicitly recommends grid coverage or convex-hull coverage for this purpose.

### Why it matters

A set of geometrically valid matches clustered around one crater may not adequately constrain the transformation over the complete image overlap.

Therefore:

> More matches are not necessarily better if they are wrong or spatially clustered.

### Inputs

- Verified-inlier coordinates.
- Defined evaluation/overlap region.
- Coverage configuration.

### Units

Dimensionless normalized coverage, unless otherwise defined by the implementation.

### Evaluation set

Verified inliers.

### Interpretation

Higher coverage generally indicates that verified correspondences occupy a larger portion of the evaluated overlap.

### Limitations

Coverage does not measure correspondence correctness by itself.

A large spatial distribution of incorrect correspondences can still produce apparently high coverage.

Coverage must therefore be interpreted alongside geometric verification and independent registration accuracy.

---

# 12. Grid Coverage

### Purpose

Measures how many predefined spatial cells contain at least one verified inlier.

### Definition

Divide the evaluation region into a predefined grid.

For each grid cell:

```text
covered = 1
```

if the cell contains at least one verified inlier.

Otherwise:

```text
covered = 0
```

### Formula

$$
Coverage_{grid}
=
\frac{N_{covered}}
     {N_{total}}
$$

### Inputs

- Verified-inlier coordinates.
- Evaluation-region geometry.
- Grid definition.

### Units

Dimensionless ratio.

It may be displayed as a percentage.

### Evaluation set

Verified inliers.

### Interpretation

Higher grid coverage indicates that verified correspondences are distributed across more parts of the evaluation region.

### Limitations

The result depends on grid resolution.

A coarse grid and a fine grid can produce different coverage values for exactly the same correspondences.

Therefore the grid definition must remain fixed for directly comparable benchmark runs.

### Configuration requirement

The benchmark must record:

- grid dimensions;
- evaluation-region definition;
- cell inclusion rule.

The project feedback gives a `4 × 4` grid as an example of a possible implementation, but that example must not be treated as the official V1 configuration unless the benchmark specification defines it.

### Reporting format

```text
grid_coverage:
  value: <measured>
  grid_rows: <configured>
  grid_columns: <configured>
```

---

# 13. Convex-Hull Coverage

### Purpose

Measures the spatial extent occupied by verified inliers using their convex hull.

### Definition

Construct the convex hull of the verified-inlier coordinates.

Let:

- `A_hull` = area of the convex hull.
- `A_eval` = area of the defined evaluation region.

Then:

$$
Coverage_{hull}
=
\frac{A_{hull}}
     {A_{eval}}
$$

### Inputs

- Verified-inlier coordinates.
- Evaluation-region geometry.

### Units

Dimensionless ratio.

### Evaluation set

Verified inliers.

### Interpretation

Higher convex-hull coverage indicates that the verified correspondences span a larger portion of the evaluation region.

### Limitations

A convex hull can overstate actual spatial coverage.

For example, points distributed around the perimeter can create a large hull even if the interior contains no correspondences.

Therefore convex-hull coverage should not automatically replace grid coverage or point-density inspection.

### Degenerate cases

If the verified points do not define a meaningful two-dimensional hull, such as when they are:

- too few;
- collinear;
- coincident;

the metric may be undefined.

Such cases must be represented as unavailable rather than assigned an arbitrary value.

### Reporting format

```text
convex_hull_coverage: <measured_or_null>
```

---

# 14. Residual Vectors

### Purpose

Diagnoses spatial patterns in transformation error.

### Definition

For a point `i`, the residual vector is:

```text
r_i =
p_i^pred - p_i^gt
```

or equivalently:

```text
r_i = (dx_i, dy_i)
```

where:

```text
dx_i = x_i^pred - x_i^gt
dy_i = y_i^pred - y_i^gt
```

### Inputs

- Predicted point positions.
- Ground-truth point positions.

### Units

Source-image pixels.

### Evaluation set

Preferably independent check points for final registration diagnostics.

### Interpretation

Residual-vector plots can reveal systematic spatial patterns such as:

- translation bias;
- scale mismatch;
- rotation;
- perspective effects;
- terrain-related distortion;
- spatially varying transformation error.

### Limitations

A single aggregate RMSE cannot reveal these patterns.

Residual vectors should therefore be inspected when interpreting registration quality.

The project feedback specifically recommends inspecting residual vectors across the image and considering local/piecewise refinement or sensor geometry when residuals change systematically across the image.

### Reporting format

A benchmark result should preserve residual vectors or an equivalent diagnostic representation when the implementation supports it.

---

# 15. Ground Error in Metres

### Purpose

Express registration error in physical ground units when the required geospatial information makes the conversion meaningful.

### Definition

If a source-image error can validly be converted using the applicable ground sampling distance:

$$
E_m
\approx
E_{px}
\times
GSD_{m/px}
$$

### Inputs

- Source-image registration error.
- Valid source GSD.
- Appropriate projection/geospatial information.
- Reference/ground-truth information.

### Units

Metres.

### Evaluation set

Independent check points.

### Interpretation

Lower ground error indicates lower physical registration error under the assumptions of the conversion.

### Limitations

This conversion is not universally valid.

A pixel-to-metre conversion can become misleading when:

- GSD varies spatially;
- the image is not appropriately map-projected;
- viewing geometry affects the relationship;
- terrain relief is significant;
- the ground-truth coordinate system differs from the assumed model.

Therefore source-image pixel error remains the primary registration unit unless the benchmark establishes that physical conversion is meaningful.

### Important comparison rule

The same pixel error does not imply the same ground error across sensors.

For example, `0.2` source pixels at different sensor GSDs represent different physical distances.

### Reporting format

```text
ground_error_m:
  value: <measured_or_null>
  conversion_basis: <documented>
```

If the conversion is not scientifically justified:

```text
ground_error_m: null
```

with an explanatory status/reason field.

---

# 16. Registration Fit Error

### Purpose

Describes the residual error on the points used to estimate the transformation.

### Status

**Diagnostic only.**

Fit error must not be substituted for independent check-point error.

### Definition

The same point-wise error formulation can be applied to control points after fitting.

### Evaluation set

Transformation-fitting/control points.

### Units

Source-image pixels.

### Interpretation

Low fitting error indicates that the selected transformation model can explain the control points well.

### Limitations

Low fitting error can be misleading because the transformation was estimated from these points.

It does not establish independent registration accuracy.

### Reporting rule

If reported, fit error must be explicitly labeled:

```text
fit_error_px
```

and must never be presented as:

```text
checkpoint_rmse_px
```

---

# 17. Runtime

### Purpose

Measures the computational cost of the evaluated pipeline.

### Definition

Runtime is the elapsed execution time for the benchmark scope defined by the evaluation implementation.

The timing boundary must be documented.

### Inputs

- Start/end timestamps or equivalent benchmark timing mechanism.
- Benchmark configuration.

### Units

Seconds.

### Evaluation set

The complete benchmark execution or the explicitly defined measured stage.

### Interpretation

Lower runtime indicates faster execution under the same timing definition and comparable hardware conditions.

### Limitations

Runtime depends on:

- hardware;
- CPU/GPU;
- software environment;
- image size;
- preprocessing;
- matching configuration;
- retrieval/indexing;
- data loading;
- timing boundary.

Runtime values should not be compared directly when these conditions materially differ.

### Reporting format

```text
runtime_s: <measured>
```

The result should identify the hardware/environment used for the measurement.

---

# 18. Failure Status

### Purpose

Preserves failed benchmark executions rather than reporting only successful registrations.

### Definition

Each benchmark run should have an explicit success/failure status.

Example representation:

```text
status: success
```

or:

```text
status: failure
failure_reason: <documented_reason>
```

### Inputs

- Pipeline execution state.
- Stage-level failure information.

### Units

Categorical.

### Evaluation set

Every benchmark run.

### Interpretation

Failures are part of benchmark behavior.

They can expose sensitivity to:

- scale;
- illumination;
- modality;
- low-feature terrain;
- geometric distortion;
- insufficient correspondences;
- transformation instability.

### Limitations

A failure taxonomy must be defined consistently before failure rates are compared across experiments.

### Reporting format

```text
status: <success|failure|other_defined_status>
failure_reason: <value_or_null>
```

Do not silently remove failed runs from benchmark datasets.

---

# 19. Failure Rate

### Purpose

Measures how frequently a benchmark configuration fails across a defined set of test cases.

### Definition

If:

- `N_failed` = number of failed test cases;
- `N_total` = total number of evaluated test cases;

then:

$$
FailureRate
=
\frac{N_{failed}}
     {N_{total}}
$$

### Inputs

- Per-case failure status.
- Complete test-case set.

### Units

Dimensionless ratio or percentage.

### Evaluation set

All test cases in the explicitly defined evaluation set.

### Interpretation

Lower failure rate means fewer test cases fail to produce a valid benchmark result.

### Limitations

Failure rate depends heavily on the composition of the test set.

It must always be reported with the evaluated test population and stress-case definition.

### Reporting format

```text
failure_rate:
  failed: <measured>
  total: <measured>
  value: <measured>
```

No failure-rate threshold is defined in this document.

---

# 20. Retrieval Metrics

## 20.1 Recall@1

### Status

Applicable only if the evaluated V1 configuration includes a global retrieval stage.

### Purpose

Measures whether the correct reference region appears as the top-ranked retrieved candidate.

### Definition

For each query:

```text
hit@1 = 1
```

if the correct reference region is ranked first.

Otherwise:

```text
hit@1 = 0
```

Then:

$$
Recall@1
=
\frac{N_{hit@1}}
     {N_{queries}}
$$

### Units

Dimensionless ratio or percentage.

### Limitations

Recall@1 measures retrieval, not final local registration accuracy.

---

## 20.2 Recall@5

### Status

Applicable only if a Top-K/global retrieval stage is included.

### Purpose

Measures whether the correct reference region appears within the first five retrieved candidates.

### Definition

```text
hit@5 = 1
```

if the correct candidate appears in ranks 1 through 5.

Then:

$$
Recall@5
=
\frac{N_{hit@5}}
     {N_{queries}}
$$

### Units

Dimensionless ratio or percentage.

### Limitations

Recall@5 does not establish that local correspondence or registration will succeed.

---

# 21. Metric Dependencies

Metrics depend on different stages of the pipeline.

| Metric                | Candidate matches | Verified inliers | Control points | Independent check points | Geospatial metadata |
| --------------------- | ----------------: | ---------------: | -------------: | -----------------------: | ------------------: |
| Candidate match count |                 ✓ |                  |                |                          |                     |
| Verified-inlier count |                 ✓ |                ✓ |                |                          |                     |
| Inlier ratio          |                 ✓ |                ✓ |                |                          |                     |
| Grid coverage         |                   |                ✓ |                |                          |                     |
| Convex-hull coverage  |                   |                ✓ |                |                          |                     |
| Fit error             |                   |                  |              ✓ |                          |                     |
| Check-point error     |                   |                  |                |                        ✓ |                     |
| Check-point RMSE      |                   |                  |                |                        ✓ |                     |
| Ground error          |                   |                  |                |                        ✓ |                   ✓ |
| Residual vectors      |                   |                  |                |                        ✓ |                     |
| Runtime               |                   |                  |                |                          |                     |
| Failure status        |                   |                  |                |                          |                     |
| Recall@1              |                   |                  |                |                          |                     |
| Recall@5              |                   |                  |                |                          |                     |

This dependency distinction prevents a metric from being calculated from the wrong evaluation set.

---

# 22. Fitting Metrics vs Independent Metrics

This distinction is mandatory.

## Fitting metrics

Metrics calculated using the points that helped estimate the transformation.

Examples:

- control-point residuals;
- fit RMSE.

These metrics describe how well the transformation explains its training/control points.

## Independent evaluation metrics

Metrics calculated using points that were not used to estimate the final transformation.

Examples:

- check-point error;
- check-point RMSE;
- independent residual distribution.

These metrics describe how accurately the final transformation performs on independent evidence.

### Required interpretation

```text
Fit error
    ↓
Model fitting diagnostic

Check-point error
    ↓
Independent registration evaluation
```

The two must never be conflated.

---

# 23. Sub-Pixel Accuracy Reporting

The V1 evaluation should use precise terminology around sub-pixel accuracy.

### Required unit

Registration error must first be reported in **source-image pixels**.

Example:

```text
checkpoint_rmse_px: <measured>
```

Only after that should a physical conversion be considered.

### Sub-pixel does not mean physically identical accuracy

A result below one source pixel does not automatically imply the same physical accuracy across different sensors.

For example:

```text
0.2 source pixels
```

has different physical meaning depending on the source product's GSD and geometry.

### Ground-truth requirement

A sub-pixel claim requires ground truth that supports the precision being claimed.

A visually sharp overlay or low fitting residual is not sufficient evidence.

### Reporting rule

Do not write:

```text
Sub-pixel accuracy achieved
```

unless the benchmark result and ground-truth protocol actually support that claim.

Instead report the measured numerical value:

```text
checkpoint_rmse_px: <measured>
```

and interpret it according to the benchmark's ground-truth quality.

---

# 24. Metric Validity Rules

A metric is valid only when its required inputs and evaluation conditions are satisfied.

## Rule 1 — No independent points, no independent RMSE

If no independent check points exist, do not report fitting error as check-point RMSE.

---

## Rule 2 — No candidates, no inlier ratio

If the candidate count is zero, inlier ratio is undefined.

---

## Rule 3 — Insufficient geometry, no fabricated transformation result

If geometric verification cannot establish a valid transformation, registration metrics that require a final transformation must be reported as unavailable.

---

## Rule 4 — No meaningful physical conversion, no ground-error claim

If GSD/projection/reference information does not support conversion to metres, report source-pixel error only.

---

## Rule 5 — No invented values

Missing metrics must be represented as:

```text
null
```

or the repository's defined unavailable representation.

They must not be replaced with:

```text
0
```

unless zero is the actual measured value.

---

## Rule 6 — Preserve failures

Failed benchmark cases must remain part of the evaluation record.

---

## Rule 7 — Preserve metric configuration

Coverage and runtime measurements must retain the configuration needed to interpret them.

---

# 25. Spatial Distribution Requirements

A registration result should not be judged solely by the number of verified inliers.

The benchmark should inspect whether the verified points are:

- spread across the overlap;
- concentrated around one feature;
- concentrated near an image boundary;
- distributed across multiple terrain structures;
- affected by low-feature areas.

### Recommended evidence

Where supported, retain:

1. Verified-inlier visualization.
2. Spatial coverage metric.
3. Grid-coverage representation.
4. Convex-hull representation.
5. Residual-vector visualization.

### Interpretation

A result with many verified points concentrated in one small region can be less informative for the complete overlap than a result with fewer, well-distributed verified points.

This is why spatial coverage is a separate metric category.

---

# 26. Metric Reporting for a Single Pair

A single V1 pair should report, where applicable:

```yaml
correspondence:
  candidate_matches: <measured>
  verified_inliers: <measured>
  inlier_ratio: <measured_or_null>

spatial:
  grid_coverage: <measured_or_null>
  convex_hull_coverage: <measured_or_null>

registration:
  checkpoint_count: <measured_or_null>
  checkpoint_rmse_px: <measured_or_null>
  checkpoint_error_min_px: <measured_or_null>
  checkpoint_error_median_px: <measured_or_null>
  checkpoint_error_max_px: <measured_or_null>

geospatial:
  ground_error_m: <measured_or_null>

system:
  runtime_s: <measured_or_null>
  status: <measured>
  failure_reason: <value_or_null>
```

The exact machine-readable schema must follow the repository's implemented result schema when one exists.

---

# 27. Metric Reporting Across Multiple Pairs

When V1 is evaluated over multiple image pairs, per-pair results should be preserved.

Aggregate statistics must not replace the individual measurements.

Recommended reporting structure:

```text
Dataset
├── Pair 001
│   ├── correspondence metrics
│   ├── spatial metrics
│   ├── registration metrics
│   └── system status
│
├── Pair 002
│   ├── correspondence metrics
│   ├── spatial metrics
│   ├── registration metrics
│   └── system status
│
└── ...
```

Aggregate statistics should identify:

- number of evaluated cases;
- number of successful cases;
- number of failed cases;
- metric aggregation method;
- cases excluded from a particular metric and why.

Do not silently exclude failed or undefined cases.

---

# 28. Aggregation Rules

No official aggregation thresholds or statistical aggregation method are specified in the supplied project materials.

Therefore, the benchmark implementation must explicitly define the aggregation method before presenting an aggregate result.

Possible descriptive statistics include:

- mean;
- median;
- minimum;
- maximum;
- standard deviation;
- percentile statistics.

These should not be introduced as official V1 requirements unless the benchmark specification establishes them.

### Important distinction

For a metric such as check-point RMSE:

```text
Per-pair RMSE
```

and:

```text
Aggregate RMSE
```

are not necessarily equivalent.

The benchmark should preserve the per-pair values and clearly document any aggregate calculation.

---

# 29. Stress-Case Metric Reporting

The project evaluation guidance recommends testing different conditions, including:

- easy overlap;
- Sun-angle stress;
- scale stress;
- modality stress;
- geometry stress;
- low-feature terrain.

Metrics should be reported per stress category when such categories are part of the benchmark.

### Example structure

| Test condition      | Inlier count | Inlier ratio | Coverage | Check-point RMSE |  Runtime | Status   |
| ------------------- | -----------: | -----------: | -------: | ---------------: | -------: | -------- |
| Easy pair           |     Measured |     Measured | Measured |         Measured | Measured | Measured |
| Sun-angle stress    |     Measured |     Measured | Measured |         Measured | Measured | Measured |
| Scale stress        |     Measured |     Measured | Measured |         Measured | Measured | Measured |
| Modality stress     |     Measured |     Measured | Measured |         Measured | Measured | Measured |
| Geometry stress     |     Measured |     Measured | Measured |         Measured | Measured | Measured |
| Low-feature terrain |     Measured |     Measured | Measured |         Measured | Measured | Measured |

This table is a reporting template, not a set of existing benchmark results.

---

# 30. Method Comparison

When multiple correspondence methods are compared, they must be evaluated using the same benchmark conditions wherever possible.

The project feedback recommends comparing the same test pairs through:

1. SIFT baseline.
2. A stronger matcher.
3. The full sensor-aware and multi-scale pipeline.

The comparison should focus on measured changes in:

- registration error;
- inlier statistics;
- spatial coverage;
- failure behavior;
- runtime.

### Example comparison structure

| Method           | Candidate Matches | Verified Inliers | Inlier Ratio | Coverage | Check-Point RMSE |  Runtime | Failures |
| ---------------- | ----------------: | ---------------: | -----------: | -------: | ---------------: | -------: | -------: |
| SIFT baseline    |          Measured |         Measured |     Measured | Measured |         Measured | Measured | Measured |
| Stronger matcher |          Measured |         Measured |     Measured | Measured |         Measured | Measured | Measured |
| Full pipeline    |          Measured |         Measured |     Measured | Measured |         Measured | Measured | Measured |

No method should be declared superior from this document alone.

The benchmark results determine the observed differences.

---

# 31. Visual Evidence vs Quantitative Evidence

Visual overlays are useful diagnostic artifacts but are not themselves quantitative metrics.

### Visual evidence can show

- approximate alignment;
- obvious registration failures;
- spatial correspondence patterns;
- transformation artifacts;
- residual-vector behavior.

### Quantitative evidence must show

- correspondence statistics;
- spatial coverage;
- independent registration error;
- applicable geospatial error;
- runtime;
- failure behavior.

### Required principle

```text
Visual overlay
      ≠
Numerical registration accuracy
```

A visually convincing overlay must not be used to replace independent numerical evaluation.

---

# 32. What Each Metric Does Not Prove

| Metric                         | Does prove                                    | Does not prove                      |
| ------------------------------ | --------------------------------------------- | ----------------------------------- |
| Candidate match count          | Number of proposed correspondences            | Correspondence correctness          |
| Verified-inlier count          | Number surviving geometric verification       | Uniform spatial coverage            |
| Inlier ratio                   | Fraction of candidates surviving verification | Absolute registration accuracy      |
| Grid coverage                  | Distribution across configured cells          | Match correctness                   |
| Convex-hull coverage           | Spatial extent of inliers                     | Interior point density              |
| Fit error                      | Model agreement with control points           | Independent accuracy                |
| Check-point RMSE               | Aggregate independent registration error      | Global lunar correctness            |
| Check-point error distribution | Distribution of independent errors            | Cause of errors                     |
| Ground error                   | Physical error when conversion is justified   | Universal geospatial accuracy       |
| Residual vectors               | Spatial error patterns                        | Overall benchmark performance alone |
| Runtime                        | Execution cost under the measured setup       | Algorithmic quality by itself       |
| Failure rate                   | Frequency of failed cases                     | Cause of failure                    |
| Recall@1 / Recall@5            | Retrieval success                             | Final registration accuracy         |

---

# 33. Recommended V1 Reporting Order

For each benchmark result, present the metrics in the following order:

```text
1. Run status
2. Candidate match count
3. Verified-inlier count
4. Inlier ratio
5. Spatial coverage
6. Independent check-point count
7. Independent check-point error
8. Independent check-point RMSE
9. Ground error, if meaningful
10. Residual diagnostics
11. Runtime
12. Failure information, when applicable
```

This ordering follows the conceptual flow from correspondence generation to final registration evaluation.

---

# 34. Example Result Template

The following is a documentation template only.

It contains no actual ChandraMap measurements.

```yaml
benchmark:
  version: v1
  pair_id: <pair-id>

correspondence:
  candidate_matches: <measured>
  verified_inliers: <measured>
  inlier_ratio: <measured>

spatial:
  grid_coverage: <measured>
  convex_hull_coverage: <measured>

registration:
  checkpoint_count: <measured>
  checkpoint_error_min_px: <measured>
  checkpoint_error_median_px: <measured>
  checkpoint_error_max_px: <measured>
  checkpoint_rmse_px: <measured>

geospatial:
  ground_error_m: <measured-or-null>
  conversion_basis: <documented-or-null>

diagnostics:
  residual_vectors: <artifact-reference>
  residual_distribution: <artifact-reference>

system:
  runtime_s: <measured>
  status: <success-or-failure>
  failure_reason: <value-or-null>
```

---

# 35. Invalid Reporting Patterns

The following reporting patterns must not be used.

## 35.1 Decorative accuracy scores

Do not report:

```text
Accuracy: 92%
Confidence: 5/5
Registration quality: ★★★★★
```

unless these values are formally defined metrics backed by measured benchmark results.

---

## 35.2 Training/fitting error presented as registration accuracy

Do not report:

```text
RMSE = <fit error>
```

as independent registration accuracy when the same points were used to estimate the transformation.

---

## 35.3 Pixel error presented as metres without justification

Do not convert source-pixel error into metres when the required GSD/projection/geometric assumptions are unavailable.

---

## 35.4 Match count treated as correctness

Do not interpret:

```text
1000 matches
```

as proof that 1000 correct correspondences exist.

---

## 35.5 Inlier ratio treated as complete performance

Do not interpret a high inlier ratio without considering:

- inlier count;
- spatial coverage;
- independent registration error.

---

## 35.6 Visual overlay treated as ground truth

Do not treat a visually convincing overlay as proof of sub-pixel or geospatial accuracy.

---

## 35.7 Failure cases removed

Do not calculate success metrics only from the cases that happened to succeed unless the reporting explicitly identifies the conditioning and also reports the overall failure behavior.

---

# 36. Metric Interpretation Checklist

Before accepting a V1 result, verify:

- [ ] Candidate matches are recorded.
- [ ] Verified inliers are separately recorded.
- [ ] Inlier ratio is calculated from the correct populations.
- [ ] Spatial coverage is calculated from verified correspondences.
- [ ] Coverage configuration is recorded.
- [ ] Transformation-fitting points are distinguished from check points.
- [ ] Check points were not used to fit the final transformation.
- [ ] Check-point error is reported in source-image pixels.
- [ ] Check-point RMSE is calculated only from independent points.
- [ ] Error distribution is retained where supported.
- [ ] Residual vectors are inspected where applicable.
- [ ] Ground error is reported only when physical conversion is meaningful.
- [ ] Runtime timing boundaries are documented.
- [ ] Failed cases are preserved.
- [ ] No unsupported thresholds are claimed.
- [ ] No unmeasured accuracy values are presented.
- [ ] No decorative confidence scores are presented as benchmark results.

---

# 37. Metric Limitations

The V1 metric system cannot eliminate all ambiguity in lunar image registration.

### Sensor differences

OHRC, TMC-2, IIRS, and reference imagery can have substantially different spatial scales and imaging characteristics.

Metrics therefore need to be interpreted in the context of the evaluated sensor pair.

### Illumination differences

Changing Sun angle can alter shadows and terrain appearance.

A correspondence failure under different illumination cannot necessarily be interpreted as a simple registration-model failure.

### Geometric differences

Lunar terrain is not a flat surface.

A single global transformation may not explain all residual structure.

### Ground-truth quality

The quality of the evaluation metrics is limited by the quality of the ground truth.

### Spatial distribution

A small number of highly accurate points may not provide sufficient coverage for the complete overlap.

### Aggregation

Aggregate metrics can hide difficult individual cases.

Per-case results should therefore remain available.

---

# 38. V1 Metric Summary

| Metric                | Unit          | Evaluation points                   | Role                                    |
| --------------------- | ------------- | ----------------------------------- | --------------------------------------- |
| Candidate match count | Count         | Candidate matches                   | Correspondence diagnostic               |
| Verified-inlier count | Count         | Verified inliers                    | Core correspondence metric              |
| Inlier ratio          | Ratio / %     | Candidate + verified matches        | Geometric verification metric           |
| Grid coverage         | Ratio / %     | Verified inliers                    | Spatial distribution                    |
| Convex-hull coverage  | Ratio / %     | Verified inliers                    | Spatial distribution                    |
| Fit error             | Source pixels | Control points                      | Diagnostic only                         |
| Check-point error     | Source pixels | Independent check points            | Registration metric                     |
| Check-point RMSE      | Source pixels | Independent check points            | Recommended primary registration metric |
| Error distribution    | Source pixels | Independent check points            | Registration diagnostic                 |
| Ground error          | Metres        | Independent check points            | Conditional geospatial metric           |
| Residual vectors      | Source pixels | Preferably independent check points | Geometry diagnostic                     |
| Runtime               | Seconds       | Benchmark execution                 | System metric                           |
| Failure status        | Categorical   | Every run                           | System metric                           |
| Failure rate          | Ratio / %     | Complete evaluation set             | System metric                           |
| Recall@1              | Ratio / %     | Retrieval queries                   | Conditional retrieval metric            |
| Recall@5              | Ratio / %     | Retrieval queries                   | Conditional retrieval metric            |

---

# 39. Final V1 Evaluation Principle

The V1 benchmark should answer a simple quantitative question:

> **Can ChandraMap establish reliable lunar correspondences, verify their geometry, estimate a transformation, and demonstrate the accuracy of that transformation on independent check points?**

The evaluation chain is:

```text
LOCAL MATCHES
      ↓
RANSAC
      ↓
VERIFIED INLIERS
      ↓
SPATIAL COVERAGE
      ↓
FINAL TRANSFORMATION
      ↓
INDEPENDENT CHECK POINTS
      ↓
SOURCE-PIXEL ERROR
      ↓
RMSE
      ↓
OPTIONAL PHYSICAL GROUND ERROR
      ↓
RUNTIME + FAILURE BEHAVIOR
```

The benchmark should report what was actually measured, preserve difficult and failed cases, and keep fitting metrics separate from independent evaluation metrics.

No numerical result should be considered a ChandraMap V1 result until it has been produced by the actual benchmark implementation using the defined V1 dataset and ground-truth protocol.

<!-- Source basis: project benchmark instructions and supplied SIH 26166 technical feedback, including the correspondence → RANSAC → inlier → transformation → independent check-point evaluation flow. -->
