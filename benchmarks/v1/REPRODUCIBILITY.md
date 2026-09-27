# ChandraMap V1 Reproducibility

This document defines the reproducibility requirements for **ChandraMap V1** benchmark experiments.

It specifies how to reproduce benchmark execution, preserve result provenance, control experimental variables, verify comparability between runs, reproduce stress tests and failures, and retain the artifacts required to audit a result.

> **Important:** ChandraMap V1 is intended to measure lunar image correspondence and registration, not merely produce visually convincing overlays or mosaics. Reproducibility therefore applies to the complete chain from input data and preprocessing through correspondence generation, geometric verification, transformation estimation, independent check-point evaluation, metrics, runtime, and failure reporting.

---

## 1. Scope

This document applies to experiments performed under:

```text
benchmarks/v1/
```

and covers:

- benchmark dataset inputs;
- reference imagery;
- ground truth;
- benchmark configuration;
- preprocessing;
- multi-scale processing;
- local correspondence;
- geometric verification;
- sub-pixel refinement;
- transformation estimation;
- registration evaluation;
- retrieval evaluation where applicable;
- stress tests;
- runtime measurement;
- failure handling;
- software environment;
- hardware environment;
- random seeds;
- result artifacts;
- provenance;
- reproducibility verification.

The V1 evaluation flow is based on the controlled sequence:

```text
Source Image
     ↓
Metadata / Sensor Route
     ↓
Sensor-Specific Preprocessing
     ↓
Multi-Scale Search
     ↓
Candidate Matches
     ↓
RANSAC / Geometric Verification
     ↓
Verified Inliers
     ↓
Sub-Pixel Tie-Point Refinement
     ↓
Final Transformation
     ↓
Registered Output
     ↓
Independent Check Points
     ↓
Metrics
```

The project feedback specifically emphasizes preserving the distinction between candidate correspondences, verified inliers, refined tie points, and the final transformation.

---

## 2. Reproducibility Definition

ChandraMap V1 uses three related but distinct concepts.

### 2.1 Reproducible Execution

A benchmark can be executed again using the same:

- code;
- configuration;
- dataset;
- ground truth;
- preprocessing;
- environment;
- hardware assumptions;
- random seeds;
- evaluation procedure.

The second execution follows the same computational procedure as the first.

---

### 2.2 Reproducible Result

A repeated execution produces results that are sufficiently consistent with the original result under the benchmark's defined reproducibility conditions.

This does **not** necessarily require bit-for-bit identical floating-point output.

Numerical differences can occur because of:

- GPU kernels;
- parallel execution;
- library implementations;
- hardware differences;
- floating-point behavior;
- nondeterministic operations;
- operating-system differences.

The acceptable tolerance for a metric must be defined by the benchmark implementation before it is used to declare two runs reproducible.

**Tolerance policy:** `To be defined`

---

### 2.3 Traceable Result

A result is traceable when another person can connect the result to its complete provenance:

```text
Code
+
Configuration
+
Dataset
+
Ground Truth
+
Metrics
+
Environment
+
Execution
+
Artifacts
```

A benchmark result without this provenance should not be treated as a fully reproducible V1 result.

---

# 3. V1 Reproducibility Levels

V1 should distinguish different levels of reproducibility rather than treating reproducibility as binary.

| Level                              | Meaning                                                                                                                       |
| ---------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| Level 0 — Untracked                | Result exists without sufficient provenance                                                                                   |
| Level 1 — Traceable                | Code, configuration, dataset and result metadata are recorded                                                                 |
| Level 2 — Executable               | Another user can execute the same benchmark procedure                                                                         |
| Level 3 — Numerically reproducible | Repeated execution produces results within defined tolerances                                                                 |
| Level 4 — Environment-matched      | Code, data, dependencies, hardware assumptions and execution conditions are pinned closely enough for controlled reproduction |

### V1 target

**Recommended target:** Level 3 for published benchmark results.

Level 4 is recommended for results intended to serve as a long-term reference baseline.

The exact acceptance policy for these levels is:

`To be defined`

---

# 4. Source-of-Truth Policy

Reproducibility must be based on the actual ChandraMap V1 benchmark definition and implementation.

The following hierarchy should be used:

1. V1 benchmark specification.
2. V1 metric definitions.
3. Versioned benchmark configuration.
4. Versioned dataset and ground-truth manifests.
5. Actual implementation at the recorded commit.
6. Environment lockfiles and dependency versions.
7. Benchmark execution record.
8. Generated result artifacts.

Generic assumptions must not silently override project-specific definitions.

Where the repository does not yet specify a value, record:

```text
Not specified
```

rather than inventing a value.

Where an implementation is planned but does not yet exist, record:

```text
Not yet implemented
```

---

# 5. Current Reproducibility Status

The following fields must be completed as the benchmark implementation becomes available.

| Reproducibility component       | V1 status           |
| ------------------------------- | ------------------- |
| Benchmark version               | `v1`                |
| Benchmark specification version | Not specified       |
| Dataset version                 | Not specified       |
| Dataset manifest                | Not specified       |
| Ground-truth version            | Not specified       |
| Ground-truth manifest           | Not specified       |
| Configuration schema            | Not specified       |
| Reference configuration         | Not specified       |
| Benchmark execution command     | Not specified       |
| Environment specification       | Not specified       |
| Dependency lockfile             | Not specified       |
| Random seed policy              | To be defined       |
| Deterministic execution policy  | To be defined       |
| Hardware reference environment  | Not specified       |
| Metric tolerance policy         | To be defined       |
| Result schema                   | Not specified       |
| Artifact manifest               | Not specified       |
| Automated reproducibility check | Not yet implemented |

This table is intentionally explicit. A reproducibility document must not claim that infrastructure exists when it has not yet been implemented.

---

# 6. Reproducibility Contract

A V1 benchmark result should be considered reproducible only when the following contract can be satisfied:

```text
Same benchmark version
        +
Same dataset manifest
        +
Same ground-truth manifest
        +
Same configuration
        +
Same preprocessing
        +
Same implementation commit
        +
Compatible environment
        +
Controlled random seeds
        +
Same evaluation procedure
        +
Recorded hardware
        +
Recorded artifacts
        =
Reproducible benchmark execution
```

For numerical reproducibility:

```text
Reproducible execution
        +
Defined numerical tolerances
        =
Reproducible result
```

---

# 7. Required Inputs

A V1 reproduction requires the following input classes.

## 7.1 Source Images

The exact source products used by the original benchmark run must be available.

For each source image, record at minimum:

- product identifier;
- sensor;
- filename or stable identifier;
- image dimensions;
- pixel scale/GSD where available;
- acquisition metadata where available;
- footprint where available;
- map projection status;
- calibration/processing status;
- checksum.

The supplied project feedback explicitly recommends recording image dimensions, product type, pixel scale/GSD, and available geolocation or map-projection metadata.

---

## 7.2 Reference Images

The exact reference product used for each benchmark case must be recorded.

The project materials identify LRO NAC as a possible reference source and LRO WAC as an additional lunar-scale/illumination dataset, but the final V1 reference product is not specified by the supplied materials.

Therefore:

```text
V1 reference product:
Not specified
```

The benchmark implementation must not silently substitute a different reference product.

---

## 7.3 Dataset Manifest

Every benchmark dataset should have a manifest.

Recommended fields:

```yaml
dataset:
  name: <dataset-name>
  version: <dataset-version>
  manifest_version: <manifest-version>
  created_at: <timestamp>
  source: <source>
  cases:
    - case_id: <case-id>
      source_image: <path-or-id>
      reference_image: <path-or-id>
      sensor: <sensor>
      stress_case: <stress-case>
      checksum:
        algorithm: sha256
        source: <hash>
        reference: <hash>
```

The exact schema is:

`To be defined`

The principle is mandatory: **a dataset path alone is not sufficient provenance**.

---

# 8. Dataset Versioning

A reproduction must use the exact dataset version associated with the original benchmark result.

Do not reproduce a historical result using:

- a newer image;
- a replacement product;
- a reprocessed product;
- a different crop;
- a different tile;
- a different reference image;
- a different metadata interpretation;

unless the experiment is explicitly classified as a new benchmark run.

### Required dataset identity

At minimum:

```text
Dataset name
Dataset version
Case identifiers
File identifiers
File checksums
Manifest version
```

### If dataset versioning does not yet exist

Record:

```text
Dataset version: Not specified
```

and preserve a manifest containing immutable file checksums for the benchmark run.

---

# 9. Ground-Truth Versioning

Ground truth is a critical part of V1 reproducibility.

The benchmark must record:

- ground-truth source;
- ground-truth version;
- coordinate system;
- point definitions;
- control-point definitions;
- check-point definitions;
- point precision;
- creation or validation procedure;
- associated source/reference pair;
- checksum or immutable identifier.

The project evaluation guidance explicitly requires independent check points when evaluating the final transformation. Points used to estimate the transformation must not also be used as independent evaluation points.

---

## 9.1 Control Points

Control points are points used to estimate the transformation.

They may include verified inliers after geometric verification and subsequent sub-pixel refinement.

---

## 9.2 Check Points

Check points are independent points used to evaluate the final transformation.

They must not be used to fit the transformation being evaluated.

The required conceptual separation is:

```text
Candidate Matches
        ↓
RANSAC
        ↓
Verified Inliers / Control Points
        ↓
Sub-Pixel Refinement
        ↓
Final Transformation
        ↓
Independent Check Points
        ↓
Registration Error
```

This separation is central to V1 reproducibility.

---

# 10. Ground-Truth Coordinate System

The benchmark must record the coordinate system used by ground truth.

At minimum:

```text
coordinate_space:
  type: <source-pixel | reference-pixel | projected | geographic | other>
  convention: <documented-convention>
  origin: <documented-origin>
  axis_order: <documented-axis-order>
  units: <documented-units>
```

The exact V1 coordinate convention is:

`Not specified`

Do not assume that `(x, y)` conventions are identical across libraries.

Coordinate ordering must be explicitly documented.

---

# 11. Sub-Pixel Ground Truth

V1 concerns source-image sub-pixel accuracy.

The benchmark should therefore report registration error in source-image pixels before converting it to physical units.

Conversion to metres is appropriate only when:

- the product GSD is known;
- the projection is known;
- the reference truth supports the conversion;
- the geometric assumptions make the conversion meaningful.

The supplied technical feedback explicitly warns that equal pixel errors can correspond to different physical errors for sensors with different GSDs.

### V1 rule

```text
Source-image pixel error
        ↓
Primary registration measurement
        ↓
Optional physical conversion
```

Do not use physical conversion to hide uncertainty in the source-image measurement.

---

# 12. Sensor-Specific Reproduction

ChandraMap must not assume that OHRC, TMC-2, and IIRS are interchangeable inputs.

The project materials specify separate sensor-aware processing routes and recommend preserving metadata such as:

- footprint;
- pixel scale;
- map projection;
- viewing geometry;
- lighting geometry.

---

## 12.1 OHRC

For OHRC reproduction, record:

- exact product;
- product GSD;
- calibration status;
- image dimensions;
- map-projection status;
- preprocessing configuration;
- illumination/viewing metadata where available.

The challenge product metadata should be treated as the final authority for the actual product scale rather than relying on a generic sensor specification.

---

## 12.2 TMC-2

For TMC-2 reproduction, record:

- exact product;
- product GSD;
- calibration status;
- map-projection status;
- any DEM/geometric information used;
- preprocessing configuration.

Use the project spelling:

```text
TMC-2
```

rather than `TMC` when referring specifically to the instrument.

---

## 12.3 IIRS

IIRS must be treated as spectral/hyperspectral data rather than automatically as an ordinary 2D camera image.

The exact V1 IIRS representation is:

`Not specified`

Possible representations discussed by the project materials include:

- selected band;
- PCA/composite representation;
- structural representation.

These are candidate approaches, not an assertion that one is the official V1 representation.

Every IIRS experiment must record the exact representation used.

---

# 13. Reference-Scale Reproduction

The benchmark must reproduce the same effective scale relationship between source and reference images.

Do not reproduce a result by simply changing image dimensions.

Upsampling changes pixel count but does not recover missing spatial information.

The project guidance recommends:

```text
Higher-resolution reference
        ↓
Reference pyramid / downsampling
        ↓
Comparable effective ground scale
        ↓
Coarse search
        ↓
Fine matching
```

Record:

- source GSD;
- reference GSD;
- pyramid levels;
- scale factors;
- selected matching level;
- interpolation method;
- crop/tile dimensions.

If any value is unavailable:

```text
Not specified
```

---

# 14. Preprocessing Reproducibility

Preprocessing is part of the benchmark, not an informal preparation step.

Every preprocessing operation that changes the input to matching must be reproducible.

Record:

- calibration;
- radiometric normalization;
- denoising;
- contrast normalization;
- edge/gradient conversion;
- structural representation;
- masking;
- shadow handling;
- cropping;
- reprojection;
- resampling;
- normalization;
- data type conversion;
- clipping;
- interpolation;
- image orientation;
- reference-scale conversion.

---

## 14.1 Preprocessing Principle

Every preprocessing step must be explicitly identified.

Use:

```text
Raw Input
   ↓
Step 1
   ↓
Step 2
   ↓
Step 3
   ↓
Matcher Input
```

rather than describing preprocessing as:

```text
preprocessed image
```

without further information.

---

## 14.2 Illumination Reproduction

Sun-angle changes can alter shadows and terrain appearance.

Brightness normalization alone does not reproduce identical terrain appearance across different illumination geometries.

The benchmark should therefore preserve:

- illumination metadata where available;
- photometric normalization parameters;
- structural representation;
- shadow masks if used;
- any illumination-specific preprocessing.

The recommended stress test compares the same region under similar and substantially different lighting conditions.

---

# 15. Matching Reproduction

The matching algorithm and all relevant parameters must be recorded.

For a SIFT baseline, record at minimum:

- detector configuration;
- descriptor configuration;
- number of features or equivalent limit;
- image pyramid settings;
- descriptor matching method;
- ratio-test configuration;
- cross-check configuration;
- candidate filtering.

The project feedback identifies the baseline flow as:

```text
SIFT
→ descriptor matching
→ ratio/cross-check filtering
→ RANSAC
→ affine/homography
→ residual error
```

---

## 15.1 Learned Matchers

If using:

- ALIKED + LightGlue;
- LoFTR;
- another learned matcher;

record:

- model name;
- model version;
- model checkpoint;
- checkpoint checksum;
- preprocessing;
- image size;
- scale;
- device;
- inference precision;
- confidence thresholds;
- matching configuration.

A pretrained model must not be described as lunar-invariant without benchmark evidence.

---

# 16. Retrieval Reproduction

Global retrieval is conditional in the V1 architecture.

If reliable metadata such as latitude/longitude, footprint, or map-projection information can restrict the search, that information may be used according to the benchmark configuration.

If global retrieval is used, the reference database must itself be reproducible.

The project feedback specifies an offline preparation flow:

```text
Reference Images
      ↓
Tiles + Scales
      ↓
Global Descriptor
      ↓
FAISS Index + Metadata
```

The online source then produces the corresponding descriptor and retrieves Top-K candidates.

Record:

- reference database version;
- tile-generation configuration;
- tile identifiers;
- scale levels;
- global descriptor implementation;
- descriptor model/checkpoint;
- descriptor dimensionality;
- FAISS version;
- index type;
- index parameters;
- index checksum;
- Top-K value;
- candidate filtering.

---

# 17. Geometric Verification Reproduction

Geometric verification must be reproduced exactly.

Record:

- transformation model;
- RANSAC implementation;
- RANSAC threshold;
- confidence;
- iteration limit;
- minimum sample size;
- random seed;
- inlier criterion;
- coordinate normalization;
- degenerate-case handling.

The exact V1 RANSAC parameter values are:

`Not specified`

Do not invent them in benchmark reports.

---

# 18. Required Geometry Order

The V1 geometry sequence should preserve the following order:

```text
Candidate Matches
        ↓
RANSAC + Initial Model
        ↓
Verified Inliers
        ↓
Sub-Pixel Refinement
        ↓
Refit Final Transformation
        ↓
Registration Evaluation
```

This order is explicitly identified in the supplied technical feedback.

A reproduction that changes this sequence is a different experimental configuration unless the benchmark specification explicitly allows it.

---

# 19. Transformation Model

The transformation model must be recorded.

Potential models discussed by the project include:

- affine;
- homography;
- local/piecewise refinement;
- sensor-geometry/DEM-assisted approaches.

The appropriate model depends on the image geometry.

A single global transformation must not automatically be assumed to explain all lunar terrain.

Residual vectors should be inspected for systematic spatial variation.

### V1 default model

`Not specified`

---

# 20. Sub-Pixel Refinement Reproduction

If sub-pixel refinement is enabled, record:

- refinement method;
- patch size;
- search window;
- interpolation;
- optimization parameters;
- stopping criteria;
- coordinate precision;
- implementation/library version;
- seed if applicable.

The project guidance recommends refining verified control points after RANSAC and then refitting the final transformation.

### V1 refinement implementation

`Not specified`

If sub-pixel refinement is not implemented in the executed benchmark:

```text
subpixel_refinement:
  enabled: false
  status: not_yet_implemented
```

Do not claim sub-pixel refinement merely because the architecture contains a placeholder for it.

---

# 21. Metric Reproduction

The same metric definitions must be used when reproducing a result.

V1 evaluation includes, where applicable:

- candidate match count;
- verified-inlier count;
- inlier ratio;
- spatial coverage;
- check-point error;
- check-point RMSE;
- ground error where meaningful;
- runtime;
- failure status/rate;
- Recall@1 / Recall@5 when retrieval is evaluated.

The project evaluation guidance explicitly separates retrieval, local matching, distribution, registration, geospatial accuracy, and system metrics.

---

## 21.1 Independent Check-Point Evaluation

This is a mandatory reproducibility rule.

The transformation must be evaluated on points that were not used to fit the final transformation.

Incorrect:

```text
Inliers
  ↓
Fit transform
  ↓
Evaluate RMSE on same inliers
```

Required:

```text
Control Points
  ↓
Fit transform

Independent Check Points
  ↓
Evaluate final transform
```

Using the fitting points as the final evaluation set can make registration appear more accurate than it is.

---

# 22. Random Seed Control

Randomness must be controlled wherever the implementation uses stochastic operations.

Potential sources include:

- RANSAC;
- randomized sampling;
- data shuffling;
- train/test selection;
- augmentation;
- retrieval sampling;
- learned-model inference;
- GPU operations;
- multiprocessing.

The exact V1 seed policy is:

`To be defined`

Until a project-wide policy exists, every experiment should record:

```yaml
randomness:
  seed: <integer-or-null>
  python_seed: <integer-or-null>
  numpy_seed: <integer-or-null>
  framework_seed: <integer-or-null>
  torch_deterministic: <true|false|not_applicable>
  cudnn_deterministic: <true|false|not_applicable>
```

Only record values that actually apply to the implementation.

---

# 23. Deterministic Execution

A fixed seed does not automatically guarantee deterministic execution.

GPU libraries and parallel algorithms may still produce numerical differences.

The benchmark should distinguish:

```text
Seed-controlled
```

from:

```text
Deterministic
```

These are not equivalent.

### Required reporting

```yaml
determinism:
  requested: <true|false>
  achieved: <true|false|unknown>
  mechanism: <documented>
  limitations: <documented>
```

If deterministic execution has not been validated:

```text
achieved: unknown
```

Do not claim deterministic execution based only on setting a random seed.

---

# 24. Software Environment

A reproducible run must record the software environment.

At minimum:

- operating system;
- Python version;
- package manager;
- dependency versions;
- lockfile version;
- OpenCV version;
- numerical-library versions;
- ML framework version where applicable;
- CUDA version where applicable;
- cuDNN version where applicable;
- FAISS version where applicable;
- matcher/model versions;
- benchmark implementation commit.

The exact supported environment is:

`Not specified`

---

# 25. Dependency Pinning

Benchmark dependencies should be pinned.

Preferred order:

```text
Exact lockfile
      ↓
Exact package versions
      ↓
Compatible version ranges
      ↓
Unspecified dependency
```

For benchmark publication, exact or lockfile-based dependency resolution is preferred.

A benchmark should not rely on:

```text
pip install package
```

without recording the resolved version.

---

# 26. Git Commit Pinning

Every benchmark result must identify the exact code version.

Required:

```text
repository:
  commit: <full-commit-sha>
  branch: <branch-or-detached>
  tag: <tag-or-null>
  dirty: <true|false>
```

The benchmark should preferably be executed from a clean working tree.

If the tree contains uncommitted modifications:

```text
dirty: true
```

and the result must identify the modified state.

A Git branch name alone is insufficient because branches move.

---

# 27. Configuration Pinning

The exact benchmark configuration must be retained.

Record:

- benchmark version;
- dataset;
- ground truth;
- sensor;
- preprocessing;
- scale settings;
- retrieval settings;
- matcher;
- matcher parameters;
- geometric model;
- RANSAC settings;
- sub-pixel settings;
- metrics;
- runtime settings;
- random seeds.

Recommended representation:

```yaml
benchmark:
  version: v1
  config_version: <version>

data:
  dataset_version: <version>
  ground_truth_version: <version>

pipeline:
  sensor: <sensor>
  preprocessing: <configuration>
  scale: <configuration>
  retrieval: <configuration>
  matcher: <configuration>
  geometry: <configuration>
  refinement: <configuration>

evaluation:
  metrics: <configuration>

runtime:
  device: <cpu|cuda|other>
  workers: <integer>
```

The exact schema is:

`To be defined`

---

# 28. Hardware Reproduction

Runtime is hardware-dependent.

Every runtime result should record:

- CPU;
- GPU;
- GPU memory;
- RAM;
- operating system;
- CUDA/runtime version where applicable;
- worker count;
- device used;
- power/performance configuration if relevant.

The expected judging hardware is explicitly identified as a question still requiring confirmation in the supplied feedback.

Therefore:

```text
Official V1 hardware:
Not specified
```

Do not compare runtime values from materially different hardware environments without clearly labeling the difference.

---

# 29. Runtime Measurement

Runtime measurement must have a defined timing boundary.

Examples:

```text
Input loading
→ preprocessing
→ retrieval
→ matching
→ verification
→ refinement
→ evaluation
```

or:

```text
matching only
```

These are different measurements.

Record:

```yaml
runtime:
  scope: <documented-scope>
  wall_time_s: <value>
  cpu_time_s: <value-or-null>
  device: <device>
  warmup_runs: <value-or-null>
  measured_runs: <value>
```

The V1 runtime boundary is:

`Not specified`

---

# 30. Benchmark Case Identity

Every test pair should have a stable case identifier.

Recommended:

```text
v1-<sensor>-<region>-<condition>-<case>
```

Example:

```text
v1-ohrc-region01-easy-001
```

The exact naming convention is:

`To be defined`

Do not use filenames alone as case identifiers because filenames may change.

---

# 31. Stress-Test Reproduction

V1 should preserve the defined stress-test categories.

| Stress case         | Reproduction purpose               |
| ------------------- | ---------------------------------- |
| Easy pair           | End-to-end pipeline validation     |
| Sun-angle stress    | Illumination robustness            |
| Scale stress        | Multi-scale matching behavior      |
| Modality stress     | Cross-sensor representation        |
| Geometry stress     | Transformation/refinement behavior |
| Low-feature terrain | Failure and false-match behavior   |

These categories are directly reflected in the supplied evaluation guidance.

---

## 31.1 Easy Pair

Reproduce:

- known overlap;
- documented source/reference pair;
- similar illumination;
- moderate scale difference;
- fixed preprocessing.

Expected purpose:

```text
Verify complete pipeline execution.
```

---

## 31.2 Sun-Angle Stress

Use the same lunar region under substantially different illumination conditions.

Record:

- acquisition conditions;
- lighting metadata where available;
- preprocessing;
- structural representation;
- shadow handling.

The result should preserve the performance difference rather than hiding it through post-processing.

---

## 31.3 Scale Stress

Reproduce a large difference in effective ground scale.

Record:

- source GSD;
- reference GSD;
- scale ratio;
- pyramid level;
- downsampling factor;
- selected matching scale.

Do not enlarge a low-resolution source and interpret the additional pixels as recovered terrain detail.

---

## 31.4 Modality Stress

For IIRS or other cross-modal cases, record:

- source sensor;
- reference sensor;
- source representation;
- selected band(s);
- PCA/composite configuration if used;
- structural representation;
- normalization.

Do not silently substitute an easier same-modality pair.

---

## 31.5 Geometry Stress

Record:

- viewpoint difference;
- terrain characteristics;
- projection status;
- transformation model;
- residual diagnostics;
- local refinement configuration.

Residual-vector behavior should be preserved where available.

---

## 31.6 Low-Feature Terrain

Low-feature and repetitive terrain should remain in the benchmark.

The purpose is not to make the benchmark look successful.

The purpose is to expose:

- insufficient correspondences;
- false matches;
- spatial clustering;
- unstable transformations;
- failure cases.

The supplied feedback explicitly recommends keeping failures and showing where the pipeline fails.

---

# 32. Baseline Reproduction

The V1 benchmark should preserve the ability to reproduce a simple baseline.

The recommended progression is:

```text
Baseline
SIFT
  ↓
Geometric verification
  ↓
Registration
  ↓
Metrics
```

followed by controlled comparisons with:

```text
Stronger matcher only
```

and:

```text
Full sensor-aware + multi-scale pipeline
```

The same image pairs should be used for controlled comparisons.

---

# 33. Fair Comparison Rules

Two experiments are comparable only when the intended experimental variables are controlled.

For a method comparison, keep fixed:

- benchmark cases;
- source images;
- reference images;
- ground truth;
- evaluation metrics;
- hardware where practical;
- runtime measurement boundary;
- preprocessing unless preprocessing itself is the experimental variable;
- train/test split where applicable.

Only change the variable under investigation.

Example:

```text
SIFT baseline
      ↓
same image pairs
      ↓
same ground truth
      ↓
same evaluation
```

versus:

```text
Learned matcher
      ↓
same image pairs
      ↓
same ground truth
      ↓
same evaluation
```

---

# 34. Preventing Data Leakage

Benchmark data must not leak evaluation information into the transformation fitting process.

The most important V1 rule is:

```text
Check points
≠
Transformation-fitting points
```

If the benchmark includes retrieval, evaluation regions must also be defined so that reference indexing does not unintentionally expose evaluation answers in a way prohibited by the benchmark specification.

The exact retrieval split policy is:

`Not specified`

---

# 35. Result Provenance

Every benchmark run should produce a provenance record.

Recommended structure:

```yaml
run:
  run_id: <unique-run-id>
  timestamp_utc: <timestamp>
  benchmark_version: v1
  config_version: <version>

code:
  repository: <repository>
  commit: <full-sha>
  dirty: false

data:
  dataset_version: <version>
  dataset_manifest: <path-or-id>
  ground_truth_version: <version>
  ground_truth_manifest: <path-or-id>

environment:
  os: <value>
  python: <value>
  dependencies: <manifest-or-lockfile>
  cuda: <value-or-null>
  cudnn: <value-or-null>

hardware:
  cpu: <value>
  gpu: <value-or-null>
  ram_gb: <value>

randomness:
  seed: <value-or-null>
  deterministic: <true|false|unknown>

execution:
  command: <exact-command>
  status: <success|failure>
  runtime_s: <value>

results:
  result_file: <path>
  metrics_file: <path>

artifacts:
  manifest: <path>
```

The exact schema is:

`To be defined`

---

# 36. Run ID

Every benchmark execution should have a unique run ID.

Recommended components:

```text
benchmark version
+
timestamp
+
short commit identifier
+
configuration identifier
```

Example:

```text
v1-20260927T120000Z-a1b2c3d-config01
```

The exact run-ID convention is:

`To be defined`

---

# 37. Artifact Retention

A reproducible benchmark should retain enough information to diagnose and reproduce the result.

Recommended artifacts include:

### Required

- benchmark configuration;
- dataset manifest;
- ground-truth manifest;
- provenance record;
- metric results;
- execution status;
- code commit.

### Strongly recommended

- candidate matches;
- verified inliers;
- transformation matrix/model;
- sub-pixel refined points;
- independent check-point errors;
- residual vectors;
- registered output;
- match visualization;
- rejected-match visualization;
- runtime information;
- environment information.

The supplied project feedback specifically recommends preserving match plots, rejected outliers, registered overlays, inlier statistics, check-point error, coverage, RMSE, and runtime.

---

# 38. Artifact Manifest

A benchmark run should contain an artifact manifest.

Example:

```yaml
artifacts:
  - name: source_image
    path: <path>
    checksum: <sha256>

  - name: reference_image
    path: <path>
    checksum: <sha256>

  - name: candidate_matches
    path: <path>
    checksum: <sha256>

  - name: verified_inliers
    path: <path>
    checksum: <sha256>

  - name: transformation
    path: <path>
    checksum: <sha256>

  - name: check_point_errors
    path: <path>
    checksum: <sha256>

  - name: metrics
    path: <path>
    checksum: <sha256>

  - name: registered_output
    path: <path>
    checksum: <sha256>
```

The exact artifact schema is:

`To be defined`

---

# 39. Checksums

Large scientific datasets can change without their filenames changing.

Checksums should therefore be used to identify immutable benchmark inputs.

Recommended algorithm:

```text
SHA-256
```

If the repository specifies another algorithm, follow the repository specification.

The exact project-wide checksum policy is:

`To be defined`

---

# 40. Reproducing a Historical Result

To reproduce a historical V1 result:

### Step 1 — Obtain the run record

Locate:

```text
run_id
```

and retrieve its provenance record.

### Step 2 — Verify the code

Checkout the exact recorded commit.

```text
commit = <recorded-full-sha>
```

Do not substitute the current branch head.

### Step 3 — Verify the dataset

Retrieve the exact dataset version and compare file checksums.

### Step 4 — Verify ground truth

Retrieve the exact ground-truth version and verify its manifest.

### Step 5 — Restore the environment

Install the exact dependency versions recorded by the run.

### Step 6 — Restore configuration

Use the exact benchmark configuration.

### Step 7 — Restore randomness

Set the recorded seed values.

### Step 8 — Verify hardware assumptions

Record the actual reproduction hardware.

### Step 9 — Execute

Use the exact benchmark command recorded in the provenance manifest.

### Step 10 — Compare results

Compare:

- status;
- candidate matches;
- verified inliers;
- inlier ratio;
- spatial coverage;
- check-point errors;
- check-point RMSE;
- ground error where applicable;
- runtime;
- failure reason.

---

# 41. Benchmark Command

The repository-specific benchmark execution command is:

```text
Not specified
```

Do not invent a command in this document.

Once implemented, the exact command used for a published result must be stored in the result provenance.

Example format:

```text
<exact benchmark command>
```

The placeholder must be replaced by the actual repository command before the benchmark is considered fully operational.

---

# 42. Configuration Validation Before Execution

Before running a benchmark, validate:

```text
[ ] Benchmark version is correct
[ ] Dataset version is correct
[ ] Dataset manifest exists
[ ] Source checksums match
[ ] Reference checksums match
[ ] Ground-truth version is correct
[ ] Ground-truth checksums match
[ ] Configuration version is correct
[ ] Code commit is recorded
[ ] Working tree state is recorded
[ ] Dependencies are pinned
[ ] Hardware is recorded
[ ] Random seed is recorded
[ ] Determinism setting is recorded
[ ] Runtime scope is recorded
```

If any required item cannot be verified, the run should be marked accordingly rather than silently treated as an exact reproduction.

---

# 43. Output Validation

After execution, verify:

```text
[ ] Run completed or failure was recorded
[ ] Candidate matches were generated or failure was recorded
[ ] RANSAC status is recorded
[ ] Inlier count is recorded
[ ] Inlier ratio is recorded when defined
[ ] Spatial coverage is recorded when defined
[ ] Independent check points are identified
[ ] Check-point RMSE is calculated only on independent points
[ ] Ground error is calculated only when meaningful
[ ] Runtime is recorded
[ ] Failure reason is recorded if applicable
[ ] Result file exists
[ ] Artifact manifest exists
[ ] Checksums are recorded
```

---

# 44. Failure Reproduction

Failures are benchmark results.

A failed run must not be deleted simply because it does not produce a successful registration.

The run record should preserve:

```yaml
status: failure

failure:
  stage: <stage>
  reason: <reason>
  exception: <exception-or-null>
  message: <message-or-null>
  recoverable: <true|false|unknown>
```

Possible stages include:

```text
input
metadata
preprocessing
scale-normalization
retrieval
matching
geometric-verification
subpixel-refinement
transformation
evaluation
output
runtime
```

The exact failure taxonomy is:

`To be defined`

---

# 45. Failure Reproduction Procedure

To reproduce a failure:

1. Use the same case ID.
2. Verify the same source and reference checksums.
3. Use the same ground truth.
4. Checkout the recorded code commit.
5. Restore the recorded configuration.
6. Restore the same seed.
7. Restore the same preprocessing.
8. Restore the same model/checkpoint.
9. Restore the same hardware class where relevant.
10. Execute the benchmark.
11. Compare the failure stage and diagnostic output.

A failure that cannot be reproduced should be marked:

```text
reproduction_status: not_reproduced
```

rather than silently classified as a successful run.

---

# 46. Failure Preservation

The benchmark result set should retain both:

```text
Successful cases
```

and:

```text
Failed cases
```

Failure rate is itself a V1 system metric.

Removing difficult cases after execution creates selection bias and makes the benchmark less representative.

The project feedback explicitly recommends exposing difficult cases honestly and retaining failures.

---

# 47. Reproducibility of Visual Artifacts

Visual outputs should be reproducible where possible.

This includes:

- candidate-match plots;
- inlier plots;
- residual-vector plots;
- registered overlays;
- coverage visualizations.

Record:

- image dimensions;
- plot configuration;
- normalization;
- colormap;
- coordinate convention;
- visualization software version;
- output format.

Visual artifacts are diagnostic evidence and must not be treated as replacements for quantitative metrics.

---

# 48. Reproducibility of Spatial Coverage

Spatial coverage depends on its configuration.

If using grid coverage, record:

- grid dimensions;
- evaluation-region definition;
- coordinate space;
- cell assignment rule;
- inlier definition.

The project materials give a `4 × 4` grid as an example, but this should not be treated as the official V1 configuration unless the benchmark specification defines it.

If using convex-hull coverage, record:

- coordinate system;
- evaluation region;
- hull implementation;
- degenerate-case handling.

---

# 49. Reproducibility of Ground Error

If ground error in metres is reported, record:

```text
source pixel error
+
GSD
+
projection
+
reference coordinate system
+
conversion method
```

Do not reproduce ground error from a previously rounded metre value.

Recompute it from the underlying source-image error and documented conversion parameters.

If the conversion is not scientifically meaningful:

```text
ground_error_m: null
```

The primary measurement remains source-image pixel error.

---

# 50. Numerical Comparison Policy

Two runs should be compared using the same metric definitions and evaluation population.

At minimum compare:

| Result            |    Original | Reproduction |
| ----------------- | ----------: | -----------: |
| Status            |    Recorded |     Measured |
| Candidate matches |    Recorded |     Measured |
| Verified inliers  |    Recorded |     Measured |
| Inlier ratio      |    Recorded |     Measured |
| Spatial coverage  |    Recorded |     Measured |
| Check-point count |    Recorded |     Measured |
| Check-point RMSE  |    Recorded |     Measured |
| Ground error      | Recorded/NA |  Measured/NA |
| Runtime           |    Recorded |     Measured |
| Failure reason    | Recorded/NA |  Measured/NA |

### Tolerance

The official V1 numerical tolerance policy is:

`To be defined`

Until defined, report the exact values from both runs rather than declaring them identical.

---

# 51. Bitwise Reproducibility

Bitwise-identical output is a stronger requirement than numerical reproducibility.

It may not be achievable across:

- CPU architectures;
- GPU architectures;
- CUDA versions;
- BLAS implementations;
- parallel execution strategies;
- floating-point implementations.

Therefore:

```text
Bitwise equality
```

must not be assumed as the default definition of V1 reproducibility.

If bitwise reproducibility is required for a particular experiment, it must be explicitly configured and validated.

---

# 52. Environment Drift

Environment drift can occur even when source code remains unchanged.

Examples include:

- changed dependency versions;
- changed CUDA drivers;
- changed GPU kernels;
- changed compiler versions;
- changed operating-system libraries;
- changed model checkpoints;
- changed data files.

For this reason, a benchmark result should record both:

```text
Code identity
```

and:

```text
Environment identity
```

---

# 53. Model and Checkpoint Reproducibility

If a learned model is used, record:

- model name;
- model version;
- checkpoint filename;
- checkpoint checksum;
- source repository;
- model configuration;
- inference precision;
- preprocessing;
- device.

A model name alone is insufficient.

For example:

```text
LightGlue
```

does not uniquely identify:

```text
LightGlue version
+
feature extractor
+
checkpoint
+
configuration
```

---

# 54. Reference Index Reproducibility

If FAISS retrieval is used, the index is a benchmark artifact.

The index should not be regenerated implicitly during every experiment unless that regeneration is itself part of the benchmark.

Record:

```text
index version
index type
index parameters
reference tile manifest
descriptor model
descriptor version
descriptor parameters
index checksum
```

The project feedback specifically distinguishes offline reference-index construction from online retrieval.

---

# 55. Metadata Reproducibility

Metadata can affect benchmark behavior.

Where metadata is used, preserve:

- original metadata;
- parsed metadata;
- normalized metadata;
- metadata version;
- parser version;
- coordinate system;
- missing-value handling.

This is especially important for:

- latitude/longitude;
- footprint;
- pixel scale;
- map projection;
- spacecraft geometry;
- viewing geometry;
- illumination geometry.

The project materials explicitly recommend preserving these metadata fields where available.

---

# 56. Metadata-Assisted Search

Using available metadata to restrict the search is valid only when it is part of the benchmark configuration.

The project feedback describes metadata-assisted search as legitimate engineering and recommends using a pure image-retrieval fallback when reliable metadata is unavailable.

Therefore a result must record:

```yaml
search:
  metadata_assisted: <true|false>
  metadata_fields_used:
    - <field>
```

A metadata-assisted run and a pure-image-retrieval run should not be treated as identical configurations.

---

# 57. Benchmark Result Schema

A V1 result should contain enough information to reconstruct the evaluation.

Recommended structure:

```yaml
benchmark:
  version: v1
  case_id: <case-id>
  run_id: <run-id>

code:
  commit: <full-sha>
  dirty: <true|false>

data:
  dataset_version: <version>
  ground_truth_version: <version>

configuration:
  config_version: <version>
  sensor: <sensor>
  preprocessing: <configuration>
  scale: <configuration>
  matcher: <configuration>
  geometry: <configuration>
  refinement: <configuration>

randomness:
  seed: <value-or-null>
  deterministic: <true|false|unknown>

metrics:
  candidate_matches: <value-or-null>
  verified_inliers: <value-or-null>
  inlier_ratio: <value-or-null>
  spatial_coverage: <value-or-null>
  checkpoint_count: <value-or-null>
  checkpoint_rmse_px: <value-or-null>
  ground_error_m: <value-or-null>

runtime:
  seconds: <value-or-null>

status:
  value: <success|failure>
  failure_stage: <value-or-null>
  failure_reason: <value-or-null>

artifacts:
  manifest: <path-or-id>
```

The exact machine-readable schema is:

`To be defined`

---

# 58. Result Immutability

Once a benchmark result has been published or used as a reference baseline, its provenance should be treated as immutable.

If a result is corrected:

```text
Original run
      ↓
Preserved
      +
Correction run
      ↓
New run ID
```

Do not silently overwrite the original result.

This makes historical benchmark comparisons auditable.

---

# 59. Reproduction Record

A person reproducing a benchmark should create a reproduction record.

Recommended:

```yaml
reproduction:
  original_run_id: <run-id>
  reproduction_run_id: <run-id>

  reproduced_by: <identifier>
  timestamp_utc: <timestamp>

  code_commit: <sha>
  dataset_version: <version>
  ground_truth_version: <version>
  config_version: <version>

  environment:
    os: <value>
    python: <value>
    gpu: <value-or-null>

  status:
    execution: <success|failure>
    numerical_match: <pass|fail|unknown>

  deviations:
    - <documented-deviation>

  notes:
    - <notes>
```

---

# 60. What Makes Two Runs Comparable?

Two V1 runs are directly comparable when:

- they evaluate the same benchmark cases;
- they use the same dataset version;
- they use the same ground-truth version;
- they use the same metric definitions;
- they use compatible preprocessing;
- the intended experimental variable is clearly identified;
- the configuration is recorded;
- the evaluation population is the same;
- failures are handled consistently.

They are not directly comparable merely because they:

- use the same algorithm name;
- use the same filenames;
- produce visually similar overlays;
- report the same metric names.

---

# 61. What Does Not Count as Reproduction?

The following are not sufficient:

### Re-running the current branch

```text
git checkout main
run benchmark
```

without verifying the original commit.

### Downloading the latest dataset

A newer product can change the result.

### Using the same filename

The underlying file may have changed.

### Using the same algorithm name

Different versions or parameters can produce different behavior.

### Using the same random seed only

Seed control does not guarantee deterministic execution.

### Reusing the same fitting points

This invalidates independent registration evaluation.

### Reproducing only the visualization

A matching overlay does not reproduce the quantitative benchmark.

### Reporting only successful cases

This hides failure behavior.

---

# 62. Recommended Reproduction Workflow

The complete workflow is:

```text
1. Identify original run
        ↓
2. Load provenance record
        ↓
3. Verify Git commit
        ↓
4. Verify dataset manifest
        ↓
5. Verify source/reference checksums
        ↓
6. Verify ground-truth manifest
        ↓
7. Restore environment
        ↓
8. Restore configuration
        ↓
9. Restore random seeds
        ↓
10. Verify hardware
        ↓
11. Run benchmark
        ↓
12. Validate output artifacts
        ↓
13. Recalculate metrics
        ↓
14. Compare numerical results
        ↓
15. Record deviations
        ↓
16. Publish reproduction status
```

---

# 63. Recommended Benchmark Directory Artifacts

The exact repository structure is governed by the project's benchmark architecture.

A reproducibility run should logically preserve artifacts corresponding to:

```text
benchmark-run/
├── provenance/
│   ├── run.yaml
│   ├── environment.yaml
│   └── command.txt
│
├── data/
│   ├── dataset-manifest.yaml
│   └── ground-truth-manifest.yaml
│
├── config/
│   └── benchmark-config.yaml
│
├── results/
│   ├── metrics.yaml
│   └── status.yaml
│
└── artifacts/
    ├── matches/
    ├── inliers/
    ├── transforms/
    ├── residuals/
    ├── overlays/
    └── visualizations/
```

This is a recommended logical organization, not a claim about the current repository implementation.

---

# 64. Reproducibility Checklist

## Dataset

- [ ] Dataset version recorded.
- [ ] Dataset manifest recorded.
- [ ] Source identifiers recorded.
- [ ] Reference identifiers recorded.
- [ ] File checksums recorded.
- [ ] Image dimensions recorded.
- [ ] Product type recorded.
- [ ] Pixel scale/GSD recorded.
- [ ] Relevant metadata preserved.

## Ground Truth

- [ ] Ground-truth version recorded.
- [ ] Ground-truth manifest recorded.
- [ ] Coordinate system recorded.
- [ ] Control points identified.
- [ ] Independent check points identified.
- [ ] Check points excluded from transformation fitting.

## Code

- [ ] Full Git commit recorded.
- [ ] Working-tree state recorded.
- [ ] Benchmark version recorded.
- [ ] Configuration version recorded.

## Environment

- [ ] OS recorded.
- [ ] Python version recorded.
- [ ] Dependency versions recorded.
- [ ] Lockfile recorded where available.
- [ ] CUDA version recorded where applicable.
- [ ] GPU recorded where applicable.
- [ ] CPU recorded.
- [ ] RAM recorded.

## Randomness

- [ ] Seed recorded.
- [ ] Framework seeds recorded where applicable.
- [ ] Deterministic settings recorded.
- [ ] Nondeterministic operations documented.

## Pipeline

- [ ] Sensor route recorded.
- [ ] Preprocessing recorded.
- [ ] Scale configuration recorded.
- [ ] Retrieval configuration recorded where applicable.
- [ ] Matcher configuration recorded.
- [ ] RANSAC configuration recorded.
- [ ] Transformation model recorded.
- [ ] Sub-pixel configuration recorded.

## Metrics

- [ ] Candidate match count recorded.
- [ ] Verified-inlier count recorded.
- [ ] Inlier ratio recorded.
- [ ] Spatial coverage recorded.
- [ ] Independent check-point error recorded.
- [ ] Check-point RMSE recorded.
- [ ] Ground error recorded only when meaningful.
- [ ] Runtime recorded.
- [ ] Failure status recorded.

## Artifacts

- [ ] Candidate matches retained.
- [ ] Verified inliers retained.
- [ ] Transformation retained.
- [ ] Check-point errors retained.
- [ ] Residuals retained where applicable.
- [ ] Registered output retained.
- [ ] Visualization artifacts retained.
- [ ] Artifact manifest retained.

---

# 65. V1 Reproducibility Acceptance Criteria

A benchmark run should not be labeled **fully reproducible** until the following are available:

| Requirement                        | Required                                           |
| ---------------------------------- | -------------------------------------------------- |
| V1 benchmark version               | Yes                                                |
| Exact code commit                  | Yes                                                |
| Dataset identity                   | Yes                                                |
| Dataset checksums                  | Yes                                                |
| Ground-truth identity              | Yes                                                |
| Ground-truth checksums             | Yes                                                |
| Configuration                      | Yes                                                |
| Environment                        | Yes                                                |
| Randomness policy                  | Yes                                                |
| Execution command                  | Yes                                                |
| Result metrics                     | Yes                                                |
| Failure status                     | Yes                                                |
| Artifact provenance                | Yes                                                |
| Independent check-point evaluation | Yes for registration accuracy                      |
| Numerical tolerance policy         | Required before claiming numerical reproducibility |

The exact pass/fail tolerance values are:

`To be defined`

---

# 66. Reproducibility Limitations

V1 reproducibility has several limitations.

### Dataset availability

Some lunar products may be large, restricted, unavailable, or subject to external distribution conditions.

If the exact dataset cannot be redistributed, preserve:

- stable product identifiers;
- source information;
- acquisition metadata;
- checksums;
- download/retrieval instructions;
- preprocessing metadata.

---

### Hardware differences

Runtime and some numerical results may vary across hardware.

---

### GPU nondeterminism

GPU execution may produce small numerical differences even with fixed seeds.

---

### Library drift

Different versions of:

- OpenCV;
- NumPy;
- PyTorch;
- FAISS;
- CUDA;
- other dependencies;

can alter results.

---

### Ground-truth uncertainty

Reproducibility of the computation does not automatically imply correctness of the ground truth.

The benchmark can reproduce a ground-truth-based measurement while the ground truth itself contains uncertainty.

---

### Lunar geometry

A single global transformation may not explain every lunar scene.

Residuals can vary spatially because of terrain relief, viewpoint, projection, or sensor geometry.

---

### Sensor differences

OHRC, TMC-2, IIRS, and reference imagery have different spatial scales and imaging characteristics.

Results should therefore remain associated with their sensor/path rather than being blindly collapsed into one mixed average.

The supplied project feedback specifically recommends separate sensor results, particularly for OHRC/TMC-2 versus IIRS.

---

# 67. Scientific Reproducibility vs Software Reproducibility

These should not be confused.

## Software reproducibility

Answers:

> Can the same implementation be executed again?

Requires:

- code;
- dependencies;
- configuration;
- environment.

## Scientific reproducibility

Answers:

> Does the same experimental procedure support the same measured conclusion?

Requires:

- correct dataset;
- correct ground truth;
- controlled experiment;
- correct metrics;
- independent evaluation;
- documented limitations.

A benchmark can be software-reproducible but scientifically invalid if, for example, it evaluates the transformation on the same points used to fit it.

---

# 68. Benchmark Integrity Rules

The following rules are mandatory for trustworthy V1 reporting:

1. Do not modify benchmark cases after seeing results without recording the change.
2. Do not remove failures from the final dataset without documenting the exclusion.
3. Do not change ground truth after seeing benchmark results without creating a new version.
4. Do not change preprocessing silently.
5. Do not change the transformation model silently.
6. Do not replace a reference image silently.
7. Do not report fitting error as independent registration accuracy.
8. Do not convert pixel error to metres without a valid conversion basis.
9. Do not report placeholder accuracy numbers as experimental results.
10. Do not use decorative confidence percentages as benchmark metrics.
11. Do not claim deterministic execution without testing it.
12. Do not claim reproducibility without preserving provenance.

The project feedback specifically warns against unmeasured percentage and star ratings and emphasizes actual RMSE, inlier ratio, coverage, and runtime.

---

# 69. Recommended First Reproducibility Milestone

The first reproducibility milestone should be intentionally small.

Use:

```text
One known source/reference pair
        ↓
SIFT baseline
        ↓
RANSAC
        ↓
Transformation
        ↓
Independent check points
        ↓
RMSE
        ↓
Runtime
        ↓
Saved artifacts
        ↓
Second execution
```

The project feedback identifies this one-pair end-to-end result as the first meaningful milestone before expanding to retrieval, stronger matchers, sub-pixel refinement, and additional sensors.

A benchmark that cannot reproduce one controlled pair should not be expanded to a large benchmark suite first.

---

# 70. Reproducibility Expansion Path

After the first reproducible pair:

```text
Milestone A
One known pair
        ↓
Milestone B
Scale + illumination
        ↓
Milestone C
Small retrieval database
        ↓
Milestone D
Stronger local matcher
        ↓
Milestone E
Sub-pixel refinement
        ↓
Milestone F
Additional sensors
```

This follows the project build sequence supplied in the technical feedback.

Each milestone should produce reproducible evidence before the next layer is introduced.

---

# 71. Reproducibility Report Template

Every published V1 benchmark result should be accompanied by a compact reproducibility record.

```yaml
reproducibility:
  benchmark_version: v1
  run_id: <run-id>

  code:
    commit: <full-sha>
    dirty: <true|false>

  data:
    dataset_version: <version>
    dataset_manifest: <path-or-id>
    ground_truth_version: <version>
    ground_truth_manifest: <path-or-id>

  configuration:
    config_version: <version>
    sensor: <sensor>
    matcher: <matcher>
    geometry_model: <model>
    subpixel_refinement: <enabled|disabled>

  environment:
    os: <value>
    python: <value>
    dependencies: <lockfile-or-manifest>
    cpu: <value>
    gpu: <value-or-null>
    cuda: <value-or-null>

  randomness:
    seed: <value-or-null>
    deterministic: <true|false|unknown>

  execution:
    command: <exact-command>
    runtime_s: <value>
    status: <success|failure>

  results:
    metrics: <path-or-id>
    artifacts: <path-or-id>

  reproduction:
    level: <0|1|2|3|4>
    deviations:
      - <value>
```

---

# 72. Final Reproducibility Standard

A ChandraMap V1 result should be considered reproducible only when another researcher can answer all of the following without guessing:

```text
Which code?
Which commit?
Which benchmark version?
Which dataset?
Which exact files?
Which checksums?
Which ground truth?
Which check points?
Which configuration?
Which preprocessing?
Which scale?
Which matcher?
Which geometric model?
Which RANSAC settings?
Which sub-pixel method?
Which random seed?
Which environment?
Which hardware?
Which command?
Which metrics?
Which failures?
Which artifacts?
Which tolerances?
```

If any of these materially affects the result and is unknown, it must be recorded as:

```text
Not specified
```

or:

```text
To be defined
```

rather than inferred after the fact.

---

# 73. Final Reproducibility Principle

ChandraMap V1 reproducibility is not simply:

```text
Run the code again.
```

It is:

```text
Same Code
+
Same Data
+
Same Ground Truth
+
Same Configuration
+
Same Preprocessing
+
Same Evaluation Procedure
+
Controlled Randomness
+
Recorded Environment
+
Recorded Hardware
+
Preserved Failures
+
Preserved Artifacts
+
Traceable Provenance
```

The benchmark must make it possible to distinguish:

```text
A new experiment
```

from:

```text
A reproduction of an existing experiment
```

and:

```text
A numerically consistent reproduction
```

from:

```text
A bitwise-identical execution
```

These are different claims and must not be conflated.

The central V1 principle is:

> **Build small, measure honestly, preserve the failures, and make every reported result traceable to the exact data, code, configuration, ground truth, environment, and evaluation procedure that produced it.**

The supplied project feedback consistently emphasizes this controlled approach: begin with a measurable known pair, preserve actual metrics and failure cases, separate fitting points from independent check points, use sensor-aware and multi-scale processing, and expand the benchmark only after the smaller experiment is reproducible.
