# ChandraMap V1 — Acceptance Criteria

**File:** `benchmarks/v1/ACCEPTANCE_CRITERIA.md`
**Benchmark:** ChandraMap V1
**Project:** ChandraMap
**Problem:** SIH 26166 — Multi-modal, Sun angle and scale invariant image correspondence using Chandrayaan-2 optical images

---

## 1. Purpose

This document defines the acceptance criteria for the ChandraMap V1 benchmark.

It specifies the conditions under which:

- a V1 benchmark implementation is valid;
- a benchmark run is valid;
- ground truth is acceptable for evaluation;
- correspondence evaluation is valid;
- geometric verification is valid;
- registration evaluation is valid;
- independent check-point evaluation is valid;
- metrics are valid;
- stress-test evaluation is valid;
- reproducibility requirements are satisfied;
- evidence is sufficient to support an accepted benchmark result.

This document is an **acceptance contract** for benchmark validity.

It does **not** define:

- the complete V1 benchmark specification;
- the complete ground-truth protocol;
- detailed metric formulas;
- the complete stress-test protocol;
- actual benchmark results;
- implementation status;
- measured accuracy;
- measured RMSE;
- measured runtime;
- benchmark thresholds that have not been formally defined.

The SIH 26166 evaluation framing emphasizes reliable correspondence, source-image sub-pixel accuracy, well-distributed match points, registered output, and measurable quantities such as RMSE and inlier statistics.

---

## 2. Acceptance Philosophy

V1 acceptance is based on **evidence and validity**, not on decorative confidence scores or unsupported performance claims.

The project feedback explicitly recommends removing unmeasured percentage ratings and replacing them with actual RMSE, inlier ratio, coverage, and runtime measurements.

Therefore:

> A benchmark result MUST NOT be considered accepted merely because the registered images look visually convincing.

Likewise:

> A large number of candidate matches MUST NOT be treated as evidence of successful registration unless those matches survive geometric verification and produce valid evaluation results.

The benchmark should demonstrate:

```text
Valid Data
    ↓
Valid Ground Truth
    ↓
Valid Correspondences
    ↓
Geometric Verification
    ↓
Valid Transformation
    ↓
Independent Evaluation
    ↓
Valid Metrics
    ↓
Reproducible Evidence
```

---

# 3. Acceptance Statuses

Each criterion SHOULD have one of the following statuses when a benchmark report is produced.

| Status          | Meaning                                                                            |
| --------------- | ---------------------------------------------------------------------------------- |
| `PASS`          | Criterion has been satisfied and sufficient evidence exists.                       |
| `FAIL`          | Criterion has been violated or required evidence is invalid.                       |
| `BLOCKED`       | Criterion cannot be evaluated because required information or data is unavailable. |
| `N/A`           | Criterion genuinely does not apply to the evaluated benchmark case.                |
| `NOT SPECIFIED` | The project has not yet defined the required threshold or implementation detail.   |

`PASS` MUST NOT be assigned merely because a value exists. The value and its provenance must be valid.

---

# 4. Threshold Policy

## 4.1 No invented thresholds

The current project materials do not establish numerical acceptance thresholds for:

- minimum inlier count;
- minimum inlier ratio;
- maximum check-point RMSE;
- minimum spatial coverage;
- maximum ground error;
- maximum runtime;
- maximum failure rate;
- minimum stress-test success rate.

Therefore these thresholds are:

> **To be defined**

until the V1 benchmark specification establishes them.

This document MUST NOT silently convert examples, engineering preferences, or typical computer-vision values into official V1 thresholds.

---

## 4.2 Metric availability is not metric acceptance

A metric can be mandatory to report without having a defined numerical pass threshold.

For example:

```text
Check-point RMSE
    ↓
MUST be measured and reported
    ↓
Numerical acceptance threshold
    ↓
To be defined
```

This distinction is important.

The benchmark can therefore determine whether a measurement was performed correctly without falsely claiming that the project has already decided what numerical value constitutes success.

---

# 5. Requirement Classification

Each criterion in this document is classified as one of:

| Classification    | Meaning                                                                                                |
| ----------------- | ------------------------------------------------------------------------------------------------------ |
| **Mandatory**     | Required for a valid V1 benchmark evaluation when applicable.                                          |
| **Recommended**   | Strongly recommended by project feedback but not currently established as a mandatory acceptance gate. |
| **To Be Defined** | A required project decision or threshold is not currently specified.                                   |
| **Conditional**   | Required only when the relevant capability or benchmark case is being evaluated.                       |

The classification describes the current documentation state. It does not imply that an unimplemented requirement has already been satisfied.

---

# 6. Relationship to Other V1 Documents

The V1 benchmark documentation is organized as a hierarchy:

```text
benchmarks/README.md
        ↓
Overall benchmark suite
        ↓
benchmarks/v1/README.md
        ↓
V1 benchmark overview
        ↓
benchmarks/v1/BENCHMARK_SPEC.md
        ↓
V1 benchmark contract
        ↓
benchmarks/v1/GROUND_TRUTH_PROTOCOL.md
        ↓
Ground-truth and independent check-point rules
        ↓
benchmarks/v1/METRICS.md
        ↓
Metric definitions and calculations
        ↓
benchmarks/v1/STRESS_TESTS.md
        ↓
Robustness/stress-testing protocol
        ↓
benchmarks/v1/ACCEPTANCE_CRITERIA.md
        ↓
Conditions required for benchmark validity and acceptance
```

The documents have different responsibilities.

| Document                                 | Responsibility                                |
| ---------------------------------------- | --------------------------------------------- |
| `benchmarks/README.md`                   | Overall benchmark suite                       |
| `benchmarks/v1/README.md`                | V1 benchmark overview                         |
| `benchmarks/v1/BENCHMARK_SPEC.md`        | V1 benchmark contract                         |
| `benchmarks/v1/GROUND_TRUTH_PROTOCOL.md` | Ground-truth and independent evaluation rules |
| `benchmarks/v1/METRICS.md`               | Metric definitions and calculations           |
| `benchmarks/v1/STRESS_TESTS.md`          | Stress-test and robustness protocol           |
| `benchmarks/v1/ACCEPTANCE_CRITERIA.md`   | Acceptance and validity conditions            |

This document MUST NOT redefine rules that belong to those documents unless required to determine acceptance.

---

# 7. Acceptance Layers

V1 acceptance is evaluated at three levels.

## 7.1 Implementation acceptance

Determines whether the benchmark implementation provides the required mechanisms to execute V1 evaluation correctly.

## 7.2 Run acceptance

Determines whether a particular benchmark execution was performed under valid conditions.

## 7.3 Result acceptance

Determines whether the produced measurements and evidence are valid enough to be included in a benchmark report.

These are different concepts.

A valid implementation can produce an invalid run.

A valid run can produce a valid measurement without satisfying a numerical performance threshold.

---

# 8. Implementation Acceptance Criteria

## AC-IMP-001 — V1 benchmark scope

**Classification:** Mandatory

The implementation MUST identify itself as a V1 benchmark implementation and MUST follow the V1 benchmark contract defined by the project's V1 documentation.

**Acceptance condition:**

- V1 evaluation behavior is distinguishable from development experiments.
- The benchmark configuration is identifiable.
- The evaluated pipeline is documented.

**Threshold:** Not applicable.

---

## AC-IMP-002 — Ground-truth integration

**Classification:** Mandatory

The implementation MUST support evaluation against the project's defined ground truth when ground truth is required for the benchmark case.

The ground-truth protocol requires separation between points used for fitting and independently held-out check points.

**Acceptance condition:**

- Ground truth can be associated with the evaluated source/reference pair.
- Control/fitting points and check/evaluation points can be distinguished.
- Check points are not used to fit the final transformation.

The project feedback explicitly states that transformations must not be fitted and judged on exactly the same points.

---

## AC-IMP-003 — Candidate/inlier distinction

**Classification:** Mandatory

The implementation MUST distinguish candidate matches from geometrically verified inliers.

A matcher confidence score MUST NOT be treated as proof that a correspondence is geometrically correct.

The project feedback explicitly recommends using the terminology **Candidate Matches** and allowing RANSAC/geometric verification to determine verified inliers.

---

## AC-IMP-004 — Geometric verification

**Classification:** Mandatory

The implementation MUST provide a documented geometric verification stage when the evaluated method produces local correspondences.

The documented project flow is:

```text
Candidate Matches
        ↓
RANSAC + Initial Model
        ↓
Verified Inliers
```

The exact transformation model and numerical parameters MUST follow `BENCHMARK_SPEC.md` and the implementation configuration.

---

## AC-IMP-005 — Final transformation sequence

**Classification:** Mandatory when sub-pixel refinement is used

Where sub-pixel refinement is part of the evaluated pipeline, the processing order MUST preserve the documented geometry flow:

```text
Candidate Matches
        ↓
RANSAC + Initial Model
        ↓
Verified Inliers
        ↓
Sub-Pixel Refinement
        ↓
Refit Final Transform
```

The project feedback specifically identifies this order as the required correction to the pipeline design.

---

## AC-IMP-006 — Independent evaluation

**Classification:** Mandatory

The implementation MUST support evaluation on points that were not used to fit the final transformation.

A benchmark implementation that can only report fitting-point error MUST NOT present that error as independent registration accuracy.

---

## AC-IMP-007 — Metric generation

**Classification:** Mandatory

The implementation MUST produce the metrics required by the applicable V1 benchmark specification.

At minimum, the project evaluation framework identifies:

- inlier count;
- inlier ratio;
- spatial coverage;
- check-point RMSE in source pixels;
- ground error when meaningful;
- runtime;
- failure rate.

The project feedback defines these measurements as useful indicators for local matching, spatial distribution, registration, geospatial accuracy, and system reliability.

---

## AC-IMP-008 — Sensor-aware handling

**Classification:** Mandatory for sensor-specific evaluation

When evaluating OHRC, TMC-2, or IIRS cases, the implementation MUST preserve the sensor-specific processing assumptions defined by the V1 benchmark.

The project materials explicitly state that OHRC, TMC-2, and IIRS should not be treated as identical image sources.

---

## AC-IMP-009 — Scale handling

**Classification:** Mandatory for scale-stress evaluation

The evaluated pipeline MUST NOT treat simple upsampling as recovery of missing spatial information.

For large resolution differences, the benchmark should operate at physically meaningful effective scales before fine registration.

The project feedback explicitly recommends reference pyramids or downsampling of the higher-resolution side rather than enlarging coarse IIRS imagery and treating the result as recovered detail.

---

## AC-IMP-010 — Reproducible configuration

**Classification:** Mandatory

A benchmark run MUST preserve sufficient configuration information to identify:

- evaluated method;
- relevant preprocessing;
- transformation configuration;
- refinement configuration, if used;
- benchmark case;
- ground-truth version;
- applicable evaluation configuration.

Exact configuration fields are governed by the V1 implementation/schema.

---

# 9. Data Acceptance Criteria

## AC-DATA-001 — Source/reference identity

**Classification:** Mandatory

Every benchmark case MUST identify:

- source image/product;
- reference image/product;
- sensor/product type;
- relevant image metadata required by the benchmark.

---

## AC-DATA-002 — Known overlap

**Classification:** Mandatory for known-overlap V1 cases

The source and reference images MUST represent the intended common lunar region defined by the benchmark case.

The benchmark MUST NOT claim successful correspondence on a pair that is not a valid evaluation pair.

---

## AC-DATA-003 — Image integrity

**Classification:** Mandatory

Input data MUST be readable and sufficiently intact for the benchmark stage being evaluated.

Corrupt, incomplete, or incompatible input MUST cause the relevant benchmark case to be marked invalid rather than silently substituted.

---

## AC-DATA-004 — Metadata preservation

**Classification:** Mandatory when available

Relevant metadata SHOULD be retained, including where available:

- pixel scale/GSD;
- footprint;
- map projection;
- viewing geometry;
- lighting geometry;
- sensor/product identity.

The project feedback explicitly recommends preserving these metadata fields.

---

## AC-DATA-005 — Sensor representation

**Classification:** Mandatory for IIRS evaluation

IIRS MUST NOT be treated as an ordinary single-band camera image without a documented representation step.

The project materials recommend beginning with a sensible 2D representation such as a selected band, PCA/composite, or structural representation.

The exact representation is implementation-dependent unless fixed elsewhere by the V1 specification.

---

# 10. Ground-Truth Acceptance Criteria

## AC-GT-001 — Authoritative ground truth

**Classification:** Mandatory

The benchmark MUST use the authoritative ground-truth source defined for the applicable V1 evaluation.

If official challenge ground truth is available and specified as authoritative, it MUST take precedence over an independently generated substitute.

The project feedback explicitly recommends using challenge ground truth when available.

---

## AC-GT-002 — Ground-truth provenance

**Classification:** Mandatory

The ground-truth data used for a benchmark run MUST be identifiable by source and version where such information exists.

---

## AC-GT-003 — Valid correspondence

**Classification:** Mandatory

Each evaluated ground-truth correspondence MUST represent the same physical lunar location in the source and reference representations.

---

## AC-GT-004 — Coordinate consistency

**Classification:** Mandatory

Source and reference coordinates MUST use the coordinate convention defined by the benchmark.

Coordinate-system mismatches MUST cause the relevant evaluation to fail or become blocked rather than being silently corrected.

---

## AC-GT-005 — Control/check separation

**Classification:** Mandatory

Ground-truth points used for transformation fitting MUST be distinguishable from independent check points.

---

## AC-GT-006 — Check-point independence

**Classification:** Mandatory

Check points MUST NOT be used to fit the final transformation.

This includes direct and indirect use through the final fitting process.

The project feedback identifies independent check points as necessary to avoid overly optimistic registration error.

---

## AC-GT-007 — Ambiguous-point handling

**Classification:** Mandatory

Ambiguous ground-truth points MUST NOT silently enter the independent evaluation set.

Their handling MUST follow `GROUND_TRUTH_PROTOCOL.md`.

---

## AC-GT-008 — Ground-truth immutability during evaluation

**Classification:** Mandatory

Ground truth MUST NOT be modified after observing benchmark results merely to improve or simplify the reported outcome.

A correction to genuinely erroneous ground truth must be versioned and documented.

---

# 11. Correspondence Acceptance Criteria

## AC-MATCH-001 — Candidate correspondence output

**Classification:** Mandatory

The evaluated local matching stage MUST produce an identifiable set of candidate correspondences when the method supports local matching.

---

## AC-MATCH-002 — Candidate/inlier separation

**Classification:** Mandatory

Candidate correspondences MUST remain distinguishable from geometrically verified inliers.

---

## AC-MATCH-003 — Geometric verification

**Classification:** Mandatory

Correspondences used for transformation estimation MUST pass the benchmark's documented geometric verification procedure.

The project feedback explicitly states that matcher confidence alone is not proof of geometric correctness.

---

## AC-MATCH-004 — Inlier statistics

**Classification:** Mandatory

The benchmark MUST record the applicable:

- inlier count;
- inlier ratio.

The numerical threshold for acceptable inlier count or ratio is:

> **To be defined.**

---

## AC-MATCH-005 — Spatial distribution

**Classification:** Mandatory for applicable registration evaluation

The benchmark MUST evaluate whether accepted correspondences are spatially distributed across the relevant overlap rather than merely concentrated in one local feature.

Possible project-supported representations include:

- grid coverage;
- convex-hull coverage.

The project feedback explicitly identifies spatial distribution as an evaluation requirement.

---

## AC-MATCH-006 — No match-count-only acceptance

**Classification:** Mandatory

A benchmark result MUST NOT be accepted solely because it produces more matches than another method.

Match correctness, geometric consistency, spatial distribution, and independent registration accuracy must remain distinguishable.

The project feedback explicitly warns that more matches are not necessarily better when they are incorrect or clustered.

---

# 12. Geometric Verification Acceptance Criteria

## AC-GEO-001 — Initial model

**Classification:** Mandatory

An initial geometric model MUST be defined for the applicable benchmark case.

For local, already map-projected image pairs, affine transformation or homography may be used as initial models where appropriate, but the benchmark MUST follow the actual model specified by the V1 implementation.

The project feedback describes affine or homography as reasonable first models for appropriate local map-projected cases, while warning that lunar terrain is not inherently planar.

---

## AC-GEO-002 — RANSAC verification

**Classification:** Mandatory

When RANSAC is the documented verification method, the benchmark MUST use it to distinguish geometrically consistent correspondences from outliers.

---

## AC-GEO-003 — Residual inspection

**Classification:** Mandatory for registration evaluation

Residual behavior MUST be inspectable.

If residuals vary systematically across the image, the benchmark MUST NOT automatically assume that a single global transformation adequately represents the geometry.

The project feedback recommends inspecting residual vectors across the image.

---

## AC-GEO-004 — Transformation appropriateness

**Classification:** Mandatory

The selected transformation MUST be appropriate for the geometry represented by the benchmark case.

A more flexible transformation MUST NOT be used solely to conceal weak or inaccurate correspondences.

---

## AC-GEO-005 — Local/piecewise refinement

**Classification:** Conditional

Where residual structure demonstrates that a global model is insufficient, local/piecewise refinement or available sensor/DEM geometry MAY be evaluated.

This is not a universal V1 requirement for every case.

---

# 13. Sub-Pixel Refinement Acceptance Criteria

## AC-SUBPIX-001 — Verified-point prerequisite

**Classification:** Mandatory when sub-pixel refinement is evaluated

Sub-pixel refinement MUST operate on geometrically verified control/tie points rather than arbitrary candidate matches.

---

## AC-SUBPIX-002 — Refinement ordering

**Classification:** Mandatory when applicable

The documented order MUST be:

```text
Candidate Matches
        ↓
Geometric Verification
        ↓
Verified Inliers
        ↓
Sub-Pixel Refinement
        ↓
Final Transformation Refit
```

---

## AC-SUBPIX-003 — Final-transform refit

**Classification:** Mandatory when refined points are used

The final transformation MUST be estimated again from the refined control points when the benchmark workflow uses those refined points.

This sequence is explicitly recommended in the project feedback.

---

## AC-SUBPIX-004 — Source-image units

**Classification:** Mandatory

Sub-pixel registration accuracy MUST be reported in source-image pixels before any conversion into ground units.

The project materials explicitly require source-image pixels as the primary representation.

---

## AC-SUBPIX-005 — No unsupported fine-detail claim

**Classification:** Mandatory

Sub-pixel refinement MUST NOT be interpreted as evidence that the source sensor contains spatial detail below its physically supported information content.

Upsampling does not recover missing spatial detail.

---

# 14. Registration Acceptance Criteria

## AC-REG-001 — Final transformation

**Classification:** Mandatory

A registration result MUST identify the final transformation used to generate the registered output.

---

## AC-REG-002 — Independent registration evaluation

**Classification:** Mandatory

Registration accuracy MUST be evaluated on independent check points whenever valid check-point ground truth is available.

---

## AC-REG-003 — Check-point RMSE

**Classification:** Mandatory

The benchmark MUST calculate and report check-point RMSE in source-image pixels for applicable registration cases.

The project feedback explicitly identifies check-point RMSE in source pixels as the registration metric.

The numerical acceptance threshold is:

> **To be defined.**

---

## AC-REG-004 — Fitting-point error distinction

**Classification:** Mandatory

Error measured on transformation-fitting points MUST NOT be presented as independent registration accuracy.

---

## AC-REG-005 — Registered output

**Classification:** Mandatory

A valid registration benchmark case MUST produce the registered output required by the V1 benchmark specification, when registration output is part of that case.

---

## AC-REG-006 — Visual output is supporting evidence

**Classification:** Mandatory

A visual overlay or registered preview MAY support inspection, but MUST NOT replace quantitative independent evaluation.

The project framing identifies correspondences and measurable registration accuracy as the core deliverables, with a mosaic treated as a downstream demonstration.

---

# 15. Geospatial Accuracy Acceptance Criteria

## AC-GEOACC-001 — Conditional ground-error reporting

**Classification:** Conditional

Ground error in metres MAY be reported only when:

- appropriate ground scale/GSD is available;
- projection information is valid where required;
- reference truth supports the conversion;
- the conversion is scientifically meaningful.

The project feedback explicitly limits metre-based ground error to cases where projection/GSD and reference truth make the conversion meaningful.

---

## AC-GEOACC-002 — No unsupported metre conversion

**Classification:** Mandatory

The benchmark MUST NOT convert source-pixel error into metres using an assumed or undocumented scale.

---

## AC-GEOACC-003 — Sensor-specific interpretation

**Classification:** Mandatory

The same numerical pixel error MUST NOT automatically be interpreted as the same physical ground error across sensors with different GSDs.

The project feedback explicitly notes this distinction for TMC-2 and IIRS.

---

# 16. Spatial Coverage Acceptance Criteria

## AC-COV-001 — Coverage measurement

**Classification:** Mandatory for applicable correspondence/registration evaluation

The benchmark MUST measure the spatial distribution of accepted correspondences using the coverage representation defined by the V1 metrics specification.

Possible representations identified by project feedback include:

- grid coverage;
- convex-hull coverage.

---

## AC-COV-002 — Coverage reporting

**Classification:** Mandatory

The reported coverage MUST identify the metric definition and evaluation space.

---

## AC-COV-003 — Coverage threshold

**Classification:** To Be Defined

No official numerical minimum coverage threshold is currently established by the provided project materials.

The project feedback provides an example of grid-based coverage, including a `4 × 4` grid representation, but does not establish a project-wide acceptance threshold for the fraction of occupied cells.

Therefore:

> A `4 × 4` grid MUST NOT be interpreted as an official pass threshold unless established elsewhere in the V1 specification.

---

# 17. Retrieval Acceptance Criteria

Global retrieval is conditional in the project architecture.

The project feedback states that global retrieval should not be mandatory when reliable geolocation, footprint, or map-projection metadata can already restrict the search.

## AC-RET-001 — Conditional retrieval

**Classification:** Conditional

If global retrieval is evaluated, the benchmark MUST evaluate it according to the V1 retrieval specification.

If retrieval is not required for a known-overlap case, its absence MUST NOT automatically invalidate the case.

---

## AC-RET-002 — Retrieval metric

**Classification:** Conditional

Where retrieval is evaluated, applicable metrics may include:

- Recall@1;
- Recall@5.

The project feedback identifies these metrics for global retrieval.

---

## AC-RET-003 — Reference-index validity

**Classification:** Conditional

If FAISS or another vector index is used for global retrieval, the reference-side index MUST exist and be generated according to the documented benchmark procedure.

The project feedback explicitly distinguishes offline reference indexing from online source-image retrieval.

---

# 18. Stress-Test Acceptance Criteria

V1 stress testing is intended to demonstrate where the system remains reliable and where performance degrades.

The project feedback identifies the following stress categories:

1. Easy pair.
2. Sun-angle stress.
3. Scale stress.
4. Modality stress.
5. Geometry stress.
6. Low-feature terrain.

---

## AC-STRESS-001 — Easy pair

**Classification:** Mandatory when included in the V1 stress matrix

The easy-pair case SHOULD represent:

- known overlap;
- similar illumination;
- moderate scale difference.

Its purpose is to demonstrate an end-to-end working pipeline.

---

## AC-STRESS-002 — Sun-angle stress

**Classification:** Mandatory when included in the V1 stress matrix

The benchmark MUST evaluate behavior under materially different shadow/illumination conditions.

Performance degradation MUST be reported rather than hidden.

The project feedback explicitly recommends comparing similar and very different illumination conditions and reporting the performance drop honestly.

---

## AC-STRESS-003 — Scale stress

**Classification:** Mandatory when included in the V1 stress matrix

The benchmark MUST include a case involving a meaningful ground-resolution difference where the test is intended to evaluate scale robustness.

The evaluation MUST NOT interpret upsampling as recovery of missing detail.

---

## AC-STRESS-004 — Modality stress

**Classification:** Conditional

Where IIRS is included, the benchmark SHOULD evaluate an IIRS-derived 2D representation against an appropriate visible reference.

---

## AC-STRESS-005 — Geometry stress

**Classification:** Conditional

Where geometry stress is included, the benchmark SHOULD evaluate conditions involving:

- relief-rich terrain;
- stronger viewpoint difference;
- or other geometry conditions defined by `STRESS_TESTS.md`.

---

## AC-STRESS-006 — Low-feature terrain

**Classification:** Recommended

The benchmark SHOULD include smooth or repetitive terrain where false matches are more likely.

This provides evidence about failure behavior rather than only favorable cases.

---

## AC-STRESS-007 — Stress-test performance threshold

**Classification:** To Be Defined

No numerical pass threshold for stress-test success rate or degradation is currently specified by the available project materials.

Therefore:

> Stress-test measurements MUST be reported when the corresponding test is evaluated, but numerical acceptance thresholds remain **To be defined**.

---

# 19. Failure Handling Acceptance Criteria

## AC-FAIL-001 — Explicit failure recording

**Classification:** Mandatory

Benchmark failures MUST be recorded explicitly.

A failed registration MUST NOT be silently omitted from aggregate results.

---

## AC-FAIL-002 — Failure-rate measurement

**Classification:** Mandatory when multiple test cases are evaluated

The benchmark MUST report failure rate when the V1 run evaluates multiple cases.

The project feedback identifies runtime and failure rate as system-level evaluation measures.

The maximum acceptable failure rate is:

> **To be defined.**

---

## AC-FAIL-003 — Failure classification

**Classification:** Recommended

Failures SHOULD be classified where possible, for example by:

- scale;
- illumination;
- modality;
- retrieval;
- geometry;
- low-feature terrain;
- insufficient correspondences;
- invalid input;
- runtime/system failure.

The project feedback explicitly identifies these categories as useful for diagnosing failure modes.

---

## AC-FAIL-004 — No selective failure removal

**Classification:** Mandatory

A difficult benchmark case MUST NOT be removed solely because it produces an unfavorable result.

Case exclusion requires an independent validity reason.

---

# 20. Runtime Acceptance Criteria

## AC-RUNTIME-001 — Runtime measurement

**Classification:** Mandatory when runtime is part of the V1 run

Runtime MUST be measured under the documented execution environment.

---

## AC-RUNTIME-002 — Environment identification

**Classification:** Mandatory when runtime is reported

The benchmark MUST identify the relevant hardware/software environment sufficiently to interpret the measurement.

---

## AC-RUNTIME-003 — Runtime threshold

**Classification:** To Be Defined

The project materials identify runtime as a metric but do not define a universal numerical V1 runtime threshold.

Therefore:

> Maximum acceptable runtime is **To be defined**.

---

## AC-RUNTIME-004 — Runtime scope

**Classification:** Mandatory

The benchmark report MUST make clear what the runtime measurement includes.

Where applicable, retrieval/search and demonstration/runtime components should not be silently mixed.

---

# 21. Reproducibility Acceptance Criteria

## AC-REPRO-001 — Dataset identity

**Classification:** Mandatory

A benchmark run MUST identify the dataset/product inputs used.

---

## AC-REPRO-002 — Ground-truth version

**Classification:** Mandatory

The benchmark MUST identify the ground-truth version used whenever ground-truth versioning exists.

---

## AC-REPRO-003 — Configuration capture

**Classification:** Mandatory

The benchmark MUST preserve the configuration required to understand the evaluated pipeline.

---

## AC-REPRO-004 — Metric definition

**Classification:** Mandatory

Reported metrics MUST correspond to the definitions in the V1 metrics specification.

---

## AC-REPRO-005 — Randomness

**Classification:** Mandatory when stochastic components are used

If a benchmark component is stochastic, the run SHOULD preserve the relevant seed or deterministic configuration where technically supported.

The exact reproducibility mechanism is:

> **To be defined**

unless established by the V1 implementation.

---

## AC-REPRO-006 — Evidence retention

**Classification:** Mandatory

Sufficient evidence MUST be retained to reconstruct or audit the benchmark result.

---

# 22. Required Evidence

A valid V1 benchmark run SHOULD retain evidence appropriate to the evaluated stages.

The project feedback specifically recommends retaining outputs such as match plots, rejected outliers, registered overlays, inlier statistics, check-point error, and runtime.

At minimum, applicable evidence should include:

| Evidence                      | Requirement                                          |
| ----------------------------- | ---------------------------------------------------- |
| Source/reference identifiers  | Mandatory                                            |
| Ground-truth identity/version | Mandatory                                            |
| Candidate correspondences     | Mandatory when local matching is evaluated           |
| Verified inliers              | Mandatory when geometric verification is evaluated   |
| Transformation information    | Mandatory for registration                           |
| Check-point evaluation        | Mandatory for independent registration evaluation    |
| Inlier count                  | Mandatory                                            |
| Inlier ratio                  | Mandatory                                            |
| Spatial coverage              | Mandatory where applicable                           |
| Check-point RMSE              | Mandatory where registration is evaluated            |
| Ground error                  | Conditional                                          |
| Runtime                       | Mandatory when runtime is part of the run            |
| Failure status                | Mandatory                                            |
| Registered preview            | Mandatory when registered output is part of the case |
| Residual information          | Mandatory for geometry evaluation                    |
| Stress-test case identity     | Mandatory for stress evaluation                      |

---

# 23. Evidence Integrity

Evidence MUST correspond to the actual benchmark run being reported.

Evidence MUST NOT be:

- manually altered to hide failures;
- selectively chosen to imply better performance;
- presented without identifying the associated benchmark case;
- reused from another run without disclosure.

Screenshots and visualizations are supporting evidence and do not replace machine-readable or numerical measurements where those measurements are required.

---

# 24. Metric Acceptance

## AC-METRIC-001 — Correct metric definition

**Classification:** Mandatory

Every reported metric MUST use the definition established by `METRICS.md`.

---

## AC-METRIC-002 — Unit correctness

**Classification:** Mandatory

Each metric MUST report its unit explicitly where applicable.

Examples:

- source-image pixels;
- metres;
- count;
- ratio;
- percentage;
- time.

---

## AC-METRIC-003 — Source-pixel priority

**Classification:** Mandatory for registration accuracy

Registration error MUST be reported in source-image pixels before ground-unit conversion.

The project feedback explicitly emphasizes this reporting order.

---

## AC-METRIC-004 — Ground-error validity

**Classification:** Conditional

Ground error MUST only be reported when its geospatial conversion is meaningful.

---

## AC-METRIC-005 — No fabricated values

**Classification:** Mandatory

Missing measurements MUST be represented as missing, unavailable, blocked, or not applicable.

They MUST NOT be replaced with:

- estimated benchmark scores presented as measurements;
- placeholder percentages presented as results;
- invented RMSE;
- invented runtime;
- invented coverage;
- invented success rates.

---

# 25. Baseline Comparison Acceptance

The project feedback recommends comparing the same test pairs using:

1. SIFT baseline;
2. stronger matcher only;
3. full sensor-aware + multi-scale pipeline.

## AC-BASELINE-001 — Same-pair comparison

**Classification:** Recommended

When comparative evaluation is performed, methods SHOULD be evaluated on the same benchmark cases.

---

## AC-BASELINE-002 — Comparable evaluation

**Classification:** Recommended

Compared methods SHOULD use equivalent:

- test cases;
- ground truth;
- evaluation metrics;
- acceptance procedure.

---

## AC-BASELINE-003 — Baseline result integrity

**Classification:** Mandatory when a baseline is reported

A baseline MUST be reported using actual measured values.

No method may be assigned a score or percentage that has not been measured.

---

# 26. Sensor-Specific Acceptance

## 26.1 OHRC

For OHRC cases:

- product metadata MUST be preserved where available;
- the challenge/product pixel scale MUST be treated as authoritative where specified;
- fine terrain correspondence MAY be evaluated at the resolution supported by the source.

The project materials identify OHRC as high-detail visible panchromatic imagery and recommend using the challenge product metadata as the final authority for pixel scale.

---

## 26.2 TMC-2

For TMC-2 cases:

- product scale MUST be represented using authoritative metadata;
- map projection/DEM information MAY be incorporated when applicable;
- registration claims MUST remain consistent with the available source information.

---

## 26.3 IIRS

For IIRS cases:

- the spectral nature of the source MUST be acknowledged;
- a registration-friendly 2D representation MUST be defined;
- the representation MUST NOT imply recovery of spatial detail unavailable from the source;
- ground-scale interpretation MUST remain physically meaningful.

The project feedback identifies IIRS as hyperspectral/IR data and recommends a simple 2D representation before conventional image matching.

---

# 27. Illumination Acceptance

## AC-ILLUM-001 — Illumination-aware evaluation

**Classification:** Mandatory for Sun-angle stress evaluation

The benchmark MUST distinguish between ordinary brightness normalization and genuine changes in illumination geometry.

The project materials explicitly state that contrast normalization cannot make changed crater shadows geometrically identical.

---

## AC-ILLUM-002 — Structural representation

**Classification:** Recommended

For strong Sun-angle differences, structural representations such as:

- edges;
- gradients;
- phase-based representations;
- stable terrain geometry;

SHOULD be evaluated where relevant.

---

## AC-ILLUM-003 — Performance degradation reporting

**Classification:** Mandatory for Sun-angle stress evaluation

If performance decreases under changed illumination, the decrease MUST be reported rather than concealed.

---

# 28. Scale Acceptance

## AC-SCALE-001 — Comparable effective scale

**Classification:** Mandatory for scale-stress evaluation

The benchmark MUST compare imagery at a physically meaningful effective scale before fine correspondence where large resolution differences exist.

---

## AC-SCALE-002 — No false detail recovery

**Classification:** Mandatory

Upsampling MUST NOT be described as recovering spatial information that was absent from the original sensor.

---

## AC-SCALE-003 — Refinement limitation

**Classification:** Mandatory

Fine registration claims MUST remain consistent with the spatial information actually available in the source image.

The project feedback explicitly states that fine refinement should stop when the source sensor no longer contains the spatial information needed for a finer claim.

---

# 29. Geometry Acceptance

## AC-GEOM-001 — No flat-Moon assumption

**Classification:** Mandatory

The benchmark MUST NOT assume that a single global homography is universally sufficient for lunar imagery.

The project feedback explicitly notes terrain relief, viewing geometry, and raw-image geometry as reasons a single global transform may be insufficient.

---

## AC-GEOM-002 — Residual behavior

**Classification:** Mandatory

Residual vectors SHOULD be inspected spatially for registration evaluation.

Systematic spatial residual behavior MUST be documented when observed.

---

## AC-GEOM-003 — Flexible warp restraint

**Classification:** Mandatory

Flexible warping MUST NOT be used as a substitute for accurate, well-distributed control points.

The project feedback specifically warns that flexible warping can make an overlay look good despite weak correspondences.

---

# 30. Benchmark Run Acceptance Matrix

The following matrix summarizes the current V1 acceptance state.

| Area            | Requirement                        | Classification                       | Threshold                          |
| --------------- | ---------------------------------- | ------------------------------------ | ---------------------------------- |
| Data            | Valid source/reference pair        | Mandatory                            | Defined by benchmark case          |
| Ground truth    | Valid authoritative ground truth   | Mandatory                            | No numeric threshold specified     |
| Ground truth    | Control/check separation           | Mandatory                            | Must be satisfied                  |
| Matching        | Candidate correspondences          | Mandatory                            | Defined by applicable method       |
| Matching        | Geometric verification             | Mandatory                            | Defined by V1 configuration        |
| Matching        | Inlier count                       | Mandatory to report                  | Acceptance threshold not specified |
| Matching        | Inlier ratio                       | Mandatory to report                  | Acceptance threshold not specified |
| Coverage        | Spatial coverage                   | Mandatory to report where applicable | Threshold not specified            |
| Registration    | Independent check-point evaluation | Mandatory                            | Must be independent                |
| Registration    | Check-point RMSE                   | Mandatory to report                  | Threshold not specified            |
| Geospatial      | Ground error                       | Conditional                          | Threshold not specified            |
| Retrieval       | Recall@1 / Recall@5                | Conditional                          | Threshold not specified            |
| Stress          | Easy pair                          | Applicable                           | Threshold not specified            |
| Stress          | Sun-angle                          | Applicable                           | Threshold not specified            |
| Stress          | Scale                              | Applicable                           | Threshold not specified            |
| Stress          | Modality                           | Conditional                          | Threshold not specified            |
| Stress          | Geometry                           | Conditional                          | Threshold not specified            |
| Stress          | Low-feature terrain                | Recommended                          | Threshold not specified            |
| System          | Runtime                            | Applicable                           | Threshold not specified            |
| System          | Failure rate                       | Applicable                           | Threshold not specified            |
| Reproducibility | Configuration/evidence             | Mandatory                            | Must be sufficient                 |
| Reporting       | No invented results                | Mandatory                            | Zero tolerance                     |
| Reporting       | Units and definitions              | Mandatory                            | Must be correct                    |

---

# 31. Mandatory Acceptance Gates

A benchmark run MUST NOT be considered valid if any applicable mandatory gate fails.

## Gate A — Data validity

The benchmark inputs are valid and identifiable.

```text
Source identified
Reference identified
Input readable
Applicable metadata available
```

---

## Gate B — Ground-truth validity

```text
Ground truth identified
Coordinates valid
Provenance known
Control/check separation maintained
```

---

## Gate C — Correspondence validity

```text
Candidate matches identified
Geometric verification performed
Verified inliers identified
```

---

## Gate D — Registration validity

```text
Final transformation identified
Independent check points available
Check points not used for fitting
Registration error measured
```

---

## Gate E — Metric validity

```text
Metric definitions followed
Units correct
Source-pixel error reported
Ground error only when meaningful
No fabricated values
```

---

## Gate F — Evidence validity

```text
Benchmark case identifiable
Configuration identifiable
Ground-truth version identifiable
Relevant outputs retained
Failures retained
```

---

# 32. Conditions That Cause Benchmark Failure

A V1 benchmark run MUST be considered invalid or failed for the affected evaluation when any mandatory condition is violated.

Examples include:

### Ground-truth leakage

Using independent check points to fit the final transformation.

### Invalid metric claim

Reporting fitting-point RMSE as independent check-point accuracy.

### Coordinate mismatch

Comparing coordinates expressed in incompatible conventions without a documented valid transformation.

### Unsupported ground conversion

Reporting metre-level error using an assumed or invalid GSD conversion.

### Hidden failures

Removing unsuccessful benchmark cases from reported results without a valid case-exclusion reason.

### Invented results

Presenting unmeasured accuracy, RMSE, runtime, coverage, or success rates as actual results.

### Invalid evidence

Reporting a benchmark result without sufficient information to identify the evaluated data, configuration, and ground truth.

### Unsupported detail claim

Presenting upsampling as recovery of missing source spatial information.

### Match-count-only claim

Declaring success solely because the pipeline generated many candidate or inlier matches without independent registration evidence.

### Visual-only acceptance

Declaring registration successful solely because the overlay or mosaic looks visually good.

---

# 33. Conditions That Do Not Automatically Cause Failure

The following do **not**, by themselves, establish benchmark failure:

- low inlier count when no minimum threshold has been defined;
- high RMSE when no acceptance threshold has been defined;
- low coverage when no minimum threshold has been defined;
- runtime above an unspecified target;
- failure on a difficult stress case;
- inability to report ground error when the required geospatial information is unavailable;
- absence of global retrieval when metadata already provides the relevant search restriction;
- use of a particular matcher rather than another;
- use of a simple model instead of a more complex model when the simple model is appropriate.

Such conditions may affect performance interpretation, but they must not be converted into undocumented pass/fail thresholds.

---

# 34. Threshold Registry

The current project materials identify the following categories as requiring eventual numerical or explicit acceptance definitions.

| Criterion                             | Current state |
| ------------------------------------- | ------------- |
| Minimum inlier count                  | To be defined |
| Minimum inlier ratio                  | To be defined |
| Maximum independent check-point RMSE  | To be defined |
| Minimum spatial coverage              | To be defined |
| Maximum ground error                  | To be defined |
| Maximum runtime                       | To be defined |
| Maximum failure rate                  | To be defined |
| Minimum stress-test success rate      | To be defined |
| Maximum acceptable stress degradation | To be defined |
| Retrieval Recall@1 threshold          | To be defined |
| Retrieval Recall@5 threshold          | To be defined |

These values MUST NOT be populated from arbitrary industry conventions or from an individual experimental result.

---

# 35. Recommended Acceptance Enhancements

The following are recommended improvements rather than currently established numerical acceptance gates.

## Recommended 1 — Spatially explicit evaluation

Use grid or convex-hull coverage to show whether accepted correspondences span the overlap.

## Recommended 2 — Failure taxonomy

Record whether failures arise from scale, illumination, modality, geometry, retrieval, or low-feature terrain.

## Recommended 3 — Baseline comparison

Evaluate the same image pairs using:

```text
SIFT baseline
      vs
Stronger matcher
      vs
Full sensor-aware + multi-scale pipeline
```

The project feedback recommends this controlled comparison to determine which pipeline component produces measurable improvement.

## Recommended 4 — Residual visualization

Retain residual-vector visualizations for geometry analysis.

## Recommended 5 — Before/after refinement

Where sub-pixel refinement is evaluated, retain measurements before and after refinement.

The project feedback specifically recommends comparing source-pixel check-point RMSE before and after refinement.

## Recommended 6 — Separate sensor reporting

Report OHRC, TMC-2, and IIRS results separately where the benchmark includes them.

The project feedback recommends separate sensor results rather than hiding them inside one mixed average.

---

# 36. Benchmark Result Acceptance Template

A V1 benchmark report SHOULD contain an acceptance summary similar to:

| Criterion                | Status              | Evidence                 | Threshold             | Notes |
| ------------------------ | ------------------- | ------------------------ | --------------------- | ----- |
| Data validity            | `PASS/FAIL/BLOCKED` | Dataset/case evidence    | Defined/Not specified | —     |
| Ground-truth validity    | `PASS/FAIL/BLOCKED` | Ground-truth evidence    | Defined/Not specified | —     |
| Control/check separation | `PASS/FAIL`         | Evaluation configuration | Mandatory             | —     |
| Geometric verification   | `PASS/FAIL`         | Inlier evidence          | Defined/Not specified | —     |
| Spatial coverage         | `PASS/FAIL/BLOCKED` | Coverage metric          | To be defined         | —     |
| Check-point evaluation   | `PASS/FAIL/BLOCKED` | Check-point evidence     | Mandatory             | —     |
| Check-point RMSE         | Measured value      | Metric output            | To be defined         | —     |
| Ground error             | Measured/N/A        | Geospatial evidence      | To be defined         | —     |
| Stress tests             | `PASS/FAIL/BLOCKED` | Stress outputs           | To be defined         | —     |
| Runtime                  | Measured/N/A        | Runtime output           | To be defined         | —     |
| Failure rate             | Measured/N/A        | Run summary              | To be defined         | —     |
| Reproducibility          | `PASS/FAIL`         | Configuration/evidence   | Mandatory             | —     |

This template is an acceptance-report structure, not a statement that any current benchmark has passed.

---

# 37. Result Interpretation Rules

## Rule 1 — Acceptance is not the same as performance

A run can be technically valid while producing poor measured performance.

For example:

```text
Valid evaluation
        ≠
Good accuracy
```

The benchmark must keep these concepts separate.

---

## Rule 2 — No threshold means no numerical pass claim

If the project has not defined a threshold:

```text
Measured RMSE = X
```

may be reported as a measurement, but:

```text
RMSE = X → PASS
```

MUST NOT be claimed unless an applicable acceptance threshold exists.

---

## Rule 3 — Failed stress case remains evidence

A stress-test failure should remain part of the evidence unless the case itself is invalid.

Failure behavior is useful information about system limitations.

---

## Rule 4 — Independent evaluation takes precedence

When fitting error and independent check-point error differ, independent evaluation is the relevant evidence for registration accuracy.

---

## Rule 5 — Visual quality is supporting evidence

A visually attractive registered image does not override poor quantitative registration evidence.

---

## Rule 6 — More matches do not automatically mean better registration

Match count must be interpreted together with:

- inlier ratio;
- spatial coverage;
- independent check-point error;
- failure behavior.

---

## Rule 7 — Physical plausibility matters

A benchmark result must remain consistent with sensor resolution, GSD, projection, modality, and available spatial information.

---

# 38. Final Acceptance Decision

A V1 benchmark run may be classified as **accepted for reporting** only when:

1. all applicable mandatory validity criteria are satisfied;
2. required ground-truth independence is maintained;
3. required metrics are calculated according to their documented definitions;
4. required units are correct;
5. required evidence is retained;
6. failures are not selectively hidden;
7. no unsupported numerical claims are made;
8. any applicable numerical acceptance thresholds that have been formally defined are satisfied.

Where a required threshold has not yet been defined, the result MUST be reported as:

> **Threshold not specified / To be defined**

rather than being assigned an invented pass/fail status.

---

# 39. V1 Acceptance Decision Model

The complete decision process is:

```text
                    V1 BENCHMARK RUN
                           │
                           ▼
                    DATA VALID?
                     /        \
                   NO          YES
                   │            │
                INVALID         ▼
                         GROUND TRUTH VALID?
                            /          \
                          NO            YES
                          │              │
                       BLOCK/FAIL        ▼
                              CONTROL/CHECK
                                SEPARATED?
                               /          \
                             NO            YES
                             │              │
                           FAIL             ▼
                                  MATCHING / GEOMETRY
                                      VALID?
                                    /       \
                                  NO         YES
                                  │           │
                                FAIL          ▼
                                  INDEPENDENT
                                  EVALUATION?
                                  /          \
                                NO            YES
                                │              │
                              FAIL             ▼
                                  METRICS VALID?
                                    /       \
                                  NO         YES
                                  │           │
                                FAIL          ▼
                                  EVIDENCE COMPLETE?
                                    /       \
                                  NO         YES
                                  │           │
                                BLOCKED      ▼
                                      THRESHOLDS
                                      DEFINED?
                                     /        \
                                   NO          YES
                                   │            │
                            REPORT WITHOUT      ▼
                            NUMERIC PASS    THRESHOLDS
                                           SATISFIED?
                                           /       \
                                         NO         YES
                                         │           │
                                       FAIL       ACCEPTED
```

The `ACCEPTED` state means that the benchmark run satisfied the currently defined acceptance requirements. It does **not** mean that ChandraMap has demonstrated a particular level of scientific accuracy unless the applicable numerical threshold and measured result support that conclusion.

---

# 40. Evidence Required for a Defensible V1 Result

A strong V1 result should allow an independent reviewer to determine:

```text
What images were evaluated?
        ↓
What ground truth was used?
        ↓
Which points were used for fitting?
        ↓
Which points were held out?
        ↓
What candidate matches were produced?
        ↓
Which matches survived geometric verification?
        ↓
How were points spatially distributed?
        ↓
What transformation was fitted?
        ↓
Was sub-pixel refinement used?
        ↓
What was the independent check-point error?
        ↓
What happened under stress conditions?
        ↓
How long did the system take?
        ↓
What failed?
        ↓
Can the result be reproduced?
```

If these questions cannot be answered for a claimed benchmark result, the result should not be treated as fully defensible V1 evidence.

---

# 41. V1 Acceptance Checklist

## 41.1 Benchmark definition

- [ ] V1 benchmark specification identified.
- [ ] Applicable benchmark case identified.
- [ ] Evaluation mode identified.
- [ ] Applicable metrics identified.
- [ ] Applicable stress tests identified.

## 41.2 Data

- [ ] Source image identified.
- [ ] Reference image identified.
- [ ] Sensor/product identified.
- [ ] Image data are readable.
- [ ] Relevant metadata retained.

## 41.3 Ground truth

- [ ] Ground-truth source identified.
- [ ] Ground-truth version identified where applicable.
- [ ] Correspondences validated.
- [ ] Coordinate convention identified.
- [ ] Control points identified.
- [ ] Check points identified.
- [ ] Check points excluded from final fitting.
- [ ] Ambiguous points handled according to protocol.

## 41.4 Correspondence

- [ ] Candidate matches identified.
- [ ] Candidate matches distinguished from verified inliers.
- [ ] Geometric verification performed.
- [ ] Inlier count measured.
- [ ] Inlier ratio measured.
- [ ] Spatial coverage measured where applicable.

## 41.5 Geometry

- [ ] Initial transformation identified.
- [ ] RANSAC/geometric verification documented.
- [ ] Residuals inspected.
- [ ] Transformation appropriate to the geometry.
- [ ] Flexible warping not used to conceal weak correspondences.

## 41.6 Sub-pixel refinement

- [ ] Refinement performed only on verified points.
- [ ] Refinement procedure documented.
- [ ] Final transform refit where required.
- [ ] Accuracy reported in source-image pixels.
- [ ] No unsupported spatial-detail claim made.

## 41.7 Registration

- [ ] Final transformation identified.
- [ ] Registered output generated where applicable.
- [ ] Independent check-point evaluation performed.
- [ ] Check-point RMSE reported.
- [ ] Fitting-point error not substituted for check-point error.

## 41.8 Geospatial accuracy

- [ ] GSD verified where ground error is reported.
- [ ] Projection verified where required.
- [ ] Reference truth supports the conversion.
- [ ] Ground error reported only when meaningful.

## 41.9 Stress tests

- [ ] Easy pair evaluated where applicable.
- [ ] Sun-angle stress evaluated where applicable.
- [ ] Scale stress evaluated where applicable.
- [ ] Modality stress evaluated where applicable.
- [ ] Geometry stress evaluated where applicable.
- [ ] Low-feature terrain evaluated where applicable.
- [ ] Performance degradation reported honestly.

## 41.10 System evaluation

- [ ] Runtime measured where applicable.
- [ ] Execution environment identified.
- [ ] Failures recorded.
- [ ] Failure rate reported where applicable.
- [ ] Failure causes documented where possible.

## 41.11 Reproducibility

- [ ] Dataset identity retained.
- [ ] Ground-truth identity retained.
- [ ] Configuration retained.
- [ ] Metric definitions identified.
- [ ] Relevant random seeds/configuration retained.
- [ ] Evidence retained.

## 41.12 Reporting integrity

- [ ] No invented benchmark values.
- [ ] No invented accuracy.
- [ ] No invented RMSE.
- [ ] No invented thresholds.
- [ ] No unsupported percentage claims.
- [ ] No hidden benchmark failures.
- [ ] No unsupported metre-level claims.
- [ ] No visual-only acceptance claim.

---

# 42. Current Threshold Status

The current project evidence establishes **what should be measured and what constitutes a valid evaluation process**, but it does not establish complete numerical acceptance thresholds.

The following therefore remain explicitly unresolved:

```text
Minimum acceptable inlier count
        → To be defined

Minimum acceptable inlier ratio
        → To be defined

Maximum acceptable check-point RMSE
        → To be defined

Minimum acceptable spatial coverage
        → To be defined

Maximum acceptable ground error
        → To be defined

Maximum acceptable runtime
        → To be defined

Maximum acceptable failure rate
        → To be defined

Minimum stress-test success rate
        → To be defined

Maximum acceptable degradation under stress
        → To be defined

Retrieval Recall@1 threshold
        → To be defined

Retrieval Recall@5 threshold
        → To be defined
```

The absence of these thresholds is intentional. The project feedback provides the evaluation dimensions but does not provide authoritative V1 numerical acceptance limits.

---

# 43. What Counts as a Valid V1 Benchmark

A valid V1 benchmark is one in which:

```text
The data are valid
        AND
The ground truth is valid
        AND
The fitting/evaluation separation is preserved
        AND
Correspondences are geometrically verified
        AND
The final transformation is evaluated independently
        AND
Metrics are calculated correctly
        AND
Relevant stress behavior is reported
        AND
Evidence is retained
        AND
No unsupported claims are introduced
```

A valid benchmark does **not** automatically imply that the system has achieved a particular accuracy target.

---

# 44. What Counts as an Accepted Result

An accepted result is a **valid benchmark result whose applicable formally defined acceptance conditions have been satisfied**.

If a numerical threshold has not yet been formally defined, the result should retain its measured value and explicitly state:

```text
Acceptance threshold: To be defined
```

rather than converting the measurement into an unsupported success claim.

---

# 45. Scientific Integrity Requirement

The V1 benchmark MUST favor measurable evidence over presentation quality.

The project feedback repeatedly emphasizes:

- real image pairs;
- real inlier statistics;
- real spatial coverage;
- real check-point error;
- real runtime;
- honest failure reporting.

The recommended development path explicitly starts with one measurable source/reference pair and progresses toward broader stress testing and sensor coverage.

Therefore:

> **The benchmark's purpose is to establish what the system actually does, including where it fails, rather than to demonstrate that every pipeline stage appears complete.**

---

# 46. Final Acceptance Principle

The V1 acceptance standard can be summarized as:

```text
MEASURE
  ↓
VERIFY
  ↓
SEPARATE FIT FROM EVALUATION
  ↓
REPORT IN CORRECT UNITS
  ↓
RETAIN FAILURES
  ↓
PRESERVE EVIDENCE
  ↓
DO NOT INVENT THRESHOLDS OR RESULTS
```

The central acceptance requirement is:

> **ChandraMap V1 must be evaluated against trusted reference information using independent check points, geometrically verified correspondences, correctly defined metrics, and reproducible evidence.**

The project materials support this evaluation philosophy, while the exact numerical performance thresholds remain **To be defined** until they are formally established in the V1 benchmark specification.
