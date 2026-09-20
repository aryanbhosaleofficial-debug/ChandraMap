# ChandraMap Research

ChandraMap is an open-source research and engineering project focused on **lunar image correspondence, registration, multi-sensor matching, and reproducible scientific evaluation**.

This directory contains the research-oriented documentation for investigating how images of the same lunar region can be matched reliably when they differ in:

- sensor and mission
- Ground Sample Distance (GSD)
- spatial resolution
- illumination and Sun angle
- viewing geometry
- modality
- radiometric response
- terrain relief
- available metadata

The primary research objective is not to produce a visually attractive lunar mosaic. ChandraMap focuses first on **measurable correspondence and registration**: reliable matched points, verified geometric support, transformations, residuals, spatial coverage, independent evaluation, and reproducible benchmark results.

> **A visually convincing registration is useful for inspection, but scientific conclusions should come from controlled measurements.**

---

## 🔬 Research Overview

ChandraMap investigates the problem of finding reliable correspondence between lunar observations that may appear significantly different even when they cover the same physical region.

A source observation from Chandrayaan-2 may need to be compared with imagery from another Chandrayaan-2 instrument or with reference imagery from NASA's Lunar Reconnaissance Orbiter.

The central research problem can be summarized as:

```text
Different lunar observations
        ↓
Sensor-aware representation
        ↓
Physically meaningful scale handling
        ↓
Correspondence generation
        ↓
Geometric verification
        ↓
Optional local / sub-pixel refinement
        ↓
Final geometric model
        ↓
Registration
        ↓
Independent evaluation
```

Research should determine **which parts of this process actually improve performance**, rather than adding algorithmic complexity without controlled evidence.

---

## 🎯 Research Objectives

ChandraMap research is intended to answer measurable questions about lunar image correspondence and registration.

Primary objectives include:

1. Establish a reproducible classical correspondence and registration baseline.
2. Measure how large cross-sensor resolution differences affect matching.
3. Evaluate sensor-aware preprocessing instead of treating every instrument identically.
4. Determine which image representations remain useful under major Sun-angle changes.
5. Measure whether physically meaningful multi-scale matching outperforms naïve resizing.
6. Compare classical and learned correspondence methods on the same controlled data.
7. Measure whether local or sub-pixel refinement reduces independent registration error.
8. Quantify whether verified correspondences are spatially distributed across the valid overlap.
9. Separate global/regional retrieval performance from local registration performance.
10. Preserve enough provenance for another contributor to reproduce an experiment.
11. Record scientific failures rather than hiding them.
12. Preserve earlier baselines so later research versions can be compared fairly.

---

## 🌙 Core Research Problem

Two images of the same lunar region can look substantially different.

The difference may come from:

- Sun-angle changes
- different shadow geometry
- different spatial sampling
- different camera or spectrometer characteristics
- different viewing directions
- terrain relief
- image projection
- radiometric response
- spectral modality
- incomplete or uncertain metadata

This means lunar correspondence is not equivalent to matching two ordinary photographs captured under similar conditions.

A robust research system should distinguish the following stages:

```text
Source / Reference Data
        ↓
Sensor Preparation
        ↓
Scale Preparation
        ↓
Candidate Correspondences
        ↓
Geometric Verification
        ↓
Verified Inliers
        ↓
Refinement
        ↓
Final Transformation
        ↓
Registered Result
        ↓
Evaluation
```

These terms should not be treated as interchangeable.

In particular:

- candidate matches are not automatically correct matches
- matcher confidence is not geometric proof
- RANSAC inliers are not automatically ground truth
- registration is not automatically geospatial localization
- reference imagery is not automatically independent truth

---

## 🛰️ Sensors and Data Modalities

ChandraMap research spans instruments with substantially different physical properties.

| Instrument / Dataset          | Approximate Project Context                                                                                           | Research Implication                                                                              |
| ----------------------------- | --------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------- |
| **Chandrayaan-2 OHRC**        | Very high-resolution visible/panchromatic imagery; approximately 0.25–0.32 m/pixel depending on product/documentation | Suitable for detailed terrain correspondence and fine registration                                |
| **Chandrayaan-2 TMC-2**       | Panchromatic terrain imagery; approximately 5 m/pixel                                                                 | Useful for broader terrain structure and cross-scale correspondence                               |
| **Chandrayaan-2 IIRS**        | Imaging infrared hyperspectral/spectrometer data; approximately 80 m/pixel; approximately 0.8–5.0 µm                  | Requires a dedicated cross-modal registration strategy rather than ordinary grayscale treatment   |
| **LRO NAC**                   | High-resolution lunar reference imagery; product scale varies and is often approximately 0.5–2 m/pixel                | Useful as a detailed reference where scientifically appropriate                                   |
| **LRO WAC**                   | Broader lunar-scale imagery and context                                                                               | Useful for regional/global reference work, illumination studies, and potential retrieval research |
| **Kaguya / SELENE TC**        | Optional future cross-mission imagery                                                                                 | Potential independent cross-mission research direction                                            |
| **Synthetic transformations** | Generated research data                                                                                               | Useful for controlled rotation, scale, contrast, geometry, and stress experiments                 |

These are approximate project-level descriptions.

> **Product metadata should be treated as authoritative for the exact observation being evaluated.**

A nominal sensor specification should not replace the actual metadata of a specific product.

---

## 🧠 Research Principles

### Sensor-aware processing

OHRC, TMC-2, and IIRS should not automatically pass through one identical preprocessing path.

The preprocessing stage should respect:

- sensor modality
- radiometric characteristics
- spatial sampling
- available metadata
- the representation required by the correspondence method

The aim is to create comparable registration representations without pretending the original sensors contain equivalent information.

---

### Physically meaningful scale handling

Upsampling a low-resolution image produces more pixels, not more lunar surface information.

> **Increasing array size does not recover spatial detail that the sensor never measured.**

Research should instead consider approaches such as:

- image pyramids
- reference pyramids
- downsampling the finer observation
- matching at comparable effective ground scales
- coarse-to-fine correspondence
- scale-aware candidate selection

Fine refinement should only make claims supported by the information content of the source product.

---

### Illumination robustness

Lunar illumination differences are not only brightness differences.

Changes in Sun geometry can change:

- shadow direction
- shadow length
- crater appearance
- ridge visibility
- local gradient structure
- feature repeatability

Research may compare representations such as:

- raw grayscale
- locally normalized intensity
- gradients
- edges
- phase-based representations
- structural terrain representations
- shadow-aware representations

Each technique should be evaluated experimentally rather than assumed to provide illumination invariance.

---

### Geometric verification

Matcher output should initially be treated as candidate correspondence.

A typical research sequence is:

```text
Candidate Matches
        ↓
Robust Geometric Verification
        ↓
Initial Model
        ↓
Verified Inliers
        ↓
Optional Local / Sub-Pixel Refinement
        ↓
Final Model Refit
        ↓
Registration
        ↓
Evaluation
```

A correspondence score alone should not replace geometric verification.

---

### Evidence before complexity

Newer does not automatically mean better.

A learned matcher, specialized preprocessing stage, or more complex geometric model should remain in the project only when its value can be demonstrated for the scientific task it is intended to improve.

> **Advanced methods should earn their place through controlled evaluation.**

---

### Reproducibility

A research result should preserve enough information to determine:

- which data were used
- which source/reference pair was evaluated
- how each input was prepared
- which scientific version ran
- which algorithm was used
- which configuration was resolved
- which parameters were active
- which code revision produced the result
- which environment was used where relevant
- which random state was used where relevant
- which evaluation protocol was applied
- which metrics were produced
- which stages failed, if any

---

## ❓ Research Questions

Representative ChandraMap research questions include:

1. How robust are classical local features to large lunar illumination changes?
2. How much does sensor-aware preprocessing improve cross-sensor correspondence?
3. Which IIRS representation preserves the most useful terrain structure for registration?
4. At what scale or GSD differences do different correspondence methods begin to fail?
5. Does matching at comparable effective ground scale outperform naïve image resizing?
6. How well do learned terrestrial feature matchers generalize to lunar imagery?
7. Under which conditions is affine registration sufficient?
8. When does a homography provide a useful local approximation?
9. When are local, piecewise, terrain-aware, or sensor-model-based approaches required?
10. How much does sub-pixel refinement reduce independently measured error?
11. How should well-distributed lunar correspondences be quantified?
12. How much does metadata-assisted candidate restriction reduce the global search problem?
13. How useful is image-only retrieval when reliable location metadata is unavailable?
14. How much performance is lost under large Sun-angle differences?
15. Which methods are most robust on low-feature terrain?
16. Which methods are most vulnerable to repetitive crater patterns?
17. Can structural multimodal methods improve difficult cross-sensor pairs?
18. How stable are learned methods under lunar domain shift?
19. How should failed registrations be categorized without inventing unsupported root causes?
20. Which improvements remain measurable when the same frozen benchmark is used across versions?

These are research directions, not claims that the corresponding problems have already been solved.

---

## 🧪 Experimental Methodology

Research experiments should ideally change one major variable at a time.

This makes it possible to determine which component actually caused a measured change.

A conceptual progression could be:

| Experiment                 | Controlled Change                             | Purpose                                       |
| -------------------------- | --------------------------------------------- | --------------------------------------------- |
| Baseline                   | Classical correspondence pipeline             | Establish reference performance               |
| Sensor-aware preprocessing | Change preprocessing only                     | Measure sensor preparation benefit            |
| Multi-scale strategy       | Add physically meaningful scale handling      | Measure scale-handling benefit                |
| Stronger matcher           | Replace correspondence method only            | Isolate matcher contribution                  |
| Combined pipeline          | Sensor-aware + multi-scale + stronger matcher | Test interaction of improvements              |
| Refinement                 | Add local/sub-pixel refinement                | Measure effect on independent geometric error |

Avoid comparing two systems where multiple major variables changed simultaneously unless the goal explicitly requires a combined-system comparison.

Ablation experiments should be preferred when determining **why** performance changed.

---

## 🧱 Baseline

ChandraMap needs a simple, explainable baseline that future methods can be compared against.

A conceptual classical baseline is:

```text
Source + Reference
        ↓
SIFT
        ↓
Descriptor Matching
        ↓
Ratio / Cross-Check Filtering
        ↓
RANSAC
        ↓
Affine or Homography Model
        ↓
Registration
        ↓
Evaluation
```

This structure is intended as a research baseline concept.

The repository implementation and version documentation remain authoritative for exact pipeline behavior.

The baseline should remain:

- understandable
- reproducible
- measurable
- sufficiently simple to diagnose
- stable enough for future comparison

The purpose of the baseline is not to be intentionally weak.

It provides a trustworthy reference against which additional complexity can be measured.

---

## 🚀 Candidate and Advanced Methods

ChandraMap research may investigate methods including:

| Method / Family                    | Research Role                                      | Important Caution                                                          |
| ---------------------------------- | -------------------------------------------------- | -------------------------------------------------------------------------- |
| **RootSIFT**                       | Classical descriptor variant                       | Benefit should be measured against the baseline                            |
| **ORB**                            | Possible speed-oriented classical baseline         | Faster execution does not automatically imply adequate scientific accuracy |
| **ALIKED + LightGlue**             | Learned sparse feature and matching path           | Terrestrial pretraining does not guarantee lunar robustness                |
| **LoFTR**                          | Detector-free correspondence research              | Domain shift and large scale differences may remain difficult              |
| **RIFT**                           | Multimodal structural correspondence research      | Integration and lunar performance require controlled evaluation            |
| **CFOG-like approaches**           | Structural multimodal matching direction           | Should be evaluated against project-specific lunar conditions              |
| **Phase correlation**              | Registration/refinement research                   | Applicability depends on representation and geometry                       |
| **Patch correlation**              | Local or sub-pixel refinement                      | Requires reliable initialization                                           |
| **Planetary registration methods** | Research direction for lunar-specific registration | Must be compared using the same evaluation contract                        |
| **Learned global descriptors**     | Candidate retrieval research                       | Retrieval performance is separate from local registration performance      |

These should be treated as candidate or research methods unless the repository explicitly establishes implementation status.

> **A pretrained terrestrial matcher is not automatically lunar invariant.**

---

## 🔍 Retrieval vs Registration

ChandraMap may eventually address two related but distinct problems.

### Global / Regional Retrieval

Retrieval is relevant when the approximate location of the source observation is unknown.

A possible research flow is:

```text
Reference Imagery
        ↓
Tiling / Multi-Scale Preparation
        ↓
Global Descriptor Extraction
        ↓
Vector Index
        ↓
Top-K Candidate Regions
```

Online:

```text
Source / Query Observation
        ↓
Compatible Global Descriptor
        ↓
Similarity Search
        ↓
Top-K Candidate Reference Regions
```

If FAISS is used, its role should be described correctly:

> **FAISS performs similarity search and indexing over vectors. It does not itself extract lunar image features or perform image registration.**

### Local Correspondence / Registration

After a candidate overlap is known:

```text
Source Image
+
Candidate Reference Region
        ↓
Local Correspondence Generation
        ↓
Candidate Matches
        ↓
Geometric Verification
        ↓
Verified Inliers
        ↓
Refinement
        ↓
Final Transformation
        ↓
Registered Result
```

### Metadata-assisted search

Global image retrieval should be conditional.

If reliable metadata provides:

- latitude/longitude
- footprint
- projection
- approximate location
- other useful geometry

the system should use that information to reduce the search region where scientifically appropriate.

Solving a whole-Moon image-retrieval problem is unnecessary when trustworthy metadata has already constrained the location.

---

## 📏 Scale and Resolution Research

Ground Sample Distance (GSD) describes approximately how much lunar surface one image pixel represents.

ChandraMap may need to compare imagery spanning very different GSD ranges.

Conceptually:

```text
OHRC        → very fine spatial detail
LRO NAC     → fine reference detail
TMC-2       → broader terrain structure
IIRS        → much coarser spatial sampling
```

The correct research question is not:

> How can every image be resized to the same pixel dimensions?

It is:

> At which effective ground scales do the two observations contain comparable terrain information?

Useful strategies may include:

- reference pyramids
- multi-resolution representations
- controlled downsampling
- scale-aware matching
- coarse-to-fine refinement

> **Upsampling changes pixel count; it does not recover missing physical detail.**

---

## ☀️ Illumination Research

Lunar terrain appearance is strongly influenced by illumination geometry.

The same crater can produce significantly different image patterns under different Sun angles.

Brightness normalization may reduce radiometric differences, but it cannot automatically move a shadow to the location it would occupy under another illumination geometry.

Research should investigate whether stable terrain cues such as:

- crater rims
- ridges
- edges
- local gradients
- phase structure
- relative geometry
- terrain-derived features

provide greater correspondence stability.

Controlled evaluation should include pairs with different illumination conditions where suitable data are available.

---

## 🌈 IIRS / Cross-Modal Research

IIRS deserves a dedicated research path.

IIRS is an **Imaging Infrared Spectrometer**, not simply a lower-resolution grayscale camera.

Its data may contain many spectral bands covering approximately 0.8–5.0 µm, while its spatial sampling is much coarser than OHRC, TMC-2, or high-resolution NAC imagery.

A conventional local feature pipeline should therefore not receive a full hyperspectral cube without a clearly defined representation strategy.

Research may investigate:

- individual band selection
- wavelength-range selection
- Principal Component Analysis (PCA)
- spectral composites
- structural projections
- gradient-based representations
- other registration-friendly 2D representations

The experiment should identify **exactly which representation was used**.

Fine terrain detail should not be claimed when the source instrument cannot physically resolve it.

---

## 📐 Geometry and Refinement

The geometric model should be as simple as possible while adequately explaining the correspondence residuals.

Potential local models include:

- affine transforms
- projective/homography transforms

However, the lunar surface is not a flat plane.

A single global transform may become insufficient because of:

- terrain relief
- viewing geometry
- sensor geometry
- wide-area coverage
- projection differences

Research should inspect residual behavior rather than assuming one model is universally valid.

A preferred refinement order is:

```text
Candidate Correspondences
        ↓
RANSAC / Robust Verification
        ↓
Initial Geometric Model
        ↓
Verified Inliers
        ↓
Local / Sub-Pixel Coordinate Refinement
        ↓
Final Model Refit
        ↓
Registration
```

Sub-pixel refinement should operate on reliable correspondences rather than being used to rescue arbitrary matcher output.

A highly flexible warp should not be used to hide weak or poorly distributed control points.

---

## 📊 Evaluation Metrics

Different stages require different metrics.

| Evaluation Area | Example Metric        | What It Measures                                                                 |
| --------------- | --------------------- | -------------------------------------------------------------------------------- |
| Retrieval       | Recall@1              | Whether the correct region is ranked first                                       |
| Retrieval       | Recall@K / Recall@5   | Whether the correct region appears among the top candidates                      |
| Matching        | Candidate match count | Number of proposed correspondences                                               |
| Matching        | Verified inlier count | Number of correspondences consistent with the geometric model                    |
| Matching        | Inlier ratio          | Fraction of the defined candidate population surviving geometric verification    |
| Geometry        | Fit residual          | Consistency of the transform with fitting points                                 |
| Registration    | Check-point RMSE      | Error on independent points not used to fit the transform                        |
| Registration    | Reprojection error    | Geometric consistency under a defined model                                      |
| Registration    | Residual vectors      | Magnitude and direction of local geometric error                                 |
| Distribution    | Grid coverage         | How widely verified correspondences cover the valid region                       |
| Distribution    | Convex-hull coverage  | Area supported by the spatial extent of correspondences                          |
| Geospatial      | Ground-space error    | Physical error where GSD/projection/truth allow meaningful conversion            |
| System          | Runtime               | Computational execution cost under documented conditions                         |
| System          | Memory use            | Resource requirement where relevant                                              |
| System          | Success rate          | Fraction of valid evaluation pairs meeting defined scientific success conditions |
| System          | Failure rate          | Fraction of valid pairs producing scientific failure                             |

More candidate matches are not necessarily better.

A large number of:

- incorrect matches
- duplicated matches
- highly clustered matches

can still produce weak scientific registration.

---

## Independent Evaluation

Whenever possible, the points used to fit the transformation should not be the only points used to evaluate it.

For example:

```text
Control / Fit Points
        ↓
Estimate Transformation

Independent Check Points
        ↓
Evaluate Transformation
```

Using the fitting points themselves for the final error measurement can make the result appear more accurate than its true independent registration performance.

Prefer:

- challenge-provided or otherwise valid evaluation truth
- independently verified check points
- held-out points not used for model fitting

when available.

---

## 🗺️ Spatial Coverage

"Well-distributed matches" should be measurable.

Visual inspection alone is insufficient.

Possible research metrics include:

### Grid coverage

Divide the valid overlap into an \(N \times N\) grid and measure how many cells contain at least one verified inlier.

### Convex-hull coverage

Compute the area enclosed by verified correspondences relative to the valid overlap.

### Other coverage definitions

Alternative measures may be introduced if their:

- formula
- coordinate space
- denominator
- valid region
- interpretation

are defined explicitly.

> **Spatial coverage and geometric accuracy measure different properties.**

A method can have excellent local error but poor coverage if all inliers cluster around one crater.

---

## Sub-Pixel Accuracy

Sub-pixel error should first be interpreted in the pixel coordinate system of the relevant image.

For example:

```text
0.2 source-image pixels
```

is meaningful only when the source coordinate space is clear.

Do not automatically convert pixel error to metres unless:

- product-specific GSD is known
- projection/geometry supports the conversion
- the reference truth supports ground-space interpretation

For example:

```text
0.2 px on TMC-2
```

and:

```text
0.2 px on IIRS
```

represent substantially different physical ground distances.

> **Sub-pixel is a coordinate-space statement, not automatically a sub-metre statement.**

---

## 🧪 Stress-Test Matrix

A research benchmark should include more than one favorable pair.

| Stress Case                           | What It Tests                                                                               |
| ------------------------------------- | ------------------------------------------------------------------------------------------- |
| **Known-overlap / lower-stress pair** | Whether the pipeline works end-to-end under relatively favorable conditions                 |
| **Sun-angle stress**                  | Sensitivity to different illumination and shadow geometry                                   |
| **Scale / GSD stress**                | Robustness to large differences in spatial sampling                                         |
| **Modality stress**                   | Cross-modal registration, particularly IIRS-derived representations against visible imagery |
| **Geometry / viewpoint stress**       | Sensitivity to geometric distortion, relief, or viewing differences                         |
| **Low-feature terrain**               | Behavior where few stable local structures exist                                            |
| **Repetitive terrain**                | Resistance to ambiguous or repeated crater/terrain patterns                                 |
| **Retrieval stress**                  | Ability to retrieve the correct candidate region when location is not already known         |

The exact benchmark categories and thresholds should be defined in the project's evaluation documentation rather than inferred from this table.

---

## 🧩 Versioned Benchmarking

ChandraMap preserves scientific versions so that research improvements can be compared rather than continuously overwriting the previous methodology.

The versioned approach supports:

- reproducible baselines
- controlled comparison
- ablation studies
- regression detection
- incremental research
- long-term traceability

Conceptually:

```text
Frozen Benchmark Data
        ├── Scientific V1
        ├── Scientific V2
        ├── Scientific V3
        └── Scientific V4
              ↓
        Comparable Metrics
```

Where scientifically compatible, versions should be compared using the same:

- image pairs
- truth
- held-out check points
- metric definitions
- coordinate conventions
- category definitions

Later research versions should extend capabilities rather than silently changing what historical V1 metrics mean.

For developer-facing benchmark governance, see [Benchmarking](../development/benchmarking.md).

For the current V1 project scope, see [V1 Scope](../project/v1-scope.md).

---

## 📦 Research Data

Important research data sources include:

| Dataset / Instrument            | Organization        | Research Role                                                                         |
| ------------------------------- | ------------------- | ------------------------------------------------------------------------------------- |
| Chandrayaan-2 OHRC              | ISRO                | High-resolution source/target imagery and fine terrain correspondence                 |
| Chandrayaan-2 TMC-2             | ISRO                | Medium-scale terrain correspondence and cross-scale research                          |
| Chandrayaan-2 IIRS              | ISRO                | Cross-modal and hyperspectral registration research                                   |
| LRO NAC                         | NASA / LROC         | High-resolution lunar reference imagery                                               |
| LRO WAC                         | NASA / LROC         | Regional/global lunar reference context                                               |
| Kaguya / SELENE Terrain Camera  | JAXA                | Optional future cross-mission research                                                |
| Synthetic lunar transformations | ChandraMap research | Controlled scale, rotation, geometry, contrast, and illumination-style stress testing |

This table describes research roles.

It does **not** imply that every dataset is currently:

- downloaded
- integrated
- redistributed by the repository
- included in a formal benchmark
- processed by every scientific version

Data availability, licensing, product selection, and benchmark membership should be documented separately.

---

## 💾 Research Artifacts

A reproducible experiment should preserve useful evidence rather than only its final preview.

Depending on the experiment, useful artifacts may include:

- experiment configuration
- resolved configuration
- source product identifier
- reference product identifier
- pair identifier
- preprocessing configuration
- registration representation
- candidate correspondences
- filtered correspondences
- verified inliers
- rejected matches
- initial transformation
- refined/final transformation
- residuals
- independent check-point measurements
- registered image or overlay
- spatial-coverage visualization
- metric output
- logs
- runtime information
- dependency/environment information
- failure diagnostics
- notes about scientific limitations

Artifacts should not be silently mixed with permanent source code.

The repository's configured result/artifact structure should remain authoritative for where generated outputs belong.

---

## 🔁 Reproducibility

A useful experiment should make it possible to reconstruct:

```text
Data
+
Scientific Version
+
Code Revision
+
Representation / Preprocessing
+
Resolved Configuration
+
Environment
+
Random Context
+
Evaluation Protocol
=
Reproducible Research Context
```

At minimum, preserve enough information to answer:

1. Which source/reference products were used?
2. Which scientific version ran?
3. Which algorithm/path was evaluated?
4. How were the inputs prepared?
5. Which configuration was active?
6. Which code revision produced the output?
7. Which benchmark/evaluation protocol was used?
8. Which truth/check-point set was used?
9. Which metrics were computed?
10. Which stages failed?
11. Which hardware or accelerator affected runtime claims?
12. Which random state mattered for stochastic methods?

Reproducibility does not necessarily mean bitwise-identical output across every hardware/library combination.

It means preserving enough scientific and engineering context for the experiment to be repeated and interpreted correctly.

---

## 📂 Research Documentation

This README is the entry point for `docs/research/`.

Research documentation should be divided by stable responsibility rather than by temporary experiment state.

The research documentation set is intended to cover areas such as:

- baseline methodology
- research questions
- research assumptions
- experiment methodology
- known limitations
- scientific references

Only documents that actually exist in the repository should be linked from this section.

When a new research document is added, include it here with:

- a relative link
- a one-sentence responsibility description
- terminology consistent with the rest of ChandraMap

Avoid creating navigation links to planned-but-nonexistent files.

---

## 🧭 Relationship to Other Documentation

Research documentation does not replace project, architecture, sensor, benchmark, or development documentation.

Relevant project documentation includes:

- [Project Goals](../project/goals.md) — project-level objectives
- [Project Non-Goals](../project/non-goals.md) — boundaries of the project
- [V1 Scope](../project/v1-scope.md) — current V1 scientific scope
- [Terminology](../project/terminology.md) — canonical project and scientific vocabulary
- [Project Assumptions](../project/assumptions.md) — assumptions that affect design and interpretation
- [Project Limitations](../project/limitations.md) — known project-level constraints

Relevant architecture documentation includes:

- [System Overview](../architecture/system-overview.md) — high-level ChandraMap system architecture
- [V1 Pipeline](../architecture/v1-pipeline.md) — V1 pipeline architecture and stage relationships
- [Core Engine Architecture](../architecture/core-engine-architecture.md) — scientific core-engine responsibilities
- [Data Flow](../architecture/data-flow.md) — movement of scientific data through the system
- [Output Flow](../architecture/output-flow.md) — production and propagation of results/artifacts

Relevant scientific/development guidance includes:

- [Sensors Overview](../sensors/overview.md) — project sensor context
- [Benchmarking Guide](../development/benchmarking.md) — benchmark execution, governance, fairness, and reproducibility
- [Naming Conventions](../development/naming-conventions.md) — scientific identifier and terminology conventions
- [Local Development](../development/local-development.md) — local development workflow
- [Coding Standards](../development/coding-standards.md) — engineering-quality expectations

---

## 🧑‍🔬 Adding a Research Experiment

A lightweight ChandraMap research workflow is:

1. **Define the hypothesis.**
   State what scientific question the experiment is intended to answer.

2. **Select controlled data.**
   Choose data that can validly test the hypothesis without cherry-picking only favorable pairs.

3. **Define the evaluation population.**
   Specify which pairs, sensors, or conditions are included.

4. **Record configuration.**
   Preserve preprocessing, algorithm, transform, refinement, and evaluation settings.

5. **Run the baseline.**
   Establish reference behavior using the appropriate existing scientific baseline.

6. **Run the experimental method.**
   Change only the intended factor where possible.

7. **Use the same evaluation protocol.**
   Preserve truth, coordinate conventions, metrics, and failure handling where scientifically compatible.

8. **Save quantitative outputs.**
   Preserve results, residuals, coverage, runtime, and relevant diagnostics.

9. **Preserve failures.**
   A failed valid pair is research evidence.

10. **Compare against the baseline.**
    Identify which metrics improved, regressed, or remained unchanged.

11. **Document limitations.**
    Avoid interpreting one successful category as universal robustness.

12. **Promote only reproducible findings.**
    An exploratory result should not become a project-wide claim until it survives controlled evaluation.

---

## ✅ What Counts as a Valid Research Result

A useful research result should ideally include:

- exact source/reference pair or dataset population
- sensor/product context
- scientific version
- algorithm or method
- resolved configuration
- preprocessing/representation details
- quantitative metrics
- units and coordinate spaces
- baseline comparison
- independent evaluation where possible
- spatial-distribution evidence where relevant
- success and failure outcomes
- reproducibility information
- known limitations

A result should not rely solely on:

- one attractive overlay
- matcher confidence
- raw correspondence count
- one favorable pair
- manually tuned final parameters
- an undocumented preprocessing sequence

---

## Research Evidence Hierarchy

Different outputs provide different strengths of evidence.

| Evidence                               | Useful For                          | Limitation                                          |
| -------------------------------------- | ----------------------------------- | --------------------------------------------------- |
| Visual overlay                         | Human inspection                    | Can hide local geometric error                      |
| Candidate-match visualization          | Debugging matching                  | Does not establish correctness                      |
| Verified-inlier visualization          | Inspecting geometric support        | Inliers are still model-dependent                   |
| Fit residual                           | Model-fitting diagnostics           | Not independent evaluation                          |
| Spatial coverage                       | Distribution of support             | Does not measure geometric accuracy                 |
| Held-out check-point error             | Independent registration evaluation | Requires valid independent truth                    |
| Benchmark result across multiple pairs | Scientific comparison               | Depends on benchmark quality and representativeness |
| Reproducible multi-version benchmark   | Long-term method comparison         | Still limited by benchmark scope                    |

---

## ⚠️ Research Limitations

ChandraMap operates in a difficult scientific domain.

Important limitations include:

### Large cross-sensor scale gaps

OHRC, TMC-2, IIRS, NAC, and WAC may represent the lunar surface at substantially different spatial sampling.

Fine terrain visible in one product may not physically exist in another observation.

### Illumination variation

Large Sun-angle changes alter shadows and feature visibility.

Simple brightness normalization cannot fully remove this effect.

### Terrain relief

Lunar topography can violate simple planar geometric assumptions.

One affine transform or homography may not explain every scene.

### Cross-modal appearance

IIRS and visible panchromatic imagery may respond to different physical properties.

Cross-modal registration is therefore not just a resolution problem.

### Incomplete metadata

Useful metadata such as:

- precise footprint
- projection
- geometry
- illumination
- product-specific GSD

may not always be available or equally reliable.

### Limited independent truth

Reliable independently verified lunar correspondence truth can be difficult to construct.

This limits how strongly some accuracy claims can be made.

### Domain shift

Learned terrestrial feature matchers may behave differently on:

- lunar texture
- extreme shadow patterns
- crater repetition
- unusual scale differences

Their performance must be measured rather than assumed.

### IIRS spatial constraints

IIRS may support stable large-scale correspondence while lacking the spatial detail required for fine-feature claims available to higher-resolution instruments.

### Benchmark familiarity

A fixed benchmark can gradually become familiar enough that repeated development begins to overfit it.

Long-term research may therefore require additional stress tests, external validation, or benchmark revisions.

---

## 📚 References and Resources

Research should prefer authoritative mission and scientific documentation before implementation tutorials.

Important source categories include:

### Mission and data documentation

- ISRO Chandrayaan-2 instrument documentation
- ISRO / ISSDC PRADAN science-data documentation
- LRO / LROC mission and product documentation
- product-specific metadata and processing documentation

### Planetary image processing

- USGS ISIS documentation
- planetary image registration and control-network resources

### Classical computer vision

- OpenCV feature-matching documentation
- SIFT
- geometric-model estimation
- RANSAC
- image registration techniques

### Learned correspondence

- LightGlue
- ALIKED
- LoFTR

### Multimodal remote-sensing correspondence

- RIFT
- CFOG-related research

Exact papers, repositories, citations, and URLs should be maintained in the project's dedicated references documentation rather than invented here.

---

## 🤝 Contributing to Research

Research contributions should prioritize reproducibility over impressive demonstrations.

A research contribution should make clear:

- which question it addresses
- why the experiment is needed
- which baseline it compares against
- which data it evaluates
- what changed
- what remained controlled
- which metrics were measured
- which failures occurred
- whether the result is exploratory or benchmarked
- whether documentation needs updating

Contributors should avoid:

- tuning only the final benchmark pair
- deleting difficult valid cases
- using held-out truth to choose parameters
- presenting matcher scores as geometric accuracy
- calling reference imagery ground truth without justification
- converting image error to metres without valid geometric context
- replacing the baseline before its comparison value has been preserved
- claiming robustness from one successful experiment

---

## 📌 Research Status

ChandraMap is an active research and engineering project.

The project is designed to establish a reproducible classical baseline first and then evaluate increasingly capable research methods under controlled benchmarks.

Current documentation should therefore distinguish carefully between:

- established project principles
- implemented scientific components
- experiments under evaluation
- planned research
- future research directions

No method should be described as scientifically superior, lunar invariant, production-ready, or validated to a particular accuracy unless controlled repository results provide the supporting evidence.

> **The next useful research result is not another pipeline box—it is a reproducible experiment showing what changed, why it changed, and how that change affected measured performance.**

---

### Related Documentation

- [Project Goals](../project/goals.md)
- [V1 Scope](../project/v1-scope.md)
- [Project Terminology](../project/terminology.md)
- [System Overview](../architecture/system-overview.md)
- [V1 Pipeline](../architecture/v1-pipeline.md)
- [Sensors Overview](../sensors/overview.md)
- [Benchmarking Guide](../development/benchmarking.md)
- [Naming Conventions](../development/naming-conventions.md)

The research documentation should evolve as experiments become reproducible, benchmarked, and scientifically interpretable.

<!-- Source request: :contentReference[oaicite:0]{index=0} -->
