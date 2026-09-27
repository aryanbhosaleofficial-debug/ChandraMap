# Raw Data

`data/raw/` is the designated repository location for **source lunar data entering the ChandraMap data pipeline**.

ChandraMap is a lunar image correspondence and registration system for comparing imagery acquired under different sensor characteristics, spatial scales, illumination conditions, resolutions, and geometric conditions. Raw data forms the starting point from which validation, sensor-aware preparation, preprocessing, correspondence, geometric verification, registration, and evaluation are performed.

> **Repository status:** The supplied project documentation establishes the role of lunar source/reference data and identifies several relevant data sources, but it does **not** confirm the final physical contents, filenames, file formats, download scripts, or exact ingestion contract of `data/raw/`. Those details are therefore not invented here.

---

## Purpose

The purpose of `data/raw/` is to preserve the **source data as acquired or imported into ChandraMap before project-specific preprocessing or derived transformations**.

Conceptually:

```text
data/raw/
    │
    ▼
Validation / Preparation
    │
    ▼
Sensor-Aware Preprocessing
    │
    ▼
Multi-Scale Representation
    │
    ▼
Image Correspondence
    │
    ▼
Geometric Verification
    │
    ▼
Transformation Estimation
    │
    ▼
Registration
    │
    ▼
Evaluation / Benchmarking
```

The project feedback recommends first locking down the data definition, supplied formats, metadata, reference product, and evaluation rule before building the complete pipeline.

---

## What "Raw Data" Means in ChandraMap

The repository does not currently provide a formally confirmed definition of the exact ingestion boundary for the term **raw data**.

Therefore, for this directory, the following interpretation is recommended:

> **Raw data is source imagery and associated source metadata imported into ChandraMap before ChandraMap-specific preprocessing, normalization, matching, registration, or benchmark-result generation.**

This definition does **not** necessarily mean that every file is an untouched spacecraft telemetry-level product.

For example, the project feedback explicitly raises an unresolved question about whether the expected inputs are raw products, calibrated products, map-projected products, or another supplied product level.

Therefore:

| Data state                                                    | `data/raw/` status                                                          |
| ------------------------------------------------------------- | --------------------------------------------------------------------------- |
| Original/source lunar imagery imported for ChandraMap         | Intended role                                                               |
| Source-associated metadata                                    | Intended role                                                               |
| Official supplied scientific products used as pipeline inputs | Potentially appropriate                                                     |
| Calibrated products                                           | **Not confirmed** as the required ingestion level                           |
| Map-projected/orthorectified products                         | **Not confirmed** as the required ingestion level                           |
| ChandraMap-normalized imagery                                 | Does not belong in raw data                                                 |
| Rescaled/downsampled imagery                                  | Does not belong in raw data unless explicitly defined as the source product |
| Feature descriptors                                           | Does not belong in raw data                                                 |
| Candidate matches                                             | Does not belong in raw data                                                 |
| RANSAC inliers                                                | Does not belong in raw data                                                 |
| Ground-truth points                                           | Does not belong in raw data                                                 |
| Registered images                                             | Does not belong in raw data                                                 |
| Benchmark results                                             | Does not belong in raw data                                                 |
| Synthetic augmentations                                       | Generated data, not raw source data                                         |

---

## Role in ChandraMap

Raw data is the starting point of the scientific processing chain.

```text
                         ChandraMap
                             │
                             ▼
                       ┌───────────┐
                       │ Raw Data  │
                       └─────┬─────┘
                             │
                             ▼
                  ┌────────────────────┐
                  │ Validation /       │
                  │ Metadata Inspection│
                  └─────────┬──────────┘
                            │
                            ▼
                  ┌────────────────────┐
                  │ Sensor-Aware       │
                  │ Preparation        │
                  └─────────┬──────────┘
                            │
                            ▼
                  ┌────────────────────┐
                  │ Multi-Scale /      │
                  │ Structural Prep    │
                  └─────────┬──────────┘
                            │
                            ▼
                  ┌────────────────────┐
                  │ Correspondence     │
                  └─────────┬──────────┘
                            │
                            ▼
                  ┌────────────────────┐
                  │ Geometric          │
                  │ Verification       │
                  └─────────┬──────────┘
                            │
                            ▼
                  ┌────────────────────┐
                  │ Registration       │
                  └─────────┬──────────┘
                            │
                            ▼
                  ┌────────────────────┐
                  │ Benchmark /        │
                  │ Evaluation         │
                  └────────────────────┘
```

The project architecture specifically emphasizes:

- sensor-aware processing,
- multi-scale search,
- reliable correspondence,
- geometric verification,
- sub-pixel refinement,
- spatial coverage,
- measurable registration error,
- and independent evaluation.

Raw data therefore must remain sufficiently traceable that downstream results can be related back to the original input.

---

## Relationship to the Data Pipeline

The conceptual relationship is:

```text
Raw Source Data
       │
       ▼
Input Validation
       │
       ▼
Metadata / Product Inspection
       │
       ▼
Sensor Routing
       │
       ▼
Preprocessing
       │
       ▼
Multi-Scale Search / Representation
       │
       ▼
Candidate Correspondences
       │
       ▼
RANSAC / Geometric Verification
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
Registered Output
       │
       ▼
Benchmark Metrics
```

The recommended implementation flow separates candidate matches, RANSAC verification, sub-pixel refinement, and final transformation estimation.

---

## Data Sources

The supplied SIH project material identifies the following lunar data sources and intended roles:

| Source                        | Documented project role                       | `data/raw/` interpretation          |
| ----------------------------- | --------------------------------------------- | ----------------------------------- |
| Chandrayaan-2 OHRC            | Main target/source imagery                    | Source data when imported           |
| Chandrayaan-2 TMC-2           | Main target/source imagery                    | Source data when imported           |
| Chandrayaan-2 IIRS            | Main target/source imagery                    | Source data when imported           |
| LRO NAC                       | Reference and training pairs                  | Reference/source data when imported |
| LRO WAC                       | Additional lunar-scale/illumination training  | Source/reference data when imported |
| Kaguya/SELENE TC              | Optional additional cross-sensor training     | Optional source data                |
| Synthetic lunar augmentations | Sun-angle, rotation, scale, contrast training | **Generated data, not raw data**    |

These roles are documented in the supplied SIH problem material.

The presence of a source in project documentation does **not** mean that the corresponding data has already been downloaded or committed to `data/raw/`.

Actual repository availability is:

**Not confirmed.**

---

## Sensor-Aware Raw Data

Raw data must preserve sensor identity.

OHRC, TMC-2, and IIRS are not interchangeable inputs.

The supplied technical feedback describes:

- **OHRC** as high-detail visible panchromatic imagery.
- **TMC-2** as panchromatic terrain imagery at approximately the metre scale.
- **IIRS** as hyperspectral/infrared data requiring sensor-specific handling.
- **LRO NAC** as a higher-resolution lunar reference source whose effective scale may need adjustment when paired with coarser imagery.

This affects how raw data is interpreted downstream.

### OHRC

OHRC is intended for fine terrain correspondence and registration.

The project feedback notes that official documentation can report different OHRC scales depending on product/documentation and therefore recommends using the **challenge product metadata as the final authority for the actual pixel scale**.

### TMC-2

TMC-2 provides panchromatic terrain imagery at approximately the metre scale and can serve as a structural bridge between detailed and coarser lunar data.

### IIRS

IIRS should not be treated as an ordinary single-band camera image.

The feedback describes it as hyperspectral/infrared data and recommends initially testing a registration-friendly 2D representation such as:

- a selected band,
- PCA/composite representation,
- or structural representation.

The exact IIRS input product supplied to the repository is:

**Not confirmed.**

---

## Raw Data vs Preprocessed Data

The raw-data boundary must remain clear.

### Raw data

```text
Source imagery
       +
Source metadata
       +
Provenance
```

### Preprocessed data

Examples of operations discussed by the project include:

- sensor-specific representation,
- local contrast normalization,
- edge/gradient/structural representations,
- scale adjustment,
- reference pyramids,
- downsampling,
- other matching-oriented preparation.

These are downstream processing concepts and should not silently overwrite raw inputs.

The feedback recommends that preprocessing make different sensors comparable without pretending that missing spatial detail exists.

---

## Raw Data Must Be Preserved

Raw source data should be treated as an immutable input wherever practical.

The intended relationship is:

```text
                 ┌────────────────┐
                 │   Raw Source   │
                 │      Data      │
                 └───────┬────────┘
                         │
              ┌──────────┴──────────┐
              │                     │
              ▼                     ▼
        Validation              Processing
              │                     │
              │                     ▼
              │              Derived Data
              │
              └─────────────────────┘
```

Processing should create derived outputs rather than modifying the original source representation in place.

If the project later defines a formal immutable-data or content-addressed policy, that policy should supersede this general recommendation.

---

## What Does Not Belong in `data/raw/`

The following should not be stored as raw data merely because they originated from a raw image.

### Preprocessed imagery

Examples:

- normalized images,
- resized images,
- downsampled images,
- image pyramids,
- edge maps,
- gradient maps,
- structural representations.

### Matching outputs

Examples:

- keypoints,
- descriptors,
- candidate correspondences,
- matcher confidence,
- accepted matches.

### Geometric outputs

Examples:

- RANSAC inliers,
- estimated affine transforms,
- estimated homographies,
- residual vectors.

### Registration outputs

Examples:

- registered images,
- warped images,
- mosaics,
- final overlays.

### Ground truth

Ground-truth correspondence points and independent check points belong to the benchmark/ground-truth architecture rather than the raw-data layer.

### Evaluation outputs

Examples:

- RMSE,
- inlier ratio,
- coverage,
- runtime,
- failure rate,
- Recall@K,
- benchmark tables.

The project explicitly distinguishes candidate matches from verified inliers and requires independent check-point evaluation rather than treating algorithm outputs as ground truth.

---

## Raw Data and Ground Truth

Raw data and ground truth serve different purposes.

```text
Raw Data
   │
   │ input to evaluated system
   ▼
ChandraMap Pipeline
   │
   ▼
Predicted Correspondences
   │
   ▼
Registration
   │
   ▼
Evaluation ◄──────── Independent Ground Truth
```

Ground truth must remain independent of the predictions being evaluated.

Candidate matches generated by SIFT or another correspondence method are **not** automatically ground truth.

Likewise, RANSAC inliers are algorithm-derived results rather than independent ground truth.

The project feedback specifically recommends using challenge ground truth when available, or independently checked tie points when it is not, and keeping evaluation check points separate from transformation-fitting points.

For the detailed ground-truth component documentation, see:

`benchmarks/baselines/ground_truth/README.md`

For the benchmark-wide protocol, see:

`benchmarks/v1/GROUND_TRUTH_PROTOCOL.md`

---

## Raw Data and Benchmark Datasets

Raw data is an **input source**, while benchmark data is an **evaluation-defined subset or organization of data**.

Conceptually:

```text
                 Source Data
                     │
                     ▼
                  data/raw/
                     │
          ┌──────────┴──────────┐
          │                     │
          ▼                     ▼
   Development Data      Benchmark Dataset
                                │
                                ▼
                         Ground Truth
                                │
                                ▼
                         Evaluation
```

Not every raw source product necessarily becomes a benchmark test case.

Benchmark inclusion, pair construction, stress categories, ground-truth assignment, and evaluation rules are governed by the benchmark documentation.

The raw-data directory should therefore not be treated as the benchmark definition.

---

## Data Provenance

Every raw dataset used in a reproducible ChandraMap experiment should be traceable to its source.

Where the information is available, provenance should identify:

- Source dataset/product
- Instrument/sensor
- Product identity
- Acquisition information
- Relevant source metadata
- Import/acquisition context
- Processing state
- Ground scale/GSD
- Footprint
- Viewing/illumination information
- Map-projection information
- Associated documentation
- Raw-data version or release identity

The exact metadata schema is:

**Not yet defined.**

The project feedback specifically recommends preserving pixel size, footprint, lighting/viewing metadata, and relevant map-projection information when available.

---

## Metadata Expectations

Metadata is part of the scientific input, not merely an optional convenience.

At minimum, ChandraMap should preserve available information needed to interpret the image correctly.

Potential metadata categories include:

| Metadata category              | Status                       |
| ------------------------------ | ---------------------------- |
| Sensor/product identity        | Required concept             |
| Image dimensions               | Required when available      |
| Pixel scale/GSD                | Required when available      |
| Footprint                      | Recommended / when available |
| Lighting/Sun-angle information | Recommended / when available |
| Viewing geometry               | Recommended / when available |
| Map projection                 | Recommended / when available |
| Geolocation information        | Recommended / when available |
| Product processing state       | Recommended                  |
| Source provenance              | Required concept             |

The exact field names and metadata schema are **Not specified**.

Do not create a project-wide metadata schema in this README unless it is formally defined elsewhere.

---

## Pixel Scale and Physical Resolution

Raw imagery must retain its physical scale information when supplied.

The project explicitly warns against treating pixel count as equivalent to spatial information.

For example:

> Upsampling a coarse image creates more pixels but does not recover missing spatial detail.

The recommended approach for large resolution differences is to compare imagery at physically meaningful effective scales, such as through reference pyramids or downsampling of the higher-resolution side.

This is especially important for IIRS and high-resolution reference imagery.

Do not use `data/raw/` metadata to imply accuracy that the source sensor cannot support.

---

## Illumination Metadata

Sun angle can materially change lunar terrain appearance.

The project feedback notes that changing Sun geometry changes shadows and that brightness normalization alone cannot reconstruct the same terrain appearance under a different illumination geometry.

Where illumination information is available, it should therefore remain associated with the raw source.

This allows downstream experiments to distinguish:

```text
Similar illumination
        vs.
Different illumination
```

rather than treating all appearance differences as ordinary brightness changes.

---

## Acquisition / Import Workflow

The exact production acquisition commands and scripts are:

**Not specified.**

The documented conceptual workflow is:

```text
1. Identify source product
          │
          ▼
2. Acquire / import source data
          │
          ▼
3. Preserve source metadata
          │
          ▼
4. Validate integrity and product identity
          │
          ▼
5. Record provenance
          │
          ▼
6. Place source data in raw-data storage
          │
          ▼
7. Create derived/preprocessed representations separately
```

The supplied project feedback recommends beginning with a small real dataset and first proving one measurable source/reference pair before scaling to a larger lunar dataset.

---

## Data Validation

Raw data should be validated before it is used for correspondence or registration.

### Recommended validation checks

- Source product identity is known.
- Sensor identity is known.
- Image can be read successfully.
- Metadata is associated with the correct product.
- Image dimensions are consistent with metadata where such metadata exists.
- Pixel scale/GSD is recorded when available.
- Footprint/geolocation information is preserved when available.
- Viewing/illumination metadata is preserved when available.
- No accidental preprocessing has replaced the source representation.
- Provenance is recorded.
- The data can be traced to its source.

The exact validation implementation is:

**Not confirmed.**

No specific validation command or checksum mechanism is asserted.

---

## Integrity

Raw data should be protected against accidental modification.

Recommended integrity practices include:

- Keep raw source data separate from generated outputs.
- Do not overwrite source files during preprocessing.
- Record a stable dataset/version identifier.
- Record checksums when the repository's data-management policy supports them.
- Validate source files before benchmark execution.
- Preserve provenance alongside the data.

Checksum usage in ChandraMap is:

**Not specified.**

Therefore, checksums are a **Recommended** practice rather than an existing project requirement.

---

## Versioning

Raw-data versioning must allow a benchmark run to identify which source data was used.

A conceptual version record is:

```text
Raw Dataset Version
       │
       ├── Source products
       ├── Metadata
       ├── Provenance
       ├── Validation state
       └── Change history
```

The exact raw-data versioning mechanism is:

**Not yet defined.**

At minimum, benchmark results should not ambiguously refer to a mutable dataset whose contents may have changed.

If a raw source product is replaced, corrected, reprocessed, or otherwise changed in a way that can affect evaluation, the change should be traceable.

---

## Naming Conventions

The repository does not currently confirm a final naming convention for files stored under `data/raw/`.

Therefore, this README does not prescribe a fabricated filename pattern.

### Recommended naming principles

When the project establishes a convention, filenames should avoid ambiguous names such as:

```text
image1
image2
new_image
final_image
test
test2
```

Instead, names should preferably encode stable source identity where supported by the source product, such as:

```text
<source-product-identity>
```

or an equivalent project-defined naming scheme.

The exact convention is:

**To be defined.**

Do not rename official source products merely to satisfy a local convention if doing so destroys provenance or makes the original product identity harder to recover.

---

## Directory Organization

The final contents of `data/raw/` are not confirmed by the supplied repository information.

Therefore, the following should **not** be interpreted as the current repository structure:

```text
data/
└── raw/
    ├── ohrc/
    ├── tmc2/
    ├── iirs/
    ├── lro_nac/
    ├── lro_wac/
    └── ...
```

This is only a possible organizational concept.

If the repository later adopts sensor-specific subdirectories, the structure should be documented here after implementation.

### Current status

**Exact `data/raw/` child directories: Not confirmed.**

---

## Storage and Git Policy

The supplied project documentation does not confirm whether the repository currently stores complete lunar source datasets directly in Git.

Therefore:

**Current Git storage policy: Not specified.**

### Recommended policy

Large scientific source datasets should generally be kept separate from ordinary source-code history when repository size, licensing, or distribution constraints make direct Git storage inappropriate.

Regardless of the storage mechanism:

- Source provenance must remain traceable.
- Dataset identity must remain reproducible.
- Evaluation inputs must be versioned.
- The repository should not contain undocumented binary data.
- Large files should not be silently committed merely because they can technically be added to Git.
- Any external storage mechanism must be documented before becoming part of the reproducibility contract.

No specific Git LFS service, cloud provider, object store, database, or data platform is prescribed here because none is confirmed by the supplied project information.

---

## `.gitignore` Considerations

The exact project `.gitignore` policy is not confirmed.

Raw data should **not** automatically be ignored without considering reproducibility.

There is an important distinction:

```text
Source code
     │
     ├── usually tracked
     │
     ▼
Metadata / manifests
     │
     ├── generally should be tracked when appropriate
     │
     ▼
Large scientific data
     │
     ├── storage policy dependent
     │
     ▼
Generated outputs
     │
     └── usually reproducible and therefore potentially excluded
```

If raw data is intentionally excluded from Git, the repository should still document:

- what dataset is required,
- where it comes from,
- which version is required,
- how it is identified,
- and how its integrity is verified.

The exact download/retrieval procedure is **Not specified**.

---

## Reproducibility

A reproducible ChandraMap run should be able to answer:

1. Which raw source data was used?
2. Which sensor/product generated it?
3. Which raw-data version was used?
4. Which metadata accompanied it?
5. Which preprocessing path was applied?
6. Which benchmark dataset was derived from it?
7. Which ground-truth version was used?
8. Which configuration produced the evaluation result?

Conceptually:

```text
Raw Data Version
       +
Metadata
       +
Preprocessing Configuration
       +
Benchmark Dataset Version
       +
Ground-Truth Version
       +
Evaluation Configuration
       │
       ▼
Reproducible Benchmark Run
```

The exact implementation of this provenance chain is:

**Not yet confirmed.**

---

## Raw Data and Preprocessing

Preprocessing must not erase the distinction between the original source and a derived representation.

The supplied project feedback recommends sensor-specific preparation:

```text
OHRC / TMC-2
      │
      ├── Calibration / standard product handling
      ├── Geometry metadata preservation
      ├── Light denoising
      ├── Contrast / structural representations
      └── Comparable-scale preparation

IIRS
      │
      ├── Inspect supplied product
      ├── Select suitable representation
      ├── Band / PCA / composite experiment
      └── Structural representation
```

The project specifically recommends keeping OHRC/TMC-2 and IIRS paths sensor-aware rather than forcing all three sensors through one identical processing route.

The exact preprocessing implementation belongs elsewhere in the repository and is not defined by this README.

---

## Raw Data and Multi-Scale Processing

Scale handling is a core part of ChandraMap.

The raw dataset should preserve the original physical scale rather than storing only resized copies.

The project feedback recommends:

1. Preserve the source resolution.
2. Compare images at physically meaningful effective scales.
3. Use a reference pyramid or downsample the higher-resolution side when appropriate.
4. Perform fine refinement only where the source contains sufficient spatial information.

This prevents preprocessing from creating the false appearance of information recovery.

---

## Raw Data and Illumination Handling

Raw images should retain their original appearance whenever practical because illumination variation is itself part of the problem.

The benchmark includes a conceptual **Sun-angle stress** condition intended to measure robustness to changed shadows.

Therefore, raw data should not be replaced by a brightness-normalized representation before the original source is preserved.

---

## Raw Data and Registration

Raw data ultimately supports registration between images of the same lunar region.

The project emphasizes that the primary technical objective is reliable correspondence and registration rather than merely producing a visually attractive mosaic.

A typical path is:

```text
Raw Source
   │
   ▼
Prepared Source
   │
   ▼
Local Matches
   │
   ▼
RANSAC Inliers
   │
   ▼
Sub-Pixel Tie Points
   │
   ▼
Final Model
   │
   ▼
Registered Images
```

The final registered product is downstream of the raw-data layer.

---

## Raw Data and Evaluation

Raw data is not itself a benchmark result.

Evaluation is performed using derived correspondence and registration outputs against independent ground truth.

Relevant measurements identified by the project include:

- Inlier count
- Inlier ratio
- Spatial coverage
- Check-point RMSE in source-image pixels
- Ground error when scientifically meaningful
- Runtime
- Failure rate
- Recall@1 / Recall@5 when global retrieval is used

No benchmark performance values are reported in this README.

---

## Security and Data Safety

Raw scientific data should be handled as externally sourced data.

Before importing data into the project:

- Verify the source.
- Preserve provenance.
- Avoid executing unknown files obtained with datasets.
- Keep source data separate from executable code.
- Do not store credentials or API keys inside raw-data directories.
- Do not place personal or sensitive information in metadata without an explicit project need.
- Do not modify official source products without preserving their original identity.

No project-specific credential system, cloud-storage system, or security infrastructure is confirmed.

---

## Licensing and Data-Use Considerations

The supplied project materials identify several external scientific data sources, but this README does not establish the license or redistribution terms for each dataset.

Therefore:

**Dataset-specific licenses: Not specified.**

Before redistributing raw scientific products, verify the terms applicable to the particular source and product.

This is especially important when raw data originates from external mission archives or agencies.

The repository should not claim that all source imagery is freely redistributable unless the applicable source terms have been verified.

---

## Synthetic Data

Synthetic lunar augmentations are mentioned in the SIH project material for:

- Sun-angle variation
- Rotation
- Scale
- Contrast

These are **generated data**, not raw source data.

They should therefore remain conceptually separate:

```text
Raw Lunar Data
      │
      ▼
Synthetic Augmentation
      │
      ▼
Generated Training / Experiment Data
```

The exact augmentation implementation and storage location are:

**Not specified.**

---

## Data Acquisition Status

The project feedback recommends starting with a small real dataset, including overlapping regions from relevant lunar data sources, before attempting to build the entire system.

However, the supplied materials do not confirm that a particular raw-data collection has already been downloaded into this directory.

Therefore:

| Item                                      | Status        |
| ----------------------------------------- | ------------- |
| Lunar source data requirements identified | Documented    |
| Chandrayaan-2 source families identified  | Documented    |
| LRO reference data identified             | Documented    |
| Optional Kaguya/SELENE data identified    | Documented    |
| Synthetic augmentation concept identified | Documented    |
| Exact current `data/raw/` contents        | Not confirmed |
| Exact dataset size                        | Not specified |
| Exact filenames                           | Not specified |
| Exact acquisition scripts                 | Not specified |
| Exact raw-data version                    | Not confirmed |

---

## Current Implementation Status

This README distinguishes the documented architecture from implementation details that have not been confirmed.

| Capability                         | Status                                        |
| ---------------------------------- | --------------------------------------------- |
| `data/raw/` as source-data layer   | Documented / Intended                         |
| Lunar source data categories       | Documented                                    |
| Sensor-aware data handling         | Specified                                     |
| Preservation of source metadata    | Recommended / Specified conceptually          |
| Provenance tracking                | Required concept                              |
| Raw-data versioning                | Required concept; exact mechanism not defined |
| Automated raw-data validation      | Not confirmed                                 |
| Exact raw-data schema              | Not specified                                 |
| Exact raw-data file formats        | Not specified                                 |
| Exact raw-data filenames           | Not specified                                 |
| Exact child directories            | Not confirmed                                 |
| Acquisition/download scripts       | Not confirmed                                 |
| Checksums                          | Recommended; implementation not confirmed     |
| Git/LFS/external storage mechanism | Not specified                                 |
| Dataset-specific licensing records | Not confirmed                                 |

---

## Known Gaps

The following questions remain open unless answered by later repository documentation:

### Product level

- Are raw spacecraft products expected?
- Are calibrated products expected?
- Are map-projected products expected?
- Are orthorectified products expected?

The supplied feedback explicitly identifies this as a question that must be answered before implementation is finalized.

### Metadata

- Which metadata fields are mandatory?
- What is the authoritative metadata schema?
- How are missing metadata values represented?

**Status:** To be defined.

### Storage

- Are large source datasets committed to Git?
- Are they stored externally?
- Is Git LFS used?
- What is the official data distribution mechanism?

**Status:** Not specified.

### Versioning

- What is the authoritative raw-data version identifier?
- How are dataset revisions recorded?
- How are benchmark runs linked to raw-data versions?

**Status:** To be defined.

### Validation

- Which automated integrity checks are required?
- Are checksums mandatory?
- Which metadata consistency checks are automated?

**Status:** Not confirmed.

### Licensing

- Which license applies to each external data source?
- Which products may be redistributed through the repository?

**Status:** Not specified.

---

## Recommended Data Lifecycle

The recommended lifecycle is:

```text
                    ┌──────────────────┐
                    │ External Source  │
                    │ Lunar Data       │
                    └────────┬─────────┘
                             │
                             ▼
                    ┌──────────────────┐
                    │ Acquisition /    │
                    │ Import           │
                    └────────┬─────────┘
                             │
                             ▼
                    ┌──────────────────┐
                    │ Provenance +     │
                    │ Metadata         │
                    └────────┬─────────┘
                             │
                             ▼
                    ┌──────────────────┐
                    │ Raw Data         │
                    │ data/raw/        │
                    └────────┬─────────┘
                             │
                             ▼
                    ┌──────────────────┐
                    │ Validation       │
                    └────────┬─────────┘
                             │
                             ▼
                    ┌──────────────────┐
                    │ Preprocessing    │
                    └────────┬─────────┘
                             │
                             ▼
                    ┌──────────────────┐
                    │ Derived Data     │
                    └────────┬─────────┘
                             │
                             ▼
                    ┌──────────────────┐
                    │ Benchmark /      │
                    │ Registration     │
                    └──────────────────┘
```

The raw layer should remain stable while downstream representations evolve.

---

## Raw Data Release Checklist

Before treating a raw-data collection as a reproducible ChandraMap input, verify:

### Source identity

- [ ] Source product is identified.
- [ ] Sensor/instrument identity is recorded.
- [ ] Source provenance is recorded.
- [ ] Product processing state is known or explicitly marked unknown.

### Metadata

- [ ] Pixel scale/GSD is preserved when available.
- [ ] Image dimensions are preserved when available.
- [ ] Footprint is preserved when available.
- [ ] Viewing geometry is preserved when available.
- [ ] Illumination information is preserved when available.
- [ ] Map-projection information is preserved when available.

### Integrity

- [ ] Source files can be read.
- [ ] Source files have not been unintentionally modified.
- [ ] Data identity can be reproduced.
- [ ] Integrity verification is performed where the project defines such a mechanism.

### Reproducibility

- [ ] Raw-data version is identifiable.
- [ ] Dataset provenance is documented.
- [ ] Benchmark runs can reference the raw-data version.
- [ ] Any external acquisition requirements are documented.

### Separation

- [ ] Preprocessed data is not confused with raw data.
- [ ] Candidate matches are not stored as raw data.
- [ ] RANSAC outputs are not stored as raw data.
- [ ] Registered outputs are not stored as raw data.
- [ ] Ground truth remains separate.
- [ ] Benchmark results remain separate.

---

## Design Principles

The `data/raw/` layer follows these principles:

### 1. Preserve the source

Do not destroy the original input while experimenting with preprocessing.

### 2. Preserve provenance

A researcher should be able to determine where an input came from.

### 3. Preserve physical meaning

Pixel scale, sensor identity, geometry, and illumination information should not be discarded when available.

### 4. Separate raw and derived data

Preprocessed images and model outputs should not silently replace source imagery.

### 5. Do not fabricate metadata

Unknown information must remain unknown.

### 6. Do not fabricate resolution

Upsampling does not recover spatial detail.

### 7. Keep sensors distinct

OHRC, TMC-2, and IIRS require sensor-aware handling.

### 8. Keep evaluation independent

Raw data, predictions, and ground truth must remain logically distinct.

### 9. Make changes traceable

Raw-data changes that affect scientific results must be versioned or otherwise documented.

### 10. Measure rather than overclaim

The project emphasizes actual RMSE, inlier ratio, coverage, runtime, and failure behavior rather than unsupported confidence ratings.

---

## Relationship to Other Data Layers

The conceptual data separation is:

```text
data/
│
├── raw/
│   └── Source lunar data
│
├── <preprocessed / derived layers>
│   └── Project-generated representations
│
├── samples/
│   └── Benchmark/sample organization
│
└── ground_truth/
    └── Independent evaluation information
```

The exact complete `data/` tree is governed by the repository's current structure and should not be inferred from this README.

The important distinction is:

```text
Raw
  ≠
Preprocessed
  ≠
Benchmark
  ≠
Ground Truth
  ≠
Expected Results
  ≠
Evaluation Outputs
```

---

## Related Documentation

- `data/README.md` — Data-layer overview
- `benchmarks/README.md` — Overall benchmark architecture
- `benchmarks/v1/README.md` — V1 benchmark overview
- `benchmarks/v1/BENCHMARK_SPEC.md` — V1 benchmark specification
- `benchmarks/v1/GROUND_TRUTH_PROTOCOL.md` — Ground-truth protocol
- `benchmarks/v1/METRICS.md` — Benchmark metric definitions
- `benchmarks/v1/STRESS_TESTS.md` — Stress-test definitions
- `benchmarks/v1/ACCEPTANCE_CRITERIA.md` — Benchmark acceptance criteria
- `benchmarks/v1/REPRODUCIBILITY.md` — Reproducibility requirements
- `benchmarks/baselines/README.md` — Baseline architecture
- `benchmarks/baselines/ground_truth/README.md` — Ground-truth component

---

## Source Basis

This documentation is based on the supplied ChandraMap/SIH 26166 project materials.

The SIH problem material identifies Chandrayaan-2 OHRC, TMC-2, and IIRS as primary target/source imagery and identifies LRO NAC, LRO WAC, Kaguya/SELENE TC, and synthetic lunar augmentations as additional data sources or training resources with different roles.

The technical feedback establishes the importance of preserving sensor-specific characteristics, pixel scale, footprint, lighting/viewing metadata, and map-projection information where available.

The registration feedback also establishes that preprocessing should make data comparable without pretending that missing spatial detail exists, that large resolution differences should be handled through physically meaningful scales, and that IIRS requires a sensor-specific representation rather than being treated as an ordinary 2D camera image.

Where the supplied project information does not confirm an implementation detail, this README intentionally marks it as **Not specified**, **Not confirmed**, **To be defined**, **Planned**, or **Recommended** rather than inventing repository behavior.
