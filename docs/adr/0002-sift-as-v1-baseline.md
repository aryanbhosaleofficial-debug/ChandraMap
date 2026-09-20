# ADR-0002: SIFT as the V1 Local-Matching Baseline

- **ADR:** ADR-0002
- **Title:** SIFT as the V1 Local-Matching Baseline
- **Status:** Accepted
- **Date:** `<YYYY-MM-DD>`
- **Decision Scope:** V1
- **Architecture Area:** Local Correspondence / Feature Matching
- **Sensor Scope:** OHRC, TMC-2, IIRS-derived 2D representations, LRO reference imagery
- **Supersedes:** N/A
- **Superseded By:** N/A
- **Related ADRs:** ADR-0001 — V1 Known-Overlap First
- **Related Issues:** N/A
- **Related PRs:** N/A
- **Related Experiments:** TBD
- **Related Benchmarks:** V1 local-matching benchmark / TBD
- **Measured Evidence Status:** Validation pending; no benchmark result is asserted by this ADR

> **Decision:** ChandraMap V1 will use SIFT-based local feature matching as its primary reproducible classical baseline for known-overlap lunar image registration.

SIFT is selected as the **baseline**, not as the assumed final matcher for ChandraMap.

The purpose of this decision is to establish a mature, interpretable, training-free, reproducible local-correspondence path against which later sensor-aware, learned, multimodal, multi-scale, and refinement methods can be evaluated under controlled conditions.

---

## Status

**Accepted**

`Accepted` means that the architectural role of SIFT as the canonical V1 local-matching baseline has been selected.

It does **not** mean that:

- the V1 implementation is complete;
- V1 benchmark results already exist;
- a particular RMSE has been achieved;
- SIFT has been proven sufficient for every lunar sensor;
- SIFT is the final operational matcher;
- SIFT has been proven superior to learned methods;
- all SIFT parameters have been finalized;
- advanced matchers have been rejected.

Quantitative validation remains a benchmark and implementation task.

---

## Context

ChandraMap is a lunar image correspondence and registration system intended to establish reliable relationships between images of the same lunar region despite differences in:

- sensor;
- spatial resolution;
- Sun angle;
- illumination;
- viewing geometry;
- processing level;
- modality;
- effective ground scale.

Primary Chandrayaan-2 source instruments include:

- **OHRC** — Orbiter High Resolution Camera;
- **TMC-2** — Terrain Mapping Camera-2;
- **IIRS** — Imaging Infrared Spectrometer.

Reference imagery may include:

- **LRO NAC**;
- **LRO WAC**.

These sources must not be treated as equivalent camera images.

Approximate characteristics relevant to matching architecture include:

- OHRC: approximately `0.25–0.32 m/pixel`, depending on product/documentation;
- TMC-2: approximately `5 m/pixel`;
- IIRS: approximately `80 m/pixel`, with hyperspectral/infrared measurements;
- LRO NAC: high-resolution lunar reference imagery;
- LRO WAC: broader-scale lunar reference imagery.

ChandraMap's primary technical outputs are correspondence and registration products such as:

1. candidate correspondences;
2. geometrically verified inliers;
3. refined tie/control points where applicable;
4. transformation models;
5. registered source/reference outputs;
6. registration metrics;
7. spatial-coverage metrics;
8. benchmark results;
9. runtime measurements;
10. reproducible experimental artifacts.

A lunar mosaic, 3D Moon, or map interface is a downstream demonstration. It does not replace correspondence quality as the core technical problem.

---

## Relationship to ADR-0001

[ADR-0001](0001-v1-known-overlap-first.md) establishes that ChandraMap V1 begins with **known-overlap source/reference pairs**.

ADR-0001 answers:

> **Where does V1 begin?**

Answer:

> With source and reference images for which the overlapping lunar region is already known or otherwise supplied.

Therefore the first V1 problem is:

```text
Known Source / Reference Pair
        ↓
Local Correspondence
        ↓
Geometric Verification
        ↓
Transformation
        ↓
Registration
        ↓
Evaluation
```

V1 does not initially require:

- whole-Moon retrieval;
- global descriptor search;
- FAISS indexing;
- Top-K region retrieval;
- unknown-location localization.

ADR-0002 answers the next architectural question:

> **What local-matching method establishes the first reproducible V1 baseline?**

Answer:

> **SIFT.**

Conceptually:

```text
ADR-0001
Known-Overlap First
        ↓
Need a Reproducible Local-Matching Baseline
        ↓
ADR-0002
SIFT as the V1 Baseline
        ↓
Measure Real Performance
        ↓
Test Controlled Improvements
        ↓
Retain Only Improvements Supported by Evidence
```

---

## Problem

ChandraMap is intended to evolve through controlled, independently benchmarkable improvements.

Without a stable local-matching baseline, future statements such as:

- "LightGlue improved correspondence";
- "LoFTR reduced registration error";
- "sensor-aware preprocessing improved reliability";
- "scale normalization helped";
- "sub-pixel refinement improved accuracy";

cannot be interpreted rigorously.

If the reference method changes every time a new technique is introduced, ChandraMap loses the ability to determine which component caused an improvement or regression.

V1 therefore requires a local-matching approach that is:

- understandable;
- reproducible;
- mature;
- relatively simple;
- training-free;
- easy to inspect;
- compatible with classical geometric verification;
- suitable for controlled comparison.

The architecture also needs to keep local correspondence separate from:

- geometric verification;
- transformation estimation;
- sub-pixel refinement;
- registration;
- evaluation.

The local matcher must be replaceable so later approaches can be compared without rewriting the entire pipeline.

---

## Decision Drivers

The major decision drivers are:

### Reproducibility

The V1 baseline should be straightforward to rerun without requiring:

- training data;
- custom learned weights;
- a model-training pipeline;
- specialized accelerator hardware.

### Interpretability

Feature detection, descriptor construction, descriptor comparison, match filtering, and geometric verification should remain inspectable as separate stages.

### Baseline simplicity

V1 needs a method that is sufficiently capable to establish a meaningful reference without making the baseline itself a large research system.

### Mature implementation

The algorithm should have reliable implementations in widely used computer-vision libraries.

### Training-free execution

The initial benchmark should avoid introducing training and model-selection variables before ChandraMap has established a classical reference point.

### CPU accessibility

The baseline should remain practical in environments where GPU inference is unavailable.

This does not establish a universal runtime requirement.

### Controlled benchmarking

Later approaches should be comparable on the same image pairs, evaluation rules, and downstream registration stages.

### Failure analysis

The architecture should make it possible to diagnose whether failure occurs during:

- feature detection;
- descriptor matching;
- filtering;
- geometric verification;
- transform estimation.

### Scale and rotation tolerance

The initial matcher should offer useful local tolerance to ordinary image scale and orientation variation.

This must not be confused with solving arbitrary physical GSD differences.

### Geometric-verification compatibility

Candidate correspondences should feed naturally into RANSAC-based geometric verification.

### V1 stability

The method should be stable enough to remain a historical benchmark even after later versions introduce stronger approaches.

---

## Considered Options

### Option A — SIFT as the V1 Baseline

Use SIFT keypoint detection and descriptors with classical descriptor matching, filtering, and geometric verification.

#### Advantages

- classical and well understood;
- mature implementation ecosystem;
- training-free;
- interpretable feature pipeline;
- scale- and rotation-aware local feature design;
- straightforward candidate-match inspection;
- compatible with standard geometric verification;
- suitable for CPU-accessible experimentation;
- relatively low V1 engineering complexity;
- useful reference for evaluating learned and multimodal methods.

#### Trade-offs

- may degrade under severe illumination differences;
- may struggle with strong cross-modal appearance differences;
- may fail when effective ground-scale differences are too large;
- can perform poorly on low-feature terrain;
- can produce ambiguous matches in repetitive crater fields;
- does not exploit learned correspondence priors;
- does not solve hyperspectral representation design;
- does not solve non-planar or terrain-dependent geometry.

---

### Option B — ALIKED + LightGlue as the V1 Baseline

Use ALIKED for learned sparse keypoints/descriptors and LightGlue for sparse feature matching.

Conceptually:

```text
Prepared Image
        ↓
ALIKED
        ↓
Sparse Keypoints + Descriptors
        ↓
LightGlue
        ↓
Candidate Correspondences
```

#### Advantages

- modern sparse learned correspondence pipeline;
- provides an important later comparison against the classical baseline;
- may improve correspondence quality in some conditions;
- allows learned local-feature research without abandoning sparse geometry.

#### Trade-offs

- introduces pretrained model dependencies;
- adds model-version provenance requirements;
- may increase runtime or hardware complexity;
- pretrained-domain assumptions require lunar validation;
- makes the initial baseline dependent on learned behavior before a classical benchmark exists;
- complicates attribution of later gains;
- lunar robustness remains an empirical question.

ALIKED and LightGlue must not be described as one feature extractor. ALIKED generates sparse local features; LightGlue matches them.

---

### Option C — LoFTR as the V1 Baseline

Use LoFTR as a detector-free learned correspondence method.

Conceptually:

```text
Prepared Image Pair
        ↓
LoFTR
        ↓
Coarse-to-Fine Learned Correspondences
```

#### Advantages

- detector-free correspondence architecture;
- potentially useful when repeatable sparse keypoints are weak;
- important research comparison for difficult lunar imagery.

#### Trade-offs

- learned model dependency;
- domain-shift uncertainty;
- potentially greater computational requirements;
- substantially different architecture from the classical sparse pipeline;
- difficult to treat as a minimal classical reference;
- lunar illumination, modality, and extreme-scale behavior must still be measured.

LoFTR must not be placed conceptually in a "feature extractor" list beside SIFT or ALIKED. It represents a detector-free matching architecture.

---

### Option D — ORB as the V1 Baseline

Use ORB as a lightweight classical detector/descriptor approach.

#### Advantages

- fast;
- lightweight;
- widely available;
- CPU-friendly;
- useful future speed-oriented comparison.

#### Trade-offs

- the initial V1 goal prioritizes establishing correspondence quality before aggressive speed optimization;
- may provide a less useful primary accuracy-oriented reference for difficult lunar cases;
- benchmark behavior still requires measurement.

This ADR does not claim that ORB is universally inferior.

ORB remains a reasonable secondary speed-oriented baseline candidate.

---

## Decision

**Option A — SIFT as the V1 Baseline** is selected.

ChandraMap V1 will use SIFT-based sparse local feature matching as the canonical classical local-correspondence baseline for known-overlap lunar registration.

The baseline path is conceptually:

```text
Prepared Source Image
        +
Prepared Reference Image
        ↓
SIFT Keypoint Detection
        ↓
SIFT Descriptor Extraction
        ↓
Descriptor Matching
        ↓
Match Filtering
        ↓
Candidate Correspondences
        ↓
RANSAC / Geometric Verification
        ↓
Verified Inliers
        ↓
Transformation Estimation
        ↓
Registration
        ↓
Evaluation
```

The rationale is **not**:

> "SIFT is the best lunar matcher."

The rationale is:

> **SIFT provides a mature, interpretable, training-free, reproducible classical reference against which ChandraMap's later lunar-aware and learned improvements can be measured.**

---

## Rationale

### Classical and interpretable

SIFT exposes a clear local-feature pipeline:

```text
Keypoint Detection
        ↓
Descriptor Extraction
        ↓
Descriptor Comparison
        ↓
Candidate Correspondence
```

This helps separate different failure modes.

For example:

- insufficient keypoints is different from;
- many ambiguous descriptor matches, which is different from;
- geometrically inconsistent candidates.

A baseline should make those distinctions visible.

### Scale and rotation awareness

SIFT was designed to provide local feature robustness to image scale and rotation variation.

This makes it a reasonable starting point for lunar registration where source and reference products may differ in orientation and image scale.

However:

> **SIFT scale invariance does not eliminate physical ground-resolution differences.**

If one sensor cannot resolve a terrain feature, no descriptor can recover information that was never measured.

### Mature implementation

Reliable SIFT implementations exist in commonly used computer-vision software such as OpenCV.

This reduces V1 implementation complexity and improves the likelihood that the baseline can be reproduced independently.

### No training stage

The V1 SIFT baseline does not inherently require:

- model training;
- lunar-specific learned weights;
- training-pair generation;
- GPU inference;
- model checkpoint management;
- learned-model domain assumptions.

These variables can be introduced later as explicit experimental factors.

### Useful comparison point

SIFT provides a stable reference for controlled comparisons with approaches such as:

- ALIKED + LightGlue;
- LoFTR;
- RootSIFT variants;
- RIFT-inspired techniques;
- CFOG-inspired techniques;
- later lunar-specific methods.

A stronger method should demonstrate measurable value relative to this baseline under the same controlled evaluation conditions where scientifically appropriate.

---

## Important Limitation

> **Choosing SIFT as the baseline is not evidence that SIFT is sufficient for the final ChandraMap system.**

Expected areas of difficulty include:

- severe Sun-angle differences;
- major shadow changes;
- extreme effective ground-scale differences;
- strong cross-modal appearance changes;
- low-feature terrain;
- repetitive crater terrain;
- hyperspectral-to-visible matching;
- large viewpoint differences;
- terrain-induced geometric distortion.

These limitations are part of the reason the baseline exists.

The purpose is to establish what a straightforward classical method can achieve before more sophisticated components are introduced.

---

## What This Decision Establishes

ADR-0002 establishes that:

1. **SIFT is the canonical V1 local-feature baseline.**
2. SIFT feature extraction and descriptor matching constitute the baseline local-correspondence path.
3. Descriptor matches remain **candidate correspondences** until geometric verification.
4. RANSAC/geometric verification remains a separate architectural stage.
5. Baseline configuration must be reproducible and externally inspectable.
6. Scientifically meaningful configuration must not be hidden throughout implementation code.
7. Advanced matchers should be evaluated relative to the maintained SIFT baseline.
8. The same controlled image pairs should be used across matcher comparisons where scientifically valid.
9. SIFT baseline results should remain available for historical comparison after stronger methods are added.
10. Baseline failures are scientific evidence and should not be hidden.

---

## What This Decision Does Not Establish

ADR-0002 does **not** decide:

- the final ChandraMap matcher;
- the final learned matcher;
- exact SIFT parameter values;
- exact descriptor-filter thresholds;
- an exact ratio-test threshold;
- an exact descriptor-distance threshold;
- exact RANSAC thresholds;
- final RANSAC confidence settings;
- the final geometric transform model;
- the final sub-pixel refinement algorithm;
- RootSIFT adoption;
- ORB benchmark design;
- final IIRS representation;
- global retrieval architecture;
- FAISS usage;
- Moon tiling strategy;
- global descriptor architecture;
- final sensor preprocessing;
- final illumination representation;
- final V2/V3/V4 matcher architecture;
- GPU deployment policy;
- production backend serving architecture.

These remain separate implementation, research, benchmark, or architectural decisions.

---

## V1 Baseline Pipeline

The broader V1 baseline is conceptually:

```text
Known Overlapping Pair
        ↓
Input Validation
        ↓
Sensor-Aware Preparation
        ↓
Comparable Effective Scale
        ↓
SIFT Keypoints + Descriptors
        ↓
Descriptor Matching
        ↓
Match Filtering
        ↓
Candidate Correspondences
        ↓
RANSAC / Geometric Verification
        ↓
Verified Inliers
        ↓
Initial Transformation
        ↓
Optional Sub-Pixel Refinement
        ↓
Final Transform Refit
        ↓
Registered Output
        ↓
Metrics + Artifacts
```

SIFT is one component of this pipeline.

SIFT itself does **not** perform:

- sensor routing;
- scale preparation;
- geometric verification;
- RANSAC;
- transformation estimation;
- image warping;
- sub-pixel refinement;
- geolocation;
- registration evaluation;
- benchmark aggregation.

Maintaining these boundaries prevents the local matcher from becoming responsible for unrelated scientific stages.

---

## Matching Terminology

Precise terminology is required because each stage represents a different level of evidence.

### SIFT Keypoint

A location, scale, and orientation identified as a potentially distinctive local image structure.

A keypoint is not a correspondence.

### SIFT Descriptor

A numerical representation describing local image structure around a detected keypoint.

A descriptor belongs to a specific keypoint and image representation.

### Descriptor Match

A proposed relationship between a source descriptor and a reference descriptor produced by descriptor comparison.

A descriptor match is a hypothesis.

### Candidate Match

A descriptor correspondence that survives the configured descriptor-level filtering process.

A candidate match is **not automatically geometrically correct**.

### Verified Inlier

A candidate correspondence that is consistent with the selected geometric model under the geometric-verification process.

RANSAC inlier status means geometric consistency under the fitted model; it does not mean independent ground truth.

### Tie / Control Point

A correspondence accepted for transformation or registration use according to the pipeline's control-point semantics.

Where refinement exists, the final tie-point coordinates may differ from the original SIFT keypoint coordinates.

These terms must not be used interchangeably.

---

## Descriptor Matching

SIFT produces descriptors.

A separate descriptor-matching stage compares source descriptors with reference descriptors to propose candidate correspondences.

Potential classical matching mechanisms may include:

- nearest-neighbor matching;
- nearest-neighbor ratio filtering;
- mutual or cross-check filtering where scientifically appropriate.

ADR-0002 does not prescribe permanent numerical thresholds.

Configuration such as:

- number of nearest neighbors;
- ratio-test threshold;
- descriptor-distance filtering;
- cross-check behavior;

must be explicit and benchmarkable.

Values not yet established remain:

`TBD`.

### Why thresholds are not fixed here

A permanent threshold should not be selected merely because it is common in generic image-matching tutorials.

Lunar data may exhibit different:

- texture;
- illumination;
- scale relationships;
- ambiguity patterns.

Threshold selection should therefore be configuration-driven and experimentally justified.

---

## RootSIFT

RootSIFT may be evaluated as a controlled variation of the classical SIFT descriptor pipeline.

However:

- RootSIFT is **not** made part of the canonical V1 baseline by this ADR;
- RootSIFT must not silently replace SIFT in historical V1 results;
- if evaluated, RootSIFT should be identified as a separate variant.

The accepted decision remains:

> **SIFT as the V1 local-matching baseline.**

---

## Geometric Verification

SIFT descriptor matching and geometric verification are separate responsibilities.

Conceptually:

```text
SIFT Descriptors
        ↓
Descriptor Matching
        ↓
Descriptor Filtering
        ↓
Candidate Correspondences
        ↓
RANSAC + Geometric Model
        ↓
Verified Inliers
```

This distinction is architecturally important.

A low descriptor distance or high matcher confidence does not prove that two features represent the same physical lunar location.

Geometric verification determines whether candidate correspondences are jointly consistent with a spatial transformation.

Therefore:

> **Descriptor-filtered correspondences must not be labelled "final matches" before geometric verification.**

---

## Transformation Models

The V1 registration pipeline may evaluate standard transform families such as:

- affine transformation;
- homography.

ADR-0002 deliberately does not make a permanent transform-model selection.

Transform choice depends on:

- product preparation;
- projection;
- overlap extent;
- viewpoint;
- terrain relief;
- sensor geometry;
- benchmark behavior.

The Moon is not a flat poster.

Raw or insufficiently projected imagery may contain residual effects due to:

- topography;
- viewing geometry;
- spacecraft sensor geometry;
- projection differences.

If affine and homography variants are both evaluated, they should be identified explicitly rather than silently choosing whichever produces the most favorable result.

A separate architectural decision may be appropriate if one model becomes the canonical transform for a scientific version.

---

## Sub-Pixel Refinement Relationship

SIFT keypoint coordinates are not automatically the final sub-pixel registration solution.

Where local or sub-pixel refinement is included, the preferred conceptual ordering is:

```text
SIFT Candidate Correspondences
        ↓
RANSAC / Initial Model
        ↓
Verified Inliers
        ↓
Local / Sub-Pixel Refinement
        ↓
Final Transform Refit
```

The pipeline should not refine large populations of unverified descriptor candidates and then treat them as authoritative tie points.

After accepted tie-point coordinates change through refinement, the final transformation should be estimated again from the refined coordinates when that refinement path is used.

ADR-0002 does not select the refinement algorithm.

Possible later research directions may include:

- correlation-based refinement;
- phase-based refinement;
- planetary image-registration tools;
- other controlled, experimentally validated approaches.

---

## Sensor Considerations

SIFT is the shared **baseline matcher**, not a requirement that every sensor receive identical preprocessing.

### OHRC

OHRC provides very high-resolution visible/panchromatic lunar imagery.

SIFT may be evaluated after appropriate:

- product preparation;
- geometry handling;
- scale preparation.

A potential difficulty is that OHRC can contain much finer terrain detail than a coarser reference representation.

More source detail does not automatically make correspondence easier if the reference cannot resolve that detail.

The baseline should therefore compare physically meaningful image information rather than assuming that maximum source resolution is always optimal for matching.

---

### TMC-2

TMC-2 provides panchromatic terrain imagery at approximately `5 m/pixel`.

It is suitable for V1 classical matching experiments because it contains visible terrain structure at a moderate spatial scale.

This ADR does not claim:

- that TMC-2 is the easiest sensor;
- that a particular TMC-2 benchmark already succeeds;
- that one preprocessing path is final.

Those are empirical questions.

---

### IIRS

IIRS is hyperspectral/infrared imaging data at approximately `80 m/pixel`.

It must not be treated as an ordinary grayscale camera frame without an explicit representation step.

Conceptually:

```text
IIRS Product
        ↓
Defined Registration-Compatible 2D Representation
        ↓
SIFT Baseline
```

Potential research representations may include:

- selected spectral band;
- PCA-derived representation;
- fixed composite;
- gradient representation;
- structural representation.

ADR-0002 does not choose among these alternatives.

The representation must be:

- explicit;
- reproducible;
- traceable to the parent product.

A future ADR may formalize an IIRS registration representation after controlled evidence exists.

---

### LRO Reference Imagery

LRO NAC or WAC reference products may differ significantly in effective ground scale from Chandrayaan source imagery.

The reference side may therefore require:

- downsampling;
- pyramid-level selection;
- another controlled scale-preparation mechanism.

LRO NAC and LRO WAC should remain distinguishable where their different spatial roles matter.

---

## Scale Considerations

> **SIFT scale invariance does not mean ChandraMap can ignore physical ground-resolution differences.**

Conceptually:

```text
OHRC       ~0.25–0.32 m/px
TMC-2      ~5 m/px
IIRS       ~80 m/px
LRO NAC    high-resolution reference imagery
LRO WAC    broader-scale reference imagery
```

These values describe substantially different physical sampling regimes.

If an IIRS pixel represents terrain tens of metres across, enlarging that pixel grid does not reveal sub-metre craters.

Therefore:

```text
High-Resolution Reference
        ↓
Reference Pyramid / Downsampling
        ↓
Comparable Effective Scale
        ↓
SIFT Matching
```

is scientifically preferable to:

```text
Low-Resolution Source
        ↓
Large Upsampling
        ↓
Assume Fine Detail Now Exists
```

Upsampling changes sampling density.

It does not recover missing surface information.

The exact scale-pyramid or scale-selection architecture is deferred.

---

## Illumination Considerations

SIFT must not be described as automatically invariant to major lunar illumination changes.

Different Sun angles may change:

- brightness;
- shadow direction;
- shadow length;
- crater appearance;
- ridge visibility;
- gradient structure;
- which terrain elements are visible.

Simple brightness or contrast normalization may help with radiometric differences, but it cannot reconstruct shadow geometry produced by a different illumination direction.

Experiments may compare representations such as:

- raw grayscale;
- normalized grayscale;
- gradients;
- edges;
- other structural representations.

ADR-0002 does not select one as universally superior.

Such decisions require controlled benchmark evidence.

---

## Matcher Interface Boundary

SIFT should be represented as one implementation of a replaceable local-correspondence stage.

Conceptually:

```text
Prepared Image Pair
        ↓
Local Matcher Interface
        │
        ├── SIFT Baseline
        ├── ALIKED + LightGlue
        ├── LoFTR
        └── Future Matcher
        ↓
Candidate Correspondences
        ↓
Geometric Verification
```

This is an architectural boundary, not a requirement for a specific class name or programming-language interface.

No concrete Python interface is defined by this ADR.

### Why matchers remain modular

ChandraMap is a benchmark-oriented research project.

To compare local matching approaches fairly, matcher-specific code should not unnecessarily own:

- RANSAC;
- geometric verification;
- metric computation;
- benchmark orchestration;
- registration visualization;
- result persistence;
- global evaluation.

Keeping these responsibilities separated makes it possible to change the matcher while preserving the downstream evaluation contract.

---

## Advanced Matcher Candidates

### ALIKED + LightGlue

A correct high-level interpretation is:

```text
ALIKED
        ↓
Sparse Keypoints + Descriptors
        ↓
LightGlue
        ↓
Sparse Candidate Correspondences
```

Potential value:

- modern sparse learned local correspondence;
- candidate for comparison against classical SIFT.

Important limitation:

> Pretrained terrestrial models must not automatically be assumed robust to lunar imagery.

Performance must be measured under lunar benchmark conditions.

---

### LoFTR

LoFTR represents a detector-free learned matching architecture.

Conceptually:

```text
Prepared Image Pair
        ↓
LoFTR
        ↓
Coarse-to-Fine Learned Correspondences
```

Potential value:

- may help where repeatable sparse keypoints are weak.

Important uncertainties include:

- lunar domain shift;
- extreme scale differences;
- illumination differences;
- cross-sensor behavior.

These are benchmark questions.

---

### RIFT- and CFOG-Inspired Methods

RIFT- and CFOG-style remote-sensing methods are relevant research directions for correspondence under multimodal or radiometric differences.

Potential value includes structure-oriented matching where raw intensity consistency is weak.

However:

- they may require additional engineering;
- they are not assumed to be drop-in replacements;
- no superiority claim is made by this ADR.

They remain research alternatives until validated.

---

## Why Advanced Methods Are Not the V1 Baseline

A baseline is not intended to maximize architectural sophistication.

A useful baseline should be:

- understandable;
- reproducible;
- stable;
- sufficiently capable to generate meaningful evidence;
- relatively low-complexity;
- easy to compare against.

The intended research logic is:

```text
Baseline
        ↓
Measure
        ↓
Introduce One Controlled Change
        ↓
Measure Again
        ↓
Determine Whether the Change Helped
```

not:

```text
Add Multiple Algorithms
        ↓
Increase Pipeline Complexity
        ↓
Assume Complexity Means Improvement
```

Advanced methods earn their role by improving measured behavior for identified problems.

---

## Baseline Configuration

The SIFT baseline must be reproducible.

Scientifically relevant parameters should be configuration-driven rather than distributed as unexplained constants throughout implementation code.

Where applicable, configuration should preserve or resolve:

- SIFT detector parameters;
- maximum feature count if explicitly limited;
- descriptor matcher;
- number of nearest neighbors;
- descriptor-filter strategy;
- ratio-test threshold if used;
- mutual/cross-check behavior;
- image representation;
- scale/pyramid settings;
- preprocessing choices;
- RANSAC model;
- RANSAC threshold;
- RANSAC confidence;
- random state where relevant;
- transformation model;
- refinement enablement where applicable.

Exact values are **TBD** unless separately established.

This ADR intentionally does not fabricate defaults.

---

## Baseline Stability

Once a V1 baseline configuration is used for formal comparison, it must not be silently modified while retaining the same scientific identity.

Examples of scientifically significant drift include silently changing:

- SIFT configuration;
- preprocessing;
- descriptor filtering;
- reference scale;
- RANSAC behavior;
- transform model;
- benchmark pair set;
- metric definition.

If a significant change is required:

1. document the change;
2. version or otherwise identify the new configuration;
3. rerun affected benchmarks;
4. state whether old and new results remain directly comparable;
5. create a new ADR if the change is architectural.

Historical V1 results should remain interpretable.

---

## Benchmark and Evaluation Requirements

The baseline exists to create a controlled reference.

Advanced methods should therefore use the same evaluation context wherever scientifically appropriate.

Conceptually:

```text
Same Controlled Pair
        │
        ├── SIFT Baseline
        │
        ├── ALIKED + LightGlue
        │
        ├── LoFTR
        │
        └── Later Lunar-Aware Method
```

Relevant controls may include:

- source/reference pair;
- sensor representation;
- preprocessing, unless preprocessing is the tested variable;
- scale handling, unless scale is the tested variable;
- geometric-verification rules;
- transform model;
- evaluation truth;
- metric definitions;
- success/failure accounting.

The project should avoid changing multiple major variables and attributing the resulting difference to one algorithm.

---

## Expected Metrics

No metric value is asserted by this ADR.

### Matching Metrics

Expected measurements may include:

- detected keypoint count;
- candidate match count;
- verified inlier count;
- inlier ratio.

### Geometric Metrics

Where scientifically valid:

- reprojection residuals;
- independent check-point RMSE;
- source-image pixel error.

### Distribution Metrics

Possible measurements include:

- grid coverage;
- convex-hull coverage;
- normalized spatial distribution.

The exact spatial-coverage metric may be finalized separately.

### Reliability Metrics

- successful pair count;
- failed pair count;
- failure category.

### Performance Metrics

Where relevant:

- feature-extraction runtime;
- matching runtime;
- geometric-verification runtime;
- total local-registration runtime;
- memory usage;
- execution hardware context.

### Geospatial Metrics

Ground-space error may be reported only when the required context is available, including:

- valid GSD/scale information;
- meaningful projection or spatial model;
- appropriate reference truth.

Source-image pixel error should remain available before converting to metres where that conversion is scientifically meaningful.

---

## Important Evaluation Rule

Do not evaluate registration accuracy only on the same points used to estimate the transformation when independent evaluation is available.

Preferred structure:

```text
Fit Tie Points
        ↓
Estimate Transformation

Independent Check Points
        ↓
Apply Transformation
        ↓
Evaluate Registration Error
```

A fit residual answers:

> How well does the model explain the data that contributed to fitting it?

An independent check-point error answers:

> How accurately does the transformation predict points not used to estimate it?

These are different measurements.

RANSAC inliers must not be described as independent truth merely because they passed geometric verification.

---

## Benchmark Comparison Format

Future benchmark summaries may use a structure such as:

| Method             | Test Set               | Inlier Ratio | Check-Point RMSE | Spatial Coverage | Runtime |
| ------------------ | ---------------------- | -----------: | ---------------: | ---------------: | ------: |
| SIFT baseline      | `<dataset/pairs>`      |        `TBD` |            `TBD` |            `TBD` |   `TBD` |
| ALIKED + LightGlue | `<same dataset/pairs>` |        `TBD` |            `TBD` |            `TBD` |   `TBD` |
| LoFTR              | `<same dataset/pairs>` |        `TBD` |            `TBD` |            `TBD` |   `TBD` |

This table is a reporting format, not measured evidence.

Methods should use the same controlled pair set where scientifically valid.

Do not report an undefined single "accuracy %" as the primary scientific result.

---

## Spatial Coverage

More verified matches do not automatically imply stronger registration.

Conceptual example:

```text
Case A
120 verified points
concentrated around one small crater
```

versus:

```text
Case B
60 verified points
distributed across the overlap region
```

Depending on their accuracy and geometry, Case B may provide stronger support for a global registration model.

Therefore V1 evaluation should not rely only on raw match count.

Potential spatial-distribution measures include:

- grid coverage;
- convex-hull coverage;
- normalized spatial spread.

Exact definitions remain part of the evaluation methodology.

---

## Failure Analysis

The SIFT baseline should expose failure rather than conceal it.

Potential observed failure categories include:

- insufficient keypoints;
- insufficient candidate matches;
- insufficient verified inliers;
- spatially clustered inliers;
- RANSAC failure;
- unstable transformation;
- excessive residual error;
- scale mismatch;
- illumination mismatch;
- cross-modal mismatch;
- projection mismatch;
- low-feature terrain;
- repetitive terrain;
- poor overlap.

No frequency is asserted for any category.

### Observed stage vs root cause

A failure observed at RANSAC does not prove that RANSAC itself is the root cause.

For example:

```text
Observed:
RANSAC cannot establish a stable model.
```

Possible underlying causes may include:

- incorrect candidate correspondences;
- scale mismatch;
- illumination difference;
- wrong image representation;
- insufficient common detail.

Failure reporting should distinguish observation from inference.

---

## Reproducibility Requirements

Where relevant, a formal SIFT baseline run should preserve enough information to reconstruct the experiment.

Useful provenance may include:

- source product identifier;
- reference product identifier;
- input hashes where used;
- source sensor;
- reference sensor;
- source/reference GSD where known;
- projection information where relevant;
- image representation;
- preprocessing settings;
- scale/pyramid settings;
- SIFT configuration;
- matcher configuration;
- match-filter configuration;
- RANSAC configuration;
- transform type;
- random state where relevant;
- OpenCV/library version;
- Python/runtime environment;
- platform information;
- CPU/GPU context for performance measurements;
- benchmark version;
- metric definitions;
- test-pair identifiers;
- code revision.

The reproducibility objective is:

```text
Same Data
+
Same Scientific Configuration
+
Same Relevant Software Environment
≈
Reproducible Baseline Result
```

Bit-for-bit identity across every platform is not asserted by this ADR.

---

## Artifacts

A formal benchmark run should ideally preserve useful diagnostic and reproducibility artifacts where supported.

Potential artifacts include:

- source identifier;
- reference identifier;
- sensor metadata;
- input dimensions;
- GSD where known;
- preprocessing configuration;
- SIFT configuration;
- matcher configuration;
- candidate-match visualization;
- verified-inlier visualization;
- rejected-match visualization;
- transformation output;
- residual information;
- registered overlay;
- metric record;
- runtime record;
- environment information.

This section describes expected evidence categories.

It does not claim that all such artifacts are currently implemented.

---

## Testing and Validation Plan

Quantitative validation is pending.

Implementation should proceed in increasingly difficult stages rather than beginning with the complete lunar problem.

### Stage 1 — One Known Pair

Conceptually:

```text
Known Pair
        ↓
SIFT
        ↓
Descriptor Matching
        ↓
Filtering
        ↓
RANSAC
        ↓
Transform
        ↓
Registered Overlay
        ↓
Metrics
```

Goal:

Demonstrate that the classical path works end-to-end and produces inspectable outputs.

This is implementation validation, not proof of general lunar robustness.

---

### Stage 2 — Multiple Known Pairs

Run the same frozen baseline across multiple lunar regions.

Goal:

Evaluate whether the pipeline behaves consistently beyond one demonstration pair.

No pair count is specified by this ADR.

---

### Stage 3 — Scale Stress

Evaluate increasingly difficult source/reference scale relationships.

Goal:

Identify where ordinary SIFT scale tolerance becomes insufficient and where explicit physical scale preparation becomes necessary.

---

### Stage 4 — Sun-Angle Stress

Where suitable real data are available, evaluate corresponding terrain under meaningfully different illumination.

Goal:

Measure performance degradation rather than assuming illumination invariance.

---

### Stage 5 — Low-Feature and Repetitive Terrain

Evaluate terrain with:

- limited distinctive structure;
- repetitive crater patterns.

Goal:

Expose ambiguous matching and spatial-support limitations.

---

### Stage 6 — Advanced Matcher Comparison

Compare:

```text
SIFT
vs
ALIKED + LightGlue
vs
LoFTR
```

on the same controlled data where scientifically appropriate.

Goal:

Determine whether advanced methods provide measurable improvement and under which conditions.

---

### Stage 7 — Lunar-Aware Pipeline Comparison

Compare the SIFT baseline with controlled additions such as:

- sensor-aware preprocessing;
- explicit scale handling;
- structural illumination representations;
- sub-pixel refinement;
- stronger matching methods.

Goal:

Attribute gains to individual components before evaluating their combined pipeline behavior.

---

## Acceptance Criteria

This ADR intentionally defines structural rather than fabricated numerical acceptance thresholds.

ADR-0002 is considered implemented when the V1 baseline can satisfy the following conditions:

- SIFT feature extraction can operate on a prepared known-overlap pair;
- source and reference descriptors can be produced;
- descriptor matching can generate candidate correspondences;
- descriptor-level candidates are explicitly distinguished from verified inliers;
- geometric verification can consume candidate correspondences;
- RANSAC or the selected verification stage can establish valid inliers where sufficient support exists;
- transform estimation can operate when sufficient valid geometry exists;
- scientifically invalid geometry is surfaced as failure rather than converted into false success;
- a registered output can be generated after successful transformation;
- benchmark metrics can be recorded;
- baseline configuration is reproducible;
- failures remain visible;
- later matchers can be compared using the same surrounding registration/evaluation architecture.

Quantitative thresholds should be established only when justified by:

- real benchmark evidence;
- external requirements;
- version-level acceptance criteria.

---

## Consequences

### Positive Consequences

#### Stable classical benchmark

ChandraMap gains a persistent local-matching reference.

#### Interpretability

Feature detection, descriptors, candidate matches, verification, and transformation remain separable.

#### Lower reproducibility barrier

No training pipeline is required for the primary V1 baseline.

#### Easier debugging

Failures can be inspected stage by stage.

#### CPU-accessible starting point

Baseline evaluation does not inherently depend on GPU inference.

#### Direct comparison with advanced methods

Later methods can be evaluated relative to a known reference.

#### Clear research progression

The project can distinguish classical performance from later gains.

#### Suitable first V1 implementation

SIFT supports the known-overlap-first architecture established by ADR-0001 without adding global retrieval complexity.

---

### Negative Consequences / Trade-offs

#### Illumination sensitivity

SIFT may perform poorly under severe lunar shadow and Sun-angle differences.

#### Cross-modality limitations

Ordinary SIFT descriptors may struggle where source/reference appearance differs substantially across modalities.

#### Extreme scale gaps

Local scale robustness does not solve missing physical information.

#### Low-feature terrain

Some lunar terrain may not provide sufficiently distinctive sparse keypoints.

#### Repetitive terrain

Repeated crater-like structures may create ambiguous candidate matches.

#### Limited performance ceiling

The SIFT baseline may not represent the eventual best ChandraMap correspondence method.

#### Future dependency diversity

Adding learned alternatives later will introduce additional model and environment management.

---

### Neutral Consequences

- later versions may use a stronger operational matcher;
- SIFT remains a benchmark path wherever practical;
- matcher performance should be reported by relevant sensor/stress category;
- RootSIFT, ORB, learned matchers, and remote-sensing methods remain legitimate research alternatives;
- the baseline does not constrain later global retrieval architecture.

---

## Risks and Mitigations

| Risk                                              | Why It Matters                                                                                 | Mitigation                                                                                         |
| ------------------------------------------------- | ---------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| SIFT succeeds mainly on easy pairs                | Could create false confidence about lunar robustness                                           | Include controlled scale, illumination, modality, low-feature, and repetitive-terrain stress cases |
| Descriptor matches are treated as final matches   | Could inflate apparent correspondence quality                                                  | Require separate geometric verification                                                            |
| Raw feature count becomes the main success metric | More features do not guarantee accurate registration                                           | Report inliers, error, spatial coverage, failures, and runtime                                     |
| SIFT scale invariance is overinterpreted          | Extreme physical GSD gaps remain unsolved                                                      | Use physically meaningful scale preparation and preserve source information limits                 |
| SIFT performs poorly on IIRS                      | Hyperspectral-to-visible correspondence differs fundamentally from ordinary grayscale matching | Define and benchmark explicit IIRS-derived 2D registration representations                         |
| Baseline configuration drifts                     | Historical benchmark comparison becomes unreliable                                             | Preserve and identify configuration; rerun benchmarks after scientifically significant changes     |
| Learned methods are assumed superior              | Complexity could increase without measurable benefit                                           | Require controlled comparison against the same baseline                                            |
| Evaluation uses only fitted correspondences       | Fit error may understate true registration error                                               | Use independent check points where available                                                       |
| SIFT matches cluster spatially                    | A transform may be weakly constrained outside the cluster                                      | Include spatial-coverage metrics                                                                   |
| A flexible transform hides weak correspondences   | Good-looking overlays may obscure poor control geometry                                        | Inspect inliers, residuals, held-out error, and coverage                                           |
| Failure is replaced by identity/default outputs   | Could create false successful registrations                                                    | Represent geometric failure explicitly                                                             |
| Advanced methods silently replace V1              | Historical baseline becomes irreproducible                                                     | Keep SIFT as an identified V1 benchmark path                                                       |

These risks are architectural considerations, not claims that the failures have already occurred.

---

## Deferred Decisions

The following remain explicitly deferred:

- SIFT parameter optimization;
- exact descriptor-matching implementation details;
- exact descriptor-filter threshold;
- exact ratio-test threshold;
- exact RANSAC threshold;
- exact RANSAC confidence;
- final transform model;
- RootSIFT adoption;
- ORB speed-baseline definition;
- ALIKED + LightGlue adoption;
- LoFTR adoption;
- RIFT implementation;
- CFOG implementation;
- final IIRS representation;
- sub-pixel refinement algorithm;
- illumination structural representation;
- sensor-specific matcher-selection policy;
- matcher ensemble design;
- global retrieval architecture;
- global descriptor model;
- FAISS usage;
- reference tiling strategy;
- later-version production matcher.

Each may require:

- controlled experiment;
- benchmark evidence;
- separate ADR;
- or a combination of these.

---

## Relationship to the Research Baseline

The research baseline documentation should remain consistent with this ADR.

The architectural distinction is:

```text
ADR-0002
records why SIFT is the V1 baseline

Research baseline documentation
defines how that baseline is used as a scientific comparison point

Benchmark documentation
defines how baseline performance is measured
```

The ADR is historical architectural rationale.

It is not itself a benchmark specification.

---

## Relationship to Future ChandraMap Versions

Future versions may introduce more capable correspondence methods.

Conceptually:

```text
V1
SIFT Classical Baseline
        ↓
Later Version
Improved Preprocessing and/or Stronger Matching
        ↓
Later Research
More Advanced Multi-Sensor / Multimodal Methods
```

Exact V2, V3, or V4 matcher architectures are not defined here.

The important requirement is:

> **SIFT should remain runnable as a historical benchmark path wherever practical so future improvements retain a stable comparison point.**

A later method becoming preferred operationally does not erase the architectural value of the V1 SIFT baseline.

---

## Architectural Evolution Model

```text
ADR-0001
Known-Overlap First
        ↓
ADR-0002
SIFT as V1 Baseline
        ↓
Controlled Measurement
        ↓
Identify Failure Modes
        ↓
Test One Improvement at a Time
        ↓
Sensor / Scale / Geometry / Refinement Research
        ↓
Advanced Matcher Comparison
        ↓
Later Version Decisions
```

Future architectural decisions should preserve the ability to identify which part of this progression generated each measured improvement.

---

## Revisit Conditions

ADR-0002 may be revisited if:

- SIFT cannot establish a meaningful reference on representative benchmark data;
- the project changes to a task where sparse SIFT correspondence is fundamentally inappropriate;
- reproducible evidence shows another classical baseline provides a substantially more informative reference;
- project requirements materially change;
- SIFT implementation/platform support changes in a way that undermines reproducibility;
- the benchmark architecture is substantially restructured;
- a later architecture makes maintaining the SIFT baseline technically impractical.

Revisiting this decision does not erase it.

If replaced:

1. preserve this file;
2. change its status to `Superseded`;
3. add `Superseded By: ADR-XXXX`;
4. create a new ADR explaining the replacement;
5. preserve historical V1 benchmark interpretation.

---

## Evidence Status

At the time represented by this ADR:

| Evidence Category                  | Status                                                               |
| ---------------------------------- | -------------------------------------------------------------------- |
| SIFT implementation availability   | Architectural assumption supported by mature computer-vision tooling |
| V1 ChandraMap end-to-end result    | TBD                                                                  |
| V1 lunar benchmark                 | TBD                                                                  |
| Check-point RMSE                   | TBD                                                                  |
| Inlier-ratio benchmark             | TBD                                                                  |
| Spatial-coverage benchmark         | TBD                                                                  |
| Runtime benchmark                  | TBD                                                                  |
| OHRC-specific measured result      | TBD                                                                  |
| TMC-2-specific measured result     | TBD                                                                  |
| IIRS-derived representation result | TBD                                                                  |
| ALIKED + LightGlue comparison      | TBD                                                                  |
| LoFTR comparison                   | TBD                                                                  |
| RIFT/CFOG comparison               | TBD                                                                  |

This ADR intentionally does not fabricate benchmark evidence.

The decision establishes the reference architecture required to produce that evidence.

---

## References

### Internal ChandraMap Documentation

- [`README.md`](README.md) — Architecture Decision Record operating guide
- [`ADR_TEMPLATE.md`](ADR_TEMPLATE.md) — ADR template, where present
- [`0001-v1-known-overlap-first.md`](0001-v1-known-overlap-first.md) — preceding architectural decision establishing the known-overlap-first V1 scope
- [`../research/baseline.md`](../research/baseline.md) — research baseline definition
- [`../research/experiment-methodology.md`](../research/experiment-methodology.md) — controlled experiment methodology
- [`../architecture/v1-pipeline.md`](../architecture/v1-pipeline.md) — V1 processing architecture
- [`../versions/v1/specification.md`](../versions/v1/specification.md) — V1 scientific specification
- [`../versions/v1/pipeline.md`](../versions/v1/pipeline.md) — V1 version pipeline
- [`../versions/v1/benchmark.md`](../versions/v1/benchmark.md) — V1 benchmark definition
- [`../algorithms/sift.md`](../algorithms/sift.md) — SIFT algorithm documentation
- [`../algorithms/matching.md`](../algorithms/matching.md) — matching-stage documentation
- [`../algorithms/match-filtering.md`](../algorithms/match-filtering.md) — descriptor-match filtering
- [`../algorithms/ransac.md`](../algorithms/ransac.md) — geometric verification
- [`../algorithms/transforms.md`](../algorithms/transforms.md) — transform-model documentation
- [`../algorithms/subpixel-refinement.md`](../algorithms/subpixel-refinement.md) — refinement-stage documentation
- [`../evaluation/benchmark-protocol.md`](../evaluation/benchmark-protocol.md) — benchmark protocol
- [`../evaluation/metrics.md`](../evaluation/metrics.md) — metric definitions
- [`../evaluation/checkpoint-evaluation.md`](../evaluation/checkpoint-evaluation.md) — independent evaluation
- [`../evaluation/spatial-coverage.md`](../evaluation/spatial-coverage.md) — correspondence-distribution metrics
- [`../evaluation/failure-cases.md`](../evaluation/failure-cases.md) — failure taxonomy
- [`../evaluation/reproducibility.md`](../evaluation/reproducibility.md) — scientific reproducibility requirements
- [`../development/testing.md`](../development/testing.md) — implementation testing policy
- [`../development/benchmarking.md`](../development/benchmarking.md) — development benchmarking workflow

### External Technical References

Relevant external sources for implementation and future evaluation include:

- David G. Lowe — _Distinctive Image Features from Scale-Invariant Keypoints_
- OpenCV — SIFT documentation
- OpenCV — feature matching documentation
- OpenCV — homography and RANSAC documentation
- LightGlue official repository/documentation
- ALIKED research paper
- LoFTR — detector-free local feature matching research
- RIFT — multimodal remote-sensing image matching research
- CFOG — multimodal remote-sensing matching research
- USGS ISIS image-registration documentation
- ISRO Chandrayaan-2 instrument and science documentation
- LROC documentation

External references provide implementation and scientific context. They do not substitute for ChandraMap's own controlled lunar benchmark evidence.

---

## Decision Summary

ADR-0001 establishes **where V1 starts**:

```text
Known Source / Reference Overlap
```

ADR-0002 establishes **how the first local-correspondence reference is defined**:

```text
Prepared Known-Overlap Pair
        ↓
SIFT Keypoints + Descriptors
        ↓
Descriptor Matching
        ↓
Candidate Correspondences
        ↓
Geometric Verification
        ↓
Verified Inliers
        ↓
Transform
        ↓
Registration
        ↓
Evaluation
```

The enduring architectural principle is:

> **SIFT is selected because ChandraMap needs a mature, interpretable, training-free and reproducible baseline—not because SIFT is assumed to be the final or universally best lunar matcher.**

Future methods must be evaluated against controlled evidence.

If they demonstrate measurable value for the targeted lunar conditions, they may become part of later ChandraMap scientific versions.

The SIFT path should nevertheless remain available as the historical V1 comparison baseline wherever practical.
