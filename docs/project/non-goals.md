# ChandraMap Project Non-Goals

This document defines what **ChandraMap does not treat as part of its primary scientific mission or current definition of project success**.

A non-goal does **not** necessarily mean:

> ChandraMap can never support this capability.

Instead, it means:

> **This capability is not currently required to prove that ChandraMap's scientific core is successful.**

Some non-goals may later become:

- downstream applications
- optional integrations
- research experiments
- benchmark extensions
- roadmap items
- separate projects

without becoming part of the current core mission.

The central ChandraMap boundary remains:

```text
Source Lunar Observation
        +
Reference Observation
        ↓
Candidate Correspondence
        ↓
Geometric Verification
        ↓
Transformation
        ↓
Registration
        ↓
Measured Quality
        ↓
Accept / Reject
```

Everything else should be judged by whether it meaningfully supports this core.

---

## 1. Purpose

The purpose of this document is to prevent:

- uncontrolled scope expansion
- architecture driven by presentation rather than science
- benchmark contamination
- unnecessary infrastructure
- technology adoption for appearance alone
- downstream applications overshadowing correspondence quality
- documentation that overstates project capability
- future research ideas being mistaken for current requirements

ChandraMap should remain focused enough that progress can be evaluated scientifically.

A larger repository is not automatically a better research system.

---

## 2. How to Interpret a Non-Goal

### A Non-Goal Does Not Mean Impossible

If this document says:

> Building a global lunar mosaic is not a core ChandraMap goal.

that does **not** mean mosaics are technically impossible or forbidden.

It means mosaic generation should not define whether the core correspondence and registration system succeeds.

---

### A Non-Goal Does Not Mean Never

Project scope can change.

A current non-goal may later become relevant because:

- the scientific mission expands
- new evidence demonstrates its necessity
- benchmark scope changes
- a downstream application becomes a supported objective
- the roadmap deliberately promotes it

Such changes should be made explicitly rather than introduced accidentally through implementation work.

---

### A Non-Goal Is Not the Same as Unsupported

This document describes **scope and intent**, not an implementation-support matrix.

Prefer:

> X is not a core project goal.

over:

> ChandraMap does not support X.

unless repository evidence confirms the implementation claim.

---

## 3. Core Project Boundary

ChandraMap's scientific core is:

```text
Lunar Observation Pair
        ↓
Correspondence
        ↓
Geometric Verification
        ↓
Transformation Estimation
        ↓
Registration
        ↓
Evaluation
        ↓
Accept / Reject
```

Core project success should therefore be judged primarily through:

- physically meaningful correspondence
- geometric consistency
- valid transformations
- measurable registration quality
- spatial support
- reproducibility
- honest failure handling

It should not be judged primarily by:

- interface polish
- number of supported technologies
- repository size
- animation quality
- number of model integrations
- number of matches drawn on a screenshot

---

# 4. Scientific Non-Goals

## 4.1 Perfect Registration for Every Pair

ChandraMap is not required to produce an accepted registration for every source/reference pair.

Some observations may contain:

- insufficient overlap
- insufficient distinctive features
- extreme scale differences
- severe illumination differences
- incompatible sensing modalities
- unsuitable geometry
- incorrect reference candidates
- incomplete or weak metadata

In such cases:

```text
Rejected
```

or:

```text
Insufficient Evidence
```

may be the scientifically correct result.

---

## 4.2 Always Returning a Transform

Returning a transformation for every input is not a project goal.

A system that always emits a matrix may be less trustworthy than one that refuses unsupported registrations.

The existence of:

```text
a numerical transform
```

does not prove:

```text
a valid physical registration
```

---

## 4.3 Zero Error Under All Conditions

ChandraMap should not define success as universally achieving zero registration error.

Real scientific imagery may contain:

- sensor limitations
- sampling limitations
- projection uncertainty
- terrain effects
- annotation uncertainty
- interpolation effects
- product-processing differences

The goal is measurable, scientifically interpretable error—not an impossible universal guarantee.

---

## 4.4 Maximum Candidate-Match Count

More candidate matches do not automatically produce better registration.

For example:

```text
1000 weak or repetitive matches
```

may be less useful than:

```text
30 well-distributed,
geometrically verified matches
```

Raw match count should therefore not become an optimization target by itself.

---

## 4.5 Maximum Inlier Count at Any Cost

Likewise, ChandraMap should not optimize only for the largest possible inlier count while ignoring:

- residual error
- spatial coverage
- transformation stability
- degeneracy
- independent accuracy
- failure behavior

An inlier count has meaning only in its geometric and evaluation context.

---

## 4.6 Perfect Scale Invariance

ChandraMap should not claim mathematically perfect robustness across arbitrary scale differences.

Different sensors measure different physical levels of detail.

The realistic research objective is:

> improve correspondence robustness across physically meaningful GSD differences.

---

## 4.7 Upsampling as Resolution Recovery

Increasing raster dimensions through interpolation does not recreate missing terrain information.

Therefore:

```text
coarse image
→ enlarge pixels
→ now equivalent to high-resolution image
```

is not a valid scientific objective.

Upsampling may support processing, but it does not recover sensor-measured detail that never existed.

---

## 4.8 Perfect Sun-Angle Invariance

ChandraMap should not claim that preprocessing can remove every effect of changing illumination.

Different Sun angles can alter:

- shadow direction
- shadow length
- illuminated crater walls
- visible ridges
- local feature structure

The project should pursue **robustness to illumination differences**, not unsupported universal invariance.

---

## 4.9 Perfect Modality Invariance

Different sensors may measure different physical properties.

Panchromatic and hyperspectral/imaging-infrared observations should not be expected to become identical through preprocessing.

The project should study reliable cross-modality correspondence without assuming universal modality invariance.

---

## 4.10 Equal Performance Across Every Sensor Pair

ChandraMap should not define success as every possible sensor combination achieving identical registration quality.

For example, these may represent very different levels of difficulty:

- similar panchromatic observations
- large-GSD-gap observations
- panchromatic versus hyperspectral imagery

Scientific evaluation should preserve those differences rather than hiding them behind one expectation.

---

# 5. Evaluation Non-Goals

## 5.1 Visual-Only Validation

A visually aligned overlay is not an accuracy metric.

Visualizations are valuable for:

- debugging
- interpretation
- communication
- demonstration

but:

```text
looks aligned
```

must not replace:

```text
measurable registration evidence
```

---

## 5.2 Matcher Confidence as Final Truth

A matcher confidence score does not automatically establish:

- geometric correctness
- physical correspondence
- registration accuracy

Candidate matches still require geometric and scientific evaluation.

---

## 5.3 Fit-Point Residual as Independent Accuracy

ChandraMap should not define final independent registration accuracy solely from the same points used to estimate the transformation when independent check points are available.

Keep the distinction:

```text
Fit Points
    ↓
Estimate Transform
```

and:

```text
Independent Check Points
    ↓
Evaluate Transform
```

Fit residuals remain useful diagnostics.

They are not automatically independent validation.

---

## 5.4 Sub-Pixel as Sub-Metre

ChandraMap should not treat:

```text
sub-pixel
```

as synonymous with:

```text
sub-metre
```

A fraction of one pixel has different physical meaning depending on:

- image GSD
- coordinate space
- projection
- reference geometry

---

## 5.5 One Magic Quality Score

ChandraMap should not automatically collapse:

- RMSE
- inlier ratio
- spatial coverage
- success rate
- runtime
- retrieval quality

into one arbitrary "overall score."

These metrics describe different aspects of system behavior.

A combined score should exist only if it is formally defined and scientifically justified elsewhere.

---

# 6. Algorithm and Technology Non-Goals

Methods are tools used to pursue ChandraMap's goals.

They are not goals by themselves.

---

## 6.1 "Use More AI" as an Objective

ChandraMap does not need to maximize AI/ML complexity.

A learned method should be adopted because evidence shows that it improves a defined scientific objective.

Not because it appears more advanced.

---

## 6.2 LightGlue as a Project Goal

LightGlue is a possible matching method.

The project objective is not:

> implement LightGlue.

The relevant question is:

> Does a LightGlue-based configuration provide measurable value on controlled lunar correspondence tasks?

---

## 6.3 LoFTR as a Project Goal

LoFTR is a possible detector-free correspondence method.

Implementing it is not inherently a project achievement.

Its scientific value depends on measured behavior under ChandraMap's benchmark conditions.

---

## 6.4 FAISS as a Project Goal

FAISS is a vector indexing and similarity-search technology.

It is not the scientific objective.

A meaningful higher-level goal may be:

> efficiently identify promising reference regions when location is unknown.

FAISS is only one possible implementation mechanism for that capability.

---

## 6.5 Implement Every Possible Matcher

ChandraMap should not become a catalogue containing every available image matcher.

A method should be added because it:

- answers a research question
- provides a meaningful baseline
- enables an ablation
- addresses an observed limitation
- supports a justified benchmark comparison

Novelty alone is insufficient.

---

## 6.6 Replace Classical Methods Entirely

Advanced methods do not make classical baselines useless.

Classical approaches provide valuable:

- interpretability
- reproducibility
- simplicity
- baseline evidence

V1 should remain available as a meaningful reference against later methods.

---

# 7. Benchmark Non-Goals

## 7.1 Best Benchmark Number at Any Cost

ChandraMap should not optimize for impressive-looking benchmark numbers through:

- cherry-picked pairs
- hidden failures
- per-pair secret tuning
- ground-truth leakage
- inconsistent metrics
- changing units
- manually edited results

Benchmark integrity is more important than obtaining a larger or smaller number.

---

## 7.2 Cherry-Picked Evaluation

Running many cases and publishing only attractive successful examples is not an acceptable benchmark objective.

Failures and difficult cases are part of the scientific result.

---

## 7.3 Hidden Pair-Specific Tuning

Canonical benchmark methodology should not change parameters manually for individual test pairs after observing final evaluation truth.

This includes unrecorded changes to:

- match filtering
- preprocessing
- transformation model
- acceptance criteria

Ground-truth-assisted tuning may be useful as an explicitly labelled oracle experiment.

It should not be presented as ordinary system behavior.

---

## 7.4 Hiding Failed Cases

Failed and rejected cases should not disappear from benchmark summaries merely because they lower apparent performance.

Failure modes help reveal:

- scale limitations
- modality limitations
- illumination sensitivity
- weak geometry
- retrieval ambiguity

---

## 7.5 Every Later Version Must "Win"

Benchmark V2 does not need to outperform V1 on every metric.

V3 does not need to outperform V2 everywhere.

V4 does not need to be universally superior.

A later method performing worse can still be a useful scientific result.

---

## 7.6 V4 as "The Best Version"

V4 is a research configuration.

Its later number does not make it automatically:

- best
- final
- perfect
- universally preferable

Added complexity must justify itself through controlled evidence.

---

## 7.7 Benchmark Versions as Software Releases

Do not interpret:

```text
Benchmark V1
Benchmark V2
Benchmark V3
Benchmark V4
```

as:

```text
software v1.0.0
software v2.0.0
software v3.0.0
software v4.0.0
```

Benchmark configurations and software releases are separate concepts.

---

## 7.8 Test Set as Development Set

Where train/validation/test concepts apply, final evaluation data should not become the primary configuration-tuning population.

Repeatedly tuning against final test outcomes undermines independent evaluation.

---

## 7.9 Benchmark-Specific Hardcoding

Scientific production code must not identify:

- pair IDs
- filenames
- known benchmark transforms
- known evaluation coordinates

and return special-case answers to improve scores.

The benchmark exists to measure the system.

The benchmark definition must not be embedded into the solution.

---

# 8. V1-Specific Non-Goals

Canonical Benchmark V1 is intentionally limited.

Its authoritative boundary is defined by [V1 Scope](../../.ai/context/V1_SCOPE.md).

Canonical V1 should not be expanded merely to appear more sophisticated.

V1 generally does **not** require:

- whole-Moon visual retrieval
- global descriptors
- FAISS
- Top-K reference search
- learned local matching
- ALIKED + LightGlue
- LoFTR
- full native IIRS hyperspectral processing
- advanced sensor-specific routing
- automatic multi-scale reference search
- DEM-aware geometry
- local/piecewise warping
- advanced sub-pixel refinement where excluded by V1 scope
- adaptive matcher selection
- calibrated confidence
- global mosaic generation

The purpose of V1 is to remain a credible classical known-overlap baseline.

If later methods are silently added to V1, the project loses the baseline needed to measure their value.

---

# 9. Later-Version Boundary Principles

## 9.1 V2 Should Not Become V3 + V4

Benchmark V2 should remain focused on its sensor/scale-aware research role according to its authoritative specification.

It should not automatically absorb every advanced matcher, retrieval method, local geometry model, or downstream application.

---

## 9.2 V3 Should Not Absorb Every Experiment

Benchmark V3 may investigate advanced correspondence and retrieval, but it should not automatically become the home for:

- every matcher
- every geometry experiment
- every downstream feature
- every V4 research idea

Its boundary should remain benchmark-driven.

---

## 9.3 V4 Is Still Bounded

Even an advanced research configuration should remain:

- relevant
- testable
- benchmarkable
- interpretable
- connected to correspondence/registration

V4 should not become:

> everything imaginable that could ever be added to ChandraMap.

---

# 10. Product and Application Non-Goals

## 10.1 Visualization as the Core Project

ChandraMap is not primarily a visualization project.

The primary success criterion is not:

- polished dashboards
- attractive match diagrams
- animation
- interactive Moon globes
- many map markers

Visualization should communicate scientific results rather than define them.

---

## 10.2 Mosaic-First Development

Producing a large lunar mosaic is not the primary scientific goal.

The correct dependency is:

```text
Correspondence
    ↓
Verified Geometry
    ↓
Registration
    ↓
Evaluation
    ↓
Optional Mosaic
```

A smooth-looking mosaic can conceal local registration errors.

---

## 10.3 Complete Lunar GIS Platform

ChandraMap is not required to reproduce the complete capabilities of:

- desktop GIS systems
- planetary-data catalogues
- mission archives
- full geospatial-analysis suites

Geospatial functionality should exist where it supports the lunar correspondence and registration problem.

---

## 10.4 Mission-Control Software

ChandraMap is not:

- spacecraft mission-control software
- command-and-control software
- spacecraft flight software
- mission-operations infrastructure
- certified navigation software

The project should not imply operational mission-critical readiness.

---

## 10.5 Autonomous Rover Navigation

Lunar rover autonomy is a separate, much broader problem.

Image registration techniques may eventually support localization research, but ChandraMap is not intended to be a complete:

- rover-navigation stack
- autonomous-driving system
- hazard-avoidance system

---

## 10.6 Terrain and Route Planning

ChandraMap is not primarily responsible for:

- rover path planning
- route optimization
- landing-site selection
- hazard avoidance
- navigation control

Such systems may consume registered imagery but are not the core registration problem.

---

## 10.7 Mineral Classification

IIRS provides hyperspectral information, but ChandraMap's core goal is not:

- mineral classification
- abundance estimation
- compositional mapping
- complete spectral-science analysis

IIRS matters here primarily because hyperspectral imagery introduces a difficult cross-modality correspondence problem.

---

## 10.8 Complete Hyperspectral Science Platform

ChandraMap should not attempt to replace dedicated hyperspectral-analysis software.

Its relevant question is:

> How can hyperspectral information be represented and used appropriately for image correspondence and registration?

not:

> How can every scientific property of the hyperspectral cube be analyzed?

---

## 10.9 Generic Computer Vision Framework

ChandraMap does not need to become a framework for:

- object detection
- OCR
- face recognition
- video analytics
- generic segmentation
- generic image retrieval

The project's focus is lunar/planetary image correspondence and registration.

---

## 10.10 General Image Editor

ChandraMap is not a photo-editing or image-enhancement application.

Preprocessing should serve scientific correspondence and registration.

---

# 11. Engineering Non-Goals

## 11.1 Four Duplicate V1–V4 Codebases

Benchmark versions should not result in four unrelated copies of the same scientific pipeline.

Avoid architecture such as:

```text
v1_complete_pipeline.py
v2_complete_pipeline.py
v3_complete_pipeline.py
v4_complete_pipeline.py
```

when shared components can be composed through configuration and controlled method choices.

Prefer:

```text
Shared Scientific Core
        ↓
Controlled Configuration
        ↓
Benchmark V1 / V2 / V3 / V4
```

---

## 11.2 One Giant Stable Pipeline Script

The opposite extreme is also undesirable.

Stable core scientific functionality should not remain permanently embedded in one large script containing:

- loading
- preprocessing
- matching
- RANSAC
- registration
- metrics
- visualization
- benchmark orchestration

Experiments may begin simply, but reusable core behavior should become modular enough for testing and comparison.

---

## 11.3 Microservices Without a Requirement

Professional architecture does not require:

- microservices
- Kafka
- Redis
- Kubernetes
- distributed workers
- message buses
- service meshes

unless a real technical requirement justifies them.

ChandraMap is primarily a scientific/research software project.

---

## 11.4 Network Services for Scientific Modules

Matching, preprocessing, geometry, and metrics should not be split into separate network services merely to make the architecture appear sophisticated.

Simple in-process modular boundaries are preferable until scale or deployment requirements justify otherwise.

---

## 11.5 Mandatory Cloud Infrastructure

The scientific core should not depend on cloud infrastructure purely for architectural prestige.

Local and reproducible scientific execution remains valuable.

Cloud services may be useful later for deployment or scale, but they should not define core correctness.

---

## 11.6 GPU Requirement for the Classical Baseline

Canonical V1 should not require GPU infrastructure simply because later learned configurations may use accelerators.

The baseline should remain lightweight and CPU-capable where practical.

---

## 11.7 Frontend-Defined Scientific Truth

Authoritative scientific behavior should not live only in frontend code.

The frontend should not independently define:

- RMSE
- inlier status
- transformation validity
- registration confidence
- benchmark acceptance

The UI should consume scientific results.

---

## 11.8 API-Defined Scientific Truth

Backend/API handlers should not become a second independent implementation of:

- correspondence
- RANSAC
- transformation estimation
- metrics
- registration

They should orchestrate or expose reusable scientific behavior.

---

## 11.9 Notebook-Only Core Logic

Notebooks are valuable for:

- exploration
- visualization
- analysis
- experiments

but stable scientific functionality should not remain dependent on undocumented interactive notebook state.

---

## 11.10 Maximum Repository Complexity

Professional quality is not measured by having the most:

- directories
- abstractions
- configuration files
- services
- workflows
- badges

A professional repository should instead be:

- understandable
- reproducible
- testable
- secure
- maintainable
- scientifically honest

---

## 11.11 Badge Collection

README badges should represent real project integrations or statuses.

The number of badges is not evidence of software or scientific quality.

---

## 11.12 100% Coverage as the Only Quality Metric

High line coverage does not prove:

- correct coordinate handling
- correct geometry
- valid benchmark methodology
- scientific registration accuracy

Testing effort should prioritize meaningful scientific behavior.

---

## 11.13 Software Tests as Scientific Proof

Passing unit and integration tests demonstrates implementation behavior.

It does not demonstrate strong registration performance on real lunar data.

Software testing and scientific benchmarking answer different questions.

---

## 11.14 Full Scientific Benchmark on Every Small Change

Expensive mission-scale benchmarks do not need to become ordinary unit tests unless the repository deliberately adopts such a policy.

Use testing and benchmarking at the appropriate level.

---

## 11.15 Optimize Before Correctness

ChandraMap should not sacrifice:

- correctness
- scientific validity
- clarity
- reproducibility

for unmeasured performance optimization.

Optimization should respond to evidence.

---

## 11.16 Unsupported Real-Time Claims

ChandraMap should not be described as "real-time" without:

- measured performance
- a defined workload
- a defined latency requirement
- documented hardware conditions

---

# 12. Data Non-Goals

## 12.1 Store Every Lunar Dataset in Git

The Git repository should not become a complete archive of large mission datasets.

Scientific data handling should consider:

- dataset size
- provider terms
- provenance
- reproducibility
- storage design

---

## 12.2 Replace Official Mission Data Portals

ChandraMap should not attempt to replace official scientific data archives or portals.

Its purpose is to use relevant scientific products for correspondence and registration research.

Official providers remain the authoritative source for their mission products and metadata.

---

## 12.3 Claim Ownership of External Mission Data

ChandraMap should not imply authorship or ownership of external:

- Chandrayaan-2 products
- LRO/LROC products
- other mission datasets

when those products originate from external scientific organizations.

---

## 12.4 Modify Original Scientific Products

Normal processing should not overwrite authoritative source products.

Keep the distinction:

```text
Original Scientific Product
```

versus:

```text
Derived Representation
```

Examples of derived representations may include:

- normalized images
- pyramid levels
- spectral projections
- gradient representations
- registered images

---

## 12.5 Replace Reliable Metadata with AI

If reliable mission/product metadata already provides:

- location
- footprint
- projection
- sensor identity

ChandraMap does not need to ignore it simply to create a more difficult image-only problem.

Metadata-free localization may be a valid research experiment.

It should not replace metadata-aware scientific processing by default.

---

# 13. Documentation and Claim Non-Goals

## 13.1 Documentation as Proof of Implementation

A capability being described in:

- architecture documentation
- roadmap
- task documents
- benchmark specifications
- research notes

does not prove that it exists in code.

Documentation may describe target or future architecture.

---

## 13.2 Architecture Diagram as Current Reality

A target architecture diagram should not be interpreted as evidence that every illustrated component currently exists.

Status must be established from repository implementation evidence.

---

## 13.3 Roadmap as Current Capability

Roadmap items describe future intent.

They must not automatically appear in current feature lists.

---

## 13.4 Fake Accuracy Claims

ChandraMap should not publish invented values such as:

- RMSE targets
- inlier percentages
- success percentages
- confidence values
- speedups

without measured and documented evidence.

---

## 13.5 State-of-the-Art Claims Without Evidence

ChandraMap should not describe itself as:

- state-of-the-art
- best-performing
- universally invariant
- perfectly accurate

without an evaluation methodology capable of supporting such claims.

---

## 13.6 Official Agency Endorsement

ChandraMap should not imply that it is:

- officially maintained by ISRO
- officially maintained by NASA
- officially endorsed by LROC
- an official mission product

unless verified evidence explicitly establishes that relationship.

Using mission data does not imply organizational endorsement.

---

## 13.7 Publication Claims Without Evidence

The project should not claim:

- paper publication
- conference acceptance
- DOI
- peer-reviewed benchmark status

unless such claims are verifiably true.

Research-quality software does not require fabricated publication credentials.

---

## 13.8 Hiding Limitations for Portfolio Presentation

ChandraMap may serve as a portfolio project.

That does not justify hiding:

- failures
- unsupported areas
- negative experiments
- scientific limitations
- experimental status

Professional presentation should reinforce scientific honesty rather than replace it.

---

# 14. Scope Categories

Not all non-goals have the same meaning.

| Category                   | Meaning                                                    | Example                         |
| -------------------------- | ---------------------------------------------------------- | ------------------------------- |
| **Core Non-Goal**          | Outside ChandraMap's present primary mission               | Spacecraft mission control      |
| **Current-Scope Non-Goal** | Potentially valuable later, but not required now           | Broad planetary support         |
| **Downstream Non-Goal**    | Useful output that does not define scientific-core success | Interactive lunar map           |
| **Method Non-Goal**        | Technology that should not become the objective itself     | "Use FAISS"                     |
| **Benchmark Non-Goal**     | Behavior that would invalidate fair evaluation             | Cherry-picking successful pairs |

This classification prevents a useful future idea from being misrepresented as either forbidden forever or required immediately.

---

# 15. Prefer / Avoid

| Prefer                                    | Avoid                                                 |
| ----------------------------------------- | ----------------------------------------------------- |
| Measured registration quality             | Visual-only success                                   |
| Explicit rejection                        | Forced transformations                                |
| Geometrically verified correspondences    | Maximum raw match count                               |
| Controlled benchmark comparison           | Cherry-picked results                                 |
| Methods justified by evidence             | AI/ML for appearance                                  |
| Stable classical baseline                 | Replacing the baseline whenever a newer model appears |
| Shared scientific core                    | Four duplicated benchmark pipelines                   |
| Valid metadata when available             | Rediscovering known location unnecessarily            |
| Modular software                          | Enterprise infrastructure without need                |
| Downstream UI after scientific validation | UI-first scientific development                       |
| Honest limitations                        | Portfolio hype                                        |
| Reproducible outputs                      | Manually edited benchmark values                      |

---

# 16. Decision Test for New Features

Before promoting a major capability into the ChandraMap core, ask:

1. **Does it improve or directly support the correspondence/registration problem?**
2. **Does it answer a documented scientific or engineering question?**
3. **Can its benefit be measured?**
4. **Does it belong in the active benchmark scope?**
5. **Is it a scientific-core capability or a downstream application?**
6. **Can it be added without destroying baseline comparability?**
7. **Can the complexity be justified by evidence?**
8. **Can it be tested and reproduced?**
9. **Does an existing simpler mechanism already satisfy the requirement?**
10. **Would adding it shift the project's mission unintentionally?**

If the answers are unclear, the capability may belong initially in:

- research
- experiments
- a downstream application
- the roadmap
- a separate project

rather than the scientific core.

---

# 17. When a Non-Goal May Become a Goal

A non-goal may be reconsidered when:

- project scope intentionally expands
- controlled evidence shows the capability is necessary
- benchmark design changes
- a downstream feature becomes a supported project objective
- architectural constraints change
- the roadmap formally promotes it

A major boundary change should be deliberate.

Relevant documentation may need to be updated together, including:

- [`goals.md`](./goals.md)
- this file
- [`overview.md`](./overview.md)
- [`problem-statement.md`](./problem-statement.md)
- [Roadmap](../../ROADMAP.md)
- architecture documentation
- benchmark specifications

A major non-goal becoming core is a **project-scope decision**, not a minor implementation detail.

---

# 18. Relationship to Project Goals

[`goals.md`](./goals.md) defines:

> **What outcomes should ChandraMap pursue?**

This document defines:

> **What outcomes should not drive the project or define scientific success?**

The two documents should reinforce each other without simply repeating opposite sentences.

Conceptually:

```text
Goals
→ reliable correspondence
→ valid geometry
→ measurable registration
→ reproducibility

Non-Goals
→ visual appearance as proof
→ forced transformations
→ technology adoption for appearance
→ benchmark gaming
→ unnecessary scope expansion
```

---

## Relationship to the Problem Statement

[`problem-statement.md`](./problem-statement.md) defines the scientific and engineering problem ChandraMap addresses.

This file identifies adjacent problems that are intentionally outside the core mission or not currently required.

---

## Relationship to the Project Overview

[`overview.md`](./overview.md) explains ChandraMap positively:

> What is the project?

This file provides the complementary boundary:

> What should not define the project?

---

## Relationship to the Roadmap

[ROADMAP.md](../../ROADMAP.md) may contain downstream or future ideas that remain current non-goals.

A roadmap item does not automatically become a current core requirement.

---

## Relationship to V1 Scope

[V1 Scope](../../.ai/context/V1_SCOPE.md) is authoritative for canonical Benchmark V1 inclusions and exclusions.

If this document and V1 scope ever conflict regarding V1:

> **`V1_SCOPE.md` takes precedence.**

---

## Relationship to Benchmark Rules

[Benchmark Rules](../../.ai/development/BENCHMARK_RULES.md) define how scientific comparison should remain fair and reproducible.

This document reinforces the principle that impressive results obtained through invalid methodology are not a ChandraMap objective.

---

## Relationship to Architecture

Architecture should exist to support the project's real scientific requirements.

ChandraMap should not add:

- distributed infrastructure
- services
- databases
- queues
- deployment complexity

solely because they appear professional in a diagram.

---

# 19. Core Boundary Summary

ChandraMap should prioritize:

```text
Scientific Validity
        ↓
Measured Registration Quality
        ↓
Reproducible Benchmarking
        ↓
Maintainable Engineering
        ↓
Visualization / Product Features
```

The project should not optimize the bottom of this stack while the scientific foundation remains unverified.

The following are therefore especially important non-goals:

1. A map UI is not the scientific core.
2. A mosaic is not the scientific core.
3. ChandraMap is not a complete lunar GIS.
4. ChandraMap is not mission-control software.
5. ChandraMap is not a rover-autonomy stack.
6. Mineral classification is not the core problem.
7. Full hyperspectral science analysis is not the core problem.
8. Every pair does not need to register successfully.
9. Returning any transform is not automatically success.
10. Zero error is not a universal requirement.
11. Maximum candidate-match count is not the goal.
12. Maximum inlier count alone is not the goal.
13. Matcher confidence is not final scientific truth.
14. Visual alignment is not an accuracy metric.
15. Fit-point residual is not automatically independent accuracy.
16. Sub-pixel does not automatically mean sub-metre.
17. Upsampling is not physical resolution recovery.
18. Universal scale invariance is not claimed.
19. Universal Sun-angle invariance is not claimed.
20. Universal modality invariance is not claimed.
21. Valid metadata does not need to be ignored.
22. Whole-Moon retrieval is not required for V1.
23. FAISS itself is not a project goal.
24. LightGlue itself is not a project goal.
25. LoFTR itself is not a project goal.
26. More AI/ML is not automatically better.
27. Every possible matcher does not need to be integrated.
28. Later benchmark versions do not need to win every metric.
29. V4 is not automatically the best configuration.
30. Benchmark V1–V4 are not software releases.
31. Benchmark gaming is never a project objective.
32. Failed cases should not be hidden.
33. V1–V4 should not become four duplicated codebases.
34. Enterprise infrastructure is not required for appearance.
35. GPU infrastructure is not required merely for the classical baseline.
36. Frontend/backend layers should not redefine scientific truth.
37. Notebook-only stable core logic is not the target architecture.
38. Canonical benchmark results should not be edited manually.
39. Git is not intended to be a complete lunar-data archive.
40. ChandraMap does not replace official mission archives.
41. ChandraMap does not claim ownership of mission data it did not produce.
42. ChandraMap should not imply agency endorsement without evidence.
43. A roadmap item is not automatically a current feature.
44. Documentation does not prove implementation.
45. Planetary expansion should not distract from validating the lunar core.

---

# 20. Related Documents

- [Project Overview](./overview.md) — explains what ChandraMap is
- [Problem Statement](./problem-statement.md) — defines the scientific and engineering problem
- [Project Goals](./goals.md) — defines the outcomes ChandraMap aims to achieve
- [Project Context](../../.ai/context/PROJECT_CONTEXT.md) — canonical project identity and research context
- [Domain Context](../../.ai/context/DOMAIN_CONTEXT.md) — lunar imaging and scientific constraints
- [Terminology](../../.ai/context/TERMINOLOGY.md) — canonical project vocabulary
- [Dataset Context](../../.ai/context/DATASETS.md) — product, metadata, provenance, and data-governance guidance
- [V1 Scope](../../.ai/context/V1_SCOPE.md) — canonical Benchmark V1 boundary
- [System Overview](../../.ai/architecture/SYSTEM_OVERVIEW.md) — major architectural responsibilities
- [Processing Pipeline](../../.ai/architecture/PIPELINE.md) — detailed scientific processing flow
- [Module Map](../../.ai/architecture/MODULE_MAP.md) — repository responsibility ownership
- [Benchmark Rules](../../.ai/development/BENCHMARK_RULES.md) — controlled evaluation and benchmark integrity
- [Documentation Rules](../../.ai/development/DOCUMENTATION_RULES.md) — documentation and claim standards
- [Roadmap](../../ROADMAP.md) — future project direction

A non-goal should protect ChandraMap's scientific focus without preventing useful experimentation. New capabilities should become core only when their relevance, evidence, and architectural place are clear.

<!-- Source specification: :contentReference[oaicite:0]{index=0} -->
