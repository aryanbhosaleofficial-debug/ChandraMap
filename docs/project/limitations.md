# ChandraMap Project Limitations

This document defines the canonical **human-facing record of scientifically important limitations, interpretation boundaries, expected failure conditions, and unresolved research challenges in ChandraMap**.

ChandraMap is a research system for lunar image correspondence and registration. It operates on imagery that may differ substantially in:

- spatial resolution
- ground sampling distance
- illumination
- shadow geometry
- sensor modality
- viewing geometry
- terrain relief
- product processing
- geospatial representation

These differences create real scientific limits. Some arise from the physics of sensing and cannot be removed simply by writing better software.

The central interpretation principle is:

> **ChandraMap should prefer an explicit rejection over a confident-looking registration when the available observations do not contain enough evidence to support reliable correspondence.**

Professional research software does not need to eliminate every limitation.

It should instead:

- understand limitations
- expose them clearly
- detect them where possible
- benchmark them
- avoid overclaiming
- reject unsupported cases
- investigate improvements through controlled research

This document does not claim that every limitation described below has already been observed in the current implementation. Where current benchmark evidence is unavailable, limitations are described as **domain-derived, methodological, expected, or not yet characterized** rather than as measured project failures.

---

## 1. Purpose

This document answers questions such as:

- Under what conditions may lunar correspondence become unreliable?
- When does scale difference remove common observable structure?
- Why can illumination changes break otherwise strong image matching?
- Why can cross-sensor intensity comparison fail?
- When can affine or homography geometry become inadequate?
- Why can many RANSAC inliers still support a wrong registration?
- What limits registration accuracy when independent ground truth is weak?
- Why can pixel-space accuracy not always be converted directly into metres?
- What limitations are intentionally exposed by Benchmark V1?
- What additional limitations appear when learned matching or global retrieval is introduced?
- How should benchmark results be interpreted without overgeneralizing them?
- Which limitations motivate V2, V3, and V4?

The goal is not to make ChandraMap appear weaker.

The goal is to make its scientific claims **bounded, interpretable, and trustworthy**.

---

## 2. How to Interpret This Document

### Limitation vs Bug

A **bug** means the implementation behaves incorrectly relative to its intended design.

Example:

> Source and reference coordinates are accidentally reversed.

A **limitation** means the method behaves as designed but has a scientific or methodological boundary.

Example:

> A classical sparse-feature method may struggle when two observations have an extreme physical scale difference and share little common observable detail.

Bugs should be fixed.

Scientific limitations should be understood, detected where possible, benchmarked, and reduced through research.

Do not excuse implementation defects as scientific limitations.

---

### Limitation vs Non-Goal

A **non-goal** describes something ChandraMap intentionally does not treat as a primary objective.

Example:

> Complete rover navigation is not a ChandraMap goal.

A **limitation** describes a weakness or boundary in something ChandraMap actually attempts to do.

Example:

> Cross-sensor correspondence may fail when there is insufficient common spatial structure.

Do not classify every absent feature as a limitation.

See [`non-goals.md`](./non-goals.md) for project-scope exclusions.

---

### Limitation vs Assumption

An **assumption** is a condition accepted while applying or interpreting a method.

Example:

> A homography provides a useful local approximation for the selected overlap.

A **limitation** describes what happens when that assumption is weak, violated, or insufficient.

Example:

> Strong terrain relief may produce residual distortion that a single homography cannot model.

See [`assumptions.md`](./assumptions.md) for the assumptions register.

---

### Limitation vs Current Implementation Status

A methodological capability being absent from the current repository is not automatically a scientific limitation.

Distinguish:

#### Methodological Limitation

The method itself has a known or expected boundary.

#### Current Implementation Limitation

The current repository lacks or restricts a capability.

#### Research Gap

The method has not yet been sufficiently evaluated.

#### Planned Capability

A future capability is documented but not currently implemented.

This document does not infer current implementation limitations without repository evidence.

---

### Observed vs Expected Limitations

An **observed limitation** should be supported by actual evidence such as:

- benchmark results
- reproducible experiments
- validated failure cases
- issue history
- executed tests

An **expected or domain-derived limitation** follows from known sensor, geometric, algorithmic, or scientific behavior but may not yet have been measured in the current project.

Where evidence is absent, this document uses language such as:

- may
- can
- is expected to
- can become difficult
- should be evaluated

rather than:

- currently fails
- always fails
- is proven to fail

---

## 3. Limitation Summary

| Limitation                     | Primary Impact                              | Possible Response                                      |
| ------------------------------ | ------------------------------------------- | ------------------------------------------------------ |
| Extreme GSD difference         | Little common observable structure          | Scale-aware research, controlled resampling, rejection |
| Upsampling                     | Cannot recreate missing physical detail     | Treat as resampling only                               |
| Downsampling                   | Removes fine detail                         | Controlled scale tradeoff                              |
| Sun-angle difference           | Changed shadows and visible structure       | Structural representations, benchmark stress tests     |
| Cross-sensor modality          | Pixel intensities may be incomparable       | Sensor-aware representation research                   |
| IIRS reduction to 2D           | Spectral information may be discarded       | Evaluate multiple defined representations              |
| Viewing geometry               | Apparent local shape can change             | Stronger geometry where justified                      |
| Terrain relief                 | Planar models may leave local residuals     | Local/terrain-aware research                           |
| Repetitive crater terrain      | Plausible false correspondences             | Geometric verification and coverage checks             |
| Low-feature terrain            | Too few reliable correspondences            | Explicit rejection                                     |
| Partial overlap                | Reduced support and coverage                | Overlap-aware evaluation                               |
| Sparse feature methods         | May struggle under severe appearance change | Later matcher research                                 |
| RANSAC                         | Cannot guarantee physical truth             | Independent evaluation                                 |
| Spatial clustering             | Weak global geometric support               | Coverage diagnostics                                   |
| Coordinate convention mismatch | Incorrect transforms/metrics                | Explicit coordinate semantics                          |
| Lunar CRS differences          | Incorrect geolocation                       | Planetary-aware geospatial handling                    |
| Incomplete metadata            | Weak scale/location interpretation          | Preserve unknown state, validate metadata              |
| Imperfect reference data       | Reference is not error-free truth           | Preserve uncertainty/provenance                        |
| Limited check points           | Weak independent accuracy evidence          | Improve evaluation truth where possible                |
| Pixel-to-ground conversion     | Can be physically misleading                | Require valid spatial context                          |
| Global retrieval miss          | Correct region never reaches local matcher  | Improve retrieval or reject                            |
| Learned-model domain shift     | Lunar generalization may be weak            | Lunar-domain benchmarking                              |
| Benchmark representativeness   | Limited generalization                      | Expand controlled coverage                             |
| Runtime context                | Timing may not be comparable                | Record relevant scope/hardware                         |
| Nondeterminism                 | Run-to-run variation                        | Seed/control where practical                           |

The listed responses are possible mitigation directions.

They are not guarantees that the limitation is fully resolved.

---

# 4. Data and Sensor Limitations

## 4.1 Extreme GSD Differences

**Type:** Domain / Data

Different lunar instruments can observe the same terrain at dramatically different physical ground scales.

Approximate project context includes:

- OHRC: roughly `~0.25–0.32 m/px`
- TMC-2: roughly `~5 m/px`
- IIRS: roughly `~80 m/px`
- LRO/LROC products: product-dependent

Specific product metadata should take precedence over these broad instrument-level approximations.

### Why It Matters

At sufficiently different GSDs, terrain visible in the finer observation may not be physically resolved in the coarser observation.

For example, a feature may exist clearly in one image but have no separate observable representation in another.

This is not merely:

> one image has fewer pixels.

It is an **information-content difference**.

### Typical Consequence

Potential effects include:

- reduced descriptor similarity
- fewer common keypoints
- unstable scale estimation
- correspondence ambiguity
- rejection

### Possible Mitigation

Possible research directions include:

- physically meaningful scale handling
- downsampled reference representations
- reference pyramids
- scale-aware candidate search

None of these methods can recreate terrain detail that the coarser sensor never observed.

---

## 4.2 Upsampling Limitation

Upsampling can increase raster dimensions.

It cannot recreate missing physical information.

Conceptually:

```text
Coarse Observation
        ↓
Interpolation
        ↓
Larger Raster
```

does not mean:

```text
Additional Lunar Detail Was Observed
```

Upsampling can still be useful computationally, but it should not be described as physical resolution enhancement.

---

## 4.3 Downsampling Tradeoff

Downsampling a high-resolution reference may make its effective representation more comparable to a coarser source.

However, downsampling also removes:

- high-frequency texture
- small crater detail
- fine edges
- local structure

Therefore:

> more comparable scale does not automatically mean more useful correspondence.

The appropriate scale treatment remains an empirical research question.

---

## 4.4 Multi-Scale Search Limitation

Reference pyramids or other multi-scale strategies may improve robustness to scale mismatch.

They may also increase:

- computation
- storage
- configuration complexity
- candidate ambiguity
- duplicate candidate evaluation

Multi-scale search also cannot reconstruct details missing from the original coarse observation.

---

## 4.5 Product Metadata Quality

**Type:** Data / Geospatial

Scientific metadata may be:

- incomplete
- product-specific
- differently encoded
- unavailable in the representation currently being processed
- difficult to normalize across providers

If registration depends on incorrect scale, footprint, projection, or coordinate interpretation, downstream results may also become incorrect.

Missing metadata should remain unknown rather than being silently fabricated.

---

## 4.6 Nominal Instrument Specifications

Broad instrument values are useful for explanation and planning.

They are not substitutes for product-specific metadata.

Using a nominal sensor value as an exact per-product quantity can distort:

- scale calculations
- pixel-to-ground conversion
- benchmark interpretation

---

## 4.7 Product Processing Differences

Source and reference imagery may differ in processing state.

Potential examples include:

- calibrated products
- map-projected products
- orthorectified products
- derived products
- browse representations

Different processing states may introduce different geometric or radiometric behavior.

Two images from the same sensor should not automatically be assumed geometrically equivalent.

---

## 4.8 Reference Quality

The term **reference** means the observation or coordinate frame to which another image is being registered.

It does not mean:

> perfect error-free ground truth.

Reference imagery may itself contain:

- projection uncertainty
- varying GSD
- no-data
- illumination differences
- terrain effects
- processing artifacts

Final accuracy claims must account for the quality of the reference and evaluation truth where known.

---

# 5. Illumination Limitations

## 5.1 Sun-Angle Differences

**Type:** Domain

Different solar illumination can substantially change the appearance of the same lunar terrain.

Changes may include:

- shadow direction
- shadow length
- visible crater walls
- hidden crater walls
- ridge brightness
- edge contrast
- local texture visibility

The same physical feature may therefore look structurally different across acquisitions.

---

## 5.2 Shadow Geometry

Shadow change is not just an intensity change.

A region visible under one illumination condition may be:

- dark
- partially hidden
- structurally different

under another.

If corresponding terrain is not observable in both images, no matcher can recover a reliable visual correspondence from that absent information alone.

---

## 5.3 Photometric Normalization

Methods such as:

- histogram equalization
- contrast normalization
- brightness normalization
- CLAHE

may reduce some radiometric differences.

They do not reconstruct:

- terrain hidden by shadow
- changed shadow boundaries
- missing structural visibility

Therefore such processing should not be described as providing complete Sun-angle invariance.

---

## 5.4 Structural-Cue Stability

Edges, gradients, crater rims, and other structural representations may sometimes be more stable than raw intensity.

However, they are still affected by:

- severe illumination change
- coarse spatial resolution
- partial visibility
- sensor modality
- noise

Structural representations may reduce an appearance problem.

They do not guarantee correspondence.

---

# 6. Modality Limitations

## 6.1 Cross-Sensor Appearance

**Type:** Domain / Sensor

Different instruments may respond to different physical properties of the lunar surface.

Therefore:

```text
Same Lunar Location
≠
Same Raw Pixel Intensity
```

across all sensors.

A method relying strongly on identical local intensity structure may therefore struggle in cross-sensor registration.

---

## 6.2 Panchromatic vs Hyperspectral Data

Panchromatic imagery and hyperspectral/imaging-infrared observations are fundamentally different data representations.

Cross-modal correspondence may contain weaker direct appearance similarity even when both observations represent the same terrain.

---

## 6.3 IIRS Representation Limitation

IIRS is hyperspectral / imaging-infrared data.

A conventional 2D matcher requires a selected or derived two-dimensional representation.

Possible representations may include:

- selected spectral band
- PCA-derived component
- composite
- gradient representation
- edge/structural representation

The representation itself becomes part of the registration methodology.

No one representation should be assumed universally optimal without evidence.

---

## 6.4 Reducing Hyperspectral Data to 2D

Reducing a hyperspectral cube to one 2D representation simplifies local registration.

It may also discard:

- spectral discrimination
- wavelength-specific structure
- material information
- spatial cues visible only in some bands

This is a methodological tradeoff rather than a universally correct transformation.

---

# 7. Feature and Matching Limitations

## 7.1 Low-Feature Terrain

**Type:** Domain / Method

Some lunar regions may contain too little distinctive structure for reliable sparse correspondence.

Potential causes include:

- smooth terrain
- weak local contrast
- heavy shadow
- coarse sampling
- repetitive texture

Potential symptoms include:

- few detected keypoints
- few candidate matches
- unstable geometry
- rejection

Explicit rejection may be the scientifically correct result.

---

## 7.2 Repetitive Crater Terrain

The lunar surface contains many visually similar structures:

- circular craters
- crater rims
- overlapping impacts
- ridges
- rocks
- repeated textures

This creates a fundamental ambiguity:

```text
Looks Similar
≠
Same Physical Location
```

A matcher may propose plausible but physically incorrect crater-to-crater correspondences.

---

## 7.3 SIFT Limitations

SIFT is a useful classical baseline.

It should not be interpreted as perfectly invariant to arbitrary:

- GSD differences
- Sun-angle differences
- cross-modality differences
- terrain geometry
- viewpoint differences

Its purpose in V1 is to establish a credible classical reference method whose behavior can be measured.

---

## 7.4 RootSIFT Limitations

RootSIFT may alter descriptor matching behavior and may help in some conditions.

It does not by itself solve:

- missing common physical detail
- severe cross-modality differences
- wrong reference regions
- unsuitable geometry
- poor spatial coverage

---

## 7.5 RANSAC Limitations

RANSAC can reduce the effect of inconsistent candidate matches.

It is not a proof of physical correspondence.

RANSAC may fail or produce misleading consensus when:

- too few correct candidates exist
- outliers dominate
- false matches form a plausible pattern
- matches are spatially clustered
- the selected model is inappropriate
- the geometry is degenerate
- configuration is unsuitable

A RANSAC inlier means:

> model-consistent under the selected estimation process.

It does not mean:

> independently verified ground truth.

---

## 7.6 Matcher Confidence

Matcher scores are not automatically:

- calibrated probabilities
- registration confidence
- physical-correspondence certainty

A high matcher confidence can still correspond to a geometrically incorrect match.

Calibration requires independent evaluation.

---

## 7.7 Spatial Clustering

A large number of verified inliers concentrated in one small region may provide weak support for the rest of the overlap.

For example:

```text
Many Inliers
Around One Crater
```

can be less informative than:

```text
Fewer Inliers
Distributed Across the Overlap
```

This is why spatial coverage should complement match count where defined.

---

## 7.8 Coverage Metrics Are Simplifications

Metrics such as:

- grid coverage
- convex-hull coverage

summarize spatial support.

They do not completely describe:

- geometric conditioning
- terrain complexity
- local residual structure
- physical correctness

Coverage should therefore be interpreted with other evidence.

---

# 8. Geometry and Terrain Limitations

## 8.1 Viewing Geometry

Different spacecraft positions and viewing directions can change the apparent shape and relative placement of surface features.

A simple 2D model may not fully explain those differences.

---

## 8.2 Lunar Terrain Relief

**Type:** Domain / Geometry

The lunar surface is three-dimensional.

Features such as:

- crater walls
- slopes
- ridges
- mountains
- depressions

can introduce local geometric effects and parallax.

A single global planar transform cannot model arbitrary 3D terrain perfectly.

---

## 8.3 Affine Transform Limitations

An affine transform can model:

- translation
- rotation
- scale
- shear

but cannot represent every projective or terrain-induced deformation.

It is useful as a simple baseline model, not as a universal lunar geometry model.

---

## 8.4 Homography Limitations

A homography is more flexible than an affine transform for planar projective relationships.

However:

> a homography remains fundamentally a planar model.

It cannot completely model arbitrary:

- relief
- parallax
- non-planar local deformation
- large-area 3D geometry

---

## 8.5 Local Approximation

A transform may fit well around its supporting points while producing increasing error elsewhere.

This is especially relevant when:

- correspondences are clustered
- the overlap is large
- terrain relief varies spatially

Spatial coverage and independent evaluation therefore matter.

---

## 8.6 Geometric Degeneracy

Some correspondence layouts may not sufficiently constrain the selected transform.

Examples include:

- too few points
- severe clustering
- nearly collinear point distributions
- numerically unstable configurations

The existence of a returned matrix does not guarantee useful geometry.

---

## 8.7 Spatially Varying Distortion

One global transform assumes one relationship across the overlap.

Real imagery may contain spatially varying distortion caused by:

- terrain
- projection
- viewpoint
- sensor geometry

Local, piecewise, or terrain-aware methods may reduce this limitation in later research, but they also introduce additional complexity and assumptions.

---

# 9. Coordinate and Geospatial Limitations

## 9.1 `(x, y)` vs `(row, column)`

Image-processing libraries and numerical arrays may represent coordinates differently.

Confusing:

```text
(x, y)
```

with:

```text
(row, column)
```

can invalidate transforms and metrics.

This is an implementation risk rather than an unavoidable scientific limitation, but it is important enough to define explicitly because errors may remain visually plausible.

---

## 9.2 Source vs Reference Direction

A source-to-reference transform is not identical to its inverse.

Incorrect direction can invalidate:

- warping
- coordinate transfer
- metric calculation
- result interpretation

A wrong implementation is a bug, not a scientific limitation.

The broader limitation is that transform matrices are ambiguous without explicit coordinate semantics.

---

## 9.3 Lunar CRS Differences

Lunar products may use different:

- planetary coordinate reference systems
- projections
- body models
- longitude conventions

Incorrect assumptions can create false geolocation differences.

---

## 9.4 Earth CRS Defaults

Earth WGS84 / EPSG:4326 must not be silently applied to lunar spatial data.

A terrestrial coordinate default is not a valid substitute for appropriate lunar geospatial definitions.

---

## 9.5 Longitude Conventions

Products may differ in:

- positive-east vs positive-west longitude
- `0–360°`
- `-180–180°`

Coordinates may therefore refer to the same physical location while appearing numerically different until conventions are reconciled.

---

## 9.6 Projection Differences

Two products may represent the same terrain under different projections or geometric corrections.

An apparent image-registration difficulty may therefore partly reflect product geometry rather than image appearance alone.

---

# 10. Registration Limitations

## 10.1 Partial Overlap

Source and reference imagery may share only part of their total coverage.

Partial overlap reduces:

- usable correspondence count
- spatial coverage
- transform support

Non-overlapping regions should not be forced into correspondence.

---

## 10.2 Missing Visibility

A feature visible in one observation may have no usable counterpart in another because of:

- shadow
- coarse resolution
- footprint difference
- modality
- viewpoint

Not every detected feature should be expected to match.

---

## 10.3 Warp vs Accuracy

A raster can often be warped once a mathematically valid transform exists.

That proves only that the transformation can be applied numerically.

It does not prove:

- physical correctness
- independent accuracy
- sufficient spatial support

Warping and validation are separate stages.

---

## 10.4 Spatial Support

Registration may be locally strong but globally weak when correspondences do not cover enough of the overlap.

A model fitted to one small cluster may extrapolate poorly.

---

## 10.5 Detectable vs Hard-to-Detect Failure

Some limitations produce obvious evidence:

- zero features
- too few matches
- invalid transform
- RANSAC failure

Other failures may be harder to detect:

- plausible but wrong crater correspondence
- biased global transform
- poor extrapolation outside matched regions
- weakly calibrated confidence

Independent evaluation remains important because not every incorrect registration fails loudly.

---

# 11. Evaluation Limitations

## 11.1 Limited Ground-Truth Availability

**Type:** Evaluation

Reliable independent registration truth may be difficult to obtain.

Some evaluation cases may lack:

- dense trusted correspondences
- independent check points
- highly accurate external coordinates

When independent truth is limited, final claims must be correspondingly limited.

---

## 11.2 Fit-Point Evaluation

A model evaluated on the same points used to fit it may appear more accurate than it is on independent locations.

Fit-point residuals are useful for:

- model diagnostics
- outlier analysis
- consistency inspection

They are not automatically independent registration accuracy.

---

## 11.3 Check-Point Quality

Even independent check points may contain:

- annotation uncertainty
- coordinate uncertainty
- limited spatial distribution

The precision of evaluation claims cannot reasonably exceed the quality of the evaluation truth without additional justification.

---

## 11.4 Pixel Error

A statement such as:

```text
RMSE = 0.8 px
```

is incomplete unless the coordinate space is identified.

For example:

```text
0.8 source-image px
```

may have a very different physical interpretation from:

```text
0.8 reference-image px
```

when the images have different GSD.

---

## 11.5 Ground-Distance Conversion

Converting pixel error into metres may require valid:

- GSD
- projection
- image coordinate interpretation
- geometric context

A simple multiplication using a broad nominal sensor value may not produce a scientifically defensible ground error.

---

## 11.6 Sub-Pixel Interpretation

`Sub-pixel` means:

> less than one pixel in a specified image coordinate system.

It does not automatically mean:

> less than one metre on the lunar surface.

Physical accuracy depends on the product and geometry.

---

## 11.7 Visual Validation

Registered overlays are useful for:

- debugging
- qualitative assessment
- communication

but can appear convincing while:

- residual error remains
- one region is biased
- only a small area aligns well
- global coverage is weak

Visual agreement should support quantitative evidence rather than replace it.

---

## 11.8 Confidence / Quality Scores

A heuristic combined quality score may be useful operationally.

However, without calibration it should not be interpreted as:

> probability that the registration is correct.

Calibration requires evaluation against independent outcomes.

---

## 11.9 Conditional Accuracy

Accuracy calculated only among successful or accepted registrations can hide a high failure/rejection rate.

For example:

```text
Low RMSE on Accepted Cases
```

does not answer:

```text
How Often Did the Method Produce an Accepted Case?
```

Both aspects may be needed for interpretation.

---

## 11.10 Aggregate Metrics

A single average can hide:

- catastrophic failures
- sensor-specific differences
- stress-category differences
- skewed error distributions

Pair-level or category-level analysis may be required for meaningful scientific interpretation.

---

## 11.11 Combined Overall Scores

Collapsing:

- RMSE
- coverage
- success rate
- runtime
- retrieval performance

into one number may hide important tradeoffs.

ChandraMap should not invent an overall score without an explicit, justified methodology.

---

# 12. Retrieval Limitations

Global retrieval is a different problem from local registration and introduces additional failure modes.

## 12.1 Retrieval Miss

If the correct region is not retrieved, a local matcher may never receive the correct reference candidate.

Therefore:

```text
Retrieval Failure
```

and:

```text
Local Registration Failure
```

must remain distinguishable.

---

## 12.2 Global Descriptor Ambiguity

Global descriptors compress an image or reference tile into a vector.

This representation may lose detailed spatial information.

Similar lunar regions may therefore appear close in descriptor space even when they represent different physical locations.

Repetitive crater terrain can make this particularly challenging.

---

## 12.3 FAISS Limitation

FAISS can efficiently index and search vectors.

It cannot repair:

- weak descriptors
- incorrect reference tiling
- bad metadata-to-tile mapping
- missing reference coverage
- poor local correspondence
- invalid geometry

FAISS is a retrieval infrastructure component.

It is not a complete lunar-registration solution.

---

## 12.4 Top-K Limitation

A larger `K` may increase the chance that the correct reference appears among retrieved candidates.

It also increases:

- local matching work
- geometric verification work
- false candidate evaluation

There is no universal best `K` defined by this document.

---

## 12.5 Reference-Tiling Tradeoffs

Reference tile design may influence retrieval quality.

Potential tradeoffs include:

- small tiles may lack context
- large tiles may dilute local structure
- features may cross tile boundaries
- overlap can increase index size
- additional pyramid levels can increase storage and compute

Exact tiling methodology belongs in dedicated retrieval documentation when defined.

---

## 12.6 Offline vs Online Cost

Retrieval systems may move substantial computation into offline work such as:

- reference tiling
- descriptor generation
- vector-index construction

Reporting online query latency alone may therefore provide an incomplete view of total system cost.

---

# 13. Learned-Model Limitations

## 13.1 Domain Shift

**Type:** Open Research

Learned models developed primarily on terrestrial imagery may not generalize perfectly to:

- lunar textures
- crater geometry
- severe shadows
- unusual GSD relationships
- lunar sensor modalities

Their value must therefore be measured on lunar data rather than assumed.

---

## 13.2 ALIKED / LightGlue

If evaluated in later benchmark configurations, possible limitations may include:

- model/checkpoint dependence
- feature-extractor dependence
- preprocessing sensitivity
- compute requirements
- domain shift

These are expected considerations.

This document does not claim that they have been measured as current ChandraMap failures.

---

## 13.3 LoFTR

LoFTR may provide strong detector-free correspondence in some domains.

Potential concerns include:

- computational cost
- memory use
- model-domain mismatch
- correspondence ambiguity
- very large scale or modality differences

Again, these are possible methodological considerations rather than claimed measured project results.

---

## 13.4 Remote-Sensing Matchers

Remote-sensing-oriented methods such as RIFT or CFOG may be relevant to multi-modal lunar research.

Potential challenges may include:

- implementation complexity
- parameter sensitivity
- runtime
- uncertain transfer to particular lunar products

Their mention does not imply current support or measured superiority.

---

## 13.5 Checkpoint Dependence

Learned behavior can depend on:

- model checkpoint
- training data
- feature extractor
- preprocessing
- model revision

Naming only the architecture may be insufficient for reproducibility.

---

## 13.6 Compute Requirements

Advanced learned methods may require more:

- memory
- computation
- accelerator support

than the classical baseline.

No particular hardware requirement is asserted here.

---

# 14. Benchmark Limitations

## 14.1 Representativeness

Every benchmark samples only part of the possible lunar-registration domain.

If an evaluation population has limited:

- geography
- terrain types
- illumination conditions
- sensor combinations
- stress conditions

then its conclusions are limited accordingly.

This document does not claim that the current ChandraMap benchmark has any particular size or coverage.

---

## 14.2 Sensor-Pair Generalization

Performance on one sensor pair should not automatically be generalized to another.

For example, evidence on one:

```text
Sensor A ↔ Reference
```

combination does not establish equivalent performance on:

```text
Sensor B ↔ Reference
```

where scale, modality, or noise characteristics differ.

---

## 14.3 Stress-Category Coverage

If a benchmark does not contain a meaningful set of:

- scale-stress cases
- illumination-stress cases
- modality-stress cases
- relief-heavy cases
- low-feature cases

then it cannot strongly support conclusions about robustness to those conditions.

---

## 14.4 Metric Stability

Historical benchmark results become harder to compare when metric definitions change.

Potential breaking changes include:

- fit RMSE → independent check-point RMSE
- source-image pixels → reference-image pixels
- changed coverage formula
- changed failure denominator
- changed success criteria

Metric changes may require rerunning historical methods for fair comparison.

---

## 14.5 Evaluation-Population Changes

Changing the pair population changes the experiment.

Results from:

```text
Population A
```

should not automatically be treated as directly equivalent to results from:

```text
Population B
```

even when the method name is unchanged.

---

## 14.6 Failure Accounting

A method that produces excellent accuracy on a small successful subset may still be unreliable overall if it fails many cases.

Benchmark interpretation should therefore preserve:

- successful cases
- rejected cases
- failed cases

rather than hiding unsuccessful examples.

---

## 14.7 Aggregate Statistics

Means, medians, and aggregate success metrics answer different questions.

Any aggregate can hide:

- outliers
- stress-category weaknesses
- sensor-specific behavior
- severe failures

Detailed reporting may therefore be necessary beneath headline summary values.

---

## 14.8 Internal Comparison

Comparison between ChandraMap V1–V4 provides internal project evidence.

It does not by itself establish:

- state-of-the-art performance
- superiority to all external methods
- whole-Moon robustness

External claims require compatible datasets, metrics, protocols, and evidence.

---

# 15. Benchmark V1 Limitations

Benchmark V1 is intentionally simple.

Its limitations are part of its value as a baseline.

The authoritative boundary is defined in [`v1-scope.md`](./v1-scope.md) and [`.ai/context/V1_SCOPE.md`](../../.ai/context/V1_SCOPE.md).

---

## 15.1 Known-Overlap Limitation

Canonical V1 receives the correct overlapping reference region.

Therefore V1 does **not** measure:

- whole-Moon localization
- global reference retrieval
- unknown-location search

A successful V1 registration must not be interpreted as evidence that ChandraMap can globally identify an arbitrary unknown lunar image.

---

## 15.2 Classical Sparse-Feature Limitation

V1 uses a classical sparse local-feature baseline.

Such methods may struggle under:

- severe scale gaps
- strong illumination changes
- cross-modal appearance differences
- repetitive terrain
- low-feature terrain

These are useful baseline stress conditions rather than reasons to silently expand V1.

---

## 15.3 Minimal Preprocessing Limitation

V1 intentionally avoids sophisticated sensor-specific preprocessing.

This makes the baseline easier to interpret but may reduce robustness on difficult sensor combinations.

---

## 15.4 Limited Scale Handling

Canonical V1 does not attempt to solve the complete physically aware scale-selection problem.

Large GSD differences may therefore expose a limitation that V2 is intended to investigate.

---

## 15.5 No Native Full Hyperspectral Pipeline

Canonical V1 does not claim native full-cube IIRS correspondence.

A deterministic derived 2D representation may be used where explicitly defined, but this does not remove the wider hyperspectral registration problem.

---

## 15.6 No Global Retrieval

V1 does not include:

- global descriptors
- FAISS
- Top-K candidate retrieval

because the reference region is already supplied.

---

## 15.7 No Learned Local Matching

Canonical V1 intentionally excludes learned local correspondence methods.

This allows later V3-style comparisons to measure whether advanced matching provides additional value.

---

## 15.8 Planar Geometry Limitation

V1's affine/homography-style global geometry is intentionally simple.

It may be insufficient for:

- large footprints
- strong relief
- large viewpoint differences
- spatially varying distortion

---

## 15.9 Refinement Boundary

Advanced local/sub-pixel refinement is outside canonical V1 where excluded by the authoritative scope.

V1 should not be silently strengthened with later-version capabilities merely to reduce residuals.

---

## 15.10 V1 Failure Is Not Automatically a Bug

If V1 rejects a difficult:

- large-scale-gap
- strong-illumination-change
- cross-modal
- relief-heavy

pair, that may be exactly the evidence needed to justify later research.

The correct response is not automatically:

> add V2/V3/V4 methods to V1 until the case passes.

Baseline stability matters.

---

# 16. V2, V3, and V4 Remaining Limitations

Later benchmark configurations may reduce some V1 limitations.

They do not become limitation-free.

---

## V2

V2 is intended to investigate stronger:

- sensor awareness
- scale handling
- registration representations
- structural preprocessing

Potential remaining limitations include:

- extreme modality difference
- insufficient common observable structure
- complex terrain geometry
- unknown global location
- preprocessing sensitivity

V2 should not be described as "solving scale mismatch" without benchmark evidence.

---

## V3

V3 may investigate advanced matching and retrieval.

Potential limitations include:

- learned-model domain shift
- checkpoint dependence
- increased compute
- retrieval ambiguity
- correct candidate absent from Top-K
- larger configuration space

Advanced matching does not remove the need for geometric verification and independent evaluation.

---

## V4

V4 may investigate:

- tie-point refinement
- local/piecewise geometry
- terrain-aware methods
- uncertainty
- confidence calibration
- stronger rejection logic

Potential remaining limitations include:

- imperfect terrain models
- local-model instability
- limited ground truth
- confidence-calibration error
- increased compute
- residual cross-modal ambiguity

V4 should not be described as:

- complete
- perfect
- final
- universally superior

without evidence.

---

## Later Version Does Not Mean Better Version

Version ordering represents research progression.

It does not define a performance ranking.

A later configuration may:

- improve one metric
- worsen another
- require more compute
- work better on one stress category
- perform worse on another

Benchmark evidence decides.

---

# 17. Runtime and Reproducibility Limitations

## 17.1 Runtime Comparability

Runtime depends on factors such as:

- hardware
- CPU/GPU
- image dimensions
- preprocessing
- model loading
- retrieval candidate count
- cache state
- timing scope

A matcher-only runtime should not be directly compared with complete end-to-end runtime as though they measure the same workload.

---

## 17.2 Hardware Dependence

Advanced learned or retrieval configurations may behave differently across hardware.

This can affect:

- runtime
- memory
- numerical behavior

No current hardware requirement is asserted here.

---

## 17.3 Memory Use

Potential memory consumers include:

- large lunar rasters
- image pyramids
- large feature sets
- learned models
- reference indexes

This document does not claim that memory is currently a measured ChandraMap bottleneck.

---

## 17.4 Randomness and Nondeterminism

Components such as:

- RANSAC
- stochastic model procedures
- some GPU operations

may produce small run-to-run differences.

Where meaningful:

- control seeds
- record seeds
- report repeated-run variability

Do not promise universal bitwise reproducibility without evidence.

---

## 17.5 Dependency Changes

Scientific behavior may change after updates to:

- numerical libraries
- image-processing libraries
- geospatial libraries
- ML frameworks
- model checkpoints

Unexpected benchmark changes after dependency updates should be investigated rather than automatically attributed to the algorithm.

---

## 17.6 Reproducibility Context

A scientific result may depend on:

- data identity
- configuration
- code revision
- metric implementation
- model/checkpoint
- environment

Missing run context limits the ability to reproduce or interpret historical results.

---

# 18. Current Implementation Limitations

This document does **not** enumerate specific current repository limitations unless they are confirmed by repository evidence.

Examples such as:

- missing retrieval implementation
- incomplete IIRS support
- unavailable V3/V4 pipeline
- missing API behavior
- unsupported operating systems
- missing benchmark runner

must not be inferred merely from:

- roadmap items
- design documents
- future architecture
- benchmark plans

A planned capability is not automatically a current defect.

Implementation-specific limitations should be added here only when the repository state, tests, issue history, or executed evidence supports them.

Until then, this document focuses primarily on durable scientific, methodological, evaluation, and benchmark limitations.

---

# 19. Generalization Limits

## 19.1 Entire-Moon Generalization

Results on a controlled benchmark population support conclusions about that population.

They do not automatically prove:

> reliable registration for every lunar region.

Lunar terrain varies substantially in:

- morphology
- texture
- illumination
- relief
- feature density

Whole-Moon robustness requires appropriately broad evaluation.

---

## 19.2 Sensor Generalization

Success on one instrument combination does not prove equivalent behavior on another.

Different instruments may vary in:

- GSD
- spectral response
- noise
- processing
- geometry
- spatial detail

Sensor combinations should be evaluated rather than assumed equivalent.

---

## 19.3 Illumination Generalization

Performance under one Sun-angle range does not automatically establish robustness under all illumination conditions.

Extreme shadow changes may expose qualitatively different failure modes.

---

## 19.4 Other Planetary Bodies

A method developed and evaluated on lunar imagery should not automatically be assumed to generalize to:

- Mars
- Venus
- Mercury
- asteroids
- other bodies

Different bodies and missions may involve different:

- terrain morphology
- atmospheres
- scattering
- sensors
- coordinate systems
- acquisition conditions

Planetary expansion requires separate evaluation.

---

# 20. Downstream Application Limitations

## 20.1 Mosaic Limitation

Mosaics can accumulate registration error.

A visually smooth mosaic may conceal:

- local misregistration
- drift
- biased transforms
- weak pairwise correspondence

Mosaic appearance does not remove the need for scientific registration evaluation.

---

## 20.2 UI Limitation

A frontend can display:

- matches
- registered images
- maps
- metrics
- overlays

but cannot improve scientific correctness by itself.

Presentation quality does not compensate for weak correspondence or invalid geometry.

---

## 20.3 API Limitation

An API improves access to scientific functionality.

It does not improve registration quality by itself.

API development should therefore not be presented as mitigation for scientific correspondence limitations.

---

## 20.4 Documentation Limitation

Documentation can explain:

- methodology
- assumptions
- limitations
- expected behavior

but documentation does not prove:

- implementation correctness
- scientific performance
- benchmark execution

Code, tests, and benchmark evidence remain separate.

---

# 21. Information / Observability Boundary

Some registration failures are not algorithmic shortcomings that can necessarily be "fixed."

If the source/reference observations do not contain enough common observable structure, reliable correspondence may be impossible from those observations alone.

Examples include:

- feature exists only in one sensor's spatial resolution
- terrain is hidden by shadow in one image
- overlap is too small
- modality suppresses common structure
- source and reference do not actually overlap

No image-registration algorithm can reliably infer arbitrary physical correspondence from information that is not present in the observations.

In such conditions:

> **rejection may be the scientifically correct output.**

---

# 22. Detecting and Mitigating Limitations

Limitations should be detected where practical and mitigated cautiously.

| Limitation                 | Possible Detection                               | Possible Mitigation / Research Direction                 |
| -------------------------- | ------------------------------------------------ | -------------------------------------------------------- |
| Extreme scale gap          | GSD metadata, low correspondence support         | GSD-aware scale handling, reference pyramids             |
| Illumination difference    | Metadata, structural mismatch, residual failures | Structural preprocessing, illumination-stress evaluation |
| Cross modality             | Sensor identity                                  | Sensor-aware representation research                     |
| Low-feature terrain        | Keypoint/candidate counts                        | Alternate representations or explicit rejection          |
| Repetitive terrain         | Ambiguous candidates, geometry diagnostics       | Stronger geometric verification                          |
| Clustered inliers          | Coverage diagnostics                             | Coverage gates, broader correspondence                   |
| Terrain relief             | Spatial residual pattern                         | Local or terrain-aware geometry                          |
| Poor ground truth          | Missing/uncertain check points                   | Improve evaluation data                                  |
| Retrieval miss             | Correct candidate absent from Top-K              | Retrieval research, metadata constraints                 |
| Learned-model domain shift | Lunar benchmark performance                      | Lunar-specific evaluation/adaptation research            |
| Nondeterminism             | Repeated-run variation                           | Seed control and reproducibility logging                 |

These are possible approaches.

They should not be interpreted as guaranteed fixes.

---

## Mitigation Language

Prefer:

- may reduce
- can help
- is intended to address
- should be evaluated
- is a later research direction

Avoid unsupported wording such as:

- completely solves
- eliminates
- guarantees
- fixes all cases

---

# 23. Limitations as Research Questions

Limitations are useful when they become measurable research questions.

### Scale Limitation

Limitation:

> Classical correspondence may struggle when GSD difference removes common local detail.

Research question:

> Does V2 scale-aware processing improve controlled scale-stress performance relative to V1?

---

### Illumination Limitation

Limitation:

> Changed shadow geometry may invalidate intensity-based similarity.

Research question:

> Which structural representations preserve useful correspondence across controlled illumination differences?

---

### Modality Limitation

Limitation:

> Panchromatic and hyperspectral imagery may have weak direct intensity similarity.

Research question:

> Which IIRS-derived representations preserve useful spatial correspondence?

---

### Geometry Limitation

Limitation:

> One homography may not model strong terrain relief.

Research question:

> Under which conditions do local or terrain-aware models improve independent registration accuracy?

---

### Retrieval Limitation

Limitation:

> Similar lunar regions can produce ambiguous global descriptors.

Research question:

> How often does the correct candidate appear in Top-K under a defined retrieval benchmark?

---

### Confidence Limitation

Limitation:

> Matcher or heuristic confidence is not necessarily calibrated.

Research question:

> Can confidence be calibrated against independent registration outcomes?

Turning limitations into controlled experiments is central to ChandraMap's benchmark-driven research model.

---

# 24. What a Limitation Does Not Mean

| Statement                                | Correct Interpretation                                                                                 |
| ---------------------------------------- | ------------------------------------------------------------------------------------------------------ |
| V1 rejects a difficult scale-stress pair | The classical baseline may lack sufficient scale robustness; this is not automatically a bug           |
| Registration is rejected                 | The system may be correctly refusing weak evidence                                                     |
| Many RANSAC inliers exist                | Geometry is model-consistent; independent physical truth still matters                                 |
| Fit residual is sub-pixel                | The model fits those points to fractional-pixel precision; ground accuracy is separate                 |
| Registered overlay looks good            | Visual alignment is encouraging but not sufficient scientific validation                               |
| Retrieval Recall@K is strong             | Correct regions are often retrieved; local registration quality remains a separate question            |
| Learned method is newer                  | It may offer different tradeoffs; superiority must be measured                                         |
| V4 includes more processing              | It is more complex, not automatically more accurate                                                    |
| A feature is absent from V1              | It may be intentionally outside V1 scope rather than a defect                                          |
| A capability is documented               | It may be target, experimental, planned, or implemented; documentation alone does not establish status |

---

# 25. Limitations, Assumptions, Bugs, and Non-Goals

A useful classification is:

```text
Assumption
→ condition accepted by a method

Limitation
→ where the method or assumption may become inadequate

Bug
→ implementation violates intended behavior

Non-Goal
→ capability intentionally outside the project's primary objective
```

Example:

### Assumption

> A homography adequately approximates the selected local region.

### Limitation

> Strong terrain relief may leave spatially varying residual distortion.

### Bug

> Homography is accidentally applied in the inverse direction.

### Non-Goal

> ChandraMap is not a complete rover-navigation system.

Keeping these categories distinct prevents scientific limitations from becoming excuses for engineering defects.

---

# 26. Known vs Uncharacterized Limitations

Not every limitation described in this document is currently quantified.

Where benchmark evidence exists, future revisions may classify a limitation as observed and link it to the relevant result/report.

Where evidence does not yet exist, the limitation should remain described as:

- domain-derived
- expected
- method-dependent
- open research
- not yet characterized

Do not invent:

- failure percentages
- RMSE degradation
- affected pair counts
- success rates
- hardware bottlenecks

to make a limitation appear more concrete.

---

# 27. Negative Results

Negative results are scientifically valuable when they are produced through valid controlled experiments.

Examples could include:

- scale normalization provided no measurable benefit
- learned matching underperformed the classical baseline
- a retrieval representation produced ambiguous candidates
- a hyperspectral representation contained insufficient spatial structure
- refinement destabilized some cases

Specific negative results should be added to scientific reports only when actual experiment evidence exists.

They must not be fabricated from expected limitations.

---

# 28. Limitation Maintenance

Update this document when:

- benchmark evidence identifies a repeated failure mode
- a limitation is materially reduced
- a new sensor/modality is introduced
- geometric assumptions change
- retrieval becomes part of a benchmark configuration
- evaluation methodology changes
- support boundaries change
- new benchmark coverage characterizes an existing limitation
- evidence shows that an expected limitation was incorrectly framed

---

## When a Limitation Is Mitigated

Do not automatically delete historically important limitations.

Instead, when appropriate, document:

- which method/version addresses the limitation
- whether it is reduced or fully resolved
- what evidence supports the change
- which cases remain difficult

Avoid:

> solved

unless the evidence genuinely supports that conclusion.

---

## Limitation Change Impact

A significant limitation change may require reviewing:

- [`assumptions.md`](./assumptions.md)
- [`goals.md`](./goals.md)
- [`non-goals.md`](./non-goals.md)
- [`v1-scope.md`](./v1-scope.md)
- benchmark specifications
- metric definitions
- dataset documentation
- architecture
- roadmap
- result interpretation

Scientific documentation should evolve together when the underlying methodology changes.

---

# 29. Related Documents

- [Project Overview](./overview.md) — explains ChandraMap's purpose and scientific context
- [Problem Statement](./problem-statement.md) — defines the lunar correspondence and registration challenge
- [Project Goals](./goals.md) — defines the outcomes ChandraMap aims to achieve
- [Project Non-Goals](./non-goals.md) — defines what does not constitute project success
- [Project Assumptions](./assumptions.md) — defines conditions accepted by ChandraMap methods and benchmarks
- [Benchmark V1 Scope](./v1-scope.md) — defines the human-facing V1 boundary
- [Project Terminology](./terminology.md) — defines canonical ChandraMap vocabulary
- [Project Context](../../.ai/context/PROJECT_CONTEXT.md) — canonical technical project context
- [Domain Context](../../.ai/context/DOMAIN_CONTEXT.md) — lunar imaging and registration constraints
- [Dataset Context](../../.ai/context/DATASETS.md) — mission/product, metadata, provenance, and dataset guidance
- [Canonical AI V1 Scope](../../.ai/context/V1_SCOPE.md) — detailed V1 contract
- [System Overview](../../.ai/architecture/SYSTEM_OVERVIEW.md) — high-level architecture
- [Processing Pipeline](../../.ai/architecture/PIPELINE.md) — scientific processing order and later-version flow
- [Data Flow](../../.ai/architecture/DATA_FLOW.md) — coordinate, transform, provenance, and result semantics
- [Testing Rules](../../.ai/development/TESTING_RULES.md) — software and scientific testing expectations
- [Benchmark Rules](../../.ai/development/BENCHMARK_RULES.md) — benchmark comparability and scientific-governance rules
- [Documentation Rules](../../.ai/development/DOCUMENTATION_RULES.md) — documentation and scientific-claim standards
- [Roadmap](../../ROADMAP.md) — planned project and research progression

ChandraMap should not be judged by whether it hides every failure.

A stronger standard is whether it can identify where its evidence becomes insufficient, report that limitation honestly, preserve reproducibility, and use controlled benchmarks to determine which later methods actually improve the scientific result.

<!-- Source specification: :contentReference[oaicite:0]{index=0} -->
