# ChandraMap Project Context

ChandraMap is an open-source research and engineering project for **lunar image correspondence and registration**. Its primary objective is to identify the same physical lunar terrain across images captured by different sensors or under different imaging conditions, geometrically align those observations, and report measurable evidence of registration quality.

ChandraMap originated from the problem context of **SIH 26166 — multi-modal, Sun-angle and scale invariant image correspondence using Chandrayaan-2 optical/scientific imagery**. The project is now intended to evolve beyond a hackathon prototype into a reproducible research and software system for lunar correspondence, registration, evaluation, and benchmarking.

This document provides high-level project truth for AI coding agents and contributors. It explains **what ChandraMap is and what the system is trying to accomplish**. Detailed engineering rules, architecture, dataset specifications, benchmark definitions, terminology, and implementation guidance belong in their respective documentation.

---

## 1. Project Identity

**Project:** ChandraMap
**Repository type:** Open-source personal/research/portfolio software project

Primary technical domains include:

- lunar image correspondence
- lunar image registration
- multi-sensor image matching
- geospatial localization
- computer vision
- remote sensing
- scientific computing
- AI/ML
- benchmarking
- research engineering
- backend engineering
- MLOps
- lunar mapping

ChandraMap should not be understood merely as a Moon map or visualization application.

Its core scientific problem is **correspondence and registration**.

---

## 2. Project Mission

ChandraMap is designed to determine whether observations from different lunar imaging systems represent the same physical terrain, establish reliable point correspondences between them, estimate an appropriate geometric relationship, and evaluate the resulting registration quantitatively.

The system is intended to handle differences such as:

- spatial resolution
- ground sampling distance
- image scale
- Sun angle
- illumination
- shadow geometry
- viewing geometry
- sensor modality
- image quality
- terrain relief
- partial overlap
- feature density

The project's engineering and research philosophy is:

> Build a sensor-aware, scale-aware, geometry-verified lunar correspondence system beginning with a simple reproducible baseline, introduce advanced methods only when they address demonstrated weaknesses, and report both success and failure using measurable evidence.

This is a project direction and research philosophy, not a claim that all of these goals have already been achieved.

---

## 3. Core Scientific Problem

The fundamental question is:

> Given two or more observations that may show the same lunar region but can look substantially different, can ChandraMap determine which image locations correspond to the same physical terrain features and register the observations accurately?

A successful system must go beyond making two images appear visually similar.

It should establish evidence such as:

- reliable correspondences
- geometric consistency
- spatial distribution of matches
- transformation quality
- independent error measurements where possible
- explicit rejection of unreliable results

The distinction matters because visually plausible alignment can still be geometrically or scientifically incorrect.

---

## 4. Why the Problem Is Difficult

### 4.1 Large Resolution Differences

The same lunar region can be represented at substantially different physical scales.

Conceptually:

```text
OHRC
≈ sub-metre-scale imagery

TMC-2
≈ several metres/pixel

IIRS
≈ tens of metres/pixel

Reference imagery
≈ depends on product and instrument
```

Matching cannot be solved simply by resizing every image to the same pixel dimensions.

Two images may have equal array dimensions while representing completely different physical levels of detail.

ChandraMap should compare information at meaningful effective ground scales.

---

### 4.2 Sun-Angle and Shadow Differences

Lunar appearance changes significantly with solar illumination geometry.

Different Sun angles may alter:

- shadow direction
- shadow length
- crater appearance
- illuminated slopes
- ridge contrast
- local intensity patterns

Brightness normalization may reduce some radiometric differences, but it cannot reconstruct or relocate shadows created by different illumination geometry.

---

### 4.3 Sensor Modality Differences

Different instruments do not necessarily measure the same physical signal.

For example:

```text
Panchromatic optical imagery
```

and:

```text
Hyperspectral / imaging-infrared measurements
```

are different representations of lunar terrain.

Cross-sensor correspondence may therefore require sensor-specific preparation before a common matching representation becomes meaningful.

---

### 4.4 Viewing Geometry

Images may be acquired under different spacecraft viewing conditions.

Terrain relief can produce local geometric effects that are not always explained perfectly by a single simple transform.

This becomes particularly important when working with:

- relief-rich regions
- stronger viewing-angle differences
- products that are not already well map-projected or orthorectified

---

### 4.5 Repetitive Lunar Features

Lunar imagery contains many visually similar geological structures.

Repeated crater shapes and terrain patterns can produce false correspondences.

A local feature matcher can find visually plausible matches that are geographically incorrect.

Geometric verification is therefore essential.

---

### 4.6 Low-Feature Terrain

Some regions may contain:

- weak texture
- smooth terrain
- repetitive structure
- limited distinctive features
- insufficient stable keypoints

Such cases should be treated as legitimate failure or stress cases rather than hidden from evaluation.

---

### 4.7 Partial Overlap

Source and reference observations may overlap only partially.

The system must not assume that:

- every source pixel has a reference counterpart
- the entire image should participate in one transform
- a global match necessarily means complete overlap

---

## 5. Primary Inputs

ChandraMap may work with multiple Chandrayaan-2 scientific instruments. Their physical differences must remain explicit.

### 5.1 Chandrayaan-2 OHRC

**OHRC — Orbiter High Resolution Camera**

General characteristics:

- visible panchromatic imagery
- very high spatial resolution
- approximately `0.25–0.32 m/pixel` depending on official product/documentation
- suitable for detailed terrain correspondence

OHRC can contain substantially finer terrain detail than other project sensors.

Actual product metadata should remain authoritative for a particular observation.

Do not assume every OHRC product has an identical ground scale or processing level.

---

### 5.2 Chandrayaan-2 TMC-2

**TMC-2 — Terrain Mapping Camera-2**

General characteristics:

- panchromatic terrain imagery
- approximately `5 m/pixel`
- useful for terrain-scale structural correspondence
- associated with terrain/stereo mapping capabilities

Use **TMC-2** when referring to the Chandrayaan-2 instrument.

Do not casually shorten the project terminology to `TMC` where the specific instrument name is intended.

---

### 5.3 Chandrayaan-2 IIRS

**IIRS — Imaging Infrared Spectrometer**

General characteristics:

- hyperspectral / imaging-infrared data
- significantly coarser spatial resolution than OHRC or TMC-2
- approximately `80 m/pixel` spatial resolution
- measurements distributed across many spectral bands

IIRS must not be conceptualized merely as a low-resolution grayscale camera.

A conventional 2D correspondence pipeline may require conversion into a registration-friendly representation such as:

- selected spectral band
- PCA-derived representation
- spectral composite
- gradient representation
- edge representation
- other structure-oriented representation

No representation should be described as the proven default unless repository experiments support that conclusion.

---

## 6. Reference Data

### 6.1 LRO NAC

**LRO NAC — Lunar Reconnaissance Orbiter Narrow Angle Camera**

Potential role:

- detailed lunar reference imagery
- high-resolution local reference registration
- overlap validation
- scientific comparison

NAC spatial resolution varies with observation and product geometry.

Do not assume a single universal NAC GSD.

---

### 6.2 LRO WAC

**LRO WAC — Lunar Reconnaissance Orbiter Wide Angle Camera**

Potential role:

- broader lunar coverage
- coarse geographic context
- larger-area reference imagery
- possible candidate-region search
- coarse localization

NAC and WAC should not be treated as interchangeable products.

Their roles differ because of their spatial coverage and resolution characteristics.

---

## 7. Optional and Future Research Data

Potential future research may involve:

- Kaguya / SELENE Terrain Camera products
- lunar DEM / DTM products
- LOLA-derived elevation data
- synthetic lunar augmentations
- additional lunar imaging missions
- additional terrain products
- other planetary imagery

These are possible research directions.

They must not automatically be described as currently supported.

Use implementation-status language accurately:

| Status                          | Meaning                                    |
| ------------------------------- | ------------------------------------------ |
| **Implemented**                 | Exists in current project implementation   |
| **Experimental**                | Exists as research/prototype functionality |
| **Planned**                     | Intended future work                       |
| **Proposed**                    | Idea under consideration                   |
| **Optional research direction** | Possible future investigation              |

Repository implementation and tests determine actual support.

---

## 8. Core Outputs

A ChandraMap result should contain more scientific information than simply:

> registered image

Depending on the pipeline and benchmark configuration, meaningful outputs may include:

- candidate correspondences
- geometrically verified inliers
- rejected outliers
- tie points
- source/reference point mappings
- transformation model
- transformation parameters
- registered image
- registration overlay or preview
- geospatial localization where meaningful
- residual information
- evaluation metrics
- spatial coverage measurements
- runtime information
- quality/confidence information
- explicit rejection information
- explicit failure information

The exact result schema belongs in architecture or contract documentation.

---

## 9. What Counts as a Strong Registration

A strong registration result should ideally provide evidence beyond:

> The images look aligned.

Useful evidence may include:

- verified correspondences
- well-distributed tie points
- an explicit transformation model
- residual measurements
- independent check-point error where possible
- clear coordinate conventions
- clear units
- spatial coverage
- registration quality state
- failure or rejection reasons when applicable

A smooth warp or overlay is not sufficient evidence if its control points are unreliable.

---

## 10. Core Metrics

Metrics may differ by pipeline stage.

Potential registration and correspondence metrics include:

- candidate match count
- inlier count
- inlier ratio
- reprojection residuals
- source-image pixel error
- independent check-point RMSE
- occupied grid cells
- grid coverage
- convex-hull coverage
- registration success rate
- failure rate
- runtime

When global retrieval is part of the evaluated system, metrics may additionally include:

- Recall@1
- Recall@5
- Top-K candidate success

### Ground Error

Ground error in metres should only be reported when the required spatial information supports that conversion, including where relevant:

- known GSD
- meaningful projection
- appropriate coordinate transformation
- valid reference/ground truth

Do not equate:

```text
sub-pixel image error
```

with:

```text
sub-metre ground error
```

They are different claims.

---

## 11. Primary Scientific Deliverable vs Downstream Applications

This distinction is fundamental.

### Primary Scientific Deliverable

```text
Reliable Correspondence
        +
Geometric Registration
        +
Measured Quality
```

### Downstream Demonstrations

Potential downstream applications include:

- lunar mosaic generation
- interactive Moon maps
- registered footprint displays
- scientific dashboards
- 3D lunar visualization
- stitched map layers
- other derived mapping products

A visually attractive mosaic must not hide weak correspondence or registration quality.

The scientific core should remain valid independently of the visualization layer.

---

## 12. High-Level System Concept

The long-term conceptual ChandraMap workflow may resemble:

```text
Source Lunar Image
        +
Reference Lunar Dataset
        ↓
Input Validation
        ↓
Metadata Extraction
        ↓
Sensor Identification
        ↓
Sensor-Aware Preparation
        ↓
Projection / Coordinate Handling
        ↓
Multi-Scale Representation
        ↓
Metadata-Constrained Search
        OR
Image-Based Global Retrieval
        ↓
Candidate Region(s)
        ↓
Local Matching
        ↓
Candidate Correspondences
        ↓
Geometric Verification
        ↓
Verified Inliers
        ↓
Spatial Coverage + Residual Analysis
        ↓
Sub-Pixel Tie-Point Refinement
        ↓
Final Transform Refit
        ↓
Registration
        ↓
Independent Quality Evaluation
        ↓
Accept / Refine / Reject
        ↓
Registration Result
        ↓
Optional Visualization / Mosaic
```

This represents a **target conceptual system**.

It is not evidence that every stage is currently implemented.

Consult current code, tests, configuration, architecture documentation, and benchmark specifications before making implementation claims.

---

## 13. Metadata and Search Strategy

### 13.1 Metadata-First Design

When reliable metadata exists, ChandraMap should use it.

Potentially useful metadata may include:

- approximate latitude/longitude
- image footprint
- map projection
- GSD
- sensor/instrument
- product identifier
- acquisition information
- viewing geometry
- illumination geometry

Using known location or footprint metadata to reduce the candidate search area is valid engineering.

It is not cheating.

If metadata already constrains the overlap sufficiently, performing whole-Moon visual retrieval may add unnecessary complexity.

---

### 13.2 Image-Only Retrieval

When geospatial metadata is unavailable, incomplete, or intentionally excluded from an experiment, image-based candidate retrieval may be useful.

Conceptually:

```text
Source Image
      ↓
Global Descriptor
      ↓
Reference Index Search
      ↓
Top-K Candidate Regions
      ↓
Local Registration
```

Image retrieval is therefore a possible localization mechanism, not necessarily a mandatory stage for every registration task.

---

## 14. Global Retrieval vs Local Matching

These are separate technical problems.

### Global Retrieval

Question:

> Which lunar region is most likely to contain the source observation?

Possible components:

```text
Source Image
      ↓
Global Descriptor
      ↓
Similarity Search
      ↓
Top-K Candidate Tiles
```

### Local Matching

Question:

> Which precise locations correspond between the source image and a selected candidate reference image?

Possible components:

```text
Source / Candidate Pair
        ↓
Local Feature or Detector-Free Matching
        ↓
Candidate Correspondences
        ↓
Geometric Verification
```

Different feature representations may be appropriate for these two stages.

Do not collapse them conceptually.

---

## 15. FAISS Context

If FAISS is introduced or used, treat it as a **vector similarity-search/indexing system**.

FAISS does not itself produce an image descriptor.

A conceptual offline reference workflow may be:

```text
Reference Imagery
      ↓
Tile Generation
      ↓
Multi-Scale Representation
      ↓
Global Descriptor Extraction
      ↓
FAISS Index
      +
Tile Metadata
```

A conceptual query workflow may be:

```text
Source Image
      ↓
Compatible Global Descriptor
      ↓
FAISS Search
      ↓
Top-K Reference Candidates
```

Local correspondence remains a separate stage.

---

## 16. Local Correspondence Methods

Potential correspondence methods may include multiple algorithm families.

### Classical Methods

Possible approaches include:

- SIFT
- RootSIFT
- ORB where justified

### Learned Methods

Potential experimental approaches include:

- ALIKED + LightGlue
- LoFTR

### Multimodal / Remote-Sensing Research

Possible research directions include:

- RIFT-inspired approaches
- CFOG-inspired approaches
- other multimodal remote-sensing methods

These names represent possible benchmark or research candidates.

Do not assume every algorithm is simultaneously part of the active pipeline.

Methods should be selected based on controlled evaluation rather than popularity.

---

## 17. Baseline-First Principle

ChandraMap should preserve a simple, understandable baseline.

A conceptual classical baseline is:

```text
Input Pair
    ↓
Basic Preprocessing
    ↓
SIFT / RootSIFT
    ↓
Descriptor Matching
    ↓
Match Filtering
    ↓
RANSAC
    ↓
Affine / Homography
    ↓
Registration
    ↓
Evaluation
```

The baseline provides a controlled point of comparison.

An advanced method should be retained because it improves a measured weakness or provides another clearly justified benefit.

Complexity alone is not progress.

---

## 18. Local Correspondence and Geometry

### 18.1 Candidate Matches

Matcher output should initially be treated as:

> candidate correspondences

Matcher confidence is useful but does not establish geometric correctness.

---

### 18.2 Geometric Verification

Conceptually:

```text
Candidate Matches
        ↓
RANSAC / Initial Geometric Model
        ↓
Verified Inliers
```

Only after geometric verification should candidate matches be treated as geometrically verified correspondences.

---

### 18.3 Sub-Pixel Refinement

Where appropriate:

```text
Verified Inliers
      ↓
Local Tie-Point Refinement
      ↓
Refined Coordinates
      ↓
Final Transform Refit
```

Do not conceptually perform sub-pixel refinement on arbitrary candidate matches before identifying reliable geometry unless a specific researched method intentionally requires a different design.

---

### 18.4 Transformation Models

Initial or local registration may use models such as:

- affine transformation
- homography

depending on the product geometry and experiment.

However:

> The Moon is not a flat poster.

One global transformation may not fully explain:

- terrain relief
- viewpoint differences
- sensor geometry
- projection differences
- spatially varying residuals

Possible future research may therefore include:

- local refinement
- piecewise transformations
- terrain-aware warping
- DEM-assisted registration

These should not automatically be treated as currently implemented or required.

---

## 19. Physical Scale Principle

One of ChandraMap's central scientific rules is:

> **Resizing is not detail recovery.**

Upsampling a coarse observation does not create missing physical terrain information.

Conceptually:

```text
Coarse Source
80 m/px
    ↓
Upsample
    ↓
More array pixels

NOT

More physical ground detail
```

A more defensible cross-resolution strategy is:

```text
High-Resolution Reference
        ↓
Reference Pyramid / Downsampling
        ↓
Comparable Effective Ground Scale
        ↓
Coarse Correspondence
        ↓
Refinement only where source information supports it
```

Scale handling should be based on information content and effective ground scale rather than array dimensions alone.

---

## 20. Illumination Principle

Lunar illumination differences involve more than brightness.

Changing solar geometry can alter the apparent location and shape of shadows.

Potentially stable structural clues may include:

- crater rims
- ridges
- edges
- gradients
- phase-related information
- relative feature geometry

Such representations should be treated as hypotheses or experiment choices until benchmark evidence demonstrates their usefulness.

Do not claim illumination invariance simply because contrast normalization is present.

---

## 21. Fit Points vs Check Points

These serve different purposes.

### Fit Points

Used to estimate the transformation.

```text
Fit Points
    ↓
Transformation Estimation
```

### Check Points

Held outside transformation fitting and used to evaluate the resulting registration.

```text
Final Transformation
        +
Independent Check Points
        ↓
Registration Accuracy
```

When reliable independent evaluation is available, check-point measurements provide stronger evidence of general registration accuracy than reporting only fitting residuals.

---

## 22. Spatial Coverage

Registration quality depends on where correspondences occur, not only how many exist.

For example, a project benchmark may evaluate coverage using a conceptual grid:

```text
+----+----+----+----+
|    | ●  |    | ●  |
+----+----+----+----+
| ●  |    | ●  |    |
+----+----+----+----+
|    | ●  |    | ●  |
+----+----+----+----+
| ●  |    | ●  |    |
+----+----+----+----+
```

Possible spatial metrics include:

- occupied grid cells
- grid coverage
- convex-hull coverage
- normalized area coverage

A large number of inliers concentrated around one crater may still provide weak support for registration across the entire overlap.

---

## 23. Failure Is a Valid Result

ChandraMap should not force registration when the evidence is insufficient.

Possible rejection reasons may include:

- insufficient candidate matches
- insufficient verified inliers
- poor spatial coverage
- excessive residual error
- unstable transformation
- degenerate geometry
- no meaningful overlap
- unsupported input
- unsupported sensor
- insufficient source resolution
- ambiguous candidate retrieval

Conceptually:

```text
Registration Evidence
        ↓
Quality Evaluation
        ↓
┌────────┬──────────┬────────┐
│ Accept │ Refine   │ Reject │
└────────┴──────────┴────────┘
```

An explicit rejection is preferable to an apparently confident but incorrect registration.

---

## 24. Benchmark Architecture

ChandraMap is designed around four conceptual research/benchmark configurations.

These exist to make progress measurable.

They are not automatically software releases.

```text
Benchmark V1 ≠ software v1.0.0
Benchmark V2 ≠ software v2.0.0
Benchmark V3 ≠ software v3.0.0
Benchmark V4 ≠ software v4.0.0
```

Software releases may follow a separate versioning lifecycle.

The exact benchmark specifications belong in dedicated benchmark documentation.

---

### 24.1 Benchmark V1 — Classical Baseline

**Purpose:** establish a simple, reproducible registration baseline.

Conceptually, V1 may include:

- known overlapping image pair
- basic preprocessing
- SIFT / RootSIFT
- descriptor matching
- match filtering
- RANSAC
- affine/homography
- registered output
- evaluation metrics

Primary research value:

> provide a stable baseline against which later approaches can be measured

V1 should remain intentionally simple.

Do not turn it into an advanced learned pipeline merely to improve its numbers.

---

### 24.2 Benchmark V2 — Sensor-Aware + Multi-Scale

**Purpose:** address physical sensor differences and large scale mismatch.

Potential research additions include:

- sensor routing
- OHRC-specific preparation
- TMC-2-specific preparation
- IIRS-derived 2D representations
- reference pyramids
- effective-GSD comparison
- structure-oriented preprocessing
- illumination stress testing
- improved failure handling
- spatial coverage evaluation

Core research question:

> Does physically informed sensor preparation and scale handling improve correspondence and registration over the classical baseline?

---

### 24.3 Benchmark V3 — Advanced Matching + Retrieval

**Purpose:** evaluate stronger matching techniques and optional large-area candidate retrieval.

Potential local matching research may include:

- ALIKED + LightGlue
- LoFTR
- multimodal remote-sensing approaches

Potential retrieval research may include:

- reference tiling
- multi-resolution reference pyramids
- global descriptor extraction
- vector indexing
- FAISS-based similarity search
- Top-K candidate selection
- metadata indexing

Possible research questions include:

- Do learned matchers outperform earlier baselines on hard cases?
- Does candidate retrieval recover the correct region reliably?
- Which approaches are most robust to scale, illumination, or modality stress?

These are research questions, not assumed conclusions.

---

### 24.4 Benchmark V4 — Research-Grade Robustness

**Purpose:** address weaknesses exposed by earlier benchmarks using justified research extensions.

Potential areas include:

- improved IIRS representations
- DEM-aware geometry
- local/piecewise refinement
- uncertainty estimation
- confidence calibration
- quality gates
- automatic accept/refine/reject logic
- matcher selection
- failure classification
- scalable retrieval
- reproducible experiment orchestration

V4 does not mean:

> add every advanced algorithm available

Every major addition should address a demonstrated weakness or defined research question.

---

## 25. Benchmark Comparability

Benchmark comparisons should control variables wherever scientifically appropriate.

Relevant context may include:

- image pairs
- dataset version
- preprocessing
- scale strategy
- matcher
- geometry model
- thresholds
- metric definitions
- model/checkpoint
- random seed
- hardware when runtime matters

The ideal comparison changes only the factor under investigation.

Do not treat results as directly comparable when multiple uncontrolled aspects of the experiment changed.

---

## 26. Stress-Test Philosophy

ChandraMap should include difficult cases instead of evaluating only convenient examples.

Potential benchmark categories include:

| Stress Case             | Purpose                                                                                        |
| ----------------------- | ---------------------------------------------------------------------------------------------- |
| **Easy Pair**           | Verify the end-to-end pipeline on a known overlap                                              |
| **Sun-Angle Stress**    | Measure sensitivity to illumination/shadow differences                                         |
| **Scale Stress**        | Test large GSD/resolution differences                                                          |
| **Modality Stress**     | Test cross-modal correspondence such as an IIRS-derived representation against visible imagery |
| **Geometry Stress**     | Test relief-rich terrain or stronger viewing differences                                       |
| **Low-Feature Terrain** | Test regions with weak or repetitive structure                                                 |
| **Retrieval Stress**    | Test localization where the overlap is not already known                                       |

These categories describe useful evaluation concepts.

They do not imply that benchmark datasets or results for every category already exist.

---

## 27. Core vs Supporting vs Downstream Systems

Architecture decisions should preserve the distinction between the scientific core and supporting products.

### Core Scientific System

Potential responsibilities include:

- sensor-aware preprocessing
- scale handling
- candidate search where needed
- correspondence
- geometric verification
- tie-point refinement
- transformation estimation
- registration
- evaluation
- rejection/quality logic

### Supporting Engineering Systems

Potential responsibilities include:

- configuration
- CLI
- backend services
- experiment execution
- artifact management
- CI
- testing infrastructure
- reproducibility tooling

### Downstream Presentation Systems

Potential applications include:

- dashboard
- lunar map
- visualization
- mosaic
- 3D demonstration

The scientific system should remain conceptually independent from its presentation interface.

---

## 28. Backend Context

Backend functionality may eventually support operations such as:

- image ingestion
- registration jobs
- candidate search
- benchmark execution
- metric access
- artifact retrieval

These are supporting engineering capabilities.

The existence of backend infrastructure does not redefine the scientific goal of ChandraMap.

Do not assume specific:

- API routes
- services
- job systems
- databases
- deployment environments

without repository evidence.

---

## 29. Frontend and Visualization Context

Useful scientific visualization may eventually display:

- source image
- reference image
- candidate matches
- verified inliers
- rejected correspondences
- registration overlays
- residual vectors
- benchmark comparisons
- candidate locations
- quality metrics
- explicit rejection/failure state

Visualization should represent actual scientific state.

Do not display decorative percentages, confidence values, or benchmark metrics in a way that implies they are measured experimental results.

---

## 30. Mosaic Context

Mosaic generation is a downstream application of reliable registration.

Potential future mosaic-related work may involve:

- image stitching
- overlap management
- warping
- blending
- seam handling
- map-layer generation

A smooth mosaic does not prove that the underlying correspondences are correct.

Registration quality must remain measurable independently.

---

## 31. Research vs Stable Functionality

ChandraMap should maintain a conceptual distinction between:

```text
Core / Stable Functionality
```

and:

```text
Research / Experimental Functionality
```

Research may explore:

- new local descriptors
- learned matchers
- alternative IIRS representations
- lunar-specific learned features
- illumination handling
- piecewise warping
- DEM-aware registration
- new retrieval methods

A method performing well on one image pair does not automatically justify making it the default pipeline.

Promotion toward stable functionality should be supported by broader evidence.

---

## 32. Reproducibility

Reproducibility is part of project quality.

Research and benchmark outputs should increasingly be traceable through information such as:

- explicit configuration
- benchmark pair manifests
- dataset provenance
- random seeds where relevant
- model/checkpoint identity
- dependency/environment information
- output metadata
- controlled metric definitions
- code revision where available

A benchmark result without sufficient experimental context is difficult to interpret or reproduce.

---

## 33. Data Provenance

Where available and scientifically relevant, ChandraMap should preserve metadata such as:

- mission
- instrument
- product identifier
- product type
- product level
- data source
- GSD
- image dimensions
- projection
- footprint
- acquisition information
- illumination information
- viewing geometry

These attributes can materially affect interpretation of a registration result.

Data provenance should not be discarded merely because the numerical image array is sufficient to run an algorithm.

---

## 34. Project Scope

### 34.1 In Scope

High-level project scope may include:

- lunar image correspondence
- lunar image registration
- sensor-aware preprocessing
- scale-aware comparison
- metadata-based localization
- optional visual retrieval
- local matching
- geometric verification
- sub-pixel tie-point refinement
- transformation estimation
- independent quality evaluation
- spatial coverage evaluation
- failure detection
- benchmarking
- reproducibility
- backend/API support
- scientific visualization
- downstream mosaic demonstrations

Actual implemented scope must be verified from the repository.

---

### 34.2 Not the Current Core

The following should not replace the primary scientific objective:

- building a general-purpose Google Maps clone for the Moon
- photorealistic lunar rendering
- supporting every planetary mission immediately
- prioritizing global mosaic production before local registration is validated
- training large neural networks without benchmark evidence
- adding algorithms only because they are popular
- maximizing frontend complexity before the scientific pipeline is trustworthy

These may be interesting applications or research directions, but they are not the defining problem.

---

### 34.3 Long-Term Research

Long-term work may eventually explore adaptation to:

- Mars
- Venus
- additional lunar missions
- other planetary imagery

These are extensions.

The lunar registration problem should be validated first.

Do not describe non-lunar planetary support as currently implemented unless current code and tests demonstrate it.

---

## 35. Project Development Philosophy

A sensible conceptual development sequence is:

1. understand the scientific data
2. establish a simple measurable baseline
3. establish benchmark infrastructure
4. handle physical scale correctly
5. add sensor-specific preparation
6. measure illumination and modality stress
7. evaluate stronger matchers
8. add broad-area retrieval when it solves a real requirement
9. add sub-pixel/local refinement after global geometry is stable
10. build supporting backend/UI around validated scientific behavior
11. build mosaic and mapping demonstrations on top of trustworthy registration
12. move toward stable open-source releases as interfaces and reproducibility mature

This is a research/engineering progression.

It is not a fixed release schedule.

---

## 36. What Long-Term Project Success Means

Long-term ChandraMap success should mean the system can reliably:

1. accept supported lunar imagery
2. identify or validate relevant sensor/metadata information
3. determine or search for candidate overlap
4. generate candidate correspondences
5. reject geometrically inconsistent matches
6. maintain useful spatial correspondence coverage
7. refine reliable tie points where appropriate
8. estimate an appropriate transformation
9. register the imagery
10. evaluate registration accuracy independently where possible
11. detect weak or failed registrations
12. reproduce results from documented configuration
13. compare improvements against controlled baselines

This describes a target capability set, not a statement that every item is already complete.

---

## 37. Scientific Integrity

ChandraMap should prioritize measured evidence over presentation.

Do not:

- invent accuracy
- invent benchmark results
- display fabricated confidence values
- hide failed examples
- claim invariance without testing
- equate more matches with better registration
- treat upsampling as information recovery
- present fitting residuals as independent validation
- describe planned functionality as completed
- treat visual alignment alone as proof

The project should make it possible to understand:

```text
What worked?
Where did it fail?
What changed?
Did the change measurably help?
```

---

## 38. Implementation Status Language

Use status language carefully.

### Current / Implemented

Use when repository implementation supports the statement.

Example:

> The current implementation provides X.

Only use this wording after inspecting the code or other direct repository evidence.

### Experimental

Use for research/prototype functionality.

Example:

> An experimental pipeline evaluates X.

### Planned

Use when a documented future capability has not yet been implemented.

Example:

> V3 is intended to evaluate advanced local matching and optional retrieval.

### Proposed

Use for ideas that remain under consideration.

Example:

> DEM-aware local refinement is a proposed research direction.

Do not convert a roadmap item into a current capability.

---

## 39. Roadmap Is Not Implementation Evidence

`ROADMAP.md` represents planned direction.

For example:

```text
ROADMAP:
Add end-to-end IIRS registration
```

does not prove:

```text
CURRENT SYSTEM:
IIRS registration is fully supported
```

Agents must inspect:

- current implementation
- tests
- configuration
- benchmark specifications
- current technical documentation

before describing feature status.

---

## 40. Repository Structure Context

The repository may contain areas conceptually responsible for:

- applications
- benchmarks
- configuration
- contracts
- data handling
- documentation
- experiments
- notebooks
- research
- results
- scripts
- services
- reusable source code
- tests
- deployment/support tooling

Do not assume exact directory names or current existence from this conceptual description.

Repository structure must be inspected directly when file placement or module ownership matters.

---

## 41. Related AI Context

`PROJECT_CONTEXT.md` answers:

> **What project am I modifying?**

It should not absorb every specialized concern.

Where present, deeper AI context should own more specific responsibilities.

| Context                    | Primary Responsibility                         |
| -------------------------- | ---------------------------------------------- |
| `AGENTS.md`                | Repository-wide AI-agent rules                 |
| `.ai/README.md`            | AI context navigation                          |
| `.ai/ENGINEERING_RULES.md` | How engineering work should be performed       |
| `DOMAIN_CONTEXT.md`        | Lunar and remote-sensing scientific background |
| `TERMINOLOGY.md`           | Canonical terminology                          |
| `DATASETS.md`              | Sensor, dataset, and product context           |
| `SYSTEM_OVERVIEW.md`       | Software/system architecture                   |
| `PIPELINE.md`              | Detailed scientific processing sequence        |
| `MODULE_MAP.md`            | Repository/module responsibility mapping       |
| `DATA_FLOW.md`             | Data and object movement through the system    |
| Benchmark documentation    | Exact benchmark definitions                    |
| Metrics documentation      | Exact metric definitions                       |
| Research documentation     | Experiment and reproducibility rules           |

Only rely on paths that actually exist in the current repository.

---

## 42. Source of Truth

This document provides high-level project context.

It does not override more specific current evidence.

Detailed project truth may come from:

- current implementation
- tests
- configuration
- benchmark specifications
- architecture documentation
- dataset documentation
- metric definitions
- current repository policies

If this document conflicts materially with more specific current repository evidence, investigate the inconsistency.

Possible explanations include:

- this document is stale
- another document is stale
- implementation changed without documentation
- implementation is incomplete
- documentation describes intended rather than current behavior
- code contains a regression

Do not silently choose whichever source is more convenient.

---

## 43. Facts That Must Not Be Invented

Do not invent:

- implementation status
- benchmark completion status
- benchmark scores
- accuracy percentages
- RMSE values
- success rates
- dataset counts
- test counts
- model performance
- software release versions
- API endpoints
- databases
- cloud architecture
- production deployments
- CI behavior
- team members
- publications
- model checkpoints
- supported platforms

Unknown facts should remain unknown until repository or authoritative evidence establishes them.

---

## 44. Key Facts for Agents

Keep the following project context in mind when performing significant ChandraMap work:

- ChandraMap is a **lunar correspondence and registration project first**.
- Mosaics, globes, and map UIs are downstream applications.
- OHRC, TMC-2, and IIRS are physically different sensors.
- IIRS is hyperspectral/imaging-infrared data, not simply a low-resolution grayscale camera.
- Use **TMC-2** when referring to the Chandrayaan-2 instrument.
- LRO NAC and LRO WAC serve different reference roles.
- Upsampling does not recover missing spatial information.
- Cross-resolution processing should compare physically meaningful information.
- Different lunar Sun angles alter shadow geometry, not only brightness.
- Global retrieval and local matching are separate tasks.
- FAISS is a vector search/indexing component, not an image feature extractor.
- Candidate matches are not verified correspondences.
- Geometric verification should establish verified inliers.
- The normal conceptual sequence is RANSAC → verified inliers → sub-pixel refinement → final transform refit.
- Fit-point residuals and independent check-point error are different measurements.
- Match spatial distribution matters, not only match count.
- Registration failure or rejection is a valid result.
- Benchmark V1–V4 are research configurations, not software release versions.
- V1 should remain an intentionally simple classical baseline.
- V2 focuses on sensor-aware and multi-scale improvements.
- V3 is intended for advanced matching and optional retrieval research.
- V4 is intended for justified research-grade robustness improvements.
- Advanced methods should earn their place through controlled evidence.
- Research prototypes should not automatically become stable defaults.
- Reproducibility and provenance are core project-quality goals.
- A visually convincing overlay does not prove correct registration.
- Planned functionality must never be presented as already implemented.
