# Future Research Roadmap

> **Directory:** `research/future/`
> **Project:** ChandraMap
> **Scope:** Post-V1 research, experimental directions, architectural evolution, and future-version planning
> **Status:** Research roadmap / navigation document
> **Principle:** **Build small → measure honestly → preserve failures → promote only validated improvements**

---

## 1. Purpose

The `research/future/` directory contains research directions that are **not yet part of the established V1 implementation** but may become future ChandraMap capabilities after sufficient experimentation and validation.

This directory exists to separate:

* established project knowledge;
* currently implemented or actively evaluated V1 work;
* measured experimental findings;
* exploratory research ideas;
* future architectural directions.

The separation is intentional.

A research idea should not become part of the main system merely because it is technically interesting or more sophisticated than the current implementation.

It should first demonstrate measurable value on appropriate lunar data.

---

## 2. What `research/future/` Means

The future-research directory answers:

> **What should ChandraMap investigate next, why does it matter, how should it be tested, and what evidence would justify promoting it into a future version?**

It does **not** mean:

> "These features already exist."

A document placed under `research/future/` should therefore be treated as one of the following:

| Classification          | Meaning                                                                          |
| ----------------------- | -------------------------------------------------------------------------------- |
| **Exploratory**         | Research idea requiring investigation                                            |
| **Proposed**            | Direction supported by project needs but not yet validated                       |
| **Planned**             | Intentionally scheduled for future experimentation                               |
| **Validated direction** | Experimental evidence supports further development                               |
| **Promoted**            | Sufficient evidence exists to move the capability toward a future implementation |
| **Not implemented**     | Research/documentation exists, but production code does not                      |
| **Deferred**            | Potentially useful, but intentionally postponed                                  |
| **Rejected**            | Tested or reviewed and not justified for the current roadmap                     |

The classification should be explicit.

---

# 3. Why Future Research Is Separate From V1

ChandraMap V1 is intended to establish a measurable foundation for lunar image correspondence and registration.

The current V1 direction focuses on proving the core pipeline:

```text
Source Image
      │
      ▼
Sensor-Aware Preparation
      │
      ▼
Scale / Representation Handling
      │
      ▼
Candidate Correspondences
      │
      ▼
Geometric Verification
      │
      ▼
Initial Transformation
      │
      ▼
Residual Analysis
      │
      ▼
Sub-Pixel Refinement
      │
      ▼
Final Transformation
      │
      ▼
Independent Evaluation
```

The supplied project feedback repeatedly emphasizes establishing **one measurable end-to-end result before expanding the system**. 

Future research therefore begins from a measured V1 foundation rather than replacing it prematurely.

---

# 4. Research Lifecycle

Future research should follow the broader ChandraMap research lifecycle:

```text
Current Research
      │
      ▼
V1 Experiments
      │
      ▼
Measured Evidence
      │
      ▼
Research Findings
      │
      ▼
Future Research
      │
      ▼
New Experiment
      │
      ▼
Benchmark Evaluation
      │
      ▼
Reproducibility Check
      │
      ▼
Validated Improvement
      │
      ▼
Future ChandraMap Version
```

A future idea should move through this lifecycle rather than directly becoming production functionality.

---

# 5. Relationship to the Main Repository

The future research directory is one part of a larger research system.

```text
research/
├── README.md
├── literature/
│   └── README.md
├── notes/
│   ├── lunar-registration.md
│   ├── illumination-invariance.md
│   ├── scale-invariance.md
│   ├── sensor-modality.md
│   ├── geometric-models.md
│   └── ground-truth-design.md
└── future/
    └── README.md

experiments/
├── templates/
├── v1/
└── ...

benchmarks/
├── v1/
└── ...

data/
├── ...
└── ...

results/
└── ...
```

The exact contents of `research/future/` may expand over time.

Only files that are actually created and maintained should be presented as existing implementation artifacts.

---

# 6. Current V1 Foundation

The future roadmap is based on the current ChandraMap V1 research structure.

Confirmed V1 experiment documentation includes:

* `experiments/v1/README.md`
* `experiments/v1/baseline/EXP-001-sift-baseline/README.md`
* `experiments/v1/preprocessing/EXP-002-scale-pyramid/README.md`
* `experiments/v1/preprocessing/EXP-003-gradient-representation/README.md`
* `experiments/v1/geometry/EXP-004-affine-vs-homography/README.md`
* `experiments/v1/geometry/EXP-005-residual-analysis/README.md`
* `experiments/v1/refinement/EXP-006-subpixel-refinement/README.md`

The current V1 progression can be summarized as:

```text
EXP-001
SIFT Baseline
   │
   ▼
EXP-002
Scale Pyramid
   │
   ▼
EXP-003
Gradient Representation
   │
   ▼
EXP-004
Affine vs Homography
   │
   ▼
EXP-005
Residual Analysis
   │
   ▼
EXP-006
Sub-Pixel Refinement
```

These experiments establish the foundation from which future research can proceed.

---

# 7. Current Scientific Priorities

The project feedback identifies several important research problems:

1. sensor-aware preprocessing;
2. physically meaningful multi-scale handling;
3. illumination robustness;
4. geometric verification;
5. residual analysis;
6. sub-pixel refinement;
7. independent accuracy evaluation;
8. stronger local matching;
9. global retrieval where necessary;
10. additional sensor coverage;
11. DEM/sensor-geometry-aware registration;
12. downstream products such as mosaics.

The important distinction is that these are **research directions with different maturity levels**, not one single implementation milestone.

---

# 8. Future Research Principles

Future ChandraMap research should follow these principles.

## 8.1 Evidence before complexity

A more sophisticated algorithm should earn its place through measurable improvement.

> **Do not add a method because it is newer. Add it because the experiment demonstrates why it is needed.**

The supplied feedback explicitly recommends starting with SIFT and then testing stronger approaches on the same image pairs rather than placing every algorithm into one pipeline. 

---

## 8.2 Preserve the baseline

Future experiments should continue to compare against an interpretable baseline.

The V1 SIFT path provides that reference.

A future method should therefore make comparisons such as:

```text
V1 Baseline
     │
     ├── SIFT
     │
     └── Future Method
              │
              ▼
       Same image pairs
              │
              ▼
       Same evaluation
```

---

## 8.3 Measure difficult cases

A future method should be evaluated where the current method has a known or suspected weakness.

Relevant stress conditions include:

* Sun-angle stress
* scale stress
* modality stress
* geometry stress
* low-feature terrain

The feedback explicitly recommends this stress-test structure. 

---

## 8.4 Preserve failure cases

A method that fails should not have its failure removed from the benchmark.

Failure can reveal:

* scale limits;
* modality limits;
* geometric-model limitations;
* insufficient feature density;
* poor representation;
* illumination sensitivity;
* ground-truth uncertainty.

Future research should therefore preserve both:

```text
Successful cases
+
Failure cases
```

---

## 8.5 Avoid unsupported claims

Future documentation must not state that a planned method is:

* lunar-invariant;
* sensor-invariant;
* illumination-invariant;
* globally accurate;
* sub-pixel accurate in physical metres;
* production-ready;

unless the relevant evidence exists.

---

# 9. Future Research Classification

Every future research document should ideally contain a status block similar to:

```markdown
## Research Status

- Status: Exploratory / Proposed / Planned / Validated / Promoted
- Target Version: [TBD]
- Related V1 Experiment: [TBD]
- Benchmark Required: Yes
- Implementation Status: Not implemented
- Evidence Status: [TBD]
```

The exact template can evolve with the repository.

---

# 10. Future Research Areas

The current roadmap is organized around research problems rather than a list of technologies.

---

## 10.1 Stronger Local Matching

### Motivation

V1 establishes SIFT as a simple and explainable baseline.

Cross-sensor and low-feature conditions may expose limitations in traditional local feature matching.

The supplied feedback identifies several research directions:

* ALIKED + LightGlue
* LoFTR
* RIFT
* CFOG

These are **research candidates**, not automatically selected production components. 

### Research questions

* Does a learned matcher improve difficult lunar cases?
* Does it improve modality stress?
* Does it improve low-feature terrain?
* Does it remain reliable under large scale differences?
* Does it improve independent check-point accuracy?
* Does it improve spatial coverage rather than only match count?

### Promotion evidence

A stronger matcher should demonstrate measurable improvement against the V1 baseline on controlled pairs.

### Status

**Research direction — not assumed implemented.**

---

# 11. Sensor-Aware Registration

### Motivation

OHRC, TMC-2, and IIRS are not equivalent image sources.

The project feedback recommends separate sensor-aware preparation rather than forcing every sensor through one identical path. 

### Research directions

Potential future work includes:

* sensor-specific preprocessing;
* structural representations;
* cross-modal descriptors;
* sensor-aware matching;
* modality-specific scale handling;
* cross-sensor evaluation.

### IIRS-specific direction

IIRS requires particular attention because it is hyperspectral/IR data rather than a conventional single-band image.

Potential representations include:

* selected spectral band;
* PCA;
* spectral composite;
* structural representation.

The project materials explicitly recommend starting with a simple 2D representation rather than immediately building a complex hyperspectral matching network. 

### Promotion evidence

A future sensor-aware method should show:

* improved modality-stress performance;
* independent registration accuracy;
* acceptable spatial coverage;
* reproducible preprocessing;
* clearly documented sensor assumptions.

### Status

**Proposed / research direction.**

---

# 12. Illumination-Robust Registration

### Motivation

Sun-angle differences can change lunar appearance substantially.

They affect:

* shadows;
* local contrast;
* visible terrain boundaries;
* crater appearance;
* ridge visibility.

Brightness normalization cannot fully reverse changes caused by illumination geometry.

The project feedback specifically recommends treating illumination handling as an experiment and comparing raw intensity with structural representations such as gradients and edges. 

### Future research directions

Potential investigations include:

* illumination-aware preprocessing;
* gradient/edge representations;
* phase-based representations;
* shadow-aware correspondence;
* photometric correction where metadata supports it;
* illumination-conditioned matching.

### Required evaluation

Use controlled pairs containing:

```text
Similar illumination
        vs
Strongly different illumination
```

Report the performance degradation rather than only the successful result.

### Status

**Proposed / research direction.**

---

# 13. DEM-Aware Registration

### Motivation

A global affine transform or homography may not explain all lunar registration errors.

The Moon contains significant terrain relief, and viewing geometry can produce spatially varying differences.

The feedback recommends using available DEM/sensor geometry when residuals suggest that a single global model is insufficient. 

### Research direction

DEM-aware registration could investigate:

```text
Image Correspondences
        │
        ▼
Sensor / Camera Geometry
        +
        ▼
DEM / Terrain Information
        │
        ▼
Geometry-Aware Registration
        │
        ▼
Independent Evaluation
```

### Research questions

* Can DEM information explain structured residual fields?
* Can terrain-aware geometry reduce systematic registration error?
* When does a global transform become insufficient?
* Can DEM information improve cross-view registration?
* What additional metadata is required?

### Status

**Future research direction.**

A dedicated future document such as:

`research/future/DEM_AWARE_REGISTRATION.md`

has been identified in the project planning context, but its implementation status must remain separate from this roadmap.

---

# 14. Lunar Mosaic Research

### Motivation

A registered image can become an input to downstream products such as mosaics.

However:

> **The mosaic is downstream of reliable correspondence and registration.**

The supplied project feedback explicitly emphasizes that correspondences and measurable registration quality are the core problem, while the mosaic is a downstream demonstration. 

### Future research

Potential work includes:

* multi-image alignment;
* overlap graph construction;
* transformation chaining;
* seam handling;
* exposure/appearance consistency;
* global bundle adjustment;
* mosaic quality evaluation;
* map-projected lunar image products.

### Research questions

* How does pairwise registration error accumulate across many images?
* How should transformations be globally optimized?
* How can inconsistent overlaps be detected?
* How should seams be evaluated?
* How can mosaic quality be separated from correspondence quality?

### Status

**Future research direction.**

A dedicated research document:

`research/future/LUNAR_MOSAIC.md`

is part of the planned research documentation context.

---

# 15. Multi-Image Registration and Global Consistency

Pairwise registration is only one part of a larger mapping problem.

For multiple images:

```text
Image A ───── Image B
   │              │
   │              │
   └──── Image C ─┘
```

independent pairwise transformations may become mutually inconsistent.

Future research may investigate:

* image-overlap graphs;
* transformation graphs;
* global optimization;
* loop-closure consistency;
* bundle-style adjustment;
* global residual analysis.

### Important constraint

This should only be pursued after pairwise correspondence and registration are sufficiently measurable.

A global optimizer cannot compensate indefinitely for incorrect correspondences.

---

# 16. Global Retrieval

Global retrieval becomes relevant when the source image's location is not already sufficiently constrained by metadata.

The project feedback distinguishes global retrieval from local matching:

```text
Global Retrieval
     │
     ▼
Candidate Region
     │
     ▼
Local Matching
     │
     ▼
Geometric Verification
```

FAISS, for example, is an indexing/search component rather than a complete retrieval solution. A reference database must first be prepared with suitable descriptors and metadata. 

### Future research questions

* What global representation is robust across lunar sensors?
* How should reference tiles be generated?
* What effective scales should be indexed?
* How should metadata constrain search?
* What Recall@K is achieved?
* When is image retrieval unnecessary because geolocation metadata already provides sufficient constraints?

### Status

**Future research direction.**

---

# 17. Multi-Scale Global Search

The current V1 scale-pyramid work establishes a foundation for future coarse-to-fine search.

The future direction is:

```text
Large Search Space
       │
       ▼
Coarse Reference Pyramid
       │
       ▼
Candidate Regions
       │
       ▼
Finer Scale Search
       │
       ▼
Local Matching
       │
       ▼
Sub-Pixel Refinement
```

The project feedback recommends searching at comparable effective ground scales before fine matching. 

### Research questions

* How should scale levels be selected?
* How should candidate regions be ranked?
* Can retrieval and local matching share representations?
* How should coarse localization uncertainty propagate into fine registration?

---

# 18. Advanced Geometric Models

V1 evaluates affine and homography models.

Future research can investigate more complex geometry only when residual evidence justifies it.

Potential directions include:

* local/piecewise transformations;
* terrain-aware transformations;
* DEM-assisted models;
* sensor-model-based registration;
* spatially varying warps.

### Important rule

> **Use the simplest transformation that explains the measured residuals.**

A more flexible transformation should not be promoted merely because it produces a lower fitting residual.

Independent check points remain necessary.

---

# 19. Local / Piecewise Registration

A global transform can fail when different parts of an image experience different geometric displacement.

Future research may therefore investigate:

```text
Global Model
     │
     ├── Region A
     ├── Region B
     ├── Region C
     └── Region D
             │
             ▼
      Local Models
```

Potential benefits:

* handling terrain relief;
* handling residual spatial variation;
* improving local registration.

Potential risks:

* overfitting;
* hiding poor correspondences;
* unstable extrapolation;
* discontinuities;
* increased complexity.

The project feedback specifically warns that flexible warping should only be used after control points are accurate and well distributed. 

---

# 20. Sub-Pixel Registration Beyond V1

V1 includes sub-pixel refinement as EXP-006.

Future research can investigate whether refinement should become:

* sensor-aware;
* representation-aware;
* patch-adaptive;
* uncertainty-aware;
* geometry-aware.

Potential methods may include:

* correlation-based refinement;
* phase-based refinement;
* local patch optimization;
* planetary registration tools;
* uncertainty estimation.

The key principle remains:

```text
Candidate Matches
       ↓
Verified Inliers
       ↓
Sub-Pixel Refinement
       ↓
Final Transform
       ↓
Independent Evaluation
```

Sub-pixel refinement should not be used as a mechanism for turning weak correspondences into apparently precise measurements.

---

# 21. Correspondence Uncertainty

A future system could represent uncertainty explicitly rather than returning only point coordinates.

For a correspondence:

$$
p_s \leftrightarrow p_r
$$

future work could potentially estimate:

$$
p_r \sim P(p_r \mid p_s)
$$

or another appropriate uncertainty representation.

Possible sources of uncertainty include:

* descriptor ambiguity;
* image noise;
* interpolation;
* sensor resolution;
* local texture;
* geometric model uncertainty;
* ground-truth uncertainty.

### Status

**Exploratory research direction.**

No specific uncertainty model is currently established by the V1 documentation.

---

# 22. Learned Lunar Representations

Learned features and descriptors are a possible future direction.

Potential research includes:

* lunar-specific feature learning;
* multimodal contrastive learning;
* domain adaptation;
* self-supervised correspondence learning;
* sensor-specific embeddings.

However, pretrained terrestrial models should not automatically be described as lunar-invariant.

The supplied feedback explicitly warns that domain shift can affect learned matchers and that performance must be measured on lunar data. 

---

# 23. Synthetic Lunar Training Data

The SIH problem material identifies synthetic lunar augmentations such as:

* Sun-angle variation;
* rotation;
* scale;
* contrast;

as potential training or robustness mechanisms. 

Future research could investigate whether synthetic transformations approximate the failure modes encountered in real lunar imagery.

### Important limitation

Synthetic augmentation should not automatically be treated as equivalent to real sensor variation.

For example:

```text
Synthetic brightness change
        ≠
Real spectral sensor difference
```

and:

```text
Synthetic shadow manipulation
        ≠
Full physical illumination change
```

Synthetic data should therefore supplement, not replace, real cross-sensor evaluation.

---

# 24. Sensor and Modality Expansion

The current problem context includes:

* OHRC
* TMC-2
* IIRS
* lunar reference imagery

The SIH source material also identifies possible additional data sources such as:

* LRO NAC
* LRO WAC
* Kaguya/SELENE TC

as potential training or research data. 

Additional sensors should not be added simply to increase the dataset count.

Each new modality introduces questions about:

* spatial scale;
* spectral response;
* radiometry;
* geometry;
* projection;
* metadata;
* ground truth;
* representation.

### Status

**Future expansion direction.**

---

# 25. Ground-Truth Expansion

Future research depends on trustworthy evaluation.

Potential directions include:

* larger independent check-point sets;
* sensor-specific ground truth;
* DEM-assisted reference points;
* multi-image control networks;
* uncertainty-aware ground truth;
* cross-sensor validation sets.

The key rule remains:

> **Do not fit and evaluate the transformation on exactly the same points.**

The feedback explicitly recommends independent check points or challenge ground truth for registration evaluation. 

---

# 26. Benchmark Evolution

Future research should not be promoted without benchmark support.

A future benchmark should continue to measure:

* inlier count;
* inlier ratio;
* spatial coverage;
* independent check-point RMSE;
* residual behavior;
* ground error where meaningful;
* runtime;
* failure rate.

The stress-test structure should continue to include:

```text
Easy
  │
Sun-Angle
  │
Scale
  │
Modality
  │
Geometry
  │
Low-Feature
```

Future benchmark versions may add additional cases only when a research question requires them.

---

# 27. Future Research Promotion Gate

A future research idea should pass through a promotion process.

```text
Research Idea
      │
      ▼
Scientific Hypothesis
      │
      ▼
Controlled Experiment
      │
      ▼
Benchmark Comparison
      │
      ▼
Independent Evaluation
      │
      ▼
Failure Analysis
      │
      ▼
Reproducibility
      │
      ▼
Promotion Decision
```

Possible outcomes:

```text
            ┌──► Promoted
            │
Experiment ─┼──► More Research Needed
            │
            ├──► Deferred
            │
            └──► Rejected
```

The outcome should be recorded explicitly.

---

# 28. Minimum Evidence Before Promotion

A proposed future capability should normally provide:

### 1. Clear research question

What problem is being solved?

### 2. Baseline

What existing ChandraMap method is being compared against?

### 3. Controlled data

What image pairs or stress cases are being used?

### 4. Measured metrics

At minimum, where applicable:

* RMSE;
* inlier ratio;
* spatial coverage;
* runtime;
* failure rate.

### 5. Independent evaluation

Were evaluation points excluded from transformation fitting?

### 6. Failure analysis

Where does the method fail?

### 7. Reproducibility

Can the experiment be repeated from documented configuration and data?

### 8. Scientific interpretation

Does the evidence actually support the proposed conclusion?

---

# 29. Future Research Must Connect to Experiments

A future research note should not remain disconnected from experimental work.

The expected relationship is:

```text
research/future/
       │
       ▼
Research Hypothesis
       │
       ▼
experiments/
       │
       ▼
results/
       │
       ▼
benchmarks/
       │
       ▼
Research Finding
       │
       ▼
Future Decision
```

A future document should identify the experiment that will test it once that experiment exists.

---

# 30. Future Research Must Connect to Research Notes

The repository already contains foundational notes such as:

* `research/notes/lunar-registration.md`
* `research/notes/illumination-invariance.md`
* `research/notes/scale-invariance.md`
* `research/notes/sensor-modality.md`
* `research/notes/geometric-models.md`
* `research/notes/ground-truth-design.md`

These explain the scientific concepts.

The `research/future/` directory answers the next question:

> **What should be investigated using those concepts?**

For example:

```text
sensor-modality.md
       │
       ▼
Sensor-aware research question
       │
       ▼
Future experiment
       │
       ▼
Measured cross-sensor result
```

---

# 31. Future Research Must Connect to Benchmarks

Benchmarks provide the validation boundary.

A future method should not define its own success criteria after seeing its results.

Instead:

```text
Research Question
      │
      ▼
Predefined Evaluation
      │
      ▼
Experiment
      │
      ▼
Benchmark Results
      │
      ▼
Interpretation
```

This reduces the risk of selecting metrics that make a new method appear successful.

---

# 32. Future Research Must Connect to V1

Future work should reuse V1 where appropriate.

For example:

| V1 foundation            | Future extension                       |
| ------------------------ | -------------------------------------- |
| SIFT baseline            | Learned / multimodal matching          |
| Scale pyramid            | Multi-scale global retrieval           |
| Gradient representation  | Cross-modal structural representations |
| Affine/homography        | DEM-aware or local geometry            |
| Residual analysis        | Spatially varying geometry             |
| Sub-pixel refinement     | Uncertainty-aware refinement           |
| Independent check points | Larger multi-sensor ground truth       |
| Stress matrix            | Expanded cross-sensor benchmark        |

This prevents future development from becoming disconnected from measured V1 behavior.

---

# 33. Proposed Future Version Evolution

Future versions should emerge from validated research rather than from a predetermined list of technologies.

A conceptual evolution is:

```text
V1
Measurable Registration Foundation
        │
        ▼
V2
Validated Robustness Extensions
        │
        ├── stronger matching
        ├── improved modality handling
        ├── improved scale handling
        └── expanded benchmarks
        │
        ▼
V3
Geometry / Multi-Image Research
        │
        ├── DEM-aware registration
        ├── local geometry
        ├── multi-image consistency
        └── larger retrieval
        │
        ▼
V4
Integrated Lunar Registration / Mapping System
        │
        ├── broader sensor coverage
        ├── scalable retrieval
        ├── robust registration
        ├── multi-image products
        └── mature reproducibility
```

These version descriptions are **roadmap concepts**, not claims that those versions already exist or that every listed capability will necessarily be implemented.

---

# 34. Research Maturity Levels

A useful maturity model is:

### Level 0 — Idea

Concept identified.

```text
No experiment
No implementation
No evidence
```

---

### Level 1 — Hypothesis

A scientific reason exists to investigate the idea.

```text
Research question defined
Expected mechanism described
```

---

### Level 2 — Prototype

A minimal implementation or experimental procedure exists.

```text
Implementation / method exists
Still exploratory
```

---

### Level 3 — Controlled Experiment

The method has been tested against a baseline.

```text
Same data
Same evaluation
Controlled variables
```

---

### Level 4 — Benchmark Evidence

The method has been evaluated across relevant stress cases.

```text
Metrics
Failures
Runtime
Independent evaluation
```

---

### Level 5 — Reproducible Finding

The result can be reproduced and its interpretation is stable.

---

### Level 6 — Candidate for Production

The evidence justifies engineering integration.

---

### Level 7 — Integrated Capability

The feature is implemented, tested, documented, and part of the supported system.

Future research should not skip directly from Level 0 to Level 7.

---

# 35. Current Future Research Status

The roadmap should currently be interpreted approximately as follows:

| Research direction              | Current role                  | Implementation status                                   |
| ------------------------------- | ----------------------------- | ------------------------------------------------------- |
| Stronger local matching         | Research direction            | Not established as final method                         |
| Sensor-aware registration       | Research direction            | V1 foundation exists; future extensions remain research |
| Illumination robustness         | Research direction            | Requires controlled stress testing                      |
| DEM-aware registration          | Future direction              | Not implemented unless separately documented            |
| Lunar mosaic                    | Downstream future research    | Not the primary V1 registration objective               |
| Global retrieval                | Conditional future capability | Not required for every known-overlap case               |
| Multi-image registration        | Future direction              | Not established as V1                                   |
| Learned lunar representations   | Exploratory                   | Not established                                         |
| Synthetic training/augmentation | Exploratory                   | Requires validation against real data                   |
| Expanded sensor coverage        | Future direction              | Requires sensor-specific validation                     |
| Advanced geometric models       | Research direction            | Should follow residual evidence                         |
| Uncertainty estimation          | Exploratory                   | Not established                                         |

This table is a roadmap classification, not an implementation inventory.

---

# 36. Explicitly Not Implemented by This Roadmap

The existence of a future research document does **not** mean that ChandraMap currently provides:

* universal sensor invariance;
* universal illumination invariance;
* global lunar retrieval;
* DEM-aware registration;
* globally optimized multi-image registration;
* a production lunar mosaic engine;
* lunar-specific learned feature models;
* hyperspectral end-to-end matching;
* guaranteed sub-pixel physical accuracy;
* arbitrary cross-sensor registration;
* automatic success on low-feature terrain;
* automatic handling of all viewing geometries.

Unless a separate implementation document and measured result establish such a capability, it should remain marked as:

**Not implemented / Research direction.**

---

# 37. Things Future Research Must Not Do

## Do not optimize only for match count

More matches can be worse if they are incorrect or spatially clustered.

---

## Do not optimize only for visual appearance

A visually attractive overlay can hide geometric errors.

---

## Do not use fitting residual as the only accuracy metric

Independent check-point evaluation is required for meaningful generalization.

---

## Do not use upsampling as a substitute for spatial information

More pixels do not mean more terrain detail.

---

## Do not assume pretrained models are lunar-invariant

Learned terrestrial features require lunar evaluation.

---

## Do not force every sensor into one identical pipeline

OHRC, TMC-2, and IIRS have different measurement characteristics.

---

## Do not add advanced algorithms without a research question

Every additional method should have a reason to exist.

---

## Do not remove failures

Failures define the boundaries of the method.

---

## Do not hide uncertainty behind decorative confidence scores

Real RMSE, inlier ratio, spatial coverage, runtime, and failure rate are more useful than unmeasured percentage claims. 

---

# 38. Recommended Future Experiment Template

Future experiments should follow the project's existing experiment structure:

`experiments/templates/EXPERIMENT_TEMPLATE.md`

A future research experiment should define:

```text
Research Question
      │
      ▼
Hypothesis
      │
      ▼
Baseline
      │
      ▼
Controlled Variables
      │
      ▼
Independent Variable
      │
      ▼
Dataset / Image Pairs
      │
      ▼
Evaluation Metrics
      │
      ▼
Failure Criteria
      │
      ▼
Results
      │
      ▼
Interpretation
      │
      ▼
Promotion Decision
```

This makes future research comparable with V1 research.

---

# 39. Recommended Future Benchmark Comparison

Where applicable, future methods should be compared through the same test pairs:

```text
Same Image Pairs
       │
       ├── V1 SIFT baseline
       │
       ├── New method
       │
       └── Full future pipeline
                │
                ▼
       Same benchmark metrics
```

The project feedback specifically recommends comparing the baseline, a stronger matcher, and the complete sensor-aware/multi-scale pipeline on the same pairs. 

This helps identify whether improvement comes from:

* the matcher;
* preprocessing;
* scale handling;
* geometry;
* refinement;
* or their interaction.

---

# 40. Future Research Evidence Package

Every mature future research project should preserve:

### Input evidence

* source image;
* reference image;
* sensor identity;
* product metadata;
* pixel scale;
* projection;
* illumination/viewing information where available.

### Correspondence evidence

* candidate matches;
* rejected matches;
* verified inliers;
* spatial distribution.

### Geometry evidence

* transformation;
* residual vectors;
* residual statistics.

### Accuracy evidence

* independent check-point RMSE;
* robust error statistics;
* ground error where meaningful.

### System evidence

* runtime;
* failure rate;
* resource requirements.

### Reproducibility evidence

* configuration;
* version;
* seed where applicable;
* data provenance.

---

# 41. Research Roadmap Decision Matrix

A future idea can be evaluated using the following questions:

| Question                                  | Required answer                       |
| ----------------------------------------- | ------------------------------------- |
| What problem does it solve?               | Explicit                              |
| Why is V1 insufficient?                   | Evidence or clearly stated hypothesis |
| What is the baseline?                     | Defined                               |
| What data tests it?                       | Defined                               |
| What metric measures success?             | Defined                               |
| What stress case matters?                 | Defined                               |
| What failure would reject the hypothesis? | Defined                               |
| Can it be reproduced?                     | Required                              |
| Does it improve independent accuracy?     | Preferred evidence                    |
| Does it improve spatial coverage?         | Where applicable                      |
| Does runtime remain acceptable?           | Required for system integration       |
| Is the improvement sensor-specific?       | Report separately                     |
| Is it production-ready?                   | Only after evidence                   |

---

# 42. How a Future Idea Becomes a ChandraMap Feature

The intended promotion path is:

```text
Research Idea
      │
      ▼
Documented Hypothesis
      │
      ▼
Future Research Note
      │
      ▼
Experiment
      │
      ▼
Controlled Benchmark
      │
      ▼
Failure Analysis
      │
      ▼
Reproducibility
      │
      ▼
Research Finding
      │
      ├───────────────┐
      ▼               ▼
Reject / Defer     Promote
                      │
                      ▼
             Implementation
                      │
                      ▼
                  Tests
                      │
                      ▼
               Documentation
                      │
                      ▼
              Future Version
```

This keeps the research repository scientifically traceable.

---

# 43. Future Research Directory Navigation

The future directory is expected to contain research documents as they are created.

Known/planned research documents from the current project context include:

* `research/future/README.md`
* `research/future/LUNAR_MOSAIC.md`
* `research/future/DEM_AWARE_REGISTRATION.md`

These documents should remain research/planning artifacts until their corresponding ideas have been experimentally validated and, where appropriate, implemented elsewhere in the repository.

Additional future documents should only be added when a real research direction warrants a dedicated document.

---

# 44. How to Add a New Future Research Document

Before creating a new file under `research/future/`, answer:

1. What research problem does this document represent?
2. Is it already covered by an existing research note?
3. What V1 limitation motivates it?
4. What experiment could test it?
5. What benchmark would evaluate it?
6. What evidence would cause the idea to be rejected?
7. What future version could potentially consume the result?

A new future document should not exist simply to list an algorithm.

---

# 45. Recommended Structure for Future Research Documents

Future research documents should generally contain:

```text
Title
Research Status
Problem
Motivation
Current V1 Limitation
Research Question
Hypothesis
Proposed Approach
Assumptions
Related V1 Experiments
Data Requirements
Evaluation Plan
Stress Cases
Success Criteria
Failure Criteria
Risks
Reproducibility Requirements
Promotion Criteria
Future Version Relevance
Open Questions
```

The exact structure can vary according to the research topic.

---

# 46. Relationship With Implementation

Research documentation and implementation should remain separate.

```text
research/future/
      │
      │ hypothesis
      ▼
experiments/
      │
      │ evidence
      ▼
results/
      │
      │ validated design
      ▼
src/
      │
      │ implementation
      ▼
tests/
      │
      │ verification
      ▼
benchmarks/
      │
      │ system evidence
      ▼
future release
```

A future research document should not imply that corresponding code already exists.

---

# 47. Relationship With the Benchmark

The benchmark is the gatekeeper for scientific claims.

A future method should not define success only through:

* visual quality;
* number of matches;
* subjective confidence;
* one successful example.

Where applicable, the benchmark should evaluate:

$$
\text{Registration Quality}
=
f(
\text{Accuracy},
\text{Coverage},
\text{Robustness},
\text{Reliability},
\text{Runtime}
)
$$

The exact acceptance thresholds remain governed by the benchmark documentation and current project status.

---

# 48. Research Integrity Rules

Future research should preserve the following rules:

### Rule 1

Never report an unmeasured result as a percentage.

### Rule 2

Never convert a numerical sub-pixel estimate into physical accuracy without appropriate scale and projection information.

### Rule 3

Never call candidate matches verified inliers before geometric verification.

### Rule 4

Never use evaluation points to fit the transformation and then present that same fitting error as independent accuracy.

### Rule 5

Never hide modality-specific failures inside a single average.

### Rule 6

Never treat a learned method as lunar-invariant without testing.

### Rule 7

Never treat upsampling as information recovery.

### Rule 8

Never use a flexible warp to conceal weak or poorly distributed correspondences.

### Rule 9

Never remove failed experiments simply because they make the result less attractive.

### Rule 10

Never promote a future research idea into the main architecture without evidence.

---

# 49. Suggested Future Milestone Sequence

The current project feedback suggests a practical progression from a measurable baseline toward broader capabilities. 

### Milestone A — One measurable pair

```text
Known source/reference pair
        ↓
SIFT
        ↓
RANSAC
        ↓
Transformation
        ↓
Independent error
```

---

### Milestone B — Scale + illumination

```text
Reference pyramid
+
Structure-focused representation
        ↓
Stress testing
```

---

### Milestone C — Small retrieval database

```text
Reference tiles
        ↓
Global descriptors
        ↓
Index
        ↓
Top-K candidates
```

---

### Milestone D — Stronger local matcher

```text
SIFT
vs
ALIKED + LightGlue
or
LoFTR
```

---

### Milestone E — Sub-pixel refinement

```text
Verified inliers
        ↓
Local refinement
        ↓
Final transformation
        ↓
Independent RMSE
```

---

### Milestone F — Sensor expansion

```text
OHRC / TMC-2
        ↓
IIRS
        ↓
Separate sensor-specific evaluation
```

These milestones are a research progression, not a guarantee that every stage will be implemented.

---

# 50. Future Version Promotion Logic

A research capability should be considered for a future ChandraMap version only when:

* the research question is answered sufficiently;
* the method has a reproducible implementation;
* the benchmark shows measurable behavior;
* failure cases are understood;
* independent evaluation exists where appropriate;
* resource requirements are understood;
* documentation is complete;
* tests exist;
* the capability provides a meaningful improvement or enables a justified new use case.

A future version should therefore represent **validated engineering**, not accumulated experiments.

---

# 51. Roadmap Status Language

Use precise language throughout future research documentation.

### Use

* `Exploratory`
* `Proposed`
* `Planned`
* `Under investigation`
* `Experimental`
* `Validated`
* `Candidate for promotion`
* `Deferred`
* `Not implemented`
* `To be verified`
* `TBD`

### Avoid

* `Solved`
* `Guaranteed`
* `Fully invariant`
* `Production-ready`

unless those claims are supported by project evidence.

---

# 52. Open Research Questions

The following questions remain important for future ChandraMap development:

### Data

* Which exact product types should be supported?
* Which metadata fields are consistently available?
* Which reference products are authoritative for each benchmark?

### Modality

* Which representations transfer best across OHRC, TMC-2, and IIRS?
* How should hyperspectral IIRS data be reduced to a registration-friendly representation?
* Which structural features remain stable across sensors?

### Scale

* How should effective matching scale be selected automatically?
* How much fine detail can each sensor actually support?
* How should coarse retrieval and fine registration interact?

### Illumination

* Which representations remain stable under large Sun-angle changes?
* Can available illumination metadata improve correspondence?
* How should shadow-dependent structures be rejected?

### Matching

* When does SIFT fail?
* When does a learned matcher provide measurable improvement?
* Do multimodal remote-sensing methods improve independent accuracy?

### Geometry

* When is affine sufficient?
* When is homography justified?
* When do residuals require local or DEM-aware geometry?

### Refinement

* How much independent accuracy improvement does sub-pixel refinement provide?
* How should refinement uncertainty be represented?

### Retrieval

* When is metadata sufficient?
* When is global retrieval necessary?
* What reference representation should be indexed?

### Multi-image registration

* How should pairwise transformations become globally consistent?
* How should accumulated registration error be controlled?

---

# 53. Long-Term Research Vision

The long-term research direction is not simply to build a larger image-matching pipeline.

The intended progression is toward a system that can reason about:

```text
Lunar Surface
      │
      ▼
Sensor / Product Characteristics
      │
      ▼
Illumination + Viewing Geometry
      │
      ▼
Scale + Representation
      │
      ▼
Correspondence
      │
      ▼
Geometric Verification
      │
      ▼
Registration
      │
      ▼
Independent Accuracy
      │
      ▼
Multi-Image Consistency
      │
      ▼
Lunar Mapping Products
```

The scientific objective remains reliable correspondence and registration.

Downstream products such as mosaics, global maps, or visualization systems should depend on that foundation rather than substitute for it.

---

# 54. Practical Rule for Contributors

When considering a new research idea, ask:

> **What measured V1 problem does this solve?**

Then ask:

> **What experiment would prove that it solves it?**

Then:

> **What result would convince us that it should become part of ChandraMap?**

If these questions cannot yet be answered, the idea should remain exploratory.

---

# 55. Final Roadmap Principle

ChandraMap's future should be driven by evidence rather than by algorithm count.

The project feedback repeatedly emphasizes the same engineering direction:

```text
Start small
   ↓
Use real lunar data
   ↓
Build one measurable baseline
   ↓
Measure failure
   ↓
Form a research hypothesis
   ↓
Test one improvement
   ↓
Benchmark on the same pairs
   ↓
Keep the failures
   ↓
Promote only validated improvements
```

The most important future capability is therefore not simply a more advanced matcher, a larger model, a more flexible warp, or a larger pipeline.

It is a **scientifically defensible progression from measured V1 limitations to validated improvements**.

That principle should govern every document added under `research/future/`.

---

## 56. Source Context

This roadmap is grounded in the project materials and repository context provided for ChandraMap, including:

* `SIH26166 Silarlar PS.pdf`
* `Aryan_SIH_26166_Lunar_Image_Correspondence_Feedback.pptx`
* `Aryan_Lunar_Image_Registration_Feedback.pdf`

The source material emphasizes:

* one measurable end-to-end result before broad expansion;
* sensor-aware handling of OHRC, TMC-2, and IIRS;
* physically meaningful scale handling;
* illumination stress testing;
* SIFT as an explainable baseline;
* controlled evaluation of stronger matchers;
* RANSAC and geometric verification;
* independent check-point evaluation;
* spatial coverage;
* residual inspection;
* sub-pixel refinement after verified inliers;
* gradual sensor expansion;
* retrieval only where necessary;
* future use of DEM/sensor geometry where residual evidence justifies it.  

The SIH source material also identifies additional potential lunar data sources and synthetic augmentation directions for future research, while emphasizing that the core correspondence problem remains the primary deliverable. 

---

## 57. Maintenance

This README should be updated when:

* a new future research direction is formally introduced;
* an exploratory direction receives a dedicated research document;
* a future experiment is created;
* a research direction is validated;
* a research direction is rejected or deferred;
* a capability is promoted into implementation;
* a new ChandraMap version adopts a research result.

When updating this document, preserve the distinction between:

```text
Research Idea
      ≠
Experiment
      ≠
Measured Finding
      ≠
Implementation
      ≠
Supported Feature
```

That distinction is essential for maintaining a trustworthy scientific and engineering history of ChandraMap.
