# ChandraMap V1 Scope

Benchmark V1 is ChandraMap's **canonical classical lunar image-registration baseline**.

Its purpose is to establish a simple, explainable, reproducible reference pipeline for known-overlap lunar image pairs before introducing sensor-aware processing, large-scale retrieval, learned matching, terrain-aware geometry, or other advanced methods.

V1 is intentionally limited.

> **V1 exists to establish a trustworthy baseline, not to achieve the strongest possible registration performance.**

Later benchmark configurations should be able to answer a meaningful question:

> Did the additional complexity measurably improve the result relative to V1?

This document defines **what belongs in Benchmark V1 and what does not**. It defines target benchmark scope; it does not by itself prove that every V1 component is already implemented.

---

## 1. Purpose

V1 should answer one primary question:

> **Can a classical, explainable local image-registration pipeline establish reliable correspondences and estimate a geometric alignment for a known overlapping lunar image pair?**

Conceptually:

```text
Known Source Image
        +
Known Overlapping Reference Image
        ↓
Input Validation
        ↓
Minimal Preprocessing
        ↓
SIFT / Canonical Classical Features
        ↓
Descriptor Matching
        ↓
Candidate Match Filtering
        ↓
RANSAC / Geometric Verification
        ↓
Verified Inliers
        ↓
Global Affine or Homography Transform
        ↓
Registration
        ↓
Baseline Evaluation
        ↓
Success / Rejection / Failure
```

V1 provides the reference against which V2, V3, and V4 should be evaluated.

---

## 2. V1 Research Question

The primary V1 research question is:

> **How well does a classical local-feature and geometric-verification pipeline perform on known-overlap lunar image pairs?**

Useful secondary questions include:

- How many features are detected?
- How many candidate matches are produced?
- How many candidate matches survive geometric verification?
- What is the inlier ratio?
- Are verified inliers spatially distributed?
- What geometric residuals remain?
- What independent registration error is observed where check points exist?
- Which lunar image pairs fail?
- Which scale or illumination differences expose weaknesses in the classical baseline?
- What is the runtime under documented benchmark conditions?

V1 should reveal limitations rather than hide them.

---

## 3. Why V1 Exists

A useful scientific baseline must remain:

- simple
- inspectable
- explainable
- reproducible
- measurable
- lightweight
- deterministic where practical
- easy to debug
- intentionally limited

The goal is not:

> maximize accuracy by adding every available technique

The goal is:

> establish a controlled classical reference point

A baseline loses value if advanced components are repeatedly added to make its results look stronger.

For example, a baseline that begins as:

```text
SIFT
  ↓
Matching
  ↓
RANSAC
```

should not gradually become:

```text
Sensor Routing
  +
Learned Features
  +
LightGlue
  +
LoFTR
  +
Global Retrieval
  +
FAISS
  +
Local Warping
  +
Confidence Calibration
```

while still being called the same V1 baseline.

Later versions should improve on V1 without redefining V1 into those later versions.

---

## 4. Benchmark V1 vs Software Versioning

Benchmark V1 is a **research configuration**.

It is not automatically equivalent to a software release.

```text
Benchmark V1 ≠ software v1.0.0
Benchmark V2 ≠ software v2.0.0
Benchmark V3 ≠ software v3.0.0
Benchmark V4 ≠ software v4.0.0
```

Software release numbering and benchmark numbering are independent.

Do not:

- change package versions because the benchmark is called V1
- infer software maturity from the benchmark number
- interpret Benchmark V1 as release `1.0.0`

---

## 5. Scope Summary

| Component                          | Canonical V1 Scope                                                  |
| ---------------------------------- | ------------------------------------------------------------------- |
| Primary purpose                    | Stable classical registration baseline                              |
| Search scope                       | Known-overlap local source/reference pair                           |
| Primary input                      | Registration-ready 2D imagery                                       |
| Global localization                | Excluded                                                            |
| Whole-Moon retrieval               | Excluded                                                            |
| Preprocessing                      | Minimal, generic, deterministic                                     |
| Primary feature method             | SIFT                                                                |
| RootSIFT                           | Optional only if explicitly standardized as a named configuration   |
| ORB                                | Optional secondary baseline, kept separate                          |
| Learned features                   | Excluded                                                            |
| Descriptor matching                | Conventional classical descriptor matching                          |
| Match filtering                    | Simple deterministic filtering                                      |
| Geometric verification             | RANSAC                                                              |
| Verified output                    | Model-consistent inliers                                            |
| Transform                          | Affine and/or homography according to fixed benchmark policy        |
| Geometry                           | One global 2D model                                                 |
| Canonical sub-pixel refinement     | Excluded unless already explicitly defined by project specification |
| Sensor-specific advanced routing   | Excluded                                                            |
| Multi-scale reference search       | Excluded                                                            |
| Full IIRS hyperspectral processing | Excluded                                                            |
| DEM-aware geometry                 | Excluded                                                            |
| Global retrieval / FAISS           | Excluded                                                            |
| Core evaluation                    | Candidate matches, inliers, ratio, coverage, error, runtime, status |
| Visualization                      | Registered preview and match/inlier diagnostics                     |
| Failure                            | Valid benchmark result                                              |
| Reproducibility                    | Required                                                            |
| Preferred compute target           | CPU-capable classical baseline                                      |

No threshold values are defined in this scope document. Exact thresholds belong in benchmark configuration or metric specifications.

---

## 6. Input Contract

### 6.1 Known-Overlap Requirement

Canonical V1 assumes the approximate source/reference overlap is already known.

The benchmark unit is conceptually:

```text
Source Image
      +
Known Overlapping Reference Image
```

Examples may involve:

```text
Chandrayaan-2 Source
        +
Corresponding LRO Reference Region
```

The reference pair may be selected using:

- trusted metadata
- known benchmark preparation
- controlled manual preparation
- another reproducible pair-definition process

V1 is not intended to solve global lunar localization.

---

### 6.2 Local Registration Scope

V1 primarily evaluates:

> Given the correct or expected reference region, can a classical pipeline establish reliable local correspondence and estimate a usable geometric transformation?

This deliberately separates:

```text
Global Search Problem
```

from:

```text
Local Registration Problem
```

Global search belongs outside canonical V1.

---

### 6.3 Registration-Ready 2D Input

Canonical V1 should primarily operate on 2D image representations suitable for classical local feature extraction.

Potential examples include:

- OHRC-derived 2D imagery
- TMC-2 imagery
- LRO NAC reference imagery
- LRO WAC reference imagery where appropriate
- explicitly prepared benchmark images

The exact supported products and loaders must be determined from current repository implementation and dataset documentation.

---

### 6.4 Source and Reference Roles

Every V1 pair should clearly distinguish:

- **source image**
- **reference image**

Transform direction must be explicit where it affects evaluation or output.

Do not treat source and reference roles as automatically interchangeable.

---

### 6.5 Metadata

V1 local registration should not require complete planetary metadata merely to execute classical image matching.

However, benchmark records should preserve available scientific metadata where practical, including:

- mission
- instrument
- product identifier
- GSD
- projection
- footprint
- acquisition information
- illumination information

Missing values must not be fabricated.

---

## 7. IIRS Scope in V1

Full IIRS hyperspectral processing is **outside canonical V1**.

Native IIRS processing introduces additional methodological choices such as:

- band selection
- wavelength selection
- PCA
- composites
- spectral preprocessing
- structural representations

Those decisions make the pipeline sensor-specific and move beyond the minimal classical baseline.

Therefore canonical V1 should not require:

```text
Raw IIRS Hyperspectral Cube
        ↓
Band / PCA / Composite Selection
        ↓
Registration
```

as part of the core baseline.

If a deterministic externally prepared IIRS-derived 2D representation is used experimentally, it must be identified clearly as:

> derived 2D benchmark input

and not as proof of native V1 hyperspectral support.

Advanced IIRS representation research belongs primarily in later benchmark configurations.

---

## 8. Scale Handling

Canonical V1 does not include a full physical multi-scale search system.

V1 should not require:

- automated reference-pyramid search
- effective-GSD level selection
- automatic scale-level selection
- coarse-to-fine multi-scale reference retrieval
- scale-aware sensor routing

Those concepts belong primarily in V2.

V1 should nevertheless preserve available source/reference scale metadata so that scale-related failure can be studied later.

A V1 failure caused by a large physical scale difference is scientifically useful baseline information.

---

### 8.1 No False Resolution Recovery

V1 must not claim that resizing or interpolation restores missing physical information.

Conceptually:

```text
Coarse Source
      ↓
Upsampling
      ↓
Larger Raster
```

does not mean:

```text
Recovered Fine Terrain Detail
```

If resizing is used for algorithmic compatibility, document it as a representation change rather than physical resolution enhancement.

---

## 9. Minimal Preprocessing

V1 preprocessing should remain:

- simple
- deterministic where practical
- generic
- documented
- reproducible

Possible baseline operations may include, when appropriate:

- grayscale preparation
- finite-value handling
- basic intensity normalization
- contrast normalization
- optional CLAHE if explicitly standardized
- mild filtering if explicitly justified

Every operation should have a clear purpose.

V1 should not accumulate an elaborate enhancement pipeline merely to improve individual examples.

---

### 9.1 Outside V1 Preprocessing Scope

Canonical V1 should not require:

- learned preprocessing
- neural enhancement
- illumination-conditioned preprocessing
- dedicated OHRC/TMC-2/IIRS optimization branches
- shadow-aware processing
- photometric terrain reconstruction
- large structural-fusion pipelines
- automatic sensor-specific method selection

These are later-version concerns.

---

## 10. Classical Feature Baseline

### 10.1 SIFT

The preferred primary V1 classical feature method is:

> **SIFT**

Its role is to provide:

- keypoint detection
- local descriptors
- classical scale/rotation robustness
- mature and interpretable baseline behavior

V1 must not claim that SIFT is:

- Sun-angle invariant
- modality invariant
- universally robust
- guaranteed across extreme GSD differences

Those limitations are part of what V1 is intended to measure.

---

### 10.2 RootSIFT

RootSIFT may be included if the project explicitly standardizes it.

If both SIFT and RootSIFT are evaluated, treat them as separate named configurations, for example conceptually:

```text
V1 / SIFT
V1 / RootSIFT
```

Do not silently alternate between descriptor variants while presenting results as one identical benchmark configuration.

The canonical baseline configuration must be clear.

---

### 10.3 ORB

ORB may optionally serve as a separate speed-oriented classical comparison.

It should not replace the primary SIFT baseline unless the benchmark specification explicitly changes.

If ORB is evaluated:

- keep results separate
- record its configuration separately
- do not average ORB and SIFT into one undefined "V1 score"

---

## 11. Descriptor Matching

V1 should use a conventional descriptor-matching strategy appropriate to the selected classical descriptor.

Possible mechanisms may include:

- nearest-neighbor matching
- k-nearest-neighbor matching
- ratio filtering
- cross-checking
- mutual consistency

The canonical benchmark should define the selected policy.

Do not silently change matching strategy across benchmark pairs.

Do not invent threshold values in this scope document.

Where appropriate, thresholds should be explicit configuration rather than hidden constants.

---

## 12. Candidate Match Filtering

Matching output before geometric verification should be referred to as:

> **candidate matches**

Filtering may include simple classical mechanisms such as:

- descriptor distance criteria
- ratio filtering
- mutual/cross-check consistency

depending on the canonical configuration.

Keep V1 filtering simple.

Do not introduce:

- learned confidence calibration
- learned match filtering
- complex adaptive matcher selection

into the canonical baseline.

---

## 13. Candidate Matches Are Not Verified Inliers

Before geometric verification:

```text
Descriptor Matcher
        ↓
Candidate Matches
```

Candidate matches may contain:

- correct correspondences
- descriptor ambiguity
- repetitive-crater confusion
- false matches
- accidental structural similarity

Do not call them:

- verified matches
- trusted correspondences
- ground truth

merely because they have a strong descriptor score.

---

## 14. Geometric Verification

RANSAC is the primary robust geometric-verification mechanism for canonical V1.

Conceptually:

```text
Candidate Matches
        ↓
RANSAC
        ↓
Initial Geometric Model
        ↓
Verified Inliers
        +
Rejected Outliers
```

Geometric verification is mandatory for the classical baseline.

Descriptor matching alone is insufficient evidence of reliable registration.

---

### 14.1 Verified Inliers

A candidate correspondence satisfying the selected geometric model within the configured verification criterion becomes a:

> **verified inlier**

A verified inlier is:

> consistent with the selected model and threshold

It is not automatically:

> independently proven physical ground truth

That distinction must remain explicit.

---

## 15. Transformation Models

V1 should use a simple global 2D transformation.

Allowed baseline models may include:

- affine transform
- homography

The canonical benchmark must define a deterministic model policy.

---

### 15.1 Affine Transform

An affine transform may represent:

- translation
- rotation
- scale
- shear

and may be suitable for some locally comparable or already corrected image pairs.

---

### 15.2 Homography

A homography may provide a more flexible global projective alignment.

It can be useful for image registration, but it does not imply that lunar terrain is physically planar.

---

### 15.3 Model Selection Must Be Reproducible

If both affine and homography are supported, the benchmark must not manually select whichever model produces the most attractive result after inspection.

Use one of:

- a fixed canonical model
- a deterministic documented model-selection policy
- separately reported affine and homography configurations

Do not silently choose the best-looking result.

---

## 16. Geometry Explicitly Outside V1

Canonical V1 does not attempt to solve full 3D lunar imaging geometry.

Excluded techniques include:

- DEM-aware registration
- physical camera/sensor models
- piecewise transforms
- local mesh warps
- dense terrain-aware deformation
- global bundle adjustment
- dense 3D reconstruction

V1 intentionally tests how far a simple global 2D model can go.

---

## 17. Sub-Pixel Refinement

Canonical local sub-pixel tie-point refinement should normally remain **outside V1**.

The clean baseline is:

```text
Candidate Matches
        ↓
RANSAC
        ↓
Verified Inliers
        ↓
Global Transform
        ↓
Registration
        ↓
Evaluation
```

rather than:

```text
Verified Inliers
        ↓
Advanced Local Refinement
        ↓
Refined Coordinates
        ↓
Refit
```

The latter introduces another methodological stage whose benefit should be measurable relative to the unrefined baseline.

If repository-approved V1 documentation already defines sub-pixel refinement as canonical V1 behavior, preserve that existing source of truth rather than silently redefining it.

Otherwise, sub-pixel experiments during V1 development should be labeled as non-canonical extensions.

---

## 18. Registration and Warping

After a valid geometric model is estimated, V1 may produce:

- transformed/registered image
- registered preview
- source/reference overlay
- candidate-match visualization
- verified-inlier visualization
- outlier visualization

These outputs are useful for:

- debugging
- human inspection
- demonstrations

They are not sufficient scientific validation by themselves.

---

### 18.1 No Force-Warping

Do not warp an image merely because a numerical transformation matrix can be computed.

A transformation should satisfy the benchmark's validity requirements before being treated as an accepted registration.

If geometric evidence is insufficient:

> reject the result

rather than forcing a visually plausible warp.

---

## 19. Required V1 Outputs

A successful or failed V1 run should make available enough information to inspect and reproduce the outcome.

Relevant output categories include:

### Input Context

- source identifier
- reference identifier
- benchmark pair identity
- available source/reference metadata

### Configuration

- preprocessing configuration
- feature method
- matching method
- filtering policy
- geometric model
- verification configuration

### Feature / Matching Diagnostics

- detected feature/keypoint count where relevant
- candidate match count
- verified inlier count
- rejected outlier information where appropriate
- inlier ratio

### Geometry

- transformation model type
- estimated transformation
- transform validity/status

### Evaluation

- residual/error information
- spatial coverage
- independent check-point error where available
- runtime
- success/rejection/failure status

### Artifacts

- match visualization
- inlier visualization
- registered preview
- overlay where useful

### Failure Information

- explicit failure category/reason

This document does not define exact API, JSON, class, or field names.

Use the repository's actual result contracts.

---

## 20. Core V1 Metrics

V1 should report a consistent baseline metric set where the required data is available.

---

### 20.1 Candidate Match Count

Number of correspondences proposed before geometric verification.

This measures matcher output quantity, not final registration quality.

---

### 20.2 Inlier Count

Number of candidate matches accepted as geometrically consistent with the selected model.

Higher count can be useful but is not sufficient alone.

---

### 20.3 Inlier Ratio

Conceptually:

```text
inlier ratio
=
verified inliers / candidate matches
```

Exact zero-case and aggregation behavior belongs in metrics documentation.

---

### 20.4 Residual / Reprojection Error

Measures disagreement between transformed and observed correspondence positions.

Every reported value must identify its coordinate space and units.

---

### 20.5 Spatial Coverage

Measures how widely verified correspondences are distributed across the relevant overlap.

Possible project metrics may include:

- occupied grid cells
- grid coverage
- convex-hull coverage

The exact formula belongs in metrics documentation.

---

### 20.6 Independent Check-Point RMSE

Where independent check points or suitable validated reference truth exist, check-point RMSE should be preferred as a final registration-accuracy measure.

It evaluates locations not used to fit the transformation.

---

### 20.7 Runtime

Record elapsed processing time when runtime is part of benchmark comparison.

Runtime interpretation requires documented:

- processing scope
- configuration
- relevant hardware/environment context

Do not invent or estimate runtime.

---

### 20.8 Success / Failure

Each benchmark execution should expose whether it:

- succeeded
- was rejected
- failed

according to defined benchmark criteria.

Do not infer success merely because the program completed without an exception.

---

## 21. Evaluation Rules

### 21.1 Fit Points vs Check Points

**Fit points** participate in transformation estimation.

**Check points** are independent evaluation points.

Conceptually:

```text
Fit Points
    ↓
Estimate Transform
```

then:

```text
Independent Check Points
        ↓
Evaluate Final Transform
```

Do not report fit residuals as though they were independent validation.

---

### 21.2 When Check Points Are Unavailable

If a benchmark pair does not contain independent check points or validated ground truth, report only what is genuinely measurable.

Potential diagnostics include:

- fit residuals
- candidate/inlier statistics
- spatial coverage
- registered visualization

State the limitation.

Do not rename these as:

- ground-truth accuracy
- independent RMSE
- absolute registration error

without evidence.

---

### 21.3 Error Units Are Mandatory

Never report:

```text
RMSE = 0.7
```

without defining what `0.7` means.

Potential units include:

- source-image pixels
- reference-image pixels
- projected map units
- metres

---

### 21.4 Source-Image Pixel Error

Where scientifically appropriate, source-image pixels provide a useful baseline error space because they avoid overstating physical accuracy.

For example:

```text
error = N source-image px
```

may be more defensible than converting prematurely to metres.

---

### 21.5 Pixel Error vs Ground Error

Do not equate:

```text
0.5 px
```

with:

```text
0.5 m
```

Ground error depends on:

- physical image scale
- projection
- coordinate geometry
- reference quality

Convert to physical units only when the metric definition supports the conversion.

---

### 21.6 Visual Alignment Is Not Accuracy

A visually convincing overlay is diagnostic evidence, not independent numerical validation.

V1 should not define:

> "looks aligned"

as the success metric.

---

## 22. Spatial Coverage

V1 should measure correspondence distribution, not only correspondence count.

For example:

```text
100 inliers
clustered around one crater
```

may constrain the whole overlap less reliably than:

```text
30 well-distributed inliers
across the overlap
```

Spatial coverage therefore belongs in baseline evaluation.

The exact metric implementation belongs in dedicated metric documentation.

---

## 23. Failure Is a Valid V1 Result

V1 must be allowed to fail.

Possible failure conditions include:

- unreadable input
- invalid raster
- unsupported representation
- insufficient keypoints
- missing/empty descriptors
- insufficient candidate matches
- insufficient RANSAC inliers
- degenerate correspondences
- transform-estimation failure
- invalid/non-finite transform
- poor spatial coverage
- no meaningful overlap
- excessive evaluation error under defined benchmark rules

Failure is valuable baseline information.

A difficult image pair may reveal a genuine limitation of the classical method.

---

## 24. Quality Gates

V1 may use simple deterministic validity checks.

Possible categories include:

- minimum feature availability
- minimum candidate-match support
- minimum inlier support
- transformation validity
- finite residuals
- spatial coverage requirements

This scope document intentionally does not define numerical thresholds.

Thresholds must come from:

- benchmark specification
- metric definitions
- configuration

Do not invent them.

---

## 25. V1 Success Definition

A successful canonical V1 run should conceptually include:

1. valid source/reference input
2. sufficient local feature information
3. candidate correspondence generation
4. geometric verification
5. a valid global transformation
6. registration output
7. measurable evaluation information
8. reproducible configuration/context

If benchmark quality criteria are not satisfied, the run should return an explicit rejection/failure rather than pretending to succeed.

---

## 26. Benchmark Pair Design

V1 should operate on a controlled set of known-overlap source/reference image pairs.

Potential conditions may include:

- straightforward overlap
- moderate illumination difference
- moderate scale difference
- low-feature terrain
- more challenging terrain

The initial benchmark does not need to solve every future stress category before V1 becomes scientifically useful.

A small, carefully verified benchmark is preferable to a large poorly documented one.

The exact number of benchmark pairs is defined by benchmark data/manifest configuration.

Do not invent a pair count.

---

## 27. Preferred V1 Development Order

A practical conceptual order is:

1. identify one verified overlapping lunar pair
2. confirm input loading
3. apply minimal preprocessing
4. detect SIFT features
5. generate descriptors
6. generate candidate matches
7. apply simple match filtering
8. perform RANSAC/geometric verification
9. estimate a global transform
10. generate a registered preview
11. calculate baseline metrics
12. add explicit rejection/failure handling
13. add reproducible configuration
14. expand to a controlled benchmark pair set
15. stabilize the canonical V1 definition before comparing later versions

This is an engineering progression, not a dated release schedule.

---

## 28. Configuration and Parameter Control

V1 should be reproducibly configurable.

Potential configurable concepts include:

- preprocessing choices
- SIFT parameters
- descriptor-matching policy
- candidate-filtering parameters
- ratio threshold
- RANSAC configuration
- transform model
- quality-gate thresholds

Do not invent exact configuration keys or filenames.

Use the repository's actual configuration system.

---

### 28.1 No Hidden Per-Pair Tuning

Canonical V1 results should not rely on manually changing parameters for individual benchmark pairs simply to obtain success.

If an experiment deliberately studies parameter tuning, label it accordingly.

The canonical baseline should use a documented, reproducible policy.

---

## 29. Determinism

V1 should be deterministic where practical.

Some components, such as RANSAC implementations, may involve randomness.

Where applicable:

- control a random seed
- record the seed
- record configuration
- document unavoidable nondeterminism

Do not claim complete determinism when underlying libraries or hardware do not guarantee it.

---

## 30. Reproducibility Requirements

A scientifically useful V1 result should ideally be traceable to:

- source product/item
- reference product/item
- benchmark pair ID
- preprocessing configuration
- feature method
- matching configuration
- filtering configuration
- transformation model
- RANSAC configuration
- benchmark configuration
- random seed where relevant
- code revision where available
- resulting metrics
- runtime environment when runtime matters

Do not invent schema fields if existing repository contracts already define them.

---

## 31. Data and Provenance

Preserve relevant scientific provenance where available.

Potential information includes:

- mission
- instrument
- product identifier
- source/archive
- GSD
- projection
- footprint
- acquisition metadata
- illumination metadata

V1 may not require all metadata for local feature matching, but available context should not be discarded unnecessarily.

Large mission products should not be committed to ordinary Git merely to run V1.

Dataset acquisition and storage policy belong primarily in `DATASETS.md`.

---

## 32. Testing Requirements

V1 implementation should separate software validation from scientific benchmarking.

### Unit Tests

Potential subjects include:

- input validation
- blank/constant image handling
- descriptor edge cases
- matching helpers
- transform helpers
- metric calculations
- configuration validation

### Integration Tests

Potential subjects include:

- compact known-overlap pair
- end-to-end V1 registration path
- transformation output
- result serialization where relevant

### Regression Tests

Add when fixing discovered V1 bugs where practical.

### Benchmark Tests

Use controlled real lunar source/reference pairs for scientific evaluation.

Large mission datasets should not be mandatory for ordinary unit tests.

---

## 33. Failure Tests

Relevant V1 failure tests may include:

- blank image
- constant image
- invalid input
- insufficient keypoints
- empty descriptors
- no candidate matches
- zero geometric inliers
- degenerate correspondence geometry
- transform-estimation failure
- invalid transformation
- non-overlapping pair where suitable

Do not invent expected numerical thresholds.

---

## 34. Dependency Philosophy

Canonical V1 should remain lightweight where practical.

It should not require advanced dependencies merely to strengthen benchmark results.

Canonical V1 should not inherently require:

- large neural-network frameworks
- learned-model downloads
- GPU-only libraries
- vector databases
- large retrieval stacks

unless an existing project-level dependency architecture makes such a dependency unavoidable.

---

## 35. CPU-Capable Baseline

V1 should preferably remain runnable on a normal CPU development environment.

GPU acceleration should not be required for the canonical classical baseline.

This helps V1 remain:

- reproducible
- accessible
- testable
- suitable for lightweight CI
- easier to compare across systems

Do not make unmeasured runtime guarantees.

---

## 36. Output Artifacts

Potential V1 artifacts include:

- candidate-match visualization
- inlier/outlier visualization
- registered image
- registered preview
- overlay
- transformation record
- metrics report
- benchmark result record

Artifacts are outputs.

They are not source datasets.

Numerical result artifacts must not be manually edited to improve benchmark appearance.

---

## 37. Logging

Useful diagnostic information may include:

- current pipeline stage
- keypoint count
- candidate match count
- verified inlier count
- model-estimation status
- rejection/failure reason
- runtime

Avoid:

- huge array dumps
- complete image dumps
- excessive per-feature logging

Follow existing repository logging conventions.

---

# 38. Included in V1

Canonical V1 includes:

- known-overlap source/reference image pairs
- registration-ready 2D input representations
- input validation
- minimal generic preprocessing
- SIFT classical feature baseline
- RootSIFT only when explicitly standardized as a named V1 configuration
- optional ORB secondary baseline when kept separate
- conventional descriptor matching
- simple deterministic candidate filtering
- explicit candidate-match terminology
- RANSAC geometric verification
- verified-inlier extraction
- rejected-outlier identification where available
- one simple global 2D transform
- affine and/or homography according to fixed policy
- transformation validity checks
- image warping/registration
- registered diagnostic preview
- candidate match count
- inlier count
- inlier ratio
- spatial coverage
- residual/error reporting
- independent check-point RMSE where check points exist
- runtime measurement where benchmarked
- explicit success/rejection/failure status
- reproducible configuration
- benchmark pair identity
- scientific provenance where available
- software tests
- controlled scientific benchmark execution

---

# 39. Explicitly Excluded from Canonical V1

The following are outside canonical V1 unless the repository's approved benchmark definition explicitly states otherwise.

## Search and Retrieval

- whole-Moon visual search
- global lunar localization
- learned global descriptors
- FAISS
- vector retrieval databases
- Top-K global retrieval
- automated reference-tile search
- large-scale retrieval indexing

## Advanced Scale Handling

- automatic multi-scale reference search
- full reference-pyramid candidate search
- automatic effective-GSD selection
- scale-conditioned routing

## Advanced Sensor Processing

- dedicated sensor-optimized routing
- sophisticated OHRC-specific pipelines
- sophisticated TMC-2-specific pipelines
- native/full hyperspectral IIRS processing
- advanced IIRS band-selection research
- PCA optimization for IIRS
- complex spectral composites
- sensor-conditioned matcher selection

## Learned Matching

- ALIKED
- LightGlue
- LoFTR
- SuperPoint
- SuperGlue
- other learned local-feature systems
- learned correspondence filtering
- learned confidence models

## Advanced Remote-Sensing Matching

- RIFT
- CFOG
- other advanced multimodal matchers as part of the canonical baseline

## Advanced Geometry

- canonical sub-pixel tie-point refinement
- DEM-aware registration
- terrain-aware warping
- piecewise transformations
- local mesh warps
- dense optical flow as the canonical registration method
- sensor physical camera models
- multi-image bundle adjustment
- dense 3D reconstruction

## Advanced Decision Systems

- learned confidence calibration
- automated matcher selection
- advanced uncertainty modeling
- complex accept/refine/reject policy learning
- advanced failure classifiers

## Downstream Applications

- whole-Moon mosaic production
- interactive Moon map UI as a scientific V1 requirement
- 3D lunar visualization as a V1 requirement
- Mars support
- Venus support
- general planetary registration support

These may belong to V2, V3, V4, or downstream demonstrations.

---

# 40. V1 → V2 Boundary

V2 begins where the system moves beyond the generic classical baseline into **sensor-aware and physically scale-aware processing**.

Expected V2 research areas may include:

- sensor routing
- sensor-specific preprocessing
- OHRC/TMC-2 preparation differences
- IIRS-derived 2D representation research
- physical GSD awareness
- reference pyramids
- multi-scale correspondence
- stronger illumination handling
- structural preprocessing
- improved failure analysis

V1 should preserve failures that motivate those improvements rather than absorbing them into the baseline.

---

# 41. V1 → V3 Boundary

V3 may investigate advanced local matching and large-area retrieval.

Potential V3 areas include:

- ALIKED + LightGlue
- LoFTR
- advanced remote-sensing correspondence methods
- global descriptors
- reference tiling
- multi-scale retrieval data
- FAISS indexing
- Top-K candidate retrieval

These do not belong in canonical V1.

---

# 42. V1 → V4 Boundary

V4 may investigate research-grade robustness beyond the earlier benchmark configurations.

Potential V4 areas include:

- DEM-aware geometry
- local/piecewise refinement
- uncertainty estimation
- confidence calibration
- advanced quality gates
- matcher-selection strategies
- advanced failure classification
- scalable retrieval
- robust research orchestration

V1 should not be back-edited to absorb these techniques.

---

# 43. Baseline Stability

Once the canonical V1 benchmark is established, its methodology should remain stable enough to support longitudinal comparison.

Avoid silently changing:

- preprocessing
- feature method
- descriptor representation
- matching policy
- thresholds
- transform policy
- metric definitions
- benchmark pair membership

after baseline results have been established.

If a material change is necessary, document the comparability impact.

---

## 43.1 Freeze Principle

A baseline must eventually become stable.

After V1 is sufficiently defined:

```text
V1 Methodology
      ↓
Freeze / Control Changes
      ↓
Compare V2 / V3 / V4 Against It
```

Continuous retrofitting destroys the ability to determine whether later methods improved anything.

---

## 43.2 Bug Fix vs Methodology Change

Distinguish carefully.

### Bug Fix

Example:

> Incorrect source/reference coordinate order produced invalid transformation results.

Fixing this restores intended V1 behavior.

### Methodology Change

Example:

> Replace SIFT with LightGlue.

This changes the scientific method and should not silently become the same V1 benchmark.

Methodology changes may require:

- a benchmark revision
- a separately named configuration
- movement to another benchmark version
- documented comparability break

depending on project policy.

---

## 44. V1 Change Control

When a change materially affects canonical V1 results, document:

- what changed
- why it changed
- whether old and new results remain comparable
- whether benchmark reruns are required

Relevant updates may affect:

- benchmark documentation
- configuration
- result metadata
- changelog

Do not invent a separate bureaucracy or revisioning system unless the repository defines one.

---

# 45. Definition of Done

Benchmark V1 can be considered functionally complete when the project has sufficient evidence for all applicable items below.

- [ ] A canonical V1 pipeline is documented.
- [ ] V1 input expectations are defined.
- [ ] V1 output expectations are defined.
- [ ] At least one validated known-overlap lunar pair can execute through the complete classical baseline.
- [ ] SIFT or the explicitly selected canonical classical feature configuration is defined.
- [ ] Candidate matches are generated.
- [ ] Candidate matches are not mislabeled as verified correspondences.
- [ ] RANSAC/geometric verification is performed.
- [ ] Verified inliers are identifiable.
- [ ] A valid global transformation can be estimated when evidence supports one.
- [ ] Invalid/degenerate transformations are rejected.
- [ ] Registration output can be generated for successful cases.
- [ ] A registered preview/diagnostic can be produced.
- [ ] Candidate match count is reported.
- [ ] Inlier count is reported.
- [ ] Inlier ratio is reported.
- [ ] Spatial match distribution is evaluated.
- [ ] Error/residual information is available.
- [ ] Independent check-point error is used where valid check points exist.
- [ ] Error units are explicit.
- [ ] Runtime can be recorded where benchmarked.
- [ ] Failure/rejection is represented explicitly.
- [ ] Relevant software tests exist.
- [ ] Important failure cases are tested.
- [ ] Benchmark pair/configuration information is reproducible.
- [ ] Scientific data provenance is preserved where available.
- [ ] Result outputs are machine-readable or consistently documented where repository architecture requires them.
- [ ] No fake accuracy, confidence, or benchmark values remain.
- [ ] No advanced V2/V3/V4 method is required to make canonical V1 operate.
- [ ] V1 methodology is stable enough to serve as the comparison baseline for later benchmark configurations.

V1 completion does not require a particular number of benchmark pairs unless another authoritative project specification defines one.

---

## 46. Weak Accuracy Does Not Mean V1 Is Incomplete

V1 may perform poorly on difficult cases.

That is scientifically acceptable.

For example, V1 may expose weaknesses under:

- large GSD differences
- strong Sun-angle changes
- repetitive terrain
- weak-feature regions
- cross-modality inputs

If the pipeline:

- behaves correctly
- rejects invalid results appropriately
- reports its metrics honestly
- preserves reproducibility
- exposes failure modes

then poor performance can be a valid baseline finding.

The purpose of later versions is to determine whether those weaknesses can be improved.

---

# 47. Relationship with Other Project Context

## `PROJECT_CONTEXT.md`

Defines:

> What ChandraMap is and what the overall project is trying to accomplish.

This document defines:

> What Benchmark V1 specifically includes and excludes.

---

## `DOMAIN_CONTEXT.md`

Defines scientific background such as:

- lunar illumination
- scale/GSD
- sensor differences
- correspondence
- geometry

This document applies those constraints specifically to V1 scope.

---

## `TERMINOLOGY.md`

Provides canonical language.

V1 documentation should consistently use terms such as:

- source image
- reference image
- candidate match
- verified inlier
- outlier
- tie point
- fit point
- check point
- transformation
- registration
- RMSE
- spatial coverage

---

## `DATASETS.md`

Defines:

- instruments
- scientific products
- metadata
- provenance
- dataset roles

This document defines only what categories of input belong in V1.

---

## Pipeline Documentation

Should define:

> How the V1 stages are implemented and connected.

This file defines:

> Which stages belong in V1.

---

## Metrics Documentation

Should define exact:

- equations
- units
- aggregation
- thresholds
- edge-case handling

This file identifies only the metric categories V1 requires.

---

## `ROADMAP.md`

Defines planned project evolution.

A roadmap milestone does not prove that V1 is currently implemented.

Current implementation status must be verified from:

- code
- tests
- configuration
- benchmark definitions
- actual results

---

# 48. Key V1 Rules for AI Agents

1. V1 is the **classical baseline**, not the strongest ChandraMap pipeline.

2. V1 exists to provide a stable reference for later benchmark configurations.

3. Benchmark V1 is not software release `v1.0.0`.

4. V1 assumes a known-overlap source/reference image pair.

5. V1 is primarily a **local registration** benchmark.

6. Whole-Moon/global visual retrieval does not belong in canonical V1.

7. FAISS does not belong in canonical V1.

8. Top-K global reference retrieval does not belong in canonical V1.

9. V1 primarily operates on registration-ready 2D imagery.

10. Full native IIRS hyperspectral processing does not belong in canonical V1.

11. A pre-derived deterministic IIRS 2D representation does not imply native V1 hyperspectral support.

12. Advanced sensor-aware routing belongs primarily in V2.

13. Multi-scale reference-pyramid search belongs primarily in V2.

14. V1 preprocessing should remain minimal and reproducible.

15. SIFT is the primary classical feature baseline unless the authoritative benchmark specification says otherwise.

16. RootSIFT should be treated as a clearly named configuration if used.

17. ORB should remain a separate optional classical comparison if used.

18. ALIKED does not belong in canonical V1.

19. LightGlue does not belong in canonical V1.

20. LoFTR does not belong in canonical V1.

21. RIFT and CFOG do not belong in the canonical minimal V1 baseline.

22. Learned feature and matching methods belong in later controlled comparisons.

23. Matcher output is called **candidate matches** before geometric verification.

24. Candidate matches must not be called verified merely because descriptor confidence is high.

25. RANSAC provides the canonical V1 geometric-verification stage.

26. RANSAC inliers are model-consistent correspondences, not automatically ground truth.

27. Use a deterministic affine/homography policy for reproducible comparison.

28. Do not manually select whichever transform makes a benchmark pair look better.

29. DEM-aware geometry does not belong in V1.

30. Piecewise/local terrain warping does not belong in V1.

31. Canonical sub-pixel tie-point refinement should remain outside V1 unless an existing authoritative specification explicitly includes it.

32. Registered previews are diagnostic artifacts, not proof of accuracy.

33. More candidate matches do not automatically mean better registration.

34. More inliers do not automatically mean better registration.

35. Spatial distribution of verified inliers matters.

36. Independent check points are preferred for final accuracy evaluation where available.

37. Fit-point RMSE is not independent registration accuracy.

38. Always report error units.

39. Distinguish source-image pixel error from reference-image pixel error.

40. Sub-pixel image-space error is not automatically sub-metre ground error.

41. Registration rejection/failure is a valid V1 result.

42. Do not force a transform when geometric evidence is insufficient.

43. Exact benchmark thresholds must come from configuration/specification, not guesses.

44. Do not invent pair counts, thresholds, runtime, accuracy, or success rates.

45. Avoid hidden per-pair manual tuning in canonical benchmark runs.

46. Preserve benchmark configuration and data provenance.

47. Keep the canonical V1 baseline CPU-capable where practical.

48. Do not add heavyweight ML or retrieval dependencies merely to improve V1 results.

49. Keep unit tests separate from scientific benchmark execution.

50. Do not require whole-Moon mission datasets for ordinary V1 unit testing.

51. Once V1 is stable, do not change its methodology silently.

52. Distinguish bug fixes from benchmark-methodology changes.

53. Document any V1 change that breaks comparability with previous results.

54. Weak V1 performance on difficult pairs can be a valid scientific baseline result.

55. Later benchmark versions should improve on V1 without redefining what V1 means.

<!-- Source specification: :contentReference[oaicite:0]{index=0} -->
