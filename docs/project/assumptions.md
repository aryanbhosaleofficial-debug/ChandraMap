# ChandraMap Project Assumptions

This document defines the canonical **human-facing register of important assumptions used throughout ChandraMap**.

Scientific software can continue to execute even when one of its underlying assumptions is false. In that situation, the software output may look reasonable while the scientific interpretation is invalid.

ChandraMap therefore treats important assumptions as:

> **Explicit + Traceable + Testable Where Possible + Version-Specific Where Necessary**

This document answers:

> **What conditions are being assumed to be true when ChandraMap is designed, executed, benchmarked, or interpreted?**

An assumption is not automatically a fact, requirement, invariant, configuration default, or hypothesis. Those distinctions are important throughout this document.

---

## 1. Purpose

ChandraMap uses assumptions at several levels:

- scientific problem definition
- source/reference image interpretation
- mission-product identity
- metadata use
- sensor and modality handling
- spatial scale and GSD
- illumination
- image representation
- geometric models
- lunar coordinates
- correspondence quality
- registration acceptance
- evaluation truth
- retrieval
- benchmarking
- runtime behavior
- reproducibility
- Benchmark V1–V4

Making those assumptions explicit helps answer questions such as:

- Why is a particular registration considered valid?
- What information does a method rely on?
- What happens when metadata is missing?
- When is a homography an acceptable approximation?
- Why can a valid RANSAC result still be scientifically wrong?
- When does a benchmark result stop being comparable with an older result?
- Which assumptions apply only to V1 rather than ChandraMap as a whole?

This document does not define implementation details, metric formulas, benchmark thresholds, APIs, or algorithm code.

---

## 2. How to Read This Document

### Assumption vs Fact

A **fact** is established by authoritative project, product, or scientific evidence.

Example:

> A particular product's metadata states that it uses a particular projection.

An **assumption** is a condition accepted by a workflow, benchmark, model, or experiment so that its result can be interpreted.

Example:

> The supplied reference crop contains the correct overlapping lunar terrain.

An assumption may later be verified, rejected, refined, or replaced.

Do not present assumptions as universal truths.

---

### Assumption vs Requirement

A **requirement** describes what ChandraMap must do.

Example:

> Transform direction must remain explicit.

An **assumption** describes a condition under which the method is expected to be valid.

Example:

> The supplied source/reference pair contains meaningful overlap.

Requirements constrain the system.

Assumptions constrain interpretation.

---

### Assumption vs Invariant

An **invariant** is a relationship that should remain true if the implementation is correct.

Example:

```text
verified inlier count
<=
candidate-match count
```

An **assumption** may be scientifically reasonable but still turn out to be false.

Example:

> A homography adequately approximates the selected local terrain.

Do not use assumption language to weaken implementation invariants.

---

### Assumption vs Hypothesis

A **hypothesis** is a testable proposed explanation or prediction.

Example:

> Scale-aware preprocessing will improve difficult TMC-2/reference registration.

An **assumption** defines a condition under which that experiment is interpreted.

Example:

> The selected benchmark pair contains the intended overlapping lunar terrain.

Hypotheses should be tested.

Assumptions should be documented, validated where possible, and revisited when evidence challenges them.

---

### Assumption vs Configuration Default

A configuration value is not automatically a scientific assumption.

For example:

```text
RANSAC threshold = configured value
```

is a methodological/configuration choice.

The corresponding assumption may be:

> The configured geometric tolerance is meaningful for this evaluation protocol.

Exact values belong in configuration or benchmark documentation.

---

### Project-Wide vs Version-Specific

Some assumptions apply broadly across ChandraMap.

Examples:

- source/reference roles must be known
- metric units must be explicit
- candidate matches are not automatically correct
- lunar coordinates must not silently use Earth defaults

Other assumptions belong only to a specific benchmark configuration.

Examples:

- Benchmark V1 assumes known overlap
- V1 uses simple global geometry as a baseline
- later retrieval-enabled versions may assume a prepared reference index
- terrain-aware research may assume compatible DEM/DTM information exists

A version-specific assumption must not be generalized to the entire project.

---

## 3. Assumption Summary

The following register summarizes major assumptions without assigning unverified validation status.

| ID   | Assumption                                                                   | Primary Scope         | If False                                                           |
| ---- | ---------------------------------------------------------------------------- | --------------------- | ------------------------------------------------------------------ |
| A-01 | Source and reference roles are known                                         | Project-wide          | Transform/error interpretation becomes ambiguous                   |
| A-02 | Local registration has meaningful overlap                                    | Local registration    | Registration should reject or fail scientifically                  |
| A-03 | V1 receives the correct overlapping reference region                         | Benchmark V1          | V1 result no longer measures the intended local-registration task  |
| A-04 | Inputs are scientifically identifiable                                       | Data / provenance     | Results become difficult to interpret or reproduce                 |
| A-05 | Product-specific metadata is preferred over nominal instrument values        | Data / geospatial     | Scale or geometry interpretation may be wrong                      |
| A-06 | Metadata may be incomplete                                                   | Project-wide          | Missing information must remain unknown rather than fabricated     |
| A-07 | Array semantics are known                                                    | Data / software       | Preprocessing and matching may operate on the wrong representation |
| A-08 | 2D matching receives an appropriate 2D representation                        | Local matching / IIRS | Matching may become scientifically meaningless                     |
| A-09 | Cross-sensor raw intensity equality is not assumed                           | Multi-sensor          | Intensity-only matching may fail                                   |
| A-10 | GSD differences represent real physical information differences              | Scale handling        | Resizing may be misinterpreted as information recovery             |
| A-11 | Illumination can change visible structure                                    | Illumination          | Photometric normalization may be overtrusted                       |
| A-12 | Some structural cues remain useful in suitable cases                         | Correspondence        | Matching may fail under extreme changes                            |
| A-13 | A simple planar model may approximate some local regions                     | V1 geometry           | Residual distortion or rejection may occur                         |
| A-14 | Transform direction and coordinate convention are known                      | Geometry              | Warp and metrics may be incorrect                                  |
| A-15 | Lunar spatial data requires lunar coordinate context                         | Geospatial            | Coordinates may be misinterpreted                                  |
| A-16 | Candidate matches contain both possible inliers and outliers                 | Matching              | Geometry verification would be incorrectly bypassed                |
| A-17 | RANSAC can recover useful consensus when enough valid candidates exist       | V1 geometry           | Transform estimation may fail                                      |
| A-18 | RANSAC inliers are model-consistent, not ground truth                        | Evaluation            | Accuracy claims may become circular                                |
| A-19 | Spatial distribution matters in addition to count                            | Evaluation            | Clustered support may be overvalued                                |
| A-20 | Warp completion does not establish accuracy                                  | Registration          | Numerical execution may be mistaken for scientific success         |
| A-21 | Independent check points provide stronger validation where available         | Evaluation            | Fit error may be overstated as independent accuracy                |
| A-22 | Ground truth may be incomplete or uncertain                                  | Evaluation            | Precision claims may exceed available evidence                     |
| A-23 | Pixel error requires an explicit coordinate space                            | Metrics               | Error becomes ambiguous                                            |
| A-24 | Pixel error does not automatically equal ground error                        | Metrics               | Metre-level claims may be invalid                                  |
| A-25 | Rejection is a valid scientific outcome                                      | Project-wide          | System may force unsupported registrations                         |
| A-26 | Scientific failure differs from software error                               | Project-wide          | Benchmark interpretation becomes misleading                        |
| A-27 | Benchmark pair definitions and membership are correct                        | Benchmarking          | Results become invalid                                             |
| A-28 | Configuration is part of benchmark methodology                               | Benchmarking          | Runs cannot be compared or reproduced reliably                     |
| A-29 | Final evaluation truth is not leaked into normal method decisions            | Benchmarking          | Independent evaluation becomes invalid                             |
| A-30 | Failed/rejected cases remain visible                                         | Benchmarking          | Aggregate performance becomes biased                               |
| A-31 | Retrieval and registration have different success conditions                 | Retrieval             | Stage-level conclusions become confused                            |
| A-32 | Learned methods may experience lunar domain shift                            | V3/V4 research        | Performance may differ substantially from terrestrial expectations |
| A-33 | Later benchmark versions are not automatically better                        | V2–V4                 | Version ordering may be mistaken for performance ordering          |
| A-34 | Original and derived data remain distinguishable                             | Provenance            | Scientific lineage becomes ambiguous                               |
| A-35 | Important configuration, code, and data identity are reproducibility context | Reproducibility       | Results may not be reconstructable                                 |

---

# 4. Problem and Input Assumptions

## A-01 — Inputs Represent Lunar Observations

**Assumption:**
The intended scientific pipeline receives valid lunar observations or explicitly defined derived representations of lunar observations.

**Why it is needed:**
ChandraMap's correspondence, geometry, scale, and geospatial interpretation are designed around planetary surface imagery.

**If false:**
The software may still produce descriptors, matches, or transforms, but those outputs may have no meaningful lunar interpretation.

**How to verify or mitigate:**
Use product identity, provenance, benchmark manifests, metadata, and input validation where available. ChandraMap does not need to visually classify an image as lunar merely to validate every input.

---

## A-02 — Source and Reference Roles Are Known

**Assumption:**
The workflow knows which observation is the **source** and which is the **reference**.

**Why it is needed:**
These roles affect:

- transform direction
- warp direction
- pixel-error interpretation
- source/reference coordinate semantics
- result reporting

**If false:**
The same matrix or point set may be interpreted in the wrong direction, producing incorrect warps or metrics.

**How to verify or mitigate:**
Keep source/reference identity explicit in inputs, intermediate data, transform semantics, metrics, and outputs.

Do not rely on ambiguous names such as `image1` and `image2` when direction matters.

---

## A-03 — Local Registration Has Meaningful Physical Overlap

**Assumption:**
A local-registration pair contains enough shared lunar terrain for correspondence to be scientifically possible.

**Why it is needed:**
Local matching and geometric verification require some common physical structure.

**If false:**
Correct behavior may be:

- no useful correspondences
- geometric-verification failure
- scientific rejection

**How to verify or mitigate:**
Use trusted pair construction, metadata, footprints, benchmark manifests, or explicit overlap validation where available.

Do not force alignment of non-overlapping observations.

---

## Known Overlap Does Not Mean Known Alignment

Known overlap means:

> the correct lunar region is supplied or constrained.

It does **not** mean:

- the images are already aligned
- scale is similar
- illumination is similar
- the transformation is known
- matching is easy
- every feature has a counterpart

This distinction is especially important for Benchmark V1.

---

## A-04 — Inputs Are Scientifically Identifiable

**Assumption:**
Scientifically important inputs can be associated with enough identity and provenance to interpret them correctly.

Potential context includes:

- mission
- instrument
- product
- source/reference role
- derived-representation identity

**If false:**
Anonymous arrays may produce numerical results that cannot be interpreted or reproduced scientifically.

**How to verify or mitigate:**
Preserve available identifiers and provenance through the workflow.

---

# 5. Data and Provenance Assumptions

## A-05 — Product Metadata Takes Precedence Over Nominal Instrument Values

**Assumption:**
When specific product metadata is available and authoritative for processing, it is more appropriate than broad instrument-level approximations.

For context, project documentation may discuss approximate values such as:

- OHRC: ~0.25–0.32 m/px
- TMC-2: ~5 m/px
- IIRS: ~80 m/px

These values are contextual rather than universal product constants.

**If false:**
Using one nominal value for every product may introduce incorrect scale or ground-error interpretation.

**How to verify or mitigate:**
Use actual product metadata when available.

---

## A-06 — Metadata May Be Incomplete

**Assumption:**
Not every product will necessarily provide every field ChandraMap might want.

Potentially missing information may include:

- GSD
- footprint
- CRS
- projection
- illumination geometry
- viewing geometry
- acquisition information

**If false:**
No issue arises; richer metadata can be used.

**If metadata is actually missing:**
The field should remain unknown unless it can be derived through a documented method.

**How to verify or mitigate:**
Validate required metadata at the point it becomes necessary.

Never fabricate missing metadata.

---

## A-07 — Original Products Remain Distinct from Derived Data

**Assumption:**
Original scientific products remain distinguishable from representations produced by ChandraMap processing.

Derived data may include:

- normalized imagery
- resampled imagery
- pyramid levels
- selected spectral representations
- gradients
- registered outputs

**If false:**
Scientific provenance becomes difficult to reconstruct.

**How to verify or mitigate:**
Treat derived outputs as new artifacts with traceable lineage rather than overwriting authoritative input data.

---

## A-08 — Derived Representations Preserve Relevant Provenance

**Assumption:**
When a result depends materially on preprocessing or representation choice, that derivation can be identified.

**Why it is needed:**
Two experiments using different:

- bands
- normalizations
- resampling scales
- structural representations

are not necessarily equivalent.

**How to verify or mitigate:**
Preserve relevant configuration and source identity with derived artifacts or result records.

---

## A-09 — Masks and No-Data Are Not Normal Terrain

**Assumption:**
Invalid, missing, or masked pixels should not be interpreted as valid lunar texture.

**If false:**
Feature extraction, matching, coverage, and evaluation may use artificial boundaries or invalid values.

**How to verify or mitigate:**
Use product-specific mask/no-data information where available.

Do not invent one universal no-data value.

---

## A-10 — Filenames Alone Are Not Scientific Provenance

**Assumption:**
A filename may help locate a file but is not necessarily sufficient scientific identity.

**If false:**
Results may depend on local naming conventions rather than stable product/pair identity.

**How to verify or mitigate:**
Use stronger identifiers and metadata when available.

---

# 6. Sensor and Modality Assumptions

## A-11 — Sensor Identity May Matter

**Assumption:**
Some later ChandraMap workflows may depend on knowing which instrument produced an observation.

Relevant contexts include:

- OHRC
- TMC-2
- IIRS
- LRO/LROC NAC
- LRO/LROC WAC

**If false:**
Sensor-specific routing or interpretation may be inappropriate.

**How to verify or mitigate:**
Use product metadata/provenance rather than silently guessing sensor identity unless an explicit classifier is part of the methodology.

---

## A-12 — Scientific Arrays Are Not Assumed to Be Ordinary 8-Bit Grayscale Images

**Assumption:**
Scientific image products may use different:

- numeric types
- value ranges
- masks
- no-data representations
- dimensions
- spectral layouts

**If false:**
Blind conversion may discard scientifically meaningful information.

**How to verify or mitigate:**
Inspect and validate array semantics before algorithm-specific conversion.

---

## A-13 — 2D Local Matching Requires a Suitable 2D Representation

**Assumption:**
Conventional 2D correspondence stages receive a registration-ready 2D image representation.

This is straightforward for many panchromatic products.

It is not automatically true for hyperspectral data.

**If false:**
A 2D matcher may be applied to an inappropriate representation.

---

## A-14 — IIRS Is Hyperspectral / Imaging-Infrared Data

**Assumption:**
Native IIRS data should be interpreted according to its spectral nature rather than treated as an ordinary grayscale camera product.

**Why it is needed:**
A conventional local matcher generally expects a 2D representation.

Potential research representations include:

- selected spectral band
- PCA-derived component
- composite
- gradient/structural representation

**If false:**
Hyperspectral structure may be collapsed incorrectly or inconsistently.

**How to verify or mitigate:**
Document the representation used and preserve its provenance.

---

## A-15 — An IIRS-Derived 2D Representation Retains Useful Spatial Structure

**Assumption:**
When a particular derived representation is used for correspondence, it contains enough spatial information for meaningful matching.

**If false:**
Matching may become weak or fail entirely.

**How to verify or mitigate:**
Treat the representation as an experimental methodological choice and evaluate it.

Do not claim one representation is universally optimal without evidence.

---

## A-16 — Raw Intensities Are Not Universally Comparable Across Sensors

**Assumption:**
The same physical feature does not necessarily produce the same numerical pixel intensity in different sensors/modalities.

**Why it is needed:**
Sensor physics, spectral sensitivity, calibration, and illumination may differ.

**If false:**
Direct intensity methods may work more easily.

**How to verify or mitigate:**
Use structural, geometric, gradient, or modality-aware evidence where appropriate.

---

# 7. Scale and GSD Assumptions

## A-17 — GSD Differences Represent Real Information Differences

**Assumption:**
Differences in GSD are physical sampling differences, not merely different raster dimensions.

A high-resolution observation may contain terrain detail that a coarse observation never measured.

**If false:**
Resizing could be mistaken for information recovery.

**How to verify or mitigate:**
Keep physical scale and digital resize factor conceptually separate.

---

## A-18 — Upsampling Does Not Create New Physical Detail

**Assumption:**
Upsampling changes the sampled representation but not the original sensor information content.

Conceptually:

```text
coarse observation
      ↓
interpolation
      ↓
larger raster
```

does not mean:

```text
new lunar detail was measured
```

**If false:**
Registration claims may overstate available information.

---

## A-19 — GSD Must Be Interpreted in Product Context

**Assumption:**
A GSD value is meaningful only within its product, projection, and processing context.

**If false:**
One nominal sensor value may be incorrectly used as an exact universal conversion.

**How to verify or mitigate:**
Prefer product metadata and document the coordinate domain used by metrics.

---

## A-20 — Downsampling Is a Tradeoff

**Assumption:**
Downsampling may improve scale comparability while discarding high-frequency information.

**If false:**
Scale preparation may be described incorrectly as lossless.

**How to verify or mitigate:**
Record material resampling choices in benchmark configurations.

---

## A-21 — Scale Handling Is Part of the Method

**Assumption:**
Differences in resizing, pyramids, GSD routing, or effective-scale selection may materially affect correspondence.

**If false:**
A comparison may incorrectly attribute improvement to the matcher rather than scale preparation.

**How to verify or mitigate:**
Keep scale/preprocessing methodology explicit during benchmarking.

---

# 8. Illumination Assumptions

## A-22 — Sun-Angle Differences Can Change Visible Structure

**Assumption:**
Illumination differences may change more than brightness.

They can affect:

- shadow direction
- shadow length
- crater-wall visibility
- ridge visibility
- local contrast
- apparent structure

**If false:**
Simple photometric normalization would solve more of the problem than expected.

**How to verify or mitigate:**
Treat illumination robustness as an empirical benchmark question.

---

## A-23 — Photometric Normalization Does Not Eliminate Shadow Geometry

**Assumption:**
Methods such as:

- histogram normalization
- contrast adjustment
- CLAHE
- brightness normalization

may improve intensity comparability but cannot reconstruct terrain hidden by shadows or remove genuine shadow-geometry changes.

**If false:**
The project might overclaim Sun-angle invariance from simple preprocessing.

---

## A-24 — Some Structural Cues Remain Useful in Suitable Cases

**Assumption:**
Despite illumination changes, some features may remain sufficiently stable for correspondence.

Potential cues include:

- crater rims
- ridges
- edges
- gradient structure
- relative feature geometry

**If false:**
Local matching may fail even when the same terrain is present.

**How to verify or mitigate:**
Evaluate illumination-stress cases and preserve failure as valid evidence.

---

# 9. Geometry Assumptions

## A-25 — The Moon Is Not Globally Planar

**Assumption:**
The lunar surface is three-dimensional and contains substantial relief.

ChandraMap must not globally interpret lunar terrain as one flat plane.

This is a scientific fact/context that constrains modeling assumptions.

---

## A-26 — A Planar Transform Can Be a Useful Local Approximation

**Assumption:**
For selected local overlaps, a simple global 2D model such as:

- affine
- homography

may provide a useful baseline approximation.

**Why it is needed:**
Benchmark V1 intentionally uses simple explainable geometry.

**If false:**
Residual distortions may remain even when local correspondences are correct.

Potential causes include:

- strong terrain relief
- large spatial extent
- viewpoint differences
- projection differences

**How to verify or mitigate:**
Inspect residuals, coverage, check-point error where available, and failure patterns. Later versions may evaluate more complex geometry.

---

## A-27 — One Global Transform May Not Explain Every Region

**Assumption:**
Some lunar overlaps may violate the assumptions of one global affine/homography.

**If false:**
A simple global transform may be sufficient.

**If true:**
Local, piecewise, or terrain-aware methods may be worth evaluating in later research.

This does not make such methods a V1 requirement.

---

## A-28 — Transform Direction Is Known

**Assumption:**
Every scientific transformation has a defined mapping direction.

Conceptually:

```text
source
  ↓
transform
  ↓
reference
```

**If false:**
Warping, inverse mapping, error computation, and serialization become ambiguous.

**How to verify or mitigate:**
State direction at public interfaces and in result interpretation.

---

## A-29 — Geometric Estimation Requires Non-Degenerate Support

**Assumption:**
Candidate/inlier geometry provides enough independent spatial information to constrain the selected model.

**If false:**
A numerical estimator may fail or return unstable geometry.

Examples include:

- too few points
- severe clustering
- nearly collinear layouts

**How to verify or mitigate:**
Use model-appropriate degeneracy checks and spatial diagnostics.

---

# 10. Coordinate and CRS Assumptions

## A-30 — Coordinate Convention Is Known

**Assumption:**
Point coordinates have a clearly understood convention.

Potential representations include:

- image `(x, y)`
- array `(row, column)`

These are not assumed to be interchangeable.

**If false:**
Coordinates may be transposed or geometrically misinterpreted.

---

## A-31 — Pixel Origin / Continuous Coordinate Semantics Are Known Where Needed

**Assumption:**
When precise transformation or interpolation behavior depends on coordinate origin or pixel-center conventions, those semantics are known.

**If false:**
Subtle systematic offsets may appear.

**How to verify or mitigate:**
Use library/project contracts explicitly rather than relying on undocumented conventions.

---

## A-32 — Lunar Spatial Data Does Not Use Earth CRS Defaults

**Assumption:**
Lunar coordinates require appropriate planetary spatial definitions.

Do not silently assign:

- WGS84
- EPSG:4326

to lunar data.

**If false:**
Coordinate conversions or overlays may become scientifically incorrect.

---

## A-33 — Longitude Convention May Vary

**Assumption:**
Different lunar products may use different longitude representations.

Potential differences include:

- positive east
- positive west
- `0–360°`
- `-180–180°`

**If false:**
Direct coordinate comparisons may be wrong by convention rather than by physical location.

**How to verify or mitigate:**
Record and normalize conventions explicitly where conversion is required.

---

## A-34 — Reliable Map-Projected Geometry May Be Useful

**Assumption:**
If a product is reliably map-projected/georeferenced, that geometry may legitimately constrain ChandraMap processing.

**If false:**
Useful scientific information may be discarded unnecessarily.

**How to verify or mitigate:**
Preserve trusted geospatial metadata rather than forcing image-only inference when it is unnecessary.

---

# 11. Correspondence Assumptions

## A-35 — Candidate Matches Are Not Correct by Default

**Assumption:**
Matcher output contains hypotheses, including possible correct and incorrect correspondences.

**Why it is needed:**
Repetitive lunar terrain can produce visually plausible false matches.

**If false:**
Geometric verification would appear unnecessary.

**How to verify or mitigate:**
Use explicit geometric verification.

---

## A-36 — Descriptor Similarity Is Not Physical Truth

**Assumption:**
A low descriptor distance or high matcher score is evidence for a candidate correspondence, not proof of physical identity.

**If false:**
Matcher scores may be overinterpreted as ground truth.

---

## A-37 — RANSAC Can Recover Useful Consensus When Enough Correct Candidates Exist

**Assumption:**
If sufficient correct candidates exist and the model is suitable, robust estimation can identify a model-consistent subset.

**If false:**
Possible causes include:

- too few correct matches
- excessive outliers
- unsuitable model
- degeneracy
- poor spatial distribution

**How to verify or mitigate:**
Treat RANSAC failure as valid evidence rather than forcing a transform.

---

## A-38 — RANSAC Inliers Are Model-Consistent, Not Independent Ground Truth

**Assumption:**
An inlier means:

> consistent with the chosen model and threshold.

It does not automatically mean:

> independently verified physical correspondence.

**If false:**
Circular evaluation may result from treating fitted inliers as external truth.

---

## A-39 — Match/Mask Ordering Is Preserved

**Assumption:**
Candidate correspondence order remains aligned with:

- source coordinates
- reference coordinates
- descriptor information
- inlier masks

**If false:**
Geometry can become invalid while software still executes.

This assumption also corresponds to an important implementation invariant that should be protected by tests.

---

## A-40 — Spatial Distribution Matters

**Assumption:**
Verified correspondences distributed across the overlap generally provide stronger support for global geometry than equally many points concentrated in one small region.

**If false:**
Match count would be sufficient evidence.

**How to verify or mitigate:**
Use spatial coverage or related diagnostics where defined.

---

## A-41 — High Inlier Count Does Not Guarantee High Accuracy

**Assumption:**
A large inlier count may coexist with:

- clustered support
- wrong reference region
- incorrect model
- systematic residuals
- weak independent accuracy

**How to verify or mitigate:**
Interpret count alongside residuals, coverage, geometry validity, and independent evaluation where available.

---

# 12. Registration Assumptions

## A-42 — Warp Success Does Not Prove Registration Accuracy

**Assumption:**
A transformation can be numerically applicable even when it is scientifically incorrect.

**If false:**
Software execution would be mistaken for scientific validation.

**How to verify or mitigate:**
Keep registration evaluation separate from image warping.

---

## A-43 — Visual Overlay Is Diagnostic, Not Ground Truth

**Assumption:**
Human visual inspection is useful for:

- debugging
- qualitative review
- communicating results

but is insufficient as the sole accuracy criterion.

**If false:**
Visually plausible but systematically incorrect alignments could be accepted.

---

## A-44 — Rejection Is a Valid Scientific Outcome

**Assumption:**
Some observations cannot be registered reliably with the available evidence/method.

**If false:**
The system may be pressured to return unsupported geometry.

Correct behavior may be:

```text
Rejected
```

rather than:

```text
Forced Transform
```

---

## A-45 — Scientific Failure and Software Error Are Different

**Assumption:**
A scientifically unsuccessful registration can occur even when the software executed correctly.

Examples:

### Scientific failure

- insufficient correspondence
- unsuitable geometry
- non-overlap
- low-feature terrain

### Software error

- indexing defect
- unexpected exception
- incorrect shape handling

**If false:**
Benchmark statistics and debugging conclusions become misleading.

---

# 13. Evaluation and Ground-Truth Assumptions

## A-46 — Independent Check Points Provide Stronger Accuracy Evidence

**Assumption:**
When trusted independent check points exist, they provide stronger evidence of final transform accuracy than fit-point residuals alone.

**Why it is needed:**
Fitting and evaluating on the same points can make a model appear better than it generalizes.

---

## A-47 — Fit Residual Is Not Independent Accuracy

**Assumption:**
Residuals on points used to estimate a transform describe fitting consistency.

They should not automatically be interpreted as held-out registration accuracy.

---

## A-48 — Ground Truth May Be Limited

**Assumption:**
Not every evaluation pair necessarily has:

- dense control
- perfect annotations
- exact geolocation truth
- independent check points

**If false:**
Evaluation can be stronger.

**If true:**
Accuracy claims must remain bounded by available evidence.

---

## A-49 — Ground Truth Can Have Uncertainty

**Assumption:**
Even trusted coordinates or annotations may have finite precision.

**If false:**
Truth would provide unlimited precision.

**How to verify or mitigate:**
Do not report unjustified precision beyond the quality of the evaluation reference.

---

## A-50 — Error Values Require Coordinate Space and Units

**Assumption:**
A value such as:

```text
0.8 px
```

is scientifically meaningful only when the pixel domain is known.

Potential domains include:

- source image
- reference image
- another derived image

**If false:**
Metrics become ambiguous or incomparable.

---

## A-51 — Pixel Error Does Not Automatically Equal Ground Error

**Assumption:**
Converting image-space error to metres requires valid physical/geospatial context.

Relevant information may include:

- product GSD
- projection
- coordinate interpretation
- transformation direction
- local geometry

**If false:**
A nominal instrument GSD may be incorrectly used as a universal conversion factor.

---

## A-52 — Sub-Pixel Does Not Automatically Mean Sub-Metre

**Assumption:**
`Sub-pixel` describes image-coordinate magnitude.

Its physical meaning depends on the specific image and geometry.

Do not infer sub-metre accuracy without valid conversion evidence.

---

## A-53 — Metric Interpretation Depends on Stable Definitions

**Assumption:**
Longitudinal comparisons require the meaning of metrics to remain stable or changes to be documented.

Changes such as:

- fit RMSE → check-point RMSE
- source pixels → reference pixels
- new coverage definition
- changed failure denominator

can break direct comparability.

---

# 14. Retrieval Assumptions

## A-54 — Reliable Metadata May Constrain Search

**Assumption:**
If trustworthy product metadata provides useful spatial constraints, it may legitimately restrict reference search.

This is not considered cheating.

It uses information available from the scientific product.

---

## A-55 — Global Retrieval Is Needed Only When Location Is Insufficiently Constrained

**Assumption:**
Global visual retrieval is primarily relevant when:

- location is unknown
- footprint information is absent
- metadata is unreliable
- the remaining candidate region is too large

**If false:**
Running retrieval for every case would add unnecessary complexity.

---

## A-56 — Retrieval and Registration Have Different Success Conditions

**Assumption:**
Retrieval answers:

> Did the system find the correct candidate region?

Registration answers:

> Did the system establish accurate local geometry?

**If false:**
A retrieval miss may be incorrectly blamed on a local matcher, or local registration failure may be blamed on retrieval.

---

## A-57 — Global and Local Descriptors Serve Different Purposes

**Assumption:**
A global descriptor summarizes a region for retrieval.

A local descriptor/correspondence represents point-level evidence for registration.

They are not substitutes.

---

## A-58 — FAISS Requires Pre-Existing Numerical Descriptors

**Assumption:**
If FAISS is used in later retrieval-enabled configurations, compatible vectors have already been produced.

FAISS performs:

- indexing
- vector similarity search

It does not perform:

- image feature extraction by itself
- local correspondence
- RANSAC
- registration

---

## A-59 — Top-K Retrieval Is Useful Only if the Correct Candidate Is Included

**Assumption:**
Local registration can only recover the correct target from a Top-K retrieval list if the correct reference region appears among those candidates or another search path exists.

**If false:**
Perfect local matching cannot recover a region that was never retrieved.

---

# 15. Benchmark Assumptions

## A-60 — Benchmark Pair Definitions Are Correct

**Assumption:**
Benchmark cases correctly identify:

- source product
- reference product/region
- intended overlap
- representation
- evaluation truth where applicable

**If false:**
A scientifically correct algorithm may appear to fail—or an invalid result may appear successful.

---

## A-61 — Benchmark Membership Is Controlled

**Assumption:**
The benchmark population is known and reproducible.

A canonical benchmark should not mean:

> whatever files happen to exist in this directory today.

**If false:**
Aggregate results may change because the dataset changed rather than the method.

---

## A-62 — Direct Version Comparison Uses Comparable Pair Populations

**Assumption:**
Where V1–V4 are being directly compared, the relevant evaluation population is controlled and comparable.

**If false:**
Differences may reflect different data rather than methodological improvement.

---

## A-63 — Configuration Is Part of the Method

**Assumption:**
Important settings materially define benchmark methodology.

These may include:

- preprocessing
- scale handling
- matcher/filtering
- RANSAC
- transformation model
- refinement
- quality gates

**If false:**
Two apparently identical method labels may represent different pipelines.

---

## A-64 — No Hidden Per-Pair Tuning Is Used in Canonical Evaluation

**Assumption:**
Normal benchmark configuration is not manually changed after examining final truth for each individual test pair.

**If false:**
Evaluation may become optimistic and irreproducible.

**How to verify or mitigate:**
Use fixed or inference-available rules and preserve effective configuration.

---

## A-65 — No Ground-Truth Leakage Occurs

**Assumption:**
Final evaluation truth does not improperly influence:

- candidate retrieval
- matcher choice
- threshold selection
- transformation fitting
- per-pair model selection

unless the experiment is explicitly an oracle analysis.

**If false:**
Independent benchmark interpretation is invalid.

---

## A-66 — Failed and Rejected Cases Remain Visible

**Assumption:**
Benchmark interpretation includes unsuccessful cases rather than silently removing them.

**If false:**
Success rates and conditional accuracy may appear artificially strong.

---

## A-67 — Runtime Comparisons Use Comparable Scope

**Assumption:**
Timing comparisons refer to equivalent or explicitly documented stages.

Potential differences include:

- preprocessing
- model loading
- retrieval
- matching
- geometry
- complete pipeline

**If false:**
Runtime conclusions may be misleading.

---

## A-68 — Runtime Context Can Matter

**Assumption:**
Hardware and execution context may materially affect runtime.

Relevant factors may include:

- CPU vs GPU
- input size
- model initialization
- cache state
- concurrency

No specific hardware requirement is assumed by this document.

---

# 16. Benchmark V1 Assumptions

The following assumptions are specific to the canonical classical baseline and must remain consistent with [`v1-scope.md`](./v1-scope.md) and the canonical [AI V1 Scope](../../.ai/context/V1_SCOPE.md).

## V1-A1 — Correct Overlapping Reference Region Is Supplied

Canonical V1 assumes the correct approximate reference region is already known.

Therefore V1 does not solve whole-Moon localization.

If this assumption is removed, the task becomes a different problem involving retrieval/localization.

---

## V1-A2 — Known Overlap Does Not Mean Known Transform

V1 still needs to estimate correspondence and geometry despite receiving the correct region.

The pair may still differ significantly in:

- GSD
- illumination
- viewpoint
- modality
- extent

---

## V1-A3 — Inputs Are Suitable 2D Registration Representations

V1 assumes the local classical pipeline receives appropriate 2D inputs.

Native full hyperspectral IIRS processing is not a canonical V1 requirement.

---

## V1-A4 — Minimal Generic Preprocessing Is Sufficient for a Baseline

V1 assumes that a deliberately simple preprocessing path is adequate for establishing a classical reference baseline.

It does not assume this preprocessing solves every difficult sensor condition.

---

## V1-A5 — Classical Local Features Can Produce Useful Evidence on Suitable Cases

V1 assumes SIFT-based local features can detect useful structure on at least some controlled lunar pairs.

It does not assume SIFT always succeeds.

---

## V1-A6 — Classical Descriptor Matching Can Produce Candidate Correspondences

V1 assumes a conventional matcher can generate enough candidate relationships for geometry on suitable cases.

Failure to do so on difficult pairs is valid baseline evidence.

---

## V1-A7 — RANSAC Can Recover Model-Consistent Support When Enough Correct Candidates Exist

V1 assumes robust estimation is useful when its preconditions are met.

This does not imply every RANSAC inlier is physically correct.

---

## V1-A8 — Affine / Homography Can Be Useful Local Baseline Models

V1 uses simple global 2D geometry as a baseline approximation.

The assumption may fail for:

- large regions
- strong relief
- significant local deformation
- unsuitable projection/viewpoint conditions

Such failures help motivate later research.

---

## V1-A9 — Global Retrieval Is Unnecessary for the Canonical V1 Task

Because the overlapping region is supplied, V1 does not assume the need for:

- global descriptors
- FAISS
- Top-K retrieval

---

## V1-A10 — Advanced Sensor-Specific Processing Is Intentionally Excluded

V1 does not assume sophisticated sensor routing is required to establish the classical baseline.

Those capabilities belong primarily to later versions.

---

## V1-A11 — Advanced Refinement Is Not Required Unless the Canonical Scope Explicitly Includes It

V1 should not quietly adopt later-stage refinement merely to improve metrics.

---

## V1-A12 — Difficult-Case Failure Is Valid Baseline Evidence

A scientifically correct V1 implementation may fail on:

- extreme scale gaps
- severe illumination differences
- cross-modality cases
- low-feature terrain
- repetitive terrain

Such failures do not automatically mean the implementation is incomplete.

---

## V1 Assumptions Are Not Universal ChandraMap Assumptions

V1 assumes known overlap.

ChandraMap as a whole does not permanently assume known overlap.

V1 uses simple global geometry.

Later research may investigate:

- local geometry
- terrain-aware geometry
- retrieval
- learned correspondence
- stronger scale handling

Version-specific assumptions should remain version-specific.

---

# 17. Later-Version Assumptions

These assumptions describe conceptual research dependencies only. They do not claim implementation status.

## V2 — Sensor / Scale-Aware Research

Potential V2 assumptions may include:

- sensor identity is known well enough for routing
- GSD information is meaningful enough for scale handling
- reference pyramids provide useful comparable scales
- sensor-specific representations may improve difficult correspondence
- derived IIRS representations can be evaluated systematically

These are hypotheses/modeling assumptions to investigate, not guaranteed truths.

---

## V3 — Advanced Matching / Retrieval Research

Potential V3 assumptions may include:

- learned matchers have compatible inputs/checkpoints
- pretrained methods can produce useful lunar correspondence despite domain shift
- global descriptors produce meaningful candidate-region similarity
- reference tiles are indexed correctly
- vector IDs map correctly back to reference regions
- Top-K retrieval has a reasonable chance of containing the correct region

Their validity must be measured.

---

## V4 — Advanced Robustness Research

Potential V4 assumptions may include:

- sufficient local support exists for refinement
- more complex geometry is justified by observed residual structure
- suitable DEM/DTM information is available where terrain-aware methods are tested
- confidence information can be meaningfully calibrated if probability-like interpretation is desired
- additional complexity provides measurable scientific value

V4 assumptions are research conditions, not commitments that those methods will always improve performance.

---

# 18. Engineering and Runtime Assumptions

## A-69 — External Scientific Files Are Untrusted Software Input

**Assumption:**
Mission files, metadata, archives, configuration, and model checkpoints may originate outside the repository.

From a software-security perspective, they should not be trusted automatically.

Detailed handling belongs in project security and coding rules.

---

## A-70 — Scientific Core Should Not Depend on Undocumented Live Network Access

**Assumption:**
Reproducible processing should know which inputs it requires rather than silently downloading unknown dependencies during ordinary execution.

This does not claim that every ChandraMap workflow is fully offline.

It is a reproducibility design principle.

---

## A-71 — Heavy Learned/Vector Dependencies Do Not Define Canonical V1

**Assumption:**
The classical baseline does not require:

- GPU-only execution
- large neural frameworks
- learned checkpoints
- vector retrieval infrastructure

unless the authoritative V1 contract is deliberately changed.

---

## A-72 — CPU and GPU Results May Not Be Bitwise Identical

**Assumption:**
Later numerical/learned workflows may exhibit small differences across hardware and environments.

Reproducibility should focus on scientifically equivalent behavior unless exact determinism is explicitly demonstrated.

---

## A-73 — Randomness May Affect Some Components

**Assumption:**
Components such as:

- robust sampling
- stochastic ML procedures
- some hardware-backed operations

may contain randomness or nondeterminism.

**How to verify or mitigate:**
Control and record seeds where meaningful.

Do not claim universal determinism without evidence.

---

## A-74 — Dependency Changes May Affect Numerical Behavior

**Assumption:**
Changes in numerical, image-processing, ML, or geospatial dependencies may alter results.

**If false:**
Dependency updates would be scientifically neutral.

**How to verify or mitigate:**
Investigate unexpected benchmark changes after relevant dependency updates.

This document does not assume any specific dependency is present.

---

## A-75 — Software Tests and Scientific Benchmarks Provide Different Evidence

**Assumption:**
Passing tests shows that implementation behavior satisfies defined checks.

It does not establish strong scientific performance on lunar data.

Likewise, one good lunar benchmark result does not prove the implementation has no software defect.

Both forms of evidence are needed for different questions.

---

## A-76 — UI and API Layers Consume Scientific Results

**Assumption:**
Where presentation or service layers exist, they should use authoritative scientific outputs rather than independently redefine:

- inliers
- RMSE
- transform validity
- acceptance

**If false:**
Different interfaces could present contradictory scientific truth.

---

## A-77 — Mosaic Quality Depends on Registration Quality

**Assumption:**
A downstream mosaic is only as trustworthy as the registrations used to construct it.

A visually smooth mosaic does not independently validate the underlying correspondences.

---

## A-78 — Map UI Is Presentation, Not Scientific Validation

**Assumption:**
A map visualization displays scientific results.

It does not establish the correctness of:

- geolocation
- transforms
- correspondences
- registration metrics

---

## A-79 — Documentation May Describe Target or Experimental State

**Assumption:**
A documented capability is not automatically implemented.

Project status must distinguish:

- Documented
- Implemented
- Tested
- Experimental
- Planned
- Proposed

---

# 19. Reproducibility Assumptions

## A-80 — Configuration Is Reproducibility Data

**Assumption:**
Scientifically meaningful configuration is part of the experiment/run context.

Without configuration, a result may not be reconstructable.

---

## A-81 — Code Revision Can Affect Scientific Results

**Assumption:**
Changes to:

- algorithms
- preprocessing
- geometry
- metrics
- dependencies

may change output.

Important benchmark results should therefore be traceable to software revision where practical.

---

## A-82 — Data Identity Is Part of Reproducibility

**Assumption:**
Knowing only the algorithm name is insufficient.

A scientific run should ideally be traceable to the actual:

- source product
- reference product
- representation
- benchmark pair

---

## A-83 — Model/Checkpoint Identity Matters Where Learned Methods Are Used

**Assumption:**
Two learned-model checkpoints may produce different results.

Where relevant, checkpoint identity is part of reproducibility context.

This does not imply learned methods are currently implemented.

---

## A-84 — Seed Information Matters Where Randomness Matters

**Assumption:**
A random seed can affect some stages.

When the stage is stochastic and the project supports seed control, seed information should be preserved where scientifically relevant.

---

## A-85 — Reproducibility Does Not Always Mean Bitwise Identity

**Assumption:**
Scientifically reproducible behavior may permit small numerical variation across:

- hardware
- dependency versions
- execution environments

provided the methodology and scientific interpretation remain equivalent.

---

# 20. Assumptions We Must Not Make

This section lists particularly dangerous hidden assumptions that should **not** be used as ChandraMap scientific reasoning.

| Do Not Assume                                                       | Why                                                                                 |
| ------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| Upsampling increases physical resolution                            | Interpolation cannot recreate unmeasured terrain detail                             |
| Same image dimensions mean same ground scale                        | Pixel count and GSD are different concepts                                          |
| All products from one instrument have one exact GSD                 | Product-specific metadata may vary                                                  |
| All scientific rasters are `uint8` grayscale                        | Scientific products may use other types, ranges, bands, and masks                   |
| All multiband arrays use the same axis order                        | Product layouts vary                                                                |
| IIRS is just a low-resolution grayscale image                       | IIRS is hyperspectral / imaging-infrared data                                       |
| All sensors should produce equal raw intensity for the same terrain | Sensor modality and illumination differ                                             |
| Photometric normalization removes Sun-angle effects completely      | Shadow geometry and visibility can change physically                                |
| Known overlap means the transform is known                          | Overlap only identifies the region, not alignment                                   |
| Known overlap means matching is easy                                | Scale, illumination, modality, and geometry may still differ                        |
| More candidate matches means better registration                    | Candidate count says little about correctness or spatial support                    |
| More RANSAC inliers guarantees higher accuracy                      | Inliers may be clustered or consistent with an imperfect model                      |
| RANSAC inlier means ground truth                                    | It means model-consistent under the chosen estimator/threshold                      |
| Matcher confidence is calibrated correctness probability            | Matching scores may not be calibrated                                               |
| Successful warp means accurate registration                         | Warping only applies a transform                                                    |
| Visual alignment proves scientific accuracy                         | Visual inspection is qualitative evidence                                           |
| Fit residual equals independent accuracy                            | Fitting points may favor the model used to estimate them                            |
| Sub-pixel means sub-metre                                           | Physical scale and geometry determine metre interpretation                          |
| `1 px` always equals nominal sensor GSD                             | Coordinate/product context matters                                                  |
| Lunar data uses Earth WGS84                                         | Lunar data requires appropriate lunar spatial definitions                           |
| All lunar products use the same longitude convention                | Positive direction/range can differ                                                 |
| Reference imagery is perfect ground truth                           | Reference products can have their own limitations                                   |
| Metadata is always complete                                         | Required fields may be absent                                                       |
| Metadata is always wrong and should be ignored                      | Reliable metadata may legitimately constrain processing                             |
| Global retrieval is always necessary                                | Known location/metadata may already constrain the search                            |
| FAISS performs local image matching                                 | FAISS performs vector indexing/search                                               |
| Global retrieval failure means local matcher failure                | The local matcher may never receive the correct candidate                           |
| Local matcher failure means retrieval failure                       | Retrieval may return the correct region but local registration may still fail       |
| Pretrained terrestrial models generalize perfectly to lunar imagery | Domain shift must be measured                                                       |
| Learned method means better method                                  | Benchmark evidence decides                                                          |
| V4 must outperform V1                                               | Version order is not performance order                                              |
| Benchmark success proves implementation correctness                 | Scientific performance and software correctness are different                       |
| Passing software tests proves registration accuracy                 | Tests validate software behavior, not full scientific performance                   |
| Documentation means implemented                                     | Status must be verified separately                                                  |
| Every failure has the same cause                                    | Data, retrieval, matching, geometry, evaluation, or software may fail independently |
| Pipeline completion means registration success                      | Scientific acceptance is separate from execution completion                         |
| Default parameters are scientifically optimal                       | Library defaults are not lunar-specific evidence                                    |
| One threshold is a universal physical law                           | Thresholds are methodological decisions                                             |
| Benchmark results generalize to the entire Moon                     | Conclusions are bounded by evaluated populations                                    |
| Internal V1–V4 comparison proves state of the art                   | External comparison requires compatible evaluation                                  |

---

# 21. Validating Assumptions

Assumptions should be validated at the most appropriate layer rather than through one universal mechanism.

## Input Validation

Potential checks include:

- dimensionality
- data type
- finite values
- readable data
- valid masks
- required metadata presence

Input validation confirms structural assumptions.

It does not prove scientific registration quality.

---

## Dataset Validation

Validate where appropriate:

- source/reference identity
- pair membership
- intended overlap
- product provenance
- representation identity

This protects benchmark and scientific assumptions about the data.

---

## Metadata Validation

Check:

- sensor identity
- GSD
- projection
- CRS
- footprint
- longitude convention

where they are used.

Do not invent values when metadata is absent.

---

## Scientific Validation

Empirically evaluate assumptions such as:

- whether structural cues survive illumination change
- whether the planar model is adequate
- whether scale preparation improves matching
- whether a derived IIRS representation contains useful spatial structure
- whether learned models transfer adequately to lunar data

These are scientific questions rather than simple schema checks.

---

## Benchmark Validation

Verify:

- benchmark membership
- same-pair comparison where required
- fixed methodology
- metric definitions
- failure treatment
- no leakage
- no hidden per-pair tuning

---

## Geospatial Validation

Confirm where relevant:

- lunar CRS
- projection
- coordinate direction
- longitude convention
- source/reference transform semantics

---

## Reproducibility Validation

Preserve enough context to reconstruct important runs, potentially including:

- data identity
- configuration
- software revision
- seeds
- model/checkpoint identity
- metric definitions
- environment context where material

This document does not prescribe a specific provenance schema.

---

# 22. What to Do When an Assumption Is False

An assumption being false should not be hidden merely because the software can continue.

Possible responses include:

### Reject the Input

Appropriate when the required precondition is fundamental.

Example:

> input cannot be interpreted as the expected representation.

---

### Return Scientific Failure / Rejection

Appropriate when the input is valid but the method cannot establish reliable geometry.

Example:

> insufficient geometrically consistent correspondences.

---

### Route to a Different Method

Appropriate only when the project defines a reproducible fallback.

Example conceptually:

> a later sensor-aware or terrain-aware branch is selected because the baseline assumption no longer holds.

---

### Mark Unsupported

Appropriate where the active configuration intentionally does not handle a representation/problem class.

---

### Invalidate the Benchmark Run

Appropriate when the experiment itself was compromised.

Examples:

- wrong pair definition
- leaked truth
- incorrect configuration
- invalid metric implementation

---

### Update Methodology

Appropriate when repeated evidence shows a modeling assumption is systematically wrong.

A methodological change should be documented and may affect historical comparability.

---

### Escalate to a Later Benchmark Configuration

Some assumptions are intentionally relaxed by later research.

For example:

```text
V1
→ known overlap + simple geometry

V2
→ stronger sensor / scale handling

V3
→ advanced matching / retrieval

V4
→ advanced geometry / refinement / uncertainty
```

This progression should remain consistent with the authoritative version specifications.

---

# 23. Hard and Soft Assumptions

Where useful, ChandraMap assumptions can be understood as two broad types.

## Hard Assumption

A condition without which the intended experiment/workflow is no longer valid.

Example:

> Canonical V1 receives the intended known-overlap reference region.

If this is false, the experiment is no longer measuring the intended V1 problem.

---

## Modeling / Soft Assumption

A simplifying condition that may be imperfect while the workflow still executes.

Example:

> A homography adequately approximates the local terrain.

If false, registration quality may degrade or the case may be rejected.

This classification is conceptual and does not require every assumption to be formally labelled.

---

# 24. Testability of Assumptions

Different assumptions require different forms of evidence.

| Assumption Type         | Example                                              | Typical Validation              |
| ----------------------- | ---------------------------------------------------- | ------------------------------- |
| Directly testable       | Input has expected dimensionality                    | Software validation             |
| Metadata-verifiable     | Sensor/product identity                              | Product metadata/provenance     |
| Geospatially verifiable | CRS/longitude convention                             | Product/geospatial metadata     |
| Empirically testable    | Homography is adequate locally                       | Residual/check-point evaluation |
| Benchmark-controlled    | Pair belongs to evaluation population                | Manifest/protocol               |
| Externally provided     | Provider metadata reflects acquisition context       | Authoritative data source       |
| Research assumption     | Derived representation preserves matchable structure | Controlled experiment           |

Not every assumption can be proven by a unit test.

---

# 25. Assumptions, Limitations, and Risks

## Assumption

A condition accepted for a particular method, workflow, or interpretation.

Example:

> Local terrain is sufficiently approximated by a homography.

---

## Limitation

A known boundary or weakness resulting from the method or available data.

Example:

> Relief-heavy terrain may retain local residual distortion under one homography.

---

## Risk

An assumption becomes a risk when:

1. its truth is uncertain, and
2. the impact of being wrong is significant.

This document describes consequences when assumptions fail.

It does not assign unsupported numerical probabilities, confidence values, or risk scores.

---

# 26. Assumptions and Failure Analysis

Before changing algorithms after a failure, inspect whether an underlying assumption was violated.

Conceptually:

```text
Registration Rejected
        ↓
Check Assumptions
        ↓
Was overlap correct?
Was reference candidate correct?
Was scale context valid?
Was representation appropriate?
Were enough distinctive features present?
Was the geometric model adequate?
Were coordinates interpreted correctly?
        ↓
Only then attribute the failure
```

This helps distinguish:

- data failure
- retrieval failure
- correspondence failure
- geometry failure
- evaluation failure
- software defect

Do not assume every unsuccessful registration has the same cause.

---

# 27. Assumptions and Benchmark Progression

One purpose of Benchmark V1–V4 is to progressively relax or address earlier assumptions.

Conceptually:

### Benchmark V1

Assumes:

- known overlap
- suitable 2D representation
- classical local features
- simple global geometry

---

### Benchmark V2

Investigates whether stronger:

- sensor awareness
- GSD awareness
- representation handling
- scale handling

reduces limitations exposed by V1.

---

### Benchmark V3

May relax assumptions around:

- known location
- classical-only matching
- simple candidate search

through advanced correspondence and retrieval.

---

### Benchmark V4

May investigate assumptions around:

- global geometry
- refinement precision
- terrain effects
- confidence/uncertainty
- advanced failure handling

Later versions are not assumed to outperform earlier ones automatically.

Their contribution must be measured.

---

# 28. Assumption Change Policy

Review an assumption when:

- supported data changes
- a new sensor/modality is introduced
- benchmark scope changes
- geometry changes
- retrieval is introduced
- evaluation truth changes
- metric semantics change
- evidence repeatedly contradicts the assumption
- a major architecture decision changes how scientific context is represented

Do not change assumptions casually merely to make difficult cases pass.

---

## Impact of an Assumption Change

Changing a major assumption may require reviewing:

- [`overview.md`](./overview.md)
- [`problem-statement.md`](./problem-statement.md)
- [`goals.md`](./goals.md)
- [`non-goals.md`](./non-goals.md)
- [`v1-scope.md`](./v1-scope.md)
- [`terminology.md`](./terminology.md)
- dataset documentation
- pipeline documentation
- benchmark rules
- metrics
- tests
- result interpretation

For example:

```text
Known Overlap
```

changing to:

```text
Unknown Location
```

does not merely change one flag.

It introduces a retrieval/localization problem.

---

# 29. Assumption Traceability

Not every local code assumption requires a formal registry entry.

Traceability is most valuable for assumptions that materially affect:

- benchmark interpretation
- scientific validity
- coordinate meaning
- dataset eligibility
- transformation validity
- accuracy claims
- version boundaries

Where practical, important assumptions should be traceable to the relevant:

- benchmark version
- dataset/pair
- configuration
- evaluation protocol

The goal is scientific clarity, not unnecessary bureaucracy.

---

# 30. Relationship with Other Documents

## [`terminology.md`](./terminology.md)

Defines:

> what ChandraMap terms mean.

This document defines:

> what conditions ChandraMap assumes about those concepts.

Example:

`terminology.md`:

> Homography is a planar projective transformation.

`assumptions.md`:

> A homography may sufficiently approximate the selected local region for a baseline experiment.

---

## [`problem-statement.md`](./problem-statement.md)

Defines:

> the scientific and engineering challenge.

This document defines:

> what is taken as given while addressing that challenge.

---

## [`goals.md`](./goals.md)

Defines:

> desired ChandraMap outcomes.

This document defines:

> conditions under which those outcomes are pursued and interpreted.

---

## [`non-goals.md`](./non-goals.md)

Defines:

> what does not currently define ChandraMap success.

This document defines:

> what ChandraMap assumes when solving what is in scope.

---

## [`v1-scope.md`](./v1-scope.md)

Defines:

> canonical V1 inclusions and exclusions.

This document defines:

> the assumptions underlying that baseline.

If there is any conflict concerning canonical V1 scope, `v1-scope.md` and the canonical `.ai/context/V1_SCOPE.md` remain authoritative.

---

## [Dataset Context](../../.ai/context/DATASETS.md)

Owns detailed:

- product context
- sensor information
- provenance
- metadata handling
- dataset roles

This assumptions file should not replace dataset documentation.

---

## [Processing Pipeline](../../.ai/architecture/PIPELINE.md)

Defines:

> processing order.

This document defines:

> conditions those stages may rely upon.

---

## [Data Flow](../../.ai/architecture/DATA_FLOW.md)

Defines:

> scientific data, coordinate, transform, and result semantics.

This document identifies assumptions that must hold for those semantics to remain valid.

---

## [Benchmark Rules](../../.ai/development/BENCHMARK_RULES.md)

Define:

> how ChandraMap comparisons should remain fair and reproducible.

This document makes underlying benchmark assumptions explicit.

---

## [Testing Rules](../../.ai/development/TESTING_RULES.md)

Define:

> how software and scientific behavior should be tested.

This document identifies assumptions that can be protected through validation, testing, or controlled experimentation.

---

## [Canonical AI Terminology](../../.ai/context/TERMINOLOGY.md)

Provides stricter terminology for AI agents and maintainers.

Human-facing assumptions should remain consistent with those canonical scientific distinctions.

---

# 31. Key Assumption Rules

1. An assumption is not automatically a fact.

2. An assumption is not a requirement.

3. An assumption is not an invariant.

4. An assumption is not a hypothesis.

5. A configuration default is not automatically a scientific assumption.

6. Source/reference roles must be explicit.

7. Local registration assumes meaningful physical overlap.

8. Canonical V1 assumes the intended overlapping reference region is supplied.

9. Known overlap does not mean the transform is known.

10. Known overlap does not mean correspondence is easy.

11. Scientifically important inputs should remain identifiable.

12. Product-specific metadata should take precedence over broad instrument approximations.

13. Missing metadata should remain unknown rather than being fabricated.

14. Sensor identity should not be guessed silently when routing depends on it.

15. Scientific arrays should not be assumed to be `uint8` grayscale.

16. Multiband/hyperspectral layout should not be assumed without product context.

17. IIRS is hyperspectral / imaging-infrared data.

18. Conventional 2D matching assumes an appropriate 2D representation.

19. An IIRS-derived representation is a methodological choice, not universal truth.

20. Raw intensity equality is not assumed across sensors.

21. GSD differences represent real physical information differences.

22. Upsampling does not create physical terrain detail.

23. Downsampling is not lossless.

24. Illumination changes can alter shadow geometry and visible structure.

25. Brightness normalization is not equivalent to Sun-angle invariance.

26. Some structural cues may remain useful, but this must be demonstrated empirically.

27. The Moon is not globally planar.

28. V1's affine/homography geometry is a local modeling assumption.

29. Planar-model suitability may fail in relief-heavy or large regions.

30. Transform direction must be known.

31. `(x, y)` and `(row, column)` are not automatically interchangeable.

32. Pixel origin/center conventions should not be guessed.

33. Lunar data must not silently use Earth WGS84 defaults.

34. Longitude conventions may differ.

35. Valid geospatial metadata may legitimately constrain search.

36. Global retrieval is not necessary when location is already adequately constrained.

37. Candidate matches are not assumed correct.

38. Descriptor similarity is not physical ground truth.

39. Matcher confidence is not automatically calibrated probability.

40. RANSAC can only recover useful geometry when its assumptions are sufficiently satisfied.

41. RANSAC inliers are model-consistent, not independent ground truth.

42. Candidate/inlier index alignment must be preserved.

43. Spatial distribution matters in addition to correspondence count.

44. More inliers do not automatically mean more accurate registration.

45. Warp completion does not prove scientific success.

46. Visual overlay is diagnostic evidence, not accuracy proof.

47. Independent check points provide stronger validation where available.

48. Fit residual is not automatically independent accuracy.

49. Ground truth may be incomplete.

50. Ground truth may have uncertainty.

51. Error metrics require explicit coordinate domains and units.

52. Pixel error does not automatically equal ground error.

53. Sub-pixel does not automatically mean sub-metre.

54. Rejection is a valid scientific outcome.

55. Scientific failure and software error are different.

56. Benchmark pair definitions are assumed to be correct.

57. Benchmark membership must be controlled.

58. Direct version comparisons require comparable evaluation populations.

59. Metric semantics must remain stable or changes must be documented.

60. Configuration is part of benchmark methodology.

61. Hidden per-pair tuning invalidates ordinary benchmark interpretation.

62. Ground-truth leakage invalidates independent evaluation.

63. Failed and rejected cases should remain visible.

64. Runtime comparison requires comparable or documented conditions.

65. Retrieval and registration have different success conditions.

66. Global descriptors and local descriptors serve different purposes.

67. FAISS performs vector search, not local image registration.

68. Correct Top-K retrieval is a prerequisite for downstream local registration when retrieval is the only search path.

69. Learned methods may experience lunar domain shift.

70. Later benchmark versions are not assumed to be better.

71. Original and derived scientific data must remain distinguishable.

72. Important derived representations should preserve provenance.

73. No-data regions should not be treated as ordinary terrain.

74. Filenames alone are not ideal scientific provenance.

75. External scientific files and models should be treated as untrusted software input.

76. V1 should not depend on later-version heavy infrastructure merely for convenience.

77. Numerical results may vary slightly across environments.

78. Randomness should be controlled/recorded where meaningful.

79. Configuration, data identity, code revision, and model identity may be reproducibility context.

80. Dependency changes may affect numerical behavior.

81. Software tests do not prove scientific performance.

82. Scientific benchmark performance does not prove implementation correctness.

83. UI/API layers should consume authoritative scientific results.

84. Mosaic quality depends on underlying registration quality.

85. A map interface does not establish scientific correctness.

86. Documentation does not prove implementation.

87. Benchmark results apply to the evaluated population, not automatically to the entire Moon.

88. Internal V1–V4 comparison alone does not establish state-of-the-art performance.

89. Assumptions should be revisited when evidence shows they are false.

90. When an assumption fails, the correct response may be rejection, rerouting, benchmark invalidation, or methodology revision—not silent continuation.

<!-- Source specification: :contentReference[oaicite:0]{index=0} -->
