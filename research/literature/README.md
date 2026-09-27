# Literature Research

> Scientific literature is part of the evidence base for ChandraMap.
> This directory records the scientific and technical work used to understand the problem, select methods, design experiments, interpret results, and identify limitations.

---

## 1. Purpose

`research/literature/` is the dedicated research area for scientific and technical literature relevant to ChandraMap.

It exists to maintain a traceable connection between:

```text
Scientific Literature
        ↓
Existing Knowledge
        ↓
Research Questions
        ↓
Hypotheses
        ↓
Experiments
        ↓
Measured Results
        ↓
Analysis / Findings
        ↓
Future Research
```

The directory is **not simply a collection of papers**.

Its purpose is to make it possible to answer:

- Why was a particular method considered?
- What problem does an existing method address?
- What assumptions does the method make?
- What evidence exists in the literature?
- What limitations have already been identified?
- Which ChandraMap research question does the literature inform?
- Which experiment tests the relevant assumption?
- What did ChandraMap actually measure?
- Where does ChandraMap agree or disagree with prior work?

Literature provides scientific context and evidence. It does **not** automatically validate a ChandraMap implementation or result.

A method described as effective in another domain must still be evaluated under the lunar imaging conditions relevant to ChandraMap.

---

# 2. ChandraMap Research Context

ChandraMap addresses lunar image correspondence and registration between images of the same lunar region acquired under different imaging conditions and potentially by different instruments.

Relevant Chandrayaan-2 sources include:

- OHRC
- TMC-2
- IIRS

The project also considers lunar reference data such as LRO products where appropriate. The supplied project material identifies OHRC, TMC-2, and IIRS as materially different data sources rather than interchangeable images.

The research problem involves factors including:

- scale differences
- spatial-resolution differences
- illumination differences
- Sun-angle changes
- shadow changes
- contrast differences
- sensor differences
- modality differences
- representation differences
- geometric distortion
- correspondence uncertainty
- registration error
- sub-pixel localization

These factors motivate literature research across computer vision, remote sensing, planetary science, photogrammetry, image registration, feature matching, and scientific evaluation.

---

# 3. Why Literature Matters

A serious registration system should not select algorithms solely because they are popular or modern.

Literature research helps determine:

### Scientific Background

What is already known about:

- lunar imagery
- planetary image formation
- image registration
- feature correspondence
- geometric estimation
- multimodal matching
- photometric variation
- terrain effects

### Method Selection

What established methods exist for:

- local feature extraction
- descriptor matching
- geometric verification
- projective registration
- multimodal registration
- structural representations
- sub-pixel refinement

### Experimental Design

What variables should be controlled and measured.

### Benchmark Design

What constitutes meaningful evaluation.

### Failure Analysis

What failure modes have been observed in related problems.

### Research Gaps

Where existing methods do not adequately address ChandraMap's specific conditions.

---

# 4. Literature Is Evidence, Not Ground Truth

A paper can provide:

- an algorithm
- an experimental result
- a theoretical argument
- a benchmark
- a dataset
- a limitation
- a research direction

It does **not** automatically prove that the same method will work on ChandraMap imagery.

For example:

```text
Published Method
      ↓
Reported Performance
      ↓
Different Dataset / Sensor / Domain
      ↓
ChandraMap
      ↓
Independent Experiment
```

A method demonstrated on terrestrial imagery may behave differently on lunar imagery.

Similarly, a method designed for multimodal remote sensing may require adaptation before it can be applied to Chandrayaan-2 data.

The supplied project feedback explicitly cautions that pretrained terrestrial matching methods should not be assumed to be lunar-invariant and recommends measuring their behaviour on the same lunar image pairs used by the baseline.

---

# 5. What Belongs in `research/literature/`

Literature relevant to ChandraMap may include:

## 5.1 Lunar and Planetary Science

Examples:

- lunar imaging
- lunar surface characteristics
- planetary remote sensing
- lunar photometry
- lunar terrain representation
- planetary image processing
- lunar coordinate systems
- planetary cartography

---

## 5.2 Chandrayaan-2 Instrumentation

Literature and authoritative technical documentation concerning:

- OHRC
- TMC-2
- IIRS
- Chandrayaan-2 products
- instrument geometry
- spatial resolution
- spectral characteristics
- calibration
- map projection
- product metadata

The project documentation emphasizes that these sensors have substantially different characteristics and therefore may require separate processing paths.

---

## 5.3 Lunar Reference Data

Relevant literature and technical documentation concerning:

- LRO NAC
- LRO WAC
- other planetary reference imagery
- planetary image archives
- lunar DEMs
- lunar coordinate/reference systems

The SIH project material identifies LRO NAC as a possible reference/training source and LRO WAC as additional lunar-scale/illumination data.

---

## 5.4 Image Registration

Literature concerning:

- image alignment
- image-to-image registration
- template matching
- intensity-based registration
- feature-based registration
- multimodal registration
- coarse-to-fine registration
- local registration
- global registration

---

## 5.5 Feature Detection and Description

Relevant methods may include:

- SIFT
- RootSIFT
- ORB
- learned local features
- keypoint detection
- local descriptors

SIFT is currently important to ChandraMap as a classical, interpretable baseline rather than as an assumed final solution. The project feedback recommends starting with SIFT before evaluating stronger learned approaches.

---

## 5.6 Feature Matching

Literature concerning:

- descriptor matching
- nearest-neighbour matching
- ratio tests
- cross-checking
- sparse matching
- learned matching
- detector-free matching

Potential research directions identified by the project include:

- ALIKED
- LightGlue
- LoFTR
- RIFT
- CFOG

These should be treated as candidate research directions until experimentally evaluated on ChandraMap data.

---

## 5.7 Geometric Verification

Relevant literature includes:

- RANSAC
- robust estimation
- affine transformation
- homography
- projective geometry
- geometric outlier rejection
- degeneracy
- residual analysis

The V1 pipeline places RANSAC-based geometric verification between candidate correspondence generation and final transformation estimation.

---

## 5.8 Illumination and Photometric Variation

Literature concerning:

- illumination-invariant representations
- gradients
- edges
- phase-based representations
- shadow handling
- photometric normalization
- Sun-angle effects

This is particularly important for lunar imagery because changing Sun angle can alter shadow geometry, not merely image brightness.

The supplied project feedback explicitly recommends testing structural representations such as gradients and edges under Sun-angle stress rather than assuming brightness normalization is sufficient.

---

## 5.9 Cross-Sensor and Multimodal Registration

Relevant literature includes:

- multimodal remote sensing
- optical/infrared registration
- structural similarity
- modality-invariant descriptors
- cross-sensor feature matching
- hyperspectral-to-image registration

This area is particularly relevant to IIRS because IIRS data should not automatically be treated as an ordinary single-band camera image. The project guidance recommends first deriving a registration-friendly 2D representation.

---

## 5.10 Scale and Multi-Resolution Registration

Relevant literature includes:

- image pyramids
- multi-scale matching
- coarse-to-fine registration
- resolution normalization
- scale-space
- cross-resolution matching

The project treats scale as a physical-information problem rather than simply a pixel-count problem.

> Upsampling changes pixel count; it does not recover spatial information that was absent from the source sensor.

The project feedback recommends comparing imagery at meaningful effective ground scales before fine registration.

---

## 5.11 Sub-Pixel Registration

Relevant literature includes:

- sub-pixel localization
- tie-point refinement
- phase correlation
- patch-based correlation
- local optimization
- planetary registration
- control-point refinement

The ChandraMap geometry workflow places sub-pixel refinement after reliable geometric inliers have been established:

```text
Candidate Matches
        ↓
RANSAC
        ↓
Verified Inliers
        ↓
Sub-Pixel Tie Points
        ↓
Final Transformation
```

This ordering is part of the project methodology and should be supported by appropriate literature.

---

## 5.12 Evaluation Methodology

Literature concerning:

- registration error
- reprojection error
- RMSE
- independent check points
- control points
- spatial coverage
- failure rate
- robustness testing
- benchmark methodology

is particularly important.

The project guidance emphasizes that transformation fitting and evaluation should use separate points whenever possible.

---

# 6. What Does Not Belong Here?

Not every research-related file belongs in `research/literature/`.

| Content                              | Correct Location                               |
| ------------------------------------ | ---------------------------------------------- |
| Published paper metadata             | `research/literature/`                         |
| Literature review                    | `research/literature/`                         |
| Paper summary                        | `research/literature/`                         |
| Method comparison from papers        | `research/literature/`                         |
| Research gap identified from papers  | `research/literature/`                         |
| Personal research note               | `research/notes/` or appropriate research area |
| Experiment definition                | `experiments/`                                 |
| Experiment results                   | `experiments/` / `results/`                    |
| Benchmark specification              | `benchmarks/`                                  |
| Ground-truth definition              | `data/ground_truth/`                           |
| Implementation                       | `src/`                                         |
| Tests                                | `tests/`                                       |
| General user/developer documentation | `docs/`                                        |
| Project overview                     | root `README.md`                               |

The exact repository structure should remain consistent with the current ChandraMap repository.

---

# 7. Literature vs Research Notes

These concepts should remain separate.

## Literature

Answers:

> **What does existing scientific or technical work say?**

Example:

```text
Paper:
RIFT

Problem:
Multimodal remote-sensing image matching

Method:
[TBD]

Reported dataset:
[TBD]

Reported result:
[TBD]

Known limitation:
[TBD]
```

## Research Note

Answers:

> **What does the ChandraMap research team currently think, observe, or need to investigate?**

Example:

```text
Observation:
SIFT correspondence quality decreases under strong
illumination differences.

Evidence:
EXP-001 / EXP-003

Next question:
Does a gradient representation improve spatially
distributed verified inliers?
```

The research note may be informed by literature, but it is not itself literature.

---

# 8. Literature vs Experiments

Literature explains what is already known.

Experiments establish what happens under ChandraMap's conditions.

```text
Literature
    ↓
Method / Hypothesis
    ↓
ChandraMap Experiment
    ↓
Measured Result
```

For example:

```text
Literature:
SIFT is a scale- and rotation-aware local feature method.

        ↓

ChandraMap Question:
Can SIFT establish a reproducible lunar registration baseline?

        ↓

Experiment:
EXP-001 — SIFT Baseline

        ↓

Measurement:
Candidate matches
Inliers
Coverage
Check-point RMSE
Runtime
```

The literature statement and experimental result must not be presented as the same evidence.

---

# 9. Literature vs Implementation

A paper can describe an algorithm without ChandraMap implementing it.

Therefore use explicit status labels.

Recommended statuses:

- `Reviewed`
- `Relevant`
- `Candidate`
- `Planned`
- `Implemented`
- `Benchmarked`
- `Rejected`
- `Not Applicable`
- `Superseded`
- `[TBD]`

For example:

| Method    | Literature Status | ChandraMap Status |
| --------- | ----------------- | ----------------- |
| SIFT      | Reviewed          | Baseline          |
| ALIKED    | Reviewed          | `[TBD]`           |
| LightGlue | Reviewed          | `[TBD]`           |
| LoFTR     | Reviewed          | `[TBD]`           |
| RIFT      | Reviewed          | `[TBD]`           |
| CFOG      | Reviewed          | `[TBD]`           |

Do not write `Implemented` merely because a paper has been reviewed.

---

# 10. Literature-to-Research Traceability

Every important literature item should be connected to the part of ChandraMap that it informs.

Recommended relationship:

```text
Literature ID
      ↓
Research Topic
      ↓
Research Question
      ↓
Hypothesis
      ↓
Experiment ID
      ↓
Result
      ↓
Finding
```

Example:

```text
LIT-001
    ↓
Local feature matching
    ↓
Can classical local features establish a lunar baseline?
    ↓
EXP-001
    ↓
SIFT baseline measurements
    ↓
Finding: [TBD]
```

This prevents the literature directory from becoming disconnected from the actual research.

---

# 11. Recommended Literature Record

Each significant reference should have enough information to identify and retrieve it.

Recommended metadata:

| Field                     | Value   |
| ------------------------- | ------- |
| Literature ID             | `[TBD]` |
| Title                     | `[TBD]` |
| Authors                   | `[TBD]` |
| Year                      | `[TBD]` |
| Venue                     | `[TBD]` |
| DOI / Identifier          | `[TBD]` |
| URL                       | `[TBD]` |
| Type                      | `[TBD]` |
| Topic                     | `[TBD]` |
| ChandraMap relevance      | `[TBD]` |
| Related research question | `[TBD]` |
| Related experiment        | `[TBD]` |
| Method                    | `[TBD]` |
| Dataset                   | `[TBD]` |
| Main result               | `[TBD]` |
| Limitations               | `[TBD]` |
| ChandraMap status         | `[TBD]` |

Only populate fields supported by the source.

---

# 12. Recommended Literature Entry

A literature entry should answer more than:

> "This paper is about image registration."

Use a structured format.

```markdown
# LIT-[ID] — [Short Title]

## Bibliographic Information

| Field   | Value |
| ------- | ----- |
| Title   | [TBD] |
| Authors | [TBD] |
| Year    | [TBD] |
| Venue   | [TBD] |
| DOI     | [TBD] |
| URL     | [TBD] |

## Research Area

[TBD]

## Problem Addressed

[TBD]

## Method

[TBD]

## Dataset / Evaluation

[TBD]

## Main Findings

[TBD]

## Limitations

[TBD]

## Relevance to ChandraMap

[TBD]

## Related Research Questions

- [RQ-XXX]

## Related Experiments

- [EXP-XXX]

## ChandraMap Status

[TBD]
```

---

# 13. Bibliographic Accuracy

Bibliographic information must be recorded accurately.

Do not invent:

- authors
- publication year
- journal
- conference
- DOI
- page numbers
- dataset names
- performance values

If information has not been verified:

```text
[TBD]
```

or:

```text
[Not verified]
```

should be used.

A missing DOI is preferable to an incorrect DOI.

---

# 14. Source Hierarchy

When documenting literature, distinguish between different kinds of sources.

### Tier A — Primary Scientific Literature

Examples:

- peer-reviewed papers
- conference papers
- journal articles
- authoritative technical reports

### Tier B — Authoritative Technical Documentation

Examples:

- ISRO mission documentation
- NASA/PDS documentation
- USGS documentation
- instrument documentation
- official dataset documentation

### Tier C — Official Software Documentation

Examples:

- OpenCV documentation
- official project repositories
- official algorithm documentation

### Tier D — Secondary Sources

Examples:

- review articles
- surveys
- technical summaries

### Tier E — Informal Sources

Examples:

- blog posts
- forum discussions
- unofficial tutorials

Informal sources can help discovery but should not silently replace primary or authoritative sources when stronger sources are available.

---

# 15. Mission Documentation vs Research Papers

ChandraMap requires both scientific literature and authoritative mission/product documentation.

These sources serve different purposes.

### Scientific Paper

Useful for:

- algorithm methodology
- theoretical basis
- experimental evidence
- comparison with prior work

### Mission Documentation

Useful for:

- sensor characteristics
- product definitions
- calibration information
- metadata
- spatial scale
- spectral characteristics
- acquisition information

For example, the project feedback recommends mission/data documentation first when establishing instrument and product facts, followed by algorithm papers and implementation repositories.

---

# 16. Literature Categories

A recommended conceptual taxonomy is:

```text
research/literature/
├── planetary-science/
├── lunar-imaging/
├── chandrayaan-2/
├── remote-sensing/
├── image-registration/
├── feature-matching/
├── multimodal-registration/
├── geometric-verification/
├── scale-space/
├── illumination/
├── subpixel-registration/
├── evaluation/
└── surveys/
```

The exact physical folder structure should only be introduced if it matches the repository's actual organization.

If the repository currently uses a flatter structure, do not create unnecessary hierarchy merely for appearance.

---

# 17. Suggested Topic Classification

Each reference can additionally be assigned one or more research topics.

| Topic                  | Examples                            |
| ---------------------- | ----------------------------------- |
| `planetary-science`    | Lunar terrain, planetary imaging    |
| `instrumentation`      | OHRC, TMC-2, IIRS                   |
| `registration`         | Image alignment                     |
| `features`             | SIFT, learned features              |
| `matching`             | Descriptor or learned matching      |
| `geometry`             | Affine, homography, RANSAC          |
| `multimodal`           | Cross-sensor matching               |
| `illumination`         | Sun-angle and shadow effects        |
| `scale`                | Multi-resolution registration       |
| `subpixel`             | Tie-point refinement                |
| `evaluation`           | RMSE, checkpoints, benchmark design |
| `planetary-processing` | ISIS and related workflows          |

---

# 18. Research Questions Supported by Literature

Literature should connect to explicit research questions.

Potential ChandraMap questions include:

### RQ-001 — Correspondence

Can reliable local correspondences be obtained between lunar images acquired under different imaging conditions?

### RQ-002 — Scale

How should large spatial-resolution differences be handled without claiming information that the source sensor does not contain?

### RQ-003 — Illumination

Which representations remain useful when Sun-angle changes modify lunar shadows and local appearance?

### RQ-004 — Cross-Sensor Matching

How should correspondence be performed across sensors with different modalities and spatial scales?

### RQ-005 — Geometry

When is an affine transformation sufficient, and when does a homography provide measurable benefit?

### RQ-006 — Residuals

What do spatial residual patterns reveal about transformation inadequacy, terrain relief, projection error, or correspondence quality?

### RQ-007 — Sub-Pixel Accuracy

How accurately can verified tie points be localized in source-image pixels?

### RQ-008 — Evaluation

Which metrics best distinguish visually plausible registration from independently validated registration?

These identifiers are a documentation framework. If the repository defines different research-question IDs, those repository identifiers should take precedence.

---

# 19. Literature and Experiment Mapping

The literature directory should make experiment motivation traceable.

| Research Area              | Literature Role                    | ChandraMap Experiment |
| -------------------------- | ---------------------------------- | --------------------- |
| Local features             | Classical correspondence baseline  | EXP-001               |
| Scale handling             | Multi-resolution matching          | EXP-002               |
| Structural representation  | Illumination/appearance robustness | EXP-003               |
| Geometric models           | Affine vs projective registration  | EXP-004               |
| Residual behaviour         | Spatial error analysis             | EXP-005               |
| Sub-pixel localization     | Tie-point refinement               | EXP-006               |
| Stronger matchers          | Learned/local matching comparison  | `[TBD]`               |
| Sensor-specific processing | Cross-sensor registration          | `[TBD]`               |

The mapping should only be populated when the literature actually informs the experiment.

---

# 20. Example Traceability Record

```markdown
## Literature → Experiment Traceability

### Reference

LIT-[TBD]

### Scientific Topic

[TBD]

### Relevant Finding

[TBD]

### ChandraMap Research Question

RQ-[TBD]

### ChandraMap Hypothesis

[TBD]

### Experiment

EXP-[TBD]

### Measurement

[TBD]

### Result

[TBD]

### Interpretation

[TBD]

### Remaining Uncertainty

[TBD]
```

This structure separates:

```text
What the paper says
```

from:

```text
What ChandraMap measured
```

---

# 21. Method Selection

Literature should inform method selection, not dictate it.

The recommended process is:

```text
Literature Review
      ↓
Candidate Methods
      ↓
Assumptions
      ↓
ChandraMap Compatibility
      ↓
Controlled Experiment
      ↓
Measured Evidence
      ↓
Method Decision
```

For example:

```text
SIFT
  ↓
Known classical local-feature method
  ↓
Simple / interpretable baseline
  ↓
Test on lunar imagery
  ↓
EXP-001
  ↓
Measured performance
```

The project feedback explicitly recommends choosing matching methods through controlled experiments rather than by method name or popularity.

---

# 22. Do Not Confuse Algorithm Categories

Literature notes should distinguish between different roles.

For example:

```text
Feature Detector
        ↓
Feature / Descriptor
        ↓
Matcher
        ↓
Geometric Verification
        ↓
Transformation
        ↓
Refinement
        ↓
Evaluation
```

A literature reference should identify which component it addresses.

For example:

| Method     | Role                               |
| ---------- | ---------------------------------- |
| SIFT       | Keypoint + descriptor              |
| ALIKED     | Learned keypoint/descriptor        |
| LightGlue  | Sparse feature matcher             |
| LoFTR      | Detector-free correspondence       |
| RIFT       | Multimodal matching approach       |
| CFOG       | Structural remote-sensing matching |
| RANSAC     | Robust geometric estimation        |
| Homography | Projective transformation          |

The project feedback specifically warns against mixing feature extractors and matchers into one category.

---

# 23. Literature and Sensor Physics

Literature records should preserve physical differences between sensors.

Do not summarize all lunar imagery as:

> "Lunar images."

Instead record relevant properties such as:

- sensor type
- spatial scale
- spectral modality
- acquisition geometry
- viewing geometry
- illumination
- projection
- product level
- metadata availability

The supplied project material emphasizes that OHRC, TMC-2, and IIRS should not be treated as identical image sources.

---

# 24. Illumination Literature

Illumination-related literature should distinguish:

```text
Brightness Difference
```

from:

```text
Illumination Geometry Difference
```

On the Moon, a different Sun angle can change:

- shadow position
- shadow extent
- crater appearance
- local edge structure
- apparent intensity patterns

Therefore a literature review that discusses illumination normalization should record whether a method addresses:

- photometric variation
- shadow variation
- geometric illumination changes
- structural representation
- actual Sun-angle differences

The project feedback explicitly recommends a dedicated Sun-angle stress test rather than treating normalization as a generic promise of illumination invariance.

---

# 25. Scale Literature

Scale-related literature should distinguish:

### Pixel Scaling

Changing image dimensions.

### Physical Scale

Changing or comparing effective ground sampling distance.

These are not equivalent.

A literature record involving multi-resolution registration should document whether the method:

- resizes images
- constructs pyramids
- performs scale-space matching
- uses physical metadata
- performs coarse-to-fine matching
- accounts for different GSDs

ChandraMap should not claim recovered spatial information simply because a lower-resolution image has been upsampled.

---

# 26. Geometry Literature

Geometry literature should record assumptions explicitly.

For affine or homography papers, document:

- transformation model
- degrees of freedom
- minimum correspondences
- planar assumptions
- projective assumptions
- robustness method
- residual definition
- evaluation method
- extrapolation behaviour

A homography should not be documented as a generic model of arbitrary 3D terrain.

The project guidance describes affine/homography as reasonable first models for local, already map-projected image pairs while explicitly warning that lunar terrain is not flat.

---

# 27. Sub-Pixel Literature

Sub-pixel literature should be evaluated carefully because the unit of the requested accuracy matters.

ChandraMap should report:

> **source-image pixel error first**

and convert to metres only where:

- GSD is known
- projection is understood
- the coordinate relationship is meaningful
- suitable reference truth exists

The project documentation explicitly warns that equal pixel errors from sensors with very different GSDs do not represent equal ground errors.

---

# 28. Evaluation Literature

Literature concerning evaluation should be connected to the benchmark design.

Important concepts include:

- control points
- check points
- independent validation
- RMSE
- median error
- maximum error
- percentile error
- inlier ratio
- spatial coverage
- runtime
- failure rate

The project evaluation guidance recommends:

```text
Local Matching
    → Inlier Count + Inlier Ratio

Distribution
    → Grid / Convex-Hull Coverage

Registration
    → Independent Check-Point RMSE

Geospatial Accuracy
    → Ground Error when meaningful

System
    → Runtime + Failure Rate
```

---

# 29. Independent Evaluation

Literature records involving registration accuracy should explicitly state whether reported error is:

- training/fitting error
- inlier residual
- validation error
- independent check-point error
- ground-truth error

These are not interchangeable.

A transformation fitted using a set of points should not be evaluated as though the same points were independent ground truth.

This distinction is central to ChandraMap's evaluation methodology.

---

# 30. Literature Review Quality Standard

A useful literature review should answer at least:

1. What problem does the source address?
2. What method does it propose or evaluate?
3. What assumptions does it make?
4. What data does it use?
5. What metrics does it report?
6. What are the important results?
7. What limitations are acknowledged?
8. How is the source relevant to ChandraMap?
9. Which ChandraMap research question does it inform?
10. Does ChandraMap actually test the relevant claim?

If several of these cannot be answered, the reference may still be useful, but it should be marked as incomplete.

---

# 31. Avoiding Literature Overclaiming

Do not write:

> "Paper X proves this method works for lunar registration."

unless the paper actually evaluates the method on the relevant lunar problem and supports that conclusion.

Prefer:

> "Paper X reports improved performance on [dataset/condition]. ChandraMap will evaluate whether the method provides similar benefits under its lunar image-pair conditions."

This distinction is essential for scientific credibility.

---

# 32. Literature Claims vs ChandraMap Findings

Use explicit language.

### Literature Claim

> "The authors report..."

### Literature Limitation

> "The authors identify..."

### ChandraMap Observation

> "EXP-XXX measured..."

### ChandraMap Finding

> "Under the tested conditions, the experiment found..."

### Open Question

> "It remains unknown whether..."

This vocabulary prevents accidental conversion of published claims into project results.

---

# 33. Literature Review Table

A project-level literature matrix can use:

| ID          | Reference | Topic   | Method  | Dataset | Main Finding | Limitation | ChandraMap Relevance | Experiment |
| ----------- | --------- | ------- | ------- | ------- | ------------ | ---------- | -------------------- | ---------- |
| `LIT-[TBD]` | `[TBD]`   | `[TBD]` | `[TBD]` | `[TBD]` | `[TBD]`      | `[TBD]`    | `[TBD]`              | `[TBD]`    |

Do not populate values without verifying them.

---

# 34. Recommended Literature Status

Each reference should have a lifecycle status.

```text
Discovered
   ↓
Bibliographic information verified
   ↓
Relevant
   ↓
Reviewed
   ↓
Mapped to research question
   ↓
Mapped to experiment
   ↓
Used as evidence
```

Possible terminal states:

```text
Not Relevant
Superseded
Rejected
Archived
```

---

# 35. Adding New Literature

When adding a new reference:

### Step 1 — Verify the Source

Confirm:

- title
- authors
- year
- venue
- identifier
- official URL or DOI

### Step 2 — Identify the Research Area

Examples:

```text
Registration
Feature Matching
Illumination
Scale
Geometry
Multimodal
Sub-Pixel
Evaluation
Planetary Science
```

### Step 3 — Summarize the Method

Record what the authors actually did.

### Step 4 — Record the Evidence

Document:

- dataset
- experimental setup
- metrics
- reported results

### Step 5 — Record Limitations

Do not omit limitations merely because they make the method less attractive.

### Step 6 — Connect to ChandraMap

Identify:

- research question
- experiment
- potential implementation
- benchmark relevance

### Step 7 — Record Status

Use:

```text
Reviewed
Candidate
Planned
Implemented
Benchmarked
Rejected
```

as appropriate.

---

# 36. Literature Contribution Checklist

Before opening a pull request containing a new reference:

- [ ] Bibliographic information verified
- [ ] Source is identifiable
- [ ] Topic is recorded
- [ ] Problem is summarized
- [ ] Method is summarized accurately
- [ ] Dataset/evaluation is recorded
- [ ] Main findings are attributed to the source
- [ ] Limitations are recorded
- [ ] ChandraMap relevance is explained
- [ ] Related research question is identified
- [ ] Related experiment is identified where applicable
- [ ] Implementation status is not overstated
- [ ] No unsupported performance claim is introduced
- [ ] DOI/URL is verified where available

---

# 37. Reproducibility Requirements

Literature documentation should make it possible for another contributor to locate the same source.

Prefer stable identifiers:

- DOI
- arXiv identifier
- journal identifier
- conference identifier
- official mission-document identifier
- official repository URL

Avoid relying only on:

- screenshots
- copied text
- local filenames
- temporary download URLs
- search-engine snippets

---

# 38. Versioning Literature Evidence

Scientific literature itself can be stable while associated implementation or datasets change.

Where relevant, record:

- paper version
- preprint version
- publication version
- repository commit
- dataset version
- documentation revision

For software-based methods, distinguish:

```text
Paper
```

from:

```text
Official Implementation
```

and from:

```text
ChandraMap Implementation
```

These are separate artifacts.

---

# 39. Reproducibility of Reported Results

When a paper reports a performance value, preserve the context.

Do not record:

```text
Accuracy: 95%
```

without documenting:

- dataset
- task
- metric
- evaluation protocol
- population/test split
- conditions

Prefer:

```text
Reported metric:
[TBD]

Dataset:
[TBD]

Evaluation protocol:
[TBD]

Reported result:
[TBD]

Important qualification:
[TBD]
```

A number without its evaluation context is not reproducible evidence.

---

# 40. Literature and Benchmark Design

Literature should help explain why a benchmark contains particular conditions.

For ChandraMap, relevant stress conditions include:

- easy overlap
- Sun-angle stress
- scale stress
- modality stress
- geometry stress
- low-feature or repetitive terrain

These conditions were identified in the project evaluation guidance.

The literature directory should record the sources motivating these conditions where such sources exist.

---

# 41. Literature and Failure Analysis

Literature should not only be used to justify successful methods.

It should also inform expected failure modes.

For example:

```text
Literature
    ↓
Known limitation
    ↓
Benchmark stress condition
    ↓
Experiment
    ↓
Observed failure
```

A failure that matches a documented limitation can provide useful evidence.

A failure that contradicts published expectations may also be scientifically useful, provided the datasets and evaluation protocols are genuinely comparable.

---

# 42. Literature and Negative Results

Negative results are valuable.

If a method is investigated and does not improve ChandraMap performance, record:

- why it was tested
- which literature motivated it
- what implementation was used
- what data were used
- what metric was evaluated
- what happened
- possible reasons
- whether the result is conclusive

Do not remove a method from the literature record simply because its ChandraMap experiment was unsuccessful.

---

# 43. Literature and Advanced Methods

ChandraMap may investigate modern matching approaches such as:

- ALIKED + LightGlue
- LoFTR
- RIFT
- CFOG

The project guidance recommends benchmarking advanced methods against the same image pairs and retaining methods based on measured improvement in difficult cases rather than simply using every available algorithm.

The literature directory should therefore document:

```text
Why the method exists
        ↓
What problem it targets
        ↓
What assumptions it makes
        ↓
What evidence supports it
        ↓
What ChandraMap experiment tests it
```

---

# 44. Literature and FAISS

FAISS should be documented according to its actual role.

FAISS is a similarity-search/indexing library, not a local correspondence algorithm.

The project feedback distinguishes:

```text
Global Retrieval
    ↓
FAISS / Global Descriptor
    ↓
Top-K Candidate Regions
    ↓
Local Matching
    ↓
SIFT / ALIKED / LoFTR / etc.
```

The literature directory should preserve this distinction when documenting retrieval-related work.

---

# 45. Literature and IIRS

IIRS-specific literature should be handled separately from ordinary visible-image literature.

Relevant questions include:

- What type of data product is available?
- Is it a full hyperspectral cube?
- Are individual bands available?
- Is a browse product available?
- Which representation preserves useful terrain structure?
- How does spatial resolution affect registration?
- Which cross-modal methods are appropriate?

The project guidance recommends starting with a simple registration-friendly 2D representation before attempting a complex hyperspectral matching system.

---

# 46. Literature and Planetary Processing

Planetary image-processing literature and technical documentation can be relevant to:

- map projection
- image calibration
- geometric correction
- control networks
- coregistration
- sub-pixel alignment
- warping
- planetary coordinate systems

The supplied project feedback specifically identifies USGS ISIS coregistration/control-network documentation as relevant resources for planetary registration and refinement.

---

# 47. Literature Review Workflow

A practical workflow is:

```text
Discover
   ↓
Verify
   ↓
Classify
   ↓
Read
   ↓
Extract
   ↓
Critically Evaluate
   ↓
Map to Research Question
   ↓
Map to Experiment
   ↓
Record
   ↓
Update as Evidence Changes
```

---

# 48. Critical Reading

Do not treat an abstract or title as sufficient evidence.

When reviewing a paper, distinguish:

### Problem

What does the paper attempt to solve?

### Method

What does it actually implement?

### Dataset

What data were used?

### Evaluation

How was performance measured?

### Result

What did the authors report?

### Limitation

Under what conditions does the method struggle?

### Transferability

Which assumptions may or may not transfer to ChandraMap?

---

# 49. Transferability to Lunar Imagery

A literature method should be evaluated for domain transfer.

Ask:

| Question                                | Answer  |
| --------------------------------------- | ------- |
| Was the method tested on lunar imagery? | `[TBD]` |
| Was it tested on remote sensing?        | `[TBD]` |
| Was it tested cross-modally?            | `[TBD]` |
| Was scale variation present?            | `[TBD]` |
| Was illumination variation present?     | `[TBD]` |
| Was terrain relief relevant?            | `[TBD]` |
| Were independent checkpoints used?      | `[TBD]` |
| Is the implementation available?        | `[TBD]` |
| Are ChandraMap conditions comparable?   | `[TBD]` |

Do not infer transferability from method popularity.

---

# 50. Literature Comparison Matrix

For methods being considered for ChandraMap:

| Method    | Primary Role           | Domain  | Scale Robustness | Illumination / Modality Considerations | Geometry                  | Implementation | ChandraMap Status |
| --------- | ---------------------- | ------- | ---------------- | -------------------------------------- | ------------------------- | -------------- | ----------------- |
| SIFT      | Local feature          | `[TBD]` | `[TBD]`          | `[TBD]`                                | External RANSAC           | `[TBD]`        | Baseline          |
| ALIKED    | Learned local features | `[TBD]` | `[TBD]`          | `[TBD]`                                | External matcher/geometry | `[TBD]`        | `[TBD]`           |
| LightGlue | Local feature matching | `[TBD]` | `[TBD]`          | `[TBD]`                                | External geometry         | `[TBD]`        | `[TBD]`           |
| LoFTR     | Detector-free matching | `[TBD]` | `[TBD]`          | `[TBD]`                                | External geometry         | `[TBD]`        | `[TBD]`           |
| RIFT      | Multimodal matching    | `[TBD]` | `[TBD]`          | `[TBD]`                                | `[TBD]`                   | `[TBD]`        | `[TBD]`           |
| CFOG      | Structural matching    | `[TBD]` | `[TBD]`          | `[TBD]`                                | `[TBD]`                   | `[TBD]`        | `[TBD]`           |

This table is a research-tracking structure, not a performance ranking.

---

# 51. Do Not Rank Methods by Literature Popularity

The literature directory must not become a leaderboard.

Statements such as:

```text
Method A is the best.
```

should not be made solely from literature popularity or citation count.

Instead document:

```text
Method A:
- solves [problem]
- reports [evidence]
- assumes [conditions]
- has limitations [limitations]
- is relevant to ChandraMap because [reason]
- will be evaluated in [experiment]
```

The final ChandraMap method selection should be based on controlled measurements under the relevant conditions.

---

# 52. Literature Citation in Experiment Documentation

When an experiment uses an idea from literature, the experiment README should identify the relevant reference.

Recommended relationship:

```text
Experiment
    ↓
Method
    ↓
Literature Reference
```

For example:

```markdown
## Method Basis

The transformation model follows the standard projective
registration formulation described in:

- LIT-[TBD]
```

The exact citation format should follow the repository's established convention if one already exists.

---

# 53. Literature Citation in Source Code

Source code should not contain large copied sections from papers.

Instead:

```python
# Method based on:
# [LIT-ID / DOI / project reference]
```

when a citation is useful for implementation context.

The implementation itself should remain original and traceable to the repository's licensing and contribution requirements.

---

# 54. Copyright and Reproduction

Literature records should contain metadata and summaries rather than copied papers or large copyrighted passages.

Do not store:

- unauthorized copies of paywalled papers
- large copied sections of copyrighted text
- complete copyrighted PDFs unless repository licensing explicitly permits them

Prefer:

- citation metadata
- DOI
- official publication URL
- official repository URL
- short summaries
- notes
- extracted scientific claims with attribution

---

# 55. Data and Code Availability

When reviewing a paper, record whether:

- dataset is public
- code is public
- pretrained weights are public
- evaluation scripts are public
- implementation is reproducible

Example:

| Resource        | Availability |
| --------------- | ------------ |
| Paper           | `[TBD]`      |
| Dataset         | `[TBD]`      |
| Source code     | `[TBD]`      |
| Weights         | `[TBD]`      |
| Evaluation code | `[TBD]`      |

Do not assume that a paper is reproducible merely because the method is described.

---

# 56. Literature and Open-Source Implementations

A paper and its implementation may differ.

Document both when relevant:

```text
Scientific Paper
      ↓
Published Method

Official Implementation
      ↓
Software Behaviour

ChandraMap Implementation
      ↓
Project-Specific Behaviour
```

If an implementation deviates from the paper, document the difference.

---

# 57. Literature Maintenance

The literature directory should be maintained as the research evolves.

Review references when:

- a method is implemented
- an experiment is added
- a benchmark changes
- a new limitation is discovered
- a better source replaces an informal source
- a method is rejected
- a research question changes

Avoid allowing the directory to become a static archive with no connection to active research.

---

# 58. Deprecating Literature Entries

A literature entry should not normally be deleted because it is no longer central.

Instead mark it:

```text
Status: Superseded
```

or:

```text
Status: No longer relevant
```

and explain why.

This preserves research history.

---

# 59. Research Traceability Matrix

A project-level traceability table can be maintained as:

| Literature  | Research Question | Hypothesis | Experiment  | Metric  | Result  | Finding |
| ----------- | ----------------- | ---------- | ----------- | ------- | ------- | ------- |
| `LIT-[TBD]` | `RQ-[TBD]`        | `[TBD]`    | `EXP-[TBD]` | `[TBD]` | `[TBD]` | `[TBD]` |

This should contain only verified relationships.

---

# 60. Example End-to-End Traceability

```text
Literature
   │
   │
   ▼
SIFT / Local Feature Literature
   │
   ▼
Research Question:
Can a classical local feature pipeline establish a lunar baseline?
   │
   ▼
EXP-001 — SIFT Baseline
   │
   ▼
Candidate Matches
Verified Inliers
Spatial Coverage
Check-Point RMSE
Runtime
   │
   ▼
Observed Result
   │
   ▼
Research Finding
   │
   ├── supports next experiment
   ├── exposes limitation
   └── motivates alternative method
```

The same structure applies to scale, illumination, geometry, multimodal matching, and sub-pixel refinement.

---

# 61. Relationship to `experiments/`

The literature directory answers:

> **What does existing knowledge tell us to investigate?**

The experiment directory answers:

> **What did ChandraMap test?**

For example:

```text
research/literature/
    ↓
Why compare affine and homography?
    ↓
experiments/v1/geometry/
    ↓
EXP-004 — Affine vs Homography
    ↓
Measured RMSE / residuals / coverage
```

---

# 62. Relationship to `benchmarks/`

The literature directory informs benchmark design.

The benchmark directory defines the actual evaluation protocol.

```text
Literature
    ↓
Relevant evaluation practices
    ↓
Benchmark design
    ↓
Controlled test pairs
    ↓
Metrics
    ↓
Experiment results
```

The benchmark itself should not be modified merely because a paper reports a different metric.

Any benchmark change should be explicitly justified and documented.

---

# 63. Relationship to `data/`

Literature may identify:

- relevant datasets
- sensor documentation
- product specifications
- coordinate systems
- ground-truth approaches
- reference data

But actual ChandraMap data definitions belong in `data/`.

```text
Literature
    ↓
Dataset / Product Understanding
    ↓
data/
    ↓
Actual Dataset
```

---

# 64. Relationship to `results/`

Literature should never be mixed with experimental results.

A published result is:

```text
Literature Evidence
```

A ChandraMap result is:

```text
Experimental Evidence
```

They can be compared, but they must remain distinguishable.

---

# 65. Literature Review Deliverables

A mature literature area should eventually provide:

- bibliography
- literature summaries
- research-topic organization
- method comparisons
- research-question mapping
- experiment mapping
- limitations
- implementation availability
- dataset references
- evaluation methodology references
- research gaps

The exact filenames should follow the repository's actual structure.

---

# 66. Recommended Future Files

If the repository needs additional structure, possible files include:

```text
research/literature/
├── README.md
├── bibliography.md
├── literature-matrix.md
├── research-gaps.md
├── method-comparison.md
└── references/
```

These are recommendations only.

Do not create them unless they are actually required by the repository architecture.

---

# 67. Literature Gap Identification

A literature gap should be stated carefully.

A gap is not:

> "Nobody has solved this."

unless a sufficiently comprehensive review supports that claim.

Prefer:

> "The reviewed sources identified here do not provide evidence for [specific condition]."

For example:

```text
Existing literature:
Strong evidence for terrestrial feature matching.

ChandraMap gap:
Limited evidence identified for the same method under
large lunar scale differences and Sun-angle variation.

Experiment:
[TBD]
```

This is more defensible than claiming that no prior work exists.

---

# 68. Research Gap Categories

Potential gaps may involve:

### Domain Gap

Terrestrial method → lunar imagery.

### Sensor Gap

Method tested on one sensor → Chandrayaan-2 cross-sensor imagery.

### Scale Gap

Moderate scale variation → very large GSD differences.

### Illumination Gap

Normal photometric variation → large Sun-angle/shadow changes.

### Geometry Gap

Planar images → relief-rich lunar terrain.

### Evaluation Gap

Visual alignment → independent check-point accuracy.

### Reproducibility Gap

Published result → unavailable implementation/data/evaluation details.

These are categories for investigation, not claims that a gap definitely exists.

---

# 69. Literature Review Writing Style

Use scientific language.

Prefer:

> "The authors report..."

> "The method was evaluated on..."

> "The paper identifies..."

> "The reported limitation is..."

> "This may be relevant to ChandraMap because..."

> "ChandraMap will test..."

Avoid:

> "This algorithm is amazing."

> "This is definitely the best method."

> "This proves our approach."

> "This will solve lunar registration."

---

# 70. Evidence Levels

When useful, label the strength of a literature statement.

| Level                        | Meaning                                   |
| ---------------------------- | ----------------------------------------- |
| `Direct evidence`            | Directly demonstrated in the cited source |
| `Reported claim`             | Authors report the claim                  |
| `Methodological implication` | Reasonable implication of the method      |
| `ChandraMap hypothesis`      | Not yet experimentally established here   |
| `Open question`              | Requires further investigation            |

This prevents hypotheses from becoming accidental facts.

---

# 71. Reproducible Literature Workflow

A reproducible literature workflow should preserve:

```text
Source
  ↓
Version / Identifier
  ↓
Review Date
  ↓
Reviewer
  ↓
Summary
  ↓
Extracted Evidence
  ↓
Research Question
  ↓
Experiment
```

Recommended metadata:

| Field          | Value   |
| -------------- | ------- |
| Reviewed on    | `[TBD]` |
| Reviewed by    | `[TBD]` |
| Source version | `[TBD]` |
| Identifier     | `[TBD]` |
| Notes version  | `[TBD]` |

---

# 72. Review Date

Literature records should include a review date when appropriate.

This is especially useful for:

- rapidly evolving machine-learning methods
- software repositories
- implementation availability
- pretrained model availability
- benchmark datasets

A paper's publication date and the date ChandraMap reviewed it are different pieces of information.

---

# 73. Literature Search Log

For major research questions, contributors may maintain a search log.

```markdown
## Literature Search Log

| Date  | Query / Topic | Sources Checked | Inclusion Criteria | Notes |
| ----- | ------------- | --------------- | ------------------ | ----- |
| [TBD] | [TBD]         | [TBD]           | [TBD]              | [TBD] |
```

This is particularly useful when making claims about research gaps.

---

# 74. Inclusion Criteria

Before adding a reference to a focused literature review, define why it qualifies.

Possible criteria:

- directly addresses lunar imaging
- addresses planetary registration
- addresses remote-sensing correspondence
- addresses multimodal registration
- addresses scale variation
- addresses illumination variation
- addresses geometric registration
- addresses sub-pixel refinement
- provides relevant evaluation methodology

The actual criteria should be stated for the specific review.

---

# 75. Exclusion Criteria

Potential exclusion reasons:

- unrelated imaging domain
- no relevance to registration
- insufficient methodological information
- duplicate publication
- unreliable source
- unsupported secondary summary
- superseded implementation
- outside the research question

Do not remove a source solely because it contradicts the current hypothesis.

Contradictory evidence can be scientifically useful.

---

# 76. Contradictory Literature

When sources disagree:

1. identify the disagreement
2. describe the datasets
3. compare experimental conditions
4. compare metrics
5. compare assumptions
6. identify methodological differences
7. avoid selecting a conclusion solely because it supports ChandraMap

Example:

```text
Paper A:
Reports improvement under condition A.

Paper B:
Reports weaker improvement under condition B.

ChandraMap:
Tests condition C.

Conclusion:
[TBD after experiment]
```

---

# 77. Literature and Scientific Integrity

The literature directory should help prevent:

- unsupported claims
- citation laundering
- accidental plagiarism
- fabricated references
- incorrect attribution
- method misclassification
- benchmark leakage
- cherry-picked evidence
- confusion between published and project results

Scientific credibility depends not only on having references, but on representing them accurately.

---

# 78. Minimum Standard for a New Reference

A reference should not be considered fully documented until the contributor can answer:

```text
What is it?
Why is it relevant?
What did it actually test?
What did it report?
What are its limitations?
What does it contribute to ChandraMap?
Which research question does it inform?
Which experiment, if any, tests it?
```

---

# 79. ChandraMap Literature Philosophy

The project follows a simple research principle:

> **Use literature to understand what is known, use experiments to determine what works under ChandraMap's conditions, and keep the distinction between the two explicit.**

This is consistent with the project's broader methodology:

> **Build small. Measure honestly. Keep the failures.**

The supplied project guidance emphasizes obtaining one real end-to-end measurable result before expanding the architecture, and recommends preserving match plots, rejected outliers, registered overlays, inlier statistics, check-point error, and runtime as evidence.

---

# 80. Recommended Literature Record Template

Copy this template when creating a structured literature entry:

```markdown
# LIT-[ID] — [Short Title]

## Bibliographic Information

| Field            | Value    |
| ---------------- | -------- |
| Literature ID    | LIT-[ID] |
| Title            | [TBD]    |
| Authors          | [TBD]    |
| Year             | [TBD]    |
| Venue            | [TBD]    |
| DOI / Identifier | [TBD]    |
| Official URL     | [TBD]    |
| Source Type      | [TBD]    |
| Review Date      | [TBD]    |
| Reviewer         | [TBD]    |

## Research Area

[TBD]

## Problem Addressed

[TBD]

## Method

[TBD]

## Assumptions

- [TBD]

## Dataset

[TBD]

## Evaluation Protocol

[TBD]

## Reported Results

[TBD]

## Limitations

[TBD]

## Relevance to ChandraMap

[TBD]

## Related Research Questions

- RQ-[TBD]

## Related Experiments

- EXP-[TBD]

## Implementation Availability

[TBD]

## Dataset Availability

[TBD]

## ChandraMap Status

[TBD]
```

---

# 81. Recommended Method Review Template

For algorithm-focused literature:

```markdown
# [Method Name]

## Role

[TBD]

## Original Source

[TBD]

## Problem Addressed

[TBD]

## Inputs

[TBD]

## Outputs

[TBD]

## Core Method

[TBD]

## Important Assumptions

[TBD]

## Strengths Reported in Literature

- [TBD]

## Limitations Reported in Literature

- [TBD]

## Domain

[TBD]

## Lunar Relevance

[TBD]

## Cross-Sensor Relevance

[TBD]

## Scale Considerations

[TBD]

## Illumination Considerations

[TBD]

## Geometry Considerations

[TBD]

## Evaluation Considerations

[TBD]

## ChandraMap Research Question

RQ-[TBD]

## ChandraMap Experiment

EXP-[TBD]

## ChandraMap Status

[TBD]
```

---

# 82. Recommended Literature Matrix

For a larger literature review:

```markdown
# ChandraMap Literature Matrix

| ID        | Reference | Research Area | Method | Dataset | Key Finding | Limitation | RQ       | Experiment | Status |
| --------- | --------- | ------------- | ------ | ------- | ----------- | ---------- | -------- | ---------- | ------ |
| LIT-[TBD] | [TBD]     | [TBD]         | [TBD]  | [TBD]   | [TBD]       | [TBD]      | RQ-[TBD] | EXP-[TBD]  | [TBD]  |
```

---

# 83. Definition of Done

`research/literature/` is in good condition when a new contributor can:

- understand the ChandraMap research problem
- locate relevant scientific literature
- understand why a method is being considered
- distinguish papers from experiments
- distinguish published results from ChandraMap results
- identify method assumptions
- identify known limitations
- trace literature to research questions
- trace research questions to experiments
- reproduce the bibliographic source
- understand whether a method is merely researched or actually implemented
- identify where evidence is still missing

---

# 84. Final Research Traceability Model

The intended relationship between the major research areas is:

```text
                         ┌─────────────────────┐
                         │      Literature     │
                         │  Existing Evidence  │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │ Research Questions  │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │     Hypotheses      │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │     Experiments     │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │      Benchmarks     │
                         │  Controlled Tests   │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │   Measured Results  │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │ Analysis / Findings │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │ Future Experiments  │
                         └─────────────────────┘
```

The literature directory therefore serves as the **scientific evidence layer** of the ChandraMap research process.

It should explain where ideas come from, what existing evidence says, what assumptions remain uncertain, and why a particular question deserves an experiment.

---

# 85. Source Basis

This README is grounded in the ChandraMap project materials supplied for the repository, including:

- `SIH26166 Silarlar PS.pdf`
- `Aryan_SIH_26166_Lunar_Image_Correspondence_Feedback.pptx`
- `Aryan_Lunar_Image_Registration_Feedback.pdf`
- ChandraMap V1 experiment and benchmark structure
- the documented SIFT, scale, gradient, geometry, residual, and sub-pixel experiment sequence

The supplied project material identifies mission/data documentation, algorithm papers, implementation repositories, planetary processing documentation, and matching-method literature as complementary research resources.

The project documentation also explicitly frames ChandraMap around reliable correspondences, transformation/geolocation, registered output, and measurable quality metrics rather than treating a final mosaic as the primary scientific output.

Where repository-specific information is not yet established, this README intentionally uses `[TBD]`, `[Not provided]`, `[Not implemented]`, or `[To be documented]` rather than inventing project state or scientific evidence.
