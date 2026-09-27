# `data/`

The `data/` directory is the data boundary for **ChandraMap**: a lunar image correspondence and registration system designed to work with lunar imagery that can differ in sensor, spatial scale, illumination, viewpoint, and image characteristics.

This directory is intended to keep **source data, benchmark data, reference information, ground-truth/evaluation data, and generated artifacts** clearly separated so that experiments remain reproducible and algorithm outputs are not confused with reference truth.

> **Repository status:** The exact implemented contents of `data/` are not fully confirmed by the currently available project materials. Where this document describes a future organization, it is explicitly marked **Recommended**, **Planned**, or **Not yet defined** rather than being presented as an existing repository structure.

---

## Table of Contents

- [Purpose](#purpose)
- [ChandraMap Data Philosophy](#chandramap-data-philosophy)
- [What Data ChandraMap Works With](#what-data-chandramap-works-with)
- [Confirmed Project Data Sources](#confirmed-project-data-sources)
- [Data Lifecycle](#data-lifecycle)
- [Recommended Data Organization](#recommended-data-organization)
- [Data Categories](#data-categories)
- [Raw Data vs Derived Data](#raw-data-vs-derived-data)
- [Correspondence Data](#correspondence-data)
- [Ground Truth and Check Points](#ground-truth-and-check-points)
- [Benchmark Data](#benchmark-data)
- [Reference and Expected Data](#reference-and-expected-data)
- [Generated and Cached Data](#generated-and-cached-data)
- [Data Flow Through ChandraMap](#data-flow-through-chandramap)
- [Sensor-Aware Data Handling](#sensor-aware-data-handling)
- [Scale and Resolution Considerations](#scale-and-resolution-considerations)
- [Illumination Considerations](#illumination-considerations)
- [Metadata and Provenance](#metadata-and-provenance)
- [Naming and Organization Conventions](#naming-and-organization-conventions)
- [Version Control Policy](#version-control-policy)
- [What Should Not Be Committed](#what-should-not-be-committed)
- [Adding a New Dataset](#adding-a-new-dataset)
- [Data Validation and Quality Control](#data-validation-and-quality-control)
- [Benchmark Integrity Rules](#benchmark-integrity-rules)
- [Failure and Rejected Data](#failure-and-rejected-data)
- [Privacy, Security, and Licensing](#privacy-security-and-licensing)
- [Reproducibility Requirements](#reproducibility-requirements)
- [Current vs Planned Data Architecture](#current-vs-planned-data-architecture)
- [Related Documentation](#related-documentation)
- [Source Basis](#source-basis)

---

## Purpose

The `data/` directory exists to provide a controlled boundary between **data used by ChandraMap** and the source code, documentation, configuration, and benchmark definitions that operate on that data.

For a lunar image correspondence and registration system, this separation is particularly important because an experiment may involve:

- A source lunar image.
- A reference lunar image.
- Sensor-specific metadata.
- Different effective ground scales.
- Different illumination conditions.
- Candidate correspondences.
- Geometrically verified correspondences.
- Transformation estimates.
- Independently checked points.
- Registration outputs.
- Benchmark results.
- Stress-test variants.

These artifacts have different scientific meanings and must not be treated as interchangeable.

The central rule is:

> **Reference data, ground truth, algorithm predictions, and evaluation outputs must remain distinguishable throughout the experiment lifecycle.**

---

## ChandraMap Data Philosophy

ChandraMap is not simply an image-processing project. Its core task is to establish **reliable correspondence between lunar images and evaluate the resulting registration quantitatively**.

The project materials emphasize that imagery from different lunar sensors can differ substantially because of:

- Sensor modality.
- Spatial resolution.
- Effective ground scale.
- Illumination and Sun angle.
- Viewpoint.
- Image characteristics.
- Geometric conditions.

Therefore, the data layer must preserve enough information to understand **what an image is, where it came from, what physical conditions it represents, and how it was transformed before matching**.

A visually successful overlay is not sufficient evidence of correct registration. Evaluation should use independent check points where possible, with error reported in source-image pixels before conversion to physical units when such a conversion is meaningful.

---

## What Data ChandraMap Works With

The project materials identify several lunar data sources that may be used by the system or its experiments.

| Data source                   | Intended project role                                | Status                                    |
| ----------------------------- | ---------------------------------------------------- | ----------------------------------------- |
| Chandrayaan-2 OHRC            | Main target/source imagery                           | Specified as a project data source        |
| Chandrayaan-2 TMC-2           | Main target/source imagery                           | Specified as a project data source        |
| Chandrayaan-2 IIRS            | Main target/source imagery                           | Specified as a project data source        |
| LRO NAC                       | Reference and training pairs                         | Specified as a project data source        |
| LRO WAC                       | Additional lunar-scale / illumination training       | Specified as an available data source     |
| Kaguya/SELENE TC              | Optional additional cross-sensor training            | Specified as optional                     |
| Synthetic lunar augmentations | Sun-angle, rotation, scale, and contrast experiments | Specified as a proposed/use-case category |

The presence of a source in project planning does **not** by itself establish that the corresponding dataset has already been downloaded, processed, benchmarked, or committed to the repository. Those implementation states remain dataset-specific.

---

## Confirmed Project Data Sources

### Chandrayaan-2 OHRC

OHRC is treated as a high-detail visible/panchromatic source suitable for fine terrain correspondence.

The supplied technical feedback describes OHRC at approximately `0.25–0.32 m/pixel` depending on the referenced product/document and explicitly recommends using the challenge/product metadata as the final authority for the actual pixel scale of a dataset.

**Important:** the values above describe the project documentation and should not be copied into a dataset manifest unless they are confirmed for the specific product being used.

---

### Chandrayaan-2 TMC-2

TMC-2 is described as panchromatic terrain imagery at approximately `5 m/pixel` in the supplied feedback.

It is relevant for structural lunar correspondence and terrain mapping.

The actual GSD of an individual product must come from its product metadata rather than from this README.

---

### Chandrayaan-2 IIRS

IIRS is an imaging infrared hyperspectral instrument rather than simply another grayscale camera.

The project feedback therefore recommends that IIRS data be converted into a registration-friendly two-dimensional representation before applying a conventional image-matching pipeline. Possible experimental representations mentioned by the project include selected bands, PCA/composite representations, or structural representations.

The exact IIRS representation used by the repository is **Not yet defined** unless established elsewhere in the implementation.

---

### LRO NAC

LRO NAC is identified as a lunar reference source and as a source for training/reference pairs.

The supplied feedback notes that NAC products can have substantially different spatial scales from coarse lunar sensors and recommends bringing the higher-resolution reference to a comparable effective scale when appropriate rather than treating upsampling as recovery of missing source information.

---

### LRO WAC

LRO WAC is identified in the project planning material as additional lunar-scale and illumination-related training/reference data.

Its exact role in the implemented benchmark is **Not confirmed**.

---

### Kaguya / SELENE TC

Kaguya/SELENE TC is identified as an optional additional cross-sensor training source.

Its integration into the implemented ChandraMap benchmark is **Not confirmed**.

---

## Data Lifecycle

A ChandraMap data item should be understood according to its position in the experimental lifecycle.

```text
Source / Reference Data
        │
        ▼
Data Validation
        │
        ▼
Sensor-Specific Preparation
        │
        ▼
Scale / Representation Preparation
        │
        ▼
Image Correspondence Processing
        │
        ▼
Candidate Correspondences
        │
        ▼
Geometric Verification
        │
        ▼
Verified Inliers
        │
        ▼
Optional Sub-Pixel Refinement
        │
        ▼
Final Transformation
        │
        ▼
Registered Output
        │
        ▼
Independent Evaluation
        │
        ▼
Benchmark / Stress-Test Results
```

This represents the project-level processing logic described in the supplied technical materials. The exact implementation boundaries and storage locations for each stage are not fully confirmed.

---

## Recommended Data Organization

The following is a **recommended conceptual organization**, not a claim that every directory currently exists.

```text
data/
├── README.md
│
├── raw/                 # Recommended
│   └── source imagery and original products
│
├── metadata/            # Recommended
│   └── product and acquisition metadata
│
├── reference/           # Recommended
│   └── reference imagery / reference products
│
├── benchmark/           # Recommended
│   └── benchmark-specific datasets and pair definitions
│
├── ground_truth/        # Recommended
│   └── ground-truth points, control data, and check points
│
├── processed/           # Recommended
│   └── reproducible derived/preprocessed data
│
├── correspondences/     # Recommended
│   └── candidate and verified correspondence artifacts
│
├── outputs/             # Recommended
│   └── registration and evaluation outputs
│
└── cache/               # Recommended
    └── disposable computational cache
```

### Important

Do **not** create or rely on this structure merely because it appears in this README.

Each directory should be added only when the corresponding implementation or benchmark workflow requires it.

The current repository implementation status of these individual directories is **Not confirmed** by the supplied project materials.

---

## Data Categories

### 1. Raw / Source Data

**Meaning:** Original or minimally altered source products obtained from the relevant mission/data source.

Examples may include:

- Chandrayaan-2 imagery.
- LRO reference imagery.
- Other explicitly approved lunar source products.

**Status:** Data category is scientifically required by the workflow; exact repository directory is **Not confirmed**.

Raw data should remain distinguishable from any subsequently processed representation.

---

### 2. Metadata

Metadata describes the conditions and identity of a data product.

The project specifically identifies information such as:

- Product type.
- Pixel scale / GSD.
- Footprint.
- Geolocation information when available.
- Map-projection information when available.
- Viewing geometry.
- Lighting/Sun-angle information when available.
- Sensor identity.

The feedback explicitly recommends preserving such metadata during preprocessing.

The complete ChandraMap metadata schema is **Not yet defined**.

---

### 3. Reference Data

Reference data provides the comparison side of a correspondence or registration task.

Examples explicitly discussed in project materials include LRO imagery used as reference imagery.

Reference data must not be confused with:

- Candidate matches.
- RANSAC inliers.
- Model predictions.
- Registration outputs.

---

### 4. Processed Data

Processed data is derived from source data through an explicitly documented transformation.

Potential examples include:

- Sensor-specific representations.
- Comparable-scale representations.
- Structural representations.
- Reference pyramids.
- Derived IIRS 2D representations.

Whether each of these artifacts is actually persisted by the current implementation is **Not confirmed**.

---

### 5. Correspondence Data

Correspondence artifacts represent relationships between locations in two images.

The project makes an important distinction between **candidate matches** and **verified inliers**:

```text
Candidate Correspondences
            │
            ▼
    Geometric Verification
            │
            ▼
      Verified Inliers
```

A matcher confidence score does not establish geometric correctness. RANSAC/geometric verification is responsible for determining which candidate correspondences are consistent with the estimated model.

---

### 6. Ground-Truth Data

Ground truth represents externally established information used to evaluate algorithmic results.

Ground truth must remain separate from:

- SIFT matches.
- Learned matcher outputs.
- RANSAC inliers.
- Transformation estimates.
- Registration outputs.

The exact ground-truth generation method for ChandraMap is **Not yet defined** in the supplied materials.

---

### 7. Check Points

Check points are independently evaluated points that are **not used to fit the transformation being evaluated**.

This distinction is important because evaluating a transformation on the same points used to estimate it can make registration error appear artificially low.

The project feedback recommends using challenge ground truth where available or independently checked tie points as check points.

---

### 8. Benchmark Data

Benchmark data is data organized according to a controlled evaluation protocol.

A benchmark dataset should identify, where available:

- Source image.
- Reference image.
- Sensor/product identity.
- Relevant metadata.
- Pair/region identity.
- Ground truth.
- Check-point information.
- Evaluation conditions.
- Stress-test condition when applicable.

The exact benchmark manifest/schema is governed by the benchmark documentation rather than by this README.

---

### 9. Expected / Reference Outputs

Expected outputs are reference artifacts against which generated results can be compared.

They must not be confused with:

- The algorithm's actual predictions.
- Candidate correspondences.
- RANSAC inliers.
- Runtime outputs.

The benchmark architecture separately recognizes expected/reference information, ground truth, and generated evaluation results.

The exact file format and implementation location are **Not confirmed** here.

---

### 10. Generated Outputs

Generated outputs are artifacts produced by ChandraMap processing.

Potential examples include:

- Candidate correspondence visualizations.
- Verified inlier visualizations.
- Transformation parameters.
- Residual information.
- Registered previews.
- Evaluation metrics.
- Benchmark reports.

Only artifacts explicitly required for reproducibility or project documentation should be considered for version control.

---

### 11. Cache / Temporary Data

Cache data is computationally disposable data that can be regenerated from source data and configuration.

Examples could include intermediate computational results or temporary processing artifacts.

The exact cache mechanism is **Not specified**.

Cache data should not become an undocumented dependency of an experiment.

---

## Raw Data vs Derived Data

A fundamental rule for `data/` is:

> **Never overwrite source data with derived data.**

For example:

```text
SOURCE IMAGE
    │
    ├──► SENSOR-SPECIFIC REPRESENTATION
    │
    ├──► SCALE-ADAPTED REPRESENTATION
    │
    └──► STRUCTURAL REPRESENTATION
```

Each derived representation should remain traceable to the source from which it was produced.

A derived image should never be presented as if it were the original mission product.

---

## Correspondence Data

ChandraMap correspondence processing should preserve the distinction between different stages of matching.

### Candidate Matches

Candidate matches are proposed image-to-image correspondences generated by a matching method.

They are **not automatically correct**.

For example, the project baseline describes:

```text
SIFT
  ↓
Descriptor Matching
  ↓
Ratio / Cross-Check Filtering
  ↓
Candidate Correspondences
```

The exact implementation parameters are not specified in this README.

---

### Verified Inliers

Verified inliers are candidate correspondences that survive geometric verification.

The project specifically places RANSAC/geometric verification between candidate correspondences and later refinement.

They should therefore not be stored or described as ground truth unless independently established as such.

---

### Spatial Distribution

The number of correspondences alone is not sufficient.

The project explicitly calls for measuring whether good correspondences are spatially distributed rather than concentrated around a single feature.

Possible evaluation concepts include:

- Grid coverage.
- Convex-hull coverage.
- Residual distribution.

The exact implemented coverage definition is governed by the benchmark specification and is not redefined here.

---

## Ground Truth and Check Points

The data architecture should keep three concepts separate:

| Artifact                                | Purpose                          |                  Used to fit model? | Used to evaluate model? |
| --------------------------------------- | -------------------------------- | ----------------------------------: | ----------------------: |
| Candidate correspondences               | Proposed matches                 |                         Potentially |              Diagnostic |
| Verified inliers                        | Geometrically consistent matches |    Yes, depending on pipeline stage |              Diagnostic |
| Ground truth / independent check points | External evaluation reference    | No, when used as independent checks |                     Yes |

The exact ground-truth protocol is defined outside this README.

### Evaluation Rule

Do not estimate a transformation and then report its quality using only the same points that were used for fitting.

Instead:

```text
Correspondences
      │
      ▼
Model estimation
      │
      ▼
Final transformation
      │
      ▼
Independent check points
      │
      ▼
Registration error
```

The supplied project feedback specifically recommends source-image pixel error as the primary unit for sub-pixel registration evaluation, with conversion to metres only when product GSD and projection make that conversion meaningful.

---

## Benchmark Data

The `data/` layer supports the ChandraMap benchmark system, but benchmark definitions should remain separate from raw data storage.

A benchmark should answer:

1. **What image pair is being evaluated?**
2. **What sensor/product produced each image?**
3. **What metadata is available?**
4. **What constitutes valid correspondence?**
5. **What ground truth or check points exist?**
6. **What metrics are reported?**
7. **What conditions are being tested?**
8. **What version of the benchmark definition applies?**

The benchmark system should be reproducible independently of a researcher's local cache.

---

## Reference and Expected Data

Reference data and expected outputs serve different purposes.

### Reference Data

Reference data is an input or comparison product used by the algorithm.

Example:

```text
Source Lunar Image
        +
Reference Lunar Image
        ↓
Correspondence Pipeline
```

### Expected Data

Expected data is used to establish whether the algorithm's generated result is correct or sufficiently accurate.

Example:

```text
Algorithm Output
       +
Expected / Ground-Truth Data
       ↓
Evaluation
```

These concepts should never be merged simply because both are "reference" material.

---

## Generated and Cached Data

Generated artifacts should be traceable to:

- Input data.
- Processing configuration.
- Algorithm/version.
- Benchmark version.
- Relevant experiment/stress condition.

If a generated artifact cannot be reproduced or its provenance cannot be established, it should not silently become a benchmark dependency.

### Recommended Rule

Keep generated artifacts out of source-control unless they are:

- Required for documentation.
- Required as small benchmark fixtures.
- Required as expected/reference artifacts.
- Explicitly needed for reproducibility.
- Small enough to maintain safely.

The exact repository policy for generated files is **Not yet defined**.

---

## Data Flow Through ChandraMap

The project materials support the following conceptual flow:

```text
┌───────────────────────────────┐
│ Lunar Source / Reference Data │
└───────────────┬───────────────┘
                │
                ▼
┌───────────────────────────────┐
│ Data + Metadata Validation    │
└───────────────┬───────────────┘
                │
                ▼
┌───────────────────────────────┐
│ Sensor-Specific Preparation   │
└───────────────┬───────────────┘
                │
                ▼
┌───────────────────────────────┐
│ Scale / Representation Prep   │
└───────────────┬───────────────┘
                │
                ▼
┌───────────────────────────────┐
│ Local Correspondence Matching │
└───────────────┬───────────────┘
                │
                ▼
┌───────────────────────────────┐
│ Candidate Correspondences     │
└───────────────┬───────────────┘
                │
                ▼
┌───────────────────────────────┐
│ RANSAC / Geometric Verification│
└───────────────┬───────────────┘
                │
                ▼
┌───────────────────────────────┐
│ Verified Inliers              │
└───────────────┬───────────────┘
                │
                ▼
┌───────────────────────────────┐
│ Optional Sub-Pixel Refinement │
└───────────────┬───────────────┘
                │
                ▼
┌───────────────────────────────┐
│ Final Transformation          │
└───────────────┬───────────────┘
                │
                ▼
┌───────────────────────────────┐
│ Registered Image              │
└───────────────┬───────────────┘
                │
                ▼
┌───────────────────────────────┐
│ Independent Evaluation        │
└───────────────┬───────────────┘
                │
                ▼
┌───────────────────────────────┐
│ Benchmark / Stress Results    │
└───────────────────────────────┘
```

The project feedback explicitly describes the geometry sequence as local matches → RANSAC/inliers → sub-pixel tie points → final model → registered images.

---

## Sensor-Aware Data Handling

ChandraMap should not assume that all lunar imagery can follow an identical preprocessing path.

The project feedback explicitly recommends sensor-aware preparation for OHRC, TMC-2, and IIRS.

### OHRC / TMC-2

The supplied feedback recommends preserving relevant geometry and acquisition metadata and testing representations that retain terrain structure.

The exact preprocessing implementation is **Not confirmed**.

### IIRS

IIRS requires special treatment because it is hyperspectral/infrared data.

A conventional 2D matcher should not automatically receive an entire hyperspectral cube without an explicit representation strategy.

The project recommends first determining which 2D representation preserves stable terrain structure for registration.

---

## Scale and Resolution Considerations

ChandraMap must distinguish **pixel count** from **physical information**.

Upsampling an image does not recover spatial detail that was not present in the original sensor measurement.

The project feedback therefore recommends comparing imagery at physically meaningful effective scales and using reference pyramids or downsampling of the higher-resolution side where appropriate.

### Data Rule

Do not record a resized image as if its resizing operation changed the original sensor's physical resolution.

For every derived scale representation, preserve enough provenance to determine:

- Which source product was used.
- Which scale operation was applied.
- Why the operation was applied.
- Which benchmark condition it belongs to.

Exact scale-processing metadata is **Not yet defined**.

---

## Illumination Considerations

Lunar illumination is not simply a brightness-normalization problem.

Changing Sun angle can change shadow geometry around craters and other terrain features.

The project therefore recommends testing structure-oriented representations such as:

- Edges.
- Gradients.
- Terrain structures.
- Relative geometry between nearby features.

A dedicated Sun-angle stress condition is also identified as a useful benchmark case.

### Data Integrity Rule

Do not label an image as "illumination invariant" merely because brightness or contrast was normalized.

The actual illumination condition and any transformation applied to it should remain traceable.

---

## Metadata and Provenance

Metadata is part of the scientific data record.

At minimum, when available, ChandraMap should preserve the source metadata needed to understand the product and its registration context.

The project materials specifically identify:

- Product type.
- Pixel scale / GSD.
- Footprint.
- Geolocation information.
- Map projection.
- Viewing geometry.
- Lighting geometry.

as information that may affect the processing and should be preserved where available.

### Recommended Provenance Chain

```text
Original Product
      │
      ▼
Dataset Identity
      │
      ▼
Metadata
      │
      ▼
Derived Representation
      │
      ▼
Benchmark Pair
      │
      ▼
Algorithm Output
      │
      ▼
Evaluation Result
```

A derived artifact should be traceable backward through this chain.

---

## Naming and Organization Conventions

A final repository-wide naming convention is **Not yet defined**.

Until one is formally specified, use the following as **recommended principles** rather than mandatory filenames.

### Recommended Naming Principles

- Use stable dataset identifiers.
- Avoid ambiguous names such as `image1`, `test2`, or `final_final`.
- Preserve source/product identity.
- Preserve pair identity.
- Distinguish source and reference roles.
- Distinguish raw and derived artifacts.
- Distinguish benchmark data from experimental data.
- Include stress-condition identity where applicable.
- Avoid embedding undocumented parameter values into filenames.
- Prefer metadata/manifests for detailed provenance instead of excessively long filenames.

### Example Concept

```text
<dataset-id>
    ├── source
    ├── reference
    ├── metadata
    ├── ground-truth
    └── evaluation
```

This is a conceptual convention only; exact filenames and directories are **Not yet defined**.

---

## Version Control Policy

Large scientific datasets should not automatically be placed directly into Git.

The repository should version **definitions and provenance** even when the underlying imagery is stored elsewhere.

### Recommended to Version Control

Where applicable:

- `data/README.md`
- Dataset documentation.
- Dataset manifests.
- Small metadata records.
- Benchmark pair definitions.
- Ground-truth definitions when small and appropriate.
- Expected outputs when intentionally maintained as fixtures.
- Data validation specifications.
- Provenance information.
- Dataset version identifiers.

### Dataset-Specific Decision Required

For every data artifact, ask:

> Can another researcher understand, identify, validate, and reproduce this experiment without requiring an undocumented local file?

If not, the repository should document the missing dependency.

---

## What Should Not Be Committed

Unless explicitly required by the repository's data policy, avoid committing:

- Large raw lunar imagery.
- Large downloaded mission products.
- Temporary files.
- Local caches.
- Untracked generated outputs.
- Machine-specific intermediate artifacts.
- Duplicate copies of the same source data.
- Credentials or access tokens.
- Private/local paths.
- Unverified benchmark results.

The exact `.gitignore` policy for ChandraMap is **Not confirmed by the supplied materials**.

This README therefore does not claim that any particular data directory is currently ignored by Git.

---

## Adding a New Dataset

New data should not be added merely by copying files into `data/`.

Use the following review process.

### Step 1 — Identify the Source

Record:

- Mission/instrument.
- Product identity.
- Source/provider information.
- Acquisition/product information when available.

Do not invent missing metadata.

---

### Step 2 — Determine the Dataset Role

Explicitly classify the dataset as one or more of:

- Source imagery.
- Reference imagery.
- Training data.
- Benchmark data.
- Ground truth.
- Check-point data.
- Stress-test data.
- Derived data.
- Expected/reference output.
- Generated output.

If the role is unclear, mark it **Not yet defined**.

---

### Step 3 — Preserve Metadata

Record available:

- Pixel scale/GSD.
- Product type.
- Footprint.
- Geolocation.
- Projection.
- Viewing geometry.
- Lighting/Sun geometry.
- Sensor information.

The absence of metadata should be recorded rather than silently filled with assumptions.

---

### Step 4 — Validate the Data

Check that:

- The file can be read.
- The product identity is known.
- The source/reference role is clear.
- Relevant metadata is available or explicitly missing.
- The image corresponds to the claimed lunar region.
- Any derived representation can be traced to its source.

---

### Step 5 — Define Benchmark Role

If the dataset is part of a benchmark, identify:

- Benchmark version.
- Pair identity.
- Evaluation role.
- Ground truth/check-point relationship.
- Stress condition, if any.

Do not modify benchmark data merely to improve an algorithm's result.

---

### Step 6 — Document Provenance

A new dataset should be accompanied by enough information for another researcher to understand:

```text
Where did it come from?
        ↓
What exactly is it?
        ↓
What metadata does it contain?
        ↓
What transformations were applied?
        ↓
What benchmark does it belong to?
        ↓
How is it evaluated?
```

---

## Data Validation and Quality Control

Before data participates in a benchmark or scientific experiment, validate it.

### Identity Validation

Confirm:

- Sensor/instrument identity.
- Product identity.
- Source/reference role.
- Dataset version, if available.

### Metadata Validation

Confirm that available metadata is internally consistent.

Do not infer exact physical values when authoritative product metadata is unavailable.

### Spatial Validation

Where applicable, verify:

- Image footprint.
- Expected overlap.
- Spatial correspondence.
- Geolocation information.

### Representation Validation

For derived representations, verify:

- Source provenance.
- Transformation procedure.
- Intended physical meaning.
- Compatibility with the matching pipeline.

### Benchmark Validation

Ensure that:

- Ground truth is not replaced by algorithm output.
- Check points are not accidentally used for fitting.
- Stress-test modifications are documented.
- Benchmark pairs remain stable across algorithm comparisons.

---

## Benchmark Integrity Rules

The data layer must support fair comparison between methods.

### Same Data, Same Evaluation

When comparing SIFT, learned matchers, or the full sensor-aware pipeline, use the same benchmark pairs and evaluation definitions wherever the experiment is intended as a direct comparison.

The project feedback explicitly recommends running the same image pairs through the baseline and improved pipelines.

### Do Not Tune the Ground Truth

Ground truth should never be modified to make an algorithm appear better.

### Do Not Mix Prediction and Truth

For example:

```text
SIFT Match
    ≠
Ground Truth

RANSAC Inlier
    ≠
Ground Truth

Registered Image
    ≠
Expected Registration
```

### Preserve Failed Cases

A dataset should not be silently reduced to only the image pairs on which an algorithm succeeds.

The project materials explicitly emphasize showing where the system fails and retaining difficult cases as part of credible research evaluation.

---

## Failure and Rejected Data

Failures are scientifically useful.

Examples include:

- Insufficient correspondences.
- Poor spatial coverage.
- Strong illumination differences.
- Large scale differences.
- Modality mismatch.
- Low-feature terrain.
- Geometric inconsistency.
- Registration failure.

A failed experiment should not automatically result in deleting the input pair.

Instead, preserve enough information to determine:

```text
Input
  ↓
Failure stage
  ↓
Observed reason
  ↓
Diagnostic output
  ↓
Benchmark result
```

The exact failure-record format is **Not yet defined**.

---

## Privacy, Security, and Licensing

### Privacy

The project concerns lunar remote-sensing data, so ordinary human privacy considerations are not expected to be the primary data concern.

However, repository contributors must never place:

- Personal credentials.
- API keys.
- Authentication tokens.
- Private access information.
- Private local filesystem information.

inside the `data/` directory.

---

### Security

Downloaded or externally supplied data should be treated as untrusted input until validated.

Do not execute files merely because they are present in a dataset.

The exact data-security workflow is **Not specified**.

---

### Licensing and Data Rights

The project materials identify external mission/data sources, but the repository's final dataset licensing and redistribution policy is **Not specified** in the supplied materials.

Therefore:

- Do not assume that all source imagery may be redistributed.
- Do not copy external dataset license text into this README without verification.
- Record the applicable source/provider terms for datasets actually included in the repository.
- Distinguish between code licensing and data licensing.

Before committing external imagery, confirm that redistribution is permitted under the applicable source terms.

---

## Reproducibility Requirements

A reproducible data experiment should allow another researcher to determine:

1. Which source data was used.
2. Which reference data was used.
3. Which metadata was available.
4. Which derived representations were created.
5. Which benchmark version was used.
6. Which ground truth/check points were used.
7. Which stress condition was applied.
8. Which algorithm/configuration produced the result.
9. Which evaluation procedure produced the reported metrics.

The project feedback specifically recommends starting from a small measurable image pair and preserving outputs such as match plots, rejected outliers, registered overlays, inlier statistics, and check-point error.

### Reproducibility Rule

A result should never depend silently on a developer's private local dataset.

If the required data cannot be redistributed, the repository should document how the experiment depends on that external data, subject to the applicable licensing terms.

---

## Current vs Planned Data Architecture

The following table intentionally separates established project information from architecture that still needs implementation confirmation.

| Component                          | Status                     | Notes                                                                                              |
| ---------------------------------- | -------------------------- | -------------------------------------------------------------------------------------------------- |
| Lunar source imagery               | **Specified**              | Chandrayaan-2 OHRC, TMC-2, and IIRS are identified project inputs                                  |
| Lunar reference imagery            | **Specified**              | LRO imagery is identified as reference/training data                                               |
| Additional LRO WAC data            | **Specified**              | Identified for additional lunar-scale/illumination training                                        |
| Kaguya/SELENE TC                   | **Optional / Planned**     | Identified as optional cross-sensor training                                                       |
| Synthetic lunar augmentation       | **Specified as use case**  | Sun-angle, rotation, scale, and contrast experiments are identified                                |
| Sensor-specific preparation        | **Specified / Planned**    | Recommended because sensors should not be treated identically                                      |
| Metadata preservation              | **Specified**              | Product, scale, footprint, projection, and geometry information should be preserved when available |
| Candidate correspondence data      | **Specified conceptually** | Distinct from verified inliers                                                                     |
| RANSAC-verified inliers            | **Specified conceptually** | Used for geometric verification                                                                    |
| Independent check points           | **Specified conceptually** | Required for defensible registration evaluation                                                    |
| Ground-truth storage schema        | **Not yet defined**        | Exact format/location not confirmed                                                                |
| Dataset manifest schema            | **Not yet defined**        | Exact implementation not confirmed                                                                 |
| Raw-data directory structure       | **Not confirmed**          | Do not assume a specific directory exists                                                          |
| Processed-data directory structure | **Not confirmed**          | Recommended architecture only                                                                      |
| Cache directory structure          | **Not confirmed**          | Recommended architecture only                                                                      |
| Data checksums                     | **Not specified**          | No repository-specific checksum policy confirmed                                                   |
| Data versioning mechanism          | **Not specified**          | Exact mechanism not confirmed                                                                      |
| Large-file storage system          | **Not specified**          | No specific storage backend confirmed                                                              |
| Dataset download scripts           | **Not confirmed**          | Do not assume they exist                                                                           |
| Dataset redistribution policy      | **Not specified**          | Must be established per external source                                                            |
| Complete metadata schema           | **Not yet defined**        | Should be defined before large-scale benchmarking                                                  |
| Complete benchmark dataset         | **Not confirmed**          | Project feedback recommends starting with a small measurable dataset                               |

---

## Recommended Data Readiness Checklist

Before declaring a dataset ready for ChandraMap benchmarking:

### Dataset Identity

- [ ] Source/provider identified.
- [ ] Mission/instrument identified.
- [ ] Product identity recorded.
- [ ] Source/reference role defined.
- [ ] Dataset version recorded if available.

### Metadata

- [ ] Pixel scale/GSD recorded when available.
- [ ] Footprint recorded when available.
- [ ] Geolocation recorded when available.
- [ ] Projection recorded when available.
- [ ] Viewing geometry recorded when available.
- [ ] Illumination information recorded when available.

### Processing

- [ ] Original data preserved or documented.
- [ ] Derived representations identified.
- [ ] Processing transformations documented.
- [ ] Scale changes documented.
- [ ] Sensor-specific processing documented.

### Benchmark

- [ ] Benchmark version identified.
- [ ] Pair identity defined.
- [ ] Ground truth identified.
- [ ] Independent check points identified where applicable.
- [ ] Stress condition documented where applicable.

### Quality Control

- [ ] Data can be read successfully.
- [ ] Metadata is internally consistent.
- [ ] Source/reference roles are unambiguous.
- [ ] Candidate correspondences are not confused with ground truth.
- [ ] Failed cases are retained/documented.

### Reproducibility

- [ ] Provenance is documented.
- [ ] Required external dependencies are identified.
- [ ] Generated data can be traced to source data.
- [ ] No private credentials or machine-specific paths are included.
- [ ] Licensing/redistribution status is known.

---

## Relationship to the Benchmark System

The `data/` directory provides the physical and logical data used by the benchmark system.

The benchmark documentation defines **how data is selected, evaluated, and compared**.

Conceptually:

```text
data/
  │
  ├── Source / Reference Data
  ├── Ground Truth
  ├── Check Points
  └── Benchmark Inputs
          │
          ▼
benchmarks/
  │
  ├── Benchmark Specification
  ├── Ground-Truth Protocol
  ├── Metrics
  ├── Stress Tests
  ├── Acceptance Criteria
  └── Reproducibility
          │
          ▼
    Algorithm Execution
          │
          ▼
    Evaluation Results
```

This separation prevents the benchmark definition from becoming tied to one particular local copy of the imagery.

---

## Relationship to Lunar Registration

The data layer supports the full correspondence-to-registration chain:

```text
Lunar Images
     │
     ▼
Comparable Representations
     │
     ▼
Candidate Correspondences
     │
     ▼
Geometric Verification
     │
     ▼
Verified Inliers
     │
     ▼
Sub-Pixel Tie Points
     │
     ▼
Final Transformation
     │
     ▼
Registered Image
     │
     ▼
Independent Evaluation
```

The project materials emphasize that correspondence quality, spatial distribution, registration accuracy, and measurable evaluation are more important than producing only a visually attractive mosaic.

---

## Data Design Principles

The following principles should govern future development of the `data/` layer.

### 1. Preserve the Original

Never overwrite source data with processed data.

### 2. Preserve Provenance

Every derived artifact should be traceable to its source.

### 3. Preserve Physical Meaning

Do not confuse pixel resizing with recovery of physical spatial detail.

### 4. Preserve Sensor Differences

Do not force OHRC, TMC-2, and IIRS into an identical data representation without an explicit scientific reason.

### 5. Separate Prediction from Truth

Algorithm outputs are not ground truth merely because they appear visually convincing.

### 6. Evaluate Independently

Where possible, evaluate transformations using points not used to fit them.

### 7. Preserve Difficult Cases

Failures are benchmark evidence, not disposable noise.

### 8. Version Definitions

Even when large imagery cannot be stored in Git, benchmark definitions, manifests, metadata, and provenance should be maintained where appropriate.

### 9. Do Not Invent Missing Metadata

Use `Not specified` or `Not confirmed` rather than filling gaps with assumptions.

### 10. Make Data Reproducible

A benchmark result should be explainable from its source data, metadata, configuration, processing path, and evaluation protocol.

---

## Current Status

### Implemented

The supplied project materials establish the **scientific data requirements and intended data roles**, including sensor-aware handling, reference imagery, correspondence data, geometric verification, independent evaluation, and stress-test conditions.

The exact implemented `data/` directory contents are **Not confirmed**.

### Documented

The project documentation establishes:

- Chandrayaan-2 imagery as an important source category.
- LRO imagery as reference/training data.
- Sensor-aware processing.
- Multi-scale considerations.
- Illumination considerations.
- Correspondence and registration evaluation.
- Ground-truth/check-point separation.
- Benchmark and stress-test concepts.

### Specified but Not Yet Fully Implemented

The following are part of the intended architecture but require repository-level implementation confirmation:

- Formal dataset manifests.
- Formal metadata schema.
- Complete ground-truth storage protocol.
- Complete benchmark dataset organization.
- Standardized generated-output storage.
- Complete dataset provenance mechanism.

### Planned / Recommended

A structured separation of raw, metadata, reference, benchmark, ground-truth, processed, correspondence, output, and cache data is recommended as the project grows.

These directories should not be considered implemented until they actually exist in the repository.

---

## Related Documentation

The `data/` directory should be considered together with the repository's benchmark and experiment documentation, particularly:

- `benchmarks/README.md`
- `benchmarks/v1/README.md`
- `benchmarks/v1/BENCHMARK_SPEC.md`
- `benchmarks/v1/GROUND_TRUTH_PROTOCOL.md`
- `benchmarks/v1/METRICS.md`
- `benchmarks/v1/STRESS_TESTS.md`
- `benchmarks/v1/ACCEPTANCE_CRITERIA.md`
- `benchmarks/v1/REPRODUCIBILITY.md`
- `benchmarks/baselines/README.md`
- `benchmarks/baselines/sift/README.md`
- `benchmarks/baselines/ground_truth/README.md`
- `benchmarks/baselines/expected/README.md`

These paths are the benchmark documentation structure specified in the project architecture. Their exact current contents should remain the authoritative source for benchmark-specific rules.

---

## Source Basis

This README is grounded primarily in the ChandraMap project materials supplied for this repository.

### Project Problem Statement

`SIH26166 Silarlar PS.pdf`

The project material identifies Chandrayaan-2 OHRC, TMC-2, and IIRS as target/source imagery and LRO NAC as reference/training data, with LRO WAC, Kaguya/SELENE TC, and synthetic lunar augmentations identified as additional data categories.

### Lunar Image Correspondence Technical Feedback

`Aryan_SIH_26166_Lunar_Image_Correspondence_Feedback.pptx`

The feedback establishes the importance of sensor-aware data handling, physically meaningful scale comparison, illumination-aware representations, metadata preservation, correspondence quality, and benchmark consistency across the same image pairs.

### Lunar Image Registration Feedback

`Aryan_Lunar_Image_Registration_Feedback.pdf`

The feedback establishes the distinction between candidate matches and verified inliers, the RANSAC → sub-pixel refinement → final transformation sequence, independent check-point evaluation, spatial coverage, and preservation of difficult cases.

---

## Final Rule

The `data/` directory should make it possible to answer one question for every important artifact:

> **What is this data, where did it come from, what was done to it, what role does it play in ChandraMap, and how can its use be reproduced?**

If those questions cannot yet be answered, the data should be marked accordingly rather than silently treated as validated, benchmark-ready, or ground truth.
