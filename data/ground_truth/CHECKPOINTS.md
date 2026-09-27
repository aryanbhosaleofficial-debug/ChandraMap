# Check Points

> **Document:** `data/ground_truth/CHECKPOINTS.md`
> **Project:** ChandraMap
> **Scope:** Ground-truth check points for independent lunar image correspondence and registration evaluation
> **Status:** Specification / data-governance document
> **Implementation status:** The repository sources reviewed do not establish a finalized machine-readable check-point schema or complete check-point dataset. Where implementation details are not confirmed, this document explicitly marks them as **Not specified**, **Not confirmed**, or **To be defined**.

---

## 1. Purpose

This document defines the role and requirements of **check points** in ChandraMap.

Check points are used to evaluate the quality of a final image registration **independently from the points used to estimate the transformation**.

The central evaluation principle is:

```text
Control / Fit Points
        │
        ▼
Transformation Estimation
        │
        ▼
Independent Check Points
        │
        ▼
Registration / Geometric Error Evaluation
```

The distinction is scientifically important because evaluating a transformation on the same points used to fit that transformation can make the reported error appear better than the actual registration quality.

The project feedback explicitly recommends that transformations should not be fitted and judged on exactly the same points. Where challenge ground truth is available, it should be used; otherwise, independently checked tie points can serve as check points and must not be used to fit the transformation.

---

## 2. ChandraMap Context

ChandraMap addresses lunar image correspondence and registration across imagery that can differ in:

- sensor;
- spatial scale;
- illumination;
- viewpoint;
- image characteristics;
- resolution;
- modality;
- geometric conditions.

The project specifically considers Chandrayaan-2 imagery such as:

- OHRC;
- TMC-2;
- IIRS;

together with lunar reference imagery such as LRO products where applicable.

The system's primary technical output is not merely a visually attractive mosaic. The project feedback identifies reliable matched points, transformation/geolocation, registered imagery, and measurable quality metrics as core outputs.

Check points therefore provide the independent geometric evidence needed to determine whether the correspondence and registration pipeline actually produced an accurate transformation.

---

## 3. Definition

### 3.1 Check Point

A **check point** is a verified source/target point correspondence reserved for **independent evaluation** of an estimated registration transformation.

A check point:

1. identifies a corresponding location in the source and target/reference imagery;
2. has a documented provenance;
3. has defined coordinate conventions;
4. is independently verified to an appropriate degree;
5. is not used to estimate the transformation being evaluated;
6. is used after transformation estimation to measure registration error.

Conceptually:

```text
Source Check Point
        │
        │ known correspondence
        ▼
Target Check Point
        │
        │ evaluate estimated transform
        ▼
Predicted Target Location
        │
        ▼
Residual / Error
```

The resulting error is evidence about how well the transformation generalizes beyond its fitting points.

---

## 4. Why Check Points Are Required

A registration system can produce a low fitting error without providing equally accurate registration elsewhere in the image.

For example:

```text
Fit Points
   ↓
Transform estimated
   ↓
Same Fit Points evaluated
   ↓
Potentially optimistic error
```

This can happen because the transformation has already been optimized using those observations.

Independent check points instead follow:

```text
Fit Points
   ↓
Transform estimated
   ↓
Check Points kept untouched
   ↓
Transform applied
   ↓
Independent residual measured
```

The project feedback explicitly identifies independent check-point error as the registration metric that measures accuracy on points **not used to fit the transformation**.

---

# 5. Check Points and Ground Truth

Check points are related to ground truth but are not automatically synonymous with the entire ground-truth dataset.

A ground-truth resource may contain:

- known image-to-image correspondences;
- control/fit points;
- check points;
- reference coordinates;
- metadata;
- validation information;
- provenance;
- benchmark-specific annotations.

Within that broader ground-truth context, check points represent the subset or designated observations intended for independent evaluation.

### Important rule

```text
Ground Truth
    │
    ├── Fit / Control Information
    │
    └── Independent Check Information
```

The exact machine-readable representation of this separation is **not confirmed by the reviewed project sources** and must be defined consistently before implementation.

---

# 6. Check Points vs Other Point Types

## 6.1 Check Points vs Control Points

These terms must not be treated as interchangeable.

### Control / Fit Points

Control points are points used to estimate, constrain, or refine a transformation.

```text
Control Points
      ↓
Estimate Transform
```

### Check Points

Check points are withheld from transformation estimation and are used to evaluate the resulting transformation.

```text
Check Points
      ↓
Evaluate Transform
```

Therefore:

| Property                             | Control / Fit Point | Check Point               |
| ------------------------------------ | ------------------- | ------------------------- |
| Used for transformation fitting      | Yes                 | No                        |
| Used for independent evaluation      | No                  | Yes                       |
| May influence fitted model           | Yes                 | No                        |
| Used to calculate check-point RMSE   | No                  | Yes                       |
| Must remain independent from fitting | Not applicable      | Yes                       |
| Primary purpose                      | Estimate model      | Measure model performance |

The project feedback specifically recommends refining verified control points and then refitting the final transformation, while preserving independent check points for evaluation.

---

## 6.2 Check Points vs Candidate Correspondences

Candidate correspondences are proposed matches produced by the local matching stage.

They may be generated by methods such as:

- SIFT;
- ALIKED + LightGlue;
- LoFTR;
- other tested matching approaches.

A candidate match is **not automatically ground truth**.

The project feedback explicitly recommends calling these **Candidate Matches**, because matcher confidence is not proof that a correspondence is geometrically correct. RANSAC/geometric verification determines which candidates become verified inliers.

```text
Candidate Matches
       ↓
Geometric Verification
       ↓
Verified Inliers
```

Check points are different:

```text
Independent Ground-Truth Correspondence
       ↓
Check Point
       ↓
Evaluate Final Transform
```

---

## 6.3 Check Points vs RANSAC Inliers

RANSAC inliers are observations accepted by a geometric verification process under a fitted model.

They are useful for determining whether candidate correspondences are geometrically consistent.

However:

> **RANSAC inlier status does not by itself make a point an independent check point.**

If the point contributed to the model estimation, it is not independent of that model.

The project pipeline recommends:

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

This sequence is explicitly identified in the project feedback.

Check points remain outside the fitting path:

```text
Independent Check Points
        ↓
Final Transform Evaluation
```

---

## 6.4 Check Points vs Transformation-Estimation Points

Transformation-estimation points are any observations used to determine transformation parameters.

They can include points selected from verified correspondences.

A check point must not participate in estimating the transformation whose performance it evaluates.

The fundamental rule is:

```text
POINT ∈ FIT SET
    ⇒
POINT ∉ CHECK SET
```

For a benchmark evaluation:

```text
Fit Set ∩ Check Set = ∅
```

This separation must be preserved during:

- initial transformation estimation;
- RANSAC fitting;
- sub-pixel refinement;
- final transformation refitting;
- model selection where the same transformation is being evaluated.

---

# 7. Role in the ChandraMap Registration Pipeline

Check points belong to the **evaluation layer** of the registration pipeline rather than the correspondence-generation layer.

A simplified ChandraMap flow is:

```text
Input Images + Metadata
        ↓
Sensor-Aware Preparation
        ↓
Multi-Scale / Coarse Search
        ↓
Local Matching
        ↓
Candidate Matches
        ↓
RANSAC / Geometric Verification
        ↓
Verified Inliers
        ↓
Sub-Pixel Refinement
        ↓
Final Transformation
        ↓
Independent Check Points
        ↓
Check-Point Error
        ↓
Registration Metrics
```

The project feedback identifies a first end-to-end milestone as:

```text
Input
  →
Candidate Matches
  →
Verified Inliers
  →
Final Transform
  →
Registered Overlay
  →
Numerical Error on Independent Check Points
```

This is considered an important transition from a conceptual pipeline to a measurable system.

---

# 8. Minimum Information for a Check Point

A check point should contain enough information to reproduce and independently evaluate the correspondence.

The exact final schema is **not confirmed** in the reviewed project materials. The following fields therefore define the required semantic information, while the final serialization format remains **To be defined**.

## 8.1 Identity

Each check point should have a stable identifier.

Example conceptual field:

```text
check_point_id
```

The identifier should uniquely distinguish the point within its relevant dataset or pair.

---

## 8.2 Image-Pair Identity

A check point must identify the source/reference image pair to which it belongs.

Conceptually:

```text
source_image_id
target_image_id
```

The exact repository identifier scheme is **Not specified**.

---

## 8.3 Source Coordinate

The check point must provide its location in the source image.

Conceptually:

```text
source:
    x
    y
```

The coordinate convention must be explicitly documented.

---

## 8.4 Target Coordinate

The corresponding location in the target/reference image must also be recorded.

Conceptually:

```text
target:
    x
    y
```

---

## 8.5 Coordinate Reference

The representation must specify whether coordinates refer to:

- pixels;
- source-image pixels;
- target-image pixels;
- projected coordinates;
- geographic coordinates;
- another defined coordinate system.

For registration accuracy, the project feedback places primary emphasis on **source-image pixels** for sub-pixel reporting. Conversion to metres should only be performed when the product GSD and projection make that conversion meaningful.

---

## 8.6 Provenance

Each check point should have enough provenance to answer:

- Where did the point originate?
- Who or what produced it?
- Which source/reference products were used?
- Was it manually verified?
- Was it derived from an existing ground-truth source?
- Which annotation/version produced it?
- When was it created or updated?

The exact provenance schema is **Not specified**.

---

## 8.7 Verification Status

A check point should have an explicit verification state rather than being assumed correct merely because it exists.

Possible conceptual states include:

```text
verified
unverified
rejected
needs_review
```

These exact enum values are **To be defined** and are not presented here as an existing repository schema.

---

## 8.8 Quality Information

Where available, quality metadata may include:

- annotation confidence;
- localization uncertainty;
- source/reference image quality;
- point visibility;
- geometric ambiguity;
- reviewer status.

The project sources do not define a finalized check-point quality schema. Such fields should therefore not be added to the production dataset without an agreed specification.

---

# 9. Coordinate Conventions

Coordinate conventions must be explicit.

A check-point record must not rely on undocumented assumptions such as:

- whether `(0, 0)` represents the image corner or pixel center;
- whether `x` is column and `y` is row;
- whether coordinates are integer or floating point;
- whether coordinates are source or target coordinates;
- whether the image has been cropped, resampled, or reprojected.

At minimum, documentation associated with the check-point dataset should identify:

```text
Coordinate Space
Coordinate Origin
Axis Convention
Units
Image Version
Image Dimensions
Transform / Projection Context
```

The exact convention for ChandraMap is **Not confirmed in the reviewed project sources** and must be fixed before check-point data are treated as a finalized benchmark artifact.

---

# 10. Source and Target Point Representation

A check point represents a correspondence:

```text
P_source ↔ P_target
```

where:

- `P_source` is the known location in the source image;
- `P_target` is the corresponding known location in the target/reference image.

For a transformation:

```text
T(P_source) = P_target_predicted
```

the independent residual can conceptually be represented as:

```text
e = P_target_predicted - P_target
```

and its magnitude can be used for error evaluation.

The exact mathematical definition of the project's final RMSE implementation is governed by the benchmark/evaluation specification and should not be redefined independently in this file.

---

# 11. Independence Requirements

Independence is the most important property of check points.

A check point must not be allowed to influence the transformation whose accuracy it measures.

## 11.1 No Fitting Leakage

A check point must not be passed to:

- transformation fitting;
- RANSAC sampling;
- model parameter estimation;
- final model refitting.

---

## 11.2 No Hidden Refinement Leakage

If sub-pixel refinement is performed, the check points must remain excluded from that refinement when the resulting model is being evaluated on those check points.

The project feedback recommends:

```text
RANSAC
   ↓
Reliable Inliers
   ↓
Sub-Pixel Refinement
   ↓
Refit Final Transform
```

followed by independent evaluation.

---

## 11.3 No Selection Leakage

A check-point set must not be repeatedly inspected and selectively changed solely because a particular model performs poorly on it.

If check-point selection changes, the dataset version must change and the reason must be recorded.

---

## 11.4 No Benchmark Leakage

Test check points must not be used to tune:

- matcher parameters;
- transformation thresholds;
- refinement parameters;
- preprocessing choices;
- model selection;
- benchmark-specific heuristics.

If such data influence development, they should no longer be treated as an untouched evaluation set.

---

# 12. Check-Point Splitting

The exact project-approved split strategy is **Not specified**.

A benchmark implementation should nevertheless maintain an explicit distinction between:

```text
Training / Development Data
        ↓
Tuning / Validation Data
        ↓
Final Evaluation Data
```

Within an image pair:

```text
Fit / Control Points
        +
Independent Check Points
```

The precise split ratios, counts, spatial sampling strategy, and randomization policy are **To be defined** rather than assumed.

---

# 13. Spatial Distribution

Check points should provide meaningful spatial coverage of the image overlap.

A single group of points concentrated around one crater or small region may not adequately test registration quality across the complete overlap.

The project feedback explicitly identifies spatial coverage as a registration-quality metric and recommends checking whether good points are distributed across the overlap rather than clustered around one feature.

Conceptually:

```text
Poor distribution:

+-----------------------+
|                       |
|       ●●●●●           |
|       ●●●●●           |
|                       |
|                       |
+-----------------------+


Better distribution:

+-----------------------+
| ●           ●         |
|                       |
|      ●       ●        |
|                       |
| ●           ●      ●  |
+-----------------------+
```

The exact grid size or coverage threshold is **Not finalized** in the reviewed sources.

---

# 14. Feature Selection Considerations

Check points should represent identifiable and repeatable terrain correspondence.

Potentially useful terrain structures include:

- crater rims;
- ridge lines;
- stable terrain boundaries;
- other structurally identifiable lunar features.

The project feedback recommends using stable terrain structure, including crater rims and ridge lines, particularly when illumination changes make raw brightness less reliable.

Check points should not be selected merely because they produce a favorable metric.

---

# 15. Illumination Considerations

Lunar illumination can change the visual appearance of the same terrain.

Sun-angle differences can alter shadows and apparent intensity, so brightness normalization alone does not guarantee identical terrain appearance.

For check-point creation and verification:

- terrain identity should be considered;
- shadow changes should not automatically be treated as different terrain;
- ambiguous points should be reviewed;
- point correspondence should remain physically meaningful.

The exact annotation procedure for difficult illumination cases is **Not specified**.

---

# 16. Multi-Sensor Considerations

Check-point interpretation must account for the sensor involved.

The project explicitly states that OHRC, TMC-2, and IIRS should not be treated as identical imagery.

The project feedback describes:

- OHRC as high-detail visible panchromatic imagery;
- TMC-2 as panchromatic terrain imagery at a substantially coarser scale;
- IIRS as imaging infrared hyperspectral data requiring an appropriate 2D representation before conventional image matching.

Therefore, check-point datasets should record sensor identity where applicable.

Conceptually:

```text
sensor_source
sensor_target
```

The exact metadata schema is **To be defined**.

---

# 17. Scale Considerations

Check-point accuracy must be interpreted in the context of physical image scale.

The project feedback explicitly warns that upsampling does not recover missing spatial detail. A higher-resolution reference should instead be brought toward a comparable effective scale for coarse matching, followed by refinement only where the source contains sufficient information.

Consequently:

> A check point should not be interpreted as supporting a precision claim beyond the information contained in the source product.

The benchmark must report registration error in source-image pixels first.

Ground-distance conversion should only be made where GSD and projection make the conversion meaningful.

---

# 18. Ground Error vs Source-Pixel Error

The primary registration evaluation unit identified by the project feedback is:

```text
Source-image pixels
```

Ground error in metres may be reported when:

- product ground scale is known;
- projection information is available;
- the conversion is meaningful;
- reference information supports that interpretation.

The project explicitly cautions that the same pixel error does not represent the same physical ground error for sensors with different spatial scales.

Therefore:

```text
0.2 source pixels
```

must not automatically be interpreted as a universal physical accuracy value across OHRC, TMC-2, and IIRS.

---

# 19. Check-Point Error

For each independent check point:

```text
Known Target Point
        │
        │
        ├──────────────┐
        │              │
        ▼              ▼
Estimated Transform    Ground Truth
        │              │
        ▼              ▼
Predicted Location   Known Location
        │              │
        └──────┬───────┘
               ▼
            Residual
```

The resulting residuals can be summarized using the benchmark's registration metrics.

The project feedback specifically identifies:

- check-point RMSE in source pixels;
- ground error in metres when meaningful;
- inlier count;
- inlier ratio;
- spatial coverage;
- runtime;
- failure rate

as relevant measurable outputs.

---

# 20. RMSE Interpretation

Check-point RMSE should be calculated from points that were not used to fit the evaluated transformation.

The conceptual distinction is:

```text
Fit RMSE
```

versus:

```text
Independent Check-Point RMSE
```

The latter is the more relevant quantity for evaluating generalization of the fitted registration model.

The exact RMSE formula, aggregation method, outlier handling, and acceptance threshold belong to the applicable benchmark specification and are not duplicated here.

---

# 21. Residual Analysis

A single RMSE value is not sufficient to understand all geometric failure modes.

The project feedback recommends inspecting residual vectors across the image. Systematic residual changes from one side of an image to another can indicate that a single global transformation is insufficient.

Check-point analysis should therefore support, where implemented:

```text
Check Points
     ↓
Residual Vectors
     ↓
Spatial Inspection
     ↓
Global vs Local Error Pattern
```

Potential causes can include:

- non-planar lunar relief;
- viewing geometry;
- raw-image sensor geometry;
- projection differences;
- an insufficient transformation model.

These are diagnostic considerations, not automatic classifications.

---

# 22. Transformation Model Independence

The check-point dataset must remain independent of the transformation model.

For example, the same check points may be used to evaluate different candidate models:

```text
Check Points
   ├── Evaluate Affine
   ├── Evaluate Homography
   └── Evaluate Other Validated Model
```

The check points should not be changed simply because a different model is being evaluated.

This enables fair model comparison on the same independent observations.

---

# 23. Sub-Pixel Refinement

Sub-pixel refinement is an important part of the ChandraMap registration design.

The project feedback recommends refining verified control/inlier point coordinates locally and then refitting the final transformation.

The conceptual flow is:

```text
Candidate Matches
       ↓
RANSAC
       ↓
Verified Inliers
       ↓
Sub-Pixel Refinement
       ↓
Final Transform
       ↓
Independent Check Points
       ↓
Check-Point RMSE
```

Check points must remain independent throughout this process.

---

# 24. Data Leakage Prevention

Data leakage can occur if check-point information influences development or fitting.

Potential leakage paths include:

### Transformation leakage

```text
Check Point
   ↓
Transform Fitting
```

### Parameter tuning leakage

```text
Check Point
   ↓
Threshold Selection
   ↓
Final Evaluation
```

### Model selection leakage

```text
Check Point Results
   ↓
Choose Best Matcher
   ↓
Report Same Check Point Results
```

### Dataset construction leakage

```text
Evaluation Points
   ↓
Repeated Manual Adjustment
   ↓
Favorable Evaluation
```

These practices should be avoided.

---

# 25. Provenance Requirements

Every production check-point dataset should be traceable to the source data from which it was created.

At minimum, provenance should be capable of identifying:

| Provenance Item                 | Status                   |
| ------------------------------- | ------------------------ |
| Source image identity           | Required                 |
| Target/reference image identity | Required                 |
| Source image version            | Required                 |
| Target image version            | Required                 |
| Coordinate convention           | Required                 |
| Point identity                  | Required                 |
| Annotation origin               | Required                 |
| Verification status             | Required                 |
| Dataset version                 | Required                 |
| Creation/update information     | Required where available |
| Annotation tool                 | To be defined            |
| Reviewer identity               | To be defined            |
| Review timestamp                | To be defined            |

The exact serialization and metadata schema are not confirmed by the reviewed project sources.

---

# 26. Point Identity and Stability

Check-point identifiers should remain stable when the underlying annotation has not changed.

Changing an identifier unnecessarily makes it difficult to:

- compare benchmark versions;
- reproduce evaluations;
- track corrections;
- audit results;
- identify regressions.

If a point's semantic location changes materially, the annotation should be treated as a changed record rather than silently overwriting its history.

The exact identifier-generation strategy is **To be defined**.

---

# 27. Validation

Before a check-point dataset is used for benchmarking, it should undergo validation.

Validation should verify, where applicable:

- source image exists;
- target image exists;
- coordinates are within image bounds;
- coordinate conventions are valid;
- source and target dimensions are consistent with metadata;
- point identifiers are unique;
- source/target coordinates are present;
- provenance is available;
- verification status is valid;
- fit/check separation is preserved;
- no duplicate records exist unintentionally;
- the dataset version is identifiable.

The exact automated validation implementation is **Not confirmed**.

---

# 28. Geometric Sanity Checks

Check-point data should be checked for obvious geometric inconsistencies.

Examples include:

```text
Invalid:
x < 0
y < 0
x >= image_width
y >= image_height
```

and cases where:

- a point is outside the relevant image;
- the image version has changed;
- cropping changed the coordinate frame;
- resampling changed the coordinate system;
- a point no longer corresponds to the same terrain location.

The final implementation should encode the project-approved coordinate convention before applying these checks automatically.

---

# 29. Duplicate and Near-Duplicate Points

Duplicate points should not unintentionally inflate the importance of a single terrain feature.

For example:

```text
Same crater
    ├── Point A
    ├── Point B
    ├── Point C
    └── Point D
```

may provide less independent spatial evidence than points distributed throughout the overlap.

The exact minimum spacing or clustering rule is **Not specified** and should not be invented without a benchmark decision.

---

# 30. Spatial Coverage and Distribution

Check points should be evaluated not only by count but also by distribution.

The project feedback recommends spatial coverage metrics, including grid-based or convex-hull-style coverage, to determine whether good points are distributed across the overlap.

Therefore:

```text
Point Count ≠ Spatial Coverage
```

A large number of points concentrated in one small region should not automatically be interpreted as strong registration evidence.

---

# 31. Benchmark Integration

Check points form part of the benchmark's independent evaluation layer.

A conceptual benchmark evaluation is:

```text
Benchmark Pair
      │
      ├── Fit / Control Data
      │
      └── Independent Check Data
                 │
                 ▼
          Final Transform
                 │
                 ▼
       Check-Point Evaluation
                 │
                 ├── RMSE
                 ├── Residuals
                 └── Spatial Analysis
```

The benchmark should record which check-point version was used for every reported evaluation.

This prevents an experiment from becoming difficult to reproduce because the ground-truth data silently changed.

---

# 32. Baseline Comparison

The project recommends comparing the same test pairs across different matching pipelines, including a SIFT baseline, a stronger matcher, and the complete sensor-aware/multi-scale pipeline.

Check points should remain fixed across such comparisons.

For example:

```text
Same Image Pair
Same Check-Point Set
       │
       ├── SIFT Baseline
       ├── Stronger Matcher
       └── Full Pipeline
```

This allows differences in measured registration quality to be attributed to the evaluated pipeline rather than changing evaluation data.

---

# 33. Sensor-Specific Evaluation

Check-point evaluation should preserve sensor identity.

The project recommends adding sensors gradually and reporting sensor results separately rather than hiding differences inside one mixed average.

Conceptually:

```text
OHRC Evaluation
TMC-2 Evaluation
IIRS Evaluation
        ↓
Separate Results
```

The exact aggregation policy is **To be defined**.

---

# 34. Stress-Test Integration

The project defines several useful stress categories:

- Easy pair;
- Sun-angle stress;
- Scale stress;
- Modality stress;
- Geometry stress;
- Low-feature terrain.

Check points should be associated with the appropriate benchmark case so that registration error can be analyzed under these conditions.

Example:

```text
Stress Case
    ↓
Image Pair
    ↓
Independent Check Points
    ↓
Registration Evaluation
    ↓
Stress-Specific Metrics
```

The exact dataset membership of each stress category is **Not confirmed**.

---

# 35. Failure Cases

A failed registration should not automatically result in removal of the associated check points.

Failure cases are scientifically useful for identifying weaknesses involving:

- scale;
- illumination;
- modality;
- retrieval;
- geometry;
- sub-pixel refinement.

The project feedback explicitly recommends preserving failures and measuring difficult cases rather than presenting only successful examples.

Check-point data should therefore support honest evaluation of both successful and unsuccessful registrations.

---

# 36. Quality-Control Workflow

A recommended project workflow is:

```text
1. Identify image pair
        ↓
2. Establish image metadata
        ↓
3. Create or import correspondence
        ↓
4. Verify correspondence
        ↓
5. Assign stable point identity
        ↓
6. Record provenance
        ↓
7. Assign independent check status
        ↓
8. Validate coordinates
        ↓
9. Verify fit/check separation
        ↓
10. Version dataset
        ↓
11. Use in benchmark evaluation
```

The workflow is a documentation and engineering requirement; the exact tooling is **Not specified**.

---

# 37. Contributor Workflow

Contributors modifying check-point data should:

1. identify the image pair;
2. document the source of the correspondence;
3. preserve coordinate conventions;
4. avoid using evaluation check points for transformation fitting;
5. verify changed points;
6. record meaningful metadata changes;
7. update the dataset version when appropriate;
8. run available validation checks;
9. document known limitations;
10. avoid modifying points solely to improve benchmark scores.

If an automated validation command exists in the repository, it should be used. The reviewed project sources do not confirm a specific command, so no command is prescribed here.

---

# 38. Versioning

Check-point data are benchmark-critical artifacts and should be versioned.

A version change may be required when:

- a point is added;
- a point is removed;
- coordinates are corrected;
- source/reference image identity changes;
- coordinate conventions change;
- provenance changes materially;
- verification status changes materially;
- the independence policy changes.

The exact versioning mechanism is **Not specified**.

A benchmark result should identify the check-point version used.

---

# 39. Reproducibility

A reproducible evaluation should be able to answer:

```text
Which images?
Which image versions?
Which check-point set?
Which check-point version?
Which coordinate convention?
Which transformation?
Which fitting points?
Which evaluation points?
Which metric definition?
```

A reported check-point RMSE without identifying the evaluation dataset is insufficient for full reproducibility.

The project feedback emphasizes measurable outputs and reproducible evidence such as actual inliers, coverage, check-point error, and runtime.

---

# 40. Storage

This document belongs at:

```text
data/ground_truth/CHECKPOINTS.md
```

It documents the semantics and governance of check points.

The actual check-point records, if stored separately, should use a clearly defined machine-readable representation.

The reviewed sources do not confirm:

- the final filename for the records;
- the final file format;
- the directory structure for individual point sets;
- the serialization schema.

Those details are therefore **To be defined** rather than invented here.

---

# 41. Relationship to Other Ground-Truth Documentation

This document should be read together with the project's other confirmed ground-truth documentation where applicable.

The intended conceptual separation is:

```text
data/ground_truth/
│
├── README.md
├── CONTROL_POINTS.md
└── CHECKPOINTS.md
```

Only the semantics relevant to check points are defined here.

`CONTROL_POINTS.md` should remain the authoritative documentation for control-point concepts if that file is part of the confirmed repository structure.

Benchmark-level evaluation rules should remain in the relevant benchmark documentation rather than being duplicated here.

---

# 42. What This File Does Not Define

This file does **not** independently define:

- a new matching algorithm;
- a new RANSAC algorithm;
- a new transformation model;
- a universal lunar coordinate system;
- a final database schema;
- an unconfirmed annotation tool;
- unconfirmed dataset sizes;
- unconfirmed accuracy thresholds;
- unconfirmed point counts;
- unconfirmed benchmark results;
- unconfirmed file formats;
- unconfirmed automation commands.

Those details must come from the corresponding project specifications or implementation.

---

# 43. Current Project Status

Based on the reviewed project materials:

| Area                                                 | Status                             |
| ---------------------------------------------------- | ---------------------------------- |
| Need for independent check-point evaluation          | **Established**                    |
| Check-point role in registration evaluation          | **Established**                    |
| Avoid fitting and judging on identical points        | **Established**                    |
| Use challenge ground truth when available            | **Established recommendation**     |
| Independent tie points as check points when required | **Established recommendation**     |
| Check-point RMSE in source pixels                    | **Established evaluation concept** |
| Spatial coverage evaluation                          | **Established evaluation concept** |
| Residual inspection                                  | **Established recommendation**     |
| Sensor-aware evaluation                              | **Established project direction**  |
| Final machine-readable check-point schema            | **Not confirmed**                  |
| Final check-point storage format                     | **Not confirmed**                  |
| Final annotation workflow/tool                       | **Not confirmed**                  |
| Final point-count requirements                       | **Not confirmed**                  |
| Final acceptance thresholds                          | **Not confirmed in this document** |
| Final automated validation command                   | **Not confirmed**                  |
| Final versioning mechanism                           | **Not confirmed**                  |

---

# 44. Known Limitations

## 44.1 Ground Truth May Not Always Be Available

The project feedback explicitly allows the use of independently checked tie points when challenge ground truth is unavailable.

This means the provenance and verification quality of the check-point set must be documented carefully.

---

## 44.2 Lunar Geometry Is Not Necessarily Planar

The Moon is not a flat image plane.

Relief, viewing geometry, and raw sensor geometry can cause systematic residuals that a single global transformation may not explain.

Check-point errors should therefore be interpreted spatially, not only through one aggregate value.

---

## 44.3 Pixel Error Is Sensor Dependent

A pixel error has different physical meaning for different sensors.

The project explicitly cautions against treating equal pixel errors as equal ground errors across sensors.

---

## 44.4 IIRS Requires Special Treatment

IIRS should not be treated as an ordinary single-channel image without an explicit representation strategy.

The project recommends investigating suitable 2D representations such as selected bands, PCA/composites, or structural representations.

Therefore, IIRS check-point interpretation may depend on the representation used for registration.

---

## 44.5 Point Quality Can Be Ambiguous

A visually identifiable terrain feature under one illumination condition may become difficult to localize under another.

This makes verification and provenance especially important for cross-illumination cases.

---

# 45. Recommended Data Integrity Rules

The following rules should be treated as core integrity requirements for a production check-point dataset:

### Rule 1 — Never fit on evaluation points

```text
Check Point → Evaluation only
```

### Rule 2 — Never assume matcher confidence means truth

```text
Candidate Match ≠ Check Point
```

### Rule 3 — Never assume RANSAC inlier means independent truth

```text
RANSAC Inlier ≠ Independent Check Point
```

### Rule 4 — Preserve point provenance

Every production point must be traceable to its source.

### Rule 5 — Preserve coordinate conventions

Never silently change the coordinate frame.

### Rule 6 — Version benchmark-critical changes

Changes to evaluation data must be traceable.

### Rule 7 — Preserve difficult cases

Do not remove failures simply because they reduce benchmark performance.

### Rule 8 — Report physical units honestly

Report source-pixel error first and convert to metres only where justified.

### Rule 9 — Evaluate spatial distribution

Point count alone does not establish adequate registration coverage.

### Rule 10 — Keep evaluation independent

The check-point set must remain outside the transformation-estimation process.

---

# 46. Example Conceptual Record

The following is a **conceptual representation only**. It is not the confirmed ChandraMap production schema.

```text
check_point_id:
    <stable identifier>

source_image:
    <source image identifier>

target_image:
    <target/reference image identifier>

source_point:
    x: <source x>
    y: <source y>

target_point:
    x: <target x>
    y: <target y>

coordinate_system:
    <explicit convention>

source_sensor:
    <sensor identifier>

target_sensor:
    <sensor identifier>

provenance:
    <origin / annotation source>

verification_status:
    <verified / other project-approved status>

dataset_version:
    <version>

independence:
    excluded_from_transform_fit: true
```

The field names above are illustrative and must not be interpreted as an already-implemented schema.

---

# 47. Evaluation Example

Consider an image pair containing:

```text
Control / Fit Points
    P1
    P2
    P3
    P4
    P5

Independent Check Points
    C1
    C2
    C3
    C4
```

The transformation is estimated using only:

```text
P1 ... P5
```

Then the transformation is applied to:

```text
C1 ... C4
```

The predicted locations are compared with the known target coordinates of:

```text
C1 ... C4
```

The resulting residuals provide independent evidence of registration accuracy.

The check points must not be inserted into the fitting set after observing their error.

---

# 48. Acceptance-Oriented Interpretation

A check-point dataset should be considered suitable for benchmark use only when its independence and provenance can be established.

The following questions should be answerable:

```text
[ ] Are source and target images identified?
[ ] Are coordinates explicitly defined?
[ ] Is the point correspondence verified?
[ ] Is provenance recorded?
[ ] Is the point excluded from transformation fitting?
[ ] Is the point excluded from final refitting?
[ ] Is the dataset version identifiable?
[ ] Are coordinate conventions documented?
[ ] Are points spatially meaningful?
[ ] Can the evaluation be reproduced?
```

If any critical answer is unknown, the dataset should not silently be treated as fully validated ground truth.

---

# 49. Relationship to Final Registration Quality

Check-point accuracy should be interpreted alongside other metrics.

The project identifies several complementary measurements:

| Metric           | Purpose                                              |
| ---------------- | ---------------------------------------------------- |
| Inlier count     | Number of geometrically verified correspondences     |
| Inlier ratio     | Fraction of candidate matches surviving verification |
| Spatial coverage | Distribution of reliable correspondences             |
| Check-point RMSE | Independent registration accuracy                    |
| Ground error     | Physical error where meaningful                      |
| Runtime          | System performance                                   |
| Failure rate     | Reliability across cases                             |

The project feedback explicitly emphasizes that good evaluation matters more than adding another pipeline box, and recommends reporting actual numerical measurements rather than decorative confidence ratings.

Therefore:

```text
More matches
      ≠
Better registration

Lower fitting error
      ≠
Better independent accuracy
```

---

# 50. Scientific Reporting Rule

When publishing or reporting a ChandraMap experiment, distinguish clearly between:

### Observed

Measured from the actual evaluation:

- check-point RMSE;
- residual distribution;
- spatial coverage;
- inlier statistics;
- runtime;
- failure rate.

### Derived

Computed from documented measurements:

- aggregated benchmark metrics;
- sensor-specific summaries;
- stress-test degradation.

### Proposed

Future or recommended behavior:

- new check-point schemas;
- additional validation;
- new annotation workflows;
- new spatial sampling strategies.

### Unknown

Information not yet established:

- final dataset size;
- final schema;
- final thresholds;
- final automated tooling.

This distinction prevents documentation from presenting planned functionality as implemented functionality.

---

# 51. Final Check-Point Specification

For ChandraMap, the essential definition is:

> **A check point is a verified source/target correspondence reserved for independent evaluation of a registration transformation and excluded from the transformation-estimation process.**

The required scientific separation is:

```text
                    ┌─────────────────────┐
                    │ Candidate Matches   │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │ RANSAC / Geometry   │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │ Verified Inliers    │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │ Sub-Pixel Refining  │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │ Final Transformation│
                    └──────────┬──────────┘
                               │
                               │ evaluated on
                               ▼
                    ┌─────────────────────┐
                    │ Independent Check   │
                    │ Points              │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │ Check-Point RMSE    │
                    │ + Residual Analysis │
                    └─────────────────────┘
```

The core requirement is therefore:

```text
Fit on one set.
Evaluate on another.
```

This separation is essential for making ChandraMap's reported registration accuracy meaningful, reproducible, and resistant to evaluation leakage. The project feedback specifically identifies independent check-point error as the evidence required to demonstrate that the final transformation works on points that were not used to fit it.
