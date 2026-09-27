# Processed Data

`data/processed/` is the ChandraMap data layer intended for **derived data products that have passed the raw/external-data boundary and have been prepared for downstream ChandraMap processing or consumption**.

ChandraMap is a lunar image correspondence and registration system that must handle differences in sensor characteristics, spatial scale, illumination, resolution, and imaging geometry. Processed data therefore exists to provide downstream stages with scientifically meaningful representations derived from source data without replacing or modifying the original inputs.

> **Repository status:** The supplied project materials define the need for sensor-aware preprocessing, multi-scale preparation, structural representations, and downstream correspondence/registration, but they do **not** confirm the final filenames, file formats, processing scripts, schemas, or exact contents currently stored in `data/processed/`. Those details are intentionally not invented here.

---

## Overview

The conceptual ChandraMap data flow is:

```text
Raw / External Data
        │
        ▼
Validation / Preparation
        │
        ▼
Interim Processing
        │
        ▼
Processed Data
        │
        ▼
Correspondence / Registration
        │
        ▼
Evaluation / Benchmarking
```

The project feedback recommends a sensor-aware pipeline in which OHRC, TMC-2, and IIRS are not treated as identical inputs. It also recommends comparing imagery at physically meaningful effective scales before fine correspondence and preserving relevant metadata throughout the process.

The purpose of `data/processed/` is therefore to hold **reusable, traceable derived representations**, rather than raw source products, temporary processing artifacts, ground truth, or final benchmark results.

---

## What "Processed Data" Means in ChandraMap

The repository does not currently provide a formally confirmed field-level definition of the processed-data boundary.

For this directory, the recommended interpretation is:

> **Processed data is a persistent, derived representation of raw or external source data that has undergone a documented ChandraMap preprocessing or preparation step and is intended for reuse by downstream pipeline stages.**

This is intentionally different from **interim data**.

A processed artifact should represent a meaningful reusable state in the pipeline rather than an arbitrary temporary file created during execution.

### Important distinction

```text
Raw / External
      │
      ▼
Interim
      │
      ▼
Processed
      │
      ▼
Pipeline Consumption
```

The exact boundary between `data/interim/` and `data/processed/` is:

**Not formally confirmed by the supplied project data.**

The distinction described in this README should therefore be treated as the project documentation convention until the repository defines a more specific contract.

---

## Role in ChandraMap

Processed data provides the bridge between source products and correspondence/registration.

```text
                         ChandraMap
                             │
                             ▼
                 ┌─────────────────────┐
                 │ Raw / External Data │
                 └──────────┬──────────┘
                            │
                            ▼
                 ┌─────────────────────┐
                 │ Validation /        │
                 │ Preparation         │
                 └──────────┬──────────┘
                            │
                            ▼
                 ┌─────────────────────┐
                 │ Processed Data      │
                 │                     │
                 │ Sensor-aware        │
                 │ Multi-scale         │
                 │ Structural          │
                 │ Matching-ready      │
                 │ representations     │
                 └──────────┬──────────┘
                            │
                            ▼
                 ┌─────────────────────┐
                 │ Correspondence      │
                 └──────────┬──────────┘
                            │
                            ▼
                 ┌─────────────────────┐
                 │ Geometric           │
                 │ Verification        │
                 └──────────┬──────────┘
                            │
                            ▼
                 ┌─────────────────────┐
                 │ Registration        │
                 └──────────┬──────────┘
                            │
                            ▼
                 ┌─────────────────────┐
                 │ Benchmark /         │
                 │ Evaluation          │
                 └─────────────────────┘
```

The project feedback identifies sensor-aware preprocessing, multi-scale search, geometric verification, sub-pixel refinement, spatial coverage, and measurable registration accuracy as important parts of the intended pipeline.

---

## What Belongs in `data/processed/`

Only artifacts that are both:

1. **Derived from an identifiable upstream source**, and
2. **Intended to be reused by downstream ChandraMap processing**

should be considered candidates for this directory.

Depending on the final implementation, this may include processed representations such as:

- Sensor-specific prepared imagery
- Registration-oriented 2D representations
- Multi-scale representations
- Physically meaningful rescaled/downsampled reference imagery
- Structural representations
- Other persistent preprocessing outputs explicitly defined by the project

However, the repository does **not** currently confirm that every item above is implemented as an actual processed-data artifact.

### Status

| Potential artifact                        | Status                                 |
| ----------------------------------------- | -------------------------------------- |
| Sensor-aware prepared imagery             | Specified conceptually                 |
| Multi-scale representations               | Specified conceptually                 |
| Registration-oriented 2D representation   | Specified for IIRS conceptually        |
| Comparable-scale reference representation | Specified conceptually                 |
| Structural representations                | Recommended / specified experimentally |
| Feature descriptors                       | Not confirmed                          |
| Keypoints                                 | Not confirmed                          |
| Candidate correspondences                 | Not confirmed as processed data        |
| Registration outputs                      | Not processed-data by default          |
| Ground-truth points                       | Separate ground-truth component        |

---

## What Does Not Belong in `data/processed/`

### Raw source products

Original source imagery belongs to:

`data/raw/`

and should not be silently replaced by processed representations.

### External source archives

External data that has not entered the ChandraMap processing lifecycle belongs to:

`data/external/`

where that directory is defined for such data.

### Temporary/intermediate artifacts

Short-lived files created during a processing step should not automatically become processed data.

Examples include:

- Temporary tiles
- Temporary caches
- Debug images
- Intermediate arrays
- Failed processing outputs
- Scratch files
- Temporary matching results

Their exact location is governed by the repository's interim/temporary-data design.

### Ground truth

Ground-truth correspondences and independent check points are evaluation references, not ordinary processed imagery.

See:

`benchmarks/baselines/ground_truth/README.md`

### Final registration outputs

Registered images, overlays, mosaics, residual plots, and benchmark reports are downstream outputs unless the repository explicitly defines a particular artifact as a persistent processed product.

### Evaluation results

Metrics such as:

- RMSE
- Inlier ratio
- Spatial coverage
- Runtime
- Failure rate
- Recall@K

are evaluation outputs, not processed source data.

---

## Processed Data vs Raw Data

The distinction is:

| Layer             | Meaning                                                          |
| ----------------- | ---------------------------------------------------------------- |
| `data/raw/`       | Source data entering the project                                 |
| `data/processed/` | Persistent derived representations intended for downstream reuse |

Conceptually:

```text
RAW
Original source representation
        │
        │ processing
        ▼
PROCESSED
Reusable derived representation
```

Raw data should remain available independently of its processed derivatives.

Processed data should be reproducible from its upstream inputs and processing configuration whenever practical.

---

## Processed Data vs External Data

External data and processed data have different provenance roles.

```text
External Source
      │
      ▼
data/external/
      │
      │ import / preparation
      ▼
ChandraMap processing
      │
      ▼
data/processed/
```

An external lunar product does not become processed data merely because it has been downloaded.

It becomes a processed artifact only after a documented project transformation creates a reusable derived representation.

The supplied project material identifies external lunar sources including LRO NAC, LRO WAC, and optional Kaguya/SELENE TC data alongside Chandrayaan-2 sources.

The exact boundary between `data/external/` and `data/raw/` is:

**Not fully specified.**

---

## Processed Data vs Interim Data

This distinction should be based on **lifecycle and reuse**, not merely on whether a file is "finished."

### Interim data

Temporary or intermediate state used to construct another artifact.

```text
Input
  │
  ▼
Interim A
  │
  ▼
Interim B
  │
  ▼
Processed
```

### Processed data

A stable derived artifact intended to be consumed again.

```text
Processed
    │
    ├──► Correspondence
    ├──► Registration
    └──► Benchmark experiments
```

The exact lifecycle policy is:

**Not yet defined.**

---

## Processed Data vs Benchmark Data

Processed data is not automatically benchmark data.

A processed image may be reusable across multiple experiments, while a benchmark dataset has a defined evaluation role.

```text
Processed Data
      │
      ├──────────────► Development experiments
      │
      ├──────────────► Correspondence pipeline
      │
      └──────────────► Benchmark dataset construction
                              │
                              ▼
                         Evaluation
```

Benchmark membership, pair definitions, stress conditions, ground truth, metrics, and acceptance rules are governed by the benchmark documentation.

See:

`benchmarks/v1/BENCHMARK_SPEC.md`

---

## Processed Data vs Ground Truth

Ground truth is independent evaluation information.

Processed data is derived input to the system.

```text
                  ┌─────────────────┐
                  │ Processed Data  │
                  └────────┬────────┘
                           │
                           ▼
                    ChandraMap
                           │
                           ▼
                  Predicted Results
                           │
                           ▼
                    Evaluation
                           ▲
                           │
                  ┌────────┴────────┐
                  │ Ground Truth    │
                  │ Independent     │
                  └─────────────────┘
```

Ground-truth data must not be generated by simply accepting a processed prediction as correct.

The project feedback explicitly requires separation between candidate matches, verified inliers, and independent evaluation points.

---

## Processed Data vs Registration Outputs

A processed representation can prepare imagery for registration, but it is not necessarily the registration result.

The intended distinction is:

```text
Processed Representation
          │
          ▼
Correspondence
          │
          ▼
Geometric Verification
          │
          ▼
Transformation
          │
          ▼
Registered Image
```

The project feedback describes a clean geometry sequence:

```text
LOCAL MATCHES
      ↓
RANSAC INLIERS
      ↓
SUB-PIXEL TIE POINTS
      ↓
FINAL MODEL
      ↓
REGISTERED IMAGES
```

Therefore, registered images should not automatically be placed in `data/processed/`.

Their exact repository destination is:

**Not confirmed.**

---

## Sensor-Aware Processing

Sensor-aware processing is a core ChandraMap requirement.

The project materials explicitly state that OHRC, TMC-2, and IIRS should not be treated as identical images.

### OHRC

OHRC is high-detail visible panchromatic imagery.

The project feedback recommends:

- Preserving geometry metadata.
- Using the challenge product metadata as the authority for actual pixel scale.
- Testing appropriate intensity/structural representations.
- Performing fine correspondence only where the source supports it.

### TMC-2

TMC-2 is panchromatic terrain imagery at approximately the metre scale in the supplied technical context.

Its processed representation should preserve the information needed for structural correspondence and registration.

### IIRS

IIRS is hyperspectral/infrared data and should not be treated as an ordinary 2D camera image.

The project recommends initially testing a registration-friendly 2D representation such as:

- A selected band
- PCA/composite representation
- Structural representation

The exact implementation is:

**Not confirmed.**

---

## Multi-Scale Processing

Scale differences are a central ChandraMap problem.

The processed-data layer may therefore contain representations designed to make imagery comparable at physically meaningful effective scales.

The project explicitly recommends:

```text
High-resolution reference
          │
          ▼
Reference pyramid / downsampling
          │
          ▼
Comparable effective scale
          │
          ▼
Coarse correspondence
          │
          ▼
Fine refinement where justified
```

Upsampling should not be treated as recovery of missing spatial detail.

The project feedback specifically states that upsampling changes pixel count but does not recover physical spatial information.

### Important rule

Processed data must not encode an artificial claim of resolution simply because an image has been resized.

---

## Illumination-Aware Processing

Lunar illumination changes terrain appearance, particularly shadows.

The project feedback states that brightness normalization can help but cannot reconstruct a different shadow geometry.

Therefore, possible processed representations may include structural information such as:

- Gradients
- Edges
- Terrain-structure representations
- Other experimentally validated representations

However:

**The repository does not confirm a final mandatory illumination-processing algorithm.**

Such processing remains experimental unless formally specified elsewhere.

---

## What Processing Should Preserve

Processed data should preserve scientifically meaningful relationships to its upstream source.

Where available, downstream consumers should be able to determine:

- Which source image produced the artifact.
- Which sensor/product was used.
- Which processing stage produced it.
- Which scale or representation was applied.
- Which relevant metadata remains applicable.
- Which processing configuration was used.
- Which version of the processed artifact is being consumed.

The exact metadata schema is:

**Not specified.**

---

## Provenance

Every persistent processed artifact should be traceable to its inputs.

Conceptually:

```text
Processed Artifact
       │
       ├── Source identity
       ├── Source version
       ├── Processing configuration
       ├── Processing stage
       ├── Sensor/product information
       ├── Relevant metadata
       └── Artifact version
```

A processed artifact that cannot be connected to its source and processing history should not be treated as a reproducible benchmark input.

The project feedback emphasizes locking down the data definition, supplied formats, metadata, reference product, and evaluation rule before building the complete pipeline.

---

## Metadata Expectations

The exact processed-data metadata schema is not confirmed.

However, processed representations should retain or reference upstream information needed to interpret the result.

Potential metadata includes:

| Metadata                   | Status                       |
| -------------------------- | ---------------------------- |
| Source product identity    | Required concept             |
| Sensor identity            | Required concept             |
| Processing stage           | Recommended                  |
| Processing configuration   | Recommended                  |
| Source pixel scale/GSD     | Important                    |
| Processed effective scale  | Recommended                  |
| Footprint                  | Preserve when applicable     |
| Viewing geometry           | Preserve when applicable     |
| Illumination information   | Preserve when applicable     |
| Map-projection information | Preserve when applicable     |
| Source-data version        | Required for reproducibility |
| Processed-data version     | Recommended                  |

These are documentation requirements/concepts, not claims about an existing schema.

---

## Processing Workflow

The exact ChandraMap processing scripts and commands are not confirmed by the supplied project data.

The documented conceptual workflow is:

```text
1. Identify source data
        │
        ▼
2. Validate source and metadata
        │
        ▼
3. Select sensor-specific processing path
        │
        ▼
4. Prepare a scientifically meaningful representation
        │
        ▼
5. Handle effective scale differences
        │
        ▼
6. Preserve provenance
        │
        ▼
7. Validate processed artifact
        │
        ▼
8. Make artifact available to downstream stages
```

The project feedback recommends building one measurable end-to-end pair before expanding the pipeline to the whole Moon.

---

## Validation

Processed artifacts should be validated before being used in benchmark or registration experiments.

### Source validation

Verify that:

- The upstream source is known.
- The source identity is correct.
- Required metadata is available or explicitly marked unavailable.
- The source version is known.

### Processing validation

Verify that:

- The intended processing operation was actually applied.
- The artifact can be read.
- Dimensions and metadata remain internally consistent where applicable.
- Physical scale has not been misrepresented.
- Sensor identity has not been lost.

### Scientific validation

Verify that:

- Processing does not fabricate spatial detail.
- Sensor-specific information is preserved.
- The representation remains appropriate for the intended correspondence task.
- Any claimed improvement is measured rather than assumed.

The project feedback recommends actual measurements such as inlier statistics, coverage, RMSE, and runtime rather than unmeasured confidence ratings.

---

## Processed Data Integrity

Processed data is derived and therefore should be reproducible.

A useful integrity relationship is:

```text
Source Version
      +
Processing Configuration
      +
Processing Implementation
      +
Environment
      │
      ▼
Processed Artifact
```

If one of these changes materially, the processed artifact may no longer be equivalent.

The repository does not currently confirm a specific checksum or content-addressing system.

Therefore:

**Checksum policy: Not specified.**

**Recommended:** use integrity identifiers for released or benchmark-critical processed artifacts when the project defines a compatible mechanism.

---

## Versioning

Processed artifacts should be versioned whenever changes can affect scientific results.

Changes that may require a new processed-data version include:

- Source-data changes
- Processing algorithm changes
- Processing-configuration changes
- Scale changes
- Sensor-routing changes
- Metadata interpretation changes
- Bug fixes affecting generated outputs
- Representation changes

Conceptually:

```text
Source Version
      +
Processing Version
      +
Configuration Version
      │
      ▼
Processed Version
```

The exact versioning mechanism is:

**Not yet defined.**

---

## Detecting Stale Processed Data

Processed data becomes stale when its upstream dependencies change.

Examples:

```text
Source changed
      │
      ▼
Processed artifact may be stale
```

```text
Processing code changed
      │
      ▼
Processed artifact may be stale
```

```text
Configuration changed
      │
      ▼
Processed artifact may be stale
```

```text
Metadata interpretation changed
      │
      ▼
Processed artifact may be stale
```

### Recommended stale-data rule

A processed artifact should be considered **stale or requiring validation** when its recorded upstream source, processing configuration, or processing implementation no longer matches the current pipeline definition.

The repository does not currently confirm an automated stale-artifact detector.

**Automated stale detection: Not confirmed.**

---

## Reproducibility

A processed artifact should be reproducible from its upstream dependencies whenever practical.

A reproducible record should conceptually identify:

```text
Raw / External Data Version
          │
          +
Processing Configuration
          │
          +
Processing Implementation
          │
          +
Relevant Metadata
          │
          ▼
Processed Artifact
```

For benchmark experiments, this should ultimately connect to:

```text
Processed Data
      +
Benchmark Dataset
      +
Ground-Truth Version
      +
Evaluation Configuration
      │
      ▼
Benchmark Result
```

The exact implementation of this provenance chain is:

**Not confirmed.**

---

## Git and Storage Policy

The supplied project information does not establish whether processed artifacts should be committed directly to Git.

Therefore:

**Current processed-data Git policy: Not specified.**

### Recommended policy

Processed artifacts should be committed when they are:

- Small enough to maintain safely,
- Required as stable repository assets,
- Difficult or expensive to regenerate,
- Explicitly part of a released benchmark,
- Or necessary for reproducible examples.

Large derived datasets should not be committed merely because they are generated by the project.

If artifacts are excluded from Git, the repository should preserve enough information to reproduce or retrieve them.

No specific external storage system is prescribed because none is confirmed by the project.

---

## Generated Data vs Processed Data

Not every generated artifact is processed data.

A useful distinction is:

```text
Source Data
    │
    ▼
Processing
    │
    ├──► Persistent reusable representation
    │          │
    │          ▼
    │     Processed Data
    │
    └──► Temporary working artifact
               │
               ▼
           Interim / Temporary
```

Similarly:

```text
Processing
    │
    ├──► Registration result
    ├──► Evaluation result
    ├──► Visualization
    └──► Debug output
```

These should not automatically be categorized as processed data.

---

## Features and Descriptors

The supplied project architecture discusses feature extraction and matching methods including:

- SIFT
- ALIKED + LightGlue
- LoFTR
- RIFT/CFOG-style approaches

However, the repository information supplied here does **not** confirm that extracted keypoints or descriptors are stored under `data/processed/`.

Therefore:

**Feature/keypoint/descriptor storage location: Not confirmed.**

If such artifacts are later placed in `data/processed/`, their source identity, extraction configuration, and version should be recorded.

---

## Correspondence Data

Candidate correspondences are outputs of matching.

The project explicitly distinguishes:

```text
Candidate Matches
       │
       ▼
RANSAC / Geometric Verification
       │
       ▼
Verified Inliers
```

Candidate matches and verified inliers should therefore not be classified as generic processed imagery.

Their exact storage location is:

**Not confirmed.**

---

## Registration-Ready Data

The purpose of some processed representations may be to make an image suitable for registration.

A registration-ready representation can conceptually be:

```text
Source Image
     │
     ▼
Sensor-Aware Representation
     │
     ▼
Comparable Effective Scale
     │
     ▼
Registration / Matching
```

The project recommends using sensor-specific preparation and scale-aware processing before correspondence.

For IIRS, this may involve converting spectral data into a registration-friendly 2D representation.

The exact definition of "registration-ready" is:

**Not formally specified.**

---

## Benchmark Reproducibility

Processed data used by the V1 benchmark must not become an untracked moving target.

A benchmark run should be capable of identifying:

- The processed-data version.
- The upstream source version.
- The processing configuration.
- The benchmark dataset version.
- The ground-truth version.
- The evaluation configuration.

Conceptually:

```text
Raw / External Version
          │
          ▼
Processed Version
          │
          ▼
Benchmark Dataset Version
          │
          ├──────────────┐
          ▼              ▼
Ground Truth        Evaluation Config
          │              │
          └──────┬───────┘
                 ▼
            Benchmark Run
```

The exact version identifiers are:

**Not yet defined.**

---

## Sensor-Specific Processing Principles

### OHRC / TMC-2

The project feedback recommends:

- Using calibrated or standard products where possible.
- Preserving footprint, pixel scale, map projection, and viewing/lighting geometry.
- Applying only justified denoising.
- Testing local contrast normalization.
- Comparing intensity against edge/gradient/structural representations.
- Bringing reference imagery to a comparable ground scale before matching.

These are project-supported processing directions, not a claim that every operation is already implemented.

### IIRS

The project recommends:

1. Inspecting the actual supplied IIRS product.
2. Determining whether it is a full cube, individual bands, browse image, or derived product.
3. Testing sensible 2D representations.
4. Selecting representations that preserve stable terrain structure.
5. Avoiding claims of fine spatial detail unsupported by the sensor.

The final IIRS processing pipeline is:

**Not confirmed.**

---

## Geometry and Projection

Processed data must not silently erase geometric information.

The project feedback notes that:

- Some products may already be map-projected or orthorectified.
- Available mapping information should be used when appropriate.
- Raw imagery can contain sensor/viewing geometry that a simple global transformation may not fully represent.
- A flexible warp should not hide poor correspondences.

Therefore, processed-data generation should preserve relevant geometry metadata where available.

The exact coordinate and projection conventions are:

**Not specified in the supplied repository data.**

---

## Illumination and Structural Representations

A processed representation should not be considered successful merely because it looks visually similar.

The project feedback recommends testing:

```text
Raw grayscale
      │
      ├──► Gradient representation
      ├──► Edge representation
      ├──► Structural representation
      └──► Other validated representation
```

especially for different Sun-angle conditions.

The correct representation must be established experimentally.

No single mandatory representation is currently confirmed.

---

## Adding New Processed Data

Contributors should not simply copy a generated file into `data/processed/`.

Before adding a persistent processed artifact, document:

1. **Input** — Which source data produced it?
2. **Purpose** — Which downstream stage consumes it?
3. **Processing** — What transformation created it?
4. **Configuration** — Which parameters affect the result?
5. **Provenance** — Which source/version was used?
6. **Validation** — How was the artifact checked?
7. **Version** — Which processed-data version does it belong to?
8. **Reproducibility** — Can another contributor regenerate it?

If the artifact is temporary, it should remain in the appropriate interim/temporary layer instead.

---

## Regenerating Processed Data

The exact regeneration commands are:

**Not specified.**

The conceptual regeneration procedure is:

```text
Identify source version
        │
        ▼
Identify processing configuration
        │
        ▼
Run documented processing implementation
        │
        ▼
Validate generated artifact
        │
        ▼
Record provenance/version
        │
        ▼
Replace or release processed artifact
```

A future project implementation should provide an authoritative command or workflow for each reproducible processed-data artifact.

---

## Change Management

Processed-data changes should be traceable because they can affect correspondence and benchmark results.

### Changes that may affect results

- New preprocessing logic
- Changed normalization
- Changed scale handling
- Changed sensor routing
- Changed IIRS representation
- Changed source product
- Changed geometry preparation
- Changed metadata interpretation
- Bug fixes affecting generated representations

Such changes should not silently overwrite an artifact that is referenced by a published or benchmarked result.

---

## Scientific Integrity

Processed data must not be used to manufacture a desired benchmark result.

The project feedback explicitly warns against:

- treating upsampling as recovery of missing detail,
- assuming pretrained matchers are automatically lunar-invariant,
- assuming more matches are better,
- and using flexible warps to conceal weak correspondences.

The processed-data layer should therefore preserve the distinction between:

```text
Data Preparation
       ≠
Algorithmic Prediction
       ≠
Ground Truth
       ≠
Evaluation Result
```

---

## Current Implementation Status

Based on the supplied project information:

| Capability                                | Status                         |
| ----------------------------------------- | ------------------------------ |
| `data/processed/` as a derived-data layer | Documented / Intended          |
| Sensor-aware preprocessing concept        | Specified                      |
| Multi-scale preparation concept           | Specified                      |
| Structural representation concept         | Specified / Experimental       |
| IIRS 2D representation concept            | Specified / Experimental       |
| Preservation of source metadata           | Specified conceptually         |
| Provenance requirements                   | Recommended / Required concept |
| Exact processed-data schema               | Not specified                  |
| Exact processed-data filenames            | Not confirmed                  |
| Exact child directories                   | Not confirmed                  |
| Exact processing scripts                  | Not confirmed                  |
| Exact processing commands                 | Not confirmed                  |
| Feature/descriptors stored here           | Not confirmed                  |
| Correspondences stored here               | Not confirmed                  |
| Registered images stored here             | Not confirmed                  |
| Automated validation                      | Not confirmed                  |
| Automated stale-data detection            | Not confirmed                  |
| Versioning mechanism                      | Not yet defined                |
| Git storage policy                        | Not specified                  |
| External artifact storage                 | Not specified                  |

---

## Known Limitations and Gaps

### 1. Exact artifact definition

The repository does not currently confirm the complete set of artifacts belonging in `data/processed/`.

**Status:** Not specified.

### 2. Processing contract

The final required processing stages and parameters are not fully defined.

**Status:** To be defined.

### 3. Metadata schema

No authoritative processed-data metadata schema is confirmed.

**Status:** Not specified.

### 4. Versioning

The conceptual requirement for reproducibility is clear, but the exact versioning implementation is not confirmed.

**Status:** To be defined.

### 5. Storage

The repository does not establish whether processed datasets are committed, externally stored, or regenerated on demand.

**Status:** Not specified.

### 6. Validation tooling

No authoritative automated processed-data validator is confirmed.

**Status:** Not confirmed.

### 7. Stale-artifact detection

No automated dependency or stale-data system is confirmed.

**Status:** Not confirmed.

### 8. Feature storage

The final location of descriptors, keypoints, and other matching intermediates is not confirmed.

**Status:** Not specified.

---

## Recommended Processed-Data Checklist

Before accepting a processed artifact into the project:

### Provenance

- [ ] Source dataset is identified.
- [ ] Source version is identified.
- [ ] Sensor/product identity is recorded.
- [ ] Processing stage is documented.
- [ ] Processing configuration is recorded.

### Scientific validity

- [ ] The artifact does not fabricate spatial detail.
- [ ] Physical scale remains interpretable.
- [ ] Sensor-specific characteristics are preserved.
- [ ] Relevant geometry metadata is preserved.
- [ ] Illumination information is preserved when available.

### Reproducibility

- [ ] The artifact can be traced to its source.
- [ ] The processing implementation is identifiable.
- [ ] Configuration is identifiable.
- [ ] Version is identifiable.
- [ ] Regeneration is possible or the artifact is intentionally treated as a fixed release asset.

### Separation

- [ ] It is not raw source data.
- [ ] It is not temporary/interim data.
- [ ] It is not ground truth.
- [ ] It is not a benchmark result.
- [ ] It is not an evaluation report.
- [ ] It is not an unexplained cache.

---

## Data Lifecycle Summary

```text
                    EXTERNAL / RAW
                          │
                          ▼
                 Validation / Import
                          │
                          ▼
                     INTERIM
                          │
                          ▼
                    PROCESSED
                          │
             ┌────────────┼────────────┐
             ▼            ▼            ▼
       Correspondence  Registration  Benchmark
             │            │            │
             └────────────┼────────────┘
                          ▼
                      Evaluation
                          ▲
                          │
                    Ground Truth
```

The exact implementation of each data layer is controlled by the repository's corresponding documentation.

---

## Design Principles

### 1. Process, don't overwrite

Processed data should be derived from source data without destroying the source representation.

### 2. Preserve provenance

Every persistent artifact should be traceable to its upstream input.

### 3. Preserve physical meaning

Processing must not create claims of spatial detail unsupported by the source sensor.

### 4. Keep sensor paths explicit

OHRC, TMC-2, and IIRS should not be forced into identical processing paths.

### 5. Compare at meaningful scales

Scale normalization should represent comparable physical information rather than merely matching pixel dimensions.

### 6. Keep temporary data temporary

Not every generated file deserves permanent repository status.

### 7. Keep evaluation independent

Processed representations must not be confused with ground truth or benchmark outputs.

### 8. Measure processing effects

A preprocessing step should be retained because it provides a measurable benefit or a documented scientific purpose, not simply because it makes images look different.

### 9. Make stale data detectable

Changes in source data or processing logic should be capable of invalidating derived artifacts.

### 10. Do not overclaim

If an artifact, processing method, schema, or result has not been implemented or measured, document it as such.

---

## Relationship to Other Documentation

### Data documentation

- `data/README.md` — Overall data architecture
- `data/raw/README.md` — Raw source data
- `data/external/README.md` — External data
- `data/interim/README.md` — Interim data

### Benchmark documentation

- `benchmarks/README.md` — Benchmark architecture
- `benchmarks/v1/README.md` — V1 benchmark overview
- `benchmarks/v1/BENCHMARK_SPEC.md` — Benchmark contract
- `benchmarks/v1/GROUND_TRUTH_PROTOCOL.md` — Ground-truth protocol
- `benchmarks/v1/METRICS.md` — Metric definitions
- `benchmarks/v1/STRESS_TESTS.md` — Stress-test protocol
- `benchmarks/v1/ACCEPTANCE_CRITERIA.md` — Acceptance criteria
- `benchmarks/v1/REPRODUCIBILITY.md` — Reproducibility requirements

### Baseline documentation

- `benchmarks/baselines/README.md` — Baseline architecture
- `benchmarks/baselines/sift/README.md` — SIFT baseline
- `benchmarks/baselines/ground_truth/README.md` — Ground-truth component
- `benchmarks/baselines/expected/README.md` — Expected/reference artifacts

Only documentation paths confirmed by the project structure should be retained if the repository later differs from this documentation plan.

---

## Source Basis

This documentation is grounded in the supplied ChandraMap/SIH 26166 project materials.

The technical feedback identifies sensor-aware preprocessing, multi-scale search, geometric verification, sub-pixel refinement, spatial coverage, and measurable evaluation as central parts of the intended system.

The project materials specifically recommend preserving pixel scale, footprint, map-projection information, and viewing/lighting metadata where available, while keeping OHRC/TMC-2 and IIRS processing sensor-aware.

The feedback also recommends comparing imagery at physically meaningful effective scales rather than attempting to solve large resolution differences by upsampling, and recommends a simple 2D representation for IIRS before conventional matching.

The practical build order recommends first establishing one measurable source/reference pair, then adding scale and illumination handling, retrieval, stronger matchers, refinement, and additional sensors incrementally.

Where the supplied project information does not confirm an implementation detail, this README intentionally labels it as **Not specified**, **Not confirmed**, **Not yet defined**, **Planned**, or **Recommended** rather than inventing repository behavior.
