# ChandraMap Output Flow

ChandraMap produces scientific evidence, geometric results, evaluation metrics, diagnostic artifacts, benchmark summaries, and application-facing representations from lunar image correspondence and registration workflows.

This document defines:

> **what leaves ChandraMap processing boundaries, which outputs are authoritative, when an output is scientifically valid, who may consume it, and how its meaning and provenance must be preserved.**

The central output principle is:

> **One scientific registration attempt should produce one authoritative scientific result. Other outputs should derive from, summarize, visualize, aggregate, serialize, or diagnose that result without redefining its scientific meaning.**

Conceptually:

```text
Scientific Core
      ↓
Authoritative Scientific Result
      ↓
┌──────────────┬──────────────┬──────────────┬──────────────┐
│ Benchmark    │ Backend/API  │ Frontend     │ Artifacts    │
└──────────────┴──────────────┴──────────────┴──────────────┘
```

This document describes the **logical output architecture**.

It does not invent concrete result classes, schemas, filenames, directories, database tables, API fields, persistence technologies, cache implementations, or output formats where repository evidence does not establish them.

---

## 1. Purpose

This document answers:

- What can one registration attempt produce?
- Which outputs are intermediate?
- Which outputs are final?
- Which output is authoritative?
- Which outputs are diagnostic only?
- Which outputs are scientific metrics?
- Which outputs are derived rasters?
- Which outputs belong to benchmarking?
- Which outputs may leave the backend?
- Which outputs are presentation-only frontend state?
- Which outputs may be cached?
- Which outputs may be persisted?
- Which outputs can be regenerated?
- Which outputs must retain provenance?
- Which outputs remain valid when registration is rejected?
- Which outputs must not exist after an earlier stage fails?
- How do outputs move from the scientific core to benchmarks, applications, UI, reports, maps, or mosaics?
- How are transform direction, coordinate domains, metric units, and scientific status preserved?
- How do Benchmark V1–V4 share comparable output semantics?
- How does ChandraMap avoid treating a screenshot, overlay, or warped raster as scientific proof?

This document focuses on:

**output creation + output classification + output validity + output ownership + output distribution + output provenance + output lifecycle.**

---

## 2. Output-Flow Principles

### 2.1 One Authoritative Scientific Result

A pair-level registration attempt should conceptually produce one authoritative scientific outcome.

That result is the primary scientific handoff to:

- benchmark aggregation
- backend/application layers
- CLI or scripts
- frontend visualization
- research analysis
- downstream systems

Outer consumers may present or aggregate it.

They should not independently redefine it.

---

### 2.2 Intermediate Output Is Not Final Output

Examples include:

- keypoints
- descriptors
- candidate correspondences
- inlier masks
- preliminary transforms

These may be scientifically useful.

They are not automatically final registration results.

---

### 2.3 Output Meaning Must Remain Explicit

A scientifically important output should retain enough context to interpret it.

Examples:

```text
Transform
=
model
+
direction
+
source coordinate domain
+
reference coordinate domain
+
parameters
```

and:

```text
Metric
=
name
+
value
+
unit
+
population
+
coordinate domain where relevant
```

---

### 2.4 Rejection Is a Valid Output

A registration attempt that correctly concludes:

> insufficient reliable evidence

has produced a meaningful scientific result.

Rejection is not missing output.

---

### 2.5 Missing Does Not Mean Zero

Critical distinctions include:

```text
unavailable RMSE
≠
RMSE = 0
```

```text
no final transform
≠
identity transform
```

```text
retrieval not executed
≠
Recall@K = 0
```

---

### 2.6 Diagnostic Output Is Not Scientific Authority

A:

- match visualization
- registered preview
- overlay
- plot
- screenshot
- dashboard

may help humans interpret a result.

It must not override the authoritative scientific status or metrics.

---

### 2.7 Provenance Must Survive Distribution

Scientific meaning must not disappear as outputs move through:

```text
Core
→ Benchmark
→ Backend
→ Frontend
→ Report
```

Important semantics such as:

- source/reference identity
- transform direction
- units
- scientific status
- method/configuration identity

should remain preserved.

---

## 3. Output Architecture at a Glance

```text
Source + Reference
       ↓
Scientific Processing
       ↓
Candidate Correspondences
       ↓
Geometric Verification
       ↓
┌──────────────────────┬──────────────────────┐
│ Verified Inliers     │ Rejected Outliers    │
└──────────┬───────────┴──────────────────────┘
           ↓
     Initial Geometry
           ↓
 Optional Refinement
           ↓
     Final Transform
       ┌───┴──────────────┐
       ▼                  ▼
Registration         Evaluation
       │                  │
       └────────┬─────────┘
                ▼
      Scientific Decision
                ↓
        Accepted / Rejected
                ↓
     Authoritative Pair Result
        ┌───────┼─────────┬────────────┐
        ▼       ▼         ▼            ▼
   Benchmark  Backend  Diagnostics   Research
                │
                ▼
             Frontend
                │
        ┌───────┴────────┐
        ▼                ▼
      Map              Mosaic
   [downstream]      [downstream]
```

Not every output exists for every registration attempt.

Output validity depends on how far scientifically valid processing progressed.

---

## 4. Output Classification

ChandraMap outputs can be classified into the following major groups.

| Output Category                | Examples                                  |                                     Authoritative? |
| ------------------------------ | ----------------------------------------- | -------------------------------------------------: |
| Intermediate scientific output | Keypoints, descriptors, candidate matches |                                                 No |
| Verified scientific evidence   | Inliers, residual evidence                |                                Supporting evidence |
| Final geometry                 | Final transform                           |                         Yes within the pair result |
| Registration output            | Warped/registered source                  |                                            Derived |
| Scientific metrics             | RMSE, coverage, inlier ratio              |          Yes when produced by canonical evaluation |
| Scientific status              | Accepted/rejected/etc.                    |                                                Yes |
| Pair-level scientific result   | Complete registration outcome             |                    Primary project-level authority |
| Diagnostic artifact            | Match visualization, overlay, plot        |                                                 No |
| Benchmark pair record          | Pair result in benchmark context          |                   Yes for that benchmark execution |
| Benchmark aggregate            | Aggregate controlled benchmark output     |                   Yes for that benchmark execution |
| Backend/API representation     | Serialized scientific result              |                                     No new science |
| Frontend presentation          | Human-readable visualization              |                                                 No |
| Cache output                   | Rebuildable intermediate                  |                                                 No |
| Research output                | Experimental derived analysis             | Depends on experiment; not automatically canonical |
| Map/mosaic output              | Downstream geospatial presentation        |                          No new registration truth |

---

## 5. Authoritative Scientific Result

The authoritative pair-level scientific result is the primary output of one source/reference registration attempt.

Conceptually it should contain enough meaning to describe the following categories.

### Identity

Which:

- source
- reference
- pair/run context

were processed.

### Method Context

Which scientifically relevant:

- benchmark configuration
- representation
- method
- model/checkpoint where applicable

produced the result.

### Correspondence Evidence

What evidence exists, such as:

- candidate support
- verified support
- outlier information where relevant

### Geometry

Whether a final transform exists and what it means.

### Evaluation

Which metrics were produced and how they should be interpreted.

### Scientific Status

Whether the result is:

- accepted
- rejected
- invalid
- unsupported

or another project-defined state.

### Failure / Rejection Context

Why a valid accepted registration was not produced.

### Artifact References

Which derived diagnostics or outputs belong to the scientific result where artifact tracking exists.

No concrete result class or field names are defined here.

---

### Authoritative Does Not Mean Programming-Language Immutable

Here, **authoritative** means:

> the source of scientific meaning for that registration attempt.

It does not require a specific programming-language immutability mechanism.

---

## 6. Intermediate Scientific Outputs

Potential intermediate scientific outputs include:

- prepared source representation
- prepared reference representation
- keypoints
- local descriptors
- global descriptors where retrieval exists
- candidate correspondences
- filtered candidate correspondences
- inlier mask
- verified inliers
- rejected outliers
- preliminary transform
- residual arrays
- coverage-support information
- refined tie points

These may be useful for:

- debugging
- testing
- diagnostics
- ablation studies
- research analysis

but they should not automatically be treated as final project outputs.

---

### Intermediate ≠ Final

For example:

```text
Candidate Correspondences
≠
Final Verified Correspondences
```

and:

```text
Initial Transform
≠
Final Transform
```

when later refinement or refitting occurs.

---

## 7. Candidate Correspondence Outputs

Candidate correspondences are produced by a local matcher.

Conceptually:

```text
Source Point
     ↔
Reference Point
```

They represent hypotheses.

They may also carry matcher-specific evidence where applicable.

---

### Candidate Output Meaning

Candidate correspondences must not be labelled:

- correct
- validated
- final
- ground truth

before geometric verification.

---

### Candidate Summary

A pair result may retain summary information such as:

- candidate count
- matching-stage status

where defined.

The detailed candidate set may remain intermediate or diagnostic rather than always being persisted.

---

### Candidate Visualization

A candidate-match visualization is a diagnostic artifact.

It may be generated even when later geometry fails.

It must remain clearly labelled as **candidate** evidence.

---

## 8. Verified Inlier and Outlier Outputs

Geometric verification separates model-consistent correspondences from rejected candidates.

Conceptually:

```text
Candidate Correspondences
          ↓
Geometric Verification
          ↓
┌──────────────────┬──────────────────┐
│ Verified Inliers │ Outliers         │
└──────────────────┴──────────────────┘
```

---

### Verified Inlier Meaning

A verified inlier means:

> consistent with the selected geometric model under the active verification policy.

It does **not** mean:

> independently confirmed physical ground truth.

---

### Inlier Output Semantics

Where verified inliers leave the geometry stage, their meaning should remain interpretable through:

- source coordinate
- reference coordinate
- coordinate domains
- relationship to geometric verification
- relationship to final geometry

No serialization format is prescribed.

---

### Outlier Output

Outliers may be retained for:

- diagnostics
- matcher analysis
- failure analysis
- visualization

They must not contribute to accepted geometric support after being rejected.

---

## 9. Transform Outputs

The final transform is one of ChandraMap's most scientifically important outputs.

A transform is not fully described by matrix values alone.

Its meaning conceptually includes:

- model family
- mapping direction
- source coordinate domain
- destination/reference coordinate domain
- numerical parameters
- validity
- relationship to supporting geometric evidence

---

### 9.1 Initial Transform

An initial transform may be produced during robust geometric verification.

Conceptually:

```text
Candidate Correspondences
        ↓
RANSAC / Geometry
        ↓
Initial Transform
```

It may remain useful for diagnostics.

---

### 9.2 Final Transform

The final transform is:

> the geometry actually used for final registration and final evaluation.

In a simple baseline:

```text
Initial Transform
=
Final Transform
```

may be valid.

When refinement occurs:

```text
Initial Transform
      ↓
Tie-Point Refinement
      ↓
Transform Refit
      ↓
Final Transform
```

---

### 9.3 Transform Direction

Preferred conceptual direction:

```text
source coordinates
        ↓
final transform
        ↓
reference coordinates
```

That direction must remain preserved through:

- pair result
- backend serialization
- benchmark output
- frontend presentation
- reports

---

### 9.4 Stale Transform Must Not Escape

If verified tie points change during refinement:

```text
Refined Tie Points
+
Pre-Refinement Transform
```

must not be presented as one coherent final scientific result.

The final transform must reflect the final accepted point geometry.

---

## 10. Refinement Outputs

Where refinement is enabled, it may produce:

```text
Verified Tie Points
       ↓
Refinement
       ↓
Refined Tie Points
       ↓
Transform Refit
       ↓
Final Transform
```

The refined tie points may themselves be useful outputs for:

- diagnostics
- research
- evaluation

but the scientific geometry used downstream must be the refitted final transform.

---

### V1 Boundary

If canonical Benchmark V1 excludes advanced refinement:

- refined tie-point outputs do not belong to canonical V1
- refinement-specific metrics do not belong to canonical V1
- V1 should not emit later-version refinement artifacts merely for appearance

---

## 11. Registration and Warp Outputs

Registration consumes the final transform and may produce:

- registered source
- warped source
- registered preview
- transformed coordinates
- overlap representation

Exact file formats are outside this document.

---

### Registered Output Is Derived Data

Conceptually:

```text
Source
+
Reference Context
+
Final Transform
+
Registration Processing
        ↓
Registered Output
```

A registered output must never be confused with the original source mission product.

---

### Registered Output Provenance

Where relevant, it should remain possible to determine:

- which source produced it
- which reference defined the target
- which final transform was used
- which processing context created it

---

### Registered Image ≠ Accuracy Proof

Critical rule:

```text
Registered Raster Exists
≠
Registration Is Scientifically Accurate
```

A wrong transform can still generate a visually valid raster.

Scientific quality comes from evaluation, not the ability to write or render an image.

---

## 12. Evaluation Outputs

Evaluation produces scientific evidence about correspondence and registration quality.

Potential outputs include:

- candidate count
- verified-inlier count
- inlier ratio
- residuals
- spatial coverage
- fit RMSE
- independent check-point RMSE
- valid ground error
- retrieval metrics where retrieval exists
- runtime where defined

Exact formulas belong in metric/benchmark documentation.

---

### Metric Semantics

A scientific metric should retain enough information to answer:

```text
What metric?
What value?
Which unit?
Which coordinate domain?
Which population?
```

---

### No Bare RMSE

Avoid interpreting:

```text
RMSE = 0.8
```

without context.

A scientifically meaningful interpretation might instead specify:

```text
RMSE
0.8
source-image px
independent check-point population
```

when that is actually the metric definition.

---

## 13. Fit Metrics vs Independent Metrics

These outputs must remain distinct.

### Fit Metric

Computed using data involved in estimating/finalizing the transform.

Useful for:

- residual analysis
- geometric consistency
- model diagnostics

---

### Independent Metric

Computed using trusted data not used to fit the transform.

Useful for:

- independent accuracy estimation
- generalization assessment

---

### Do Not Collapse Them Into "Accuracy"

Avoid presenting:

```text
Fit RMSE
```

and:

```text
Independent Check-Point RMSE
```

as though they are interchangeable measurements.

---

## 14. Pixel and Ground-Unit Outputs

### Source-Image Pixels

A metric expressed in:

```text
source-image px
```

belongs to the source image coordinate system.

---

### Reference-Image Pixels

A metric expressed in:

```text
reference-image px
```

belongs to the reference image coordinate system.

These can have very different physical interpretation when GSD differs.

---

### Ground Error

Metre-level error should only be produced by authoritative scientific evaluation when the conversion is valid.

Conceptually:

```text
Pixel Error
+
Valid GSD / Projection / Geometry Context
        ↓
Scientifically Valid Ground Error
```

Backend or frontend code must not invent ground error from nominal instrument values.

---

### Sub-Pixel

`Sub-pixel` means:

> magnitude below one pixel in the specified image coordinate system.

It does not automatically mean:

> sub-metre.

---

## 15. Spatial Coverage Outputs

Spatial coverage may provide evidence about how well verified correspondences support the relevant region.

Where reported, coverage output should retain:

- coverage definition/method
- evaluated correspondence population
- relevant image or overlap domain

Avoid unexplained output such as:

```text
Coverage = 72%
```

when the meaning of that percentage is unknown.

Coverage is supporting geometric evidence.

It is not independent ground truth.

---

## 16. Scientific Status Output

Scientific status should be explicit.

Potential conceptual states include:

- Accepted
- Rejected
- Invalid Input
- Unsupported
- Error

Actual project vocabulary should take precedence where defined.

---

### Status Must Not Be Inferred

Do not infer acceptance from:

- registered artifact existence
- transform matrix existence
- successful process exit
- HTTP success
- backend job completion

The scientific result itself should state the scientific outcome.

---

### Rejection Is a Final Scientific Output

Conceptually:

```text
Valid Inputs
      ↓
Scientific Processing
      ↓
Insufficient Reliable Evidence
      ↓
REJECTED
```

This may represent correct scientific behavior.

---

### Rejection Reason

Where available, preserve the reason structurally.

Potential conceptual categories include:

- insufficient features
- insufficient candidate support
- no valid geometry
- degeneracy
- poor coverage
- quality requirement not satisfied

No exact enum is defined here.

---

## 17. Rejected, Invalid, and Error Outputs

These outcomes must remain distinguishable.

### Rejected

The scientific method executed sufficiently to conclude that trustworthy registration was not established.

---

### Invalid Input

The input could not enter normal scientific processing because it was malformed, unsupported, or otherwise invalid.

---

### Unsupported

The requested representation or capability is outside the active supported methodology.

---

### Software / Dependency Error

Execution failed unexpectedly because of:

- implementation defect
- missing dependency
- runtime/resource problem
- other operational error

This is not the same as scientific rejection.

---

### Failure Must Not Fabricate Science

Do not create fake outputs such as:

```text
Identity Transform
RMSE = 0
Accepted = true
```

after scientific or software failure.

---

## 18. Output Validity Model

Different outputs become valid at different stages.

| Output                    | Becomes Valid When                           |
| ------------------------- | -------------------------------------------- |
| Input identity            | Input/pair identity is established           |
| Prepared representation   | Representation/preprocessing succeeds        |
| Keypoints/descriptors     | Feature extraction succeeds                  |
| Candidate correspondences | Matching succeeds                            |
| Candidate visualization   | Candidate set exists                         |
| Verified inliers          | Geometry successfully classifies candidates  |
| Outliers                  | Geometry successfully classifies candidates  |
| Initial transform         | Geometry estimates a valid preliminary model |
| Final transform           | Final geometry is established                |
| Registered output         | Valid final transform can be applied         |
| Fit metrics               | Relevant fit data and geometry exist         |
| Independent metrics       | Final transform and independent truth exist  |
| Scientific status         | Decision/failure semantics are known         |
| Pair result               | Scientific attempt is finalized              |

---

### Missing Output Is Legitimate

Not every run should produce every output.

For example:

```text
No independent check points
→ no independent check-point metric
```

```text
Geometry failure
→ no final transform
```

```text
V1
→ no retrieval Recall@K
```

---

## 19. Output State Matrix

| Output                    |                    Accepted |                             Rejected After Valid Geometry |                 Geometry Failure |                     Invalid Input |
| ------------------------- | --------------------------: | --------------------------------------------------------: | -------------------------------: | --------------------------------: |
| Source/reference identity |                         Yes |                                                       Yes |                              Yes |                   Where available |
| Candidate summary         |                         Yes |                                                       Yes | Usually yes if matching occurred |                                No |
| Verified inliers          |                         Yes |                                                 Often yes |                         Maybe/No |                                No |
| Outliers                  |         Optional diagnostic |                                       Optional diagnostic |                            Maybe |                                No |
| Final transform           |                         Yes |      May exist if rejection occurs at later quality stage |                               No |                                No |
| Registered output         |                Yes/optional | Optional diagnostic only where scientifically appropriate |                               No |                                No |
| Fit metrics               |                         Yes |                                                 May exist |                       Limited/No |                                No |
| Independent metrics       | If independent truth exists |                           If valid final transform exists |                               No |                                No |
| Scientific status         |                         Yes |                                                       Yes |                              Yes |                               Yes |
| Rejection/failure reason  |     Optional/Not applicable |                                                       Yes |                              Yes |                               Yes |
| Diagnostic artifacts      |                    Optional |                                                  Optional |     Optional partial diagnostics | Usually no scientific diagnostics |

This table is conceptual.

Actual output availability depends on the active methodology and implementation.

---

## 20. Diagnostic Artifacts

Potential diagnostic artifacts include:

- keypoint visualization
- raw/candidate match visualization
- verified-inlier visualization
- outlier visualization
- coverage visualization
- residual plot
- registered preview
- source/reference overlay
- before/after visualization

Their purpose is:

- debugging
- interpretation
- communication
- research analysis

---

### Artifact ≠ Scientific Result

A diagnostic artifact must not become the only record of:

- transform
- status
- RMSE
- coverage
- correspondence classification

when structured scientific state exists.

---

### Artifact Validity

Artifacts should correspond to the exact scientific state they depict.

For example:

```text
Inlier Visualization
```

must correspond to:

```text
the same candidate set
+
the same inlier classification
+
the same run/configuration
```

---

## 21. Pair-Level Result Output

The pair-level result is the main scientific handoff between the Core Engine and outer systems.

Conceptually:

```text
Scientific Core
      ↓
Pair-Level Scientific Result
      ↓
┌──────────────┬──────────────┬──────────────┬──────────────┐
│ Benchmark    │ Backend      │ CLI/Scripts  │ Research     │
└──────────────┴──────────────┴──────────────┴──────────────┘
```

The pair result should remain scientifically coherent.

Do not mix:

- transform from one run
- metrics from another run
- artifact from a third run

into one result.

---

## 22. Benchmark Output Flow

Benchmarking consumes controlled pair-level results.

Conceptually:

```text
Benchmark Pair Population
        ↓
Configured Scientific Runs
        ↓
Pair-Level Results
        ↓
Benchmark Collector
        ↓
Controlled Aggregation
        ↓
Aggregate Results
        ↓
Reports / Tables / Plots
```

---

### Pair Result vs Aggregate Result

These are different layers.

```text
Pair RMSE
≠
Benchmark Mean / Median RMSE
```

```text
Pair Rejected
≠
Benchmark Rejection Rate
```

---

### Rejected Cases Remain Visible

Benchmark aggregation must preserve unsuccessful cases according to benchmark methodology.

Do not silently remove rejected pairs simply because no final accuracy value exists.

---

### Conditional Accuracy

If accuracy statistics are calculated only over accepted cases:

> that condition must remain visible.

Successful-only accuracy must not be presented as complete benchmark reliability.

---

### Benchmark Population

Aggregate results must remain associated with:

- evaluated pair population
- configuration
- metric definition

Changing the benchmark population changes the scientific meaning of the aggregate.

---

## 23. Benchmark Reports

Benchmark reports may present:

- tables
- figures
- summaries
- per-category analysis
- failure distributions

Reports are human-readable analytical outputs.

They are not automatically the underlying source of benchmark truth.

---

### Report Generation Principle

Prefer conceptually:

```text
Structured Benchmark Results
        ↓
Report Generation
        ↓
Report
```

rather than:

```text
Manually Edited Report
        ↓
Becomes Scientific Truth
```

---

### Demo ≠ Benchmark

A selected successful example may be useful for demonstration.

It is not automatically representative benchmark evidence.

---

## 24. Canonical V1 Output Flow

Benchmark V1 should remain output-compatible with its classical known-overlap scope.

Conceptually:

```text
Known Source
+
Known Overlapping Reference
        ↓
Canonical V1 Processing
        ↓
Candidate Match Evidence
        ↓
Verified Inlier Evidence
        ↓
Final Affine / Homography Geometry
        ↓
Registered Output
        ↓
V1 Evaluation Evidence
        ↓
Accepted / Rejected
        ↓
Authoritative V1 Pair Result
        ↓
┌──────────────┬──────────────┬──────────────┐
│ Benchmark    │ Diagnostics  │ Backend / UI │
└──────────────┴──────────────┴──────────────┘
```

---

### V1 Scientific Output Priorities

Canonical V1 outputs should conceptually prioritize:

1. source/reference identity
2. candidate correspondence evidence
3. verified-inlier evidence
4. final affine/homography transform where valid
5. registration output/preview where appropriate
6. quality metrics
7. accepted/rejected status
8. rejection/failure context
9. reproducibility/provenance information

A final mosaic is not the core V1 scientific output.

---

### V1 Does Not Emit Later-Stage Outputs

Canonical V1 should not generate methodology-dependent outputs for stages it does not execute.

Examples include:

- global retrieval candidates
- FAISS search results
- Recall@K
- learned-matcher-specific outputs
- advanced refinement outputs
- DEM-aware geometry outputs

unless canonical V1 scope is explicitly revised.

---

## 25. V2, V3, and V4 Output Extensions

Later benchmark configurations may extend shared result semantics.

These are conceptual research extensions, not current implementation claims.

---

### V2

Potential additional context may include:

- sensor-aware representation identity
- selected scale/pyramid context
- GSD-aware processing context
- sensor-specific diagnostics

The common final scientific result should still preserve:

- status
- geometry
- metrics
- units
- provenance

---

### V3

Potential additions may include:

- retrieval candidates
- retrieval similarity evidence
- Recall@K
- learned matcher/model identity
- advanced correspondence diagnostics

Retrieval outputs remain separate from final registration outputs.

---

### V4

Potential additions may include:

- refined tie-point information
- local/piecewise geometry context
- terrain-aware geometry context
- uncertainty information
- calibrated quality/confidence where scientifically established
- stronger failure classification

These extensions should not destroy shared pair-result semantics.

---

### Benchmark Version ≠ Output Schema Version

Do not assume:

```text
Benchmark V1
→ Result Schema V1
```

```text
Benchmark V2
→ Result Schema V2
```

unless the repository deliberately implements schema versioning.

Benchmark versions describe research configurations.

They are not automatically:

- API versions
- schema versions
- software releases

---

## 26. Retrieval Outputs

Where global retrieval exists, retrieval may produce:

- ranked reference candidate identifiers
- retrieval similarity/distance values
- Top-K candidates
- reference metadata associations

These outputs answer:

> Which reference regions should local registration try?

They do not answer:

> Is the source correctly registered?

---

### Retrieval Result ≠ Registration Result

```text
Top-1 Candidate Found
≠
Accurate Registration
```

A correct retrieval candidate still requires local correspondence, geometry, and evaluation.

---

### FAISS Output

Where FAISS is used, its direct role is:

```text
Query Vector
      ↓
Vector Search
      ↓
Candidate IDs / Distances
```

FAISS does not directly output:

- point correspondences
- inlier masks
- affine/homography transforms
- registered images
- RMSE

---

### Retrieval Similarity ≠ Registration Confidence

A high vector-similarity score must not be presented as:

> probability that the final registration is correct.

---

## 27. Backend / API Output Flow

The backend/application layer may expose an authoritative scientific result through a transport representation.

Conceptually:

```text
Scientific Core Result
        ↓
Backend Application Layer
        ↓
Semantic-Preserving Translation
        ↓
Transport Representation
        ↓
Client
```

---

### Backend May Serialize, Not Reinterpret

Backend translation may preserve or format:

- scientific status
- transform semantics
- metrics
- units
- provenance
- rejection reason
- artifact references

It should not independently:

- recalculate RMSE
- recalculate coverage
- reclassify inliers
- change scientific acceptance
- create fake confidence values

---

### Request Status vs Scientific Status

These dimensions remain distinct.

Example:

```text
Application Request:
Completed
```

```text
Scientific Result:
Rejected
```

This is valid.

---

## 28. Frontend Output Flow

Frontend output is a human-facing presentation of authoritative application/scientific state.

Conceptually:

```text
Backend Result
      ↓
Frontend Data Layer
      ↓
View Model / Formatting
      ↓
Scientific Visualization
```

Possible presentation outputs include:

- result summary
- metrics
- candidate-match visualization
- inlier/outlier visualization
- registered preview
- benchmark comparison
- geospatial/map view
- mosaic view

---

### Frontend May Format

The frontend may derive:

- labels
- tables
- charts
- visible subsets
- formatting
- zoom/pan state

---

### Frontend Must Not Redefine Science

It must not alter:

- scientific status
- transform
- RMSE
- coverage
- inlier classification
- benchmark truth

---

### View State ≠ Scientific Output

Examples of frontend-only state include:

- zoom
- pan
- selected tab
- table filter
- visible layer

These are not scientific results.

---

## 29. CLI and Human-Readable Output

Where a CLI or script interface presents results, its output may summarize:

- scientific status
- key metrics
- transform information
- artifact locations/references

Human-readable terminal text should not become the only machine-readable record of scientific results when structured output exists.

---

### Logs Are Not Results

Avoid architectures that compute benchmark aggregates by parsing console lines such as:

```text
RMSE: ...
```

Operational logs and scientific results serve different purposes.

---

## 30. Log Output

Logs may include:

- processing stages
- warnings
- timings
- diagnostic messages
- failure context
- software errors

Logs are useful for operations and debugging.

They are not authoritative scientific output.

---

### Logs Must Not Become a Database

Do not rely on:

```text
console output
```

as the canonical storage location for:

- pair results
- benchmark metrics
- transform semantics

---

## 31. Map and Mosaic Downstream Outputs

Maps and mosaics are downstream products.

They should consume scientifically meaningful registration outputs.

---

### Map Output

Conceptually:

```text
Scientific Result
        +
Valid Lunar Geospatial Context
        ↓
Map Representation
        ↓
Human Visualization
```

The map is a visualization.

It does not become new correspondence truth.

---

### Lunar Context

Map outputs must preserve appropriate lunar geospatial semantics.

Do not silently label lunar coordinates as Earth WGS84.

---

### Mosaic Output

Conceptually:

```text
Accepted Registrations
        ↓
Registered Products + Geospatial Context
        ↓
Mosaic Composition
        ↓
Mosaic Output
```

---

### Canonical Mosaic Input

Trusted/canonical mosaic generation should not silently include rejected registrations.

An experimental workflow may intentionally test such behavior, but it must be labelled accordingly.

---

### Mosaic ≠ Registration Proof

A visually smooth mosaic can still conceal local registration error.

Pair-level scientific registration evidence remains important.

---

## 32. Cache and Temporary Outputs

Some outputs may exist primarily to reduce repeated computation.

Potential conceptual cache data includes:

- prepared representations
- pyramid levels
- descriptors
- global descriptors
- reference tiles
- vector indexes

This document does not claim which categories are currently cached.

---

### Cache Requirements

Caches should be:

- rebuildable
- derived
- non-authoritative
- configuration-aware where necessary

---

### Cache Is Not Scientific Truth

Deleting cache data must not erase the only record of:

- benchmark status
- final scientific result
- manually altered scientific state

---

## 33. Output Persistence and Lifecycle

Not every output needs permanent storage.

A useful conceptual classification is:

### Ephemeral Output

Exists primarily during one execution.

Examples may include:

- temporary point arrays
- intermediate residual arrays
- temporary representations

---

### Cache Output

Retained to improve performance.

Rebuildable from authoritative inputs and methodology.

---

### Persistable Scientific Output

May be worth storing for:

- reproducibility
- benchmarking
- scientific comparison
- downstream application use

Potentially includes pair-level scientific results.

---

### Presentation Artifact

A regenerable visualization or report.

---

### External Source Data

Original mission/reference data obtained from an external authoritative source.

---

### No Retention Policy Is Assumed

This document does not define:

- storage duration
- cleanup schedule
- storage backend
- archiving policy

unless another verified project document establishes those details.

---

## 34. Output Provenance and Reproducibility

A scientifically useful output should ideally allow the project to answer:

- Which source produced this?
- Which reference was used?
- Which representation was used?
- Which configuration/method produced it?
- Which final transform belongs to it?
- Which metric definitions generated the reported values?
- Which scientific status belongs to it?
- Which artifacts were derived from it?

Conceptually:

```text
Input Identity
+
Configuration
+
Method
+
Code Revision where tracked
+
Model / Checkpoint where applicable
+
Seed where relevant
        ↓
Scientific Execution
        ↓
Authoritative Result
        ↓
Artifacts / Reports / Application Views
```

---

### Minimum Provenance Principle

Do not require every run to store every intermediate byte.

Preserve enough information to:

- interpret
- audit
- reproduce where practical
- compare scientifically

the result.

---

### Configuration as Provenance

Important scientific configuration is part of output provenance.

A transform generated under one matcher/geometry configuration should not be treated as interchangeable with one generated under another without evidence.

---

### Model / Checkpoint Identity

Where learned methods are used, model/checkpoint identity may materially affect results.

A result labelled only:

```text
LoFTR
```

may be insufficient if relevant model variants/checkpoints differ.

---

### Seed Information

Where randomness materially affects a result, seed information may be relevant provenance.

Do not claim universal determinism if it is not guaranteed.

---

## 35. Output Invalidation

Outputs depend on upstream scientific state.

Changing upstream state may invalidate downstream outputs.

| Changed Scientific State     | Outputs That May Need Regeneration                            |
| ---------------------------- | ------------------------------------------------------------- |
| Source/reference product     | All downstream outputs                                        |
| Representation/preprocessing | Features, matches, geometry, registration, metrics, artifacts |
| Scale/pyramid level          | Features, coordinates, matches, geometry, registration        |
| Matcher                      | Candidate matches onward                                      |
| Candidate filtering          | Geometry onward                                               |
| Transform model/policy       | Transform, registration, evaluation, artifacts                |
| Refined tie points           | Final transform, registration, evaluation                     |
| Metric definition            | Metrics, benchmark aggregates, reports                        |
| Benchmark population         | Benchmark aggregates and reports                              |
| Model/checkpoint             | Learned-method outputs onward                                 |

---

### Output Coherence

Outputs from one scientific attempt should remain mutually consistent.

Avoid combinations such as:

```text
new metric
+
old transform
+
old inlier visualization
```

from different executions.

---

### Run / Pair Coherence

Where the project tracks execution identity, outputs belonging to one run should remain associated with the same:

- source/reference pair
- configuration
- scientific execution

No run-ID schema is defined here.

---

## 36. Serialization Boundaries

Outputs may cross:

- process boundaries
- file boundaries
- backend/API boundaries
- frontend boundaries

Serialization must preserve scientific meaning.

---

### Semantics That Should Survive

Where applicable:

- source/reference identity
- scientific status
- transform model
- transform direction
- coordinate domain
- metric units
- metric population
- representation identity
- rejection reason
- provenance

---

### Transform Serialization Anti-Pattern

Bad conceptual output:

```text
[[...], [...], [...]]
```

with no explanation.

Better semantic representation:

```text
Model: Homography
Direction: Source → Reference
Coordinate domain: image coordinates
Parameters: matrix values
```

This is conceptual documentation, not a schema.

---

### Metric Serialization Anti-Pattern

Bad:

```text
RMSE = 0.9
```

Better scientific meaning:

```text
Metric: RMSE
Unit: source-image px
Population: independent check points
```

where applicable.

---

## 37. Output Ownership

| Output                    | Primary Conceptual Owner  | Main Consumers                          |
| ------------------------- | ------------------------- | --------------------------------------- |
| Candidate correspondences | Matching                  | Geometry, diagnostics                   |
| Verified inliers          | Geometry                  | Refinement, evaluation, diagnostics     |
| Outliers                  | Geometry                  | Diagnostics/research                    |
| Initial transform         | Geometry                  | Geometry diagnostics/refinement         |
| Final transform           | Geometry/refinement       | Registration, evaluation, result        |
| Registered output         | Registration              | Evaluation, backend/UI/research         |
| Pair metrics              | Evaluation                | Decision, benchmark, backend/UI         |
| Scientific status         | Decision/result layer     | Benchmark, backend, CLI, frontend       |
| Pair scientific result    | Core/result layer         | Benchmark, backend, CLI/research        |
| Retrieval candidates      | Retrieval                 | Candidate-resolution/local registration |
| Benchmark aggregate       | Benchmark layer           | Reports, UI/research                    |
| Diagnostic artifact       | Artifact/diagnostic layer | UI, docs, research                      |
| Application/job state     | Backend                   | Frontend                                |
| View state                | Frontend                  | Frontend only                           |

This table defines conceptual ownership rather than concrete classes/modules.

---

## 38. Output Authority

| Output Type                       |             Authoritative Scientific Truth? |                                    Rebuildable? |
| --------------------------------- | ------------------------------------------: | ----------------------------------------------: |
| Original source/reference product |                  External scientific source |                           Controlled externally |
| Authoritative pair result         |                    Yes for that project run |       Depends on preserved inputs/configuration |
| Final transform                   |                  Yes within its pair result | Usually recomputable with sufficient provenance |
| Canonical metrics                 |         Yes within their defined evaluation |                            Usually recomputable |
| Scientific status                 |                Yes for the finalized result |          Recomputable from same evidence/policy |
| Registered raster                 |                   Derived scientific output |                                         Usually |
| Registered preview                |                                          No |                                             Yes |
| Match/inlier visualization        |                                          No |                                             Yes |
| Benchmark aggregate               | Yes for that benchmark execution/population |                                    Recomputable |
| Benchmark report                  |                     No new scientific truth |                                             Yes |
| Cache                             |                                          No |                                             Yes |
| Frontend view state               |                                          No |                                             Yes |

---

## 39. Security and Trust Boundaries

Outputs leaving the scientific process/application boundary should avoid exposing:

- secrets
- credentials
- tokens
- unnecessary environment details
- unnecessary absolute local paths
- unsafe internal stack traces

Detailed security controls belong in dedicated security documentation.

---

### Untrusted Output Text

Some displayed output may originate from external data such as:

- filenames
- product metadata
- provider text

Application/frontend layers must continue to treat this as external data rather than trusted executable markup.

---

### Internal Errors

Detailed internal diagnostics may be appropriate for development logs.

Externally exposed error output should avoid unnecessarily leaking internal runtime details.

---

## 40. Output Size and Performance

Potentially large outputs include:

- registered rasters
- large correspondence sets
- residual arrays
- retrieval candidate collections
- benchmark reports

A mature architecture may distinguish between:

### Summary Output

Enough information for:

- status
- list views
- quick result comparison

### Detailed Scientific Output

Potentially includes:

- correspondence evidence
- geometry
- complete metrics

### Heavy Artifacts

Examples:

- large rasters
- plots
- detailed visualizations

No concrete transfer/storage mechanism is prescribed.

---

### Do Not Embed Everything Everywhere

A list view or lightweight API response should not necessarily carry:

- full-resolution imagery
- every correspondence
- every diagnostic artifact

when consumers only require summary information.

This is architectural guidance, not a claim about current contracts.

---

## 41. Testing Output Semantics

Output-oriented testing should verify scientific consistency at important boundaries.

Conceptual test cases include:

- accepted result contains coherent final geometry
- rejected result does not fabricate successful science
- invalid input does not produce registration metrics
- transform direction is preserved
- metric units remain preserved
- source/reference pixel domains remain distinct
- fit and check metrics remain separate
- refinement output uses the refitted transform
- serialized scientific status remains unchanged
- artifact references correspond to the same scientific result
- benchmark aggregation preserves rejected cases
- frontend presentation does not alter authoritative values

Detailed testing policy belongs in the project's testing documentation.

---

### Synthetic Output Testing

Synthetic fixtures may test:

- transform serialization meaning
- unit preservation
- state transitions
- failure behavior

Synthetic values are test data.

They are not benchmark evidence.

---

## 42. Output-Flow Invariants

The following rules should remain true unless output architecture is deliberately revised.

1. Every final scientific output remains associated with source/reference identity.

2. Candidate matches are never labelled verified before geometry.

3. Verified inliers are never labelled independent ground truth.

4. Outliers remain distinct from accepted geometric support.

5. Initial and final transforms remain distinguishable when they differ.

6. Transform model and direction remain explicit.

7. Transform coordinate domains remain explicit.

8. Refined points require final transform refitting.

9. Final registered output uses the final transform.

10. Final evaluation uses the final transform.

11. Fit metrics remain separate from independent evaluation metrics.

12. Every scientific metric retains meaningful units.

13. Every metric retains its relevant evaluated population.

14. Source-image pixels and reference-image pixels are not interchangeable.

15. Ground metres exist only after scientifically valid conversion.

16. Sub-pixel does not automatically imply sub-metre.

17. Scientific status is explicit.

18. Rejection is a valid final scientific result.

19. Invalid input is not scientific rejection.

20. Software/dependency error is not scientific rejection.

21. Missing output is not represented as zero.

22. Missing transform is not represented as identity.

23. Warp success does not prove scientific acceptance.

24. Diagnostic artifacts are not authoritative scientific truth.

25. Frontend visualization cannot overwrite scientific result values.

26. Backend serialization cannot independently recalculate science.

27. Benchmark aggregation preserves rejected/failed cases according to benchmark methodology.

28. Pair-level results remain distinct from benchmark aggregates.

29. Reports derive from underlying scientific results where possible.

30. Cache output is never the authoritative scientific result.

31. Output lineage remains traceable where scientifically important.

32. Artifacts from different runs/configurations must not be mixed into one result.

33. V1 does not emit retrieval metrics for stages it does not execute.

34. Retrieval output is distinct from registration output.

35. Retrieval similarity is not registration confidence.

36. V1–V4 share stable result semantics where scientifically practical.

37. Benchmark version is not automatically software/API/schema version.

38. Map and mosaic outputs remain downstream.

39. Frontend view state remains distinct from scientific result state.

40. Backend job/application state remains distinct from scientific status.

41. Logs remain operational diagnostics rather than scientific truth.

42. Unknown or unavailable scientific output remains explicitly absent.

43. Scientific metrics from different definitions are not silently mixed.

44. Current implementation and target output architecture remain clearly distinguishable.

---

## 43. Output-Flow Anti-Patterns

### Screenshot-as-Result

Only a screenshot survives while transform, metrics, status, and provenance are lost.

---

### Identity-on-Failure

No transform was estimated, but an identity matrix is inserted to satisfy downstream code.

---

### Zero-on-Missing

Unavailable values become:

```text
0
```

despite zero having real scientific meaning.

---

### Candidate-as-Verified

Matcher-generated hypotheses are labelled final correspondences.

---

### RANSAC-as-Ground-Truth

Model-consistent inliers are presented as independently verified physical truth.

---

### Stale Transform

Refined tie points leave the system with a pre-refinement transform labelled final.

---

### New Metric + Old Artifact

A report mixes:

- newly computed metrics
- an old registration image
- an old inlier visualization

from different executions.

---

### Unitless Metric

```text
RMSE = 0.8
```

without coordinate/unit meaning.

---

### Anonymous Matrix

A transform is exposed without:

- model family
- direction
- coordinate context

---

### Pretty Overlay = Success

A visually convincing artifact overrides a rejected scientific status.

---

### Backend-Recomputed Science

An application/backend layer implements a different RMSE, coverage, or acceptance calculation.

---

### Frontend-Recomputed Science

Browser code creates another definition of:

- RMSE
- inliers
- scientific confidence
- acceptance

---

### Logs-as-Database

Benchmark results are reconstructed by scraping console messages.

---

### Cache-as-Truth

Deleting cache destroys the only scientific result.

---

### Dropped Failures

Rejected pairs disappear before benchmark aggregation.

---

### V4 = Best

Benchmark ordering becomes an unsupported performance ranking.

---

### Report-Only Benchmark

A formatted report exists with no traceable pair-level result data behind it.

---

### Manual Metric Editing

Scientific values are edited manually in a report or spreadsheet without preserving provenance.

---

### Derived Artifact as Original Data

A registered or normalized raster is later treated as though it were an untouched mission product.

---

## 44. Adding a New Output

Before introducing a new output, ask:

1. Is this scientifically authoritative or diagnostic?

2. Which stage creates it?

3. Which consumers require it?

4. Does it need source/reference identity?

5. Does it need coordinate-domain information?

6. Does it need units?

7. Does it require provenance?

8. Is it valid for rejected runs?

9. Is it optional?

10. Can it be regenerated?

11. Does it belong in the pair-level result or only as an artifact?

12. Does benchmark aggregation need it?

13. Does exposing it unnecessarily couple backend or frontend to implementation internals?

14. Is it shared across V1–V4 or methodology-specific?

15. Does an existing output already represent the same scientific concept?

---

### Output Minimalism

Do not serialize every temporary array merely because it exists.

Preserve enough output to support:

- scientific interpretation
- evaluation
- reproducibility
- debugging
- controlled comparison

without turning every internal computation into a permanent public contract.

---

## 45. Output Evolution and Compatibility

Output semantics should evolve deliberately.

Changes may affect:

- benchmark comparability
- stored historical results
- backend expectations
- frontend rendering
- research analysis
- generated reports

---

### Scientifically Breaking Output Changes

A change may be scientifically breaking even if software serialization remains syntactically compatible.

Examples include changing:

- RMSE definition
- coordinate convention
- transform direction
- acceptance semantics
- coverage definition
- benchmark population

---

### Metric Changes

If a metric definition changes:

```text
old metric
≠
new metric
```

even when both use the same name.

Historical and new values should not be silently combined.

---

### V1 Stability

Canonical Benchmark V1 should retain stable:

- method semantics
- result interpretation
- metric meaning

so later configurations can be compared meaningfully.

---

### Schema Versioning

Only document an explicit output/result schema version if the repository actually implements one.

Benchmark V1–V4 do not automatically imply schema versions.

---

## 46. Negative and Rejected Results

Negative results are scientific outputs when generated through valid methodology.

Examples might eventually include:

- method produced insufficient correspondence
- geometry could not be established
- registration was rejected
- later method did not improve over baseline

Do not remove such outcomes merely because they make a demo less visually impressive.

---

### Portfolio / Demonstration Use

Demonstration material should not hide:

- rejection
- failed cases
- weak ground truth
- unsupported capability

when presenting scientific claims.

A selected demo case is not automatically representative of benchmark performance.

---

## 47. Research and Oracle Outputs

Research experiments may generate additional:

- intermediate diagnostics
- ablation outputs
- alternate transforms
- alternative metrics
- visual analyses

These outputs must remain distinguishable from canonical benchmark results.

---

### Oracle Outputs

If privileged ground truth is used to select:

- matcher
- threshold
- candidate
- transform model

the resulting output should be clearly identified as:

- oracle
- diagnostic
- research-only

It must not be merged into normal inference results.

---

## 48. Current vs Target Output Architecture

This document describes the logical output semantics ChandraMap should preserve.

It does not claim that all described categories are currently implemented as:

- persisted files
- concrete result objects
- API responses
- frontend components
- benchmark exporters
- caches
- databases

In particular, concepts such as:

- global retrieval output
- advanced refinement output
- model/checkpoint provenance
- advanced V4 uncertainty output
- long-term result persistence

should be treated as architectural/research concepts unless repository evidence establishes current support.

Actual repository contracts take precedence over this conceptual model.

---

## 49. Maintenance Rules

Update this document when:

- authoritative pair-result semantics change
- final transform semantics change
- new canonical metrics are introduced
- metric unit/population meaning changes
- refinement becomes active in a canonical benchmark
- retrieval outputs become integrated
- benchmark aggregation semantics change
- backend result translation changes materially
- new artifact categories become official
- map/mosaic output architecture changes
- persistence strategy changes
- result/schema versioning is explicitly introduced
- output provenance requirements change

Do not update this document for every implementation refactor that preserves output semantics.

---

## 50. Related Documents

- [`system-overview.md`](./system-overview.md) — overall ChandraMap system responsibilities
- [`core-engine-architecture.md`](./core-engine-architecture.md) — reusable scientific-engine responsibilities
- [`data-flow.md`](./data-flow.md) — scientific data movement between processing boundaries
- [`v1-pipeline.md`](./v1-pipeline.md) — ordered canonical Benchmark V1 execution
- [`module-map.md`](./module-map.md) — repository responsibility ownership
- [`backend-architecture.md`](./backend-architecture.md) — application/backend exposure of scientific results
- [`frontend-architecture.md`](./frontend-architecture.md) — scientific-result visualization and frontend state
- [`../project/overview.md`](../project/overview.md) — overall project context
- [`../project/terminology.md`](../project/terminology.md) — canonical project terminology
- [`../project/assumptions.md`](../project/assumptions.md) — scientific assumptions
- [`../project/limitations.md`](../project/limitations.md) — scientific and methodological limitations
- [`../project/v1-scope.md`](../project/v1-scope.md) — canonical human-facing Benchmark V1 boundary
- [`.ai/architecture/SYSTEM_OVERVIEW.md`](../../.ai/architecture/SYSTEM_OVERVIEW.md) — deeper maintainer/AI architecture context
- [`.ai/architecture/PIPELINE.md`](../../.ai/architecture/PIPELINE.md) — broader processing-stage order
- [`.ai/architecture/DATA_FLOW.md`](../../.ai/architecture/DATA_FLOW.md) — deeper scientific data-flow semantics
- [`.ai/architecture/MODULE_MAP.md`](../../.ai/architecture/MODULE_MAP.md) — AI-oriented repository responsibility mapping
- [`.ai/context/V1_SCOPE.md`](../../.ai/context/V1_SCOPE.md) — detailed canonical V1 contract
- [`.ai/development/BENCHMARK_RULES.md`](../../.ai/development/BENCHMARK_RULES.md) — benchmark comparison and result-governance rules
- [`.ai/development/TESTING_RULES.md`](../../.ai/development/TESTING_RULES.md) — software/scientific testing expectations
- [`.ai/development/DOCUMENTATION_RULES.md`](../../.ai/development/DOCUMENTATION_RULES.md) — documentation and scientific-claim standards

ChandraMap outputs should become easier to distribute as they move outward from the scientific core, but they must never become less scientifically meaningful.

A transform should remain a directed transform. A metric should retain its units and population. A rejection should remain a rejection. A registered image should remain a derived visualization rather than evidence by itself. And every downstream benchmark, backend response, frontend view, artifact, map, or mosaic should remain traceable to the scientific result that produced it.

<!-- Source specification: :contentReference[oaicite:0]{index=0} -->
