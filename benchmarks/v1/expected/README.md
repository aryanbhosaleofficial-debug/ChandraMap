# Expected Baseline

> **ChandraMap — Benchmark Reference Component**
> **Path:** `benchmarks/baselines/expected/`

This directory is part of the ChandraMap benchmark architecture for **SIH 26166 — Multi-modal, Sun angle and scale invariant image correspondence using Chandrayaan-2 optical images**.

The benchmark evaluates lunar image correspondence and registration across differences in scale, illumination, sensor characteristics, spatial resolution, and geometric conditions.

---

## 1. Purpose

`benchmarks/baselines/expected/` is reserved for the benchmark's **expected/reference baseline component**.

However, the supplied project materials do **not currently define precisely what the repository means by `expected`**.

In particular, the available project material does not establish whether this directory represents:

- expected numerical results,
- reference registration outputs,
- expected transformations,
- an oracle/reference solution,
- validation artifacts,
- benchmark target outputs,
- a reference implementation,
- or another benchmark-specific concept.

Therefore:

> **Expected baseline role: Not specified / Not confirmed by the supplied project data.**

This README deliberately does not redefine `expected` as ground truth, an algorithm, or a machine-learning model.

The directory should only acquire a more specific semantic meaning when that meaning is explicitly defined by the benchmark specification or repository implementation.

---

## 2. Why This Directory Exists

The benchmark architecture needs a controlled place for reference or expected information if such information is required by the evaluation system.

Separating this component from algorithmic baselines prevents an important ambiguity:

```text
Algorithm Baseline
        │
        │ produces
        ▼
Prediction / Correspondence / Registration Result
        │
        │ evaluated by benchmark protocol
        ▼
Benchmark Evaluation
        │
        ├── Ground Truth
        └── Expected / Reference Component
             (only if explicitly defined)
```

The exact role of the final `expected` component remains **To be defined**.

Until that definition exists, this directory should not be treated as an authoritative source of benchmark truth.

---

## 3. Current Definition Status

| Item                               | Status                                |
| ---------------------------------- | ------------------------------------- |
| Directory                          | `benchmarks/baselines/expected/`      |
| Component type                     | Expected/reference baseline component |
| Exact semantic definition          | **Not specified**                     |
| Machine-learning model             | **Not confirmed**                     |
| Feature-matching algorithm         | **Not confirmed**                     |
| Registration algorithm             | **Not confirmed**                     |
| Expected numerical results         | **Not confirmed**                     |
| Expected transformation parameters | **Not confirmed**                     |
| Reference registration outputs     | **Not confirmed**                     |
| Oracle/reference implementation    | **Not confirmed**                     |
| Ground truth                       | **Not automatically equivalent**      |
| V1 benchmark role                  | **To be defined**                     |
| Output schema                      | **Not specified**                     |
| File naming convention             | **Not specified**                     |
| Generation procedure               | **Not specified**                     |
| Validation procedure               | **Not specified**                     |
| Versioning scheme                  | **Not specified**                     |
| Reproduction command               | **Not specified**                     |

This status is intentional. Benchmark documentation should distinguish an explicitly defined artifact from an assumed one.

---

## 4. Scope

This README documents the role and boundaries of the `expected` component.

It does **not** define a new benchmark protocol.

The benchmark protocol remains governed by the V1 benchmark documentation, including:

- [`benchmarks/v1/README.md`](../../v1/README.md)
- [`benchmarks/v1/BENCHMARK_SPEC.md`](../../v1/BENCHMARK_SPEC.md)
- [`benchmarks/v1/GROUND_TRUTH_PROTOCOL.md`](../../v1/GROUND_TRUTH_PROTOCOL.md)
- [`benchmarks/v1/REPRODUCIBILITY.md`](../../v1/REPRODUCIBILITY.md)

Where those documents provide an authoritative definition, this README should follow them.

If those documents do not define the expected-baseline semantics, this README should not invent them.

---

## 5. Expected Baseline vs. Ground Truth

### Important distinction

`Expected` must not automatically be interpreted as:

> `Ground Truth`

The two concepts serve different possible purposes in a benchmark architecture.

A simplified distinction is:

```text
Ground Truth
    │
    └── Defines what the benchmark considers correct
            │
            ▼
      Evaluation Protocol
            │
            ▼
     Algorithm Result
```

An expected/reference component could instead represent a reproducible reference output or another benchmark-controlled artifact.

The supplied project materials do not currently establish which relationship ChandraMap uses.

Therefore:

> **Relationship to ground truth: Not specified.**

The authoritative definition should be taken from:

[`benchmarks/v1/GROUND_TRUTH_PROTOCOL.md`](../../v1/GROUND_TRUTH_PROTOCOL.md)

when that relationship is explicitly documented there.

### Do not make this assumption

Do not automatically implement:

```text
expected/
└── ground_truth/
```

or copy ground-truth files into this directory merely because the directory is named `expected`.

Ground-truth data should remain governed by the project's ground-truth protocol and versioning rules.

---

## 6. Expected Baseline vs. Algorithmic Baselines

The expected component should also remain separate from algorithmic baselines.

For example, the project materials identify **SIFT** as the first serious accuracy baseline for local image matching.

The feedback describes the baseline path conceptually as:

```text
SIFT
  ↓
Descriptor Matching
  ↓
Ratio / Cross-Check Filtering
  ↓
RANSAC
  ↓
Affine / Homography
  ↓
Residual Error
```

SIFT is therefore an **algorithmic matching baseline**.

`expected` should not be described as another matching algorithm unless the repository explicitly defines it that way.

A benchmark comparison may conceptually look like:

```text
                 ┌──────────────────────┐
                 │ Algorithm Baseline   │
                 │ e.g. SIFT            │
                 └──────────┬───────────┘
                            │
                            ▼
                   Predicted Result
                            │
                            ▼
                 ┌──────────────────────┐
                 │ Benchmark Evaluation │
                 └──────────┬───────────┘
                            │
              ┌─────────────┴─────────────┐
              ▼                           ▼
        Ground Truth              Expected / Reference
        if defined                 if defined
```

The exact implementation of the final branch is **Not specified**.

---

## 7. Relationship to the V1 Benchmark

The V1 benchmark is intended to evaluate actual lunar correspondence and registration behavior rather than merely produce visually convincing overlays.

The supplied technical feedback identifies important evaluation quantities including:

- check-point RMSE in source-image pixels,
- inlier count,
- inlier ratio,
- spatial coverage,
- ground error in metres when meaningful,
- runtime,
- failure rate,
- and, when retrieval is evaluated, Recall@1 / Recall@5.

The same benchmark cases should be used when comparing alternative methods.

The expected component may participate in this process only according to its eventual repository definition.

### Current status

| V1 relationship                     | Status        |
| ----------------------------------- | ------------- |
| Used during evaluation              | Not confirmed |
| Provides reference numerical values | Not confirmed |
| Provides reference transformations  | Not confirmed |
| Provides expected registered images | Not confirmed |
| Provides comparison artifacts       | Not confirmed |
| Used as an oracle                   | Not confirmed |
| Used with ground truth              | Not specified |

---

## 8. Inputs

No authoritative input schema for the expected component is currently specified.

Depending on its eventual definition, inputs could include benchmark artifacts such as:

- source image,
- reference image,
- image-pair identifier,
- dataset manifest,
- ground-truth identifier,
- sensor metadata,
- image dimensions,
- GSD / pixel scale,
- map projection,
- footprint,
- viewing geometry,
- illumination metadata,
- benchmark configuration,
- preprocessing configuration,
- algorithm output,
- or other benchmark metadata.

These are **possible benchmark inputs, not a confirmed schema for `expected/`**.

The repository should not require any of these files in this directory until the implementation or benchmark specification defines them.

---

## 9. Outputs

The exact output contract is also:

> **Not specified.**

Possible output categories in the wider ChandraMap benchmark architecture include:

- matched points,
- verified inliers,
- transformation parameters,
- residuals,
- registered images,
- check-point errors,
- metric summaries,
- retrieval candidates,
- runtime measurements,
- failure status,
- or reference values.

The project feedback identifies interpretable outputs such as match points, transformations, residuals, inlier statistics, coverage, and registered previews as useful benchmark artifacts.

However, this does **not** establish that all of them belong inside `benchmarks/baselines/expected/`.

---

## 10. Metrics

The expected component does not currently have an independently defined metric set.

Metrics relevant to the surrounding benchmark include:

| Metric           | Purpose                                                               |
| ---------------- | --------------------------------------------------------------------- |
| Recall@1         | Retrieval performance when global retrieval is evaluated              |
| Recall@5         | Retrieval performance when global retrieval is evaluated              |
| Inlier count     | Number of geometrically verified correspondences                      |
| Inlier ratio     | Fraction of candidate matches surviving geometric verification        |
| Spatial coverage | Distribution of verified matches over the overlap                     |
| Check-point RMSE | Registration accuracy on points not used to fit the transformation    |
| Ground error     | Physical error when GSD/projection/reference truth make it meaningful |
| Runtime          | Computational cost under the defined evaluation boundary              |
| Failure rate     | Reliability across benchmark cases                                    |

The feedback specifically recommends evaluating registration using independently checked points rather than fitting and judging on exactly the same points.

This distinction must remain intact if an expected/reference artifact is eventually introduced.

---

## 11. Geometry and Registration Context

ChandraMap's registration workflow is expected to distinguish candidate correspondences from geometrically verified correspondences.

The documented conceptual sequence is:

```text
LOCAL MATCHES
      │
      ▼
RANSAC + INITIAL MODEL
      │
      ▼
VERIFIED INLIERS
      │
      ▼
SUB-PIXEL REFINEMENT
      │
      ▼
FINAL TRANSFORMATION
      │
      ▼
REGISTERED IMAGE
```

The supplied feedback specifically recommends refining verified inliers and then refitting the final transformation.

A flexible warp must not be used to hide poor correspondences.

Residual behavior should also be inspected spatially because lunar terrain is not necessarily well represented by one global transformation.

These principles apply to benchmark evaluation generally. They do not establish that `expected/` stores any particular transformation.

---

## 12. Sensor Context

ChandraMap is explicitly concerned with multiple lunar imaging conditions and sensor characteristics.

The project materials distinguish:

- **OHRC**
- **TMC-2**
- **IIRS**
- lunar reference imagery such as **LRO NAC**, where applicable.

These sensors should not automatically be treated as identical image sources.

The feedback emphasizes preserving metadata such as:

- image dimensions,
- product type,
- pixel scale / GSD,
- footprint,
- map projection,
- viewing geometry,
- illumination information.

The expected component, if eventually defined as a stored reference artifact, should preserve the sensor context required to interpret that artifact.

However:

> **Expected-baseline sensor schema: Not specified.**

---

## 13. Scale Handling

The benchmark architecture explicitly treats scale as a physical imaging issue rather than simply a pixel-count problem.

The supplied project feedback recommends:

```text
Higher-resolution reference
          │
          ▼
Reference pyramid / downsampling
          │
          ▼
Comparable effective ground scale
          │
          ▼
Coarse correspondence
          │
          ▼
Fine refinement where justified
```

Upsampling a low-resolution sensor does not recover missing spatial information.

This is particularly important for cross-sensor evaluation involving large differences in GSD.

If the expected component eventually stores reference outputs, its scale information must be retained wherever that information is necessary to interpret the output.

The exact expected-output scale policy is:

> **Not specified.**

---

## 14. Illumination and Sun-Angle Conditions

Lunar images of the same region can change substantially with Sun angle because terrain shadows change.

The project feedback recommends testing illumination explicitly rather than treating generic brightness normalization as proof of illumination invariance.

Relevant benchmark cases include:

- similar illumination,
- substantially different illumination,
- shadow changes,
- structure-focused representations,
- and difficult terrain conditions.

The expected component should not be used to conceal these differences.

If expected/reference outputs are eventually defined, their illumination metadata and benchmark case identity should be retained where necessary.

---

## 15. Stress-Test Context

The project feedback proposes a controlled stress-test matrix:

| Stress case         | Purpose                                                                |
| ------------------- | ---------------------------------------------------------------------- |
| Easy pair           | Demonstrate end-to-end correspondence and registration                 |
| Sun-angle stress    | Measure robustness to changing shadows                                 |
| Scale stress        | Measure behavior under large GSD differences                           |
| Modality stress     | Evaluate sensor-aware handling, including IIRS-derived representations |
| Geometry stress     | Evaluate transformation and refinement robustness                      |
| Low-feature terrain | Expose false-match and weak-correspondence behavior                    |

The expected component's participation in these cases is:

> **Not specified.**

If it becomes a reference-output component, each stored reference artifact should be associated with a stable benchmark case identifier rather than being treated as an anonymous expected file.

---

## 16. Reproducibility

Any expected/reference artifact used by the benchmark must eventually be reproducible or traceable according to the project's reproducibility rules.

At minimum, a reproducible benchmark artifact should be associated with the information required to identify:

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
```

The exact reproducibility requirements for this directory are:

> **Not specified.**

See:

[`benchmarks/v1/REPRODUCIBILITY.md`](../../v1/REPRODUCIBILITY.md)

for the benchmark-level reproducibility contract.

---

## 17. Versioning

The expected component should not be versioned independently from the benchmark without an explicit versioning policy.

Potential version dimensions include:

```text
Benchmark version
        │
        ├── Dataset version
        ├── Ground-truth version
        ├── Expected/reference version
        ├── Configuration version
        └── Implementation version
```

The repository currently does not provide a confirmed schema for:

- expected-baseline version identifiers,
- expected-output manifests,
- artifact hashes,
- generation timestamps,
- or compatibility declarations.

Therefore:

> **Expected-baseline versioning: To be defined.**

If reference artifacts are introduced later, changing them should be treated as a benchmark-relevant change rather than an undocumented file replacement.

---

## 18. Provenance

Any future expected/reference artifact should have enough provenance to answer:

1. What benchmark case does it belong to?
2. Which dataset version produced it?
3. Which ground-truth version applies?
4. Which configuration was used?
5. Which code version produced it, if generated?
6. Was the artifact generated or manually defined?
7. Which environment was used?
8. Which metric/evaluation protocol applies?
9. Has the artifact been independently validated?
10. Is it compatible with the current V1 benchmark specification?

The current repository data does not define a provenance schema for this directory.

> **Provenance schema: Not specified.**

---

## 19. Validation

Validation must distinguish between:

- verifying that an artifact exists,
- verifying that an artifact is internally valid,
- verifying that an artifact corresponds to the correct benchmark case,
- and verifying that it represents scientifically valid reference information.

These are not equivalent.

For example, a registered image that visually appears aligned is not sufficient by itself to establish registration accuracy.

The benchmark feedback recommends using independent check points when evaluating a transformation.

Therefore, if `expected/` eventually stores registration reference outputs, the validation process should be explicitly documented rather than inferred from visual overlays.

### Current validation status

| Validation item                     | Status        |
| ----------------------------------- | ------------- |
| Expected-output schema validation   | Not specified |
| Case-ID validation                  | Not specified |
| Dataset-version validation          | Not specified |
| Ground-truth consistency validation | Not specified |
| Numerical tolerance validation      | Not specified |
| Artifact checksum validation        | Not specified |
| Independent reference validation    | Not specified |

---

## 20. What Should Not Be Stored Here Without Explicit Definition

The following should not be placed in this directory merely because they appear to be "expected":

- raw ground-truth datasets,
- arbitrary benchmark targets,
- undocumented numerical scores,
- unverified registration outputs,
- model checkpoints,
- generic SIFT outputs,
- arbitrary transformation matrices,
- screenshots presented as scientific reference results,
- manually selected "best" examples,
- decorative confidence percentages,
- star ratings,
- or results generated from an undocumented configuration.

The supplied project feedback specifically cautions against presenting unmeasured percentages or star ratings as experimental results.

Benchmark artifacts should be measurable, traceable, and attributable to a defined procedure.

---

## 21. Expected Baseline Is Not a Performance Claim

The name `expected` must not be interpreted as a claim that an algorithm is expected to achieve a particular accuracy.

For example, this is **not valid without benchmark evidence**:

```text
Expected RMSE = 0.2 pixels
```

Likewise, this is not a valid benchmark definition without supporting specification:

```text
Expected inlier ratio = 90%
```

Numerical targets must come from an explicit benchmark requirement, acceptance criterion, or measured reference experiment.

The project feedback recommends replacing decorative percentages with actual measurements such as:

- RMSE,
- inlier ratio,
- spatial coverage,
- runtime,
- and failure rate.

---

## 22. Relationship to the SIFT Baseline

The project materials identify SIFT as the practical starting baseline for the correspondence pipeline.

A representative algorithmic baseline is:

```text
Input Pair
   │
   ▼
SIFT
   │
   ▼
Descriptor Matching
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
Transformation
   │
   ▼
Registration Metrics
```

This is an **algorithmic baseline**.

It should remain conceptually separate from `expected/` unless repository implementation explicitly defines `expected` as a SIFT-derived reference.

Current relationship:

> **SIFT ↔ expected baseline: Not specified.**

---

## 23. Relationship to Advanced Matchers

The project materials identify several possible matching paths:

- SIFT,
- ALIKED + LightGlue,
- LoFTR,
- RIFT / CFOG research directions.

The documented recommendation is to compare methods on the same image pairs and retain measured evidence rather than assuming that a more sophisticated method is automatically better.

The expected component should not be used as a hidden mechanism for selecting one algorithm over another.

Any comparison should remain governed by the benchmark protocol.

---

## 24. Reference Retrieval Context

The project architecture also considers global retrieval.

The documented conceptual retrieval design is:

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
Top-K Candidate Tiles
       │
       ▼
Local Matching
```

The project feedback emphasizes that global retrieval and local matching solve different problems.

If `expected/` eventually participates in retrieval evaluation, its exact role must specify whether it contains:

- expected candidate IDs,
- reference region labels,
- retrieval ground truth,
- expected rankings,
- or another artifact.

Currently:

> **Retrieval role of `expected/`: Not specified.**

---

## 25. Artifact Naming

No authoritative naming convention for this directory is currently supplied.

Until a naming scheme is defined, do not assume a structure such as:

```text
expected/
├── pair_001.json
├── pair_002.json
└── pair_003.json
```

or:

```text
expected/
├── transforms/
├── metrics/
└── overlays/
```

These are examples only and are **not repository requirements**.

A future naming convention should provide stable association between:

```text
Benchmark Case
    ↕
Dataset Item
    ↕
Reference / Expected Artifact
```

---

## 26. Artifact Integrity

If expected/reference artifacts are eventually committed to the repository, integrity information should be considered part of their provenance.

Potential integrity metadata includes:

- SHA-256 checksum,
- artifact type,
- case ID,
- version,
- generation method,
- source dataset,
- creation timestamp,
- producing commit,
- configuration identifier.

However:

> **Artifact-integrity schema: Not specified.**

No checksum or manifest should be fabricated for artifacts that do not yet exist.

---

## 27. Failure Handling

Expected/reference artifacts should not silently hide benchmark failures.

The benchmark philosophy documented in the supplied project material is to:

> build small, measure honestly, and keep the failures.

Therefore, if a future expected/reference generation procedure fails for a benchmark case, the failure should be represented explicitly rather than replaced by a fabricated expected result.

Potential failure metadata may eventually include:

- case ID,
- failed stage,
- failure category,
- error message,
- recoverability,
- execution identifier.

The exact failure schema is:

> **Not specified.**

---

## 28. Reproducible Comparison

A valid comparison should use the same benchmark conditions.

Conceptually:

```text
Same Case
   │
   ├── Algorithm Baseline A
   │
   ├── Algorithm Baseline B
   │
   ├── Full ChandraMap Pipeline
   │
   └── Expected / Reference Component
             │
             ▼
      Common Evaluation Protocol
             │
             ▼
      Comparable Metrics
```

The comparison should preserve:

- the same benchmark case,
- the same dataset version,
- the same evaluation protocol,
- the same ground-truth definition,
- compatible preprocessing,
- and clearly documented configuration differences.

This prevents the expected component from becoming an undocumented source of incomparable results.

---

## 29. Scientific Integrity Rules

The following rules apply to the interpretation of this directory:

### Rule 1 — Do not equate names with definitions

`expected` is a directory name, not a scientific definition.

### Rule 2 — Do not silently convert expected data into ground truth

The ground-truth relationship must be explicitly documented.

### Rule 3 — Do not treat an algorithm baseline as an expected result

SIFT, ALIKED + LightGlue, LoFTR, and related methods are algorithmic paths unless the repository explicitly states otherwise.

### Rule 4 — Do not invent numerical expectations

A number becomes a benchmark target only when supported by the benchmark specification or a documented experimental protocol.

### Rule 5 — Preserve benchmark provenance

Reference artifacts must remain traceable to the case, dataset, configuration, and evaluation protocol.

### Rule 6 — Keep failures visible

A failed reference generation should not be silently replaced with a convenient result.

### Rule 7 — Separate physical scale from pixel count

Resizing an image does not create missing spatial information.

### Rule 8 — Evaluate correspondence independently

Where transformations are fitted from control/inlier points, evaluation should use independent check points when the benchmark protocol requires registration accuracy.

---

## 30. Implementation Status

Based on the supplied project data:

| Component                                          | Status                               |
| -------------------------------------------------- | ------------------------------------ |
| `benchmarks/baselines/expected/` directory concept | Specified by repository architecture |
| Exact expected-baseline semantics                  | Not specified                        |
| Expected-baseline implementation                   | Not confirmed                        |
| Expected output schema                             | Not specified                        |
| Expected generation script                         | Not specified                        |
| Expected validation script                         | Not specified                        |
| Expected configuration                             | Not specified                        |
| Expected artifact manifest                         | Not specified                        |
| Expected numerical targets                         | Not specified                        |
| Expected/ground-truth relationship                 | Not specified                        |
| Reproduction command                               | Not specified                        |

This README therefore documents the directory without pretending that an implementation exists when one has not been confirmed.

---

## 31. Recommended Definition Checklist

Before implementing or populating this directory, the repository should explicitly answer:

- [ ] What does `expected` mean?
- [ ] Is it a result, artifact, model, transformation, or reference implementation?
- [ ] Is it separate from ground truth?
- [ ] Which benchmark version owns it?
- [ ] What inputs does it consume?
- [ ] What outputs does it produce?
- [ ] Is it generated or manually curated?
- [ ] If generated, what code generates it?
- [ ] Which configuration generates it?
- [ ] Which dataset version is required?
- [ ] Which ground-truth version is required?
- [ ] What validation procedure applies?
- [ ] What metrics are associated with it?
- [ ] What numerical tolerances apply?
- [ ] How are artifacts versioned?
- [ ] How are artifacts hashed?
- [ ] How are failures represented?
- [ ] How does it interact with SIFT and other algorithmic baselines?
- [ ] How does it participate in V1 acceptance criteria?
- [ ] How is it reproduced?
- [ ] What constitutes a valid comparison?

Until these questions are answered by the repository specification or implementation, this directory should remain semantically conservative.

---

## 32. Suggested Future Documentation Contract

If the repository later defines `expected/` as a concrete benchmark artifact, this README should be updated to include at least:

```text
Definition
    ↓
Inputs
    ↓
Generation / Construction
    ↓
Validation
    ↓
Versioning
    ↓
Storage Layout
    ↓
Artifact Schema
    ↓
Benchmark Integration
    ↓
Comparison Procedure
    ↓
Reproducibility
```

The implementation should then reference the authoritative schema and commands rather than duplicating them informally.

---

## 33. Related Benchmark Documentation

The expected component should be interpreted together with the benchmark documentation:

- [`benchmarks/v1/README.md`](../../v1/README.md) — V1 benchmark overview.
- [`benchmarks/v1/BENCHMARK_SPEC.md`](../../v1/BENCHMARK_SPEC.md) — V1 benchmark specification.
- [`benchmarks/v1/GROUND_TRUTH_PROTOCOL.md`](../../v1/GROUND_TRUTH_PROTOCOL.md) — ground-truth definition and evaluation protocol.
- [`benchmarks/v1/REPRODUCIBILITY.md`](../../v1/REPRODUCIBILITY.md) — reproducibility and provenance requirements.
- `benchmarks/baselines/sift/` — algorithmic SIFT baseline, if present in the repository.
- `benchmarks/baselines/` — benchmark baseline collection.

The exact availability and contents of these files should be determined from the repository itself.

---

## 34. Design Principle

The purpose of a benchmark reference component is not to make results look better.

It is to make evaluation **clear, repeatable, traceable, and scientifically interpretable**.

For ChandraMap, that means preserving the distinction between:

```text
Input Data
    ↓
Algorithm
    ↓
Correspondences
    ↓
Geometric Verification
    ↓
Registration
    ↓
Independent Evaluation
```

and any additional expected/reference artifact that the benchmark may define.

Until the repository explicitly defines what `expected` represents, the correct documentation state is:

> **`benchmarks/baselines/expected/` is reserved for an expected/reference baseline component, but its exact semantic role, data contract, generation process, and relationship to ground truth are not yet specified by the supplied project data.**

That distinction should remain explicit rather than being replaced with an invented implementation.

<!--
Source basis used for this documentation:
- SIH26166 Silarlar PS.pdf
- Aryan_SIH_26166_Lunar_Image_Correspondence_Feedback.pptx
- Aryan_Lunar_Image_Registration_Feedback.pdf

The supplied source material supports the documented benchmark context, SIFT baseline,
registration workflow, metrics, stress-test structure, sensor distinctions, and
ground-truth/evaluation principles. It does not explicitly define the semantic role
of benchmarks/baselines/expected/, so that role is intentionally marked as
Not specified / Not confirmed.
-->
