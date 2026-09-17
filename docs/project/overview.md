# ChandraMap Project Overview

ChandraMap is an open-source lunar image correspondence and registration research project focused on finding the same physical lunar features across observations captured by different instruments and under different imaging conditions.

The project studies a difficult scientific question:

> **Can two observations of the same lunar region be matched reliably despite differences in spatial resolution, illumination, sensor modality, viewing geometry, and terrain appearance—and can that alignment be measured rather than only judged visually?**

ChandraMap therefore treats **correspondence, geometric verification, registration, and measurable quality** as its scientific core. Mosaics, interactive maps, dashboards, and other visual applications are downstream uses of trustworthy registration rather than the primary objective.

---

## 1. What Is ChandraMap?

ChandraMap is a research/software system for **lunar image correspondence and registration**.

Given a source lunar observation and a reference observation, the project aims to determine:

1. whether the observations represent the same physical lunar region
2. which image locations correspond to the same surface features
3. which proposed correspondences are geometrically consistent
4. what transformation relates the source and reference images
5. how accurately and reliably the registration can be measured
6. whether the result should be accepted, refined, or rejected

A simplified view is:

```text
Source Lunar Image
        +
Reference Lunar Image
        ↓
Find Corresponding Features
        ↓
Verify the Geometry
        ↓
Estimate the Alignment
        ↓
Register the Images
        ↓
Measure the Result
```

ChandraMap grew from a lunar multi-modal correspondence problem involving Chandrayaan-2 imagery, but it is being developed as a broader open-source research and engineering project rather than as a hackathon feature demonstration.

---

## 2. The Problem ChandraMap Solves

Two images may contain the same crater, ridge, slope, or other lunar feature while looking surprisingly different.

For example, a Chandrayaan-2 image and an LRO reference image may observe the same crater, but:

- the crater may occupy very different numbers of pixels
- the shadows may point in different directions
- one image may contain much finer terrain detail
- contrast and radiometric appearance may differ
- the observation geometry may differ
- only part of the region may overlap

A conventional image matcher may therefore produce:

- too few correspondences
- incorrect crater-to-crater matches
- matches concentrated in only one small region
- geometrically inconsistent matches
- a visually plausible but scientifically weak alignment

ChandraMap's goal is not merely to make two images appear similar.

Its goal is to establish **trustworthy physical correspondence and measurable geometric registration**.

---

## 3. Why Lunar Image Registration Is Difficult

### Scale and GSD Differences

Different lunar instruments observe the surface at very different physical scales.

Conceptually:

```text
OHRC
→ sub-metre-scale detail

TMC-2
→ metre-scale terrain detail

IIRS
→ much coarser spatial information
```

The same crater might therefore occupy hundreds of pixels in one observation and only a small number of pixels in another.

This is not just an image-size problem. It is a **physical ground-scale problem**.

Increasing the number of pixels through interpolation does not recreate terrain detail that the original sensor never measured.

Scale-aware processing should therefore reason about **ground sampling distance and physically meaningful effective resolution**, not only digital image dimensions.

---

### Sun Angle and Shadows

The Moon's appearance is strongly influenced by directional sunlight.

Changes in illumination can alter:

- shadow direction
- shadow length
- visible crater walls
- ridge brightness
- local contrast
- apparent feature shape

A crater photographed under one Sun angle may therefore look structurally different under another.

Simple brightness normalization can reduce some radiometric differences, but it should not be described as complete Sun-angle invariance.

---

### Sensor and Modality Differences

Different instruments may measure different physical properties.

For example:

- panchromatic imagery primarily records broad visible-intensity structure
- hyperspectral / imaging-infrared observations contain wavelength-dependent information

Two sensors observing the same terrain do not necessarily produce matching pixel intensities.

Cross-sensor correspondence may therefore need to rely more strongly on:

- structure
- gradients
- edges
- geometry
- stable terrain features

rather than direct intensity equality.

---

### Viewing Geometry

Observations may be captured from different positions or viewing angles.

This can alter:

- apparent feature shapes
- local scale
- perspective
- surface visibility

Geometric registration must account for these differences rather than assuming that corresponding terrain appears identically in both images.

---

### Terrain Relief

The lunar surface is not a flat plane.

Craters, ridges, slopes, and elevation differences can produce local geometric distortions.

A single global transformation such as an affine transform or homography may work well for some local areas but may not perfectly represent a large or relief-heavy region.

Terrain-aware approaches belong to more advanced research directions.

---

### Repetitive Terrain

Lunar landscapes contain many visually similar structures:

- craters
- crater rims
- small rocks
- ridges
- repetitive texture

A matcher may therefore associate one crater with the wrong crater.

This is why visual similarity alone is not enough.

Geometric verification is essential.

---

### Partial Overlap

Two observations may share only part of the same physical region.

The rest of each image may contain terrain that does not exist in the other observation.

A useful registration system therefore needs to tolerate:

- incomplete shared coverage
- border overlap
- missing terrain
- different image extents

---

### Low-Feature Regions

Some lunar areas may contain too little distinctive structure for reliable registration.

In such cases, the scientifically appropriate result may be:

> **Insufficient evidence for reliable registration.**

ChandraMap should not force a transformation merely because producing an aligned image would look more complete.

---

## 4. Scientific Data Context

ChandraMap's primary documented scientific context includes Chandrayaan-2 imagery and LRO/LROC reference imagery.

Exact processing values should come from the metadata of the specific scientific product being used.

---

### Chandrayaan-2 OHRC

The **Orbiter High Resolution Camera (OHRC)** provides very high-resolution panchromatic observations of the lunar surface.

Project context commonly references approximately:

> **~0.25–0.32 m/px**

depending on the product and source documentation.

OHRC is particularly relevant to detailed local terrain correspondence.

---

### Chandrayaan-2 TMC-2

The **Terrain Mapping Camera-2 (TMC-2)** provides panchromatic lunar terrain imagery.

Project context commonly references approximately:

> **~5 m/px**

as broad instrument-level context.

Use the canonical name **TMC-2** when referring specifically to the Chandrayaan-2 instrument.

---

### Chandrayaan-2 IIRS

The **Imaging Infrared Spectrometer (IIRS)** provides hyperspectral / imaging-infrared lunar observations.

Project context commonly references spatial sampling around:

> **~80 m/px**

as broad context.

IIRS is not simply a lower-resolution camera. It records spectral information across many wavelengths.

A conventional 2D image-registration pipeline may therefore first require a derived registration representation, such as:

- a selected spectral band
- a dimensionality-reduced component
- a spectral composite
- a gradient or structural representation

The appropriate representation is a research question rather than something that should be assumed universally.

---

### LRO NAC

The **Lunar Reconnaissance Orbiter Camera Narrow Angle Camera (NAC)** provides detailed high-resolution lunar imagery.

Within ChandraMap, NAC is relevant as a detailed reference source for local registration.

Its exact product scale varies and should be taken from product metadata.

---

### LRO WAC

The **Lunar Reconnaissance Orbiter Camera Wide Angle Camera (WAC)** provides wider-area lunar coverage and context.

It may be useful for broader reference or localization tasks where wider spatial coverage is more important than NAC-level detail.

NAC and WAC should not be treated as interchangeable simply because both belong to the LRO/LROC imaging system.

For deeper data guidance, see the [Dataset Context](../../.ai/context/DATASETS.md).

---

## 5. Core Scientific Goal

The central ChandraMap problem can be expressed as:

```text
Given:

Source Observation A
captured by one lunar instrument

and

Reference Observation B
captured by another observation/instrument

Determine:

1. Are they observing the same physical region?
2. Which locations correspond?
3. Which proposed matches are geometrically consistent?
4. What transformation aligns the observations?
5. How well does the final registration perform?
6. Is the evidence strong enough to accept the result?
```

This separates ChandraMap from a simple image-stitching demonstration.

The project is interested in **evidence for alignment**, not only the visual output of alignment.

---

## 6. Core Scientific Outputs

### Candidate Correspondences

A **candidate match** is a proposed relationship between a source-image location and a reference-image location.

It is generated by a correspondence method but has not yet been accepted as geometrically reliable.

Conceptually:

```text
Source Point
      ↔
Reference Point
```

Candidate matches may contain correct and incorrect relationships.

---

### Verified Inliers

A **verified inlier** is a candidate correspondence that is consistent with the selected geometric model under the active verification process.

This distinction is fundamental:

```text
Candidate Match
      ↓
Geometric Verification
      ↓
Verified Inlier
```

A candidate match and a verified inlier are not interchangeable terms.

Verified inliers are also not automatically independent ground truth. They are correspondences consistent with the estimated geometry.

---

### Transformation

The transformation mathematically describes how source-image coordinates relate to reference-image coordinates.

Common global 2D models may include:

- affine transformation
- homography

depending on the benchmark configuration and geometric assumptions.

Transformation direction must remain explicit, for example:

```text
source → reference
```

rather than treating the transformation as an unlabeled matrix.

---

### Registered Output

Once a valid transformation has been established, the source imagery can be transformed into alignment with the reference.

This may produce:

- a registered image
- an overlay
- transformed coordinates
- a registration preview

A registered image is useful evidence and visualization.

It is not, by itself, proof that the registration is accurate.

---

### Quality Metrics

ChandraMap aims to report measurable evidence rather than relying only on visual inspection.

Relevant information may include:

- candidate-match count
- verified-inlier count
- inlier ratio
- geometric residuals
- spatial coverage
- independent check-point RMSE where valid check points exist
- ground-space error where scientifically justified
- runtime where appropriately defined
- success, rejection, or failure status

Exact metric mathematics belongs in dedicated benchmark/evaluation documentation.

---

### Failure and Rejection

A scientifically meaningful registration system must be allowed to reject unreliable results.

Potential failure conditions include:

- insufficient detectable features
- insufficient candidate matches
- geometric-verification failure
- degenerate transformation
- poor spatial coverage
- unstable geometry
- insufficient evidence for acceptance

Failure is not automatically a software defect.

Sometimes failure is the correct scientific conclusion.

---

## 7. Candidate Matches, Geometry, and RANSAC

A local matcher proposes correspondences.

Those correspondences must then be checked for geometric consistency.

Conceptually:

```text
Candidate Matches
        ↓
RANSAC / Geometric Verification
        ↓
Find a Consistent Geometric Group
        ↓
Verified Inliers
        +
Rejected Outliers
        +
Initial Transformation
```

RANSAC is therefore not the component that creates the original visual matches.

Its role is to estimate geometry robustly while identifying a subset of candidate correspondences that agree with that geometry.

This helps reduce the effect of incorrect crater-to-crater or texture-to-texture matches.

---

## 8. High-Level Processing Flow

The conceptual ChandraMap workflow is:

```text
Source Lunar Image
        +
Reference Image
        ↓
Input Validation
        ↓
Metadata / Product Context
        ↓
Registration Representation
        ↓
Scale Handling
        ↓
Reference Candidate Selection
        ↓
Local Correspondence Matching
        ↓
Candidate Matches
        ↓
Geometric Verification
        ↓
Verified Inliers
        ↓
Transform Estimation
        ↓
Registration
        ↓
Quantitative Evaluation
        ↓
Accept / Refine / Reject
```

This is intentionally a project-level overview.

The detailed processing sequence, optional branches, retrieval flow, refinement stages, and failure paths are defined in the [Processing Pipeline](../../.ai/architecture/PIPELINE.md).

---

## 9. Known-Overlap vs Unknown-Location Workflows

ChandraMap distinguishes **local registration** from **global localization/retrieval**.

### Known-Overlap Registration

In a known-overlap problem, the approximate reference region is already available.

The task becomes:

```text
Known Source
      +
Known Reference Region
        ↓
Local Correspondence
        ↓
Geometry
        ↓
Registration
```

This is the primary setting for canonical Benchmark V1.

---

### Metadata-Constrained Registration

If reliable footprint, projection, or approximate location metadata exists, it can be used to restrict the reference search.

Using valid scientific metadata is not a weakness in the method.

It prevents unnecessary search and preserves information already supplied by the scientific product.

---

### Unknown-Location Retrieval

If the location is unknown or useful spatial metadata is unavailable, a later research configuration may first search a larger reference collection.

Conceptually:

```text
Source Image
      ↓
Global Descriptor
      ↓
Vector Search
      ↓
Top-K Reference Candidates
      ↓
Local Matching
      ↓
Geometric Verification
      ↓
Registration
```

The responsibilities remain different:

- **global retrieval** determines where to search
- **local matching** proposes point correspondences
- **geometric verification** determines which correspondences agree geometrically
- **registration** applies the accepted transformation

If FAISS is used in a retrieval configuration, its role is vector indexing and similarity search. It does not extract local image features, perform local correspondence matching, run RANSAC, or register imagery.

---

## 10. How ChandraMap Measures Trustworthiness

A trustworthy result requires more than a large match count.

Several forms of evidence matter.

### Geometric Consistency

Candidate correspondences should support a plausible geometric transformation.

A large set of mutually inconsistent matches should not be treated as success.

---

### Spatial Coverage

Match distribution matters.

For example:

```text
Many inliers around one crater
```

may provide weaker overall geometric support than:

```text
Fewer well-distributed inliers
across the shared image region
```

Coverage therefore complements inlier count and inlier ratio.

---

### Residual and Error Information

Residuals help describe how closely corresponding points agree with the estimated transformation.

Every reported error should retain its coordinate context and units.

For example:

- source-image pixels
- reference-image pixels
- projected units
- metres where a valid physical conversion exists

A unitless accuracy value is scientifically ambiguous.

---

### Fit Points vs Check Points

Points used to estimate a transformation are **fit points**.

Independent points used only to evaluate the final transform are **check points**.

Conceptually:

```text
Fit Points
    ↓
Estimate Transform

Independent Check Points
    ↓
Evaluate Transform
```

Residual error on the fitting points is useful diagnostic information.

It should not automatically be described as independent registration accuracy.

---

### Sub-Pixel Does Not Mean Sub-Metre

`Sub-pixel` means an error smaller than one pixel in a defined image coordinate system.

Its physical meaning depends on:

- the image
- its GSD
- its geometry
- the coordinate frame

Therefore:

```text
sub-pixel
```

does not automatically imply:

```text
sub-metre
```

ground accuracy.

---

### Visual Inspection

Registered overlays are valuable for:

- debugging
- qualitative review
- understanding failure cases
- demonstrating alignment

But a result that looks correct may still contain geometric error.

Visualization should support quantitative evaluation, not replace it.

---

## 11. Benchmark-Driven Development

ChandraMap organizes major methodological progress into four conceptual benchmark configurations.

The purpose is not simply to make every version more complicated.

The purpose is to ask:

> **Does each added capability produce measurable improvement under controlled conditions?**

| Benchmark | Main purpose                                |
| --------- | ------------------------------------------- |
| **V1**    | Classical reproducible baseline             |
| **V2**    | Sensor- and scale-aware processing          |
| **V3**    | Advanced matching and optional retrieval    |
| **V4**    | Advanced robustness and refinement research |

> **Benchmark V1–V4 are research/pipeline configurations, not software release versions such as `v1.0.0`.**

Their exact boundaries are controlled by the corresponding benchmark specifications rather than by this overview.

---

### Benchmark V1 — Classical Baseline

V1 establishes the simplest meaningful baseline.

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

Canonical V1 intentionally does not absorb advanced later-version methods simply because they might improve performance.

Its authoritative scope is defined in the [V1 Scope](../../.ai/context/V1_SCOPE.md).

---

### Benchmark V2 — Sensor and Scale Awareness

V2 conceptually investigates improvements involving areas such as:

- sensor-aware preprocessing
- physical GSD handling
- reference pyramids
- scale-aware comparison
- structural image representations
- improved treatment of large sensor-resolution differences
- derived IIRS registration representations where appropriate

These are benchmark directions, not claims that every capability is currently implemented.

---

### Benchmark V3 — Advanced Matching and Retrieval

V3 may investigate stronger correspondence and localization techniques such as:

- ALIKED + LightGlue
- LoFTR
- remote-sensing correspondence methods
- global image descriptors
- reference tiling
- vector indexing/search
- Top-K candidate retrieval

Not every method is necessarily part of one canonical configuration.

Version-specific benchmark documentation determines what is actually evaluated.

---

### Benchmark V4 — Research-Grade Robustness

V4 represents a research stage for more difficult registration problems.

Potential areas include:

- sub-pixel tie-point refinement
- local or piecewise geometry
- terrain/DEM-aware correction
- advanced IIRS representations
- uncertainty estimation
- confidence calibration
- failure classification
- scalable retrieval

V4 should not be described as automatically best, final, or universally superior.

Its additional complexity must justify itself through evidence.

---

## 12. Why V1 Remains Simple

A useful baseline must remain understandable and stable.

If V1 already contains:

- global retrieval
- learned matching
- sophisticated sensor routing
- advanced scale search
- terrain-aware geometry
- local refinement

then there is no clean baseline against which those additions can be measured.

V1 therefore establishes a reference point.

Later versions can answer questions such as:

- Did scale-aware processing help?
- Did learned matching improve correspondence quality?
- Did retrieval find the correct region?
- Did refinement reduce final registration error?
- Did additional complexity improve robustness across difficult cases?

For direct comparison, the same controlled image pairs and evaluation definitions should be used where scientifically appropriate.

Otherwise, apparent improvement may come from changing the benchmark population rather than changing the method.

---

## 13. Core vs Downstream Capabilities

The project separates its scientific core from presentation and downstream applications.

### Scientific Core

The core problem includes:

- scientific input handling
- correspondence generation
- geometric verification
- transformation estimation
- registration
- evaluation
- quality/rejection decisions
- benchmarking and reproducibility

### Downstream Capabilities

Downstream applications may include:

- registered previews
- overlays
- match visualizations
- residual visualizations
- mosaics
- lunar-map interfaces
- dashboards
- APIs
- web applications

Conceptually:

```text
Scientific Core
      ↓
Evaluation / Benchmarking
      ↓
Scientific Result
      ↓
Optional Visualization / API / Map / Mosaic
```

The scientific engine should remain independent of how its results are presented.

A polished map interface cannot substitute for reliable correspondence and measured registration quality.

---

## 14. Project Scope

### Current Documented Core Scope

ChandraMap's documented core is centered on:

- lunar imagery
- image correspondence
- image registration
- Chandrayaan-2 and LRO/LROC scientific context
- controlled benchmarking
- scientific quality reporting
- explicit registration failure/rejection

This describes the project's scope.

It does not imply that every documented sensor, matcher, benchmark version, or research technique is currently implemented.

Implementation status must be established from the repository's actual code, tests, configuration, and benchmark evidence.

---

### Experimental and Research Areas

Project documentation describes research directions involving topics such as:

- advanced sensor-specific preprocessing
- IIRS registration representations
- multi-scale reference handling
- learned local correspondence
- remote-sensing matchers
- global retrieval
- local tie-point refinement
- terrain-aware geometry
- uncertainty and confidence research

A documented research direction should not be interpreted as implemented or supported functionality without repository evidence.

---

### Future Directions

The broader project roadmap may extend ChandraMap through:

- improved cross-sensor correspondence
- stronger unknown-location retrieval
- more robust IIRS handling
- terrain-aware registration
- local geometric correction
- larger-scale mosaicking
- richer visualization and map interfaces
- carefully justified extension to additional planetary datasets

For planned project direction, see the [Roadmap](../../ROADMAP.md).

Future planetary work should remain an extension of the lunar-registration architecture rather than distracting from the current lunar scientific core.

---

## 15. What ChandraMap Is Not

ChandraMap is not primarily:

- a Moon photo gallery
- a generic panorama stitcher
- a complete lunar GIS platform
- a finished global lunar mosaic
- an autonomous rover-navigation system
- a spacecraft mission-control system
- a generic computer-vision showcase
- a system that guarantees a registration for every image pair

Some related functionality may become downstream applications or future research areas.

The project's central scientific responsibility remains:

> **Find and verify lunar correspondences, estimate registration geometry, and measure whether the result is trustworthy.**

---

## 16. Scientific and Engineering Principles

### Baseline Before Complexity

Establish a simple reproducible baseline before adding advanced methods.

---

### Correspondence Before Visualization

The scientific evidence comes from trustworthy correspondences and geometry.

A polished overlay or mosaic is downstream.

---

### Candidate Matches Must Be Verified

A matcher proposes possibilities.

Geometric verification determines which candidate relationships are consistent with the transformation model.

---

### Physical Meaning Matters

Image dimensions, interpolation, GSD, and physical resolution are different concepts.

Digital resizing must not be presented as recovery of physical sensor detail.

---

### Illumination Changes Are More Than Brightness Changes

Changing Sun angle changes shadows and visible structure.

Photometric normalization may help, but it does not eliminate the underlying geometry of illumination.

---

### Modality Matters

Panchromatic and hyperspectral observations are not expected to have identical intensity patterns.

Sensor-specific scientific context should be preserved.

---

### Measure, Do Not Guess

Visual alignment should be supported by quantitative evidence wherever possible.

---

### Coverage Matters

A large number of correspondences concentrated in one small area may not constrain the complete overlap reliably.

---

### Failure Is Information

Rejecting an unreliable registration can reveal:

- scale limitations
- illumination sensitivity
- modality problems
- weak texture
- geometric-model limitations
- retrieval ambiguity

Failure cases help guide the next research step.

---

### Preserve Provenance

Scientific results should remain traceable, where practical, to:

```text
Data
+
Configuration
+
Code
+
Method
+
Metric Definitions
```

---

### Scientific Claims Must Match Evidence

ChandraMap should not claim universal:

- scale invariance
- Sun-angle invariance
- modality invariance
- state-of-the-art performance

without evidence supporting those claims.

Prefer evidence-bounded wording such as:

> designed to improve robustness to scale and illumination differences

or:

> evaluated on the defined benchmark population.

---

## 17. Limitations and Open Challenges

Some difficulties are inherent to the lunar-registration problem rather than defects unique to one implementation.

Important open challenges include:

- extreme GSD differences between instruments
- strong illumination and shadow changes
- cross-modal appearance differences
- repetitive crater terrain
- regions with few distinctive features
- partial overlap
- terrain relief that violates simple global geometry
- limited or imperfect independent ground truth
- ambiguous global retrieval among visually similar regions
- domain shift for methods trained primarily on terrestrial imagery

These are domain and research challenges.

Their inclusion here does not imply that every limitation has already been measured experimentally in the current ChandraMap implementation.

---

## 18. Who This Project Is For

ChandraMap may be useful to people interested in areas such as:

- computer vision
- image registration
- planetary remote sensing
- lunar imaging
- geospatial software
- scientific computing
- benchmark design
- reproducible research
- multi-sensor image analysis

Potential contributors include:

- researchers
- software engineers
- students
- remote-sensing developers
- geospatial engineers
- computer-vision developers
- open-source contributors

ChandraMap is an independent open-source research/software project. Use of data from space-agency missions does not imply endorsement, development, or maintenance of ChandraMap by those organizations.

---

## 19. What Project Success Looks Like

ChandraMap success should be evaluated conceptually through evidence such as:

- reliable candidate correspondences
- strong geometric consistency
- useful spatial distribution of verified inliers
- valid transformation estimation
- measurable registration quality
- clear units and coordinate semantics
- reproducible configuration
- transparent provenance
- explicit rejection of unreliable cases
- controlled comparison against the classical baseline
- evidence that later complexity solves real observed limitations

Success is not defined by one attractive registered image.

The stronger goal is:

```text
Reliable Correspondence
        +
Valid Geometry
        +
Measurable Evaluation
        +
Reproducibility
        +
Honest Failure Handling
```

No universal numerical accuracy or success-rate target is defined by this overview.

---

## 20. Where to Go Next

### Understand the Documentation System

Start with the human-facing [Documentation Landing Page](../index.md).

For a more detailed documentation map, use [`docs/README.md`](../README.md).

---

### Understand the Architecture

Read the [System Overview](../../.ai/architecture/SYSTEM_OVERVIEW.md).

It explains the major ChandraMap components and their responsibility boundaries.

---

### Understand the Processing Flow

Read the [Processing Pipeline](../../.ai/architecture/PIPELINE.md).

It explains detailed stage ordering, branches, retrieval, refinement, failure paths, and benchmark-version relationships.

---

### Understand Data Movement

Read the [Data Flow](../../.ai/architecture/DATA_FLOW.md).

It explains how imagery, metadata, correspondences, transformations, metrics, provenance, and results move through the system.

---

### Understand Repository Responsibilities

Read the [Module Map](../../.ai/architecture/MODULE_MAP.md).

It explains where major scientific and software responsibilities belong within the repository.

---

### Understand the Lunar Domain

Read the [Domain Context](../../.ai/context/DOMAIN_CONTEXT.md).

It provides deeper scientific background on:

- spatial scale
- illumination
- sensor modality
- geometry
- terrain relief
- lunar coordinate considerations

---

### Understand the Data

Read the [Dataset Context](../../.ai/context/DATASETS.md).

It covers:

- mission and sensor roles
- scientific product context
- metadata
- provenance
- derived data
- dataset governance

---

### Understand Project Terminology

Read the [Terminology](../../.ai/context/TERMINOLOGY.md).

It defines canonical terms such as:

- candidate match
- verified inlier
- tie point
- check point
- global retrieval
- local matching
- GSD
- transformation
- registration

---

### Understand Benchmark V1

Read the [V1 Scope](../../.ai/context/V1_SCOPE.md).

It is the authoritative scope definition for the classical baseline.

For implementation work, continue with the [V1 Implementation Task](../../.ai/tasks/V1_IMPLEMENTATION.md).

---

### Understand Benchmark Methodology

Read the [Benchmark Rules](../../.ai/development/BENCHMARK_RULES.md).

It covers:

- controlled comparison
- benchmark populations
- ground truth
- data leakage
- failure visibility
- aggregation
- runtime fairness
- reproducibility
- negative results

---

### Contribute

Read [CONTRIBUTING.md](../../CONTRIBUTING.md) for contribution workflow.

For deeper implementation guidance, consult:

- [Coding Rules](../../.ai/development/CODING_RULES.md)
- [Testing Rules](../../.ai/development/TESTING_RULES.md)
- [Documentation Rules](../../.ai/development/DOCUMENTATION_RULES.md)

---

### Explore Future Direction

Read the [Roadmap](../../ROADMAP.md) for planned research and engineering evolution.

Roadmap items describe future direction and should not be interpreted as current implementation merely because they are documented.

<!-- Source specification: :contentReference[oaicite:0]{index=0} -->
