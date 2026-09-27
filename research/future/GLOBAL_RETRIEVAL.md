# Global Retrieval Research

> **Status:** Future research specification
> **Implementation status:** Not implemented or experimentally validated in ChandraMap
> **Research maturity:** Exploratory research direction
> **Primary role:** Candidate-region retrieval before local correspondence and registration

---

## 1. Purpose

This document defines a future research direction for investigating **global image retrieval** as an additional stage in the ChandraMap registration pipeline.

The purpose is to determine whether global retrieval can efficiently identify likely corresponding lunar image regions before expensive local feature matching and geometric registration.

The intended hierarchical architecture is:

```text
Large Lunar Image Archive
        ↓
Global Image Retrieval
        ↓
Candidate Image / Tile Selection
        ↓
Local Feature Matching
        ↓
Geometric Verification
        ↓
Registration
        ↓
Independent Evaluation
```

Global retrieval is therefore a **candidate-search problem**, not a replacement for local registration.

This document does **not** claim that ChandraMap currently contains:

* a global retrieval system
* a production-scale image index
* a validated retrieval model
* a FAISS-based retrieval implementation
* a learned global descriptor
* a validated Recall@K result
* an automatic global-to-local registration pipeline

All of these remain future research until experimentally validated.

---

# 2. ChandraMap Context

ChandraMap is a lunar image correspondence and registration research system focused on aligning images of the same lunar region acquired from different:

* instruments
* spatial resolutions
* illumination conditions
* image representations
* sensing modalities
* viewing and geometric conditions

Relevant Chandrayaan-2 imagery includes:

* **OHRC**
* **TMC-2**
* **IIRS**

The project is organized around measurable experiments, explicit ground truth, geometric verification, quantitative evaluation, controlled comparisons, reproducibility, and separation between research and production.

The current V1 research foundation primarily assumes a known source/reference image pair.

Global retrieval extends the problem:

```text
Known reference image
        ↓
Local registration
```

toward:

```text
Source image
        ↓
Unknown or large reference archive
        ↓
Find likely reference image(s)
        ↓
Local registration
```

This is a substantially different research problem and should therefore be evaluated separately.

---

# 3. Research Status

| Capability                           | Status                          |
| ------------------------------------ | ------------------------------- |
| Known source/reference registration  | V1 research foundation          |
| Local feature matching               | V1 research foundation          |
| Global image retrieval               | Future research                 |
| Large lunar archive indexing         | Future research                 |
| Learned global descriptor            | Future research                 |
| Retrieval benchmark                  | Future research                 |
| Recall@1 measurement                 | Future research                 |
| Recall@5 measurement                 | Future research                 |
| Retrieval-to-registration evaluation | Future research                 |
| FAISS-based indexing                 | Potential future implementation |
| End-to-end retrieval + registration  | Future research                 |
| Production retrieval service         | Not established                 |

No retrieval performance numbers should be inserted until they are measured on a documented benchmark.

---

# 4. Why Global Retrieval Matters

Local feature matching is computationally practical when the correct source/reference pair is already known.

For a large archive, however, the system may face:

```text
1 source image
        ↓
Thousands / millions of candidate images or tiles
        ↓
Local matching against every candidate
```

This can be inefficient.

A hierarchical system could instead use global retrieval to reduce the candidate set:

```text
Large Archive
      ↓
Global Retrieval
      ↓
Top-K Candidates
      ↓
Local Matching
      ↓
Geometric Verification
```

The research question is whether this additional stage provides useful search efficiency while preserving sufficient recall of the correct lunar region.

---

# 5. Core Research Question

The central research question is:

> **Can global image retrieval efficiently identify the correct or geographically corresponding lunar image region from a large archive before local feature matching and geometric registration?**

This should be decomposed into measurable questions:

* Can the correct reference image be retrieved at Rank 1?
* Can the correct reference region appear within the top K candidates?
* How does retrieval performance change with scale differences?
* How does retrieval perform under illumination differences?
* How does retrieval perform across sensors?
* How does retrieval behave on low-feature terrain?
* How does retrieval behave when many archive images overlap spatially?
* How much does retrieval reduce the number of local matching operations?
* Does retrieval preserve enough candidates for successful downstream registration?
* Does retrieval improve end-to-end search efficiency?
* When is retrieval unnecessary because geographic metadata already constrains the search?

---

# 6. Retrieval Is Not Registration

A fundamental distinction is:

### Retrieval

Answers:

> Which archive image or region is likely to correspond to this source image?

### Registration

Answers:

> How are the corresponding source and reference images geometrically aligned?

The stages are therefore:

```text
Retrieval
    ↓
Candidate selection
    ↓
Correspondence
    ↓
Geometric verification
    ↓
Registration
```

A high retrieval score does not establish registration accuracy.

Similarly, successful local registration does not automatically establish that the retrieval system is scalable.

Both stages require separate evaluation.

---

# 7. When Global Retrieval May Be Unnecessary

Global retrieval should not be introduced simply because it is technically interesting.

The project context explicitly recognizes that geographic information may already constrain the search.

If reliable information such as:

* image footprint
* geographic coordinates
* map projection
* acquisition metadata
* known region of interest

can narrow the reference search sufficiently, a global visual retrieval stage may provide limited additional value.

The research should therefore compare:

```text
Metadata-constrained search
```

against:

```text
Global visual retrieval
```

where appropriate.

The objective is to determine whether visual retrieval solves a genuine search problem.

---

# 8. Hierarchical Registration Architecture

The future architecture can be represented as:

```text
                         ┌─────────────────────┐
                         │ Large Lunar Archive │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │ Global Retrieval    │
                         └──────────┬──────────┘
                                    │
                              Top-K candidates
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │ Local Features      │
                         │ + Feature Matching  │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │ Geometric           │
                         │ Verification        │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │ Registration        │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │ Independent         │
                         │ Evaluation          │
                         └─────────────────────┘
```

Global retrieval therefore acts as a **front-end search stage** for the existing local registration research.

---

# 9. Retrieval Unit

The archive does not necessarily need to be indexed only as complete images.

Potential retrieval units include:

* complete images
* image tiles
* overlapping image patches
* map regions
* sensor-specific products
* multi-resolution reference regions

The choice of retrieval unit affects:

* archive size
* indexing cost
* spatial precision
* retrieval recall
* downstream local matching
* storage requirements

This choice should be explicitly documented in future experiments.

---

# 10. Image Tiling

For large lunar images, tiling may be necessary.

A conceptual archive could be represented as:

```text
Large Reference Image
+-------------------------------+
| Tile 1 | Tile 2 | Tile 3      |
|--------|--------|-------------|
| Tile 4 | Tile 5 | Tile 6      |
|--------|--------|-------------|
| Tile 7 | Tile 8 | Tile 9      |
+-------------------------------+
```

Each tile could receive a global descriptor and become an indexed retrieval unit.

However, tiling introduces additional research variables:

* tile size
* overlap
* scale
* boundary effects
* descriptor representation
* number of indexed items

These should be controlled and benchmarked.

---

# 11. Global Representation

A retrieval system requires a representation that allows source images to be compared efficiently against archive items.

Potential research directions include:

* global image descriptors
* pooled local feature representations
* learned visual embeddings
* structural representations
* multi-scale representations
* sensor-specific representations

The project should not assume that a representation developed for ordinary terrestrial imagery will automatically perform well on lunar imagery.

The representation must be evaluated against the ChandraMap conditions.

---

# 12. Retrieval vs Local Features

Global retrieval and local matching have different objectives.

| Property          | Global Retrieval         | Local Matching               |
| ----------------- | ------------------------ | ---------------------------- |
| Main objective    | Find likely region       | Establish correspondences    |
| Output            | Ranked candidates        | Point correspondences        |
| Spatial precision | Coarse                   | Fine                         |
| Geometry          | Usually implicit/coarse  | Explicitly verified          |
| Search scale      | Large archive            | Candidate pair               |
| Primary metrics   | Recall@K                 | Inlier ratio, RMSE, coverage |
| Main failure      | Missed correct candidate | Incorrect correspondences    |

A successful system requires both stages to work together.

---

# 13. Retrieval Metrics

The primary retrieval metrics should include **Recall@K**.

## 13.1 Recall@1

Measures whether the correct target appears at the first retrieval position.

$$
Recall@1 =
\frac{
N(\text{queries where correct target is ranked first})
}{
N(\text{queries})
}
$$

---

## 13.2 Recall@5

Measures whether the correct target appears among the first five retrieved candidates.

$$
Recall@5 =
\frac{
N(\text{queries where correct target is in top 5})
}{
N(\text{queries})
}
$$

Other K values may be evaluated where justified by the archive size and downstream computational budget.

The benchmark should define the values of K before evaluation.

---

# 14. What Is the Correct Target?

Retrieval ground truth must account for the fact that multiple archive images may legitimately correspond to the same lunar region.

For example:

```text
Source image
     ↓
Reference archive
     ├── Image A — same region
     ├── Image B — overlapping region
     ├── Image C — neighboring region
     └── Image D — unrelated region
```

A strict single-image ground truth may therefore be inappropriate in some cases.

The benchmark should distinguish between:

### Exact target

The specific archive image from which the query/reference relationship is known.

### Region-level target

Any archive image or tile sufficiently overlapping the true lunar region.

### Retrieval-compatible target set

A documented set of archive entries that should be considered valid retrieval results.

The chosen definition must be established before benchmarking.

---

# 15. Geographic Ground Truth

Where geographic metadata is reliable, retrieval ground truth can incorporate spatial overlap.

Possible information includes:

* footprint
* coordinates
* map projection
* image extent
* tile boundaries

This allows evaluation of whether a retrieved candidate covers the correct lunar region even if it is not the exact same image.

This is particularly important when the archive contains overlapping observations.

---

# 16. Retrieval Ground Truth and Local Ground Truth

Global retrieval ground truth and local registration ground truth serve different purposes.

```text
Global retrieval ground truth
        ↓
Was the correct region retrieved?

Local registration ground truth
        ↓
Was the retrieved region correctly aligned?
```

They should not be conflated.

An experiment may therefore produce:

```text
Retrieval success
        +
Registration success
```

rather than treating retrieval success alone as end-to-end success.

---

# 17. End-to-End Retrieval Evaluation

The complete system should eventually be evaluated as:

```text
Query Image
     ↓
Global Retrieval
     ↓
Top-K Candidates
     ↓
Local Matching
     ↓
Geometric Verification
     ↓
Registration
     ↓
Independent Accuracy Evaluation
```

Possible end-to-end measurements include:

* retrieval Recall@K
* number of candidates requiring local matching
* successful registration rate
* independent registration RMSE
* total runtime
* failure rate

This determines whether retrieval actually improves the complete system.

---

# 18. Candidate Budget

A major research variable is the number of retrieved candidates.

For example:

```text
Archive
  ↓
Top 1
  ↓
Local registration
```

versus:

```text
Archive
  ↓
Top 5
  ↓
Local registration on each
  ↓
Best geometrically verified result
```

Increasing K may improve the chance of including a valid target but also increases downstream computation.

The experiment should therefore study:

```text
Recall@K
        vs
Local matching cost
```

rather than maximizing K without considering computational cost.

---

# 19. Retrieval-to-Registration Trade-Off

The key systems question is:

> **How many candidates are needed to preserve registration success while keeping the downstream search computationally manageable?**

Conceptually:

```text
Small K
  ↓
Lower computation
  ↓
Potentially lower retrieval coverage

Large K
  ↓
Higher computation
  ↓
Potentially higher retrieval coverage
```

The optimal K should be determined empirically for the intended archive and benchmark.

No fixed K should be declared as optimal before experiments.

---

# 20. Local Registration as the Final Authority

Global retrieval should remain subordinate to geometric verification.

The pipeline should be:

```text
Global Retrieval
       ↓
Candidate Ranking
       ↓
Local Feature Matching
       ↓
RANSAC / Geometric Verification
       ↓
Independent Accuracy Evaluation
```

A candidate ranked first by the retrieval model should not automatically be accepted.

The final registration decision should rely on the project's geometric and independent evaluation criteria.

---

# 21. Candidate Ranking vs Geometric Ranking

There are two distinct rankings:

### Retrieval ranking

Produced by global similarity.

```text
Candidate A — score 0.91
Candidate B — score 0.87
Candidate C — score 0.83
```

### Geometric ranking

Produced after local matching and verification.

```text
Candidate B — strong geometric consistency
Candidate A — weak geometric consistency
Candidate C — insufficient overlap
```

These rankings need not agree.

The research should investigate whether global retrieval successfully places geometrically valid candidates within a sufficiently small top-K set.

---

# 22. Interaction With SIFT Baseline

The existing SIFT baseline provides the local registration reference.

A future hierarchical baseline could therefore be:

```text
Archive
   ↓
Simple Global Retrieval
   ↓
Top-K Candidates
   ↓
SIFT
   ↓
Classical Matching
   ↓
RANSAC
   ↓
Registration
```

This establishes whether global retrieval provides value even before introducing learned local matching.

The experiment should not combine global retrieval and LightGlue simultaneously in the first experiment because doing so makes attribution difficult.

---

# 23. Interaction With LightGlue

After retrieval is independently understood, the pipeline could be extended:

```text
Archive
      ↓
Global Retrieval
      ↓
Top-K Candidates
      ↓
Local Feature Extraction
      ↓
LightGlue
      ↓
Geometric Verification
      ↓
Registration
```

This creates a hierarchical learned pipeline.

However, the research should preserve separate measurements for:

* retrieval quality
* local matching quality
* geometric verification
* registration accuracy
* total runtime

Otherwise, it becomes difficult to identify where an improvement or failure originated.

---

# 24. Interaction With ALIKED

A possible future architecture is:

```text
Global Retrieval
       ↓
ALIKED
       ↓
LightGlue
       ↓
Geometric Verification
       ↓
Registration
```

This combines:

* global candidate search
* learned local feature extraction
* learned sparse matching
* geometric verification

Such a system should only be evaluated after each major component has been independently characterized.

A combined result must not be interpreted as evidence that any individual component is responsible for the observed improvement.

---

# 25. Cross-Sensor Retrieval

Cross-sensor retrieval is a major research challenge.

Potential query/reference combinations include:

```text
OHRC → OHRC
OHRC → TMC-2
TMC-2 → OHRC
OHRC → IIRS
TMC-2 → IIRS
```

The exact combinations should be determined by available project data and benchmark scope.

The difficulty may increase because sensors can differ in:

* spatial resolution
* spectral response
* radiometric characteristics
* image representation
* acquisition geometry
* illumination
* preprocessing

A global descriptor that works for same-sensor imagery may not work across sensors.

---

# 26. IIRS Retrieval

IIRS requires special treatment.

IIRS imagery is hyperspectral/infrared and therefore requires an appropriate 2D representation before conventional image retrieval methods can be evaluated.

Potential representations may include:

* selected spectral band
* PCA representation
* composite representation
* structural representation
* another documented representation

The representation should be treated as an experimental variable.

The research question becomes:

> **Which 2D representation preserves enough spatially meaningful structure for reliable global retrieval?**

No representation should be assumed to be universally appropriate.

---

# 27. Scale Robustness

Global retrieval must handle meaningful differences in image scale.

Potential conditions include:

```text
High-resolution query
       ↓
Lower-resolution reference
```

and:

```text
Lower-resolution query
       ↓
Higher-resolution reference
```

The benchmark should distinguish:

* pixel resizing
* effective image scale
* physical ground sampling
* actual available terrain detail

Upsampling does not recreate missing spatial information.

Therefore, scale-aware retrieval may require:

* multi-scale descriptors
* reference pyramids
* appropriately downsampled representations
* scale-specific indexing

The correct approach should be established experimentally.

---

# 28. Illumination Robustness

Lunar illumination can change:

* shadow locations
* shadow lengths
* crater contrast
* local intensity
* visible terrain structures

Global retrieval must therefore be evaluated under different illumination conditions.

Possible categories include:

* similar illumination
* moderate Sun-angle difference
* strong Sun-angle difference

A descriptor may be globally similar even when local shadows differ, but this must be measured rather than assumed.

---

# 29. Low-Feature Terrain

Low-feature terrain is particularly important for retrieval.

A global representation may still identify broad terrain structure even when local feature extraction becomes difficult.

Alternatively, visually ambiguous regions may produce incorrect retrievals.

Experiments should therefore include:

* low-feature terrain
* repetitive terrain
* strongly shadowed regions
* terrain with limited distinctive structure

Retrieval failure cases should be analyzed spatially and geographically where possible.

---

# 30. Archive Composition

Retrieval performance depends strongly on what exists in the archive.

An archive containing:

```text
Few well-separated regions
```

is fundamentally easier than:

```text
Many overlapping images
of neighboring lunar terrain
```

The benchmark should therefore document:

* number of archive items
* image/tile definitions
* geographic distribution
* sensor distribution
* overlap characteristics
* resolution distribution
* illumination distribution

Archive size should not be interpreted without understanding archive composition.

---

# 31. Negative Samples

A retrieval benchmark needs meaningful negatives.

Negative candidates should include varying levels of difficulty:

### Easy negatives

Clearly unrelated lunar regions.

### Geographic negatives

Nearby regions with similar broad terrain.

### Appearance negatives

Different regions with visually similar structures.

### Sensor negatives

Images from other sensors that resemble the query.

### Hard negatives

Overlapping or neighboring regions that could plausibly confuse the retrieval model.

Hard negatives are particularly important because retrieval performance can appear strong when the archive contains only easy negatives.

---

# 32. Retrieval Failure Modes

Future experiments should classify failures.

## 32.1 Correct region absent from top-K

The global representation fails to retrieve the required candidate.

## 32.2 Visually similar wrong region

A different lunar region is ranked highly because of similar morphology.

## 32.3 Neighboring-region confusion

The system retrieves a nearby but geometrically insufficient region.

## 32.4 Sensor mismatch

The descriptor fails because the query and reference sensors differ.

## 32.5 Illumination mismatch

The representation changes substantially under different Sun angles.

## 32.6 Scale mismatch

The same region becomes difficult to recognize at substantially different effective scales.

## 32.7 Tile-boundary failure

The correct region is split across tiles and no individual tile receives a sufficiently high retrieval score.

## 32.8 Archive bias

The retrieval system performs well because the archive is too small or too easy.

## 32.9 Metadata leakage

Retrieval performance appears strong because geographic information accidentally reveals the target.

Metadata use must therefore be documented and controlled.

---

# 33. Metadata-Constrained Retrieval

A useful research comparison is:

```text
Method A
Visual retrieval only

Method B
Geographic metadata only

Method C
Metadata + visual retrieval
```

This allows ChandraMap to determine whether visual retrieval provides additional value beyond information already available from image metadata.

If metadata reduces the candidate space sufficiently, a full global retrieval system may not be necessary for that operational scenario.

---

# 34. Retrieval Descriptor Research

Potential descriptor strategies include:

### Classical global descriptors

Potentially simple and interpretable.

### Learned global embeddings

Potentially more robust to appearance variation but vulnerable to domain shift.

### Multi-scale descriptors

Potentially useful for large resolution differences.

### Structural descriptors

Potentially useful when intensity changes strongly with illumination.

### Sensor-specific descriptors

Potentially useful when cross-modal differences are substantial.

These are research categories rather than selected ChandraMap implementations.

---

# 35. Learned Retrieval and Domain Shift

If a learned global embedding is investigated, domain shift must be treated as a primary research concern.

A model trained on ordinary terrestrial imagery may not encode lunar terrain appropriately.

Relevant differences include:

* crater morphology
* absence of terrestrial vegetation
* lunar illumination
* terrain texture
* sensor characteristics
* spatial resolution
* spectral characteristics

Therefore, retrieval performance must be measured on ChandraMap's lunar data.

---

# 36. Synthetic Training Data

Synthetic augmentation may eventually be investigated for global retrieval.

Possible transformations include:

* rotation
* scale
* contrast variation
* illumination-like transformations
* synthetic cropping

However:

> **Synthetic appearance changes are not equivalent to real lunar sensor differences.**

Synthetic training should therefore be treated as a research hypothesis rather than proof of physical robustness.

---

# 37. Multi-Scale Retrieval

A future retrieval system may use multiple scales:

```text
Query
  ↓
Global descriptor
  ├── coarse scale
  ├── medium scale
  └── fine scale
          ↓
      candidate ranking
```

Alternatively, the archive itself may be indexed at multiple effective scales.

The objective is to determine whether multi-scale retrieval improves recall for cross-resolution imagery without creating unacceptable indexing and search costs.

---

# 38. Retrieval Indexing

For a sufficiently large archive, exhaustive comparison may become expensive.

A conceptual architecture is:

```text
Archive Images
      ↓
Descriptor Generation
      ↓
Offline Index
      ↓
Query Descriptor
      ↓
Nearest-Neighbor Search
      ↓
Top-K Candidates
```

An approximate nearest-neighbor library such as **FAISS** could be investigated as indexing/search infrastructure.

FAISS should not be treated as the retrieval model itself.

The retrieval system consists of:

```text
Image representation
        +
Descriptor generation
        +
Index
        +
Similarity/search procedure
        +
Candidate evaluation
```

Each component should be documented.

---

# 39. Offline vs Online Work

A large retrieval system should distinguish between offline and online operations.

## Offline

Potentially:

* archive preprocessing
* tiling
* descriptor generation
* index construction
* metadata association

## Online

Potentially:

* query preprocessing
* query descriptor generation
* nearest-neighbor search
* candidate ranking
* local matching
* geometric verification

This separation is useful for evaluating realistic runtime.

---

# 40. Retrieval Runtime

Runtime should be measured at multiple levels.

### Offline cost

* archive preprocessing
* descriptor generation
* index construction

### Query cost

* query preprocessing
* descriptor generation
* retrieval search

### End-to-end cost

```text
Query
  ↓
Retrieval
  ↓
Top-K local matching
  ↓
Geometric verification
  ↓
Registration
```

The relevant comparison is not only:

```text
retrieval time
```

but:

```text
retrieval + downstream registration time
```

compared with the corresponding exhaustive-search strategy.

---

# 41. Search Reduction

A key potential benefit of retrieval is reducing the number of local registrations.

For example:

```text
Archive
1000 candidates
     ↓
Global Retrieval
Top 5
     ↓
Local Registration
5 candidates
```

The research should measure:

* archive size
* number of retrieved candidates
* number of local matching operations
* number of successful registrations
* total runtime

A retrieval system is useful only if its reduction in downstream search does not cause unacceptable loss of correct candidates.

---

# 42. Exhaustive Baseline

A global retrieval experiment should have an appropriate baseline.

For a manageable archive:

```text
Query
  ↓
Local registration against every archive item
  ↓
Geometric verification
  ↓
Best valid result
```

This establishes a reference for:

* correctness
* search cost
* retrieval recall

The exact exhaustive procedure depends on the benchmark size and available data.

---

# 43. Retrieval Baseline

Before using a sophisticated learned global descriptor, a simple baseline should be established where practical.

Possible baselines include:

* metadata-constrained search
* simple image similarity
* basic global image representation
* exhaustive local matching for smaller archives

The exact baseline should be chosen according to available project data and research objectives.

A complex retrieval model should demonstrate improvement over an appropriate baseline.

---

# 44. Controlled Retrieval Experiment

The first retrieval experiment should remain small.

### Objective

Determine whether global retrieval can reduce the local registration search space without losing the correct target.

### Experimental structure

```text
Known query
      ↓
Known archive
      ↓
Global descriptor
      ↓
Top-K candidates
      ↓
Check retrieval ground truth
```

Measure:

* Recall@1
* Recall@K
* candidate count
* retrieval runtime
* failure cases

Do not introduce local registration into the first experiment unless required to answer the research question.

This isolates the retrieval stage.

---

# 45. Retrieval-to-Registration Experiment

After retrieval itself is understood:

```text
Query
  ↓
Global Retrieval
  ↓
Top-K
  ↓
SIFT + Classical Matching
  ↓
Geometric Verification
  ↓
Registration
  ↓
Independent Check Points
```

Compare against:

```text
Query
  ↓
Exhaustive Local Registration
```

This measures whether retrieval actually reduces search cost while preserving end-to-end registration success.

---

# 46. Learned Local Matcher Integration

Only after the retrieval stage is independently characterized should a future experiment investigate:

```text
Global Retrieval
       ↓
ALIKED
       ↓
LightGlue
       ↓
Geometric Verification
       ↓
Registration
```

This can become a later hierarchical research system.

The components should remain independently measurable.

---

# 47. Retrieval Benchmark Matrix

A future benchmark can use:

| Condition           | Recall@1 | Recall@5 | Retrieval Time | Registration Success | Check-Point RMSE | Total Runtime |
| ------------------- | -------: | -------: | -------------: | -------------------: | ---------------: | ------------: |
| Easy                |      TBD |      TBD |            TBD |                  TBD |              TBD |           TBD |
| Scale stress        |      TBD |      TBD |            TBD |                  TBD |              TBD |           TBD |
| Illumination stress |      TBD |      TBD |            TBD |                  TBD |              TBD |           TBD |
| Sensor stress       |      TBD |      TBD |            TBD |                  TBD |              TBD |           TBD |
| Low-feature         |      TBD |      TBD |            TBD |                  TBD |              TBD |           TBD |
| Hard negatives      |      TBD |      TBD |            TBD |                  TBD |              TBD |           TBD |

Values must remain `TBD` until measured.

---

# 48. Retrieval Evaluation by Sensor

Results should be separated by sensor where the benchmark supports it.

Example structure:

| Query Sensor | Reference Sensor | Recall@1 | Recall@5 | Registration Success | RMSE |
| ------------ | ---------------- | -------: | -------: | -------------------: | ---: |
| OHRC         | OHRC             |      TBD |      TBD |                  TBD |  TBD |
| OHRC         | TMC-2            |      TBD |      TBD |                  TBD |  TBD |
| TMC-2        | OHRC             |      TBD |      TBD |                  TBD |  TBD |
| OHRC         | IIRS             |      TBD |      TBD |                  TBD |  TBD |
| TMC-2        | IIRS             |      TBD |      TBD |                  TBD |  TBD |

This is an evaluation template, not a claim that all listed combinations are currently available in the project data.

---

# 49. Spatial Retrieval Evaluation

A retrieval system should eventually be analyzed geographically.

For each query, investigate:

```text
Query footprint
      ↓
Retrieved candidate footprint
      ↓
Spatial overlap
```

Potential measures may include:

* geographic overlap
* distance between region centers
* coverage of true region
* tile intersection

The exact metric should be selected according to the ground-truth protocol.

A retrieval candidate that is geographically adjacent but does not contain sufficient overlap may not be useful for local registration.

---

# 50. Retrieval Confidence

A retrieval similarity score should not be interpreted as a probability of successful registration unless experimentally calibrated.

For example:

```text
High retrieval similarity
        ≠
Guaranteed geometric registration
```

The system should therefore retain:

* retrieval score
* rank
* geometric verification result
* independent registration result

as separate quantities.

---

# 51. Failure Analysis

For each retrieval failure, the experiment should ask:

1. Was the correct region present in the archive?
2. Was it present at the expected scale?
3. Was the representation appropriate?
4. Was the query/reference sensor combination difficult?
5. Was the correct candidate ranked just below K?
6. Was a neighboring region ranked higher?
7. Was the archive too ambiguous?
8. Did illumination cause the failure?
9. Did scale cause the failure?
10. Did tile boundaries cause the failure?
11. Did metadata provide information that the retrieval system ignored?

This creates a research record rather than simply a success percentage.

---

# 52. Leakage and Benchmark Integrity

Retrieval experiments are especially vulnerable to leakage.

Potential leakage sources include:

* duplicate images
* near-identical tiles
* overlapping training and evaluation regions
* archive metadata revealing the target
* descriptors generated using evaluation information
* query images accidentally present in the index
* ground-truth information used during retrieval

The benchmark should therefore define:

```text
Index set
Query set
Ground-truth set
Evaluation protocol
```

before final evaluation.

---

# 53. Duplicate and Near-Duplicate Images

A global archive may contain:

* duplicate products
* overlapping tiles
* different processing versions
* multiple observations of the same region

These can make retrieval artificially easy.

The benchmark should document how such cases are handled.

Possible categories include:

* exact duplicate
* near duplicate
* same region / different acquisition
* overlapping neighboring tile
* genuinely distinct region

The definition of a correct retrieval target must account for these cases.

---

# 54. Data Splitting

If a learned retrieval model is trained or adapted using lunar imagery, spatial separation should be considered.

Randomly splitting overlapping tiles can produce overly optimistic results because nearly identical terrain may appear in both training and evaluation data.

The split strategy should therefore be documented according to the available dataset.

Potential separation dimensions include:

* geographic region
* acquisition
* sensor
* image product
* time/acquisition condition

The appropriate split depends on the actual research objective.

---

# 55. Cross-Sensor Retrieval Experiments

A structured research matrix could investigate:

```text
Same sensor
    ↓
Different resolution
    ↓
Different illumination
    ↓
Different sensor
    ↓
Different modality
```

This provides a progression from easier retrieval conditions to harder cross-modal retrieval.

The experiment should report results separately rather than hiding difficult cases inside one aggregate number.

---

# 56. Retrieval and Illumination Representation

The research should investigate whether retrieval benefits from representations that emphasize stable terrain structure.

Possible representations include:

* intensity
* normalized intensity
* gradient
* edge/structure
* multi-channel structural representations

This connects global retrieval with existing ChandraMap research on illumination invariance and gradient-based representations.

However, the representation must be measured rather than assumed to be illumination invariant.

---

# 57. Retrieval and Scale Pyramid

The existing scale-pyramid research can inform future global retrieval.

A conceptual archive may contain:

```text
Reference Region
       ├── Coarse representation
       ├── Medium representation
       └── Fine representation
```

The query can then be compared against multiple effective scales.

This may be useful when source and reference sensors have significantly different spatial resolution.

The exact pyramid design should be experimentally established.

---

# 58. Relationship to Existing V1 Research

Global retrieval builds on, but does not replace, the V1 registration foundation.

The current research progression includes:

* SIFT baseline
* reference-image scale pyramid
* gradient/structure representation
* affine vs homography comparison
* residual analysis
* sub-pixel refinement

Global retrieval adds an earlier stage:

```text
Archive Search
      ↓
Candidate Selection
      ↓
Existing V1 Registration Pipeline
```

This means the V1 registration system can become the downstream evaluator for retrieval candidates.

---

# 59. Relationship to Existing Experiments

Relevant established experiments include:

* `experiments/v1/README.md`
* `experiments/templates/EXPERIMENT_TEMPLATE.md`
* `experiments/v1/baseline/EXP-001-sift-baseline/README.md`
* `experiments/v1/preprocessing/EXP-002-scale-pyramid/README.md`
* `experiments/v1/preprocessing/EXP-003-gradient-representation/README.md`
* `experiments/v1/geometry/EXP-004-affine-vs-homography/README.md`
* `experiments/v1/geometry/EXP-005-residual-analysis/README.md`
* `experiments/v1/refinement/EXP-006-subpixel-refinement/README.md`

Future retrieval experiments should reuse established:

* ground-truth principles
* geometric verification
* independent evaluation
* residual analysis
* reproducibility practices

where applicable.

---

# 60. Relationship to Future Learned Methods

Global retrieval is complementary to future local correspondence research.

Potential future components include:

```text
Global Retrieval
      ↓
ALIKED
      ↓
LightGlue
      ↓
Geometric Verification
```

Other research directions such as:

* LoFTR
* RIFT
* CFOG

should remain separate research tracks until their specific roles and evidence are established.

Global retrieval does not require any one particular local matcher.

---

# 61. Retrieval as a Separate Research Layer

The repository should maintain a conceptual distinction between:

```text
Global Search
```

and:

```text
Local Registration
```

This improves scientific clarity.

A retrieval experiment can fail while local registration succeeds.

A retrieval experiment can succeed while local registration fails.

These are different failure modes.

---

# 62. End-to-End Success Definition

A future end-to-end system should distinguish at least three outcomes.

### Retrieval success

The correct target is included within the allowed retrieval set.

### Registration success

A retrieved candidate produces a valid geometrically verified registration.

### End-to-end success

The system retrieves a valid candidate and obtains an acceptable independently evaluated registration.

Conceptually:

```text
Retrieval Success
       +
Registration Success
       =
End-to-End Success
```

The exact acceptance criteria must be defined by the benchmark.

---

# 63. Runtime Comparison

A meaningful future comparison could be:

| Strategy             | Search Scope    | Local Registrations | Retrieval Cost | Total Runtime | Registration Success |
| -------------------- | --------------- | ------------------: | -------------: | ------------: | -------------------: |
| Exhaustive           | Entire archive  |                 TBD |            N/A |           TBD |                  TBD |
| Metadata-constrained | Reduced archive |                 TBD |            TBD |           TBD |                  TBD |
| Global retrieval     | Top-K           |                 TBD |            TBD |           TBD |                  TBD |

This allows the project to determine whether retrieval actually provides a system-level advantage.

---

# 64. Reproducibility Requirements

Every retrieval experiment should document:

### Archive

* archive version
* archive size
* image/tile definitions
* sensor composition
* geographic coverage

### Query

* query identifiers
* sensor
* representation
* image dimensions
* scale information where available

### Descriptor

* representation
* model
* model version
* preprocessing
* descriptor dimensionality where applicable

### Index

* index type
* index configuration
* construction procedure
* similarity metric

### Search

* K
* search parameters
* filtering
* metadata constraints

### Evaluation

* retrieval ground truth
* Recall@K definition
* registration benchmark
* independent check points

### Runtime

* hardware
* software environment
* offline preprocessing cost
* query-time cost
* downstream registration cost

---

# 65. Expected Research Artifacts

A future validated retrieval experiment should preserve:

* archive manifest
* query manifest
* retrieval configuration
* descriptor configuration
* index configuration
* retrieval rankings
* Recall@K results
* failure cases
* geographic overlap analysis
* downstream registration results
* runtime measurements
* benchmark summary
* reproducibility information

The exact repository storage structure should follow the project's established experiment conventions.

---

# 66. Research Experiment Progression

A recommended progression is:

```text
Experiment 1
Small archive
      ↓
Basic retrieval feasibility

Experiment 2
Controlled scale stress
      ↓
Scale robustness

Experiment 3
Illumination stress
      ↓
Appearance robustness

Experiment 4
Cross-sensor retrieval
      ↓
Sensor robustness

Experiment 5
Hard negatives
      ↓
Discriminative capability

Experiment 6
Large archive
      ↓
Search scalability

Experiment 7
Retrieval + SIFT registration
      ↓
End-to-end benefit

Experiment 8
Retrieval + learned local matching
      ↓
Hierarchical learned pipeline
```

This sequence minimizes the risk of building a complex retrieval system before its value is understood.

---

# 67. Promotion Criteria

Global retrieval should progress through the following research lifecycle:

```text
Research Idea
      ↓
Controlled Retrieval Prototype
      ↓
Retrieval Benchmark
      ↓
Stress Testing
      ↓
Failure Analysis
      ↓
Reproducibility
      ↓
Retrieval Finding
      ↓
Retrieval + Registration Experiment
      ↓
System-Level Validation
      ↓
Future Version Candidate
```

Promotion should require evidence that retrieval provides meaningful value.

Potential outcomes include:

| Outcome                                             | Research Action                              |
| --------------------------------------------------- | -------------------------------------------- |
| Strong retrieval recall and useful search reduction | Continue validation                          |
| Good recall only under metadata constraints         | Investigate hybrid retrieval                 |
| Good retrieval but poor downstream registration     | Improve local stage or representation        |
| Good retrieval only for same-sensor cases           | Treat as sensor-specific                     |
| Poor recall across difficult conditions             | Investigate representation/domain adaptation |
| Retrieval adds little over metadata                 | Defer global retrieval                       |
| Inconclusive                                        | Collect additional evidence                  |

---

# 68. What Would Justify Further Development?

Evidence supporting further development would include:

* reproducible Recall@K
* meaningful candidate-space reduction
* acceptable retrieval runtime
* preservation of downstream registration success
* robustness across representative stress categories
* useful performance across relevant sensors
* controlled handling of hard negatives
* no significant benchmark leakage
* reproducible archive/index construction

A high Recall@1 number alone is not sufficient if the archive is small or artificially easy.

---

# 69. What Would Not Justify Adoption?

Global retrieval should not be promoted based on:

* a small demonstration archive
* a single successful query
* high similarity scores without ground truth
* terrestrial retrieval benchmarks
* visually similar retrieved images without registration
* retrieval results that leak geographic metadata
* an archive containing only easy negatives
* Recall@1 without archive characterization
* faster retrieval that loses the correct region
* improved retrieval without end-to-end benefit

---

# 70. Potential Future Architecture

If validated, a future ChandraMap architecture could become:

```text
                         ┌─────────────────────┐
                         │ Lunar Image Archive │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │ Global Retrieval    │
                         │ / Candidate Search  │
                         └──────────┬──────────┘
                                    │
                                   Top-K
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │ Sensor-Aware        │
                         │ Representation      │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │ Local Features      │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │ Feature Matching    │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │ Geometric           │
                         │ Verification        │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │ Residual Analysis   │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │ Registration        │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │ Independent         │
                         │ Evaluation          │
                         └─────────────────────┘
```

This architecture remains hypothetical until validated.

---

# 71. Possible Future ChandraMap Version

Global retrieval could become a component of a future ChandraMap version if experiments demonstrate that it:

1. reliably retrieves valid candidate regions
2. reduces the local search space
3. preserves registration success
4. behaves acceptably across relevant stress categories
5. has reproducible runtime
6. has a documented ground-truth protocol
7. does not depend on uncontrolled metadata leakage

The exact version in which it could be integrated should be determined after experimental validation.

---

# 72. Limitations

Several limitations must remain explicit.

### 72.1 Archive dependence

Retrieval quality depends strongly on the archive composition.

### 72.2 Ground-truth dependence

Region-level retrieval can be difficult to label precisely when images overlap.

### 72.3 Sensor differences

Cross-sensor retrieval may be substantially harder than same-sensor retrieval.

### 72.4 Scale differences

Large physical resolution differences may remove information needed for reliable retrieval.

### 72.5 Illumination

Lunar shadows can alter global appearance substantially.

### 72.6 Domain shift

Learned retrieval models may not generalize from terrestrial imagery to lunar imagery.

### 72.7 Metadata dependence

Geographic metadata may make retrieval unnecessary or artificially easy.

### 72.8 Retrieval is not registration

Successful retrieval does not guarantee geometric registration.

---

# 73. Research Record Template

A future retrieval experiment should record:

```text
Experiment ID:
Date:
Archive Version:
Archive Size:
Query Set:
Query Sensor:
Reference Sensor:
Image / Tile Definition:
Image Representation:
Descriptor:
Descriptor Version:
Index:
Similarity Metric:
K:
Metadata Constraints:
Recall@1:
Recall@5:
Additional Recall@K:
Retrieval Runtime:
Local Registration Method:
Registration Success Rate:
Check-Point RMSE:
Total Runtime:
Failure Rate:
Failure Categories:
Hard-Negative Results:
Reproducibility Status:
Notes:
```

Values should be populated only from measured experiments.

---

# 74. Scientific Interpretation

The primary scientific question is not:

> Can ChandraMap retrieve visually similar lunar images?

The stronger question is:

> **Can retrieval reliably place geometrically useful candidate regions into a sufficiently small candidate set for downstream registration?**

The distinction matters.

For example:

```text
High visual similarity
        ↓
Wrong lunar region
        ↓
Local registration failure
```

is a retrieval failure for the end-to-end system.

Conversely:

```text
Moderate global similarity
        ↓
Correct lunar region retrieved
        ↓
Strong local correspondences
        ↓
Successful geometric registration
```

may be operationally useful even if the global similarity score is not especially high.

---

# 75. Research Principle

Global retrieval should be evaluated according to the complete scientific chain:

```text
Representation
      ↓
Retrieval
      ↓
Candidate Selection
      ↓
Local Correspondence
      ↓
Geometric Verification
      ↓
Independent Registration Evaluation
```

Every stage should have a measurable role.

No single retrieval score should replace downstream geometric validation.

---

# 76. Final Research Position

Global retrieval is a potential future extension of ChandraMap for situations where the reference image or region is not already known.

Its intended role is:

```text
Large Lunar Archive
        ↓
Reduce Search Space
        ↓
Local Correspondence
        ↓
Geometric Registration
```

The research should begin with a controlled retrieval benchmark, establish Recall@K, characterize failure modes, and determine whether retrieval meaningfully reduces the downstream search space.

Only after retrieval itself is understood should it be connected to the existing SIFT-based registration pipeline and subsequently to future learned local matching methods such as ALIKED + LightGlue.

The central principle is:

> **Global retrieval should reduce search complexity without sacrificing the ability to find a geometrically valid lunar registration.**

Until that claim is demonstrated on representative ChandraMap data with explicit ground truth, global retrieval remains a **future research direction**, not a validated ChandraMap capability.
