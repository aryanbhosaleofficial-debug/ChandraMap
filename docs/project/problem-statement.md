# ChandraMap Problem Statement

> **Problem:** Given lunar observations of the same or potentially overlapping surface region captured at different spatial resolutions, illumination conditions, viewing geometries, or sensing modalities, identify reliable physical correspondences, estimate their geometric relationship, register the observations, quantify the quality of that registration, and explicitly reject the result when the available evidence is insufficient.

ChandraMap addresses **lunar image correspondence and registration** across heterogeneous observations.

The central challenge is not simply to make two lunar images look similar. It is to determine whether image locations represent the **same physical lunar terrain**, verify that the proposed relationships are geometrically consistent, estimate an appropriate transformation between the observations, and provide evidence that the resulting alignment is trustworthy.

The original project motivation came from multi-modal, scale- and illumination-robust correspondence using Chandrayaan-2 imagery. ChandraMap now treats that problem as part of a broader open-source research and engineering effort around reproducible lunar image registration and benchmarking.

---

## 1. Problem in One Sentence

> **Find the same physical lunar features in observations that may look very different, use those features to align the observations, and measure whether the alignment is trustworthy.**

---

## 2. Problem Definition

Consider:

- a **source observation** \(S\), and
- a **reference observation** \(R\),

which may contain partially or fully overlapping lunar terrain.

The problem is to identify a set of relationships of the form:

```text
source point
      ↔
reference point
```

where both points ideally refer to the same physical feature or surface location.

From sufficiently reliable correspondences, estimate a geometric mapping conceptually represented as:

```text
T:
source coordinates
        ↓
reference coordinates
```

The resulting transformation should then support registration of the source with the reference.

The complete problem is therefore:

```text
Source Observation
        +
Reference Observation
        ↓
Candidate Correspondence Discovery
        ↓
Geometric Verification
        ↓
Transformation Estimation
        ↓
Registration
        ↓
Evaluation
        ↓
Accept or Reject
```

This formulation is intentionally independent of any one feature detector, matcher, geometric estimator, or learned model.

Algorithms may change.

The scientific problem remains the same.

---

## 3. Why This Problem Matters

Multiple lunar instruments can observe the same surface while producing substantially different representations of that terrain.

Reliable registration makes it possible to relate those observations spatially.

Potential research and engineering value includes:

- comparing observations from different lunar instruments
- aligning high- and lower-resolution imagery
- supporting cross-sensor analysis
- localizing an observation relative to reference imagery
- studying how correspondence methods behave under lunar domain shift
- enabling controlled registration benchmarks
- supporting trustworthy downstream overlays and mosaics
- enabling reproducible investigation of failure conditions

The value comes from the **geometric relationship between observations**, not simply from producing visually attractive aligned images.

---

## 4. Problem Inputs

A ChandraMap problem instance may contain some or all of the following information.

Not every scientific product is guaranteed to provide every metadata field.

### Source Observation

The **source** is the lunar observation being registered or localized.

Relevant project context includes observations derived from instruments such as:

- Chandrayaan-2 OHRC
- Chandrayaan-2 TMC-2
- Chandrayaan-2 IIRS, where an appropriate registration representation is available

The source/reference distinction should remain explicit throughout the problem.

Avoid treating the pair merely as:

```text
image1
image2
```

when transformation direction matters.

---

### Reference Observation

The **reference** defines the image or coordinate context against which the source is aligned.

Relevant project reference context includes:

- LRO/LROC NAC
- LRO/LROC WAC

The appropriate reference depends on the scientific task, available products, required coverage, and scale.

---

### Metadata

Where available, useful metadata may include:

- mission
- instrument
- product identity
- dimensions
- spatial scale / GSD
- acquisition information
- footprint
- projection
- coordinate reference information
- viewing geometry
- illumination geometry
- processing state
- spectral information

Metadata should be used when scientifically valid.

The system should not fabricate missing metadata simply because later processing would benefit from having it.

---

### Optional Evaluation Information

A benchmark or evaluation instance may also provide:

- known overlap
- trusted control information
- independent check points
- validated reference coordinates
- benchmark pair identity

Such information is evaluation context and should remain distinct from automatically generated matcher output.

---

## 5. Scientific Data Context

The instruments relevant to ChandraMap differ substantially in spatial scale and sensing characteristics.

The values below are broad project context, not universal constants for every product.

Specific product metadata should take precedence during actual processing.

### Chandrayaan-2 OHRC

The **Orbiter High Resolution Camera (OHRC)** provides very high-resolution panchromatic lunar imagery.

Project context commonly describes its spatial sampling at approximately:

> **~0.25–0.32 m/px**

depending on product and source documentation.

---

### Chandrayaan-2 TMC-2

The **Terrain Mapping Camera-2 (TMC-2)** provides panchromatic terrain imagery.

Project context commonly uses approximately:

> **~5 m/px**

as broad instrument-level context.

The canonical project name is **TMC-2**, not simply `TMC`.

---

### Chandrayaan-2 IIRS

The **Imaging Infrared Spectrometer (IIRS)** provides hyperspectral / imaging-infrared observations.

Project context commonly uses approximately:

> **~80 m/px**

as broad spatial context.

IIRS must not be reduced conceptually to a low-resolution conventional camera.

Its native observations contain spectral information across many wavelengths.

A conventional 2D correspondence system may therefore require a derived representation such as:

- selected spectral band
- dimensionality-reduced component
- spectral composite
- structural representation
- gradient representation

The choice of representation is part of the scientific method and should not be assumed universally.

---

### LRO/LROC NAC

The **Lunar Reconnaissance Orbiter Camera Narrow Angle Camera (NAC)** provides detailed lunar imagery suitable for high-resolution local reference use.

Its product scale varies.

Exact product metadata should be used rather than one universal number.

---

### LRO/LROC WAC

The **Lunar Reconnaissance Orbiter Camera Wide Angle Camera (WAC)** provides wider-area lunar imagery and context.

WAC may be useful where broader spatial coverage is more important than NAC-level local detail.

NAC and WAC should not be collapsed into one generic "LRO image" concept when their roles matter.

---

## 6. Expected Outputs

A correct system is not required to return a valid transformation for every input pair.

A successful or rejected result should contain enough information to explain what happened.

### Candidate Correspondences

Candidate correspondences are proposed source/reference point relationships produced before geometric verification.

Conceptually:

```text
source candidate point
          ↔
reference candidate point
```

They are hypotheses.

They are not yet trusted physical correspondences.

---

### Verified Inliers

A **verified inlier** is a candidate match that is consistent with the selected geometric model under the active verification method.

Conceptually:

```text
Candidate Match
      ↓
Geometric Verification
      ↓
Verified Inlier
```

Verified inliers provide geometric evidence.

They are not automatically independent ground truth.

---

### Geometric Transformation

The system should estimate the relationship between the source and reference coordinate spaces when sufficient evidence exists.

Potential global 2D models may include:

- affine transformation
- homography

depending on the specific benchmark or configuration.

Transform direction must remain explicit.

For example:

```text
source → reference
```

is not interchangeable with:

```text
reference → source
```

---

### Registered Output

When an acceptable transformation exists, the source may be transformed into the reference frame.

Potential outputs include:

- registered imagery
- transformed coordinates
- overlap view
- registered preview

A successful warp does not, by itself, prove that registration is correct.

---

### Quality Information

A result should provide measurable evidence where appropriate.

Potential information includes:

- candidate-match count
- verified-inlier count
- inlier ratio
- geometric residuals
- spatial coverage
- image-space error
- independent check-point RMSE where valid check points exist
- ground error where scientifically meaningful
- runtime where the evaluation defines it
- success/rejection/failure status

Detailed metric definitions belong in benchmark and metric documentation.

---

### Explicit Failure or Rejection

A correct result may be:

```text
Rejected
```

or:

```text
Insufficient Evidence
```

when trustworthy registration cannot be established.

The problem is not defined as:

> always return a transformation.

---

## 7. The Problem Is More Than Feature Matching

Finding similar-looking keypoints is only one part of the problem.

A complete registration problem includes:

```text
Feature / Correspondence Discovery
              ↓
      Candidate Matches
              ↓
     Geometric Verification
              ↓
       Verified Inliers
              ↓
  Transformation Estimation
              ↓
         Registration
              ↓
   Quantitative Evaluation
              ↓
       Accept or Reject
```

A matcher can generate many plausible-looking correspondences that are physically wrong.

Geometric reasoning and evaluation are therefore fundamental parts of the problem.

---

## 8. Why Lunar Image Correspondence Is Difficult

The central challenge is that the same physical terrain may produce very different image representations.

### Scale and GSD Difference

Different lunar instruments observe the same surface at very different physical ground scales.

The same crater might occupy:

```text
hundreds of pixels
```

in one observation but:

```text
only a few pixels
```

in another.

Fine terrain structure visible in a higher-resolution product may not exist in the lower-resolution observation at all.

This is a genuine information mismatch.

It is not only an image-size mismatch.

#### Upsampling Does Not Recover Detail

Increasing the dimensions of a coarse image through interpolation adds samples between existing measurements.

It does not recreate physical surface information that the sensor never captured.

Therefore:

```text
resize both images to identical dimensions
```

does not scientifically solve the scale problem.

---

### Sun Angle and Shadow Geometry

The lunar surface is strongly affected by directional illumination.

Different illumination conditions can change:

- shadow direction
- shadow length
- crater-wall visibility
- illuminated slopes
- ridge appearance
- edge strength
- local contrast
- apparent surface structure

The same crater can therefore appear very different between acquisitions.

Operations such as:

- histogram equalization
- contrast normalization
- CLAHE
- intensity normalization

may improve photometric comparability in some cases.

They do not provide true Sun-angle invariance because the **geometry of shadows and visible surfaces can change**.

---

### Sensor Modality Difference

Different instruments may measure different physical characteristics.

Panchromatic imagery and hyperspectral / imaging-infrared data should not be expected to produce identical pixel-intensity patterns.

Raw pixel comparison may therefore be unreliable.

Cross-sensor correspondence may need to rely on information such as:

- stable structural features
- gradients
- edges
- geometry
- terrain organization

rather than assuming identical radiometric appearance.

---

### IIRS Modality

IIRS deserves particular attention because its native data is hyperspectral.

Traditional local image matching commonly expects a 2D registration representation.

An IIRS workflow may therefore need to derive a representation before local correspondence is attempted.

The problem is not:

> make the entire hyperspectral cube behave like an ordinary grayscale photograph.

The scientific question is instead:

> Which representation preserves enough spatial structure for meaningful correspondence while retaining traceable provenance?

---

### Viewing Geometry Difference

Two observations may be captured from different:

- spacecraft positions
- viewing angles
- acquisition geometries
- map/projection contexts

Corresponding terrain therefore may not be related by simple image translation.

The geometric relationship must be estimated appropriately.

---

### Terrain Relief

The lunar surface is three-dimensional.

Important terrain structures include:

- crater walls
- slopes
- ridges
- mountains
- depressions

Terrain relief can cause local geometric effects that a single planar model cannot represent perfectly across every region.

Affine transformations and homographies may be useful approximations in appropriate local conditions, but more complex regions can require more sophisticated geometry.

That is an advanced research problem rather than something this problem statement assumes is already solved.

---

### Repetitive Crater Terrain

Lunar images often contain many similar structures:

- circular craters
- overlapping crater rims
- small impact features
- rocks
- ridges
- repeated texture patterns

A matcher can therefore find a visually plausible crater in the wrong location.

This creates a fundamental ambiguity:

```text
looks similar
≠
is the same physical feature
```

Geometric verification is required to reduce such false correspondences.

---

### Low-Feature Terrain

Some lunar regions may contain:

- smooth surfaces
- weak local texture
- limited edges
- large shadowed areas
- insufficient distinctive structure

Such areas may not contain enough information to establish reliable correspondence.

The correct output may therefore be rejection.

---

### Partial Overlap

The source and reference may contain only partially overlapping terrain.

Large portions of either image may have no corresponding region in the other observation.

A valid method must tolerate:

- different footprints
- missing areas
- border overlap
- unequal coverage

Not every pixel or feature should be expected to have a correspondence.

---

### Shadowed or Unobservable Features

A terrain feature visible in one observation may be:

- hidden by shadow in another
- outside the other footprint
- poorly resolved
- spectrally different
- too small to survive coarse sampling

The goal is therefore not to match every feature.

It is to identify a **sufficient set of reliable correspondences**.

---

## 9. Local Registration vs Global Localization

ChandraMap contains two related but different problem variants.

### Local / Known-Overlap Registration

The correct or approximate reference region is already known.

The question becomes:

> Can the system establish precise local correspondences and estimate reliable alignment?

Conceptually:

```text
Source Image
    +
Known Reference Region
        ↓
Local Correspondence
        ↓
Geometry
        ↓
Registration
```

This is the canonical Benchmark V1 problem variant.

---

### Global / Unknown-Location Localization

The correct reference region is not known in advance.

The problem now includes an additional question:

> Where in the reference dataset should local registration be attempted?

Conceptually:

```text
Source Observation
        ↓
Global Candidate Search
        ↓
Candidate Reference Region(s)
        ↓
Local Correspondence
        ↓
Geometric Verification
        ↓
Registration
```

Global localization and precise local registration should not be treated as the same task.

---

### Metadata-Constrained Search

When valid footprint, location, or projection metadata already constrains the source region, the system may use it.

Using valid metadata is scientifically appropriate.

The objective is not to force computer vision to rediscover information already provided reliably by the scientific product.

---

## 10. What Makes a Correspondence Reliable?

Descriptor similarity or matcher confidence alone is not enough.

A reliable correspondence should ideally have evidence from several sources.

Possible evidence includes:

- local structural similarity
- feature distinctiveness
- consistency with neighboring correspondences
- geometric-model consistency
- reasonable transformation residual
- spatial distribution across the overlap

Conceptually:

```text
Visual / Descriptor Similarity
            +
Geometric Consistency
            +
Spatial Context
            ↓
Stronger Correspondence Evidence
```

Exact thresholds belong to benchmark/configuration definitions rather than this problem statement.

---

## 11. Geometric Verification

Candidate correspondences must be tested for geometric consistency.

A robust estimator such as RANSAC may conceptually perform:

```text
Candidate Matches
        ↓
Robust Geometric Estimation
        ↓
Model-Consistent Group
        ↓
Verified Inliers
        +
Rejected Outliers
        +
Initial Transformation
```

RANSAC does not create the original visual matches.

Its role is to identify which proposed correspondences agree with a common geometric relationship.

A RANSAC inlier is therefore:

> a model-consistent candidate

not automatically:

> independently verified ground truth.

---

## 12. What Makes a Registration Reliable?

A plausible transformation matrix is not sufficient evidence of registration quality.

A trustworthy result should generally have evidence such as:

- enough reliable correspondences
- geometrically consistent inliers
- a valid transformation
- adequate spatial distribution
- reasonable residuals
- stable coordinate semantics
- independent evaluation where possible
- no obvious degeneracy

No universal numeric thresholds are defined here.

Those belong in benchmark specifications and metric definitions.

---

### Spatial Coverage

Match count alone can be misleading.

For example:

```text
100 verified matches
all concentrated around one crater
```

may provide weaker global geometric support than:

```text
30 verified matches
distributed throughout the shared region
```

The transformation should ideally be constrained by correspondences that meaningfully cover the overlap.

---

### Geometric Degeneracy

Some correspondence arrangements may not constrain a transformation reliably.

Examples include:

- too few points
- points concentrated in one tiny region
- nearly collinear arrangements for models requiring broader geometry
- numerically unstable configurations

A transformation should not be accepted simply because a numerical routine returned a matrix.

---

## 13. Evaluation Requirement

The registration problem is incomplete without evaluation.

ChandraMap aims to determine not only:

> Can an image be warped?

but:

> Is the resulting registration supported by measurable evidence?

Potential evaluation information includes:

- candidate-match count
- verified-inlier count
- inlier ratio
- spatial coverage
- residuals
- source-image pixel error
- independent check-point RMSE
- physically meaningful ground error where possible
- success/rejection/failure
- runtime where relevant

Exact formulas are defined elsewhere.

---

### Fit Points vs Check Points

This distinction is fundamental.

**Fit points** are used to estimate the transformation.

```text
Fit Points
    ↓
Estimate Transform
```

**Check points** are independent data used to evaluate it.

```text
Independent Check Points
        ↓
Evaluate Final Transform
```

Error computed only on points already used to fit a transformation is useful diagnostic evidence.

It should not automatically be described as independent registration accuracy.

---

### Error Units

Every accuracy/error value must have a meaningful unit.

Examples include:

- source-image pixels
- reference-image pixels
- projected coordinate units
- metres where a valid physical conversion exists

A statement such as:

```text
RMSE = 0.7
```

is incomplete without knowing the units and evaluation context.

---

### Pixel Error vs Ground Error

An error in pixels cannot automatically be interpreted as the same numerical value in metres.

Conceptually:

```text
Image-Space Error
        +
Valid Physical / Geospatial Context
        ↓
Ground-Space Error
```

The conversion may depend on:

- product GSD
- reference frame
- coordinate domain
- projection
- geometry

---

### Sub-Pixel vs Sub-Metre

`Sub-pixel` means:

> less than one pixel in the stated image coordinate space.

It does not automatically mean:

> less than one metre on the lunar surface.

The physical meaning depends on the spatial context of that image.

---

### Visual Overlay Limitation

Visual overlays are useful for:

- debugging
- interpretation
- demonstrations
- qualitative inspection

They are not sufficient evidence of registration accuracy.

An apparently good overlay can still contain systematic geometric error.

---

## 14. Failure Is a Valid Outcome

A scientifically trustworthy registration system must be allowed to conclude:

> **Reliable registration cannot be established from the available evidence.**

Potential reasons include:

- insufficient features
- insufficient candidate matches
- geometrically inconsistent correspondences
- poor spatial distribution
- unstable transformation
- incorrect reference candidate
- severe modality mismatch
- extreme scale mismatch
- inadequate overlap

A rejected result can be more valuable than a plausible-looking but unsupported transformation.

---

### Forced Registration Is Not Success

The following are not sufficient definitions of success:

- an image was warped
- a transform matrix was returned
- many match lines were drawn
- a matcher reported high confidence
- an overlay looks visually plausible

All of these can occur while the underlying physical correspondence is wrong.

---

## 15. Problem Assumptions and Variants

Different experiments may make different assumptions.

These assumptions change the difficulty of the problem and must therefore remain explicit.

### Known Sensor

The instrument identity is available.

This may enable scientifically appropriate interpretation of modality and scale.

---

### Known Approximate Location

The source has usable location or footprint metadata.

The reference search can be geographically constrained.

---

### Known Overlap

The correct overlapping reference image or crop is already supplied.

This isolates the local correspondence and registration problem.

---

### Unknown Location

The system must first identify candidate reference regions before local registration.

This introduces a retrieval/localization problem in addition to correspondence.

---

### Map-Projected Inputs

Some spatial geometry may already be available in the product.

Such information should not be discarded without reason.

---

### Less-Georeferenced Inputs

Products with limited geometric metadata may require greater reliance on image correspondence and geometric estimation.

Not every benchmark version assumes the same input conditions.

---

## 16. Benchmark V1 Problem Variant

Canonical Benchmark V1 isolates the fundamental known-overlap registration problem.

Given:

```text
Source 2D Image
        +
Correct Overlapping Reference 2D Image
```

determine:

```text
Candidate Matches
        ↓
Verified Inliers
        ↓
Transformation
        ↓
Registration
        ↓
Measurable Evaluation
        ↓
Success / Rejection / Failure
```

Whole-Moon search is not required.

Canonical V1 intentionally remains simple so that it can serve as a meaningful classical baseline.

The authoritative V1 boundary is defined in the [V1 Scope](../../.ai/context/V1_SCOPE.md).

---

## 17. Later Research Variants

The fundamental problem remains the same while the methods and assumptions can evolve.

### Benchmark V2

V2 conceptually investigates stronger treatment of:

- sensor differences
- physical scale / GSD differences
- multi-scale reference representations
- structural preprocessing
- derived IIRS registration representations

---

### Benchmark V3

V3 may investigate:

- stronger learned/local correspondence methods
- remote-sensing matchers
- global retrieval when location is unknown
- candidate-region search

---

### Benchmark V4

V4 may investigate:

- sub-pixel refinement
- more complex local geometry
- terrain-aware registration
- advanced IIRS handling
- uncertainty
- calibrated confidence
- stronger failure classification

These are research directions.

Their documentation does not imply that every capability is implemented.

---

### The Problem Must Survive Algorithm Replacement

The ChandraMap problem is not:

> How do we make SIFT work on the Moon?

Nor is it:

> How do we apply LightGlue or LoFTR to lunar images?

The stable problem is:

> How can trustworthy cross-observation lunar correspondence and registration be established and measured?

SIFT, learned matchers, remote-sensing methods, retrieval systems, and refinement techniques are candidate **solutions** to parts of that problem.

They do not define the problem itself.

---

## 18. Problem Decomposition

The larger research problem can be divided into seven conceptual subproblems.

| Problem                        | Question                                                                            |
| ------------------------------ | ----------------------------------------------------------------------------------- |
| **A — Input Understanding**    | What scientific product, sensor, modality, geometry, and scale are being processed? |
| **B — Candidate Search**       | Where is the corresponding reference region?                                        |
| **C — Local Correspondence**   | Which source/reference points may represent the same physical features?             |
| **D — Geometric Verification** | Which candidate matches are mutually consistent?                                    |
| **E — Registration**           | What transformation aligns the source with the reference?                           |
| **F — Evaluation**             | How accurate and reliable is the resulting registration?                            |
| **G — Failure Decision**       | Is the evidence strong enough to accept the result?                                 |

This decomposition describes the problem.

It does not prescribe specific software modules or algorithms.

---

## 19. Core Problem vs Downstream Applications

The scientific core is:

```text
Correspondence
      +
Geometric Verification
      +
Transformation
      +
Registration
      +
Evaluation
```

Downstream applications may include:

- match visualizations
- registered previews
- overlays
- lunar mosaics
- map interfaces
- dashboards
- APIs
- search interfaces

These can make ChandraMap results easier to inspect or use.

They do not replace the underlying correspondence problem.

---

### Mosaic Is Not the Core Problem

A mosaic depends on trustworthy registration.

If the underlying correspondences are wrong, a mosaic may hide or propagate alignment errors.

Therefore:

> mosaic generation is downstream of registration quality.

---

### Map UI Is Not the Core Problem

An interactive lunar map may provide a useful demonstration or interface.

It does not itself solve:

- correspondence
- geometric verification
- transformation estimation
- accuracy evaluation

---

## 20. Problem Boundaries

### In Scope

At the project-problem level, ChandraMap focuses on:

- lunar image correspondence
- lunar image registration
- multi-resolution matching
- multi-sensor matching
- robustness to substantial illumination differences
- geometric verification
- transformation estimation
- quantitative evaluation
- explicit failure/rejection
- controlled benchmarking
- localization/retrieval where relevant to later research variants

This is scope, not implementation status.

---

### Out of the Core Problem

ChandraMap is not primarily intended to solve:

- autonomous spacecraft navigation
- rover path planning
- lunar mineral classification as the main task
- complete lunar GIS functionality
- spacecraft mission planning
- planetary mission control
- photorealistic Moon rendering
- generic panorama stitching
- global mosaic generation as the primary scientific deliverable

Some related capabilities may become downstream demonstrations or research extensions.

---

## 21. Original Problem Context and Current Project

The original problem motivation focused on robust lunar image correspondence across:

- multiple sensing modalities
- large scale differences
- changing Sun-angle conditions

using Chandrayaan-2 imagery.

ChandraMap retains that scientific motivation but broadens the engineering and research objective.

The current project frames the problem as:

> **reproducible lunar correspondence, geometric registration, evaluation, and benchmarking across heterogeneous observations.**

This makes it possible to compare simple and advanced methods systematically rather than treating the project as a one-time demonstration.

ChandraMap is an independent open-source research/software project. References to ISRO, NASA, LRO/LROC, or mission instruments identify scientific data and mission context; they do not imply endorsement, maintenance, or official adoption of ChandraMap by those organizations.

---

## 22. Research Questions

ChandraMap's problem formulation supports research questions such as:

1. How robust is a simple classical registration baseline on lunar imagery?
2. How much does physically meaningful scale handling improve correspondence?
3. Which sensor-aware representations improve cross-sensor matching?
4. How should IIRS be represented for useful spatial correspondence?
5. Under which conditions do advanced or learned local matchers improve over classical methods?
6. How much does illumination difference affect correspondence reliability?
7. How should spatial coverage be measured alongside inlier count?
8. When is global retrieval necessary instead of metadata-constrained search?
9. How should unreliable registrations be detected and rejected?
10. Which sensor/modality combinations produce the most difficult failure modes?
11. When do simple global transformations become inadequate because of terrain relief?
12. How can registration quality be evaluated when independent ground truth is limited?

These are research questions, not claims that ChandraMap has already answered them.

---

## 23. Benchmark-Driven Problem Evaluation

ChandraMap treats correspondence and registration as measurable research problems.

Methods should be evaluated using controlled data and consistent evaluation definitions where scientifically appropriate.

For direct method comparison:

```text
Same Image Pair
      +
Same Ground Truth
      +
Same Metric Definition
      +
Controlled Configuration
      ↓
Compare Methods
```

Changing the data population at the same time as the method can make it difficult to determine what actually caused a performance difference.

Benchmark V1–V4 therefore exist to provide progressively richer research configurations while preserving the ability to compare them meaningfully.

They are benchmark configurations, not software-release versions.

---

## 24. Failure Analysis Is Part of the Research

A failed registration should not simply be discarded.

Failure can reveal important scientific limitations.

Questions may include:

- Was the GSD difference too large?
- Did illumination change the visible structure?
- Was the modality mismatch too severe?
- Was the terrain repetitive?
- Were too few distinctive features present?
- Was the overlap too small?
- Was the transformation model inadequate?
- Was the wrong reference region selected?

Categorizing failures helps distinguish:

```text
algorithm limitation
```

from:

```text
data limitation
```

from:

```text
geometry limitation
```

from:

```text
software defect
```

This makes rejection and failure scientifically useful information.

---

## 25. Success Criteria

A successful ChandraMap solution should ideally:

1. identify physically meaningful candidate correspondences
2. reject geometrically inconsistent matches
3. retain a sufficient set of reliable inliers
4. constrain the transformation using useful spatial support
5. estimate a valid source-to-reference geometric relationship
6. register the source appropriately
7. report quality measurements with meaningful units
8. use independent evaluation where available
9. preserve data and processing provenance
10. remain reproducible
11. explicitly reject cases with insufficient evidence
12. support fair comparison with alternative methods

No fixed RMSE, inlier ratio, coverage threshold, confidence percentage, or success-rate target is defined by this problem statement.

---

### What Success Does Not Mean

Success does not merely mean:

```text
overlay looks good
```

or:

```text
many lines were drawn between images
```

or:

```text
a matrix was returned
```

or:

```text
the matcher reported high confidence
```

or:

```text
a warp completed without an exception
```

All of those can occur in an incorrect registration.

---

### Perfect Matching Is Not Required

Not every feature in the source should be expected to have a valid counterpart.

Reasons include:

- partial overlap
- different spatial resolution
- shadows
- sensor modality
- unavailable detail
- different coverage

The goal is a sufficient and trustworthy correspondence set.

---

### Zero Error Is Not the Only Valid Outcome

Real scientific products may contain:

- measurement uncertainty
- projection uncertainty
- annotation uncertainty
- interpolation effects
- terrain-induced geometric effects

The objective is measurable and scientifically interpretable error, not an unrealistic guarantee of perfect zero-error alignment.

---

### Always Accepting Is Not the Goal

A correct rejection may be more scientifically valuable than an incorrect accepted registration.

---

## 26. Known Challenges and Limitations

The problem formulation has important limitations and open challenges.

### Extreme Scale Difference

Very large GSD differences may remove shared fine-scale information.

---

### Strong Illumination Difference

Changing Sun angle can alter actual visible structure, not just brightness.

---

### Cross-Modality Difference

Some sensor pairs may share limited direct radiometric similarity.

---

### Limited Ground Truth

Independent check points may not always be available.

This limits the strength of absolute accuracy claims.

---

### Local Planar Geometry

Affine or homography models may be insufficient over large or relief-heavy regions.

---

### Reference Ambiguity

Repeated lunar structures can make global candidate retrieval difficult.

---

### Product Metadata Variation

Available metadata and processing state may vary across products.

---

### Learned-Model Domain Shift

Methods trained primarily on terrestrial imagery may not automatically generalize perfectly to lunar terrain.

Their lunar performance must be evaluated rather than assumed.

---

### No Universal Invariance Guarantee

The research problem concerns improving robustness to:

- scale differences
- illumination differences
- modality differences

It should not be described as mathematically guaranteeing universal invariance under every possible lunar observation condition.

---

## 27. Problem Summary

The ChandraMap problem can be summarized as:

```text
Heterogeneous Lunar Observations
            ↓
Find Candidate Common Features
            ↓
Reject Incorrect Relationships
            ↓
Establish Geometrically Consistent Correspondences
            ↓
Estimate Source → Reference Geometry
            ↓
Register the Observations
            ↓
Measure Quality
            ↓
Accept Reliable Results
        OR
Reject Insufficient Evidence
```

The core challenge is therefore not simply:

> match images.

It is:

> **establish defensible physical correspondence between heterogeneous lunar observations and determine whether the resulting registration is trustworthy.**

---

## 28. Where to Go Next

### Understand the Project

Read the [ChandraMap Project Overview](./overview.md).

It explains the broader project purpose, scientific data context, benchmark philosophy, and project boundaries.

---

### Understand the Lunar Domain

Read the [Domain Context](../../.ai/context/DOMAIN_CONTEXT.md).

It provides deeper treatment of:

- lunar illumination
- spatial scale
- sensor modality
- terrain geometry
- coordinate considerations

---

### Understand Project Terminology

Read the [Terminology](../../.ai/context/TERMINOLOGY.md).

It defines canonical terms including:

- source image
- reference image
- candidate match
- verified inlier
- tie point
- fit point
- check point
- GSD
- global retrieval
- local matching
- registration

---

### Understand the Data

Read the [Dataset Context](../../.ai/context/DATASETS.md).

It covers sensor/product roles, provenance, metadata, and data-handling principles.

---

### Understand the Processing Flow

Read the [Processing Pipeline](../../.ai/architecture/PIPELINE.md).

The problem statement defines **what must be solved**.

The pipeline defines **how the processing stages are organized to solve it**.

---

### Understand the System Architecture

Read the [System Overview](../../.ai/architecture/SYSTEM_OVERVIEW.md).

It defines the high-level architectural responsibilities around the scientific core.

---

### Understand Benchmark V1

Read the [V1 Scope](../../.ai/context/V1_SCOPE.md).

It defines the canonical known-overlap classical baseline and its explicit exclusions.

---

### Understand Benchmark Methodology

Read the [Benchmark Rules](../../.ai/development/BENCHMARK_RULES.md).

They define how candidate solutions should be compared fairly and reproducibly.

---

### Explore the Documentation

Use the [Documentation Landing Page](../index.md) for guided orientation or [`docs/README.md`](../README.md) for the broader documentation index.

---

### Explore Future Direction

Read the [Roadmap](../../ROADMAP.md) for planned research and engineering evolution.

Roadmap items represent future direction and should not be interpreted as current implementation merely because they are documented.

<!-- Source specification: :contentReference[oaicite:0]{index=0} -->
