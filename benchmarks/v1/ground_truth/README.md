# Ground Truth Baseline

This directory documents the **ChandraMap ground-truth baseline/component** used to support benchmark validation for lunar image correspondence and registration.

Ground truth provides independently established correspondence and evaluation information against which candidate matches, geometrically verified inliers, transformations, and registration results can be evaluated.

It is **not an image-matching model** and should not be treated as an algorithmic baseline such as SIFT. It is an evaluation asset and supporting benchmark component.

The supplied project feedback emphasizes that registration quality must be measured on points that were not used to fit the transformation, using independent check points or challenge-provided ground truth where available.

> **Repository status:** The supplied project materials define the ground-truth role and evaluation principles, but do not confirm the final implemented filenames, schemas, scripts, commands, or dataset counts in this directory. Any such implementation-specific details below are therefore marked accordingly rather than invented.

---

## Overview

The ChandraMap benchmark evaluates correspondence and registration between lunar images that may differ in:

- Spatial scale and GSD
- Illumination and Sun angle
- Sensor characteristics
- Image resolution
- Viewpoint and imaging geometry
- Terrain appearance and modality

The ground-truth component provides the reference information required to determine whether a predicted correspondence or registration result is actually correct.

The evaluation flow distinguishes several different types of points:

```text
Ground-Truth Correspondences
            │
            ├───────────────┐
            │               │
            ▼               ▼
Candidate Matches       Independent
from Algorithm          Evaluation Points
            │               │
            ▼               │
    RANSAC / Geometry       │
            │               │
            ▼               │
     Verified Inliers       │
            │               │
            ▼               │
   Sub-Pixel Refinement     │
            │               │
            ▼               │
     Final Transform ───────┘
            │
            ▼
   Check-Point Evaluation
            │
            ▼
       RMSE / Accuracy
```

The distinction is important:

- **Candidate matches** are produced by the matching system.
- **Verified inliers** are candidate matches that survive geometric verification.
- **Ground-truth points** represent independently established correspondence information.
- **Check points** are evaluation points that must not be used to fit the transformation when they are intended to measure registration accuracy.

The project feedback explicitly warns that matcher confidence is not proof of geometric correctness and that RANSAC/geometric verification should determine which candidate matches become verified inliers.

---

## Role in ChandraMap

Ground truth sits between the dataset and the evaluation system.

```text
ChandraMap
│
├── Dataset
│   ├── Source imagery
│   ├── Reference imagery
│   └── Metadata
│
├── Baselines
│   ├── SIFT
│   └── Other algorithmic baselines
│
├── Ground Truth
│   └── Ground-Truth Baseline
│
├── Evaluation
│   ├── Metrics
│   ├── Stress Tests
│   └── Acceptance Criteria
│
└── Reproducibility
```

The ground-truth component should therefore be treated as **evaluation infrastructure**, not as another competing matcher.

The benchmark feedback identifies the main measurable outputs as correspondence quality, transformation/registration accuracy, spatial distribution, RMSE, inlier statistics, runtime, and failure behavior.

---

## Relationship to the V1 Benchmark

This directory does **not** replace:

`benchmarks/v1/GROUND_TRUTH_PROTOCOL.md`

The V1 protocol is the benchmark-wide source of truth for how ground truth and independent evaluation points are defined and used.

This README documents the implementation/component that supplies or stores that information.

### Responsibility split

| Document / Component                          | Responsibility                                       |
| --------------------------------------------- | ---------------------------------------------------- |
| `benchmarks/v1/GROUND_TRUTH_PROTOCOL.md`      | Benchmark-wide ground-truth rules                    |
| `benchmarks/baselines/ground_truth/README.md` | Ground-truth component documentation                 |
| Ground-truth data artifacts                   | Actual reference correspondence/evaluation data      |
| Evaluation code                               | Consumes ground truth to calculate benchmark metrics |
| `benchmarks/v1/METRICS.md`                    | Metric definitions and calculations                  |
| `benchmarks/v1/ACCEPTANCE_CRITERIA.md`        | Acceptance and validity conditions                   |
| `benchmarks/v1/STRESS_TESTS.md`               | Stress-test definitions                              |

If the implementation and protocol disagree, the benchmark protocol remains authoritative for benchmark evaluation behavior.

---

## Ground Truth Is Not Predicted Data

Ground truth must not be generated by simply accepting the output of the method being evaluated.

The following are **not automatically ground truth**:

- SIFT matches
- ORB matches
- ALIKED matches
- LightGlue matches
- LoFTR correspondences
- Matcher confidence scores
- RANSAC inliers
- A fitted affine transformation
- A fitted homography
- A visually attractive image overlay
- A registered mosaic

These outputs are produced by the system under evaluation.

For example:

```text
SIFT
  │
  ▼
Candidate Matches
  │
  ▼
RANSAC
  │
  ▼
Verified Inliers
  │
  ▼
Final Transform
```

This pipeline produces **predictions and derived results**, not independent ground truth.

The project feedback specifically distinguishes candidate matches from verified inliers and requires geometric verification before treating matches as geometrically reliable.

---

## Ground-Truth Independence

Ground truth must remain independent from the predictions being evaluated.

The most important separation is:

```text
                    ┌─────────────────────┐
                    │    Ground Truth     │
                    │ independent source  │
                    └──────────┬──────────┘
                               │
                               ▼
                         Evaluation
                               ▲
                               │
┌───────────────┐       ┌──────┴───────┐
│ Image Pair    │ ────► │ ChandraMap   │
│ + Metadata    │       │   Pipeline   │
└───────────────┘       └──────────────┘
                               │
                               ▼
                     Candidate / Inlier /
                     Transform Results
```

Ground-truth information must not be exposed to the evaluated algorithm in a way that allows it to directly reproduce the evaluation answer.

---

## Independent Check Points

Independent check points are especially important for registration evaluation.

A transformation should not be fitted and judged on exactly the same points.

For example:

```text
Ground-Truth Points
        │
        ├──► Control / fitting points
        │          │
        │          ▼
        │      Fit Transform
        │
        └──► Independent Check Points
                   │
                   ▼
             Evaluate Transform
                   │
                   ▼
          Check-Point RMSE
```

The project feedback explicitly states that reporting error on the same points used to estimate the transformation can make registration appear better than its true generalization quality. It recommends challenge ground truth where available, or independently checked tie points when it is not.

### Check-point rules

Independent evaluation check points should not be used for:

- Parameter tuning
- Matcher selection
- Model selection
- RANSAC threshold selection
- Transformation selection
- Post-hoc optimization

unless the benchmark protocol explicitly defines an exception.

The exact number, construction procedure, spatial distribution requirement, and acceptance thresholds are governed by the V1 ground-truth protocol and related benchmark documents.

Where the supplied project data does not define a particular numeric requirement, it is:

**To be defined**

rather than an invented value.

---

## Ground-Truth Data Representation

The exact production schema for this directory is:

**Not confirmed by the supplied project data.**

The representation should nevertheless preserve enough information to make every evaluation point auditable.

At minimum, a ground-truth record should be capable of identifying:

| Information                       | Purpose                                                   | Status                              |
| --------------------------------- | --------------------------------------------------------- | ----------------------------------- |
| Image-pair identity               | Identifies the evaluated source/reference pair            | Required concept                    |
| Source coordinates                | Identifies the point in the source image                  | Required concept                    |
| Reference coordinates             | Identifies the corresponding point                        | Required concept                    |
| Point identity                    | Allows traceability of individual points                  | Recommended                         |
| Point role                        | Distinguishes control and check points                    | Required for independent evaluation |
| Ground-truth version              | Makes evaluation reproducible                             | Required concept                    |
| Source/reference metadata         | Provides dataset context                                  | Required where available            |
| Validation/provenance information | Explains how the point was established                    | Required concept                    |
| Quality/uncertainty information   | Records known confidence or uncertainty where available   | To be defined                       |
| Sensor/product information        | Distinguishes OHRC, TMC-2, IIRS, reference products, etc. | Required where applicable           |

The actual field names and serialization format are:

**Not confirmed.**

Do not introduce a schema into implementation merely because this README describes the information that needs to be represented. The final schema should be defined by the appropriate project specification.

---

## Coordinate Convention

Ground-truth coordinates must remain unambiguous.

For image-registration evaluation, the primary accuracy reporting unit is **source-image pixels**.

Ground error in metres should only be derived when the relevant:

- Ground scale/GSD
- Projection
- Reference information
- Coordinate relationship

make that conversion scientifically meaningful.

The project feedback explicitly recommends reporting sub-pixel error in source-image pixels first and warns that the same pixel error does not represent the same physical distance for sensors with different GSDs.

The exact coordinate convention, indexing convention, axis order, and pixel-center convention are:

**To be defined / governed by the V1 ground-truth protocol.**

---

## Sensor and Modality Considerations

Ground truth should preserve sensor identity rather than treating all lunar imagery as equivalent.

The project materials distinguish:

- **OHRC** — high-detail visible panchromatic imagery
- **TMC-2** — panchromatic terrain imagery
- **IIRS** — hyperspectral/infrared imaging data
- **LRO reference imagery**, where used by the benchmark

The project feedback states that OHRC, TMC-2, and IIRS should not be forced through an identical processing path. In particular, IIRS may require a registration-friendly 2D representation such as a selected band, PCA/composite, or structural representation before conventional image matching.

Ground truth should therefore retain enough metadata to identify the relevant sensor/product path.

### Important scale principle

Ground truth must not imply spatial detail that the source sensor cannot physically resolve.

Upsampling an image does not create missing spatial information.

The project feedback recommends comparing imagery at physically meaningful effective scales and using a reference pyramid or downsampling when there is a large resolution difference.

---

## Ground Truth and Candidate Correspondences

The evaluation relationship is:

```text
Ground Truth
     │
     │ independent reference
     ▼
┌──────────────────┐
│ Candidate Matches│ ◄── SIFT / learned matcher / other method
└────────┬─────────┘
         │
         ▼
   Geometric Verification
         │
         ▼
   Verified Inliers
         │
         ▼
   Sub-Pixel Refinement
         │
         ▼
   Final Transformation
         │
         ▼
   Independent Evaluation
```

Candidate correspondences may be numerous and may include incorrect matches.

A high matcher confidence score does not make a correspondence ground truth.

Likewise, a high number of matches is not sufficient if those matches are incorrect or spatially clustered.

The project feedback explicitly emphasizes that more matches are not necessarily better and that well-distributed correspondences should be measured.

---

## Ground Truth and RANSAC Inliers

RANSAC serves a different purpose from ground truth.

### Ground truth

Provides an independent reference for evaluating correctness.

### RANSAC

Provides geometric verification by identifying a set of correspondences consistent with an estimated transformation model.

Conceptually:

```text
Ground Truth
     │
     └───────────────┐
                     │
Candidate Matches ─► RANSAC
                     │
                     ▼
              Verified Inliers
                     │
                     ▼
              Final Transform
                     │
                     ▼
             Ground-Truth Check
```

RANSAC inliers should therefore not automatically be copied into the ground-truth dataset.

They are algorithm-generated results.

---

## Ground Truth and Registration Evaluation

Ground truth supports evaluation of the final registration rather than merely the matching stage.

A typical evaluation chain is:

```text
Source Image
     │
     ▼
Candidate Matches
     │
     ▼
RANSAC / Initial Model
     │
     ▼
Verified Inliers
     │
     ▼
Sub-Pixel Refinement
     │
     ▼
Refit Final Transform
     │
     ▼
Registered Image
     │
     ▼
Independent Check Points
     │
     ▼
Registration Error
```

The project feedback recommends the sequence:

**candidate matches → RANSAC + initial model → verified inliers → sub-pixel refinement → refit final transform.**

---

## Metrics Supported by Ground Truth

Ground truth can support multiple benchmark measurements.

| Evaluation Stage     | Metric / Evidence                         | Ground Truth Role                                 |
| -------------------- | ----------------------------------------- | ------------------------------------------------- |
| Global retrieval     | Recall@1 / Recall@5, if retrieval is used | Identifies correct region/candidate               |
| Local matching       | Inlier count                              | Supports interpretation of verified matches       |
| Local matching       | Inlier ratio                              | Helps evaluate geometrically consistent matches   |
| Spatial distribution | Grid or convex-hull coverage              | Provides overlap/evaluation context               |
| Registration         | Check-point RMSE                          | Primary independent accuracy evidence             |
| Geospatial accuracy  | Ground error in metres                    | Only when scientifically meaningful               |
| System evaluation    | Failure rate                              | Determines whether valid evaluation was completed |

The project feedback identifies inlier count, inlier ratio, spatial coverage, check-point RMSE in source pixels, meaningful ground error, runtime, and failure rate as relevant measurable outputs.

The exact formulas and aggregation rules belong to the V1 metrics documentation, not this README.

---

## Ground-Truth Generation and Curation

The exact production procedure for creating the ChandraMap ground-truth dataset is:

**Not specified in the supplied project materials.**

The project materials establish the following principles:

1. Use challenge ground truth when it is available.
2. Otherwise use independently checked tie points as evaluation check points.
3. Do not fit the transformation using points reserved for independent evaluation.
4. Preserve image dimensions, product type, pixel scale/GSD, and relevant geolocation/map-projection metadata.
5. Preserve enough provenance to understand how evaluation points were established.

The project feedback explicitly identifies the need to confirm the supplied formats, metadata, reference product, and evaluation rule before implementation.

No unsupported statement is made here about who manually annotated points, what software was used, how many annotators were involved, or what numerical annotation uncertainty was achieved.

Those details are:

**To be defined / Not confirmed.**

---

## Ground-Truth Validation

Ground truth itself requires validation.

Validation should establish that:

- The source/reference pair is correctly identified.
- Corresponding points refer to the same physical lunar features or locations.
- Coordinates are stored in the correct image coordinate system.
- Metadata is associated with the correct product.
- Control points and check points are correctly separated.
- No evaluation point has unintentionally entered the fitting/tuning process.
- The ground-truth version is identifiable.
- Changes are traceable.

### Validation status

The supplied project materials do not confirm an implemented ground-truth validation script or automated validation command.

Therefore:

**Automated validation implementation: Not confirmed.**

**Validation requirements: Defined conceptually; exact implementation to be defined.**

---

## Ground-Truth Versioning

Every benchmark evaluation must be traceable to the ground-truth version used.

A ground-truth version should identify the complete logical state of the evaluation data rather than relying only on a directory modification date.

At minimum, version tracking should make it possible to determine:

```text
Ground-Truth Version
        │
        ├── Image-pair definitions
        ├── Ground-truth coordinates
        ├── Control/check-point assignments
        ├── Metadata
        ├── Provenance
        └── Validation state
```

### Version changes

Changes that can affect evaluation should be versioned, including:

- Added image pairs
- Removed image pairs
- Changed point coordinates
- Changed point roles
- Changed metadata
- Corrected correspondence assignments
- Changed validation status
- Changed annotation/provenance information
- Changed serialization/schema where it affects interpretation

The exact versioning mechanism is:

**Not confirmed.**

Recommended practice:

- Treat released ground-truth datasets as immutable evaluation inputs.
- Give each released dataset an explicit version identifier.
- Record the version in benchmark results.
- Do not silently replace an existing released ground-truth dataset.

---

## Ground-Truth Leakage Protection

Ground truth must be protected from evaluation leakage.

### Leakage can occur when:

- Evaluation points are used to tune the matcher.
- Evaluation points are used to choose a transformation.
- Ground-truth coordinates are exposed directly to the evaluated pipeline.
- Test pairs are repeatedly used during development without being treated as development data.
- Ground-truth-derived thresholds are optimized against the final test set.
- A transformation is fitted and evaluated on the same points.
- Ground-truth corrections are made after inspecting final benchmark results without versioning the change.

### Required separation

```text
Development / Tuning
        │
        ▼
Training / Configuration / Threshold Selection
        │
        X
        │
        ▼
Protected Evaluation Ground Truth
        │
        ▼
Final Benchmark Evaluation
```

Independent check points should remain protected from parameter and model selection unless the benchmark protocol explicitly permits their use.

This separation follows the project requirement that transformations should not be fitted and judged on exactly the same points.

---

## What Must Not Be Stored as Ground Truth

The following should not be silently promoted into the ground-truth dataset:

```text
❌ SIFT matches
❌ ORB matches
❌ LightGlue matches
❌ LoFTR matches
❌ Matcher confidence
❌ RANSAC inliers
❌ Predicted homography
❌ Predicted affine transform
❌ Registration residuals from the evaluated method
❌ Evaluation RMSE
❌ Post-hoc corrected predictions
```

These may be stored as **evaluation outputs**, but they are not independent ground truth.

---

## Expected Artifact Organization

The final repository contents for this directory are:

**Not confirmed by the supplied project data.**

A possible organization may eventually include artifacts conceptually similar to:

```text
benchmarks/
└── baselines/
    └── ground_truth/
        ├── README.md
        ├── data/
        │   └── <ground-truth artifacts>
        ├── schemas/
        │   └── <ground-truth schemas>
        ├── metadata/
        │   └── <dataset metadata>
        └── validation/
            └── <validation artifacts or tools>
```

This is a **documentation-level recommendation**, not a claim that these directories currently exist.

Do not create or reference these paths as implemented project interfaces until they are actually defined by the repository.

---

## Artifact Requirements

Ground-truth artifacts should be categorized by their role.

### Required conceptual artifacts

| Artifact                         | Purpose                                                       | Status           |
| -------------------------------- | ------------------------------------------------------------- | ---------------- |
| Ground-truth correspondence data | Stores independent correspondence information                 | Required concept |
| Image-pair identity              | Associates points with source/reference imagery               | Required concept |
| Coordinate information           | Identifies corresponding locations                            | Required concept |
| Point-role information           | Separates fitting/control points from evaluation check points | Required concept |
| Ground-truth version             | Enables reproducibility                                       | Required concept |
| Provenance                       | Makes the data auditable                                      | Required concept |

### Potential supporting artifacts

| Artifact                 | Purpose                             | Status        |
| ------------------------ | ----------------------------------- | ------------- |
| Schema definition        | Validates serialized ground truth   | To be defined |
| Metadata manifest        | Describes products and image pairs  | Recommended   |
| Validation report        | Records validation results          | Recommended   |
| Checksums                | Detects accidental modification     | Recommended   |
| Annotation documentation | Explains point-generation procedure | Recommended   |
| Dataset changelog        | Records ground-truth revisions      | Recommended   |

No specific filenames are asserted because the supplied project materials do not confirm them.

---

## Reproducibility

A researcher should be able to determine:

1. Which ground-truth version was used.
2. Which source/reference image pair was evaluated.
3. Which ground-truth points belonged to that pair.
4. Which points were fitting/control points.
5. Which points were independent check points.
6. Which metadata applied to the products.
7. How the ground truth was generated or validated, when documented.
8. Which benchmark metrics were calculated from it.
9. Whether the ground truth changed between benchmark runs.

A reproducible benchmark record should conceptually preserve:

```text
Benchmark Run
│
├── Ground-Truth Version
├── Dataset / Pair Identity
├── Source Product Metadata
├── Reference Product Metadata
├── Point Set / Point Roles
├── Evaluation Configuration
├── Metrics
└── Results / Evidence
```

The exact command used to reproduce the ground-truth generation or validation is:

**Not specified in the supplied project data.**

---

## Ground Truth and Stress Testing

Ground truth should remain compatible with the benchmark's stress-test structure.

The supplied project feedback identifies the following stress categories:

- Easy pair
- Sun-angle stress
- Scale stress
- Modality stress
- Geometry stress
- Low-feature terrain

These cases are intended to expose different failure modes rather than treating all image pairs as equivalent.

Ground-truth records should therefore retain enough context to identify the relevant test condition where the benchmark dataset assigns such a category.

The exact stress-test metadata schema is governed by:

`benchmarks/v1/STRESS_TESTS.md`

---

## Ground Truth and Sensor-Specific Evaluation

Ground truth should not erase the differences between sensors.

### OHRC

OHRC imagery is described in the supplied materials as high-detail visible panchromatic imagery suitable for fine terrain correspondence.

The challenge product metadata should remain authoritative for the actual pixel scale used in evaluation.

### TMC-2

TMC-2 is described as panchromatic terrain imagery at approximately the metre-scale appropriate to its product context.

Ground error calculations must use the actual product metadata rather than assuming a universal value.

### IIRS

IIRS is hyperspectral/infrared data with substantially coarser spatial resolution than OHRC and many LRO reference products.

A registration-friendly 2D representation may be required before conventional image correspondence methods are applied.

Ground truth must not be interpreted as evidence that the IIRS source contains fine spatial information that it cannot resolve.

---

## Metadata Preservation

Where available, the ground-truth dataset should preserve or reference relevant product metadata, including:

- Product identity
- Sensor
- Image dimensions
- Pixel scale/GSD
- Footprint
- Latitude/longitude information
- Map projection
- Viewing geometry
- Lighting/Sun-angle information
- Product processing state

The project feedback specifically recommends preserving footprint, pixel scale, map projection, and lighting/viewing geometry when available.

The exact metadata schema is:

**To be defined.**

---

## Visual Inspection

Visual overlays can be useful for diagnosing ground-truth or registration problems.

Useful evidence may include:

- Source/reference image pair
- Ground-truth points
- Candidate matches
- RANSAC inliers
- Independent check points
- Registered overlay
- Residual vectors

However, visual agreement alone is not sufficient evidence of registration accuracy.

The project feedback explicitly notes that a visually good overlay can still be scientifically wrong and recommends inspecting residuals and evaluating independent check points.

---

## Limitations

The following limitations apply unless later project documentation provides more precise information.

### 1. Final ground-truth construction procedure

The supplied materials do not fully specify how the production ground truth is generated.

**Status:** Not specified.

### 2. Exact ground-truth schema

The final field names and serialization format are not confirmed.

**Status:** To be defined.

### 3. Dataset size

No authoritative final number of image pairs or ground-truth points is established by the supplied materials.

**Status:** Not specified.

### 4. Annotation uncertainty

A numerical uncertainty model for ground-truth coordinates is not specified.

**Status:** To be defined.

### 5. Automated validation

A confirmed implementation of automated ground-truth validation is not provided.

**Status:** Not confirmed.

### 6. Exact versioning mechanism

The supplied materials establish the requirement for traceability but do not define the final versioning implementation.

**Status:** To be defined.

### 7. Geospatial error conversion

Ground error in metres is only meaningful when the necessary scale and projection/reference information support that conversion.

**Status:** Conditional.

---

## Implementation Status

This README intentionally distinguishes requirements from confirmed implementation.

| Capability                                          | Status        |
| --------------------------------------------------- | ------------- |
| Ground truth as an independent evaluation reference | Specified     |
| Independent check-point evaluation                  | Specified     |
| Separation of fitting and evaluation points         | Specified     |
| Ground-truth version traceability                   | Required      |
| Ground-truth provenance                             | Required      |
| Ground-truth schema                                 | Not confirmed |
| Ground-truth dataset count                          | Not specified |
| Automated ground-truth validation                   | Not confirmed |
| Ground-truth generation command                     | Not specified |
| Ground-truth release/version mechanism              | To be defined |
| Annotation uncertainty model                        | To be defined |
| Exact artifact filenames                            | Not confirmed |

This table should be updated when the repository implementation establishes authoritative details.

---

## Recommended Ground-Truth Validation Checklist

Before a ground-truth release is used for benchmark evaluation, verify:

### Dataset identity

- [ ] Source image is uniquely identified.
- [ ] Reference image is uniquely identified.
- [ ] Sensor/product identities are recorded.
- [ ] Relevant metadata is associated with the correct image pair.

### Coordinates

- [ ] Source coordinates are valid.
- [ ] Reference coordinates are valid.
- [ ] Coordinate conventions are documented.
- [ ] No unintended coordinate transformation has been applied.

### Point independence

- [ ] Control/fitting points are identified.
- [ ] Independent check points are identified.
- [ ] Check points are not used for fitting.
- [ ] Check points are not used for tuning or model selection.

### Provenance

- [ ] Ground-truth version is recorded.
- [ ] Point provenance is available.
- [ ] Validation status is recorded.
- [ ] Changes from the previous version are traceable.

### Leakage protection

- [ ] Evaluation ground truth is not exposed to the evaluated algorithm.
- [ ] Test points have not been used for parameter tuning.
- [ ] Test results have not been used to silently modify ground truth.
- [ ] Any correction produces a new traceable ground-truth version.

### Scientific validity

- [ ] Ground truth is independent of predictions.
- [ ] Ground truth is not derived from the evaluated matcher.
- [ ] Registration is evaluated on independent points.
- [ ] RMSE is reported in the appropriate coordinate unit.
- [ ] Ground error is only reported when physically meaningful.

---

## Change Management

Ground-truth changes can change benchmark results and must therefore be treated as benchmark-affecting changes.

### Do not silently modify released ground truth

If a correspondence point is corrected:

```text
Old Ground Truth
       │
       ▼
Identify correction
       │
       ▼
Validate correction
       │
       ▼
Create new version
       │
       ▼
Record change
       │
       ▼
Re-run affected evaluations
```

Do not overwrite a previously released benchmark version and continue reporting results as though the data had not changed.

### Changes requiring traceability

At minimum:

- Point-coordinate changes
- Point-role changes
- Image-pair changes
- Metadata changes affecting interpretation
- Validation-status changes
- Schema changes affecting meaning

The exact release procedure is:

**To be defined.**

---

## Relationship to Algorithmic Baselines

Ground truth and algorithmic baselines have different responsibilities.

| Component              | Produces                                       | Used for                |
| ---------------------- | ---------------------------------------------- | ----------------------- |
| Ground-truth component | Independent reference information              | Evaluation              |
| SIFT baseline          | Candidate correspondences and geometric result | Baseline comparison     |
| Learned matcher        | Candidate correspondences                      | Method evaluation       |
| RANSAC                 | Geometrically consistent inliers/model         | Verification            |
| Registration stage     | Final transformation/registered image          | Registration evaluation |
| Metrics                | Numerical evaluation                           | Benchmark reporting     |

The project recommends starting with a simple SIFT baseline and comparing it with stronger matching paths using the same image pairs and measurable outputs.

Ground truth remains independent of which algorithmic baseline is being evaluated.

---

## What Ground Truth Does Not Guarantee

The presence of ground truth does not guarantee that an algorithm will:

- Find the correct region.
- Produce candidate matches.
- Produce enough geometrically consistent matches.
- Produce well-distributed matches.
- Estimate a valid transformation.
- Achieve sub-pixel registration.
- Work under all illumination conditions.
- Work across all sensor modalities.
- Work across all scale differences.
- Produce a visually satisfactory registration.

Ground truth enables these properties to be **measured**.

It does not establish them automatically.

---

## Scientific Integrity

Ground-truth data is one of the most sensitive components of a benchmark because changing it can directly change reported results.

ChandraMap therefore follows these principles:

1. **Do not fabricate ground-truth points.**
2. **Do not fabricate ground-truth accuracy.**
3. **Do not fabricate validation results.**
4. **Do not convert predictions into ground truth without independent justification.**
5. **Do not evaluate a transform only on the points used to fit it.**
6. **Do not expose protected evaluation points to the evaluated system.**
7. **Do not silently modify released ground truth.**
8. **Do not report unsupported dataset counts or performance values.**
9. **Do not use decorative confidence percentages as substitutes for measured metrics.**
10. **Keep failures and difficult cases traceable.**

The project feedback explicitly recommends replacing unmeasured confidence/rating claims with actual RMSE, inlier ratio, coverage, and runtime measurements.

---

## Source-of-Truth Boundaries

This README should be used together with the benchmark documentation rather than as a replacement for it.

```text
benchmarks/README.md
        │
        ▼
Overall Benchmark Suite
        │
        ▼
benchmarks/v1/README.md
        │
        ▼
V1 Benchmark
        │
        ├── BENCHMARK_SPEC.md
        │       └── Benchmark contract
        │
        ├── GROUND_TRUTH_PROTOCOL.md
        │       └── Ground-truth rules
        │
        ├── METRICS.md
        │       └── Metric definitions
        │
        ├── STRESS_TESTS.md
        │       └── Stress-test rules
        │
        └── ACCEPTANCE_CRITERIA.md
                └── Acceptance conditions

benchmarks/baselines/ground_truth/README.md
        │
        └── Ground-truth component documentation
```

The component README should describe implementation and artifacts without redefining benchmark-wide rules.

---

## Current Data Availability

The supplied project materials identify potential lunar data sources including:

- Chandrayaan-2 OHRC
- Chandrayaan-2 TMC-2
- Chandrayaan-2 IIRS
- LRO NAC
- LRO WAC
- Optional additional lunar datasets

The project materials describe LRO NAC as a possible reference source and identify other datasets as potential additional training or evaluation resources.

However, the supplied materials do **not** establish the final ground-truth dataset contents for this directory.

Therefore this README does not claim:

- A specific number of image pairs
- A specific number of points
- A specific geographic coverage
- A specific annotation method
- A specific file format
- A specific checksum
- A specific validation score
- A specific benchmark result

Those values must be added only when they are established by the repository.

---

## Future Extensions

The following are reasonable extensions but are not claimed as currently implemented:

- Automated schema validation
- Automated coordinate-range validation
- Ground-truth integrity checks
- Dataset checksums
- Ground-truth manifests
- Point-level provenance
- Annotation uncertainty
- Ground-truth visualization tools
- Dataset diffing between versions
- Leakage detection
- Automated control/check-point separation checks
- Ground-truth release reports

These are **Recommended**, not current implementation claims.

---

## Maintainer Checklist

Before accepting a new ground-truth version:

- [ ] Confirm the image-pair identities.
- [ ] Confirm source/reference metadata.
- [ ] Confirm coordinate conventions.
- [ ] Confirm point provenance.
- [ ] Separate control points from independent check points.
- [ ] Confirm check points were not used for fitting or tuning.
- [ ] Validate the ground-truth records.
- [ ] Assign a traceable version.
- [ ] Record changes from the previous version.
- [ ] Confirm benchmark consumers can identify the version.
- [ ] Re-run affected evaluation where required.
- [ ] Preserve previous released versions.
- [ ] Update this README if the implementation changes.
- [ ] Do not add unsupported metrics, counts, or claims.

---

## Summary of Ground-Truth Contract

The ground-truth component exists to make ChandraMap benchmark evaluation scientifically traceable.

Its essential properties are:

```text
Independent
    +
Versioned
    +
Auditable
    +
Validated
    +
Leakage-Protected
    +
Coordinate-Explicit
    +
Sensor-Aware
    +
Reproducible
```

The central rule is:

> **Ground truth evaluates the correspondence and registration system; it must not become another output of the system being evaluated.**

The ChandraMap evaluation design specifically requires separation between candidate matches, geometrically verified inliers, refined tie points, final transformations, and independent check-point evaluation.

---

## Related Documentation

- `benchmarks/README.md` — Overall benchmark suite
- `benchmarks/v1/README.md` — V1 benchmark overview
- `benchmarks/v1/BENCHMARK_SPEC.md` — V1 benchmark specification
- `benchmarks/v1/GROUND_TRUTH_PROTOCOL.md` — Benchmark-wide ground-truth protocol
- `benchmarks/v1/METRICS.md` — Metric definitions
- `benchmarks/v1/STRESS_TESTS.md` — Stress-test definitions
- `benchmarks/v1/ACCEPTANCE_CRITERIA.md` — Benchmark acceptance criteria
- `benchmarks/baselines/` — Algorithmic and evaluation baselines

---

## Source Basis

This README is grounded in the supplied ChandraMap/SIH 26166 project materials, particularly the technical feedback covering independent check points, ground-truth usage, RANSAC/inlier separation, source-pixel RMSE, spatial coverage, sensor-specific processing, scale handling, and benchmark evaluation.

Supporting project material also emphasizes that the first measurable milestone should take a known source/reference pair through candidate matches, verified inliers, final transformation, registered output, and numerical error on independent check points.

Where the supplied project data does not establish an implementation detail, this README intentionally labels that detail as **Not specified**, **Not confirmed**, **To be defined**, or **Recommended** rather than inventing repository behavior.
