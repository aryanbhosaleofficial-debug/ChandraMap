# ChandraMap Project Goals

This document defines the intended outcomes of **ChandraMap** as an open-source lunar image correspondence and registration research/software project.

It answers:

> **What should ChandraMap ultimately achieve, and what evidence should demonstrate meaningful progress toward those outcomes?**

The central mission is to build a **trustworthy, reproducible lunar image correspondence and registration system** that can identify the same physical lunar terrain across heterogeneous observations, verify correspondence geometrically, estimate alignment, measure registration quality, preserve scientific provenance, and reject unreliable results instead of forcing a solution.

A documented goal describes an intended project outcome.

It does **not** imply that the capability is already implemented, tested, benchmarked, or supported.

---

## 1. Mission Goal

ChandraMap's primary mission is:

> **Enable reliable and measurable correspondence and registration between lunar observations that may differ substantially in spatial resolution, illumination, viewing geometry, sensor characteristics, and sensing modality.**

A trustworthy ChandraMap result should ultimately provide evidence for:

```text
Physical Lunar Correspondence
        ↓
Geometric Verification
        ↓
Source → Reference Geometry
        ↓
Registration
        ↓
Measured Quality
        ↓
Accept or Reject
```

Scientific correctness takes priority over visual appearance, implementation complexity, or demonstration quality.

The goal is not simply to produce an aligned-looking image.

The goal is to determine whether that alignment is scientifically defensible.

---

## 2. Goal Priorities

ChandraMap should prioritize outcomes in approximately the following order.

| Priority | Goal Area                       | Why It Matters                                                                        |
| -------- | ------------------------------- | ------------------------------------------------------------------------------------- |
| **1**    | Scientific correctness          | Incorrect correspondence invalidates downstream registration                          |
| **2**    | Measurable registration quality | Results must be evaluated rather than judged visually                                 |
| **3**    | Reproducibility                 | Important results should be traceable and repeatable                                  |
| **4**    | Robustness                      | Lunar observations may differ strongly in scale, illumination, modality, and geometry |
| **5**    | Maintainable engineering        | Methods must remain testable, replaceable, and comparable                             |
| **6**    | Downstream applications         | Maps, mosaics, APIs, and UI depend on trustworthy registration                        |

This priority order is not a roadmap or development timeline.

It describes what matters most when project goals compete.

---

## 3. Goals, Features, Methods, and Status

These concepts should remain separate.

### Goal

A desired project outcome.

Example:

> Provide measurable evidence that a registration is reliable.

### Feature

A capability that may help achieve a goal.

Examples:

- residual computation
- spatial-coverage measurement
- explicit failure status
- registered overlay

### Method

An algorithm or technical approach used to pursue a goal.

Examples may include:

- classical feature matching
- learned matching
- vector retrieval
- local refinement

Methods may change without changing the project goal.

### Implementation Status

Whether a capability actually exists in the repository.

A goal being documented here does **not** mean it is:

- implemented
- tested
- benchmarked
- supported

Implementation status must come from repository evidence.

---

# 4. Scientific Goals

## 4.1 Reliable Physical Correspondence

ChandraMap should identify a sufficient set of source/reference points that plausibly represent the same physical lunar terrain.

The project should preserve the distinction:

```text
Candidate Match
      ↓
Geometric Verification
      ↓
Verified Inlier
```

A matcher-generated candidate is a hypothesis.

It should not automatically be treated as a trustworthy physical correspondence.

---

## 4.2 Geometric Verification

ChandraMap should reject candidate correspondences that are inconsistent with the selected geometric relationship.

The scientific goal is not:

> maximize the number of drawn matches.

The goal is:

> retain correspondences that provide meaningful geometric support for registration.

Conceptually:

```text
Candidate Matches
        ↓
Geometric Verification
        ↓
Verified Inliers
        +
Rejected Outliers
```

A high raw match count without geometric consistency should not be considered success.

---

## 4.3 Explicit Transformation Estimation

ChandraMap should estimate an interpretable relationship between:

```text
source-image coordinates
```

and:

```text
reference-image coordinates
```

The transformation should preserve, where relevant:

- model type
- direction
- source coordinate domain
- reference coordinate domain

A transformation with ambiguous direction is not a sufficiently trustworthy scientific output.

---

## 4.4 Reliable Registration

ChandraMap should use validated geometry to align the source observation with the reference.

A registered image is a **derived scientific output**.

It should not be treated as independent proof that the registration is correct.

The goal is therefore:

```text
Verified Geometry
      ↓
Registration
      ↓
Evaluation
```

rather than:

```text
Warp Completed
      ↓
Assume Success
```

---

## 4.5 Measurable Registration Quality

ChandraMap should provide numerical evidence describing registration quality.

Potential evidence may include:

- candidate-match count
- verified-inlier count
- inlier ratio
- transformation residuals
- spatial coverage
- image-space error
- independent check-point RMSE
- physical ground error where scientifically valid
- success/rejection/failure status

This document does not define exact formulas or thresholds.

Those belong in metric and benchmark specifications.

---

## 4.6 Explicit Failure and Rejection

A trustworthy ChandraMap system should be able to report:

> **Reliable registration could not be established from the available evidence.**

Potential reasons may include:

- insufficient detectable structure
- insufficient candidate correspondences
- insufficient verified inliers
- poor spatial distribution
- unstable geometry
- invalid transformation
- incorrect reference candidate
- severe modality mismatch

Always returning a transformation is **not** a project goal.

A scientifically correct rejection is positive system behavior when the evidence is insufficient.

---

## 4.7 Scale Robustness

ChandraMap should improve correspondence robustness across observations with substantially different physical ground sampling distances.

Scale must be treated as a physical imaging problem.

For example:

```text
coarse image
      ↓
upsampling
      ↓
larger raster
```

does **not** mean:

```text
new physical terrain detail
```

Interpolation can change representation size.

It cannot recover spatial information that the original instrument never measured.

---

## 4.8 Illumination Robustness

ChandraMap should improve robustness when the same lunar terrain is observed under different illumination conditions.

Changing Sun angle can alter:

- shadow direction
- shadow length
- illuminated crater walls
- ridge visibility
- edge structure
- local contrast

The project goal is therefore:

> **robustness under meaningful illumination differences**

not an unsupported claim of mathematically perfect Sun-angle invariance.

---

## 4.9 Multi-Sensor Robustness

ChandraMap should support research into correspondence across heterogeneous lunar observations.

Important project contexts include:

- Chandrayaan-2 OHRC
- Chandrayaan-2 TMC-2
- Chandrayaan-2 IIRS
- LRO NAC
- LRO WAC

Listing these instruments as research context does not imply that every possible sensor pairing is currently implemented or supported.

---

## 4.10 Modality-Aware Handling

Data should be interpreted according to its physical sensing modality.

In particular:

> **IIRS is hyperspectral / imaging-infrared data.**

The project should not force native hyperspectral observations into assumptions intended only for ordinary 2D panchromatic imagery.

Where a 2D representation is derived for correspondence, its origin should remain scientifically traceable.

---

## 4.11 Useful Spatial Support

ChandraMap should evaluate not only how many verified matches exist, but also how they are distributed.

For example:

```text
many inliers
concentrated around one crater
```

may provide weaker global support than:

```text
fewer inliers
distributed across the overlap
```

Spatial coverage should therefore become measurable wherever the benchmark definition requires it.

---

## 4.12 Physically Meaningful Accuracy

Registration error should be reported in units that preserve scientific meaning.

Image-space results should make clear whether they use:

- source-image pixels
- reference-image pixels

Ground units such as metres should be reported only when valid spatial/geometric context supports the conversion.

Do not equate:

```text
0.5 px
```

with:

```text
0.5 m
```

without a scientifically valid relationship.

Likewise:

> **Sub-pixel does not automatically mean sub-metre.**

---

## 4.13 Independent Accuracy Where Possible

Where independent check points exist, ChandraMap should evaluate the final transformation using points that were not used to fit it.

Conceptually:

```text
Fit Points
    ↓
Estimate Transform

Independent Check Points
    ↓
Evaluate Final Transform
```

Low residual error on the same points used for fitting is useful diagnostic evidence.

It should not be the only definition of independent registration accuracy.

---

# 5. Benchmarking Goals

Benchmarking is a first-class ChandraMap goal.

The project should make methodological improvement **measurable** rather than relying on claims such as:

> the newer method looks better.

---

## 5.1 Establish a Stable Baseline

ChandraMap should maintain a simple, explainable, reproducible classical baseline.

Benchmark V1 serves this purpose.

A stable baseline provides the reference required to determine whether later complexity actually improves the scientific result.

---

## 5.2 Controlled Comparison

Where methods are intended to be compared directly, ChandraMap should aim to keep relevant evaluation conditions controlled.

These may include:

- same image pairs
- same ground truth
- same evaluation population
- same metric definitions
- same units
- explicit configuration
- comparable transformation assumptions where appropriate

The evaluation population should not be changed merely to make a method appear stronger.

---

## 5.3 Failure Visibility

Failed and rejected registrations should remain part of benchmark interpretation.

ChandraMap should avoid reporting only the successful subset without also making clear how many cases:

- succeeded
- were rejected
- failed
- were unsupported
- were not run

Failure behavior is part of method performance.

---

## 5.4 Stress-Type Understanding

Benchmarks should help determine **where** and **why** methods succeed or fail.

Potential challenge categories may include:

- comparable/easier conditions
- scale stress
- illumination/Sun-angle stress
- modality stress
- terrain/geometry stress
- repetitive terrain
- low-feature terrain
- partial overlap
- global-retrieval stress

No category counts are defined by this document.

---

## 5.5 Retrieval and Registration Separation

ChandraMap should evaluate global candidate retrieval and local registration as related but distinct problems.

Conceptually:

```text
Global Retrieval
→ Did the correct region appear among candidates?

Local Registration
→ Was the selected region aligned accurately?
```

A high retrieval score does not prove precise registration.

A precise local registration on a supplied correct crop does not prove global retrieval capability.

---

## 5.6 Reproducible Benchmark Results

A scientifically important benchmark result should ideally be traceable to:

```text
Data
+
Configuration
+
Code
+
Model / Checkpoint where applicable
+
Metric Definition
```

Benchmark reproducibility should improve scientific auditability and make later comparisons meaningful.

---

# 6. Benchmark V1 Goal

Benchmark V1 should establish a credible, reproducible **classical known-overlap registration baseline**.

Its core research question is:

> **Can a simple classical local-registration configuration establish reliable lunar correspondence and geometric alignment when the correct overlapping reference region is already known?**

Conceptually:

```text
Classical Local Features
        ↓
Candidate Matching
        ↓
Geometric Verification
        ↓
Affine / Homography
        ↓
Registration
        ↓
Evaluation
```

The authoritative V1 boundary is defined in [V1 Scope](../../.ai/context/V1_SCOPE.md).

V1 should remain simple enough to provide a meaningful baseline.

It should not silently absorb advanced later-version capabilities merely to improve results.

---

# 7. Benchmark V2 Goal

Benchmark V2 should investigate whether **sensor-aware and physically meaningful scale-aware processing** provides measurable benefit over the classical baseline.

Potential research focus includes:

- sensor-aware representations
- GSD-aware scale handling
- reference pyramids
- structural preprocessing
- derived IIRS registration representations
- improved robustness to scale differences
- improved robustness to illumination differences

These are research goals.

Their appearance here does not imply implementation.

V2 should provide evidence about whether these additions actually solve limitations observed in V1.

---

# 8. Benchmark V3 Goal

Benchmark V3 should investigate whether stronger correspondence methods and, where needed, global localization/retrieval improve robustness.

Potential research areas may include:

- ALIKED + LightGlue
- LoFTR
- remote-sensing correspondence methods
- global image representations
- vector retrieval
- candidate ranking
- Top-K reference search

The high-level goals are:

1. improve local correspondence where classical methods are weak
2. support unknown-location workflows where metadata cannot sufficiently constrain the reference search

The goal is not:

> use a specific neural method.

The goal is measurable improvement in correspondence or localization performance.

---

# 9. Benchmark V4 Goal

Benchmark V4 should investigate **research-grade robustness beyond simpler global registration**.

Potential research goals include:

- more precise tie-point refinement
- local or piecewise geometry
- terrain/DEM-aware reasoning
- advanced multi-modal handling
- uncertainty estimation
- calibrated confidence
- adaptive method selection
- stronger failure classification
- scalable retrieval

V4 should not be described automatically as:

- best
- final
- perfect
- universally superior

Additional complexity should be retained only when controlled evidence shows that it addresses real limitations.

---

## 9.1 Benchmark Versions Are Not Software Releases

Benchmark V1–V4 are research/pipeline configurations.

They do not imply software versions such as:

```text
v1.0.0
v2.0.0
v3.0.0
v4.0.0
```

Software release history and benchmark methodology are separate concerns.

---

# 10. Engineering Goals

## 10.1 Modular Scientific Core

ChandraMap should maintain a reusable scientific core in which important responsibilities can evolve independently.

Conceptually, responsibilities may include:

- scientific input handling
- preprocessing
- correspondence
- geometry
- registration
- evaluation
- retrieval
- benchmark orchestration

Detailed module ownership belongs in the [Module Map](../../.ai/architecture/MODULE_MAP.md).

---

## 10.2 Shared Components Across V1–V4

Benchmark configurations should compose shared scientific implementations where appropriate.

Avoid designing:

```text
V1 codebase
V2 codebase
V3 codebase
V4 codebase
```

as four independent duplicated systems.

Prefer:

```text
Shared Scientific Components
          ↓
Benchmark Configuration / Composition
          ↓
V1 / V2 / V3 / V4 Experiments
```

This makes comparisons more reliable and maintenance easier.

---

## 10.3 Reusable Scientific Logic

Core correspondence and registration behavior should remain reusable outside:

- CLI code
- backend routes
- frontend components
- notebooks
- benchmark runners

Presentation and transport layers should consume scientific results rather than own duplicate scientific algorithms.

---

## 10.4 Testability

Critical scientific behavior should be independently testable.

High-value areas include:

- coordinate conventions
- transformation direction
- geometric estimation
- candidate/inlier alignment
- metric calculations
- spatial coverage
- scale handling
- failure behavior

Testing should protect scientific meaning, not only code execution.

---

## 10.5 Failure-Aware Software

Expected scientific failures should be represented deliberately.

The software should avoid using:

- crashes
- fake transforms
- identity-transform fallbacks
- fabricated metrics
- silent algorithm changes

to hide unsuccessful registration.

---

## 10.6 Configuration-Driven Experiments

Scientifically meaningful experiment variation should be explicit and reproducible.

The project should avoid methodology that depends on:

- hidden code edits
- unrecorded notebook state
- pair-specific manual tuning
- developer-local state

Configuration should select experiment behavior where appropriate.

---

## 10.7 Minimal Coupling

Scientific correctness should not depend on:

- frontend framework
- web server
- visualization library
- deployment infrastructure

The desired dependency direction is conceptually:

```text
UI / API / CLI / Benchmark Runner
              ↓
       Scientific Core
```

not the reverse.

---

## 10.8 Portability

The classical baseline and core scientific workflows should remain reasonably portable where practical.

V1 should not require GPU-only infrastructure without scientific necessity.

Advanced learned research may have different hardware needs, but those requirements should not automatically propagate into the baseline.

---

## 10.9 Maintainability

A technically competent contributor should be able to:

- inspect the scientific flow
- identify module ownership
- replace a matcher
- evaluate geometry
- rerun controlled benchmarks
- inspect failures
- trace a result

without reverse-engineering one monolithic script.

---

## 10.10 Security-Aware Data Handling

External:

- scientific products
- metadata
- configuration
- archives
- model files

should be treated safely at software trust boundaries.

Detailed security requirements belong in [SECURITY.md](../../SECURITY.md).

Security is part of trustworthy scientific software, even though security mechanisms do not define the scientific registration problem itself.

---

# 11. Data and Provenance Goals

## 11.1 Preserve Product Identity

Derived outputs should remain traceable to the source and reference products that produced them.

A scientific array should not become anonymous merely because one processing stage only needs pixel values.

---

## 11.2 Preserve Sensor and Spatial Context

Relevant context should remain available where needed, including concepts such as:

- mission
- instrument
- GSD
- projection
- footprint
- acquisition context
- representation method

Missing metadata should remain missing rather than being fabricated.

---

## 11.3 Distinguish Original and Derived Data

ChandraMap should clearly distinguish:

```text
Original Scientific Product
```

from:

```text
Derived Representation
```

Examples of derived data may include:

- normalized image
- downsampled image
- pyramid level
- selected IIRS band
- PCA-derived representation
- gradient representation
- reference tile
- registered image

Derived products should preserve lineage where scientifically important.

---

## 11.4 Reproducible Dataset Membership

Benchmark membership should be identifiable and reproducible.

A benchmark should not be defined only as:

> whichever files happen to be present in a local directory.

Scientific identity and provenance should survive differences in local storage layout.

---

## 11.5 Respect Data Governance

ChandraMap should use scientific data consistently with:

- provider terms
- attribution requirements
- redistribution requirements
- repository storage policy

Large external mission datasets should not automatically be committed to Git merely for convenience.

Detailed data policy belongs in the [Dataset Context](../../.ai/context/DATASETS.md).

---

# 12. Reproducibility Goals

## 12.1 Traceable Scientific Runs

A scientifically important run should ideally remain traceable to:

- source identity
- reference identity
- benchmark/pair identity
- effective configuration
- relevant random seed
- model/checkpoint where applicable
- software revision where available
- metric definition

The exact serialization mechanism belongs elsewhere.

---

## 12.2 Deterministic Where Practical

ChandraMap should minimize unnecessary nondeterminism.

Where deterministic execution cannot be guaranteed:

- relevant randomness should be controlled where possible
- seeds/context should be recorded where meaningful
- documentation should avoid false determinism claims

---

## 12.3 Re-Runnable Baselines

Canonical benchmark configurations, especially V1, should remain reproducible enough to rerun after:

- refactoring
- dependency changes
- new research methods
- benchmark expansion

A moving baseline makes later comparisons difficult to interpret.

---

## 12.4 No Manual Scientific Result Editing

Canonical scientific results should come from reproducible computation.

Do not manually alter:

- correspondences
- transforms
- RMSE values
- coverage values
- benchmark summaries

to make results appear stronger.

If an implementation or metric is wrong, correct the underlying issue and rerun the experiment.

---

# 13. Research Goals

## 13.1 Understand Failure Modes

Failure analysis should be treated as research output.

ChandraMap should help determine whether failure resulted from:

- scale mismatch
- illumination differences
- modality differences
- repetitive terrain
- low-feature terrain
- partial overlap
- terrain relief
- reference ambiguity
- inappropriate geometry

Understanding why a method fails can be as useful as finding where it succeeds.

---

## 13.2 Measure the Value of Added Complexity

Each significant research addition should answer a measurable question.

Examples include:

- Does scale-aware processing improve the classical baseline?
- Does sensor-aware preprocessing improve correspondence?
- Does a learned matcher improve difficult pairs?
- Does local refinement improve independent accuracy?
- Does global retrieval recover correct regions reliably?

Do not assume the answer is yes before evaluation.

---

## 13.3 Preserve Negative Results

Meaningful negative findings should remain visible.

Examples include:

- an advanced matcher underperforms the classical baseline
- a representation provides no measurable benefit
- retrieval fails in repetitive terrain
- refinement destabilizes a subset
- computational cost increases without scientific improvement

Negative results can prevent future contributors from repeating unproductive approaches.

---

## 13.4 Support Ablation Studies

ChandraMap should make it possible to isolate meaningful pipeline changes where practical.

For example:

```text
Same Pair Set
+
Same Evaluation
+
Same Geometry
+
Different Matcher
```

can help determine whether the matcher contributed to the observed difference.

---

## 13.5 Study Lunar Domain Shift

Advanced computer-vision models trained primarily on terrestrial imagery may not automatically generalize perfectly to lunar terrain.

ChandraMap should treat this as an empirical research question.

The project should not assume:

> newer learned method = better lunar correspondence.

---

## 13.6 Improve Multi-Modal Correspondence

The project should investigate representations and methods capable of preserving useful structural correspondence when different sensors measure different physical information.

This is especially relevant to hyperspectral/imaging-infrared contexts such as IIRS.

---

# 14. Open-Source and Documentation Goals

## 14.1 Understandability

A technically competent newcomer should be able to understand:

- what ChandraMap does
- what scientific problem it addresses
- how registration is evaluated
- how benchmark versions differ
- where major engineering responsibilities belong

without relying on private project knowledge.

---

## 14.2 Reproducible Setup

Supported project workflows should eventually be reproducible using the repository's actual dependency and configuration tooling.

This goals document does not define installation commands or claim that a particular setup workflow already exists.

---

## 14.3 Contribution Readiness

The repository should provide enough:

- architecture guidance
- development rules
- tests
- scientific context
- benchmark methodology
- contribution documentation

for focused changes to be made safely.

---

## 14.4 Scientific Transparency

ChandraMap should expose:

- methodology
- assumptions
- limitations
- benchmark design
- failure cases
- negative results

rather than presenting only successful visual examples.

---

## 14.5 Professional Repository Quality

The long-term repository goal is that a new contributor can reasonably:

```text
Understand
   ↓
Set Up
   ↓
Run
   ↓
Test
   ↓
Inspect
   ↓
Reproduce
```

the workflows the repository genuinely supports.

No unverified command or capability is implied by this goal.

---

## 14.6 Documentation as a Source of Truth

Documentation responsibilities should remain separated.

Conceptually:

| Document                                                    | Responsibility                                     |
| ----------------------------------------------------------- | -------------------------------------------------- |
| [`overview.md`](./overview.md)                              | What ChandraMap is                                 |
| [`problem-statement.md`](./problem-statement.md)            | What scientific/engineering problem must be solved |
| `goals.md`                                                  | What outcomes matter                               |
| [Processing Pipeline](../../.ai/architecture/PIPELINE.md)   | How processing stages are ordered                  |
| [Benchmark Rules](../../.ai/development/BENCHMARK_RULES.md) | How methods should be compared                     |
| [Roadmap](../../ROADMAP.md)                                 | How future work is sequenced                       |

Documentation should distinguish:

- Implemented
- Tested
- Experimental
- Planned
- Proposed

and avoid presenting future work as present capability.

---

# 15. Downstream Application Goals

Downstream applications can make trustworthy scientific results more useful.

They remain secondary to the scientific core.

The desired conceptual dependency is:

```text
Reliable Correspondence
        ↓
Reliable Geometry
        ↓
Reliable Registration
        ↓
Measured Quality
        ↓
Visualization / Mosaic / Map / API
```

Do not reverse this dependency.

---

## 15.1 Visualization

Useful visualization goals may include:

- source/reference comparison
- candidate-match display
- verified-inlier display
- rejected-outlier display
- residual visualization
- registered overlays
- quality/status presentation

Visualization should communicate scientific results.

It should not independently redefine them.

---

## 15.2 Mosaics

A future mosaic capability should depend on trustworthy underlying registrations.

A visually smooth mosaic should not conceal weak correspondence or geometric errors.

Mosaicking is therefore a downstream capability, not the central scientific goal.

---

## 15.3 Interactive Lunar Maps

An interactive map may eventually help users:

- inspect registered observations
- explore correspondences
- inspect quality information
- compare accepted/rejected results

The map interface itself does not solve the correspondence problem.

---

## 15.4 Backend / API Access

Where API or service layers are developed, they should expose reusable scientific results rather than contain independent registration algorithms.

API availability is not a prerequisite for scientific-core completion.

---

## 15.5 Planetary Extension

The core correspondence/registration concepts may eventually be adaptable to other planetary imagery.

This should be treated as a long-term architectural or research possibility.

It does not imply current Mars, Venus, or general planetary support.

---

# 16. Non-Goals

ChandraMap should not define its primary goal as:

- building the prettiest Moon map
- producing one global lunar mosaic at any cost
- forcing every image pair to align
- maximizing raw feature-match count
- replacing reliable metadata with image-only guessing
- building a complete GIS platform
- creating spacecraft mission-control software
- creating autonomous rover-navigation software
- performing mineral classification as the project's primary function
- supporting every planetary body immediately
- maximizing AI/ML complexity
- using neural methods simply because they are newer
- implementing FAISS merely to say the project uses FAISS
- implementing LightGlue merely to say the project uses LightGlue
- achieving an invented fixed accuracy target
- achieving an invented fixed success rate
- producing arbitrary confidence percentages
- claiming universal scale, illumination, or modality invariance

Some of these areas may become downstream applications or future research extensions.

They are not the primary project goals.

---

## 16.1 "Use AI" Is Not a Goal

AI/ML is a family of methods.

A meaningful project goal is:

> improve measurable lunar correspondence robustness where learned methods provide evidence of value.

---

## 16.2 "Use FAISS" Is Not a Goal

FAISS is a possible vector-search implementation.

The higher-level goal, when global retrieval is required, is:

> efficiently identify promising reference regions while preserving measurable retrieval quality.

---

## 16.3 "Use LightGlue" Is Not a Goal

LightGlue is a possible matching method.

The project goal is:

> improve reliable correspondence under difficult conditions.

The method can change while the goal remains stable.

---

# 17. How Goal Success Should Be Evaluated

Goals should be connected to evidence rather than arbitrary completion percentages.

A conceptual goal/evidence relationship is:

| Goal                    | Evidence of Progress                                      |
| ----------------------- | --------------------------------------------------------- |
| Reliable correspondence | Geometry-consistent verified correspondences              |
| Reliable geometry       | Valid, explicit source→reference transformation           |
| Measurable quality      | Defined scientific metrics with explicit units            |
| Spatial support         | Measured correspondence distribution/coverage             |
| Safe failure            | Explicit rejection when evidence is inadequate            |
| Scale robustness        | Controlled evaluation on scale-stress cases               |
| Illumination robustness | Controlled evaluation across illumination differences     |
| Retrieval capability    | Retrieval metrics against known reference truth           |
| Reproducibility         | Result traceable to data, configuration, code, and method |
| Engineering quality     | Focused tests and clear architectural ownership           |
| Research value          | Controlled comparison, ablation, and failure analysis     |

No fixed numerical targets are defined here.

---

## 17.1 Scientific Success vs Software Success

These are different.

### Software Success

The pipeline:

- executes correctly
- preserves scientific semantics
- handles expected failure correctly
- emits valid result/status information

### Scientific Success

The method demonstrates useful performance under controlled lunar benchmark conditions.

A correctly implemented V1 may be scientifically weak on difficult cases while still being a successful baseline implementation.

Its limitations provide evidence motivating later research.

---

## 17.2 Correct Rejection Is Positive Evidence

Project progress should not be measured only by increasing the number of accepted registrations.

If the available evidence is unreliable, correctly rejecting the result demonstrates desirable system behavior.

The project should prefer:

```text
Correct Rejection
```

over:

```text
Incorrect Accepted Registration
```

---

## 17.3 Performance and Efficiency

Runtime and memory efficiency are legitimate engineering goals.

They remain secondary to:

- correctness
- scientific validity
- reproducibility

No runtime, memory, throughput, or speedup target is defined by this file.

---

# 18. Goal vs Milestone vs Task

Goals, milestones, and tasks exist at different levels.

### Goal

> Provide trustworthy registration-quality measurement.

### Possible Milestone

> Establish the project's authoritative evaluation subsystem.

### Possible Task

> Implement and test one specific metric.

The goal remains stable even when the implementation plan changes.

Therefore, this document should not contain:

- file-by-file implementation steps
- issue-level work
- completion checklists
- dates
- quarterly plans

Those belong in planning and task documentation.

---

# 19. Goal Horizons

The following grouping describes conceptual scope, not implementation status or schedule.

### Core Research Goals

- reliable correspondence
- geometric verification
- registration
- measurable quality
- explicit rejection
- classical V1 baseline
- scale/sensor-aware V2 research
- reproducible benchmarking

---

### Advanced Research Goals

- stronger learned/local correspondence
- unknown-location retrieval
- sub-pixel refinement
- local/piecewise geometry
- terrain-aware reasoning
- uncertainty and confidence calibration
- adaptive method selection

---

### Downstream / Longer-Term Goals

- richer visualization
- map interfaces
- mosaicking
- backend/API exposure
- larger-scale reference search
- possible extension to additional planetary imagery

These categories do not state what is currently complete, active, or scheduled.

---

# 20. Research Questions

ChandraMap's goals support research questions including:

1. How strong is a simple classical lunar-registration baseline?
2. Which physically meaningful scale-handling strategies improve cross-resolution correspondence?
3. Which representations remain robust under significant illumination changes?
4. Which sensor-aware preprocessing strategies provide measurable benefit?
5. Which IIRS-derived representations preserve useful spatial correspondence?
6. Under which conditions do learned matchers outperform classical methods?
7. When does global reference retrieval become necessary?
8. How should spatial coverage influence acceptance decisions?
9. When should a registration be rejected even if a transform can be estimated?
10. How much does tie-point refinement improve independent final accuracy?
11. When does terrain relief make a simple global transform inadequate?
12. Which failure modes dominate different sensor/modality combinations?
13. How strongly do terrestrial-trained models experience lunar domain shift?
14. Which additional processing stages provide real value relative to their computational complexity?

These questions are research objectives.

They are not statements that the answers are already known.

---

# 21. Goal Maintenance

Core project goals should remain relatively stable.

Update this document when:

- the primary scientific purpose changes
- the core registration scope changes materially
- benchmark architecture changes fundamentally
- project priorities change substantially
- a downstream application becomes part of the primary project purpose
- a major research direction becomes central to ChandraMap

Do not modify `goals.md` merely because:

- one function changed
- one dependency changed
- a small implementation task was completed
- one benchmark experiment was run
- one issue was closed

Those events belong in more appropriate task, changelog, benchmark, or roadmap documentation.

---

## 21.1 Goals vs Roadmap

[`ROADMAP.md`](../../ROADMAP.md) should answer:

> **How and in what sequence will the project pursue future work?**

This document answers:

> **What outcomes matter to the project?**

---

## 21.2 Goals vs Problem Statement

[`problem-statement.md`](./problem-statement.md) answers:

> **What scientific and engineering challenge exists?**

This document answers:

> **What should ChandraMap achieve in response to that challenge?**

---

## 21.3 Goals vs Project Overview

[`overview.md`](./overview.md) answers:

> **What is ChandraMap?**

This document focuses specifically on:

> **What does success mean for ChandraMap?**

---

## 21.4 Goals vs Benchmark Rules

[Benchmark Rules](../../.ai/development/BENCHMARK_RULES.md) define:

> **How should scientific comparisons be conducted fairly?**

This document defines:

> **Fair and reproducible benchmarking as a project goal.**

---

## 21.5 Goals vs Implementation Tasks

Task documents such as [V1 Implementation Task](../../.ai/tasks/V1_IMPLEMENTATION.md) describe:

> **What implementation work is required to pursue a goal?**

They should not redefine the goals themselves.

---

# 22. Related Documents

- [Project Overview](./overview.md) — introduction to ChandraMap as a project
- [Problem Statement](./problem-statement.md) — scientific and engineering challenge ChandraMap addresses
- [Project Context](../../.ai/context/PROJECT_CONTEXT.md) — canonical project identity and research context
- [Domain Context](../../.ai/context/DOMAIN_CONTEXT.md) — lunar imaging and registration constraints
- [Terminology](../../.ai/context/TERMINOLOGY.md) — canonical ChandraMap vocabulary
- [Dataset Context](../../.ai/context/DATASETS.md) — scientific products, metadata, provenance, and data governance
- [V1 Scope](../../.ai/context/V1_SCOPE.md) — canonical Benchmark V1 boundary
- [System Overview](../../.ai/architecture/SYSTEM_OVERVIEW.md) — high-level architectural responsibilities
- [Processing Pipeline](../../.ai/architecture/PIPELINE.md) — scientific processing order
- [Module Map](../../.ai/architecture/MODULE_MAP.md) — repository/module responsibility ownership
- [Data Flow](../../.ai/architecture/DATA_FLOW.md) — scientific information, coordinate, provenance, and result flow
- [Testing Rules](../../.ai/development/TESTING_RULES.md) — software and scientific testing standards
- [Benchmark Rules](../../.ai/development/BENCHMARK_RULES.md) — benchmark methodology and comparability
- [Documentation Rules](../../.ai/development/DOCUMENTATION_RULES.md) — documentation accuracy and maintenance
- [V1 Implementation Task](../../.ai/tasks/V1_IMPLEMENTATION.md) — implementation work for the canonical classical baseline
- [Roadmap](../../ROADMAP.md) — planned project evolution

ChandraMap's goals should remain method-independent, scientifically measurable, reproducible, and stable enough that changes in implementation can be evaluated against them rather than silently redefining what success means.

<!-- Source specification: :contentReference[oaicite:0]{index=0} -->
