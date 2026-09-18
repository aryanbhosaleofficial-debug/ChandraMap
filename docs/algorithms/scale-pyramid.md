You are acting as a:

- Senior Software Architect
- Senior Technical Writer
- AI/ML Engineer
- Computer Vision Engineer
- Feature Detection / Description Engineer
- Image Matching Engineer
- Image Registration Engineer
- Remote Sensing Engineer
- Planetary Image Processing Engineer
- Lunar Imaging Engineer
- Geospatial Engineer
- Scientific Computing Engineer
- Research Engineer
- Benchmarking / Evaluation Engineer
- Reproducibility Engineer
- Open-Source Repository Maintainer

Your task is to create ONE complete, professional, production-quality Markdown
documentation file for my GitHub project.

============================================================
TARGET FILE
============================================================

Repository:

ChandraMap/

Target file:

ChandraMap/docs/algorithms/sift.md

Output format:

.md / GitHub Markdown

IMPORTANT:

Return ONLY the complete contents of:

docs/algorithms/sift.md

The result must be directly copy-pasteable into the file.

Do NOT wrap the entire output inside a Markdown code fence.

Do NOT explain what you are going to write before the document.

Do NOT provide commentary after the document.

Start directly with the Markdown document.

============================================================
PROJECT
============================================================

Project Name:
ChandraMap

Repository Type:
Open-source personal/research/portfolio software project.

Domain:

- Lunar image correspondence
- Lunar image registration
- Multi-sensor image matching
- Cross-resolution image matching
- Cross-modality image matching
- Cross-mission image matching
- Geospatial localization
- Global lunar image retrieval
- Computer vision
- Remote sensing
- Planetary image processing
- Scientific benchmarking
- Machine learning
- Reproducible research
- Lunar mapping

Original problem context:

SIH 26166 — Multi-modal, Sun-angle and scale invariant image
correspondence using Chandrayaan-2 optical imagery.

However, ChandraMap is now being developed beyond the original
hackathon context as a serious open-source research and engineering
project.

The documentation therefore must NOT read like a hackathon submission.

It should read like documentation for a professional open-source
scientific/computer-vision repository.

============================================================
WHAT CHANDRAMAP DOES
============================================================

ChandraMap attempts to establish reliable correspondence and
registration between lunar images captured by different sensors or
missions despite major differences in:

- Ground Sampling Distance (GSD)
- Spatial resolution
- Sensor modality
- Spectral response
- Image scale
- Sun angle
- Illumination
- Shadow geometry
- Viewing geometry
- Terrain relief
- Projection
- Coordinate systems
- Processing state
- Contrast
- Noise
- Acquisition geometry

Primary Chandrayaan-2 source instruments include:

- OHRC
- TMC-2
- IIRS

Important reference imagery includes:

- LRO NAC
- LRO WAC

Optional/future datasets may include:

- Kaguya / SELENE Terrain Camera
- Lunar DEM/elevation products
- Additional lunar missions
- Synthetic lunar stress-test data

The core technical deliverable is NOT merely a lunar mosaic.

Core outputs include:

- Candidate correspondences
- Geometrically verified inliers
- Registration / transformation model
- Registered image or preview
- Geolocation where scientifically justified
- Registration error
- Inlier count
- Inlier ratio
- Spatial coverage
- Retrieval metrics where applicable
- Runtime
- Failure diagnostics
- Reproducible benchmark results

A lunar mosaic or map UI is a downstream demonstration.

============================================================
PURPOSE OF THIS FILE
============================================================

docs/algorithms/sift.md documents the ROLE OF SIFT IN CHANDRAMAP.

SIFT should be presented primarily as:

THE CLASSICAL SPARSE-FEATURE BASELINE

for controlled correspondence and registration benchmarking.

The file should explain:

- What SIFT is
- Why ChandraMap uses SIFT as a baseline
- What SIFT detects
- What SIFT descriptors represent
- How SIFT scale-space works conceptually
- What SIFT's orientation handling does
- What SIFT outputs
- How descriptors are matched
- Why descriptor matches are only candidate matches
- How ratio filtering may be used
- How mutual/cross-check filtering may be used
- Why filtering thresholds must be benchmark/config driven
- Why RANSAC is required after SIFT matching
- How verified inliers are obtained
- How transformations are estimated
- How sub-pixel refinement follows geometric verification
- How SIFT interacts with ChandraMap's physical scale pyramid
- Why SIFT scale-space does NOT remove the need for GSD-aware reference
  scaling
- How SIFT behaves differently for OHRC, TMC-2, and IIRS
- Why IIRS cannot simply feed a raw hyperspectral cube into SIFT
- How illumination differences affect SIFT
- How repetitive crater terrain can produce false SIFT matches
- How low-feature terrain can produce too few keypoints
- How SIFT results are evaluated
- How SIFT failures should be recorded
- How SIFT serves as the benchmark reference for stronger methods
- How V1/V2/V3/V4 may continue to use SIFT as a baseline

This document should explain SIFT deeply enough for a new contributor
to understand its scientific and engineering role.

Do NOT turn it into a generic standalone SIFT tutorial unrelated to
ChandraMap.

============================================================
CORE SIFT PRINCIPLE
============================================================

Use this principle prominently:

> In ChandraMap, SIFT is a reproducible baseline for proposing sparse
> local correspondences; geometric verification and independent
> evaluation determine whether those correspondences are actually
> useful for lunar registration.

============================================================
SECOND CORE PRINCIPLE
============================================================

Use this distinction consistently:

SIFT detection/description
→ local features

Descriptor matching
→ candidate matches

RANSAC/geometric verification
→ verified inliers

Transformation estimation
→ registration model

Independent check points
→ registration accuracy

Do NOT collapse these stages into:

"SIFT matching solved the registration."

============================================================
IMPORTANT TERMINOLOGY
============================================================

Use precise terminology.

KEYPOINT:

A local image location detected as potentially distinctive.

KEYPOINT SCALE:

The local image scale associated with the detected feature.

KEYPOINT ORIENTATION:

The orientation associated with the local feature for descriptor
normalization.

DESCRIPTOR:

A numeric vector representing the local appearance around a keypoint.

DESCRIPTOR MATCH:

A proposed association between descriptors in source and reference.

CANDIDATE MATCH:

A descriptor-level correspondence not yet geometrically verified.

INLIER:

A candidate match consistent with the selected geometric model under
geometric verification.

OUTLIER:

A candidate match rejected by geometric verification.

RANSAC:

A robust geometric estimation procedure used to estimate a model while
rejecting inconsistent correspondences.

SCALE-SPACE:

The internal multi-scale image representation used by SIFT for feature
detection.

REFERENCE PYRAMID:

ChandraMap's physically motivated reference-scale hierarchy based on
source/reference GSD.

IMPORTANT:

SIFT scale-space

and:

ChandraMap physical reference pyramid

are NOT the same thing.

============================================================
SIFT'S ROLE IN CHANDRAMAP
============================================================

SIFT should be described as the classical baseline because it provides:

- established local-feature methodology
- interpretable sparse keypoints
- deterministic/reproducible benchmarking when configuration is fixed
- relatively simple failure analysis
- a strong reference point for learned or multimodal methods
- a useful first milestone before adding more complex models

Do NOT describe SIFT as:

- lunar-specific
- Sun-angle invariant
- multimodal invariant
- guaranteed scale invariant across arbitrary sensor GSD gaps
- guaranteed to succeed

============================================================
SENSOR CONTEXT
============================================================

Use these project values conservatively.

---

## OHRC

Orbiter High Resolution Camera

Chandrayaan-2

Visible / panchromatic.

Approximate project documentation:

~0.25–0.32 m/pixel

depending on product/documentation.

SIFT considerations:

- many fine-scale keypoints may be available
- repetitive crater/texture patterns may generate ambiguity
- strong shadow differences may change detections/descriptors
- high detail does not automatically mean easy correspondence

---

## TMC-2

Terrain Mapping Camera-2

Chandrayaan-2

Panchromatic terrain imagery.

Approximate project scale:

~5 m/pixel.

SIFT considerations:

- broader terrain structure may dominate
- reference scale selection is important
- full-resolution NAC may contain too much irrelevant fine detail
- a coarser NAC pyramid level may produce more physically meaningful
  feature correspondence

---

## IIRS

Imaging Infrared Spectrometer

Chandrayaan-2

Hyperspectral / imaging infrared.

Approximate project scale:

~80 m/pixel.

Approximate spectral coverage:

~0.8–5.0 µm.

Approximate bands:

roughly ~250–256 depending on product/documentation.

SIFT considerations:

The raw hyperspectral cube should NOT be passed directly into ordinary
2D SIFT as though it were a grayscale image.

Conceptual path:

IIRS cube
→ documented 2D registration representation
→ scale preparation
→ SIFT baseline experiment

Potential representations may include:

- selected band
- PCA component
- composite
- structural representation

Do not declare one representation best without benchmark evidence.

---

## LRO NAC

LROC Narrow Angle Camera.

Fine/high-resolution lunar reference.

Current ChandraMap planning often treats NAC imagery as approximately:

~0.5–2 m/pixel

depending on actual product/acquisition geometry.

SIFT considerations:

- may provide dense fine-detail features
- reference may need downsampling for TMC-2/IIRS
- actual GSD must be used

---

## LRO WAC

LROC Wide Angle Camera.

Broad/global lunar reference.

Product/mode dependent.

SIFT may potentially be used for coarse structural experiments where
the product supports useful common terrain structure.

Do NOT invent one universal WAC GSD.

============================================================
REQUIRED DOCUMENT STRUCTURE
============================================================

Create a polished Markdown document broadly following the structure
below.

You may improve headings/grouping where useful, but do not omit the
major concepts.

# SIFT Baseline

Start with a concise introduction explaining:

- what SIFT is
- why it is important to ChandraMap
- why it serves as the classical baseline
- why it is not the whole registration system

Include a principle such as:

> SIFT proposes local evidence; geometry and independent evaluation
> decide whether that evidence supports a valid lunar registration.

---

## 1. Why ChandraMap Uses SIFT

Explain:

- reproducible baseline
- established computer-vision method
- useful for controlled comparisons
- easier to diagnose than an opaque end-to-end system
- allows later learned/multimodal approaches to prove actual
  improvement on the same pairs

---

## 2. What SIFT Does

Explain high-level stages:

1. Search for stable local extrema across image scale
2. Localize keypoints
3. Assign characteristic orientation
4. Build local descriptors

Keep this understandable.

Do not copy long text from published papers.

---

## 3. What SIFT Does Not Do

Create a clear section.

SIFT alone does NOT:

- know lunar coordinates
- retrieve the whole Moon
- prove descriptor matches are correct
- run RANSAC automatically as part of the algorithm concept
- choose final transform
- guarantee illumination robustness
- create ground truth
- compute independent registration accuracy
- make IIRS into a valid 2D image
- solve arbitrary physical resolution differences

---

# SIFT FEATURE DETECTION

## 4. Scale-Space Concept

Explain conceptually:

SIFT searches for distinctive local structure across several
algorithmic image scales.

Purpose:

detect features that remain identifiable over moderate image-scale
changes.

Avoid an overly mathematical textbook derivation.

---

## 5. Gaussian Scale Space

Explain at a conceptual level:

The image is represented at progressively smoothed scales.

This allows feature detection at different local sizes.

Do not provide unnecessary implementation constants.

---

## 6. Difference-of-Gaussians Concept

Explain at a useful high level:

SIFT uses differences between neighboring Gaussian-smoothed
representations to identify candidate scale-space extrema.

Do not reproduce copyrighted text from SIFT papers.

---

## 7. Candidate Extrema

Explain:

Local extrema in scale space become candidate keypoints.

They are then refined/rejected according to stability criteria.

Do not invent the project's exact thresholds.

---

## 8. Keypoint Localization

Explain:

Unstable or poorly localized extrema may be removed.

This improves keypoint quality.

Do not hard-code contrast thresholds or edge thresholds.

---

## 9. Edge-Like Responses

Explain that some extrema along strong edges may be poorly localized in
one direction.

SIFT attempts to reject unsuitable edge-like detections.

Relate this to lunar imagery:

- crater rims
- shadow boundaries
- mask edges

may produce many strong responses.

---

## 10. Orientation Assignment

Explain:

SIFT assigns orientation based on local gradient structure.

Purpose:

make descriptors more robust to image rotation.

But:

orientation normalization does not make SIFT invariant to arbitrary
shadow geometry.

---

# SIFT DESCRIPTORS

## 11. Descriptor Concept

Explain:

A SIFT descriptor summarizes local gradient structure around a
keypoint.

Its purpose is to allow comparison of potentially corresponding local
regions.

---

## 12. Descriptor Dimensionality

If mentioning the standard SIFT descriptor dimensionality:

verify it from authoritative SIFT/OpenCV documentation before stating
it.

Do not invent or misstate dimensions.

If uncertain:

describe it simply as a fixed-length local descriptor vector.

---

## 13. Gradient-Based Representation

Explain why gradients can provide robustness to some intensity
differences.

But also explain:

different lunar shadows alter gradients.

Therefore SIFT is not automatically Sun-angle invariant.

---

## 14. Descriptor Normalization

Explain at a high level that descriptor normalization helps reduce
sensitivity to some local intensity changes.

Do not claim this eliminates radiometric/sun-angle problems.

---

# DESCRIPTOR MATCHING

## 15. Matching Descriptors

Conceptual process:

source descriptors

- reference descriptors
  → nearest descriptor candidates
  → filtering
  → candidate correspondences

  ***

## 16. Distance Metric

Explain that SIFT descriptors are commonly compared using an
appropriate vector-distance metric.

Do not mandate implementation-specific details unless established by
the repository.

---

## 17. Nearest-Neighbor Matching

Explain:

For each source descriptor:

find one or more similar descriptors in the reference.

This produces candidates.

It does not prove physical correspondence.

---

## 18. Ratio Filtering

Explain the concept:

compare the best descriptor match against the next-best alternative.

If the best match is not sufficiently distinctive:

reject it.

IMPORTANT:

Do NOT hard-code a universal ratio such as a particular numeric value
unless the repository explicitly configures and benchmarks it.

State:

ratio threshold is configuration/benchmark dependent.

---

## 19. Why Ratio Filtering Helps

Explain:

Repeated lunar textures may give several similar descriptor candidates.

Ratio filtering can remove some ambiguous descriptors.

But it is not perfect.

---

## 20. Mutual / Cross-Check Filtering

Explain conceptually:

source → reference

and:

reference → source

can be checked for mutual consistency.

This can reduce ambiguous matches.

Do not claim cross-check is mandatory in every experiment.

---

## 21. Ratio Test vs Cross-Check

Create a comparison table.

Possible columns:

| Filter | Idea | Benefit | Limitation |
| ------ | ---- | ------- | ---------- |

Explain that they may be:

- alternatives
- combined experimentally
- benchmarked separately

depending on pipeline design.

---

## 22. Matcher Filtering Is Not Geometry

Make this explicit.

Descriptor filtering uses:

feature similarity.

RANSAC uses:

geometric consistency.

These solve different problems.

---

# CANDIDATE MATCHES

## 23. Correct Terminology

After descriptor matching/filtering:

call them:

candidate matches

NOT:

- ground truth
- verified correspondences
- guaranteed matches
- final matches

---

## 24. Candidate Match Record

Conceptually may contain:

- source keypoint index
- reference keypoint index
- source coordinates
- reference coordinates
- descriptor distance
- filtering status

Do not prescribe an exact schema unless repository contracts define it.

---

# GEOMETRIC VERIFICATION

## 25. Why RANSAC Follows SIFT

Even good descriptors can produce false correspondences due to:

- repeated craters
- similar ridges
- shadow patterns
- low texture
- large scale differences
- modality differences

Therefore candidate matches need geometric verification.

---

## 26. RANSAC Concept

Explain conceptually:

candidate matches
→ repeatedly test geometric models
→ identify consistent matches
→ reject inconsistent matches
→ return initial model + inlier set

Keep mathematical details limited.

---

## 27. Verified Inliers

After RANSAC:

inliers are matches that are consistent with the estimated model under
the selected verification conditions.

Do NOT say:

"RANSAC proves these matches are ground truth."

---

## 28. RANSAC Threshold

Explain:

geometric tolerance should be:

- configuration-driven
- expressed in documented units
- benchmarked
- interpreted relative to source/reference scale

Do not invent one universal pixel threshold.

---

## 29. RANSAC Failure

Possible reasons:

- too few candidate matches
- too many outliers
- wrong reference region
- wrong scale
- wrong geometric model
- repeated terrain
- severe illumination change
- modality mismatch

Record failures.

---

# TRANSFORMATION MODELS

## 30. Affine Model

Explain conceptual capabilities:

- translation
- rotation
- scale
- shear

May be useful for locally approximated lunar geometry.

Do not claim it always fits.

---

## 31. Homography

Explain:

A homography models a planar projective relationship.

It can be useful locally.

But:

the Moon is not a flat poster.

Relief, projection differences, viewing geometry, or wide fields may
produce spatially varying residuals.

---

## 32. Choosing Affine vs Homography

Selection should depend on:

- benchmark scope
- pair geometry
- overlap
- projection
- residual behavior

Do not automatically choose the more flexible model.

---

## 33. Transform Direction

Always document:

source → reference

or:

reference → source.

Do not save an ambiguous matrix.

---

# RESIDUAL ANALYSIS

## 34. Residual Definition

Define:

difference between an observed matched point and the location predicted
by the fitted transform.

---

## 35. Residual Vectors

Inspect both:

- magnitude
- direction
- spatial pattern

This may reveal:

- wrong model
- relief effects
- scale mismatch
- projection mismatch
- false matches

---

## 36. Clustered Inliers

Explain:

Many inliers concentrated in one crater do not necessarily provide
strong image-wide registration.

Spatial coverage matters.

---

# SUB-PIXEL REFINEMENT

## 37. Correct SIFT-to-Refinement Order

Make this explicit:

SIFT keypoints/descriptors
→ descriptor matching
→ candidate filtering
→ candidate matches
→ RANSAC
→ verified inliers
→ sub-pixel refinement
→ refit final transform
→ independent evaluation

---

## 38. Why Not Refine Every Candidate?

Refining an incorrect candidate gives:

a more precise wrong point.

Therefore:

verification first.

refinement second.

---

## 39. SIFT Keypoint Precision vs Final Registration Precision

Explain:

SIFT keypoint localization and any later sub-pixel refinement are not
the same concept.

Do not claim:

SIFT automatically guarantees final sub-pixel registration.

---

# SCALE HANDLING

## 40. SIFT Scale-Space vs Physical GSD

Make this one of the most important sections.

SIFT's internal scale-space provides some robustness to local image
scale.

But it does NOT mean ChandraMap can ignore:

- OHRC GSD
- TMC-2 GSD
- IIRS GSD
- NAC effective GSD
- WAC effective scale

---

## 41. Why SIFT Alone Does Not Solve Huge GSD Gaps

Example conceptually:

IIRS
vs
full-resolution NAC

may differ by enormous physical information content.

SIFT cannot match features that simply do not exist in the coarse
source.

---

## 42. Reference Pyramid Before SIFT

Conceptual path:

source
→ determine source GSD

reference
→ choose physically comparable pyramid level

then:

SIFT
→ descriptor matching
→ geometry

---

## 43. OHRC + SIFT Scale Path

Depending on actual metadata:

OHRC and NAC may sometimes be relatively close in GSD.

Use actual product GSD before selecting a reference level.

---

## 44. TMC-2 + SIFT Scale Path

Likely conceptual route:

TMC-2
→ coarser NAC pyramid level
→ SIFT baseline

rather than:

TMC-2
→ full-resolution NAC by default.

---

## 45. IIRS + SIFT Scale Path

Conceptual route:

IIRS cube
→ derived 2D representation
→ strongly coarsened reference scale
→ SIFT experiment

Do not expect fine NAC keypoints to exist in IIRS.

---

# ILLUMINATION HANDLING

## 46. SIFT and Brightness Differences

SIFT descriptors may tolerate some intensity changes because they rely
on local gradient structure and normalization.

But do not overstate this robustness.

---

## 47. SIFT and Sun-Angle Differences

Changing Sun angle changes:

- gradient directions
- crater-rim brightness
- shadow boundaries
- visible terrain

Therefore SIFT detections and descriptors may change.

---

## 48. Shadow Boundary Features

SIFT may detect strong keypoints near:

- shadow edges
- crater boundaries

But a shadow boundary can move under different illumination.

Such features may create incorrect descriptor matches.

---

## 49. Illumination Preprocessing

Potential preprocessing experiments may include:

- minimal intensity normalization
- local contrast normalization
- gradients
- structural representations

But do not call them guaranteed SIFT improvements.

Benchmark on the same pairs.

---

# SENSOR-SPECIFIC SIFT BEHAVIOR

## 50. OHRC + SIFT

Potential strengths:

- fine local terrain structures
- potentially many keypoints

Potential difficulties:

- high-resolution repetitive terrain
- moving shadows
- large images
- geometry/projection differences

---

## 51. TMC-2 + SIFT

Potential strengths:

- larger-scale crater/ridge structure

Potential difficulties:

- fewer fine keypoints
- mismatch with fine NAC texture
- scale differences

Reference pyramid selection is especially important.

---

## 52. IIRS + SIFT

Potentially challenging because:

- IIRS is hyperspectral
- representation must first become 2D
- modality difference remains
- spatial scale is coarse
- local gradient structure may differ greatly from NAC

SIFT should be presented primarily as:

a baseline experiment,

not a guaranteed strong solution for IIRS.

---

## 53. NAC + SIFT

NAC may generate many fine features.

But:

reference feature density should not be confused with source-visible
information.

Choose scale appropriately.

---

## 54. WAC + SIFT

Potentially useful for:

- coarse structural matching
- selected retrieval/localization experiments

Product-dependent.

Do not claim universal suitability.

---

# LOW-FEATURE TERRAIN

## 55. Low-Feature Cases

Some lunar regions may provide:

- few distinctive gradients
- smooth terrain
- limited SIFT keypoints

This is a legitimate benchmark failure condition.

Do not generate artificial success claims.

---

## 56. Handling Too Few Keypoints

Possible behavior:

- report keypoint shortage
- try configured preprocessing
- try adjacent scale level
- use alternative matcher in later versions

Do not silently lower every quality criterion until success appears.

---

# REPETITIVE TERRAIN

## 57. Repeated Crater Patterns

The lunar surface may contain:

many locally similar crater structures.

This can create descriptor ambiguity.

---

## 58. Why Descriptor Similarity Is Not Enough

Two similar-looking craters may exist in completely different
locations.

Therefore:

geometry

- spatial distribution
- reference context

are required.

---

## 59. Spatial Coverage

Verified inliers should ideally be distributed across the overlap.

A large cluster in one repetitive region can produce unstable global
registration.

---

# MASKS

## 60. SIFT Masks

If the implementation supports masks:

they may prevent keypoint detection in:

- nodata
- invalid projected regions
- unusable borders
- optionally excluded shadowed areas

Do not claim a masking interface is implemented unless confirmed.

---

## 61. Mask Edges

Mask boundaries can themselves create strong visual edges.

Ensure invalid boundaries do not become artificial SIFT features.

---

# KEYPOINT DENSITY

## 62. Keypoint Count

Keypoint count is a useful diagnostic.

But:

more keypoints
≠
better registration.

---

## 63. Keypoint Spatial Distribution

Track whether detections cover:

- entire overlap
- only crater rims
- only bright areas
- only one region

Distribution matters.

---

## 64. Keypoint Count Across Scales

Reference levels may produce different feature density.

This can help diagnose:

- over-detailed reference
- over-smoothed reference
- scale incompatibility

---

# SIFT CONFIGURATION

## 65. Configuration Principles

Potential SIFT-related parameters may include:

- feature count limits
- scale-space settings
- contrast threshold
- edge threshold
- descriptor-matching strategy
- ratio threshold
- cross-check behavior

IMPORTANT:

Do NOT invent ChandraMap default values.

Document configuration categories conceptually.

---

## 66. Defaults

If actual repository defaults exist:

document them accurately.

If they do not:

do not invent them.

Use language such as:

"configured by the experiment/baseline configuration."

---

## 67. Parameter Sensitivity

Explain:

SIFT parameters can affect:

- number of keypoints
- feature stability
- runtime
- candidate matches

Therefore parameter tuning should use:

validation/benchmark methodology,

not test-set overfitting.

---

# MATCHING CONFIGURATION

## 68. Matcher Choice

Descriptor matching may conceptually use:

- nearest-neighbor search
- brute-force comparison
- indexed approximate search where appropriate

Do not prescribe one unless architecture defines it.

---

## 69. Ratio Threshold

Must be:

- explicit
- versioned/configured
- benchmarked

Do not silently change it between methods.

---

## 70. Cross-Check Configuration

If used:

record whether cross-check is:

- enabled
- disabled
- part of an ablation

---

# SIFT PIPELINE

## 71. Canonical Baseline Pipeline

Document conceptually:

Prepared source
→ source SIFT keypoints + descriptors

Prepared reference at appropriate scale
→ reference SIFT keypoints + descriptors

Then:

descriptor matching
→ candidate filtering
→ candidate matches
→ RANSAC
→ verified inliers
→ initial transform
→ sub-pixel refinement
→ refit final transform
→ independent evaluation

This should be one of the central diagrams/workflows.

---

# MERMAID DIAGRAM

## 72. Main SIFT Baseline Diagram

Include a GitHub-compatible Mermaid diagram conceptually like:

Prepared Source
|
v
SIFT
|
Keypoints + Descriptors
|
| Prepared Reference
| |
| v
| SIFT
| |
| Keypoints + Descriptors
| |
+----------------+------------------+
|
v
Descriptor Matching
|
v
Candidate Filtering
|
v
Candidate Matches
|
v
RANSAC
|
v
Verified Inliers
|
v
Initial Transform
|
v
Sub-Pixel Refinement
|
v
Refit Final Transform
|
v
Independent Check-Point
Evaluation
|
v
Metrics + Diagnostics

Use valid GitHub Mermaid syntax.

---

## 73. Scale-Aware SIFT Diagram

Optionally include a second diagram conceptually like:

Source GSD
|
v
Reference Pyramid
|
Select Comparable Level
|
+----------------+
| |
Source SIFT Reference SIFT
| |
+-------+--------+
|
Candidate Matches
|
RANSAC

Explain:

SIFT's own scale-space does not replace this physical-scale step.

---

# SIFT OUTPUT CONTRACT

## 74. Feature Output

Conceptually:

- keypoint coordinates
- keypoint scale
- keypoint orientation
- descriptor vector
- asset ID
- preprocessing version

Do not define an exact code class.

---

## 75. Candidate Match Output

Conceptually:

- source feature ID/index
- reference feature ID/index
- source coordinates
- reference coordinates
- descriptor distance
- filtering result

---

## 76. Verified Match Output

After geometric verification:

- candidate match
- inlier/outlier status
- geometric residual

Do not make SIFT itself responsible for assigning final geometric
truth.

---

# EVALUATION

## 77. SIFT Benchmark Metrics

Use metrics such as:

- source keypoint count
- reference keypoint count
- candidate match count
- verified inlier count
- inlier ratio
- independent check-point RMSE
- source-image pixel error
- spatial coverage
- runtime
- failure status

---

## 78. Keypoint Count Is Diagnostic

Do not optimize purely for maximum keypoints.

Excessive unstable features may hurt matching.

---

## 79. Candidate Match Count Is Diagnostic

More descriptor matches do not necessarily mean better registration.

---

## 80. Inlier Count

Useful only when interpreted with:

- spatial distribution
- independent accuracy
- source/reference scale

---

## 81. Inlier Ratio

Useful diagnostic.

Do not report it as generic "accuracy."

---

## 82. Spatial Coverage

Possible measures:

- grid occupancy
- convex-hull coverage

Explain why lunar registration needs well-distributed inliers.

---

## 83. Check-Point RMSE

Preferred when independent truth exists.

Report:

- coordinate system
- source sensor
- units
- truth version

---

## 84. Source-Pixel Error First

Examples:

OHRC source
→ error in OHRC pixels

TMC-2 source
→ error in TMC-2 pixels

IIRS source representation
→ error in IIRS-source pixel coordinates

Convert to metres only when geospatial/GSD context justifies it.

---

## 85. Runtime

Possible timing breakdown:

- feature detection/description
- descriptor matching
- RANSAC
- refinement
- total

Hardware/environment may matter for comparisons.

---

# BASELINE BENCHMARKING

## 86. Why Baseline Results Matter

SIFT should provide the reference point against which future methods
are compared.

Examples:

- SIFT vs ALIKED + LightGlue
- SIFT vs LoFTR
- SIFT vs RIFT
- SIFT vs CFOG-style method

Use the SAME pairs.

---

## 87. Same-Pair Rule

Never compare:

SIFT on a difficult IIRS pair

against:

LightGlue on an easy OHRC pair

and conclude one method is better.

---

## 88. Same-Preprocessing Rule

When comparing matchers:

keep preprocessing as consistent as technically valid.

If matcher-specific input conversion differs:

document it.

---

## 89. Same-Evaluation Rule

Use:

- same pair
- same ground truth
- same check points
- same metric definitions

---

# SIFT ABLATIONS

## 90. Ratio-Filter Ablation

Conceptually compare:

- ratio filtering
- cross-check
- ratio + cross-check
- minimal filtering

Do not fill in fake results.

---

## 91. Scale-Pyramid Ablation

Same SIFT configuration.

Compare:

- full-resolution reference
- GSD-aware level
- neighboring-level search
- coarse-to-fine

---

## 92. Illumination-Preprocessing Ablation

Same SIFT matcher.

Compare:

- raw prepared intensity
- normalized intensity
- local contrast
- gradient/structural representation

---

## 93. Sensor Representation Ablation

Especially for IIRS:

same SIFT baseline

compare:

- selected band
- PCA
- composite
- structural representation

---

# SIFT BENCHMARK TABLE TEMPLATE

## 94. Results Table

Include a conceptual template such as:

| Pair | Source Sensor | Reference | Preprocessing | Pyramid Level | Keypoints | Candidates | Inliers | Inlier Ratio | Check RMSE | Coverage | Runtime | Status |
| ---- | ------------- | --------- | ------------- | ------------- | --------: | ---------: | ------: | -----------: | ---------: | -------: | ------: | ------ |

Do NOT populate with fabricated measurements.

---

# FAILURE MODES

## 95. SIFT Failure Modes

Create a practical table.

Examples:

| Failure                         | Possible Cause                            | Diagnostic / Response        |
| ------------------------------- | ----------------------------------------- | ---------------------------- |
| Few keypoints                   | Low-feature terrain                       | Record keypoint count        |
| Many candidates, few inliers    | Repetitive terrain                        | Inspect geometry             |
| No stable RANSAC model          | Wrong region / too many outliers          | Check pair/retrieval         |
| TMC-2 vs NAC fails              | Reference too fine                        | Try GSD-aware level          |
| IIRS vs NAC fails               | Scale + modality mismatch                 | Revisit representation/scale |
| Matches only in one crater      | Poor spatial coverage                     | Inspect coverage             |
| Fine shadow edges dominate      | Sun-angle difference                      | Illumination ablation        |
| Mask boundary produces features | Invalid preprocessing                     | Fix mask                     |
| Good inlier ratio but poor RMSE | Wrong geometry / clustered points         | Inspect residuals            |
| Runtime too high                | Excessive reference features / image size | Profile stage                |

---

## 96. SIFT Failure Is a Benchmark Result

Do not hide:

- zero-match cases
- insufficient keypoints
- RANSAC failure
- unstable transform

Record failure status.

---

# QUALITY CONTROL

## 97. SIFT QC Checklist

Create a practical checklist including:

- source asset valid
- reference asset valid
- preprocessing version known
- GSD/reference level known
- valid masks applied where required
- source keypoints present
- reference keypoints present
- descriptors finite/valid
- candidate matches present
- filtering config recorded
- RANSAC config recorded
- inlier distribution checked
- transform direction explicit
- independent evaluation available where possible

---

## 98. Visual QC

Useful visualizations may include:

- source keypoints
- reference keypoints
- candidate match lines
- inlier-only matches
- residual vectors
- registered overlay

But:

visualization is diagnostic.

It is not the final scientific metric.

---

# REPRODUCIBILITY

## 99. Reproducible SIFT Run

A benchmark run should be able to identify:

- source asset
- reference asset
- source/reference representations
- selected pyramid level
- preprocessing version
- SIFT configuration
- matching configuration
- candidate-filter configuration
- RANSAC configuration
- transform model
- refinement configuration
- benchmark version
- truth version

---

## 100. Configuration Versioning

Do not silently change:

- SIFT parameters
- matching filters
- RANSAC threshold
- reference scale

between benchmark runs.

---

## 101. Determinism

Where relevant, record random seeds for downstream stochastic
procedures such as RANSAC if the implementation supports/control
requires them.

Do not promise perfect bit-for-bit determinism across every platform
unless established.

---

# V1 / V2 / V3 / V4

## 102. V1 SIFT Role

SIFT should be central to V1.

Conceptual V1:

known-overlap source/reference pair
→ sensor-aware preparation
→ physical reference scale
→ SIFT
→ descriptor matching
→ candidate filtering
→ RANSAC
→ affine/homography where appropriate
→ independent check-point evaluation

Purpose:

establish a measurable classical baseline.

---

## 103. V2 SIFT Role

SIFT remains a baseline while evaluating:

- stronger scale handling
- illumination preprocessing
- structural representations
- more difficult pairs
- IIRS initial experiments

This lets preprocessing improvements be measured independently of new
matchers.

---

## 104. V3 SIFT Role

SIFT remains valuable as a local baseline even if ChandraMap adds:

- global retrieval
- ALIKED + LightGlue
- LoFTR
- WAC/NAC retrieval hierarchy

Use SIFT on the same retrieved/local pairs for comparison where
meaningful.

---

## 105. V4 SIFT Role

SIFT may remain a historical/classical baseline when research expands
to:

- lunar-specific learned models
- multimodal remote-sensing matchers
- DEM-aware geometry
- advanced local warping
- larger benchmarks

Do not remove the baseline merely because stronger methods exist.

---

# COMPARISON WITH OTHER MATCHERS

## 106. SIFT vs ALIKED + LightGlue

Describe differences without ranking.

SIFT:

- classical
- sparse detector + descriptor

ALIKED:

- learned sparse detector/descriptor

LightGlue:

- learned matcher for compatible local features

Both still require geometric verification.

---

## 107. SIFT vs LoFTR

SIFT:

detect
→ describe
→ match.

LoFTR:

detector-free correspondence estimation.

Both produce correspondences requiring geometric verification.

---

## 108. SIFT vs RIFT / CFOG

SIFT is a classical general-purpose local-feature baseline.

RIFT/CFOG-style approaches are relevant research directions for
multimodal remote-sensing imagery.

Do not rank them before lunar benchmark results exist.

---

# CLAIMS TO AVOID

## 109. SIFT Claims ChandraMap Must Avoid

Do NOT claim:

- "SIFT is lunar invariant"
- "SIFT is Sun-angle invariant"
- "SIFT is illumination invariant"
- "SIFT automatically handles OHRC/TMC-2/IIRS"
- "SIFT solves hyperspectral matching"
- "SIFT scale-space removes the need for GSD handling"
- "SIFT matches are ground truth"
- "ratio-test matches are verified correspondences"
- "RANSAC makes every inlier correct"
- "more SIFT keypoints mean better registration"
- "more SIFT matches mean better registration"
- "SIFT guarantees homography estimation"
- "SIFT guarantees sub-pixel accuracy"
- "SIFT always works on crater terrain"
- "SIFT is always worse than learned methods"
- "SIFT is always better because it is classical"
- arbitrary percentages such as "90% SIFT accuracy"

============================================================
COMMON SIFT MISTAKES
============================================================

## 110. Mistakes to Avoid

Do NOT:

- call keypoints matches
- call descriptors keypoints
- call descriptor matches inliers before geometry
- call inliers ground truth
- ignore source/reference physical scale
- use full-resolution NAC blindly
- enlarge IIRS and call scale solved
- pass raw IIRS cube directly into ordinary SIFT
- ignore valid masks
- detect features in nodata borders
- use one fixed ratio threshold without documenting it
- tune thresholds on the test set
- skip RANSAC because descriptor distance is low
- refine all candidate matches before verification
- fit and evaluate the transform on only the same points
- judge quality only by match-count visualizations
- ignore match spatial distribution
- ignore residual patterns
- hide failed pairs
- compare SIFT and learned matchers on different datasets
- claim an overlay proves accuracy
- report pixel RMSE with no coordinate-system context

============================================================
LIMITATIONS
============================================================

## 111. SIFT Limitations in ChandraMap

Include an honest limitations section.

Cover:

- SIFT depends on distinctive local gradient structure
- low-feature lunar terrain may provide few keypoints
- repetitive craters can produce ambiguous descriptors
- large illumination differences alter gradients
- shadows can produce unstable local features
- extreme GSD differences may remove common local detail
- IIRS introduces strong modality mismatch
- projection/viewing geometry can exceed a simple global model
- descriptor filtering cannot replace geometric verification
- fine reference detail may be irrelevant to coarse sources
- parameter choices affect results
- SIFT is a baseline, not a guaranteed production winner

---

# AUTHORITATIVE / PRIMARY REFERENCES

## 112. SIFT References

Use authoritative primary references.

Include:

- David G. Lowe's original/standard SIFT publication(s), using correct
  bibliographic details if confidently known
- OpenCV SIFT documentation

IMPORTANT:

Do NOT fabricate:

- paper title
- venue
- year
- DOI
- URL

If exact bibliographic details cannot be confidently generated:

name the author/method and authoritative source category instead.

Do not reproduce long copyrighted passages from papers.

---

## 113. Supporting Computer-Vision References

Relevant categories:

- OpenCV feature matching documentation
- OpenCV geometric verification/homography documentation
- robust estimation / RANSAC references where appropriate

---

## 114. ChandraMap Sensor Context Sources

Relevant authoritative source categories include:

### Chandrayaan-2

- ISRO Chandrayaan-2 documentation
- ISRO / ISSDC / PRADAN
- official OHRC documentation
- official TMC-2 documentation
- official IIRS documentation

### LRO

- NASA Lunar Reconnaissance Orbiter documentation
- LROC / Arizona State University
- NASA Planetary Data System
- official NAC/WAC documentation

Do NOT fabricate URLs.

---

# RELATIONSHIP TO ALGORITHM OVERVIEW

## 115. `overview.md`

Reference:

`overview.md`

Explain:

The algorithm overview describes the complete ChandraMap algorithm
stack.

This file describes the classical SIFT branch in detail.

---

# RELATIONSHIP TO SENSOR ROUTING

## 116. `sensor-routing.md`

Reference:

`sensor-routing.md`

Explain:

Sensor routing determines:

- what sensor/representation arrived
- which scale/reference path applies
- whether SIFT is the selected baseline matcher path

SIFT itself should not identify sensor type.

---

# RELATIONSHIP TO PREPROCESSING

## 117. `preprocessing.md`

Reference:

`preprocessing.md`

Explain:

Preprocessing produces the matcher-ready 2D representations consumed by
SIFT.

Examples include:

- valid masking
- intensity conversion
- IIRS representation
- scale preparation

---

# RELATIONSHIP TO ILLUMINATION HANDLING

## 118. `illumination-handling.md`

Reference:

`illumination-handling.md`

Explain:

SIFT has
