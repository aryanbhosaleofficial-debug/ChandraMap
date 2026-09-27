# Research

The `research/` directory contains the scientific knowledge, investigation, reasoning, and methodological analysis that informs the development of **ChandraMap**.

ChandraMap is a lunar image correspondence and registration system designed to align imagery of the same lunar region under potentially different spatial resolutions, illumination conditions, viewing conditions, sensors, image representations, and geometric characteristics.

The purpose of this directory is not simply to collect papers or notes. It provides a traceable research layer between the scientific problem and the engineering and benchmarking work that implements and evaluates solutions.

> **Research should explain why a method is worth investigating. Experiments should measure what happens when it is tested.**

---

## Purpose

Research in ChandraMap supports the project by documenting:

- scientific questions
- research hypotheses
- literature and methodological context
- lunar image-registration challenges
- sensor and dataset investigations
- algorithm investigations
- geometric-model reasoning
- representation and preprocessing analysis
- evaluation methodology considerations
- scientific assumptions
- observed limitations
- unresolved questions
- research findings
- future research directions

The directory should make it possible for another researcher to understand:

1. **What problem was being investigated?**
2. **Why was it considered important?**
3. **What evidence or prior work motivated the investigation?**
4. **What hypothesis was proposed?**
5. **How could the hypothesis be tested?**
6. **Which experiment tested it?**
7. **What was actually measured?**
8. **What was observed?**
9. **What remains uncertain?**
10. **What should be investigated next?**

---

## Research Is Not the Implementation

`research/` should contain scientific investigation and reasoning, not the production implementation of ChandraMap.

The repository separates scientific knowledge from executable implementation and standardized evaluation so that research claims remain traceable to the code and experiments that support them.

A simplified relationship is:

```text
research/
    Scientific question
            │
            ▼
    Hypothesis / rationale
            │
            ▼
experiments/
    Controlled evaluation
            │
            ▼
src/
    Implementation
            │
            ▼
results/
    Measured outputs
            │
            ▼
benchmarks/
    Standardized evaluation
            │
            ▼
    Evidence for research conclusions
```

This separation does not mean the directories are independent. They are deliberately connected.

---

## Research → Experiment → Result

A central principle of ChandraMap is that research questions should become measurable whenever practical.

The conceptual research cycle is:

```text
Research Question
        ↓
Scientific Hypothesis
        ↓
Method / Candidate Approach
        ↓
Controlled Experiment
        ↓
Measured Results
        ↓
Analysis
        ↓
Research Finding
        ↓
Future Experiment / Engineering Decision
```

For example:

```text
Question
Does a structure-focused representation improve correspondence
when lunar illumination conditions differ?

        ↓

Hypothesis
Gradient or structural representations may provide features
that are less dependent on absolute image intensity.

        ↓

Experiment
EXP-003 — Gradient Representation

        ↓

Measurement
Candidate matches
Verified inliers
Inlier ratio
Spatial coverage
Independent checkpoint RMSE
Runtime

        ↓

Analysis
Compare the structural representation against the controlled
raw-grayscale baseline.

        ↓

Finding
[TBD — based on measured results]

        ↓

Next step
[TBD]
```

A research document should not claim that a method works merely because it appears theoretically promising. The corresponding experiment, measurements, and limitations should be identified whenever evidence is required.

---

# Research vs Experiments

The two directories serve different purposes.

| `research/`                      | `experiments/`                      |
| -------------------------------- | ----------------------------------- |
| Scientific questions             | Controlled tests                    |
| Literature and context           | Experimental setup                  |
| Hypotheses                       | Controlled variables                |
| Method investigation             | Method implementation/configuration |
| Scientific reasoning             | Measurements                        |
| Theoretical analysis             | Quantitative evaluation             |
| Dataset and sensor investigation | Reproducible execution              |
| Open questions                   | Experimental observations           |
| Methodological limitations       | Experiment-specific conclusions     |
| Future research directions       | Experiment artifacts and results    |

A research document may motivate several experiments.

An experiment may provide evidence for several research questions.

Neither should be treated as a replacement for the other.

### Example

A research note may investigate:

> How does changing Sun angle affect local image correspondence?

That investigation may motivate:

- a raw grayscale baseline,
- a gradient representation experiment,
- a local-contrast experiment,
- a stronger matcher experiment,
- a geometric-model experiment.

The controlled measurements belong in the corresponding experiment documentation under `experiments/`.

---

# Research vs Documentation

`research/` and `docs/` both contain written material, but their purposes differ.

| `research/`                       | `docs/`                                     |
| --------------------------------- | ------------------------------------------- |
| Investigates scientific questions | Explains the existing project               |
| Develops hypotheses               | Documents established behavior              |
| Examines alternative methods      | Documents usage and architecture            |
| Records scientific reasoning      | Provides contributor/developer guidance     |
| Discusses uncertainty             | Describes supported workflows               |
| Can contain unresolved questions  | Should generally explain the current system |
| Supports research decisions       | Supports project usage and maintenance      |

### Example

A document asking:

> Could gradient magnitude provide more stable correspondence under different illumination?

belongs in `research/`.

Documentation explaining:

> How to run EXP-003 and where its configuration is stored

belongs with the experiment and project documentation.

Research may eventually produce conclusions that become stable project documentation, but the conclusion should be established before being presented as project behavior.

---

# Research vs Benchmarks

The research and benchmark layers have complementary responsibilities.

```text
Research
    │
    │ asks scientific questions
    ▼
Experiments
    │
    │ test candidate methods
    ▼
Benchmarks
    │
    │ standardize evaluation
    ▼
Results
    │
    │ provide measurable evidence
    ▼
Research findings
```

### Research

Determines what is worth investigating and why.

### Experiments

Determine how a specific question can be tested under controlled conditions.

### Benchmarks

Define standardized datasets, ground truth, metrics, evaluation procedures, and acceptance criteria.

### Results

Provide the measured evidence produced by execution.

Research should **not silently redefine the official V1 benchmark**.

If a research idea becomes part of the official benchmark, it should be formalized through the appropriate benchmark and experiment documentation rather than becoming an informal rule inside a research note.

Relevant benchmark definitions should remain under:

`benchmarks/`

---

# Scientific Context

ChandraMap addresses lunar image correspondence and registration under conditions that can differ substantially between source images.

Relevant imaging contexts include, where supported by the dataset and experiment:

- Chandrayaan-2 OHRC
- TMC-2
- IIRS
- other reference imagery where explicitly documented

The registration problem can involve:

- different spatial resolutions
- different effective pixel scales
- scale differences
- Sun-angle differences
- changing shadow geometry
- contrast differences
- sensor differences
- image representation differences
- local geometric distortions
- non-identical viewing conditions
- correspondence uncertainty
- registration error
- sub-pixel localization

These effects should not be reduced to a simple brightness difference.

## Illumination Is a Geometric and Appearance Problem

Changing lunar Sun geometry can change:

- shadow location
- shadow extent
- visible surface boundaries
- local gradients
- apparent terrain structure
- contrast relationships
- feature visibility

Consequently, simple intensity or contrast normalization cannot necessarily recover correspondence when illumination geometry has changed the observed structure.

For this reason, research may investigate representations that emphasize structural information rather than absolute brightness.

However, a structure-focused representation should not automatically be described as illumination invariant.

For example:

> Gradient representations may reduce dependence on absolute intensity.

is materially different from:

> Gradient representations remove illumination effects.

The latter requires experimental evidence and should not be asserted without it.

---

# Scientific Documentation Principles

Research documents should clearly distinguish different types of statements.

## Separate Facts From Hypotheses

Use explicit labels whenever appropriate.

| Type                | Meaning                                                |
| ------------------- | ------------------------------------------------------ |
| **Known**           | Supported by established project/source information    |
| **Hypothesis**      | A proposition that requires testing                    |
| **Assumption**      | A condition accepted for the current investigation     |
| **Observation**     | Something directly observed during analysis            |
| **Measured Result** | A quantified result produced by an evaluation          |
| **Interpretation**  | Reasoning about what an observation or result may mean |
| **Conclusion**      | A conclusion supported by the available evidence       |
| **Future Work**     | An unresolved or proposed next investigation           |

### Example

**Hypothesis**

> A gradient representation may produce more stable local correspondences under different illumination conditions.

**Measured result**

> The experiment produced an inlier ratio of `[TBD]` and independent checkpoint RMSE of `[TBD]`.

**Interpretation**

> The measured results suggest `[TBD]`.

**Conclusion**

> `[TBD — only after sufficient controlled evidence is available]`.

This prevents hypotheses from gradually becoming undocumented "facts".

---

# Avoid Unsupported Claims

Research documentation must not present an untested method as successful.

Avoid statements such as:

- "This method is better."
- "This algorithm is accurate."
- "This representation solves illumination problems."
- "The method is robust."
- "The registration is highly reliable."

unless those claims are supported by documented evidence and an appropriate evaluation.

Prefer language such as:

- "The experiment evaluates whether..."
- "The hypothesis is that..."
- "The measured results indicate..."
- "The current evidence suggests..."
- "This observation was obtained under..."
- "This remains unverified..."
- "The result is limited to..."
- "Further evaluation is required..."

Scientific precision is more important than persuasive language.

---

# Research Question Structure

A research question should be sufficiently specific that it can eventually lead to a measurable investigation.

Recommended structure:

```text
Research Question
├── Motivation
├── Scientific Context
├── Hypothesis
├── Existing Evidence
├── Proposed Method
├── Evaluation Strategy
├── Expected Outcome
├── Observed Outcome
├── Limitations
└── Follow-up Experiment
```

A research question does not need to contain every section when the investigation is exploratory, but important claims should remain traceable to evidence.

## Recommended Research Question Template

```markdown
# Research Question: [Title]

## Question

[Precise scientific question.]

## Motivation

[Why this question matters to ChandraMap.]

## Scientific Context

[Relevant lunar imaging, registration, sensor, geometric, or
computer-vision context.]

## Hypothesis

[What is expected and why.]

## Existing Evidence

[Relevant literature, observations, previous experiments, or
project evidence.]

## Proposed Investigation

[Method or experiment that could test the hypothesis.]

## Evaluation Strategy

[Metrics, datasets, image pairs, conditions, and controls.]

## Expected Outcome

[What would constitute evidence relevant to the hypothesis.]

## Observed Outcome

[TBD]

## Interpretation

[TBD]

## Limitations

[Known limitations and unresolved uncertainty.]

## Follow-up Experiment

[TBD]
```

---

# Recommended Research Artifact Types

The `research/` directory may contain several types of scientific material.

## Research Notes

Short investigations capturing an observation, question, or methodological idea.

Examples:

```text
research/notes/
research/observations/
```

Exact subdirectories should follow the repository's actual structure. If a structure has not yet been established, use the simplest organization that keeps the material discoverable.

## Literature Reviews

Documents summarizing relevant scientific or technical literature.

Potential topics include:

- lunar image registration
- feature matching
- multi-scale correspondence
- illumination-aware matching
- cross-modal image registration
- geometric verification
- sub-pixel registration
- planetary photogrammetry

Literature summaries should distinguish the source's conclusions from ChandraMap's own interpretation.

## Method Investigations

Analysis of candidate algorithms or representations before implementation.

Examples include:

- feature detectors
- local descriptors
- dense correspondence
- gradient representations
- edge representations
- structural representations
- geometric transformation models
- sub-pixel refinement

A method investigation should not imply that a method is implemented merely because it has been researched.

## Sensor Investigations

Analysis of the characteristics relevant to correspondence and registration.

Potential topics include:

- OHRC
- TMC-2
- IIRS
- spatial resolution
- pixel scale/GSD
- spectral characteristics
- image geometry
- sensor-specific preprocessing
- cross-instrument correspondence

When sensor information is uncertain or unavailable, record the uncertainty rather than filling the gap with assumptions.

## Dataset Investigations

Research concerning:

- image-pair suitability
- overlap
- illumination conditions
- scale differences
- metadata
- ground truth availability
- image quality
- failure cases
- benchmark suitability

Dataset investigation should not replace the official dataset documentation under `data/` or benchmark definitions under `benchmarks/`.

## Theoretical Analysis

Where useful, research may document:

- geometric assumptions
- transformation-model assumptions
- error propagation
- feature stability
- scale effects
- illumination effects
- residual behavior
- correspondence uncertainty

The analysis should identify assumptions explicitly.

---

# Research Topics

The following areas are aligned with the ChandraMap research problem.

## Lunar Image Registration

Potential research topics include:

- image correspondence
- feature matching
- geometric registration
- local alignment
- transformation models
- registration uncertainty
- correspondence validation

## Multi-Resolution Registration

Potential topics include:

- OHRC versus lower-resolution imagery
- scale differences
- reference pyramids
- resolution normalization
- effective comparison scale
- downsampling
- interpolation effects

Upsampling should not be described as recovering spatial information that was absent from the source image.

## Illumination and Appearance

Potential topics include:

- Sun-angle differences
- shadow changes
- local contrast
- gradient representations
- edge representations
- structural representations
- intensity normalization
- appearance variation

The scientific distinction between intensity change and shadow-geometry change should be preserved.

## Sensor Differences

Potential topics include:

- OHRC
- TMC-2
- IIRS
- cross-instrument correspondence
- sensor-specific preprocessing
- representation conversion
- spatial-resolution mismatch

If IIRS imagery is investigated, research should explicitly address how its multidimensional spectral information is converted into a registration-compatible representation.

## Geometric Modeling

Potential topics include:

- affine transformations
- homographies
- residual fields
- local geometric distortions
- terrain-related effects
- model adequacy
- spatially varying registration error

Affine and homography models should be treated as modeling assumptions rather than universal descriptions of lunar imaging geometry.

## Sub-Pixel Registration

Potential topics include:

- sub-pixel localization
- interpolation
- refinement objectives
- residual reduction
- control-point refinement
- independent check points

## Evaluation

Potential research topics include:

- reprojection/check-point RMSE
- median error
- P90/P95 error
- inlier count
- inlier ratio
- spatial coverage
- runtime
- success rate
- meaningful ground error where valid

---

# Connection to V1

Research supports the V1 development process by identifying questions that can be converted into controlled experiments.

The current V1 research/experimental progression includes concepts such as:

1. Known source/reference pair
2. SIFT baseline
3. Reference scale pyramid
4. Structure-focused preprocessing / gradient representation
5. Stronger geometric evaluation
6. Residual analysis
7. Sub-pixel refinement

These stages should not be interpreted as proof that every possible method is implemented.

The distinction is:

| State                      | Meaning                                       |
| -------------------------- | --------------------------------------------- |
| **Implemented experiment** | Exists in the repository and can be evaluated |
| **Planned experiment**     | Defined but not yet completed                 |
| **Research idea**          | Scientific possibility under investigation    |
| **Future research**        | Candidate direction not currently part of V1  |

Current implementation status should always be taken from the corresponding experiment documentation and repository state.

---

# Example V1 Research Chain

A research investigation may develop approximately as follows:

```text
Research:
Multi-scale lunar correspondence
        │
        ▼
EXP-001:
SIFT Baseline
        │
        ▼
Observation:
Scale differences affect correspondence
        │
        ▼
EXP-002:
Scale Pyramid
        │
        ▼
Research:
Could structural representations improve
matching under appearance differences?
        │
        ▼
EXP-003:
Gradient Representation
        │
        ▼
Research:
How does the geometric model affect residuals?
        │
        ▼
EXP-004:
Affine vs Homography
        │
        ▼
EXP-005:
Residual Analysis
        │
        ▼
Research:
Can verified correspondences be localized more precisely?
        │
        ▼
EXP-006:
Sub-Pixel Refinement
```

This is an example of research motivating measurable work. It should not be interpreted as evidence that every hypothesis in the chain has already been validated.

---

# Research Traceability

Research claims should be traceable to the repository artifacts that support them.

A useful traceability chain is:

```text
Research Document
      │
      ├── Source / Literature
      │
      ├── Dataset / Image Pair
      │
      ├── Hypothesis
      │
      ├── Experiment ID
      │
      ├── Configuration
      │
      ├── Results
      │
      └── Conclusion
```

Where applicable, research documents should reference:

- experiment IDs
- experiment README files
- dataset identifiers
- image-pair identifiers
- configuration files
- benchmark definitions
- result artifacts
- relevant implementation modules
- relevant tests
- source literature

Avoid copying large amounts of experiment output into research notes when the authoritative result already exists elsewhere.

Instead, link to the authoritative artifact.

---

# Referencing Experiments

Use explicit experiment identifiers whenever an investigation has been tested.

For example:

```markdown
See [EXP-003 — Gradient Representation](../experiments/v1/preprocessing/EXP-003-gradient-representation/README.md).
```

From a document located deeper inside `research/`, adjust the relative path according to the actual location.

An experiment reference should ideally identify:

- experiment ID
- experiment name
- purpose
- relevant result
- limitations

Do not cite an experiment as evidence for a claim if the experiment did not actually test that claim.

---

# Referencing Data

Research documents should identify the data used when data materially affects the conclusion.

Useful information includes:

- source image
- reference image
- sensor
- source GSD
- reference GSD
- effective comparison scale
- image overlap
- illumination condition
- viewing condition where available
- projection/geometric metadata
- ground-truth source
- independent check-point availability

If the exact information is unavailable:

```text
[TBD]
```

should be used rather than inventing metadata.

Research should link to the authoritative dataset documentation under `data/` where possible.

---

# Referencing Methods

When discussing an algorithm, document:

- method name
- version where relevant
- source/reference
- implementation status
- parameters if evaluated
- assumptions
- limitations
- corresponding experiment

Distinguish carefully between:

```text
Literature method
        ≠
Proposed method
        ≠
Implemented method
        ≠
Evaluated method
        ≠
Benchmark-qualified method
```

A method can be scientifically interesting without being implemented or benchmarked.

---

# Research Reproducibility

Research should be reproducible whenever practical.

A research document should identify enough information for another researcher to understand how its evidence was obtained.

At minimum, document the following where applicable:

| Field              | Description                                           |
| ------------------ | ----------------------------------------------------- |
| Research question  | Exact question being investigated                     |
| Motivation         | Why the question matters                              |
| Source material    | Literature, dataset, observation, or project evidence |
| Dataset/image pair | Data used in the investigation                        |
| Preprocessing      | Relevant preprocessing assumptions                    |
| Method             | Algorithm or representation investigated              |
| Parameters         | Important parameters                                  |
| Conditions         | Illumination, scale, sensor, geometry, etc.           |
| Metrics            | Measurements used                                     |
| Expected outcome   | What would support or challenge the hypothesis        |
| Observed outcome   | What actually occurred                                |
| Limitations        | Known uncertainty and constraints                     |
| Experiment ID      | Corresponding controlled experiment                   |
| Result artifact    | Location of measured evidence                         |

Where exact information is not yet available, use:

```text
[TBD]
```

rather than reconstructing or guessing it.

---

# Reproducibility and Provenance

Research evidence should ideally be connected to:

```text
Code
Configuration
Dataset
Ground Truth
Metrics
Environment
Execution
Result
```

Where the project provides explicit provenance mechanisms, research documents should use those mechanisms rather than creating a parallel undocumented system.

Relevant reproducibility documentation should remain authoritative in the appropriate project or benchmark documentation.

---

# Research and Ground Truth

Ground truth is a scientific evaluation asset and should not be treated as a generic collection of "correct matches".

Research involving registration accuracy should distinguish between:

- candidate correspondences
- geometrically verified inliers
- points used to fit a transformation
- independent check points
- final registration outputs

A transformation fitted using a set of points should not be described as independently validated by those same points.

Where independent check points are available, they should be used to evaluate registration accuracy separately from fitting.

Research conclusions about accuracy should therefore identify:

- how the transformation was fitted
- which points were used
- which points were independently evaluated
- which metric was used
- whether spatial coverage was considered

---

# Research and Spatial Coverage

Registration quality is not determined solely by the number of matches.

Research should consider whether correspondences are spatially distributed across the useful overlap.

Potential measures include:

- grid coverage
- convex-hull coverage
- bounding-box coverage
- spatial density
- clustering

The exact metric should follow the benchmark or experiment implementation.

A large number of correspondences concentrated in one small area should not automatically be interpreted as strong global registration evidence.

---

# Research and Illumination

Illumination is a particularly important research dimension for lunar registration.

A research investigation should distinguish between:

```text
Similar illumination
        │
        └── appearance is relatively comparable

Different illumination
        │
        ├── changed brightness
        ├── changed local contrast
        ├── changed gradients
        └── potentially changed shadow geometry
```

The last case is especially important.

A normalization operation can modify image intensity, but it cannot necessarily recreate a surface structure that is hidden or differently revealed because the illumination geometry changed.

Consequently, research claims involving illumination should identify the actual illumination conditions evaluated.

---

# Research and Sensor Modality

Cross-sensor research should document the characteristics relevant to the correspondence problem.

For example:

```text
OHRC
 └── high-resolution optical imagery

TMC-2
 └── lower-resolution terrain imagery

IIRS
 └── imaging infrared hyperspectral data
```

These descriptions are conceptual and should not replace the actual dataset metadata.

If a sensor has substantially different spatial or spectral characteristics, the research should document the representation used for correspondence.

For IIRS, a normal 2D local matcher should not be assumed to operate directly on the complete hyperspectral cube. If IIRS is used, the research should document the actual 2D conversion method, such as a selected band, derived composite, PCA representation, or structural representation, only if that method is actually implemented.

---

# Research Artifact Organization

The exact directory structure should follow the repository's established structure. Where subdirectories are introduced, they should reflect meaningful research categories rather than arbitrary hierarchy.

A possible organization is:

```text
research/
├── README.md
├── literature/
├── questions/
├── methods/
├── sensors/
├── datasets/
├── analysis/
├── notes/
└── references/
```

This is a **recommended organizational pattern**, not a claim that all of these directories currently exist.

If the repository already defines a different structure, the existing repository structure is authoritative.

### Avoid unnecessary hierarchy

Do not create multiple levels of folders simply to make the directory appear more scientific.

A research artifact should be:

- easy to discover
- clearly named
- traceable
- version controlled
- connected to the relevant experiment or data

---

# Recommended Naming

Research filenames should communicate the subject clearly.

Prefer:

```text
illumination-variation.md
gradient-representation.md
cross-sensor-registration.md
subpixel-localization.md
affine-vs-homography.md
```

over:

```text
notes1.md
idea-final.md
new-research.md
test.md
important.md
```

If a research investigation is directly associated with an experiment, include the experiment ID where useful:

```text
EXP-003-gradient-representation.md
```

The repository's existing naming conventions should take precedence.

---

# Research Lifecycle

A research artifact can progress through several states:

```text
Idea
  ↓
Question
  ↓
Hypothesis
  ↓
Investigation
  ↓
Experiment
  ↓
Measured Evidence
  ↓
Analysis
  ↓
Finding
  ↓
Engineering Decision / Future Research
```

A document should make its current state clear.

For example:

```markdown
Status: Hypothesis
Evidence: Literature review only
Experiment: Not yet implemented
Conclusion: Not established
```

or:

```markdown
Status: Evaluated
Experiment: EXP-003
Evidence: Quantitative results available
Conclusion: Limited to the evaluated dataset and conditions
```

---

# From Research Idea to Experiment

When a research idea becomes sufficiently concrete to test, create or update an experiment under `experiments/`.

A useful transition checklist is:

### 1. Define the question

What scientific uncertainty is being investigated?

### 2. Define the hypothesis

What outcome is expected and why?

### 3. Identify the independent variable

What changes between the control and experimental condition?

### 4. Define the controls

What should remain fixed?

### 5. Select data

Which source/reference images and conditions will be evaluated?

### 6. Define evaluation

Which metrics will determine the outcome?

### 7. Define reproducibility

Which configuration, seed, environment, and metadata must be recorded?

### 8. Execute

Run the controlled experiment.

### 9. Analyze

Interpret the measured results without exceeding the evidence.

### 10. Update research

Record the finding, uncertainty, limitations, and next question.

---

# Controlled Research

When an experiment is used to answer a research question, the experimental variable should be isolated as much as practical.

For example, when evaluating a representation:

```text
Control:
Raw grayscale
        │
        ├── same image pair
        ├── same scale handling
        ├── same SIFT configuration
        ├── same matcher
        ├── same RANSAC protocol
        └── same evaluation points

Experimental:
Gradient / structural representation
        │
        ├── same image pair
        ├── same scale handling
        ├── same SIFT configuration
        ├── same matcher
        ├── same RANSAC protocol
        └── same evaluation points
```

This makes the representation the primary variable rather than unintentionally changing several parts of the pipeline.

The same principle applies to scale, geometry, matching, and refinement experiments.

---

# Research Claims and Evidence Levels

Not every research statement has the same evidentiary strength.

A useful internal classification is:

| Evidence level               | Example                                         |
| ---------------------------- | ----------------------------------------------- |
| Literature-supported         | Reported by an external source                  |
| Project observation          | Observed in ChandraMap data                     |
| Single experiment            | Observed in one controlled run                  |
| Repeated experiment          | Observed across repeated controlled evaluations |
| Benchmark evidence           | Supported under the official benchmark protocol |
| Established project behavior | Documented and reproducibly implemented         |

This classification helps prevent a result from one image pair from being generalized to the entire lunar registration problem.

---

# Quantitative Evidence

Where numerical evaluation is available, research should prefer measured metrics over subjective confidence language.

Relevant metrics may include:

- candidate match count
- verified inlier count
- inlier ratio
- spatial coverage
- independent checkpoint RMSE
- median error
- P90/P95 error
- maximum error where appropriate
- meaningful ground error where valid
- runtime
- success/failure rate

The exact metrics should follow the applicable experiment and benchmark definitions.

Do not invent values.

Do not report an improvement percentage unless it has actually been calculated from documented measurements.

---

# Qualitative Evidence

Visual inspection can be valuable, especially for understanding failure modes.

Useful visual evidence may include:

- source/reference image pairs
- representation images
- gradient magnitude
- gradient orientation
- edge maps
- candidate matches
- verified inliers
- spatial match distribution
- registration overlays
- residual vector fields
- error maps

Qualitative inspection supports quantitative analysis but does not replace independent evaluation.

---

# Failure Analysis

Research should preserve failures rather than documenting only successful cases.

Potential failure categories include:

- insufficient keypoints
- weak gradients
- noise amplification
- edge fragmentation
- irrelevant boundaries
- repetitive terrain
- low-feature terrain
- crater-shadow ambiguity
- shadow reversal
- gradient polarity changes
- scale mismatch
- modality mismatch
- interpolation artifacts
- unstable normalization
- clustered correspondences
- inadequate geometric model

Only classify a failure under a specific category when there is evidence supporting that interpretation.

A failure analysis should ideally contain:

```text
Condition
    ↓
Observed behavior
    ↓
Evidence
    ↓
Suspected cause
    ↓
Impact
    ↓
Reproduction procedure
    ↓
Potential follow-up experiment
```

---

# Research Limitations

Every substantial research document should identify limitations.

Common limitations relevant to ChandraMap may include:

- limited image-pair diversity
- limited illumination diversity
- sensor-specific behavior
- spatial-resolution mismatch
- incomplete ground truth
- dependence on geometric assumptions
- limited spatial coverage
- interpolation effects
- feature sparsity
- repetitive terrain
- shadow-related ambiguity
- modality differences
- limited independent check points

A limitation should not automatically invalidate a result. It defines the conditions under which the result should be interpreted.

---

# Literature and External Sources

Research literature should be recorded with enough information to identify the source.

Where applicable, include:

- title
- authors
- publication year
- venue
- DOI or persistent identifier
- URL
- relevant section/page
- reason for relevance to ChandraMap

Do not copy large portions of copyrighted publications into the repository.

Prefer concise summaries and clearly attributed interpretations.

A literature-derived statement should remain distinguishable from an original ChandraMap observation.

---

# Research References

A research document may use a references section such as:

```markdown
## References

1. [Author(s)], "[Paper Title]," _Venue_, [Year].
   DOI: [DOI or persistent identifier]

2. [Project / Dataset Source].
   [URL or repository reference]

3. ChandraMap experiment:
   [EXP-ID and relative repository link]
```

Use the project's established citation conventions if they are defined elsewhere.

---

# Relationship to Repository Components

The research directory is part of a larger traceability system.

| Directory      | Primary role                                       |
| -------------- | -------------------------------------------------- |
| `research/`    | Scientific investigation and knowledge development |
| `docs/`        | General project and developer documentation        |
| `experiments/` | Controlled experiments                             |
| `benchmarks/`  | Standardized evaluation definitions and protocols  |
| `configs/`     | Configuration                                      |
| `src/`         | Implementation                                     |
| `tests/`       | Software and behavioral verification               |
| `data/`        | Dataset and ground-truth assets/documentation      |
| `results/`     | Experimental outputs and measured artifacts        |

The exact contents and conventions of each directory are governed by the repository's current structure.

---

# Recommended Research-to-Repository Mapping

```text
Scientific Question
        │
        ▼
research/
        │
        │ hypothesis becomes testable
        ▼
experiments/
        │
        │ method is implemented
        ▼
src/
        │
        │ data is evaluated
        ▼
data/
        │
        │ standardized evaluation
        ▼
benchmarks/
        │
        │ measured outputs
        ▼
results/
        │
        ▼
research/
        │
        └── documented finding / limitation
```

This loop allows scientific reasoning to remain connected to executable evidence.

---

# What Belongs in `research/`

Appropriate content includes:

- research questions
- hypotheses
- scientific background
- literature reviews
- methodological investigations
- sensor investigations
- dataset investigations
- theoretical analysis
- design rationale
- scientific assumptions
- research observations
- research findings
- unresolved questions
- methodological limitations
- future research directions
- analysis that informs an experiment
- analysis of experiment findings when it is genuinely research-oriented

---

# What Does Not Belong in `research/`

Avoid placing the following here unless the research artifact itself requires a reference:

### Production implementation

Do not place source implementation in `research/`.

Use:

```text
src/
```

### Controlled experiment definitions

Do not duplicate full experiment specifications in research notes.

Use:

```text
experiments/
```

and reference the experiment from the research document.

### Official benchmark methodology

Do not define benchmark rules informally in research notes.

Use:

```text
benchmarks/
```

### Dataset assets

Do not store benchmark datasets, raw imagery, or generated datasets inside research notes.

Use the appropriate `data/` structure.

### Generic project documentation

Do not move setup, installation, contribution, architecture, or user documentation into research merely because it contains technical information.

Use:

```text
docs/
```

or the appropriate project-level documentation.

### Untracked temporary output

Avoid storing temporary:

- logs
- debug images
- cache files
- notebooks with uncontrolled outputs
- generated binaries
- intermediate artifacts

unless they are intentionally part of a documented research artifact.

---

# Research Notebooks and Exploratory Analysis

Exploratory notebooks may be useful during research, but they should not automatically become authoritative evidence.

If notebooks are used:

- record their purpose
- identify the dataset
- record important parameters
- preserve relevant outputs
- avoid relying on hidden notebook state
- make important conclusions reproducible through an experiment or script
- link the notebook to the relevant research question or experiment

A notebook should not be the only record of an important benchmark result when the project provides a reproducible experiment workflow.

---

# Research Data Handling

Research should distinguish between:

```text
Source Data
    ↓
Prepared Data
    ↓
Experimental Input
    ↓
Intermediate Representation
    ↓
Experimental Output
    ↓
Evaluation Result
```

For example, a gradient image generated during an experiment is not necessarily a new dataset.

It may be an intermediate representation produced from the original image.

Research documentation should identify the distinction when it matters for reproducibility.

---

# Research and Experimental Parameters

When a research document depends on specific experimental parameters, link to the authoritative experiment/configuration instead of duplicating values unnecessarily.

Important parameters may include:

- source/reference image identifiers
- source/reference sensor
- source/reference GSD
- effective comparison scale
- preprocessing
- representation
- feature detector
- descriptor
- matcher
- filtering thresholds
- geometric model
- RANSAC parameters
- refinement parameters
- random seed
- evaluation points
- software environment

If the exact parameter is not known:

```text
[TBD]
```

---

# Research and Experimental Results

Research should distinguish:

```text
Expected result
        ≠
Observed result
        ≠
Interpretation
        ≠
Conclusion
```

### Example

**Expected**

> Gradient representation will improve verified correspondence under the tested illumination difference.

**Observed**

> The experiment produced `[TBD]` verified inliers.

**Interpretation**

> `[TBD]`

**Conclusion**

> `[TBD]`

This structure prevents an expected outcome from being mistaken for measured evidence.

---

# Research Quality Checklist

Before merging a research document, verify:

### Scientific clarity

- [ ] The research question is explicit.
- [ ] The motivation is documented.
- [ ] Hypotheses are identified.
- [ ] Assumptions are identified.
- [ ] Facts are separated from interpretations.
- [ ] Conclusions do not exceed the evidence.
- [ ] Limitations are documented.

### Traceability

- [ ] Relevant experiments are referenced.
- [ ] Relevant datasets are identified.
- [ ] Relevant methods are identified.
- [ ] Result artifacts are referenced where available.
- [ ] Benchmark implications are clearly identified.

### Reproducibility

- [ ] Experimental conditions are documented where relevant.
- [ ] Important parameters are recorded or linked.
- [ ] Dataset/image-pair identity is recorded.
- [ ] Evaluation metrics are identified.
- [ ] Unknown information is marked `[TBD]` rather than guessed.

### Scientific integrity

- [ ] No unsupported performance claims are made.
- [ ] No fabricated numerical results are included.
- [ ] No unverified "robustness" claims are presented as facts.
- [ ] Failures and limitations are not intentionally omitted.
- [ ] Literature claims are attributed.
- [ ] Results are not generalized beyond the evaluated conditions without justification.

### Repository quality

- [ ] Filename is descriptive.
- [ ] Links point to authoritative repository artifacts.
- [ ] Duplicate documentation is minimized.
- [ ] Temporary outputs are excluded.
- [ ] Markdown renders correctly.

---

# Adding New Research

When adding a new research investigation:

## Step 1 — Define the question

Start with a specific scientific question.

Avoid starting with:

> "I want to try algorithm X."

Prefer:

> "Does representation X improve correspondence under condition Y compared with baseline Z?"

The latter can be tested.

## Step 2 — Record the motivation

Explain why the question matters to ChandraMap.

## Step 3 — Review existing evidence

Check:

- existing research
- existing experiments
- benchmark requirements
- relevant datasets
- existing implementation
- known failure cases

Avoid duplicating an investigation that already exists without explaining why it is different.

## Step 4 — State the hypothesis

Clearly identify what is expected.

## Step 5 — Define the evaluation

Specify how the hypothesis could be supported or challenged.

## Step 6 — Create an experiment when appropriate

If the question requires controlled empirical evidence, create or reference an experiment under:

```text
experiments/
```

using the repository's experiment template and conventions.

## Step 7 — Record the evidence

Document measured results in the appropriate experiment/result artifacts.

## Step 8 — Update the research finding

Summarize what the evidence means, including uncertainty and limitations.

## Step 9 — Record follow-up work

Identify unresolved questions or next experiments.

---

# Research Review Checklist

Before considering a research investigation complete, ask:

```text
What was the question?
        ↓
Why did it matter?
        ↓
What was the hypothesis?
        ↓
What evidence already existed?
        ↓
What experiment tested it?
        ↓
What was measured?
        ↓
What was observed?
        ↓
What can actually be concluded?
        ↓
What remains uncertain?
        ↓
What should happen next?
```

If these questions cannot be answered, the research artifact may still be exploratory and should be labeled accordingly.

---

# V1 Research Discipline

V1 should favor controlled, traceable research over uncontrolled experimentation.

In particular:

- establish a known pair before broadening the evaluation
- maintain a clear baseline
- change one primary variable at a time where practical
- preserve source and reference metadata
- distinguish scale handling from representation changes
- distinguish candidate matches from verified inliers
- evaluate geometric registration using independent evidence where available
- consider spatial coverage, not only match count
- report pixel-space error before converting to physical units when appropriate
- preserve failures
- record runtime where relevant
- avoid unsupported confidence scores
- avoid fabricated or estimated benchmark results
- document limitations

The purpose is not to make V1 appear successful. The purpose is to establish a reproducible scientific baseline from which later methods can be compared.

---

# Example Research Entry

The following illustrates the intended level of structure without asserting that the example represents a measured project result.

```markdown
# Gradient Representation Under Illumination Variation

## Research Question

Does a gradient-based image representation improve verified
correspondence between lunar images acquired under different
illumination conditions?

## Motivation

Lunar illumination changes can alter both intensity and shadow
geometry. A representation based on local image structure may
provide information that is less dependent on absolute brightness.

## Hypothesis

A gradient representation may improve correspondence under the
tested illumination difference compared with raw grayscale.

## Existing Evidence

- [Relevant literature]
- [Project observation]
- EXP-001 — SIFT Baseline

## Proposed Experiment

EXP-003 — Gradient Representation

## Controlled Variables

- Image pair
- Scale handling
- SIFT configuration
- Matcher
- Geometric verification
- Evaluation points

## Independent Variable

Image representation.

## Metrics

- Candidate match count
- Verified inlier count
- Inlier ratio
- Spatial coverage
- Independent checkpoint RMSE
- Runtime

## Observed Result

[TBD]

## Interpretation

[TBD]

## Limitations

- [TBD]
- [TBD]

## Follow-up

[TBD]
```

---

# Relationship to Current Experiments

Research questions should link to the relevant experiment documentation rather than reproducing complete experiment specifications.

Known V1 experiment areas include:

- [V1 experiments](../experiments/v1/README.md)
- [Experiment template](../experiments/templates/EXPERIMENT_TEMPLATE.md)
- [EXP-001 — SIFT Baseline](../experiments/v1/baseline/EXP-001-sift-baseline/README.md)
- [EXP-002 — Scale Pyramid](../experiments/v1/preprocessing/EXP-002-scale-pyramid/README.md)
- [EXP-003 — Gradient Representation](../experiments/v1/preprocessing/EXP-003-gradient-representation/README.md)
- [EXP-004 — Affine vs Homography](../experiments/v1/geometry/EXP-004-affine-vs-homography/README.md)
- [EXP-005 — Residual Analysis](../experiments/v1/geometry/EXP-005-residual-analysis/README.md)
- [EXP-006 — Sub-Pixel Refinement](../experiments/v1/refinement/EXP-006-subpixel-refinement/README.md)

These links identify the corresponding repository areas. Their implementation and result status should be determined from the current repository state rather than inferred from their existence.

---

# Research and Engineering Decisions

Research findings may eventually influence engineering decisions.

The transition should be explicit:

```text
Research Evidence
        ↓
Scientific Finding
        ↓
Engineering Decision
        ↓
Implementation
        ↓
Validation
        ↓
Documentation
```

A research observation should not automatically become a production design requirement.

Before a research conclusion becomes an engineering rule, verify:

- evidence quality
- reproducibility
- scope
- dataset diversity
- benchmark relevance
- known limitations
- implementation cost
- regression risk

The resulting engineering decision should then be documented in the appropriate project documentation.

---

# Research and Scientific Credibility

Scientific credibility depends not only on obtaining good results, but on documenting the conditions under which those results were obtained.

A credible ChandraMap research record should make it possible to distinguish:

```text
What was proposed
        ↓
What was implemented
        ↓
What was tested
        ↓
What was measured
        ↓
What failed
        ↓
What was observed
        ↓
What was concluded
        ↓
What remains unknown
```

This is particularly important for lunar image registration because performance can depend strongly on:

- sensor pair
- spatial resolution
- scale
- illumination
- overlap
- terrain type
- shadow geometry
- representation
- geometric model
- ground-truth quality
- spatial distribution of correspondences

A result obtained on one image pair should therefore not automatically be described as a general property of ChandraMap.

---

# Research Status

The status of individual research documents should be explicit.

Recommended values include:

```text
Status: Draft
Status: Literature Review
Status: Hypothesis
Status: Investigation
Status: Experiment Pending
Status: Experiment Complete
Status: Analysis Pending
Status: Finding
Status: Superseded
```

Use the repository's established conventions if they differ.

---

# Final Principles

The `research/` directory should follow these principles:

1. **Ask precise scientific questions.**
2. **Separate hypotheses from established facts.**
3. **Connect empirical claims to measurable experiments.**
4. **Preserve the distinction between observation and interpretation.**
5. **Do not fabricate results or metadata.**
6. **Document uncertainty explicitly.**
7. **Preserve failure cases and limitations.**
8. **Reference authoritative datasets and experiment artifacts.**
9. **Keep benchmark methodology in `benchmarks/`.**
10. **Keep executable implementation in `src/`.**
11. **Keep controlled experiments in `experiments/`.**
12. **Keep general project documentation in `docs/`.**
13. **Prefer measured evidence over subjective confidence.**
14. **Consider spatial coverage as well as correspondence count.**
15. **Use independent evaluation points where available.**
16. **Do not treat normalization as a universal solution to illumination geometry.**
17. **Do not treat gradient or edge representations as automatically illumination invariant.**
18. **Do not generalize beyond the evaluated data and conditions without evidence.**
19. **Make important research claims traceable to code, data, experiments, and results.**
20. **Use research to turn scientific uncertainty into reproducible investigation.**

---

## Research Directory Contract

In short:

```text
research/
    asks and investigates

experiments/
    tests

src/
    implements

data/
    provides scientific inputs and ground truth

benchmarks/
    standardizes evaluation

results/
    records measured outputs

docs/
    explains the project
```

The research layer is successful when a scientific idea can be followed from **question → hypothesis → experiment → measurement → analysis → finding → next decision** without losing the connection between the scientific reasoning and the evidence that supports it.
