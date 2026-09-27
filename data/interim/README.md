# `data/interim/`

`data/interim/` is intended to hold **intermediate data representations produced while preparing ChandraMap inputs for correspondence, geometric verification, registration, and evaluation**.

However, the currently available ChandraMap project materials **do not formally define the exact contents, file formats, naming scheme, or processing artifacts of `data/interim/`**.

Therefore, this document deliberately separates:

- what is supported by the project materials,
- what is specified conceptually,
- and what is recommended for the repository architecture.

> **Important:** This README does not claim that any specific interim artifact, script, format, or processing implementation already exists unless it is confirmed by the repository.

---

## Table of Contents

- [Purpose](#purpose)
- [Current Status](#current-status)
- [What Interim Data Means in ChandraMap](#what-interim-data-means-in-chandramap)
- [What Belongs in `data/interim/`](#what-belongs-in-datainterim)
- [What Does Not Belong in `data/interim/`](#what-does-not-belong-in-datainterim)
- [Data Lifecycle](#data-lifecycle)
- [Interim Data in the ChandraMap Pipeline](#interim-data-in-the-chandramap-pipeline)
- [Examples of Potential Interim Artifacts](#examples-of-potential-interim-artifacts)
- [Raw vs External vs Interim vs Processed Data](#raw-vs-external-vs-interim-vs-processed-data)
- [Interim Data vs Correspondences](#interim-data-vs-correspondences)
- [Interim Data vs Ground Truth](#interim-data-vs-ground-truth)
- [Interim Data vs Benchmark Data](#interim-data-vs-benchmark-data)
- [Interim Data vs Evaluation Outputs](#interim-data-vs-evaluation-outputs)
- [Interim Data vs Cache](#interim-data-vs-cache)
- [Sensor-Aware Interim Processing](#sensor-aware-interim-processing)
- [Scale-Aware Interim Processing](#scale-aware-interim-processing)
- [Illumination-Aware Processing](#illumination-aware-processing)
- [Provenance](#provenance)
- [Naming and Organization](#naming-and-organization)
- [Version Control](#version-control)
- [Storage Policy](#storage-policy)
- [Validation and Quality Control](#validation-and-quality-control)
- [Stale Interim Data](#stale-interim-data)
- [Safe Contributor Workflow](#safe-contributor-workflow)
- [Reproducibility](#reproducibility)
- [Failure Handling](#failure-handling)
- [Recommended Conceptual Structure](#recommended-conceptual-structure)
- [Known Limitations and Gaps](#known-limitations-and-gaps)
- [Current vs Recommended Architecture](#current-vs-recommended-architecture)
- [Source Basis](#source-basis)

---

## Purpose

The purpose of `data/interim/` is to provide a controlled location for **data that is neither the original input nor the final scientific/evaluation output**.

For ChandraMap, this distinction is useful because lunar image correspondence may require data preparation before matching can be performed.

The project materials describe a workflow involving:

```text
Source / Reference Images
        ↓
Sensor-Aware Preparation
        ↓
Scale-Aware Preparation
        ↓
Correspondence Processing
        ↓
Geometric Verification
        ↓
Sub-Pixel Refinement
        ↓
Transformation
        ↓
Registration
        ↓
Evaluation
```

The exact repository implementation of these stages is not fully confirmed.

`data/interim/` should therefore be treated as a **data lifecycle boundary**, not as a claim that every stage necessarily writes files there.

---

## Current Status

### Formal Definition

**Not yet defined.**

The supplied ChandraMap project materials do not provide a formal specification stating exactly:

- which files belong in `data/interim/`,
- which scripts create them,
- which formats they use,
- whether they are persisted between runs,
- how they are versioned,
- or how they are invalidated.

### Conceptual Role

**Supported / Recommended.**

The project architecture clearly requires intermediate representations between source imagery and final correspondence/registration results.

The technical feedback specifically recommends:

- sensor-specific preparation,
- comparable effective scales,
- structural representations where appropriate,
- reference pyramids,
- candidate correspondences,
- RANSAC verification,
- sub-pixel refinement,
- and preservation of metadata.

These concepts provide the scientific basis for an interim-data layer.

### Implementation Status

**Not confirmed.**

This README must not be interpreted as evidence that a particular interim-processing pipeline has already been implemented.

---

## What Interim Data Means in ChandraMap

For ChandraMap, **interim data should mean a reproducible intermediate representation derived from source/external data that is required or useful for a later stage of the correspondence and registration workflow.**

It is not simply synonymous with:

- temporary data,
- cache,
- raw data,
- benchmark data,
- ground truth,
- final results,
- or algorithm predictions.

The key characteristic is its **position in the data transformation chain**.

Conceptually:

```text
Original / External Data
          │
          ▼
    Intermediate Data
          │
          ▼
Correspondence / Registration
          │
          ▼
Scientific / Evaluation Output
```

An interim artifact should therefore have a known relationship to its upstream source and downstream purpose.

---

## What Belongs in `data/interim/`

Only artifacts that satisfy the following principle should be considered for `data/interim/`:

> **The artifact is derived from source/reference data and is intended to be consumed by another ChandraMap processing stage rather than being the final evaluation result.**

Depending on the implemented pipeline, this could include intermediate representations created during:

- sensor-specific preparation,
- scale preparation,
- structural representation generation,
- reference-scale preparation,
- correspondence preparation,
- or other explicitly documented intermediate transformations.

However, the exact artifacts are **Not yet defined**.

---

## What Does Not Belong in `data/interim/`

The following should not automatically be placed in `data/interim/`.

### Original Source Products

Original mission or externally obtained products belong to the appropriate source/external-data layer.

They should not be relabeled as interim merely because ChandraMap later consumes them.

---

### Ground Truth

Ground truth is evaluation/reference information.

It must remain distinguishable from algorithm-generated intermediate data.

---

### Benchmark Definitions

Benchmark specifications belong to benchmark documentation/configuration rather than being treated as interim imagery.

---

### Final Registration Results

A final registered product is an output of the processing pipeline, not an intermediate input.

---

### Evaluation Results

Examples include:

- RMSE,
- inlier statistics,
- spatial coverage,
- runtime,
- failure rate.

These are evaluation artifacts, not interim data.

The project feedback explicitly identifies these as metrics used to measure the system.

---

### Disposable Cache

Data that exists solely to accelerate computation and can safely be regenerated should be treated as cache rather than scientifically meaningful interim data.

The exact ChandraMap cache architecture is **Not specified**.

---

## Data Lifecycle

A useful ChandraMap data lifecycle is:

```text
┌──────────────────────┐
│ Source / External    │
│ Lunar Data           │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ Validation +         │
│ Metadata Inspection  │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ Interim              │
│ Representations      │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ Correspondence       │
│ Processing           │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ Geometric            │
│ Verification         │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ Sub-Pixel / Final    │
│ Registration         │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ Evaluation           │
│ + Benchmark Results  │
└──────────────────────┘
```

This is a **conceptual architecture**, not a claim about exact filesystem operations.

---

## Interim Data in the ChandraMap Pipeline

The supplied project feedback describes sensor-aware preparation followed by coarse/multi-scale search, local matching, geometric verification, refinement, and evaluation.

A corresponding conceptual data flow is:

```text
Lunar Source / Reference Data
            │
            ▼
      Metadata + Validation
            │
            ▼
      ┌───────────────┐
      │ INTERIM DATA  │
      └───────┬───────┘
              │
      ┌───────┴────────┐
      │                │
      ▼                ▼
Scale Preparation   Structural /
                    Sensor-Aware
                    Representation
      │                │
      └───────┬────────┘
              ▼
       Local Correspondence
              │
              ▼
       Candidate Matches
              │
              ▼
        RANSAC / Geometry
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
          Evaluation
```

The project feedback explicitly recommends the order:

```text
Candidate Matches
        ↓
RANSAC + Initial Model
        ↓
Sub-Pixel Refine Inliers
        ↓
Refit Final Transform
```

and warns against treating matcher confidence as proof of geometric correctness.

---

## Examples of Potential Interim Artifacts

The following are **possible examples**, not confirmed current contents of `data/interim/`.

| Potential artifact                      | Possible role                                | Status                                                    |
| --------------------------------------- | -------------------------------------------- | --------------------------------------------------------- |
| Sensor-specific image representation    | Prepare different sensors for registration   | Recommended                                               |
| Structural image representation         | Provide terrain-focused representation       | Recommended                                               |
| Comparable-scale representation         | Reduce physically meaningless scale mismatch | Recommended                                               |
| Reference pyramid level                 | Support multi-scale search                   | Specified conceptually                                    |
| Prepared image tile                     | Support controlled matching/search           | Not confirmed                                             |
| Metadata-enriched representation        | Preserve processing context                  | Recommended                                               |
| Intermediate correspondence preparation | Prepare data for local matching              | Not confirmed                                             |
| SIFT descriptors/keypoints              | Algorithm-specific matching data             | Not confirmed as stored in `data/interim/`                |
| RANSAC inliers                          | Geometrically verified correspondences       | Not automatically interim; storage location not confirmed |
| Registration result                     | Final output                                 | Does not belong here by default                           |

The repository should add a specific artifact to `data/interim/` only after its lifecycle role has been defined.

---

## Raw vs External vs Interim vs Processed vs Final

These categories should remain conceptually distinct.

| Category                  | Meaning in ChandraMap                                          | Typical lifecycle position |
| ------------------------- | -------------------------------------------------------------- | -------------------------- |
| Raw/source                | Original lunar product                                         | Beginning                  |
| External                  | Data obtained outside the repository/project processing system | Beginning                  |
| Interim                   | Derived representation required by later processing            | Middle                     |
| Processed                 | More finalized derived data intended for downstream use        | Later                      |
| Benchmark                 | Controlled data/evaluation organization                        | Evaluation                 |
| Ground truth              | Independent reference for correctness                          | Evaluation                 |
| Expected/reference output | Reference result used for comparison                           | Evaluation                 |
| Final output              | Registration/result produced by the pipeline                   | End                        |
| Cache                     | Disposable computational acceleration                          | Any stage                  |

The exact repository boundaries between `interim` and `processed` are **Not yet defined**.

---

## Interim Data vs External Data

External data describes **where data originated**, whereas interim data describes **its lifecycle state**.

For example:

```text
External lunar product
        ↓
ChandraMap preparation
        ↓
Derived representation
```

The original external product remains external/source data.

The derived representation may become interim data.

Therefore:

> **External and interim are not mutually exclusive concepts by provenance alone; the lifecycle state must be considered.**

However, the repository should avoid duplicating external source products inside `data/interim/`.

---

## Interim Data vs Correspondences

Correspondences are relationships between locations/features in two images.

The project distinguishes:

```text
Candidate Matches
       ↓
RANSAC / Geometric Verification
       ↓
Verified Inliers
```

Candidate matches are algorithm outputs rather than ground truth.

Whether correspondence files are stored under `data/interim/`, a dedicated correspondence directory, or an experiment-output location is **Not confirmed**.

Do not assume that all correspondence artifacts belong in `data/interim/`.

---

## Interim Data vs Ground Truth

Ground truth should remain independent from interim processing artifacts.

For example:

```text
                ┌──────────────┐
                │ Ground Truth │
                └──────┬───────┘
                       │
                       │ evaluation
                       ▼
Source ──► Interim ──► Algorithm ──► Result
```

Ground truth should not be generated by the same process being evaluated unless the benchmark explicitly defines such a procedure.

The registration feedback recommends independent check points that are not used to fit the transformation being evaluated.

---

## Interim Data vs Benchmark Data

Benchmark data is controlled by an evaluation protocol.

Interim data is controlled by a processing lifecycle.

These are different concepts.

A benchmark experiment may consume interim data:

```text
Benchmark Pair
      │
      ▼
Source / Reference Data
      │
      ▼
Interim Representation
      │
      ▼
Algorithm
      │
      ▼
Evaluation
```

An interim representation should therefore not automatically be considered part of the benchmark itself.

---

## Interim Data vs Evaluation Outputs

Evaluation outputs answer questions such as:

- How many matches survived?
- What is the inlier ratio?
- How well distributed are the matches?
- What is the check-point RMSE?
- What is the runtime?
- Did the registration succeed?

The supplied registration feedback explicitly identifies these categories of measurements.

These outputs should not be confused with the intermediate image representations used to produce them.

---

## Interim Data vs Cache

### Interim Data

An interim artifact has scientific or pipeline meaning.

It should be possible to explain:

- its source,
- its transformation,
- its intended consumer,
- and why it exists.

### Cache

A cache exists primarily to avoid repeating computation.

It may be deleted and regenerated without changing the scientific definition of the experiment, provided regeneration is deterministic and documented.

The current repository does not confirm a dedicated cache implementation.

### Rule

If deleting the artifact changes the documented scientific state of the experiment, it should not be treated as an ordinary disposable cache.

---

## Sensor-Aware Interim Processing

ChandraMap's project materials explicitly state that OHRC, TMC-2, and IIRS should not simply be treated as identical images.

This has direct implications for interim data.

### OHRC / TMC-2

The project feedback recommends preserving relevant geometry and acquisition information and comparing representations such as intensity, edges, gradients, or other structural information when illumination changes are important.

Any resulting representation may be an interim artifact if it is explicitly produced for downstream matching.

The exact implemented representation is **Not confirmed**.

---

### IIRS

IIRS is described as hyperspectral/infrared data.

The project feedback recommends first producing a registration-friendly 2D representation rather than treating the entire hyperspectral cube as an ordinary image. Possible experimental directions include:

- selected bands,
- PCA/composite representations,
- structural maps.

These are **recommended experimental approaches**, not evidence that a specific IIRS representation already exists in `data/interim/`.

---

## Scale-Aware Interim Processing

Scale handling is a central concern in ChandraMap.

The project feedback explicitly warns that:

> Upsampling changes pixel count, not the physical information available from the source.

It recommends using comparable effective scales and reference pyramids/downsampling of the higher-resolution side where appropriate.

Therefore, an interim scale representation should preserve its relationship to the original source.

Conceptually:

```text
High-Resolution Reference
          │
          ▼
Comparable-Scale Representation
          │
          ▼
Coarse Correspondence
          │
          ▼
Fine Refinement
```

The exact scale-generation implementation is **Not yet defined**.

---

## Illumination-Aware Processing

Lunar illumination requires particular care.

A change in Sun angle can change shadow geometry around terrain features. Brightness normalization alone cannot make the underlying shadow geometry identical.

Potential intermediate representations may therefore include structure-oriented forms such as:

- edges,
- gradients,
- terrain structures,
- relative feature geometry.

These should only be stored as interim data when they are actually generated by the implemented pipeline.

---

## Provenance

Every meaningful interim artifact should have a provenance relationship to its input.

Conceptually:

```text
Source Product
     │
     ├── product identity
     ├── sensor
     ├── scale / GSD
     ├── geometry metadata
     └── illumination metadata
             │
             ▼
      Interim Artifact
             │
             ├── transformation applied
             ├── processing configuration
             └── intended downstream stage
```

The project feedback specifically recommends preserving metadata such as:

- footprint,
- pixel scale,
- map projection,
- viewing geometry,
- lighting geometry,

where available.

The exact ChandraMap provenance schema is **Not yet defined**.

---

## Provenance Rules

An interim artifact should not become a permanent dependency if its origin cannot be determined.

At minimum, the repository should eventually be able to answer:

1. What source data produced this artifact?
2. What processing produced it?
3. Which configuration was used?
4. What downstream stage consumes it?
5. Is it reproducible?
6. Can it safely be deleted and regenerated?

If these questions cannot be answered, the artifact should be considered insufficiently documented.

---

## Naming and Organization

A repository-wide naming convention for `data/interim/` is **Not yet defined**.

The following are therefore **Recommended conventions**, not current repository rules.

### Recommended Naming Principles

Use names that communicate:

- dataset identity,
- source/reference role,
- representation type,
- processing stage,
- benchmark/stress condition where applicable.

Avoid ambiguous names such as:

```text
final.png
new.png
test2.png
image_fixed.png
output_latest.png
```

These names do not provide scientific provenance.

---

### Recommended Conceptual Organization

A possible structure is:

```text
data/
└── interim/
    ├── <dataset-or-pair>/
    │   ├── source/
    │   ├── reference/
    │   └── metadata/
    │
    └── ...
```

This is **not a confirmed repository structure**.

Do not create additional directories solely because they appear in this README.

---

## Version Control

Interim data should generally be treated differently from source code and small benchmark definitions.

### Default Recommendation

Large derived imagery and reproducible intermediate artifacts should **not automatically be committed to Git**.

Instead, version:

- the procedure that creates them,
- the configuration defining that procedure,
- the dataset identity,
- relevant metadata,
- and provenance.

### Possible Exceptions

A small interim artifact may be version controlled when it is:

- required as a test fixture,
- required to reproduce a documented example,
- explicitly used as a benchmark fixture,
- small enough for repository maintenance,
- or otherwise intentionally part of the project.

These are recommendations rather than a currently confirmed ChandraMap policy.

---

## What Should Not Be Committed

Unless the repository explicitly requires it, do not commit:

- Large derived lunar imagery.
- Duplicate copies of external products.
- Machine-specific intermediate files.
- Temporary debugging artifacts.
- Untracked experiment outputs.
- Credentials.
- Access tokens.
- Private filesystem paths.
- Unvalidated benchmark artifacts.
- Stale representations whose provenance is unknown.

The exact ChandraMap `.gitignore` policy for `data/interim/` is **Not confirmed**.

---

## Storage Policy

The storage strategy for `data/interim/` is currently **Not specified**.

No particular:

- object store,
- cloud provider,
- database,
- dataset registry,
- cache engine,
- data-versioning platform,
- or external artifact system

should be assumed.

The important requirement is that storage must not destroy provenance or make a benchmark silently dependent on a developer's local machine.

---

## Validation and Quality Control

Interim data should be validated before it becomes an input to scientific evaluation.

### 1. Source Validation

Confirm:

- source identity,
- reference identity,
- sensor identity where applicable,
- source/reference relationship.

---

### 2. Metadata Validation

Check available metadata for consistency.

Important context includes:

- pixel scale,
- footprint,
- projection,
- viewing geometry,
- illumination geometry.

The exact required metadata fields are **Not yet defined**.

---

### 3. Representation Validation

For derived representations, confirm:

- the transformation was intentional,
- the source is known,
- the representation has not accidentally introduced unsupported information,
- the physical scale remains understood.

This is particularly important for upsampling.

---

### 4. Registration Readiness

Before an interim representation is used for matching, verify that it is appropriate for the intended correspondence experiment.

For example:

```text
Source Representation
        +
Reference Representation
        ↓
Comparable Physical Scale?
        ↓
Compatible Structural Information?
        ↓
Metadata Preserved?
        ↓
Ready for Matching
```

---

## Stale Interim Data

Interim data can become stale when its source or processing definition changes.

Examples include:

- source imagery replaced,
- metadata corrected,
- preprocessing changed,
- scale configuration changed,
- benchmark definition changed,
- sensor-specific representation changed.

### Recommended Rule

An interim artifact should be regenerated whenever the upstream input or processing definition changes in a way that affects its contents.

Do not silently reuse an old artifact simply because its filename looks correct.

---

## Detecting Stale Data

A future provenance system should make it possible to compare:

```text
Current Source
      vs
Artifact Source

Current Processing Definition
      vs
Artifact Processing Definition
```

If they differ, the artifact should be considered potentially stale.

The exact checksum/version mechanism is **Not specified**.

---

## Safe Contributor Workflow

Contributors adding interim data should follow this process.

### Step 1 — Identify the Upstream Source

Determine exactly which source/reference data is being processed.

Do not copy an undocumented local file into the repository.

---

### Step 2 — Define the Purpose

State why the intermediate representation is needed.

For example:

```text
Source
  ↓
Comparable-scale representation
  ↓
Local matching
```

The exact use case must be documented.

---

### Step 3 — Record Transformations

Document what happened to the source data.

Do not describe an operation as implemented unless the repository actually performs it.

---

### Step 4 — Preserve Metadata

Keep relevant source metadata available.

The project explicitly emphasizes preserving physical and acquisition context during preparation.

---

### Step 5 — Validate

Check that the artifact is:

- readable,
- traceable,
- physically meaningful,
- appropriate for its intended downstream stage.

---

### Step 6 — Decide Whether It Needs Git

Ask:

> Can this artifact be deterministically regenerated from documented source data and processing definitions?

If yes, committing the large artifact itself may not be necessary.

If no, document why the artifact must be retained and what reproducibility mechanism is required.

---

## Reproducibility

Interim data should support reproducible experiments rather than becoming hidden state.

A reproducible workflow should conceptually look like:

```text
Known Source Data
       +
Known Metadata
       +
Known Processing Definition
       │
       ▼
  Interim Artifact
       │
       ▼
Same Downstream Pipeline
       │
       ▼
Comparable Result
```

The project feedback strongly favors measurable, reproducible experiments beginning with a known source/reference pair and retaining match plots, rejected outliers, registered overlays, inlier statistics, and independent check-point error.

---

## Reproducibility Checklist

Before relying on an interim artifact in a reported experiment:

- [ ] Source data is identified.
- [ ] Reference data is identified.
- [ ] Sensor/product identity is known.
- [ ] Relevant metadata is preserved.
- [ ] Processing operation is documented.
- [ ] Scale changes are documented.
- [ ] Illumination-related transformations are documented.
- [ ] Artifact purpose is documented.
- [ ] Artifact can be regenerated or its retention requirement is documented.
- [ ] Benchmark identity is known where applicable.
- [ ] Ground truth remains separate.
- [ ] Evaluation points remain independent of model fitting where required.

---

## Failure Handling

Intermediate failures should not automatically be deleted.

For example, if a representation causes:

- fewer useful correspondences,
- poor spatial coverage,
- unstable geometry,
- or registration failure,

the failure can provide evidence about the processing choice.

The project explicitly recommends retaining difficult cases and measuring where the system fails rather than presenting only successful examples.

A future experiment-record system should make it possible to associate:

```text
Input
  ↓
Interim Representation
  ↓
Algorithm
  ↓
Failure / Success
  ↓
Diagnostic Metrics
```

The exact failure-record schema is **Not yet defined**.

---

## Recommended Conceptual Structure

The following is a **recommended conceptual structure only**:

```text
data/
└── interim/
    │
    ├── README.md
    │
    ├── <dataset-or-pair>/          # Recommended
    │   ├── source-derived/         # Recommended
    │   ├── reference-derived/      # Recommended
    │   └── metadata/               # Recommended
    │
    └── ...
```

A more specialized structure should only be introduced when the actual pipeline requires it.

For example, the project may eventually need to distinguish between:

```text
Sensor Preparation
        │
        ├── OHRC
        ├── TMC-2
        └── IIRS
```

or:

```text
Scale Preparation
        │
        ├── coarse
        ├── intermediate
        └── fine
```

But neither structure is currently confirmed as the implemented repository organization.

---

## Important Scientific Constraints

### Do Not Invent Spatial Detail

Upsampling an image does not recover information absent from the source sensor.

Therefore, an interim resized representation must not be described as containing newly recovered terrain detail.

---

### Do Not Treat Sensors as Equivalent

OHRC, TMC-2, and IIRS have substantially different sensing characteristics and should not automatically share an identical preparation path.

---

### Do Not Treat Brightness Normalization as Illumination Invariance

Lunar Sun-angle changes can alter shadow geometry.

A normalized image is not necessarily geometrically equivalent to another image of the same terrain.

---

### Do Not Treat Candidate Matches as Truth

Candidate correspondences must be geometrically verified before they are treated as reliable control points.

RANSAC is specifically identified as part of this verification process.

---

### Do Not Let Flexible Warping Hide Poor Correspondences

The registration feedback explicitly warns that flexible warping can make an overlay appear good even when the underlying correspondences are weak.

Control points should be accurate and spatially distributed before applying flexible warping.

---

## Known Limitations and Gaps

The following items are currently **Not confirmed or Not yet defined** from the supplied project materials:

- Exact contents of `data/interim/`.
- Exact interim-data schema.
- Exact filenames.
- Exact image formats.
- Exact metadata schema.
- Exact provenance schema.
- Exact preprocessing scripts.
- Exact processing commands.
- Exact configuration files.
- Exact cache implementation.
- Exact Git policy for interim artifacts.
- Exact large-file storage strategy.
- Exact data-versioning mechanism.
- Exact checksum policy.
- Exact automated validation tooling.
- Exact stale-artifact detection.
- Exact distinction between `interim/` and `processed/` at the filesystem level.

These gaps should be resolved by repository implementation rather than guessed in documentation.

---

## Current vs Recommended Architecture

| Area                              | Current status             | Documentation interpretation                                                |
| --------------------------------- | -------------------------- | --------------------------------------------------------------------------- |
| `data/interim/` directory         | **Requested/documented**   | Directory documentation is being established                                |
| Formal definition of interim data | **Not yet defined**        | This README provides a clearly labeled conceptual definition                |
| Exact interim artifacts           | **Not confirmed**          | Do not assume specific files exist                                          |
| Sensor-aware preparation          | **Specified conceptually** | Project materials require different treatment of lunar sensors              |
| Scale-aware preparation           | **Specified conceptually** | Comparable effective scale is emphasized                                    |
| Illumination-aware preparation    | **Specified conceptually** | Structural representations and Sun-angle experiments are recommended        |
| Reference pyramid                 | **Specified conceptually** | Identified as part of the coarse-to-fine strategy                           |
| Candidate correspondences         | **Specified conceptually** | Must remain distinct from verified inliers                                  |
| RANSAC verification               | **Specified conceptually** | Used to reject geometrically inconsistent candidates                        |
| Sub-pixel refinement              | **Specified conceptually** | Applied after reliable inliers are established                              |
| Ground-truth separation           | **Specified**              | Ground truth/check points must remain independent of fitting where required |
| Provenance schema                 | **Not yet defined**        | Required before large-scale reproducibility claims                          |
| Interim naming convention         | **Not yet defined**        | Recommended principles provided                                             |
| Git policy                        | **Not confirmed**          | Large generated artifacts should not automatically be committed             |
| Cache policy                      | **Not specified**          | Must be defined separately from scientifically meaningful interim data      |
| Stale-data policy                 | **Not yet defined**        | Regeneration should follow upstream changes                                 |
| Automated data validation         | **Not confirmed**          | Should be added as the data pipeline matures                                |

---

## Recommended Acceptance Criteria for an Interim Artifact

Before treating an artifact as a valid member of `data/interim/`, it should satisfy:

### Identity

- [ ] Its upstream source is known.
- [ ] Its purpose is known.
- [ ] Its lifecycle stage is known.

### Provenance

- [ ] The transformation that created it is documented.
- [ ] Relevant metadata is preserved.
- [ ] Its relationship to the source can be established.

### Scientific Validity

- [ ] No unsupported physical information has been introduced.
- [ ] Scale remains physically interpretable.
- [ ] Sensor differences are respected.
- [ ] Illumination transformations are documented.

### Pipeline Validity

- [ ] A downstream processing stage consumes or is intended to consume it.
- [ ] It is not merely an unexplained debugging artifact.
- [ ] It is not being used as ground truth without independent justification.

### Reproducibility

- [ ] It can be regenerated, or
- [ ] The reason for retaining the artifact is documented.

### Repository Hygiene

- [ ] It does not contain credentials.
- [ ] It does not contain private machine paths.
- [ ] Its storage strategy is appropriate for its size.
- [ ] Its provenance does not depend on undocumented local state.

---

## Relationship to Benchmarking

Interim data is an implementation layer; benchmarking is an evaluation layer.

The distinction should remain:

```text
                    BENCHMARK
                       │
          ┌────────────┴────────────┐
          │                         │
      Input Pair                Ground Truth
          │                         │
          ▼                         │
   Interim Processing               │
          │                         │
          ▼                         │
   Correspondence                   │
          │                         │
          ▼                         │
   Geometric Verification           │
          │                         │
          ▼                         │
   Registration                     │
          │                         │
          └──────────┬──────────────┘
                     ▼
                 Evaluation
```

The same benchmark pair should be processed consistently when comparing methods.

The project feedback specifically recommends comparing the same test pairs through:

1. SIFT baseline,
2. a stronger matcher,
3. the full sensor-aware/multi-scale pipeline,

so that measured improvements can be attributed to actual pipeline changes.

---

## Relationship to Stress Testing

Stress tests intentionally make controlled conditions harder.

The project materials identify stress cases including:

- Sun-angle differences,
- scale differences,
- modality differences,
- stronger geometric differences,
- low-feature terrain.

Interim data used for stress testing must preserve the distinction between:

```text
Original Benchmark Data
        │
        ▼
Controlled Stress Transformation
        │
        ▼
Stress-Test Interim Data
        │
        ▼
Same Evaluation Protocol
```

A stress-test artifact must not silently replace the baseline data.

---

## Data Integrity Rules

The following rules should be treated as core principles for `data/interim/`:

1. **Never overwrite source data with interim data.**
2. **Never call an algorithm prediction ground truth.**
3. **Never hide an untracked transformation inside an interim artifact.**
4. **Never treat upsampling as recovered spatial information.**
5. **Never remove difficult examples merely because they fail.**
6. **Never silently reuse stale intermediate artifacts.**
7. **Never commit credentials or private machine information.**
8. **Never assume a sensor-specific representation is universally valid.**
9. **Never report final accuracy from points used only for model fitting when independent checks are required.**
10. **Never present a recommended directory structure as implemented functionality.**

---

## Development Guidance

The safest implementation path is to establish the data definition before building a large interim-data collection.

The supplied project feedback recommends beginning with a small real source/reference pair, making one end-to-end baseline work, and recording measurable outputs before expanding the system.

A corresponding data-development sequence is:

```text
1. Define source/reference data
            ↓
2. Confirm metadata
            ↓
3. Define one reproducible interim representation
            ↓
4. Run one measurable correspondence experiment
            ↓
5. Validate geometric verification
            ↓
6. Validate registration
            ↓
7. Validate independent evaluation
            ↓
8. Expand to scale / illumination / modality stress
            ↓
9. Standardize interim-data organization
```

This avoids creating a large collection of undocumented intermediate files before the scientific workflow is stable.

---

## Final Definition

Until the repository defines a more precise implementation-level specification, `data/interim/` should be understood as:

> **The controlled data layer for reproducible intermediate representations derived from ChandraMap source/reference data and consumed by subsequent correspondence, geometric verification, registration, or evaluation stages.**

This definition intentionally does **not** prescribe a particular image format, preprocessing algorithm, directory structure, metadata schema, storage backend, or processing command.

Those details must be established by the implementation and benchmark specifications rather than inferred.

---

## Source Basis

This README is based on the ChandraMap project materials currently available for this project.

### `SIH26166 Silarlar PS.pdf`

The supplied problem statement identifies Chandrayaan-2 OHRC, TMC-2, and IIRS as primary target/source imagery and LRO NAC as reference/training imagery, with LRO WAC, Kaguya/SELENE TC, and synthetic lunar augmentations identified as additional data sources/use cases.

### `Aryan_SIH_26166_Lunar_Image_Correspondence_Feedback.pptx`

The project feedback establishes the need for sensor-aware preparation, physically meaningful scale handling, preservation of geometry metadata, illumination-aware representations, and a common structural representation only after sensor-specific preparation.

It also establishes the recommended progression from data definition to a small real dataset, baseline matching, scale/reference preparation, sensor-aware processing, stronger matchers, refinement, and evaluation.

### `Aryan_Lunar_Image_Registration_Feedback.pdf`

The registration feedback establishes the distinction between candidate correspondences and verified inliers, the RANSAC → sub-pixel refinement → final transformation sequence, independent check-point evaluation, spatial coverage, and preservation of difficult cases.

---

## Final Rule

`data/interim/` should never become a dumping ground for files that happen to be produced during development.

Every meaningful interim artifact should have a clear answer to four questions:

```text
Where did it come from?
        ↓
What transformation produced it?
        ↓
Why is it needed by ChandraMap?
        ↓
Can it be reproduced or safely regenerated?
```

If those questions cannot be answered, the artifact should remain **unclassified or undocumented** rather than being presented as validated ChandraMap interim data.
