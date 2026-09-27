# Ground Truth

> **ChandraMap — Ground-Truth Data Documentation**
> **Path:** `data/ground_truth/`

ChandraMap is a lunar image correspondence and registration system for matching and registering imagery captured under different scales, illumination conditions, sensor characteristics, spatial resolutions, and geometric conditions.

The `data/ground_truth/` directory is intended for **scientifically controlled reference information used to determine whether correspondence and registration results are correct**.

Ground truth is therefore an evaluation asset, not simply another image-data directory.

The project feedback explicitly emphasizes that registration quality should not be judged using the same points that were used to fit the transformation. It recommends using challenge ground truth when available, or independently checked tie points when challenge ground truth is unavailable.

> **Important status:** The supplied project materials define the evaluation principles for ground truth and independent check points, but they do not provide a complete implementation-level inventory or machine-readable schema for `data/ground_truth/`. Details that are not confirmed are explicitly marked below.

---

## 1. Purpose

`data/ground_truth/` exists to separate **authoritative or independently validated correctness information** from the image data and algorithm outputs being evaluated.

The conceptual relationship is:

```text
Source / Raw Data
       │
       ▼
Processing / Preparation
       │
       ▼
Candidate Correspondences
       │
       ▼
Geometric Verification / Ground-Truth Creation
       │
       ▼
Ground Truth
       │
       ▼
Independent Evaluation
```

Ground truth provides the reference against which ChandraMap correspondence and registration behavior can be measured.

It should not be confused with:

- candidate matches,
- RANSAC inliers,
- estimated transformations,
- registered images,
- benchmark predictions,
- or visually convincing overlays.

---

## 2. What Ground Truth Means in ChandraMap

For ChandraMap, ground truth is best understood as:

> **A controlled reference representation of the correct correspondence, geometric relationship, or registration information required to evaluate a lunar image correspondence/registration result.**

The exact representation depends on the benchmark protocol and available source information.

The project materials explicitly identify:

- challenge ground truth, when available;
- independently checked tie points;
- check-point error;
- source-image pixel error;
- and ground error when the required projection/GSD/reference information makes it meaningful.

However:

> **The supplied repository data does not define one universal ground-truth file schema for all ChandraMap cases.**

Therefore, this README does not invent a JSON, CSV, GeoJSON, image-mask, transformation-matrix, or other schema as the official format.

---

## 3. Ground Truth Is a Scientific Evaluation Asset

Ground truth should be treated as a controlled scientific asset.

It can directly affect:

- reported registration error,
- benchmark pass/fail behavior,
- baseline comparisons,
- acceptance decisions,
- reproducibility,
- and conclusions about algorithm performance.

A corrupted, undocumented, incorrectly generated, or leaked ground-truth set can make an otherwise correct algorithm appear incorrect—or make an incorrect algorithm appear successful.

The project feedback therefore recommends measuring actual quantities such as:

- reprojection/check-point RMSE,
- inlier count,
- inlier ratio,
- spatial coverage,
- ground error when meaningful,
- success rate,
- and runtime,

rather than using decorative confidence percentages or ratings.

---

## 4. Ground Truth vs. Source / Raw Data

Source or raw data is the imagery from which ChandraMap attempts to establish correspondence.

Ground truth is information used to determine whether that correspondence is correct.

Conceptually:

```text
Source Image
     │
     ├───────────────► Algorithm
     │                    │
     │                    ▼
     │             Candidate Matches
     │                    │
     │                    ▼
     │              Final Result
     │                    │
     │                    ▼
     │               Evaluation
     │                    ▲
     │                    │
     └──────────── Ground Truth
```

Therefore:

```text
Raw image ≠ Ground truth
```

A raw image may contain the physical evidence from which correspondence is established, but it does not automatically encode the benchmark's authoritative correspondence information.

---

## 5. Ground Truth vs. External Data

External data describes **where supporting data came from**.

Ground truth describes **what is considered correct for evaluation**.

An external lunar image can potentially contribute to benchmark or ground-truth construction, but:

```text
External data ≠ Ground truth
```

For example, the project materials discuss external/reference imagery such as LRO NAC and other lunar datasets.

That does not mean every externally sourced image should be placed in `data/ground_truth/`.

The source imagery and the correctness information derived from or associated with it should remain distinguishable.

---

## 6. Ground Truth vs. Interim Data

Interim data is generally an intermediate representation produced during a data-preparation workflow.

Ground truth is an evaluation reference.

Conceptually:

```text
Raw / External Data
        │
        ▼
Interim Processing
        │
        ▼
Prepared Data
        │
        ▼
Correspondence / Verification
        │
        ▼
Ground Truth
```

Interim files should not be promoted to ground truth merely because they were produced during processing.

A ground-truth artifact requires a defined correctness role and validation process.

> **Exact `data/interim/` integration rules: Not specified by the supplied project data.**

---

## 7. Ground Truth vs. Processed Data

Processed data is derived from another data representation through a documented operation.

Examples may include:

- normalized imagery,
- resized imagery,
- structural representations,
- pyramids,
- tiles,
- feature descriptors,
- or other derived products.

The project feedback recommends sensor-specific preparation and physically meaningful scale handling before matching.

Those processed representations are not automatically ground truth.

```text
Image
  │
  ▼
Preprocessing
  │
  ▼
Processed Image
```

is fundamentally different from:

```text
Correspondence Evidence
  │
  ▼
Validated Reference
  │
  ▼
Ground Truth
```

---

## 8. Ground Truth vs. Benchmark Inputs

Benchmark inputs are the data supplied to an algorithm during evaluation.

Ground truth is the reference information used to evaluate what the algorithm produced.

A simplified benchmark case is:

```text
Benchmark Case
      │
      ├── Source Input
      │
      ├── Reference Input
      │
      ├── Metadata
      │
      └── Ground Truth
               │
               ▼
         Evaluation Protocol
               ▲
               │
        Algorithm Result
```

The exact benchmark case schema is governed by the V1 benchmark specification.

> **Exact connection between `data/ground_truth/` and individual V1 benchmark manifests: Not specified.**

---

## 9. Ground Truth vs. Expected / Reference Data

Ground truth must not automatically be equated with the repository's `expected` baseline component.

The project architecture contains:

```text
benchmarks/baselines/ground_truth/
benchmarks/baselines/expected/
```

but the exact semantic contract of the `expected` component is not fully established by the supplied materials.

Therefore:

```text
Ground Truth
    ≠
Expected / Reference
```

unless the benchmark specification explicitly defines them as equivalent for a particular evaluation.

The corresponding benchmark-level ground-truth documentation should remain authoritative.

---

## 10. Ground Truth vs. Candidate Correspondences

The project feedback explicitly distinguishes **candidate matches** from geometrically verified matches.

The recommended conceptual sequence is:

```text
Candidate Matches
       │
       ▼
RANSAC + Initial Model
       │
       ▼
Verified Inliers
       │
       ▼
Sub-pixel Refinement
       │
       ▼
Final Transformation
```

Candidate correspondences are **algorithm predictions**.

They are not ground truth.

A matcher confidence score does not turn a candidate correspondence into ground truth.

---

## 11. Ground Truth vs. RANSAC Inliers

RANSAC inliers are correspondences that survived a geometric consistency test against an estimated model.

They are still part of the algorithm's result.

They should therefore be distinguished from independently established ground truth.

```text
Candidate Matches
       │
       ▼
      RANSAC
       │
       ▼
  RANSAC Inliers
       │
       └── Algorithm-derived result

Ground Truth
       │
       └── Evaluation reference
```

The project feedback specifically warns that matcher confidence is not proof of geometric correctness and recommends allowing RANSAC/geometric verification to determine verified inliers.

---

## 12. Ground Truth vs. Estimated Transformations

A transformation estimated by ChandraMap is a **prediction**.

For example:

```text
Correspondences
      │
      ▼
RANSAC
      │
      ▼
Estimated Affine / Homography / Other Model
```

The transformation is not ground truth simply because it has a low fitting error.

A transformation should be evaluated against independent reference information.

The feedback specifically recommends estimating the transformation from one set of points and evaluating it on points that were not used to fit the transform.

---

## 13. Ground Truth vs. Registration Outputs

A registered image is an output of the registration process.

It may look visually aligned while still containing significant geometric error.

Therefore:

```text
Ground Truth
      │
      ▼
Evaluation Reference

Registration Output
      │
      ▼
Algorithm Result
```

A visually convincing overlay is not sufficient evidence of registration accuracy.

The project feedback explicitly notes that a visually good overlay can still be scientifically wrong.

---

## 14. Ground Truth and Independent Check Points

Independent check points are especially important for ChandraMap registration evaluation.

The project guidance is explicit:

> Do not fit and judge on exactly the same points.

If a transformation is estimated from a set of inliers and RMSE is calculated on those same points, the reported error can look better than the actual registration quality.

The recommended approach is:

```text
Ground-Truth / Control Information
          │
          ├── Points used for fitting, where applicable
          │
          └── Independent check points
                       │
                       ▼
               Final Evaluation
```

The independent check points must not be used to fit the final transformation when they are intended for unbiased evaluation.

---

## 15. Ground-Truth Creation

The exact ChandraMap ground-truth creation procedure is:

> **Not fully specified in the supplied project data.**

The project feedback identifies several relevant sources of truth:

1. **Challenge ground truth**, when available.
2. **Independently checked tie points**, when challenge ground truth is unavailable.
3. Reference information appropriate to the image product and evaluation task.

The repository should therefore not claim a single universal creation method until the V1 ground-truth protocol defines one.

---

## 16. Ground-Truth Creation Principles

Regardless of the final implementation, ground-truth construction should preserve the following scientific principles.

### 16.1 Do not use the prediction as its own truth

An algorithm should not generate a transformation and then use that transformation as the authoritative reference for evaluating itself.

### 16.2 Separate fitting from checking

Points used to estimate the final transformation should be distinguished from independent check points when the evaluation protocol requires them.

### 16.3 Preserve coordinate meaning

The project feedback requires source-image pixel error to be reported first for sub-pixel registration, with conversion to metres only when GSD and projection make that conversion meaningful.

### 16.4 Preserve spatial distribution

A set of points concentrated around one crater can produce a misleading impression of registration quality.

The benchmark therefore considers spatial coverage important.

### 16.5 Preserve sensor context

OHRC, TMC-2, and IIRS should not be treated as identical imaging sources.

The project feedback recommends sensor-aware processing and separate reporting for these paths.

---

## 17. Ground-Truth Geometry

ChandraMap's geometry workflow is:

```text
LOCAL MATCHES
      │
      ▼
RANSAC
      │
      ▼
INLIERS
      │
      ▼
SUB-PIXEL TIE POINTS
      │
      ▼
FINAL MODEL
      │
      ▼
REGISTERED IMAGE
```

This workflow describes the **algorithmic registration process**, not the ground-truth definition itself.

The project feedback recommends:

1. fitting an initial model with RANSAC;
2. inspecting residual vectors;
3. refining verified tie-point coordinates;
4. refitting the final transformation;
5. evaluating the final result independently.

---

## 18. Ground-Truth Coordinate System

The exact ground-truth coordinate representation is:

> **Not specified.**

Depending on the benchmark product, correspondence information may need to distinguish:

- source-image coordinates,
- reference-image coordinates,
- map coordinates,
- or physical ground coordinates.

The project feedback specifically states that sub-pixel error should be reported in source-image pixels first. Ground error in metres should only be reported when the relevant GSD, projection, and reference truth make that conversion meaningful.

No additional coordinate convention should be invented here.

---

## 19. Spatial Coverage

Correctness is not determined only by the number of matches.

The project feedback recommends reporting spatial coverage, for example through grid coverage or convex-hull coverage, to determine whether valid correspondences are distributed across the overlap instead of being clustered around one feature.

Conceptually:

```text
Poor Coverage                  Better Coverage

+-----------+                  +-----------+
|           |                  | x       x |
|      xxx  |                  |           |
|      xxx  |                  |   x   x   |
|           |                  |           |
+-----------+                  | x       x |
                               +-----------+
```

The exact coverage schema and threshold are:

> **Not specified.**

---

## 20. Ground-Truth Metrics

Ground truth itself is not normally a performance metric.

It enables metrics to be computed.

The project materials identify the following benchmark metrics:

| Metric            | Ground-truth relationship                                             |
| ----------------- | --------------------------------------------------------------------- |
| Check-point RMSE  | Measures registration accuracy against independent reference points   |
| Reprojection RMSE | Measures geometric disagreement where applicable                      |
| Inlier count      | Describes algorithmic geometric verification                          |
| Inlier ratio      | Describes the fraction of candidate matches surviving verification    |
| Spatial coverage  | Measures distribution of verified correspondences                     |
| Ground error      | Physical error when GSD/projection/reference truth make it meaningful |
| Success rate      | Aggregated outcome across test cases                                  |
| Runtime           | Computational measurement, not a ground-truth accuracy measure        |

---

## 21. Check-Point RMSE

Check-point RMSE is particularly important for registration evaluation.

Conceptually:

```text
Estimated Transformation
          │
          ▼
Independent Check Points
          │
          ▼
Predicted Locations
          │
          ▼
Difference from Reference
          │
          ▼
Check-Point RMSE
```

The purpose is to evaluate the final transformation on points that were not used to fit it.

This prevents training/fitting error from being confused with generalization to independent points.

---

## 22. Pixel Error vs. Ground Error

ChandraMap must distinguish source-image pixel accuracy from physical ground accuracy.

The project feedback explicitly states:

> Report error in source-image pixels first.

Conversion to metres is appropriate only when the product's GSD and map projection make the conversion scientifically meaningful.

Therefore:

```text
Source-image error
      │
      ▼
Primary registration measurement
      │
      └──► Ground error in metres
             only when justified
```

A pixel error cannot be interpreted as a universal physical distance across sensors with different GSDs.

---

## 23. Ground Truth and Sensor Differences

ChandraMap's main sensors have different imaging characteristics.

The supplied feedback distinguishes:

- OHRC,
- TMC-2,
- IIRS,

and discusses LRO reference imagery separately.

This affects ground-truth interpretation.

For example:

```text
Same numerical pixel error
          │
          ├── Sensor A
          │
          └── Sensor B
```

does not necessarily mean the same physical ground error.

The benchmark should therefore retain the sensor/product context associated with each ground-truth case.

---

## 24. Ground Truth and Scale

Scale is a scientific property of the image pair.

The project feedback recommends comparing images at physically meaningful effective ground scales and warns that upsampling does not recover missing spatial information.

Ground-truth metadata should therefore preserve enough information to interpret the scale relationship between the source and reference products.

The exact required scale fields are:

> **Not specified.**

---

## 25. Ground Truth and Illumination

Ground truth should remain valid even when the same lunar region appears under different illumination conditions.

The project materials emphasize that changing Sun angle changes terrain shadows and cannot be solved simply through brightness normalization.

This creates important stress-test cases:

```text
Same Region
    │
    ├── Similar Illumination
    │
    └── Different Illumination
             │
             ▼
        Same spatial truth
```

The correspondence algorithm should be evaluated against the appropriate reference relationship rather than against pixel-level visual similarity alone.

---

## 26. Ground Truth and Modality

The project identifies IIRS as substantially different from visible/panchromatic imagery and recommends converting it into a registration-friendly 2D representation before applying ordinary image-matching approaches.

Ground truth should describe the intended spatial relationship independently of whichever image representation the algorithm uses.

For example:

```text
IIRS Representation
        │
        ▼
Algorithm Correspondence
        │
        ▼
Ground-Truth Spatial Relationship
```

The exact IIRS ground-truth representation is:

> **Not specified.**

---

## 27. Ground Truth and Retrieval

The project architecture distinguishes global retrieval from local correspondence.

The retrieval workflow is conceptually:

```text
Reference Images
      │
      ▼
Tiles + Scales
      │
      ▼
Global Descriptor
      │
      ▼
FAISS Index + Metadata
      │
      ▼
Top-K Candidate Regions
      │
      ▼
Local Matching
```

The feedback recommends Recall@1 and Recall@5 when retrieval is evaluated.

Ground truth for retrieval may therefore need to identify the correct region/candidate relationship.

However:

> **The exact retrieval ground-truth schema is Not specified.**

---

## 28. Ground Truth and Benchmark Stress Tests

The benchmark feedback proposes a small controlled stress-test matrix:

| Case                | Purpose                                      |
| ------------------- | -------------------------------------------- |
| Easy pair           | Demonstrate end-to-end operation             |
| Sun-angle stress    | Measure robustness to changing shadows       |
| Scale stress        | Measure behavior under large GSD differences |
| Modality stress     | Evaluate sensor-aware processing             |
| Geometry stress     | Evaluate transformation/refinement           |
| Low-feature terrain | Expose weak or false correspondence behavior |

Ground-truth records should remain associated with the benchmark case they evaluate.

The exact case-ID schema is:

> **Not specified.**

---

## 29. Ground Truth and Data Leakage

Ground-truth leakage is a serious benchmark integrity risk.

Examples include:

- using evaluation points to fit the transformation;
- using test regions during model development without an explicit split policy;
- modifying ground truth after observing algorithm results;
- using algorithm predictions to define the reference used to evaluate that same algorithm;
- or allowing benchmark-specific information to influence an algorithm configuration without documenting it.

The project feedback's strongest explicit leakage-prevention rule is:

> **Do not fit and judge on exactly the same points.**

---

## 30. Fitting Points vs. Check Points

Where the benchmark uses control points and independent check points, the distinction should be explicit.

```text
Verified / Control Points
          │
          ▼
Transformation Fitting
          │
          ▼
Final Transformation
          │
          ▼
Independent Check Points
          │
          ▼
Evaluation
```

The check points must not be reused to fit the transformation if they are intended to provide independent evaluation.

This distinction should be represented in the ground-truth data model if the repository adopts one.

---

## 31. Ground-Truth Validation

Ground truth should be validated before being used in benchmark evaluation.

Validation should answer at least:

### Identity

Does the ground truth belong to the intended image pair/case?

### Provenance

Can its source or creation process be established?

### Geometry

Are the coordinates internally consistent?

### Independence

Were evaluation points kept separate from points used to estimate the transformation?

### Spatial distribution

Are the points sufficiently distributed for the intended evaluation?

### Version

Is the ground-truth version known?

### Integrity

Can the stored artifact be verified as the intended file?

The exact automated validation tooling is:

> **Not specified.**

---

## 32. Ground-Truth Provenance

Each ground-truth artifact should be traceable to its origin.

Recommended provenance information includes:

| Information                     | Status      |
| ------------------------------- | ----------- |
| Ground-truth identifier         | Recommended |
| Benchmark case ID               | Recommended |
| Source image identity           | Recommended |
| Reference image identity        | Recommended |
| Creation method                 | Recommended |
| Creator/tool/process            | Recommended |
| Creation date                   | Recommended |
| Dataset version                 | Recommended |
| Ground-truth version            | Recommended |
| Coordinate convention           | Recommended |
| Point role                      | Recommended |
| Validation status               | Recommended |
| Integrity checksum              | Recommended |
| Related benchmark specification | Recommended |

These are **recommended provenance fields**, not an existing confirmed schema.

---

## 33. Ground-Truth Versioning

Ground truth must be versioned independently enough to determine which reference information was used for a reported result.

A conceptual version relationship is:

```text
Benchmark Version
       │
       ├── Dataset Version
       │
       ├── Ground-Truth Version
       │
       └── Configuration Version
```

A change to ground truth can change benchmark results even if the algorithm code is unchanged.

Therefore, ground-truth changes should not be treated as ordinary file replacements.

> **Formal `data/ground_truth/` versioning scheme: Not specified.**

---

## 34. Ground-Truth Immutability

Once a ground-truth version has been used to publish benchmark results, it should be treated as immutable for that benchmark version unless the benchmark explicitly declares a correction or revision.

Conceptually:

```text
Ground Truth v1
      │
      └── Benchmark Results v1

Ground Truth v2
      │
      └── Requires explicit benchmark/result version relationship
```

This is a **recommended scientific-data practice**.

The repository has not yet specified a formal immutable-release mechanism.

---

## 35. Ground Truth Integrity

Ground-truth files should not be silently edited.

A change to:

- a point coordinate,
- point identity,
- image association,
- case association,
- transformation reference,
- or validation status

may change benchmark results.

Therefore:

> **Ground-truth changes should be reviewable and traceable.**

The exact Git workflow, checksum system, and approval mechanism are:

> **Not specified.**

---

## 36. Recommended Ground-Truth Record

The project does not currently provide an authoritative machine-readable schema.

A future ground-truth record could conceptually contain:

```yaml
ground_truth:
  id: "Not specified"
  version: "Not specified"

case:
  id: "Not specified"
  dataset_version: "Not specified"
  source_image: "Not specified"
  reference_image: "Not specified"

points:
  role: "Not specified"
  coordinate_system: "Not specified"
  source_points: []
  reference_points: []

validation:
  method: "Not specified"
  status: "Not specified"

provenance:
  source: "Not specified"
  created_by: "Not specified"
  created_at: "Not specified"

integrity:
  checksum_algorithm: "Not specified"
  checksum: "Not specified"
```

This is a **recommended example only**.

It is not an existing ChandraMap schema unless the repository explicitly adopts it.

---

## 37. What Belongs in `data/ground_truth/`

Ground-truth data belongs here when it is:

- explicitly intended for correctness evaluation;
- associated with a defined dataset or benchmark case;
- traceable to its source or creation process;
- validated or independently checked according to the applicable protocol;
- versioned sufficiently for reproducibility;
- and not merely an algorithm-generated prediction.

Potential examples include:

- validated correspondence/control-point records;
- independent check-point records;
- benchmark reference correspondence information;
- other authoritative evaluation references explicitly defined by the V1 protocol.

The exact list of supported file types is:

> **Not specified.**

---

## 38. What Does Not Belong in `data/ground_truth/`

Do not place the following here unless the benchmark explicitly defines them as ground truth:

- raw lunar images;
- downloaded external imagery;
- temporary files;
- preprocessing outputs;
- normalized images;
- image pyramids;
- tiles;
- descriptors;
- candidate matches;
- matcher confidence scores;
- RANSAC inliers;
- estimated transformations;
- registered previews;
- runtime logs;
- benchmark metrics;
- model checkpoints;
- cache files;
- arbitrary screenshots;
- undocumented manual annotations;
- or expected outputs that have not been defined as ground truth.

---

## 39. Candidate Correspondence Data

Candidate correspondence data represents what an algorithm proposes.

For example:

```text
Image Pair
   │
   ▼
SIFT / ALIKED / LoFTR / Other Matcher
   │
   ▼
Candidate Correspondences
```

Candidate correspondences should remain separate from ground truth.

The project feedback specifically recommends calling these **candidate matches** rather than "high confidence matches," because a matcher confidence score is not proof of geometric correctness.

---

## 40. RANSAC Inlier Data

RANSAC inliers are algorithmically verified correspondences.

They may be useful evaluation artifacts, but they are not automatically ground truth.

```text
Candidate Matches
      │
      ▼
RANSAC
      │
      ▼
Inliers
```

The benchmark should be able to answer:

> How well did the algorithm's inliers agree with independently established reference information?

rather than:

> How well did the algorithm agree with itself?

---

## 41. Estimated Transformations

A transformation matrix or geometric model belongs to algorithm output unless explicitly defined otherwise.

Examples may include:

- affine transformation,
- homography,
- local/piecewise warp,
- sensor-geometry model,
- DEM-assisted relationship.

The project feedback recommends choosing the simplest transformation that explains the residuals and warns that lunar terrain is not a flat poster.

The exact official V1 transformation model is:

> **Not specified in the supplied project data.**

---

## 42. Registered Images

Registered images are outputs of a transformation.

They may be stored as benchmark artifacts or visual diagnostics, but they are not automatically ground truth.

The project feedback identifies the registered preview as a useful output, while emphasizing that correspondence accuracy and measurable registration error remain the core deliverables.

---

## 43. Ground Truth and Sub-Pixel Refinement

The project feedback recommends:

```text
RANSAC
   │
   ▼
Verified Inliers
   │
   ▼
Sub-pixel Refinement
   │
   ▼
Refit Final Transformation
```

Sub-pixel refinement improves the estimated correspondence coordinates.

It does not automatically modify ground truth.

Ground truth should remain independent of the algorithm's claimed refinement accuracy.

The final registration error should be measured against the appropriate reference points.

---

## 44. Ground Truth and Failure Cases

A benchmark ground-truth set should not contain only easy successful cases.

The project feedback explicitly recommends a stress-test matrix containing difficult conditions such as:

- different Sun angles,
- large scale differences,
- modality differences,
- stronger geometric differences,
- and low-feature terrain.

This allows ground truth to support evaluation of both:

```text
Successful correspondence
```

and:

```text
Failure / insufficient-evidence cases
```

The exact failure-label schema is:

> **Not specified.**

---

## 45. Ground Truth and Spatial Distribution

The benchmark should not treat a large number of matches in one small region as equivalent to well-distributed correspondence.

The project feedback recommends grid or convex-hull coverage measurements.

Ground-truth point sets should therefore preserve spatial information whenever spatial coverage is part of the evaluation.

A future schema should avoid representing a correspondence set only as an unordered collection of points if that would remove the information needed to evaluate distribution.

---

## 46. Ground Truth and Metadata

Ground truth must remain interpretable in the context of the imagery it evaluates.

Relevant image metadata may include:

- product type,
- image dimensions,
- pixel scale/GSD,
- footprint,
- map projection,
- viewing geometry,
- illumination information.

The project feedback explicitly recommends preserving these properties.

The exact metadata schema for ground truth is:

> **Not specified.**

---

## 47. Ground Truth and Reproducibility

A benchmark result should be traceable to the exact ground-truth version used to calculate it.

Conceptually:

```text
Code
  +
Configuration
  +
Dataset
  +
Ground Truth Version
  +
Metrics
  +
Environment
  +
Execution
       │
       ▼
Reproducible Result
```

The exact repository mechanism for storing these relationships is:

> **Not specified.**

The V1 reproducibility documentation should remain authoritative for the final reproducibility contract.

---

## 48. Ground Truth and Benchmark Acceptance

Ground truth is an input to benchmark acceptance, not the acceptance criterion itself.

The general relationship is:

```text
Ground Truth
      │
      ▼
Evaluation
      │
      ▼
Metrics
      │
      ▼
Acceptance Criteria
```

The exact numerical acceptance thresholds are:

> **Not specified in the supplied ground-truth materials.**

No threshold should be invented here.

---

## 49. Ground Truth and Baselines

Algorithmic baselines such as SIFT produce results that are evaluated against ground truth.

The project feedback recommends starting with:

```text
SIFT
  ↓
Descriptor Matching
  ↓
RANSAC
  ↓
Transformation
  ↓
Metrics
```

and comparing it with stronger matching approaches on the same image pairs.

The relationship is:

```text
Ground Truth
      ▲
      │
      │ evaluation
      │
Algorithm Baseline
      │
      ▼
Predicted Correspondence / Registration
```

Ground truth should remain unchanged merely because one baseline performs poorly.

---

## 50. Ground Truth and Expected Baseline

The repository also contains an `expected` baseline component.

The supplied project information does not establish that:

```text
data/ground_truth/
```

and:

```text
benchmarks/baselines/expected/
```

are interchangeable.

Therefore:

> **Relationship between these two components: Not fully specified.**

Ground truth should continue to be governed by the ground-truth protocol until the benchmark architecture explicitly defines a different relationship.

---

## 51. Contributor Workflow

Contributors adding or modifying ground truth should follow this conceptual workflow:

```text
1. Identify the benchmark/data case
              │
              ▼
2. Identify source + reference imagery
              │
              ▼
3. Establish correspondence reference
              │
              ▼
4. Validate coordinates / geometry
              │
              ▼
5. Separate fitting points from check points
              │
              ▼
6. Record provenance
              │
              ▼
7. Assign ground-truth version
              │
              ▼
8. Validate integrity
              │
              ▼
9. Review changes
              │
              ▼
10. Use in benchmark evaluation
```

The exact scripts and commands are:

> **Not specified.**

---

## 52. Adding New Ground Truth

Before adding a new ground-truth artifact, contributors should document:

### Case identity

Which source/reference pair or benchmark case does it belong to?

### Data identity

Which dataset version produced the case?

### Ground-truth identity

What version or identifier does the ground truth have?

### Coordinate meaning

What coordinate system or image coordinate convention is used?

### Point role

Are the points:

- fitting/control points,
- independent check points,
- or another explicitly defined category?

### Creation method

How was the reference correspondence established?

### Validation

How was it checked?

### Provenance

Who/what created or supplied it?

### Integrity

How can the stored artifact be verified?

### Benchmark relationship

Which benchmark evaluation uses it?

---

## 53. Updating Existing Ground Truth

Ground truth should not be edited casually.

A change should identify:

```text
Old Ground-Truth Version
        │
        ▼
Reason for Change
        │
        ▼
Validation
        │
        ▼
New Ground-Truth Version
        │
        ▼
Affected Benchmark Results
```

If a ground-truth correction changes previously reported benchmark values, the affected results should be traceable to the new ground-truth version.

The exact release/versioning process is:

> **Not specified.**

---

## 54. Ground-Truth Review Checklist

Before accepting a ground-truth change:

- [ ] Case identity is documented.
- [ ] Source and reference images are identified.
- [ ] Ground-truth purpose is clear.
- [ ] Point roles are defined.
- [ ] Coordinate meaning is documented.
- [ ] Fitting and independent check points are separated where applicable.
- [ ] The ground truth was not generated solely from the algorithm being evaluated.
- [ ] Provenance is recorded.
- [ ] Validation has been performed.
- [ ] Dataset version is known.
- [ ] Ground-truth version is known.
- [ ] Benchmark compatibility is confirmed.
- [ ] No unsupported numerical threshold has been introduced.
- [ ] Changes are reviewable and traceable.
- [ ] Existing benchmark results affected by the change are identified.

---

## 55. Recommended Integrity Record

The repository does not currently define an official integrity manifest.

A future implementation could record:

```yaml
artifact:
  id: "Not specified"
  type: "ground_truth"
  version: "Not specified"

case:
  id: "Not specified"
  dataset_version: "Not specified"

provenance:
  source: "Not specified"
  method: "Not specified"
  created_by: "Not specified"

validation:
  status: "Not specified"
  method: "Not specified"

integrity:
  checksum_algorithm: "Not specified"
  checksum: "Not specified"
```

This is **Recommended**, not an existing repository schema.

---

## 56. Storage Policy

The supplied repository information does not establish whether complete ground-truth datasets should be:

- committed directly to Git,
- stored using Git LFS,
- stored externally,
- generated during dataset preparation,
- or distributed through another artifact mechanism.

Therefore:

> **Ground-truth storage mechanism: Not specified.**

Regardless of storage technology, the benchmark must preserve the identity and version of the ground truth used for evaluation.

---

## 57. Security and Integrity Considerations

Ground truth is especially sensitive to accidental modification because changing a single coordinate can alter reported registration metrics.

Contributors should therefore avoid:

- manual edits without review,
- replacing files without version changes,
- undocumented coordinate conversions,
- silent resampling,
- changing point order without documenting semantics,
- or mixing points from different dataset versions.

The exact repository access-control mechanism is:

> **Not specified.**

---

## 58. Scientific Data Integrity Rules

The following principles should govern `data/ground_truth/`:

### Rule 1 — Ground truth must be independent of the prediction

Do not use the algorithm's output as its own evaluation reference.

### Rule 2 — Separate fitting and evaluation

Do not fit and evaluate a transformation on exactly the same points when independent evaluation is required.

### Rule 3 — Preserve provenance

Every ground-truth artifact should be traceable to its source or creation process.

### Rule 4 — Preserve coordinate meaning

Do not silently convert between image and ground coordinates.

### Rule 5 — Preserve sensor context

OHRC, TMC-2, IIRS, and external reference products must not be treated as interchangeable without documenting the transformation between them.

### Rule 6 — Preserve version identity

A benchmark result must be associated with the ground-truth version used to produce it.

### Rule 7 — Keep difficult cases

Ground truth should support stress testing rather than only easy demonstrations.

### Rule 8 — Do not invent accuracy

Ground truth enables measurement; it does not justify an unsupported accuracy claim.

---

## 59. Current Implementation Status

Based on the supplied project materials:

| Component                               | Status                        |
| --------------------------------------- | ----------------------------- |
| `data/ground_truth/` directory purpose  | Documented by this README     |
| Ground truth as an evaluation concept   | Supported by project feedback |
| Challenge ground truth                  | Supported when available      |
| Independent check-point concept         | Explicitly supported          |
| Fitting/check-point separation          | Explicitly supported          |
| Check-point RMSE                        | Explicitly supported          |
| Source-pixel error                      | Explicitly supported          |
| Ground error when meaningful            | Explicitly supported          |
| Spatial coverage                        | Explicitly supported          |
| Ground-truth file schema                | Not specified                 |
| Ground-truth manifest                   | Not specified                 |
| Formal versioning scheme                | Not specified                 |
| Automated validation tooling            | Not confirmed                 |
| Ground-truth generation scripts         | Not specified                 |
| Ground-truth checksum policy            | Not specified                 |
| Storage mechanism                       | Not specified                 |
| Complete current ground-truth inventory | Not confirmed                 |

---

## 60. Known Gaps and Limitations

### 60.1 Exact data schema

The supplied project materials do not define the exact file format for `data/ground_truth/`.

### 60.2 Ground-truth creation procedure

The exact authoritative creation workflow is not fully documented.

### 60.3 Dataset inventory

The complete set of currently available ground-truth records is not confirmed.

### 60.4 Versioning

A formal ground-truth versioning mechanism is not specified.

### 60.5 Automated validation

Automated coordinate, provenance, and integrity checks are not confirmed.

### 60.6 Storage

The repository does not specify the final large-data storage mechanism.

### 60.7 Benchmark integration

The exact mapping between `data/ground_truth/` and individual V1 benchmark cases is not fully specified.

### 60.8 Acceptance thresholds

The supplied materials identify evaluation metrics but do not establish all numerical acceptance thresholds.

---

## 61. Recommended Ground-Truth Architecture

Until the repository defines a more precise implementation, the safest conceptual architecture is:

```text
data/
└── ground_truth/
    │
    ├── Reference correctness information
    │
    ├── Provenance
    │
    ├── Version identity
    │
    └── Validation information
```

The actual filenames and subdirectories are:

> **Not specified.**

No directory structure should be assumed from this conceptual representation.

---

## 62. Relationship to the V1 Ground-Truth Protocol

The benchmark-level ground-truth protocol should be treated as authoritative for:

- what constitutes ground truth,
- how ground truth is constructed,
- how points are classified,
- how ground truth is used during evaluation,
- and what rules apply to benchmark cases.

The intended relationship is:

```text
data/ground_truth/
        │
        ▼
Ground-Truth Data Assets
        │
        ▼
benchmarks/v1/GROUND_TRUTH_PROTOCOL.md
        │
        ▼
V1 Evaluation
```

If the repository's V1 protocol defines a more specific rule, that rule takes precedence over this README.

---

## 63. Relationship to Reproducibility

A reproducible result requires more than knowing that "ground truth was used."

The exact ground-truth identity should be traceable.

Conceptually:

```text
Result
  │
  ├── Code Commit
  ├── Configuration
  ├── Dataset Version
  ├── Ground-Truth Version
  ├── Metrics
  ├── Environment
  └── Execution
```

The exact implementation of this provenance chain is:

> **Not specified.**

---

## 64. Ground Truth in the First End-to-End Milestone

The project feedback defines the first meaningful milestone as an end-to-end source/reference pair that progresses through:

```text
Input
  ↓
Candidate Matches
  ↓
Verified Inliers
  ↓
Final Transform
  ↓
Registered Overlay
  ↓
Numerical Error on Independent Check Points
```

Ground truth is therefore already central to the project's intended progression from a pipeline diagram to a measurable registration system.

The milestone should not be considered scientifically complete merely because an overlay looks visually aligned.

---

## 65. Ground Truth and the SIFT Baseline

The project feedback recommends SIFT as the first serious accuracy baseline:

```text
SIFT
  ↓
Descriptor Matching
  ↓
RANSAC
  ↓
Transformation
  ↓
Independent Evaluation
```

SIFT is the algorithm being evaluated.

Ground truth is the reference used to determine how accurately that algorithm performs.

The same ground truth should be used when comparing SIFT against another algorithm on the same benchmark case, unless the benchmark explicitly defines otherwise.

This is important because the project recommends running the same image pairs through the SIFT baseline, stronger matching methods, and the full sensor-aware pipeline.

---

## 66. Ground Truth and Advanced Matchers

The project materials discuss:

- ALIKED + LightGlue,
- LoFTR,
- RIFT,
- CFOG,

as possible stronger or research-oriented matching approaches.

These methods must be evaluated against the same defined reference conditions when they are compared.

The feedback recommends selecting methods through measured experiments rather than by assuming that a more sophisticated method is automatically better.

Ground truth should remain fixed while the algorithm under evaluation changes.

---

## 67. Ground Truth and Honest Reporting

Ground truth allows ChandraMap to report actual measurements instead of subjective claims.

The project feedback specifically recommends reporting:

- RMSE,
- inlier count,
- inlier ratio,
- spatial coverage,
- ground error where meaningful,
- success rate,
- and runtime.

Therefore, avoid statements such as:

```text
"Very accurate"
"92% confidence"
"Excellent registration"
"Nearly perfect"
```

unless such claims are explicitly supported by documented measurements and definitions.

---

## 68. Ground Truth Should Not Be Optimized Against

Ground truth is an evaluation reference, not a parameter to be adjusted until the algorithm passes.

A healthy workflow is:

```text
Freeze Evaluation Reference
          │
          ▼
Run Algorithm
          │
          ▼
Measure Result
          │
          ▼
Analyze Failure
          │
          ▼
Improve Algorithm
          │
          ▼
Re-run Against Same Reference
```

If ground truth changes during algorithm development, the change must have an independent scientific reason and must be versioned.

---

## 69. Ground Truth and Failure Analysis

Ground truth is useful not only for successful cases but also for diagnosing failure.

For a failed case, the benchmark should ideally allow the team to determine whether the problem originated from:

- scale,
- illumination,
- modality,
- retrieval,
- geometry,
- insufficient features,
- or sub-pixel refinement.

The project feedback explicitly frames these as important failure-analysis dimensions.

The exact failure-analysis schema is:

> **Not specified.**

---

## 70. Practical Ground-Truth Checklist

Before using a ground-truth artifact in evaluation:

### Identity

- [ ] Correct source image.
- [ ] Correct reference image.
- [ ] Correct benchmark case.
- [ ] Correct dataset version.

### Geometry

- [ ] Coordinate meaning is documented.
- [ ] Point correspondences are internally consistent.
- [ ] Relevant spatial reference information is preserved.

### Independence

- [ ] Fitting points and check points are distinguished.
- [ ] Evaluation points were not used to fit the transformation when independence is required.

### Provenance

- [ ] Creation/source information is documented.
- [ ] Ground-truth version is known.
- [ ] Validation status is known.

### Integrity

- [ ] Artifact identity can be verified.
- [ ] No undocumented modification occurred.

### Evaluation

- [ ] Applicable benchmark metrics are known.
- [ ] Pixel/ground units are correctly interpreted.
- [ ] Spatial coverage can be evaluated where required.

---

## 71. Final Data Flow

The complete conceptual relationship for ChandraMap is:

```text
┌──────────────────────────────┐
│ Source / Raw / External Data │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│ Preparation / Preprocessing  │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│ Candidate Correspondences    │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│ RANSAC / Geometric Filtering │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│ Verified Inliers             │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│ Sub-pixel Refinement         │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│ Final Transformation         │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│ Registration Result          │
└──────────────┬───────────────┘
               │
               ▼
       ┌────────────────┐
       │   Evaluation   │
       └───────┬────────┘
               ▲
               │
┌──────────────┴───────────────┐
│        Ground Truth          │
│                              │
│ • Reference correspondences  │
│ • Check points               │
│ • Case-specific truth        │
│ • Validation/provenance      │
└──────────────────────────────┘
```

The project feedback supports this separation by explicitly placing candidate matching, RANSAC verification, sub-pixel refinement, final transformation, and independent check-point evaluation into distinct stages.

---

## 72. Final Principle

`data/ground_truth/` should remain a **controlled scientific reference layer** in ChandraMap.

It should answer:

> **What information tells us whether the correspondence or registration result is actually correct?**

It should not become a collection of:

- raw images,
- algorithm predictions,
- RANSAC inliers,
- transformations,
- registered previews,
- arbitrary annotations,
- or undocumented expected values.

The core principle is:

```text
Do not evaluate an algorithm against itself.

Use controlled reference information,
keep fitting and checking separate,
preserve provenance and version identity,
and report measurable registration error.
```

This follows the project's central evaluation guidance: use challenge ground truth when available; otherwise maintain independently checked points that are not used to fit the transformation, and evaluate registration using check-point error rather than training/fitting error.

> **Current repository status:** The scientific role of ground truth is established, but the exact `data/ground_truth/` file schema, versioning system, storage mechanism, automated validation tooling, and complete artifact inventory remain **Not specified / Not confirmed** by the supplied project data. Those details should be added when the repository formally defines them rather than being inferred or invented.
