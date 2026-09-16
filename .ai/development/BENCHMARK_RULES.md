# ChandraMap Benchmark Rules

This document defines the governance and methodology rules that make ChandraMap benchmarks scientifically meaningful, reproducible, comparable, auditable, and resistant to cherry-picking.

It answers:

> **What rules must a ChandraMap benchmark follow before its results can be trusted or compared with another method?**

The central benchmark principle is:

```text
Same Data
+
Same Evaluation
+
Documented Configuration
+
Visible Failures
+
Traceable Results
=
Meaningful Comparison
```

A benchmark is useful only when the comparison is controlled.

Do not attribute an improvement to one algorithm when several uncontrolled variables changed at the same time.

---

## 1. Purpose

`BENCHMARK_RULES.md` defines common rules for:

- benchmark design
- V1–V4 comparison
- benchmark identity
- data and pair selection
- ground truth
- fit/check-point separation
- data-leakage prevention
- preprocessing fairness
- scale/GSD fairness
- matcher comparison
- geometric-verification fairness
- refinement fairness
- retrieval evaluation
- runtime and hardware comparison
- randomness and repeated runs
- metric usage
- aggregation
- failure handling
- benchmark result records
- reproducibility
- benchmark revisions
- negative results
- reporting
- benchmark integrity

This file defines **benchmark governance**.

It does not define:

- exact metric formulas
- exact V1/V2/V3/V4 implementations
- benchmark result values
- dataset-product specifications
- pipeline architecture
- software-testing strategy
- a performance leaderboard
- a research-paper methodology section

Exact metric mathematics should live in the project's benchmark metrics documentation when present.

Version-specific methodology should live in version-specific benchmark specifications.

---

## 2. Benchmark Principles

A trustworthy ChandraMap benchmark should be:

- controlled
- reproducible
- scientifically valid
- transparent
- traceable
- comparable
- explicit about assumptions
- explicit about units
- explicit about failures
- resistant to cherry-picking
- stable enough for longitudinal comparison

The benchmark should allow another contributor to determine:

1. what data was evaluated
2. which source and reference products were involved
3. which benchmark configuration was used
4. which preprocessing was applied
5. which matcher and geometry were used
6. which ground truth or check points were used
7. which metrics were calculated
8. which units those metrics use
9. which cases failed or were rejected
10. how aggregate values were produced
11. which code/configuration produced the result

If those questions cannot be answered, the result is difficult to interpret scientifically.

---

## 3. Benchmark vs Software Testing

A software test and a scientific benchmark answer different questions.

### Software Test

> **Does the implementation behave correctly?**

Examples:

- Does RMSE computation return the mathematically expected value?
- Does a transform preserve the correct source/reference direction?
- Does an empty correspondence set produce the expected rejection?
- Does result serialization preserve metric units?

### Scientific Benchmark

> **How well does the method perform under controlled lunar-registration conditions?**

Examples:

- What registration error does Benchmark V1 achieve on the defined evaluation pairs?
- How often does a method produce acceptable geometry?
- Does sensor-aware scale handling improve performance on scale-stress cases?
- How well does retrieval recover the correct reference region?

A benchmark failure does not automatically mean the implementation is broken.

A passing software test does not imply that the scientific method performs well.

See [`TESTING_RULES.md`](TESTING_RULES.md) for software/scientific testing standards.

---

## 4. Benchmark V1–V4 Model

ChandraMap uses conceptual research benchmark configurations:

- **Benchmark V1**
- **Benchmark V2**
- **Benchmark V3**
- **Benchmark V4**

These are **not software release versions**.

Do not assume:

```text
Benchmark V1 = software v1.0.0
Benchmark V2 = software v2.0.0
Benchmark V3 = software v3.0.0
Benchmark V4 = software v4.0.0
```

Software releases and benchmark configurations are independent.

The purpose of V1–V4 is not merely to make each version more complex.

The purpose is to determine whether additional capability produces measurable, reproducible improvement.

---

### 4.1 Benchmark V1

V1 is the stable classical baseline.

Conceptually:

```text
Known Overlap
    ↓
Minimal Preprocessing
    ↓
SIFT / RootSIFT Configuration
    ↓
Candidate Matching
    ↓
RANSAC
    ↓
Affine / Homography
    ↓
Registration
    ↓
Evaluation
```

Canonical V1 must remain aligned with [`../context/V1_SCOPE.md`](../context/V1_SCOPE.md).

Do not silently add later-version methods to V1 merely to improve its results.

---

### 4.2 Benchmark V2

V2 may introduce controlled sensor-aware and scale-aware improvements such as:

- sensor routing
- sensor-aware preprocessing
- physically meaningful GSD handling
- reference pyramids
- effective-scale comparison
- structural representations
- IIRS-derived 2D representations
- stronger illumination handling

This file does not define exact V2 methodology.

---

### 4.3 Benchmark V3

V3 may investigate capabilities such as:

- ALIKED + LightGlue
- LoFTR
- remote-sensing matching approaches
- global descriptors
- reference tiling
- vector retrieval
- FAISS
- Top-K candidate search

Not every listed method is automatically mandatory for V3.

The version-specific specification determines the active configuration.

---

### 4.4 Benchmark V4

V4 may investigate research-grade robustness such as:

- advanced local refinement
- piecewise geometry
- DEM-aware geometry
- advanced IIRS processing
- uncertainty estimation
- confidence calibration
- adaptive matcher selection
- stronger quality rejection
- scalable retrieval
- failure classification

V4 is an advanced research configuration.

Do not define it automatically as the "best" version.

---

## 5. Controlled Comparison

A scientific comparison should change the variable under investigation while holding relevant other conditions fixed where possible.

For example, a matcher comparison may use:

```text
Same Pair
+
Same Preprocessing
+
Same Scale Handling
+
Same Transform Model
+
Same Geometric Verification
+
Same Evaluation
+
Different Matcher
```

This makes differences easier to attribute to the matcher.

A weak comparison would be:

```text
Method A:
SIFT
+ one pair set
+ one preprocessing path
+ affine
+ one metric definition

Method B:
LightGlue
+ different pairs
+ different preprocessing
+ homography
+ different evaluation
```

followed by the claim:

> LightGlue improved registration.

Several variables changed, so that conclusion would not be supported.

---

## 6. One Variable at a Time Where Practical

For ablation-style experiments, isolate one meaningful variable where possible.

Examples:

### Matcher Ablation

```text
SIFT
vs
ALIKED + LightGlue
```

while holding fixed:

- data
- scale handling
- transform model
- RANSAC policy
- evaluation

### Scale Ablation

```text
No Reference Pyramid
vs
Reference Pyramid
```

while keeping the matcher and evaluation unchanged.

### Preprocessing Ablation

```text
Raw Registration Representation
vs
Structural Representation
```

under otherwise controlled conditions.

Full V1–V4 system comparisons may change several components.

Such comparisons are valid, but they measure the **combined system configuration**, not the contribution of one individual component.

---

## 7. Benchmark Identity and Manifests

Every canonical benchmark definition should have a stable identity.

Conceptually, it should be possible to determine:

- benchmark version
- benchmark suite or revision
- pair/query population
- dataset version
- benchmark configuration
- metric definition
- software revision
- learned-model/checkpoint identity where relevant

Do not invent an ID format unless repository architecture defines one.

---

### 7.1 Benchmark Manifest

Benchmark membership should preferably be defined through a reproducible manifest or equivalent source of truth.

A manifest may conceptually identify:

- pair identity
- source product
- reference product or region
- sensor pair
- overlap context
- stress category
- ground-truth/check-point source
- split/group
- notes

These are conceptual responsibilities.

Use actual repository schemas where they exist.

Do not invent fields merely to satisfy this list.

---

### 7.2 Do Not Benchmark from Random Folders

Avoid benchmark definitions based on:

> whatever files currently exist in this local directory

Local storage paths may differ between machines.

Benchmark membership should remain reproducible.

The benchmark should identify **scientific items**, not accidental filesystem state.

---

## 8. Data Selection

The same benchmark definition should use the same defined evaluation population when longitudinal comparisons are intended.

Changing benchmark data changes the experiment.

Do not silently change:

- pair membership
- reference products
- source products
- ground truth
- check points
- product-processing level
- stress categories

and then compare aggregate values as though the benchmark population were unchanged.

---

### 8.1 Pair Identity

Every benchmark pair should be traceable to appropriate scientific identity such as:

- source product
- reference product or region
- sensor
- provenance
- expected/known overlap

Avoid relying only on filenames such as:

```text
image1.tif
test2.png
final.png
```

---

### 8.2 Same-Pair Rule

When comparing V1–V4 under a shared registration benchmark, prefer the same defined pair population.

If a method fails:

> record the failure

rather than removing that pair for the failing method.

Do not give each benchmark version a different favorable subset unless the experiment explicitly studies different populations.

---

### 8.3 Easy and Difficult Cases

A useful benchmark should include enough straightforward cases to prove the basic pipeline can function.

It should also include difficult cases that expose limitations.

A benchmark containing only easy cases may exaggerate robustness.

A benchmark containing only extreme failure conditions may not demonstrate basic capability.

---

## 9. Search Mode Must Be Explicit

Registration difficulty changes substantially depending on how the reference region is supplied.

Every relevant benchmark should state its search assumption.

### Known-Overlap Benchmark

Question:

> Given the correct overlapping reference region, how well can the system establish correspondence and registration?

### Metadata-Constrained Benchmark

Question:

> Given trustworthy metadata that restricts the search region, how well does the system localize and register?

### Image-Only Retrieval Benchmark

Question:

> Without useful location metadata, can the system identify the correct reference region through visual retrieval?

### End-to-End Localization Benchmark

Question:

> Can retrieval followed by local registration produce the correct final result?

These are different scientific tasks.

Do not combine them into one undefined performance number.

---

## 10. Metadata Use

Using valid metadata is not cheating unless the benchmark explicitly prohibits it.

If the benchmark permits:

- footprint
- approximate location
- projection
- sensor identity
- acquisition metadata

state that assumption.

If the benchmark evaluates image-only retrieval:

> do not leak location metadata into candidate selection.

The benchmark methodology determines what information is legitimately available to the method.

---

## 11. Ground Truth and Evaluation Data

Ground truth should have identifiable provenance.

Possible sources may include:

- challenge-provided truth
- independently validated check points
- trusted geospatial reference
- carefully validated manual annotations

Do not use matcher output as its own ground truth.

Do not treat RANSAC inliers automatically as independent truth.

---

### 11.1 Fit Points

Fit points are correspondences used to estimate the transformation.

Conceptually:

```text
Fit Points
    ↓
Estimate Transform
```

---

### 11.2 Check Points

Check points are independent evaluation points.

Conceptually:

```text
Independent Check Points
        ↓
Evaluate Final Transform
```

Where independent accuracy is claimed, check points must remain separate from the fitting population.

---

### 11.3 Fit-Point Error

Fit-point residuals are still scientifically useful.

They may reveal:

- numerical fit
- model consistency
- outliers
- systematic residual structure

However:

> **Fit-point error is not automatically independent registration accuracy.**

Do not label fit-point RMSE as independent ground-truth accuracy unless the benchmark methodology genuinely supports that interpretation.

---

### 11.4 Ground-Truth Quality

Ground truth itself may contain uncertainty.

Where known, document relevant properties such as:

- annotation source
- coordinate convention
- reference quality
- expected accuracy
- limitations

Do not report apparent precision finer than the benchmark truth can support.

---

## 12. Data Leakage Prevention

Benchmark evaluation becomes invalid when unavailable evaluation information influences the method.

Potential leakage includes:

- ground-truth error used to choose the matcher
- check points used to estimate the transform
- final test RMSE used to tune the RANSAC threshold per pair
- correct retrieval tile used to filter search results
- test images used during learned-model tuning
- manual parameter adjustment after inspecting final results

Leakage must be prevented or explicitly disclosed.

---

### 12.1 Ground-Truth Leakage

Ground truth should be used for evaluation according to benchmark design.

It should not secretly influence:

- candidate search
- transform fitting
- parameter selection
- matcher selection
- refinement selection

unless the experiment is explicitly an oracle analysis.

---

### 12.2 Test-Set Tuning

Do not repeatedly optimize configuration on the final benchmark test population and then describe the result as unbiased final performance.

Where tuning is required, use:

- training/development data
- validation data
- a separate development benchmark

where scientifically appropriate.

---

### 12.3 Geographic Leakage

For lunar imagery, random image-level splitting may leak geographic content.

Potential leakage includes:

- neighboring crops from the same crater region
- overlapping tiles
- multiple scales of the same region
- source/reference crops containing nearly identical terrain

Where independent geography matters, define and verify geographic separation.

---

### 12.4 Observation Leakage

Different crops may originate from the same observation.

Where independence matters, preserve parent-product or observation identity.

---

### 12.5 Augmentation Leakage

Do not place:

```text
Original Observation
→ test set
```

and:

```text
Augmented Version of Same Observation
→ training set
```

when claiming independent generalization.

---

## 13. Stress Categories

A benchmark suite may classify cases by scientifically meaningful difficulty factors.

Potential categories include:

- comparable/easier conditions
- Sun-angle stress
- scale stress
- modality stress
- geometry/relief stress
- low-feature terrain
- partial overlap
- retrieval stress

Do not invent category counts.

Use only categories actually defined by the benchmark.

---

### 13.1 Difficulty Should Be Physically Interpretable

Avoid defining:

> hard case = method failed

because this is circular.

Prefer criteria based on properties such as:

- large GSD difference
- large illumination difference
- cross-modality pair
- low-feature terrain
- strong relief
- partial overlap

Difficulty definitions should ideally remain independent of one specific method's performance.

---

## 14. Metrics

This document defines **how metrics must be governed and compared**.

Exact formulas and edge-case behavior belong in the authoritative metrics documentation when present.

Potential metric families include the following.

---

### 14.1 Correspondence Metrics

Examples:

- candidate match count
- verified inlier count
- inlier ratio

These describe correspondence support.

They do not by themselves establish final registration accuracy.

---

### 14.2 Spatial-Quality Metrics

Examples:

- grid coverage
- convex-hull coverage

These help determine whether correspondences constrain a meaningful portion of the overlap.

---

### 14.3 Registration-Accuracy Metrics

Examples:

- residuals
- source-image pixel error
- reference-image pixel error
- independent check-point RMSE
- ground error where scientifically meaningful

---

### 14.4 Retrieval Metrics

Examples:

- Recall@1
- Recall@5
- Recall@K

Retrieval metrics measure candidate-region discovery.

They do not measure precise geometric registration.

---

### 14.5 Robustness Metrics

Examples:

- success rate
- rejection rate
- failure rate
- unsupported-case rate where useful

---

### 14.6 Efficiency Metrics

Examples:

- preprocessing runtime
- retrieval runtime
- matching runtime
- geometry runtime
- full-pipeline runtime
- memory usage where relevant

Do not define unnecessary metrics merely because they can be measured.

---

## 15. Metric Stability

If metric methodology changes, historical results may no longer be directly comparable.

Potential breaking changes include:

- changing fit-point RMSE to check-point RMSE
- changing source-image pixels to reference-image pixels
- changing spatial-coverage calculation
- changing failure handling
- changing retrieval ground truth
- changing aggregation population

When this occurs, document:

- what changed
- why
- which historical results are affected
- whether reruns are required

Do not silently compare results produced under different metric semantics.

---

## 16. Units and Accuracy Reporting

Every numerical accuracy metric must include units where units are meaningful.

Bad:

```text
RMSE = 0.8
```

Better, when correct:

```text
RMSE = 0.8 source-image px
```

Avoid unitless performance tables.

---

### 16.1 Source vs Reference Pixels

Source-image and reference-image pixel spaces may have different physical meaning.

Do not silently switch between them.

A benchmark report should identify the coordinate space in which an error is measured.

---

### 16.2 Ground Error in Metres

Report metres only when:

- valid spatial metadata exists
- conversion is scientifically meaningful
- projection/geometry supports the conversion
- the benchmark definition permits it

Do not multiply a pixel value by a broad approximate instrument specification and present the result as precise ground truth.

---

### 16.3 Sub-Pixel vs Sub-Metre

`Sub-pixel` means:

> less than one pixel in the specified image coordinate space

It does not automatically mean:

> less than one metre on the lunar surface

Keep these claims separate.

---

## 17. Correspondence Evidence

### Inlier Count

Answers:

> How many candidate correspondences are consistent with the selected geometric model?

It does not answer:

> How accurate is the final registration?

---

### Inlier Ratio

Useful for measuring geometric consistency, but incomplete.

A high inlier ratio may still represent weak evidence when:

- very few candidates exist
- inliers are spatially clustered
- the model is poorly constrained

---

### Spatial Coverage

A benchmark should not evaluate correspondence quality only by count.

Conceptually:

```text
100 inliers around one crater
```

may provide weaker global support than:

```text
30 well-distributed inliers
```

Coverage should be reported where the benchmark defines it.

---

### Residuals

Residual magnitude and direction can reveal:

- geometric mismatch
- terrain effects
- projection issues
- systematic distortion
- local registration problems

When residual structure matters, do not reduce all diagnostics to a single scalar only.

---

## 18. Failure and Rejection Policy

Failure handling must be defined before interpreting benchmark results.

A benchmark should distinguish scientifically different outcomes.

Conceptually:

### Success

The method produced a result satisfying benchmark acceptance criteria.

### Rejected

The method produced candidate geometry but the configured quality policy did not accept it.

### Failed

The configuration supports the case but did not produce a valid scientific result.

### Unsupported

The benchmark configuration intentionally does not support the input/capability.

### Invalid Case

The benchmark item itself violates the benchmark definition or contains invalid truth/data.

### Not Run

No valid benchmark execution occurred.

Use repository-defined status semantics if they already exist.

Do not invent schema values merely from this conceptual taxonomy.

---

### 18.1 N/A vs Failed vs Not Run

Keep these different:

| Status      | Meaning                                        |
| ----------- | ---------------------------------------------- |
| `N/A`       | Metric/case does not apply                     |
| Failed      | Method attempted but did not succeed           |
| Unsupported | Method configuration does not support the case |
| Invalid     | Benchmark case/run is invalid                  |
| Not Run     | No benchmark execution occurred                |

Do not encode these as numerical zero.

---

### 18.2 Failed Run vs Invalid Run

A **failed run** means:

> The method executed correctly but failed scientifically.

An **invalid run** means the experiment was compromised by something such as:

- software bug
- corrupted input
- incorrect configuration
- incorrect metric
- wrong transform direction
- infrastructure failure

Invalid runs should not automatically count as algorithmic failures.

---

### 18.3 Infrastructure Failure

Examples include:

- missing external file
- corrupted checkpoint
- out-of-disk condition
- unintended resource exhaustion
- broken environment

These should be recorded separately from scientific registration failure unless the benchmark specifically evaluates the resource constraint.

---

## 19. Failure Visibility

Failed or rejected benchmark cases must remain visible.

Do not remove a pair because:

- no matches were found
- RANSAC failed
- retrieval missed
- error was high
- quality gates rejected the result

unless the case itself is proven invalid under the benchmark specification.

Failures are part of performance.

---

### 19.1 Success-Rate Denominator

Success rate must use the benchmark-defined population.

Do not compute:

```text
successful cases / successful cases
```

after silently removing failures.

---

### 19.2 Conditional Accuracy

If accuracy is reported only for successful or accepted registrations, say so.

For example:

> RMSE among accepted registrations

is different from:

> RMSE across the complete benchmark population.

When failed cases cannot have an RMSE, report failure/success information separately.

---

## 20. Benchmark Configuration

Every canonical benchmark run should use explicit, reproducible configuration.

Potential configuration areas include:

- preprocessing
- scale handling
- matcher
- correspondence filtering
- RANSAC
- transformation model
- refinement
- retrieval
- quality gates

Use the repository's actual configuration mechanism.

Do not invent configuration keys or files in benchmark documentation.

---

### 20.1 Effective Configuration

Where practical, preserve the effective configuration used for a benchmark run.

This helps distinguish:

```text
nominal benchmark specification
```

from:

```text
actual values used during execution
```

Do not invent a serialization format if the repository has not defined one.

---

### 20.2 No Hidden Per-Pair Tuning

Canonical benchmark runs should avoid undocumented manual tuning per test pair.

Bad example:

```text
Pair A → threshold changed after observing final error
Pair B → matcher manually replaced
Pair C → different transformation selected after visual inspection
```

Pair-specific behavior may be valid when it is:

- an explicitly defined method
- based only on information available at inference time
- reproducible
- documented

---

### 20.3 Oracle Tuning

Ground-truth-assisted selection may be useful as an upper-bound experiment.

If the benchmark chooses the best method/configuration per pair using final truth, label the result explicitly as:

> **Oracle analysis**

Do not present it as normal system performance.

---

## 21. Preprocessing Fairness

Preprocessing can materially affect benchmark results.

Every comparison should make clear whether methods receive:

- identical preprocessing
- method-specific required preprocessing
- sensor-specific representations
- independently optimized preprocessing

If one method receives substantially improved input representation, the benchmark is comparing the **full pipelines**, not only the matchers.

Do not attribute the full improvement to the matcher alone.

---

## 22. Scale and GSD Fairness

Scale handling is an important experimental variable in ChandraMap.

Document:

- source scale context
- reference scale context
- resampling
- pyramid usage
- level selection
- known or searched scale assumptions

Do not hide scale-preparation differences.

---

### 22.1 Upsampling

If a benchmark upsamples imagery, describe it as:

- interpolation
- resampling
- representation resizing

Do not describe it as physical resolution improvement.

---

### 22.2 Reference Pyramid

If a method uses a reference pyramid, record relevant methodology such as:

- how levels are generated
- whether a level is fixed
- whether levels are searched
- how level selection occurs

Do not secretly select the level producing the lowest final ground-truth error per pair unless explicitly evaluating an oracle condition.

---

### 22.3 V1 vs V2 Scale Comparison

If V2 intentionally introduces better scale handling than V1, that difference is a valid experimental variable.

Document it explicitly.

Do not describe the outcome as proving a matcher improvement if the primary change was scale handling.

---

## 23. Sensor and Modality Rules

Benchmark interpretation should preserve instrument identity.

Relevant sensor contexts include:

- OHRC
- TMC-2
- IIRS
- LRO NAC
- LRO WAC

Do not average fundamentally different sensor/modality groups without preserving subgroup interpretation where the benchmark population supports it.

---

### 23.1 OHRC

When benchmarked, preserve appropriate product identity and scale context.

Do not treat one broad OHRC resolution approximation as exact metadata for every product.

---

### 23.2 TMC-2

Use the canonical instrument name:

> **TMC-2**

Preserve TMC-2-specific scale and product context where relevant.

---

### 23.3 IIRS

IIRS is hyperspectral / imaging-infrared data.

If the benchmark uses a derived 2D representation, record the method.

Possible representations may include:

- selected band
- PCA component
- composite
- structural representation

Two IIRS experiments using different 2D representations are different configurations.

---

### 23.4 IIRS Representation Selection

Do not select the best IIRS band/component by repeatedly evaluating final test accuracy unless the experiment explicitly studies an oracle upper bound.

Where possible, representation selection should be determined using:

- prior methodology
- training/development data
- validation data

before final test measurement.

---

### 23.5 NAC vs WAC

LRO NAC and WAC are not interchangeable reference products.

If methods use different reference products, document that experimental difference.

---

### 23.6 Product Processing State

Differences such as:

- calibrated
- map-projected
- orthorectified
- derived

may alter benchmark difficulty.

Preserve product-processing context where relevant.

---

## 24. Matcher Comparison Rules

Matcher labels must identify materially different configurations.

Avoid vague labels such as:

- Basic
- Advanced
- Best

Prefer technically meaningful labels where those configurations actually exist.

---

### 24.1 SIFT / RootSIFT

The benchmark must define whether the classical configuration uses:

- SIFT descriptors
- RootSIFT-transformed descriptors

Do not silently switch between them under the same benchmark label.

---

### 24.2 ALIKED + LightGlue

Preserve component roles:

```text
ALIKED
→ sparse feature extraction

LightGlue
→ sparse feature matching
```

When reporting performance of the combination, do not attribute the result solely to LightGlue if the feature extractor is part of the configuration.

---

### 24.3 LoFTR

LoFTR is detector-free correspondence matching.

It may produce output through a different internal process than sparse detector/descriptor methods.

For benchmark comparison, normalize its downstream correspondence output only where scientifically meaningful.

Do not pretend it uses the same internal stages as SIFT or ALIKED.

---

### 24.4 Remote-Sensing Matchers

Methods such as:

- RIFT
- CFOG

should be treated as explicit research/benchmark configurations when used.

Their presence in project documentation does not imply implementation or superiority.

---

## 25. Geometry and RANSAC Fairness

Geometry configuration materially affects results.

Relevant controlled variables may include:

- transformation model
- robust-estimation method
- RANSAC threshold/configuration
- degeneracy checks
- correspondence filtering

Do not compare matchers while silently giving them different geometric conditions unless that difference is part of the intended system comparison.

---

### 25.1 Transformation Model Fairness

If one matcher uses affine geometry and another uses homography, then the experiment changes:

- matcher
- transformation model

If the research question concerns the matcher alone, hold the transformation model fixed where scientifically appropriate.

---

### 25.2 RANSAC Fairness

Do not use more permissive geometric verification only for the method whose result you want to improve.

If method-specific geometric settings are required, document and justify them.

---

### 25.3 RANSAC Inliers

RANSAC inliers are model-consistent correspondences.

They are not automatically ground truth.

---

## 26. Refinement Fairness

If one pipeline includes sub-pixel refinement and another does not, say so.

The comparison may then be:

> pipeline vs pipeline

rather than:

> matcher vs matcher

Avoid attributing the full difference to correspondence generation alone.

---

### 26.1 Refinement Order

Where refinement exists, preserve the canonical processing relationship:

```text
Candidate Matches
        ↓
RANSAC / Geometric Verification
        ↓
Verified Inliers
        ↓
Sub-Pixel Refinement
        ↓
Final Transform Refit
```

unless an explicitly documented experiment tests another ordering.

---

### 26.2 Evaluate the Final Transform

Benchmark accuracy should evaluate the transformation actually used for final registration.

Do not:

- evaluate the initial RANSAC transform
- refine the tie points
- display a refined registration
- claim the initial transform's metric describes the final result

The evaluated transform and displayed registration should correspond to the same intended pipeline state.

---

## 27. Retrieval Benchmarks

Retrieval and registration should be evaluated separately where possible.

### Retrieval Question

> Did the system retrieve the correct candidate region?

### Registration Question

> Given a candidate region, did the system establish accurate geometry?

### End-to-End Question

> Did retrieval plus registration produce a correct final localization?

These questions should not share one undefined score.

---

### 27.1 FAISS

If FAISS is used, document it as a vector indexing/search component.

Conceptually:

```text
Reference Tiles
      ↓
Global Descriptors
      ↓
FAISS Index

Query
      ↓
Global Descriptor
      ↓
FAISS Search
      ↓
Top-K Candidate IDs
```

FAISS does not perform:

- local feature extraction
- local correspondence
- RANSAC
- registration

---

### 27.2 Top-K

Where Top-K candidate retrieval is evaluated:

- define the correct candidate-region truth
- preserve candidate ranking
- calculate retrieval metrics according to the metrics specification

Do not invent a default `K`.

---

### 27.3 Recall@K vs RMSE

Keep these metrics distinct.

```text
Recall@K
→ Did retrieval include the correct reference region?
```

```text
Registration RMSE
→ How accurately was geometry aligned?
```

High Recall@K does not prove precise registration.

Low RMSE on a supplied correct tile does not prove retrieval capability.

---

### 27.4 Offline Retrieval Cost

Potential offline work includes:

- reference tiling
- pyramid creation
- global descriptor generation
- vector-index construction

---

### 27.5 Online Retrieval Cost

Potential online work includes:

- query preprocessing
- query descriptor generation
- vector search
- candidate selection
- local registration

Do not hide substantial offline computation when discussing system scalability.

---

## 28. Runtime and Hardware Benchmarking

Runtime comparisons must use clearly defined scope.

Possible timings include:

- preprocessing only
- retrieval only
- local matching only
- geometry only
- refinement only
- registration only
- end-to-end pipeline

Do not compare:

```text
matcher-only runtime
```

against:

```text
complete end-to-end runtime
```

as though they represent equivalent work.

---

### 28.1 Hardware Context

Where runtime is compared, preserve materially relevant context such as:

- CPU
- GPU/accelerator
- input dimensions
- model/checkpoint
- batching/concurrency
- cache state
- timing scope

Do not invent a mandatory hardware-record format.

---

### 28.2 Hardware Fairness

If one method runs on GPU and another on CPU:

> state it.

Do not imply a pure algorithmic speed comparison when execution hardware differs substantially.

---

### 28.3 Model Loading

Benchmark methodology should define whether timing includes:

- model/checkpoint loading
- model initialization
- index loading
- preprocessing
- warm-up

Apply the decision consistently.

---

### 28.4 Cache Conditions

Cache state can materially influence runtime.

Potential conditions include:

- cold model load
- warm model
- cold retrieval index
- loaded index
- filesystem cache effects

When material, document the relevant condition.

---

### 28.5 Warm-Up

GPU/model inference may need warm-up for stable measurement.

If warm-up is used:

- apply it consistently
- document it

Do not invent a mandatory number of warm-up runs.

---

### 28.6 Latency vs Throughput

Distinguish:

**Latency**
→ time required for one operation/pair

**Throughput**
→ number of operations processed per unit time

Do not use these terms interchangeably.

---

## 29. Memory and Resource Measurements

Memory usage may be benchmarked when it matters.

If compared, document:

- what was measured
- measurement scope
- hardware/environment context

Do not invent memory budgets.

If resource limits are part of the benchmark, define them before evaluation.

Otherwise an accidental machine limitation should not automatically become a scientific method failure.

---

## 30. Randomness and Repeated Runs

Randomness may affect:

- RANSAC
- sampling
- augmentation
- model training
- some GPU inference behavior

Where practical:

- control random seeds
- record seeds
- preserve configuration

Do not claim determinism that underlying libraries/hardware do not guarantee.

---

### 30.1 Multiple Runs

Stochastic methods may require repeated evaluation.

If results are aggregated across runs, report:

- number of runs
- aggregation method
- variability where useful

Do not invent a mandatory run count.

---

## 31. Aggregation and Statistical Reporting

Aggregate methodology should be defined before selecting the most flattering summary.

Potential aggregation styles include:

- mean per-pair metric
- median per-pair metric
- pooled correspondence statistics
- grouped sensor results
- grouped stress-category results

These answer different questions.

Exact aggregation mathematics belongs in metrics documentation.

---

### 31.1 Mean vs Median

Large errors and failed cases can make mean and median behave differently.

Use the statistic appropriate to the benchmark's scientific question.

Do not choose whichever appears better after observing results without explanation.

---

### 31.2 Variability

Where sample size and methodology justify it, useful dispersion measures may include:

- standard deviation
- percentiles
- confidence intervals
- run-to-run range

Do not mechanically add statistical quantities to tiny populations when they add little meaning.

---

### 31.3 Pair-Level Results

Where practical, preserve pair-level results beneath aggregate summaries.

A single average can hide:

- failures
- outliers
- sensor differences
- stress-category behavior

---

### 31.4 Category-Level Results

When sample size permits, subgroup reporting may reveal behavior across:

- OHRC
- TMC-2
- IIRS
- NAC/WAC reference context
- illumination stress
- scale stress
- modality stress

Do not fabricate categories that are not defined in the benchmark.

---

### 31.5 Overall Scores

Do not invent an arbitrary combined score such as:

> ChandraMap Robustness Score

by combining:

- RMSE
- runtime
- coverage
- success rate

unless a formally defined and justified metric exists.

Prefer direct metric reporting.

---

## 32. Benchmark Result Records

Each benchmark execution should preserve enough context to interpret the result.

Potential categories include:

- benchmark identity/version
- pair/query identity
- source/reference identity
- dataset context
- preprocessing
- scale handling
- matcher
- geometry configuration
- refinement configuration
- retrieval configuration
- result status
- metrics
- units
- runtime
- hardware context where relevant
- random seed where relevant
- software revision
- model/checkpoint where relevant

These are conceptual responsibilities.

Do not invent exact schema fields.

---

### 32.1 Result Immutability

Canonical raw benchmark values should not be manually edited.

If a result is invalid because of:

- software bug
- wrong configuration
- corrupted data
- incorrect ground truth

correct the underlying issue and rerun the benchmark under a documented corrected state.

Do not hand-edit the metric.

---

### 32.2 Result Provenance

A published benchmark number should ideally be traceable to:

```text
Data
+
Configuration
+
Code
+
Method
+
Metric Definition
```

---

### 32.3 Missing Metrics

Do not replace unavailable metrics with `0` unless zero is actually the scientifically correct value.

Use explicit missing/status semantics.

---

## 33. Reproducibility

A benchmark is stronger when another contributor can reproduce:

- benchmark membership
- pair/query selection
- preprocessing
- matcher configuration
- geometry
- evaluation
- aggregation

Exact bitwise equality may not always be realistic.

Reproducibility means the methodology and result generation process are reconstructable.

---

### 33.1 Environment Context

Where relevant, preserve enough execution context to interpret:

- runtime
- GPU behavior
- learned-model inference
- numerical differences

Potential context includes:

- dependency versions
- accelerator
- operating environment
- hardware

Use actual project mechanisms rather than inventing a new environment schema.

---

### 33.2 Dependency Changes

Dependency upgrades can change:

- feature detection
- interpolation
- RANSAC
- numerical routines
- learned-model inference

If benchmark behavior changes after dependency updates, investigate before claiming an algorithmic improvement or regression.

---

## 34. V1 Baseline Protection

Once canonical V1 becomes stable, protect it as a comparison baseline.

Do not repeatedly retune V1 after observing later-version results merely to improve the baseline.

V1 should remain consistent enough that later improvements have a meaningful reference point.

---

### 34.1 V1 Scope

Canonical V1 must remain aligned with [`../context/V1_SCOPE.md`](../context/V1_SCOPE.md).

Do not silently require:

- global retrieval
- FAISS
- ALIKED + LightGlue
- LoFTR
- full native IIRS hyperspectral processing
- DEM-aware geometry
- piecewise warping
- advanced refinement

if V1 scope excludes them.

---

### 34.2 Bug Fix vs Methodology Change

Distinguish:

#### Bug Fix

Example:

> Source/reference coordinates were accidentally reversed.

This repairs intended behavior.

#### Methodology Change

Example:

> SIFT is replaced with LightGlue.

This changes the benchmark method.

Methodology changes may require a new benchmark configuration or documented revision.

---

## 35. Benchmark Revisions and Comparability

A published/stable benchmark definition should ideally freeze relevant aspects such as:

- evaluation population
- ground truth
- metric semantics
- baseline configuration

If methodology changes materially, document:

- what changed
- why it changed
- comparability impact
- whether historical results must be rerun

Do not invent a benchmark-versioning scheme unless the project defines one.

---

### 35.1 Benchmark Expansion

Adding additional benchmark pairs may improve coverage.

However:

```text
Results on population A
```

and:

```text
Results on larger population B
```

should not be treated as directly equivalent without explaining the changed population.

---

### 35.2 Dataset Changes

Changes to:

- source product
- reference product
- processing level
- metadata
- check points
- ground truth

may change benchmark interpretation.

Treat meaningful dataset changes as benchmark changes.

---

### 35.3 Metric Bug Fixes

If an authoritative metric implementation contains a bug:

1. fix it centrally
2. determine which historical results are affected
3. rerun affected benchmarks where necessary
4. document the comparability break

Do not compare old buggy values directly with corrected values without explanation.

---

## 36. Benchmark Invalidation

A benchmark result may need to be invalidated if:

- benchmark data was wrong
- ground truth was wrong
- data leakage occurred
- metric implementation was incorrect
- transform direction was wrong
- wrong configuration was used
- result values were manually altered
- reported methodology did not match execution

Invalidating a misleading result is preferable to preserving it for continuity.

---

## 37. Ablation Experiments

An ablation should identify:

- baseline configuration
- component removed or changed
- variables intentionally held fixed
- benchmark data
- metrics
- result
- interpretation

Do not call an experiment an ablation when many unrelated components changed simultaneously.

---

### 37.1 Factorial / Multi-Factor Experiments

If multiple components are systematically varied, document the experimental design.

Do not infer single-component causality without sufficient controls.

---

## 38. Synthetic vs Real Stress Tests

Synthetic transformations can help isolate variables such as:

- rotation
- geometric scale
- noise
- contrast

They are useful controlled experiments.

They are not complete substitutes for real:

- Sun-angle geometry
- cross-sensor modality
- terrain relief
- sensor physics

Label synthetic benchmarks clearly.

Do not merge real and synthetic averages without explaining the aggregation and scientific meaning.

---

## 39. Sun-Angle Benchmarking

When evaluating illumination stress, preserve available illumination metadata where practical.

Do not describe:

```text
contrast normalization
```

as equivalent to:

```text
Sun-angle invariance
```

Real Sun-angle changes can alter shadow geometry and visible structure.

Benchmark claims should reflect what was actually evaluated.

---

## 40. Confidence and Quality Scores

If a future benchmark includes confidence scores, evaluate their relationship to actual outcomes.

Potential questions include:

- Does higher confidence correlate with lower error?
- Does confidence separate success from failure?
- Is a probability-like confidence calibrated?

Do not report arbitrary percentage values such as:

> 92% probability of correct registration

without a defined and validated calibration method.

---

### 40.1 Combined Quality Scores

Avoid arbitrary formulas combining:

- inliers
- coverage
- residuals
- confidence

into one undocumented quality number.

If a combined score is adopted, its:

- formula
- interpretation
- weighting
- validation

must be documented.

---

## 41. Negative Results and Limitations

Negative results are scientifically useful.

Examples may include:

- learned matcher underperformed the classical baseline
- retrieval failed in repetitive crater terrain
- scale handling improved one sensor pair but degraded another
- IIRS representation did not provide enough local structure
- refinement increased instability

Do not hide negative results merely because they weaken a project narrative.

---

### 41.1 Limitations

Benchmark conclusions should identify relevant limitations, such as:

- small evaluation population
- limited sensor coverage
- approximate ground truth
- model domain shift
- incomplete reference database
- untested terrain types
- limited illumination diversity

Do not generalize beyond the evidence.

---

### 41.2 No SOTA Claim from Internal Comparison

Internal comparison among V1–V4 does not establish:

> state-of-the-art lunar registration

Such a claim would require an appropriate external comparison and methodology.

---

### 41.3 No Universal Invariance Claim

Do not infer universal:

- scale invariance
- Sun-angle invariance
- modality invariance

from a limited benchmark.

Prefer evidence-bounded language such as:

> improved robustness on the evaluated scale-stress cases

when supported by actual measured results.

---

## 42. Benchmark Reporting

A professional benchmark report should make clear:

- what was compared
- benchmark population
- benchmark version/configuration
- search mode
- preprocessing
- scale handling
- methods
- geometric model
- refinement status
- evaluation metrics
- units
- failure policy
- runtime scope where relevant
- hardware context where relevant
- results
- limitations

Use only real measured results.

---

### 42.1 Comparison Tables

Use consistent columns, units, and populations.

A conceptual table structure may look like:

| Method | Population | Status Metric | Accuracy Metric | Coverage | Runtime Scope |
| ------ | ---------- | ------------- | --------------- | -------- | ------------- |

Do not populate canonical documentation with invented numbers.

---

### 42.2 Rounding

Use scientifically reasonable precision.

Do not display excessive decimal places unsupported by:

- ground-truth quality
- metric stability
- run variability

---

### 42.3 Sorting

Do not sort methods solely to imply an overall winner when several metrics involve trade-offs.

Use:

- stable version order
- method order
- clearly identified metric-based ordering

where appropriate.

---

## 43. Qualitative Benchmark Visuals

Useful qualitative outputs may include:

- candidate matches
- verified inliers
- rejected outliers
- residual vectors
- registered overlays
- failure examples

Visuals should supplement measured evaluation.

They must not replace it.

---

### 43.1 No Result Beautification

Do not:

- hide failed examples
- manually remove inconvenient correspondences after evaluation
- crop away visible mismatches without explanation
- modify registered imagery manually to make the result appear better

Benchmark visuals should represent the underlying run honestly.

---

### 43.2 Consistent Presentation

Where practical, visual comparisons should use consistent:

- crop
- zoom
- source/reference orientation
- point styling
- display scale

Avoid presentation choices that visually exaggerate one method's performance.

---

### 43.3 Plots

Useful plots may include:

- error distributions
- success/failure by category
- coverage vs error
- runtime vs accuracy
- residual vectors
- retrieval-rank distributions

Use only actual benchmark data.

Do not manipulate axes to exaggerate small differences without clear context.

---

## 44. CI vs Scientific Benchmarks

Normal software CI and full scientific benchmarking have different purposes.

Conceptually:

```text
Pull-Request CI
→ unit tests
→ integration tests
→ lightweight regression checks
```

while:

```text
Scientific Benchmark
→ controlled lunar datasets
→ full evaluation methodology
→ benchmark result records
```

Do not assume expensive full lunar benchmarks run on every pull request.

Document actual repository behavior only.

---

### 44.1 Smoke Benchmark

A small benchmark may be useful to verify that benchmark infrastructure executes.

A smoke benchmark is not equivalent to the full scientific benchmark population.

Do not publish smoke-run performance as though it were full benchmark performance.

---

## 45. Benchmark Automation

An automated benchmark runner should conceptually:

1. load the benchmark definition
2. load controlled data
3. load explicit configuration
4. execute the selected method
5. preserve success/failure status
6. calculate authoritative metrics
7. store result context
8. aggregate according to benchmark policy

It must not:

- modify results to improve them
- skip failed cases silently
- choose the best configuration using final test truth
- duplicate authoritative scientific algorithms unnecessarily

---

### 45.1 Benchmark Runner vs Scientific Core

Benchmark runners should orchestrate reusable scientific code.

Avoid separate benchmark-directory implementations of:

- SIFT
- RANSAC
- RMSE
- transform application
- learned matcher wrappers

when authoritative reusable implementations already exist.

See [`../architecture/MODULE_MAP.md`](../architecture/MODULE_MAP.md).

---

### 45.2 One Authoritative Metric Implementation

Where a metric definition is intended to be the same across versions, all versions should call the same authoritative implementation.

Avoid:

```text
V1 RMSE implementation
V2 RMSE implementation
V3 RMSE implementation
```

with slightly different semantics.

---

## 46. External Benchmark and Paper Comparisons

External numbers are comparable only when methodology is sufficiently aligned.

Before comparing a ChandraMap result to an external paper/tool, check:

- dataset/population
- source/reference imagery
- coordinate space
- units
- metric definition
- fit/check-point policy
- failure treatment
- preprocessing
- search assumption
- runtime scope

A paper reporting:

```text
RMSE = X
```

may use completely different:

- imagery
- scale
- ground truth
- units
- evaluation population

Do not place unrelated numbers in a comparison table as though they were directly comparable.

---

## 47. Public Result Claims

Any README, portfolio, report, or paper claim should ideally be traceable to:

- benchmark version
- benchmark population
- method/configuration
- metric definition
- result record/report

Avoid floating claims such as:

> ChandraMap achieves very high accuracy.

Prefer evidence-specific claims when results exist.

Do not invent measurements.

---

## 48. Benchmark Software Status

Distinguish the following states where useful:

### Specified

A benchmark definition exists.

### Implemented

Executable benchmark tooling exists.

### Executed

The benchmark was actually run.

### Published

Results have been reviewed and intentionally released.

These states are not equivalent.

A V3 specification existing does not imply V3 has been implemented or executed.

---

## 49. No Fake Execution

Never claim:

- benchmark completed
- all cases evaluated
- results reproduced
- metrics verified
- GPU benchmark passed

without actual execution evidence.

If no execution occurred, use:

> **Not Run**

where status reporting is required.

---

## 50. Benchmark Security and Data Governance

Benchmark data and model checkpoints may originate externally.

Follow project security and dataset rules.

Do not:

- use unsafe deserialization merely to load a benchmark model
- expose secrets
- publish restricted data
- include private local paths in public benchmark metadata

---

### 50.1 Dataset Licensing

Benchmark reproducibility must respect data usage and redistribution requirements.

Do not commit mission products merely for convenience when redistribution is inappropriate.

Use repository-supported manifests/source references where necessary.

---

### 50.2 Portability

Canonical benchmark definitions should not depend on:

- one contributor's username
- absolute local paths
- fixed GPU IDs
- undocumented notebook state
- random local filenames

Use portable repository/data mechanisms.

---

## 51. Benchmark Anti-Patterns

### 51.1 Cherry-Picked Pairs

Running many cases and reporting only the attractive successes.

---

### 51.2 Changing the Dataset

Evaluating methods on different favorable pair populations and treating the results as directly comparable.

---

### 51.3 Fit-and-Judge

Using fit points for transformation estimation and presenting error on those same points as independent accuracy.

---

### 51.4 Ground-Truth Leakage

Using evaluation truth during search, fitting, tuning, or candidate selection.

---

### 51.5 Secret Pair-Specific Tuning

Changing thresholds or methods after viewing final test performance.

---

### 51.6 Oracle Selection Presented as Normal Performance

Selecting the best method per pair using ground truth and reporting it as ordinary system behavior.

---

### 51.7 Metric Drift

Changing RMSE or coverage definitions between versions without documenting the comparability break.

---

### 51.8 Unit Drift

Reporting one version in source-image pixels and another in reference-image pixels without identifying the difference.

---

### 51.9 Failure Hiding

Removing failed registrations before aggregate reporting.

---

### 51.10 Successful-Only Accuracy Without Failure Context

Reporting low RMSE only on successful cases without stating how many cases failed.

---

### 51.11 Runtime Scope Mismatch

Comparing matcher-only runtime with end-to-end pipeline runtime.

---

### 51.12 Hidden Hardware Mismatch

Presenting GPU-vs-CPU timing as a pure algorithmic speed comparison.

---

### 51.13 Warm/Cold Cache Mismatch

Comparing warm-cache timings against cold-start timings without disclosure.

---

### 51.14 Offline Cost Hiding

Ignoring reference-index construction while making scalability claims.

---

### 51.15 Method Label Drift

Calling a configuration:

> SIFT baseline

while silently changing:

- RootSIFT conversion
- preprocessing
- RANSAC
- geometry
- scale handling

---

### 51.16 V1 Scope Creep

Adding advanced later-version capabilities to V1 while preserving the V1 label.

---

### 51.17 Fake Confidence

Reporting arbitrary probability-like percentages without calibrated evidence.

---

### 51.18 Visual Benchmarking

Judging accuracy only from overlays.

---

### 51.19 Benchmark-by-Notebook-State

Producing results that depend on undocumented interactive cells or local variables.

---

### 51.20 Manual Result Editing

Correcting canonical numerical outputs by hand instead of rerunning the corrected experiment.

---

### 51.21 Test/Benchmark Confusion

Presenting passing unit tests as evidence that the scientific method performs accurately.

---

### 51.22 External Number Miscomparison

Comparing metrics from incompatible datasets, units, or protocols as though they were equivalent.

---

### 51.23 Fake Overall Score

Combining unrelated metrics into an undocumented "overall accuracy" or "robustness" number.

---

### 51.24 Benchmark Overfitting

Adding production logic that recognizes:

- benchmark filenames
- benchmark regions
- known transformations
- test annotations

to artificially improve evaluation.

The benchmark measures the method.

The method must not memorize the benchmark definition.

---

## 52. Benchmark Review Checklist

Before accepting benchmark results, verify:

- [ ] Benchmark purpose is defined.
- [ ] Benchmark configuration/version is explicit.
- [ ] Benchmark V1–V4 are not confused with software releases.
- [ ] Benchmark population or manifest is identifiable.
- [ ] Source and reference products are traceable.
- [ ] Search mode is stated: known-overlap, metadata-constrained, retrieval, or end-to-end.
- [ ] Sensor/product assumptions are explicit.
- [ ] Ground truth/check points have provenance.
- [ ] Ground-truth limitations are understood where relevant.
- [ ] Fit and independent check points are separated where independent accuracy is claimed.
- [ ] No ground-truth leakage occurred.
- [ ] No unintended final-test tuning occurred.
- [ ] Geographic/observation leakage has been considered where relevant.
- [ ] Preprocessing is documented.
- [ ] Scale/GSD handling is documented.
- [ ] Resampling is not described as physical resolution recovery.
- [ ] IIRS-derived representation is documented where relevant.
- [ ] Matcher configuration is identified.
- [ ] SIFT vs RootSIFT configuration is explicit.
- [ ] ALIKED and LightGlue roles are not conflated.
- [ ] LoFTR is handled according to detector-free correspondence semantics.
- [ ] Transformation model is documented.
- [ ] RANSAC/geometric-verification configuration is controlled.
- [ ] Refinement status is documented.
- [ ] Final evaluation uses the final transform.
- [ ] Metric definitions are consistent.
- [ ] Metric units are explicit.
- [ ] Source-image vs reference-image pixel space is clear.
- [ ] Ground error is reported only with valid spatial context.
- [ ] Spatial coverage is considered where required.
- [ ] Retrieval metrics remain separate from registration metrics.
- [ ] Failed/rejected cases remain visible.
- [ ] Unsupported, Failed, Invalid, N/A, and Not Run are distinguished where applicable.
- [ ] Success-rate denominator is correct.
- [ ] Conditional accuracy is labeled as conditional.
- [ ] Missing metrics are not replaced with arbitrary zeroes.
- [ ] Runtime scope is defined.
- [ ] Hardware context is recorded where needed for runtime comparison.
- [ ] Offline vs online retrieval cost is distinguished.
- [ ] Cache/warm-up state is controlled or documented when relevant.
- [ ] Model/checkpoint identity is preserved for learned methods.
- [ ] Random seed is controlled/recorded where meaningful.
- [ ] Multiple-run aggregation is defined where used.
- [ ] Aggregate methodology is predefined.
- [ ] Pair-level results remain available where practical.
- [ ] No arbitrary overall score was invented.
- [ ] No benchmark thresholds were invented after observing final test results.
- [ ] No hidden pair-specific tuning occurred.
- [ ] Oracle experiments are clearly labeled.
- [ ] No cherry-picking occurred.
- [ ] No canonical result values were manually edited.
- [ ] Results are traceable to data, configuration, code, and metric definitions.
- [ ] Dependency/environment differences are understood where relevant.
- [ ] Negative results remain visible.
- [ ] Limitations are documented.
- [ ] Conclusions do not exceed the evaluated population.
- [ ] No unsupported SOTA claim is made.
- [ ] No unsupported universal invariance claim is made.
- [ ] Execution status is reported truthfully.

---

## 53. AI-Agent Benchmark Workflow

When an AI agent is asked to create, modify, review, or execute a benchmark:

1. read the relevant benchmark/version scope
2. inspect the benchmark population/manifest
3. confirm source/reference identity
4. confirm search mode
5. inspect the active configuration
6. confirm preprocessing and scale handling
7. confirm matcher and geometry configuration
8. confirm fit/check-point policy
9. confirm authoritative metric definitions
10. check units and transform direction
11. inspect failure/status policy
12. validate affected implementation narrowly if code changed
13. run the requested benchmark only when execution is actually required
14. preserve failed/rejected cases
15. preserve configuration and result provenance
16. report exactly what was executed
17. distinguish Passed, Failed, Invalid, Unsupported, and Not Run where applicable
18. never invent missing benchmark results
19. never alter methodology merely to improve numbers
20. update comparability documentation if methodology changed

---

## 54. Narrow-to-Broad Benchmark Development

When developing a new benchmark workflow, prefer:

```text
One Verified Pair
        ↓
Small Controlled Subset
        ↓
Stress-Category Coverage
        ↓
Full Defined Benchmark Population
```

Do not begin with a massive evaluation before verifying:

- source/reference direction
- metric semantics
- transform direction
- ground truth
- configuration
- failure handling

Large-scale execution cannot rescue an invalid benchmark design.

---

## 55. Benchmark Debugging Order

When benchmark output looks suspicious, inspect fundamentals before tuning the matcher.

Recommended investigation order:

1. source/reference identity
2. benchmark-case validity
3. coordinate convention
4. transform direction
5. ground truth/check points
6. metric units
7. masks/no-data
8. scale/GSD handling
9. preprocessing
10. active benchmark configuration
11. selected matcher
12. geometric-verification settings

Do not immediately tune thresholds when the real problem may be reversed coordinates or invalid benchmark truth.

---

## 56. Relationship with Other Documents

[`TESTING_RULES.md`](TESTING_RULES.md)
→ defines whether implementation behavior is correct

This file defines whether an experimental comparison is scientifically fair and interpretable.

---

[`DOCUMENTATION_RULES.md`](DOCUMENTATION_RULES.md)
→ defines how benchmark methodology and results must be documented honestly

---

[`../context/V1_SCOPE.md`](../context/V1_SCOPE.md)
→ authoritative boundary of canonical Benchmark V1

---

[`../architecture/PIPELINE.md`](../architecture/PIPELINE.md)
→ authoritative high-level scientific processing order

Benchmarks evaluate/combine pipeline behavior; they should not casually redefine the pipeline.

---

[`../architecture/DATA_FLOW.md`](../architecture/DATA_FLOW.md)
→ defines data identity, lineage, coordinates, transform semantics, units, and result meaning

Benchmark records should preserve those semantics.

---

[`../architecture/MODULE_MAP.md`](../architecture/MODULE_MAP.md)
→ defines where benchmark orchestration and scientific implementations should live

---

[`../context/DATASETS.md`](../context/DATASETS.md)
→ defines scientific product context, provenance, dataset roles, and source/reference data principles

This file defines how those products should be controlled for evaluation.

---

[`../context/TERMINOLOGY.md`](../context/TERMINOLOGY.md)
→ defines canonical terms used in benchmark reports and specifications

---

Metrics documentation, when present, should define exact:

- equations
- aggregation
- edge-case handling
- units
- success/failure metric semantics

This file should not duplicate that mathematics.

A dedicated reproducibility document, when present, should own broader research reproducibility requirements beyond benchmark-specific governance.

---

## 57. Key Benchmark Rules for AI Agents

1. A benchmark is not a software test.

2. Benchmark V1–V4 are not software release versions.

3. V1 must remain a stable classical baseline.

4. Keep V1 aligned with `V1_SCOPE.md`.

5. Compare methods on the same defined data where the research question requires direct comparison.

6. Use a reproducible benchmark manifest or equivalent source of truth.

7. Preserve source/reference identity.

8. State whether the benchmark uses known overlap, metadata-constrained search, image-only retrieval, or end-to-end localization.

9. Using valid metadata is not cheating unless the benchmark prohibits it.

10. Do not leak ground truth into search, fitting, or parameter selection.

11. Keep fit points separate from independent check points.

12. Do not report fit-point RMSE as independent final accuracy.

13. Do not use matcher output as ground truth.

14. Do not treat RANSAC inliers automatically as ground truth.

15. Keep retrieval evaluation separate from local registration evaluation.

16. Recall@K is not registration RMSE.

17. Report accuracy metrics with explicit units.

18. Distinguish source-image pixels from reference-image pixels.

19. Convert to metres only with valid geospatial context.

20. Sub-pixel does not automatically mean sub-metre.

21. Inlier count alone is insufficient evidence.

22. Inlier ratio alone is insufficient evidence.

23. Spatial coverage matters.

24. Preserve residual information where it helps explain geometry.

25. Preserve rejected and failed cases.

26. Never compute success rate after silently removing failures.

27. Distinguish Unsupported, Failed, Invalid, N/A, and Not Run where applicable.

28. Do not replace unavailable metrics with zero.

29. Do not cherry-pick successful pairs.

30. Do not secretly tune parameters per test pair.

31. Do not use final test error to choose the normal per-pair configuration.

32. Clearly label oracle experiments.

33. Use validation/development data for tuning where appropriate.

34. Consider geographic, observation, and augmentation leakage where learned methods/retrieval require independent evaluation.

35. Record preprocessing differences.

36. Record scale/GSD handling.

37. Do not describe upsampling as physical resolution improvement.

38. Record IIRS 2D representation when applicable.

39. Do not tune IIRS representation against final test truth and call it unbiased performance.

40. Preserve SIFT vs RootSIFT configuration identity.

41. Identify ALIKED and LightGlue roles separately where material.

42. Benchmark LoFTR according to detector-free correspondence semantics.

43. Treat remote-sensing matchers as explicit research configurations.

44. Hold transformation model fixed when the research question isolates matcher behavior and doing so is scientifically appropriate.

45. Control RANSAC configuration fairly.

46. State when refinement differs between compared pipelines.

47. Refit after tie-point refinement where the pipeline requires it.

48. Evaluate the final transformation used for registration.

49. FAISS is a vector retrieval/index component, not local registration.

50. Distinguish offline retrieval-index construction from online query cost.

51. Runtime comparisons must cover comparable scope.

52. Report hardware context where runtime interpretation requires it.

53. State CPU/GPU differences.

54. Control or document cache and warm-up conditions when they materially affect timing.

55. Distinguish latency from throughput.

56. Record model/checkpoint identity for learned methods.

57. Control or record random seeds where meaningful.

58. Do not claim impossible determinism.

59. Report repeated-run aggregation and variability when repeated stochastic runs are used.

60. Define aggregation methodology before selecting the most favorable statistic.

61. Preserve pair-level results beneath aggregate summaries where practical.

62. Preserve meaningful sensor/stress-category results where practical.

63. Do not invent an arbitrary overall score.

64. Do not rank methods using undefined mixtures of metrics.

65. Quality thresholds must be explicit and must not be invented after final evaluation.

66. Benchmark failure policy should be defined before results are interpreted.

67. Accuracy reported only on accepted/successful cases must be labeled accordingly.

68. Do not manually edit canonical benchmark values.

69. Results should be traceable to data, configuration, code, and metric definitions.

70. Dependency changes may affect benchmark comparability.

71. Once V1 is stable, do not continuously retune it after seeing later-version results.

72. Distinguish bug fixes from methodology changes.

73. Benchmark methodology changes may require a new revision and reruns.

74. Dataset changes may break historical comparability.

75. Metric bug fixes may require benchmark reruns.

76. A failed scientific run can still be a valid benchmark outcome.

77. An invalid or infrastructure-compromised run is not automatically an algorithm failure.

78. Visual overlays are diagnostic evidence, not sufficient accuracy evidence.

79. Do not beautify benchmark outputs by hiding poor cases.

80. Negative results are valuable and should be preserved.

81. Do not generalize beyond the evaluated sensors, data, and stress conditions.

82. Do not claim state-of-the-art performance from internal V1–V4 comparison alone.

83. Do not claim universal scale, Sun-angle, or modality invariance without sufficient evidence.

84. Do not report arbitrary confidence percentages.

85. Calibration is required before probability-like confidence claims.

86. A benchmark specification existing does not imply it is implemented.

87. A benchmark implementation existing does not imply it has been executed.

88. Benchmark execution does not automatically imply scientific validity.

89. Never claim a benchmark was executed when it was not.

90. Do not invent benchmark scores, thresholds, pair counts, hardware, seeds, checkpoints, commands, paths, or execution status.

91. External-paper numbers require protocol and metric comparability before direct comparison.

92. Benchmark runners should orchestrate shared scientific implementations rather than duplicate them.

93. Use one authoritative metric implementation when versions share the same metric semantics.

94. Benchmark definitions should not depend on local folder contents or notebook state.

95. Benchmark implementation must not recognize benchmark cases merely to improve scores.

96. Public performance claims should be traceable to a benchmark definition and measured result.

97. Benchmark plots and tables must use real data.

98. Do not use misleading axes or excessive precision.

99. Respect data licensing and security requirements when making benchmarks reproducible.

100. The benchmark exists to measure ChandraMap—not to produce numbers that make ChandraMap look better.

<!-- Source specification: :contentReference[oaicite:0]{index=0} -->
