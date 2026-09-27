# `data/samples/`

> **ChandraMap sample-data documentation**
>
> This directory is intended to hold small, controlled data assets used to make ChandraMap workflows understandable, reproducible, and easier to develop. However, the currently available project documentation does **not** formally define an authoritative schema, fixed sample dataset, naming convention, or mandatory lifecycle for `data/samples/`. Where this document proposes practices, they are explicitly marked as **Recommended — not currently enforced**.

---

## 📌 Purpose

`data/samples/` is the repository location for **sample-level data associated with demonstrating or exercising ChandraMap workflows without treating those assets as the authoritative benchmark, ground-truth, or raw mission dataset**.

The broader ChandraMap project focuses on reliable lunar image correspondence and registration across differences in:

- sensor modality
- spatial scale
- illumination / Sun angle
- image resolution
- viewing geometry
- terrain appearance
- source/reference characteristics

The project feedback specifically recommends building small, measurable examples before scaling to larger datasets: one known source/reference pair should first pass through candidate matching, geometric verification, transformation estimation, registration, and independent error measurement.

The project also emphasizes that the primary scientific output is **reliable correspondence and registration**, while a mosaic or visual demonstration is downstream of that objective.

Therefore, sample data should support the project without being confused with the datasets used to establish scientific benchmark performance.

---

## 🎯 What `data/samples/` Means in ChandraMap

The exact formal definition of `data/samples/` is **not yet specified by the available repository/project documentation**.

Accordingly:

> **Repository status:** The repository does not yet define a formal, enforced purpose, schema, dataset inventory, or validation contract for `data/samples/`.

For repository organization, the recommended interpretation is:

> **Recommended — not currently enforced:** `data/samples/` should contain deliberately selected, small, understandable data examples that help developers and contributors exercise ChandraMap components and demonstrate expected workflow behavior.

A sample may therefore be useful for:

- development
- debugging
- pipeline demonstrations
- documentation examples
- reproducibility checks
- local workflow verification
- integration testing where appropriate

A sample should **not automatically be considered**:

- benchmark data
- ground truth
- an official evaluation split
- a scientific reference dataset
- a production dataset
- a complete lunar dataset

---

## 🔬 Why Samples Are Useful for ChandraMap

ChandraMap is intended to solve a difficult correspondence problem involving real lunar imagery with substantial differences between inputs.

The project feedback recommends starting with a small number of real overlapping regions rather than attempting to process the entire Moon immediately. One suggested first milestone is:

```text
Known source/reference pair
        ↓
Candidate matches
        ↓
RANSAC / geometric verification
        ↓
Verified inliers
        ↓
Sub-pixel refinement
        ↓
Final transformation
        ↓
Registered overlay
        ↓
Independent error measurement
```

This development strategy is explicitly intended to produce evidence from a small, measurable example before expanding the system.

`data/samples/` can therefore provide a controlled entry point into that workflow.

---

# 🧭 Relationship to the ChandraMap Data Pipeline

A sample is best understood as a **small, intentionally selected representation of data used by a workflow**, rather than as a replacement for the project's upstream data categories.

A conceptual relationship is:

```text
Raw / External Data
        │
        ▼
Interim / Preparation
        │
        ▼
Processed Data
        │
        ├──────────────► Samples
        │                   │
        │                   ├── Development
        │                   ├── Demonstration
        │                   ├── Debugging
        │                   └── Reproducibility checks
        │
        ▼
Benchmark / Evaluation Data
        │
        ▼
Ground Truth + Independent Evaluation
```

**Important:** this diagram describes a recommended organizational relationship. It does **not** establish that every sample must literally be generated from `data/processed/`, because the available project documentation does not define such a mandatory transformation.

---

# 📂 Samples vs Other Data Categories

Understanding the distinction between data directories is important because ChandraMap is a research and benchmarking project.

| Data category             | Primary role                                                 | Should automatically be treated as scientific evaluation data? |
| ------------------------- | ------------------------------------------------------------ | -------------------------------------------------------------: |
| `data/raw/`               | Original/raw project inputs                                  |                                                          ❌ No |
| `data/external/`          | External datasets or externally obtained resources           |                                                          ❌ No |
| `data/interim/`           | Intermediate processing/preparation artifacts                |                                                          ❌ No |
| `data/processed/`         | Data prepared for downstream processing                      |                                                          ❌ No |
| `data/samples/`           | Small selected examples for development/demo/reproducibility |                                                          ❌ No |
| `data/ground_truth/`      | Evaluation reference/control/check information               |                          ✅ Potentially, according to protocol |
| Benchmark datasets        | Controlled evaluation datasets/splits                        |                                                         ✅ Yes |
| Registration outputs      | Results produced by an algorithm                             |                                                          ❌ No |
| Candidate correspondences | Proposed matches before verification                         |                                                          ❌ No |
| RANSAC inliers            | Geometrically verified candidate correspondences             |                                           ❌ Not automatically |
| Estimated transformations | Models estimated by the pipeline                             |                                                          ❌ No |
| Final evaluation results  | Measurements against appropriate reference/check data        |                                           ✅ Evaluation result |

The distinction is particularly important because the ChandraMap evaluation design requires transformation fitting and evaluation to use appropriately separated information. The project feedback recommends using challenge ground truth where available, or independently checked check points that are **not used to fit the transformation**.

---

# 🧪 Intended Uses

Because the formal repository policy is not yet defined, the following classification is recommended.

## Development

**Recommended — not currently enforced**

Samples can provide a small input on which developers can rapidly iterate while implementing:

- image loading
- metadata handling
- preprocessing
- feature extraction
- candidate matching
- geometric verification
- registration
- visualization
- metric calculation

This is consistent with the project's recommendation to establish one end-to-end result before expanding the system.

---

## Debugging

**Recommended — not currently enforced**

A small sample can make failures easier to reproduce.

Examples of debugging targets include:

- incorrect image dimensions
- metadata parsing failures
- coordinate-order mistakes
- image normalization problems
- incorrect candidate matching
- RANSAC failures
- poor spatial distribution of matches
- transformation errors
- registration visualization problems

The project explicitly emphasizes preserving failures and examining where the pipeline breaks rather than hiding unsuccessful cases.

---

## Demonstrations

**Recommended — not currently enforced**

Samples may be used in:

- README demonstrations
- documentation
- presentations
- development screenshots
- pipeline walkthroughs
- example notebooks or scripts
- local demonstrations

A demonstration should clearly identify itself as an example and should not be presented as benchmark evidence unless it belongs to an officially defined evaluation set.

---

## Documentation

**Recommended — not currently enforced**

A sample may make a technical explanation substantially easier to understand.

For example:

```text
Sample input
     ↓
Candidate matches
     ↓
RANSAC inliers
     ↓
Transformation
     ↓
Registered result
```

The sample should make the workflow visible without implying that the example represents the statistical performance of the entire system.

---

## Reproducibility Checks

**Recommended — not currently enforced**

Small fixed samples can be useful for verifying that a code change has not unintentionally changed a pipeline stage.

For example:

```text
Fixed sample
     +
Fixed configuration
     +
Fixed environment
     ↓
Pipeline execution
     ↓
Recorded result
```

Such a use should preserve enough provenance to identify exactly which sample and configuration were used.

---

# ❌ What Samples Are Not

A file should not be classified as a sample merely because it is small.

### Samples are not automatically raw data

Raw data represents an upstream source asset.

A sample may be selected from raw data, but its classification should remain explicit.

---

### Samples are not automatically external data

An externally obtained lunar image remains external data according to its provenance even if it is small.

If a small external asset is copied into `data/samples/`, its original provenance must not be lost.

---

### Samples are not interim data

Interim data represents intermediate processing state.

A sample is defined by its **purpose and role**, not simply by the fact that it has been processed.

---

### Samples are not processed data

Processed data is prepared for downstream project processing.

A processed image can become a sample if deliberately selected for sample use, but the two concepts should not be treated as interchangeable.

---

### Samples are not ground truth

A sample image is not ground truth simply because the corresponding region is known.

Ground truth is an evaluation asset used to establish the expected or independently verified correspondence/reference needed for scientific evaluation.

The ChandraMap evaluation guidance explicitly distinguishes transformation-fitting points from independently checked evaluation points.

---

### Samples are not benchmark datasets

A sample should not be reported as part of benchmark performance unless it belongs to a formally defined benchmark dataset or evaluation split.

This prevents a small demonstration example from being mistaken for representative scientific evidence.

---

### Samples are not candidate matches

Candidate correspondences are outputs proposed by a matching system.

The project feedback specifically recommends calling these **candidate matches** and allowing RANSAC/geometric verification to determine which candidates become verified inliers.

---

### Samples are not RANSAC inliers

RANSAC inliers are algorithmic outputs, not source-data samples.

They should be stored and documented according to the project's result/output conventions rather than being silently classified as sample data.

---

### Samples are not estimated transformations

A transformation estimated from correspondences is an algorithm output.

It should not be stored as though it were an input sample.

---

# 🌙 ChandraMap-Specific Sample Characteristics

The project involves multiple lunar imaging conditions and sensors.

The available project feedback identifies:

- OHRC
- TMC-2
- IIRS
- LRO reference imagery

as relevant imaging sources/paths, while emphasizing that these should not automatically be treated as identical image types.

The sample-data policy should therefore preserve the context of a sample whenever that information is available.

Useful contextual information may include:

- source/reference relationship
- sensor or product identity
- image dimensions
- pixel scale / GSD
- footprint
- map projection
- viewing geometry
- lighting/Sun-angle information
- preprocessing state
- provenance
- relationship to a benchmark or evaluation case

However:

> **The repository currently does not define a mandatory sample metadata schema.**

Any schema proposed for these fields is therefore **Recommended — not currently enforced**.

---

# ☀️ Illumination and Scale

Sample data should not hide the central difficulties of the ChandraMap problem.

The project specifically identifies:

- Sun-angle changes
- scale differences
- sensor modality
- spatial resolution
- geometry

as important sources of difficulty.

A sample collection may therefore eventually contain examples representing different controlled conditions.

**Recommended — not currently enforced:**

```text
Sample condition
├── Similar illumination
├── Different Sun angle
├── Moderate scale difference
├── Large scale difference
├── Cross-modality case
├── Geometry / relief challenge
└── Low-feature terrain
```

These categories correspond to the stress-test concepts described in the project feedback.

They should not be interpreted as an officially implemented `data/samples/` taxonomy unless the repository explicitly adopts it.

---

# 🔬 Scientific Evaluation Policy

## Can samples be used for scientific evaluation?

**Not automatically.**

A sample may be used for evaluation only when its role, provenance, reference information, and evaluation protocol are explicitly defined.

A sample used solely for demonstration should not be presented as evidence of general system performance.

For scientific evaluation, the project should distinguish:

```text
Demonstration sample
        ≠
Development sample
        ≠
Benchmark evaluation case
        ≠
Ground-truth evaluation asset
```

The distinction matters because the project requires measurable evaluation using quantities such as:

- check-point RMSE
- inlier count
- inlier ratio
- spatial coverage
- ground error where meaningful
- runtime
- failure rate
- retrieval Recall@K where retrieval is used

---

# 📊 Can Samples Be Used for Benchmark Evaluation?

Only if explicitly included in the benchmark definition.

A sample directory should not silently become a benchmark dataset.

A benchmark case should have a documented relationship to:

- the benchmark specification
- evaluation inputs
- ground truth/reference information
- metrics
- evaluation procedure
- reproducibility requirements

If a sample is also used by a benchmark, that dual role should be explicitly documented to avoid ambiguity.

---

# 🚨 Data Leakage Considerations

Sample data can create leakage risks if it overlaps with evaluation data.

Potential problems include:

```text
Training / development sample
        │
        ├── same image
        ├── same geographic region
        ├── same source/reference pair
        └── near-duplicate derivative
                 │
                 ▼
          Benchmark evaluation
```

This can make a benchmark result less informative because the system may have effectively encountered the evaluation case during development.

### Recommended policy

**Recommended — not currently enforced:**

- Do not assume that a sample is safe for benchmark evaluation.
- Track whether a sample overlaps with an evaluation case.
- Avoid using benchmark check points for development tuning.
- Do not use independent check points to fit a transformation.
- Preserve benchmark/evaluation boundaries.
- Document any intentional reuse.
- Do not claim benchmark generalization from a sample-only result.

The separation between fitting points and independent check points is explicitly emphasized in the project evaluation guidance.

---

# 🧾 Provenance

Every sample should have traceable provenance whenever practical.

At minimum, contributors should be able to determine:

```text
Sample
  │
  ├── Source
  ├── Origin
  ├── Selection reason
  ├── Processing state
  ├── Relevant metadata
  ├── Version
  └── Intended use
```

### Recommended — not currently enforced

A sample provenance record should identify, where applicable:

| Field                         | Status                      |
| ----------------------------- | --------------------------- |
| Source dataset                | Recommended                 |
| Original asset identifier     | Recommended                 |
| External provider/mission     | Recommended when applicable |
| Source/reference relationship | Recommended                 |
| Processing history            | Recommended                 |
| Selection rationale           | Recommended                 |
| Sample version                | Recommended                 |
| Checksum/hash                 | Recommended                 |
| License/usage constraints     | Recommended                 |
| Benchmark relationship        | Recommended                 |
| Ground-truth relationship     | Recommended                 |

The project feedback recommends preserving product metadata such as image dimensions, product type, pixel scale/GSD, and available geolocation/map-projection information when working with real image pairs.

---

# 🏷️ Naming

The repository currently does **not** specify an authoritative sample naming convention.

Therefore:

> **Sample naming convention: Not yet defined.**

### Recommended — not currently enforced

Names should be:

- deterministic
- descriptive
- stable
- machine-friendly
- free from unnecessary personal-machine paths
- traceable to provenance where appropriate

Avoid names such as:

```text
test1.png
new.png
final.png
final2.png
latest.png
image_fixed_really_final.tif
```

Prefer names whose meaning can be understood without opening the file.

A project-specific naming scheme should only be adopted after the repository defines the required fields.

---

# 🔢 Versioning

The repository currently does **not** specify a formal sample-data versioning scheme.

Therefore:

> **Sample versioning: Not yet defined.**

### Recommended — not currently enforced

When samples change materially, record:

- what changed
- why it changed
- source-data change, if applicable
- preprocessing change, if applicable
- metadata change, if applicable
- compatibility implications
- benchmark implications

Do not silently replace an example that is referenced by documentation or reproducibility instructions.

---

# ✅ Validation

Sample validation should establish that the sample is usable for its stated purpose.

### Recommended validation checks

```text
File exists
   ↓
File can be read
   ↓
Expected metadata is available
   ↓
Image/content is valid
   ↓
Provenance is known
   ↓
Intended workflow accepts it
   ↓
Expected demonstration/development behavior is reproducible
```

For image-registration samples, additional checks may include:

- correct source/reference identification
- valid dimensions
- valid coordinate metadata when applicable
- valid pixel-scale information when available
- no accidental corruption
- no accidental duplicate of an evaluation case
- documented preprocessing state

The project does not currently provide an authoritative automated validation specification for `data/samples/`.

---

# 📦 Storage Policy

The repository currently does not specify exactly which sample file formats, maximum sizes, or storage mechanisms are mandatory.

### Recommended — not currently enforced

Small, stable, legally redistributable sample assets may be committed when they are genuinely required for:

- documentation
- examples
- reproducibility
- lightweight development
- deterministic tests

Large lunar imagery should **not** be committed merely because it is convenient.

Large or externally hosted data should follow the project's eventual data-acquisition/storage policy rather than being copied into Git without justification.

---

# 🌐 External Data and Licensing

A sample derived from external lunar data remains subject to the provenance and usage conditions of its source.

The project materials identify external lunar data sources and reference imagery as possible project inputs, including Chandrayaan-2 products and LRO imagery.

However, the available project documentation does **not** establish a complete licensing policy for `data/samples/`.

Therefore:

> **License status for individual samples: Must be verified from the actual source before committing.**

Do not assume that because data is publicly accessible it can automatically be redistributed inside the repository.

---

# 🗃️ Git Commit Policy

## Appropriate for Git

**Recommended — not currently enforced:**

Commit small sample assets when they are:

- necessary for documentation
- small enough for normal Git usage
- legally redistributable
- stable
- reproducible
- clearly documented
- useful to contributors

---

## Avoid committing

**Recommended — not currently enforced:**

Avoid committing:

- large raw lunar datasets
- entire mission archives
- temporary processing outputs
- generated caches
- local experiment artifacts
- machine-specific files
- credentials or secrets
- undocumented external data
- duplicate copies of benchmark datasets
- outputs that can be deterministically regenerated

---

# 🔁 Reproducibility

Samples can improve reproducibility when they remain stable and traceable.

A reproducible sample workflow should make it possible to identify:

```text
Sample
+
Code version
+
Configuration
+
Environment
+
Processing procedure
        ↓
Reproducible execution
        ↓
Comparable result
```

The project feedback recommends saving concrete evidence from early end-to-end experiments, including:

- match plots
- rejected outliers
- registered overlays
- inlier statistics
- check-point error

for a known pair.

Such outputs should not automatically be placed in `data/samples/`; the correct repository location should follow the project's result/artifact organization.

---

# 🧪 Samples and Testing

The exact relationship between `data/samples/` and automated tests is:

> **Not specified.**

A sample can be useful as a test fixture, but a sample should not automatically be considered a test fixture.

### Recommended distinction

```text
data/samples/
    ↓
Human-readable / workflow examples

tests/
    ↓
Automated test logic

Benchmark datasets
    ↓
Scientific evaluation

data/ground_truth/
    ↓
Evaluation reference/control information
```

If the project later establishes a dedicated fixture directory, test-only assets should generally follow that convention rather than accumulating indefinitely in `data/samples/`.

---

# 🧠 Samples and Benchmark Baselines

ChandraMap includes baseline concepts such as a SIFT-based pipeline:

```text
SIFT
 ↓
Descriptor matching
 ↓
Ratio / cross-check filtering
 ↓
RANSAC
 ↓
Affine / homography
 ↓
Residual error
```

This baseline is described as a simple, explainable starting point for the project.

A sample can be used to demonstrate this baseline, but:

> **Demonstrating a baseline on a sample is not equivalent to establishing benchmark performance.**

Benchmark claims require the project's defined evaluation data and evaluation procedure.

---

# 📐 Spatial Coverage Matters

A visually convincing sample result is not sufficient evidence of reliable registration.

The project explicitly identifies spatial distribution of good correspondences as an evaluation concern. Coverage can be represented using approaches such as grid coverage or convex-hull coverage.

Therefore, a sample demonstration should avoid implying that:

```text
More matches = Better registration
```

The actual quality depends on whether the correspondences are geometrically correct and appropriately distributed.

---

# 🎯 Sample Selection

The repository currently does not define an official sample-selection procedure.

### Recommended — not currently enforced

When creating samples for development or documentation, prefer cases that expose meaningful parts of the pipeline rather than arbitrary images.

Potential categories include:

| Sample category      | Purpose                           |
| -------------------- | --------------------------------- |
| Known overlap        | Basic end-to-end demonstration    |
| Scale difference     | Multi-scale behavior              |
| Sun-angle difference | Illumination robustness           |
| Cross-sensor case    | Sensor-aware processing           |
| Geometry challenge   | Transformation robustness         |
| Low-feature terrain  | Failure analysis                  |
| Simple baseline case | Debugging and regression checking |

These categories are derived from the project's proposed stress-test structure, not an existing mandatory sample taxonomy.

---

# 🚧 Known Limitations

The current project materials leave several aspects of `data/samples/` undefined.

| Area                                      | Current status             |
| ----------------------------------------- | -------------------------- |
| Formal purpose of `data/samples/`         | **Not formally specified** |
| Official sample inventory                 | **Not confirmed**          |
| Required sample schema                    | **Not specified**          |
| Required metadata fields                  | **Not specified**          |
| Official naming convention                | **Not defined**            |
| Official versioning scheme                | **Not defined**            |
| Automated validation                      | **Not specified**          |
| Maximum sample size                       | **Not specified**          |
| Required file formats                     | **Not specified**          |
| Official train/development/sample split   | **Not specified**          |
| Official sample/benchmark relationship    | **Not specified**          |
| Official sample/ground-truth relationship | **Not specified**          |
| Sample licensing policy                   | **Not fully specified**    |
| Dedicated sample-generation pipeline      | **Not confirmed**          |

These gaps should be resolved through repository documentation rather than being silently filled with assumptions.

---

# 📌 Current Implementation Status

| Capability                              | Status                                      |
| --------------------------------------- | ------------------------------------------- |
| `data/samples/` documentation           | **Documented by this file**                 |
| Formal repository definition of samples | **Not yet specified**                       |
| Sample dataset inventory                | **Not confirmed**                           |
| Mandatory sample metadata schema        | **Not specified**                           |
| Mandatory naming convention             | **Not specified**                           |
| Mandatory validation pipeline           | **Not specified**                           |
| Formal benchmark role                   | **Not specified**                           |
| Formal scientific-evaluation role       | **Not specified**                           |
| Recommended provenance practices        | **Documented here; not currently enforced** |
| Recommended leakage controls            | **Documented here; not currently enforced** |
| Recommended Git policy                  | **Documented here; not currently enforced** |

---

# 👥 Contributor Workflow

When adding a new sample, contributors should follow this workflow.

## 1. Establish the purpose

Ask:

> Why does this sample need to exist?

Possible answers include:

- documentation
- development
- debugging
- reproducibility
- demonstration
- controlled testing

If the answer is only “because it is a useful image,” the sample probably needs better documentation.

---

## 2. Identify provenance

Record where the data came from.

```text
Source
  ↓
Original asset
  ↓
Selection
  ↓
Processing
  ↓
Sample
```

Do not remove provenance simply to make the sample easier to use.

---

## 3. Determine its evaluation status

Explicitly establish whether the sample is:

- development-only
- demonstration-only
- test fixture
- benchmark-related
- ground-truth-related

If unknown:

> **Status: Not specified**

Do not label it as benchmark data by assumption.

---

## 4. Check for leakage

Before committing the sample, determine whether it overlaps with known benchmark or evaluation cases.

If this is unknown:

> **Benchmark overlap status: Not confirmed**

---

## 5. Validate the asset

Confirm that:

- the file can be read
- provenance is available
- the intended workflow accepts it
- required metadata is preserved
- the sample is not corrupted
- licensing/redistribution requirements are satisfied

---

## 6. Document the change

If an existing sample is replaced or materially modified, record the reason.

Avoid silently changing an asset that another contributor may rely on.

---

# 🧾 Recommended Sample Metadata

The project does not currently define an official schema.

The following is therefore:

> **Recommended — not currently enforced**

A future sample manifest could conceptually record:

```yaml
sample:
  id: <stable identifier>
  purpose: <development | demonstration | debugging | testing>

source:
  origin: <source dataset or provider>
  original_id: <original asset identifier>

context:
  source_product: <if applicable>
  reference_product: <if applicable>
  pixel_scale: <if available>
  projection: <if available>
  footprint: <if available>
  illumination: <if available>

processing:
  status: <raw | processed | derived>
  description: <brief description>

evaluation:
  benchmark_case: <yes | no | unknown>
  ground_truth: <yes | no | unknown>

provenance:
  version: <sample version>
  checksum: <optional integrity value>
```

This is an **illustrative proposal only**. It is not an existing ChandraMap schema.

---

# 🔐 Data Integrity

For reproducible research, a sample should remain stable once referenced by documentation or an experiment.

### Recommended — not currently enforced

Where practical:

- use stable identifiers
- record versions
- record checksums for important assets
- avoid silent replacement
- preserve provenance
- document transformations
- distinguish regenerated assets from source assets

The exact integrity mechanism for ChandraMap samples is currently **not specified**.

---

# 📈 Samples and Experimental Results

Sample inputs and experimental results should remain conceptually separate.

```text
Sample
  ↓
Pipeline execution
  ↓
Candidate correspondences
  ↓
Verified inliers
  ↓
Transformation
  ↓
Registration
  ↓
Metrics / Results
```

The sample is an **input asset**.

The output of the pipeline is an **experimental result**.

Do not overwrite the sample with generated outputs.

---

# 🧭 Scientific Reporting

When a result is produced using a sample, documentation should identify the sample's role.

For example:

```text
Input:
    Sample / demonstration case

Purpose:
    Development verification

Result:
    Successful registration on the selected case

Scientific benchmark status:
    Not a benchmark result
```

This prevents a successful example from being interpreted as evidence of general performance.

The project feedback explicitly recommends reporting actual measurements rather than decorative confidence percentages or star ratings.

---

# 🧪 Recommended Sample Quality Checklist

Before committing a sample:

### Purpose

- [ ] Is the purpose clearly defined?
- [ ] Is it actually needed?
- [ ] Is it distinguished from benchmark data?

### Provenance

- [ ] Is the source known?
- [ ] Is the original asset identifiable?
- [ ] Is processing history known?
- [ ] Are applicable usage restrictions known?

### Technical validity

- [ ] Can the file be opened?
- [ ] Is the data intact?
- [ ] Is relevant metadata preserved?
- [ ] Is the processing state clear?

### Evaluation safety

- [ ] Has benchmark overlap been considered?
- [ ] Is ground-truth status clear?
- [ ] Is development/evaluation separation preserved?

### Git hygiene

- [ ] Is the asset reasonably sized?
- [ ] Is committing it appropriate?
- [ ] Is it reproducible or properly documented?
- [ ] Does it avoid temporary/generated artifacts?

---

# 🚫 Anti-Patterns

Avoid these patterns:

### ❌ Calling every small image a sample

Size alone does not determine data role.

### ❌ Treating samples as benchmark evidence

A demonstration result is not automatically a benchmark result.

### ❌ Removing provenance

A sample without source information becomes difficult to reproduce or trust.

### ❌ Copying large datasets into the directory

`data/samples/` should not become an uncontrolled data archive.

### ❌ Using evaluation cases for tuning without documentation

This can introduce leakage.

### ❌ Replacing samples silently

Stable examples are important for reproducibility.

### ❌ Treating RANSAC inliers as ground truth

Geometric verification output is not automatically independent truth.

### ❌ Reporting sample performance as system-wide performance

One successful pair does not characterize the complete ChandraMap system.

---

# 🔗 Relationship to Ground Truth

Ground truth has a different scientific role from sample data.

The ChandraMap evaluation guidance states that transformations should not be fitted and judged using exactly the same points. Challenge ground truth should be used where available; otherwise independently checked tie points should be retained as check points and excluded from transformation fitting.

Therefore:

```text
Sample
  │
  ├── may contain an image/example
  │
  └── does not automatically provide truth

Ground Truth
  │
  ├── provides evaluation reference/control information
  │
  └── supports scientific measurement
```

A sample may be associated with ground-truth information, but the two roles should remain explicitly distinguishable.

---

# 🧩 Relationship to the V1 Benchmark

The project direction emphasizes controlled benchmarking rather than relying on isolated visual examples.

The recommended evaluation cases include:

- easy pair
- Sun-angle stress
- scale stress
- modality stress
- geometry stress
- low-feature terrain

with metrics including RMSE, inlier statistics, spatial coverage, ground error where meaningful, success rate, and runtime.

Therefore:

> `data/samples/` should not be assumed to be the V1 benchmark dataset.

If a sample later becomes part of V1 benchmarking, that relationship should be explicitly declared in the benchmark documentation.

---

# 🛠️ Future Improvements

The following are reasonable future repository improvements, but they are **not currently enforced**:

1. Define an official purpose for `data/samples/`.
2. Define whether samples are primarily examples, development fixtures, or both.
3. Establish a sample naming convention.
4. Establish a metadata/provenance schema.
5. Define sample versioning.
6. Define validation rules.
7. Define Git size/storage limits.
8. Define benchmark-overlap rules.
9. Define licensing requirements.
10. Define whether automated tests may depend on sample assets.
11. Establish a documented sample inventory.
12. Connect sample identifiers to reproducibility records where appropriate.

---

# 📋 Quick Reference

| Question                                  | Answer                                                                                                                       |
| ----------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| What is `data/samples/`?                  | A repository location for small, deliberately selected example/sample assets; exact formal purpose is **not yet specified**. |
| Is it raw data?                           | No, not by directory role.                                                                                                   |
| Is it external data?                      | Not necessarily. Provenance determines origin.                                                                               |
| Is it processed data?                     | Not automatically.                                                                                                           |
| Is it ground truth?                       | No, not automatically.                                                                                                       |
| Is it benchmark data?                     | No, not automatically.                                                                                                       |
| Can it support development?               | **Recommended**                                                                                                              |
| Can it support debugging?                 | **Recommended**                                                                                                              |
| Can it support demonstrations?            | **Recommended**                                                                                                              |
| Can it support reproducibility checks?    | **Recommended**                                                                                                              |
| Can it prove benchmark performance?       | **No, unless explicitly included in the benchmark definition.**                                                              |
| Can it be used for scientific evaluation? | Only when explicitly defined and appropriately validated.                                                                    |
| Is there an official naming scheme?       | **Not specified.**                                                                                                           |
| Is there an official metadata schema?     | **Not specified.**                                                                                                           |
| Is there an official versioning scheme?   | **Not specified.**                                                                                                           |
| Are proposed practices mandatory?         | **No.**                                                                                                                      |

---

# 🔄 Conceptual Lifecycle

```text
SOURCE DATA
    │
    ├── Raw mission/source data
    └── External/reference data
            │
            ▼
    PREPARATION / PROCESSING
            │
            ▼
      SELECTED SAMPLE
            │
            ├──────────────► Development
            │
            ├──────────────► Debugging
            │
            ├──────────────► Demonstration
            │
            ├──────────────► Reproducibility
            │
            └──────────────► Controlled testing

                     [Separate scientific path]

                Benchmark Dataset
                        │
                        ▼
                 Ground Truth
                        │
                        ▼
             Independent Evaluation
```

The separation is intentional: **a convenient sample is not automatically scientific ground truth or benchmark evidence.**

---

# 🧠 Core Principle

> **Keep samples small, understandable, traceable, reproducible, and honest about their role.**

For ChandraMap, the most important distinction is:

```text
Sample ≠ Ground Truth
Sample ≠ Benchmark
Sample ≠ Result
Sample ≠ Candidate Match
Sample ≠ Transformation
```

A sample is useful because it allows the project to demonstrate and develop a measurable workflow. The scientific credibility of ChandraMap, however, comes from controlled evaluation, appropriate ground truth/check points, measurable registration error, spatially distributed correspondences, and reproducible experiments—not from the existence of example images alone. The project feedback explicitly recommends building one measurable end-to-end result first and then expanding toward scale, illumination, retrieval, stronger matching, sub-pixel refinement, and additional sensors.

---

## 📌 Status of This Document

**Documentation status:** Documented
**Formal `data/samples/` repository policy:** Not yet specified
**Sample schema:** Not specified
**Sample inventory:** Not confirmed
**Mandatory workflow:** Not specified
**Recommended practices in this document:** Not currently enforced
