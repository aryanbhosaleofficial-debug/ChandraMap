# FAISS Research

> **Status:** `[Planned]`
> **Research Area:** Global image retrieval / vector similarity search / large-scale candidate discovery
> **Primary Role:** Efficient approximate or exact nearest-neighbor search over global image/tile representations
> **Registration Role:** Indirect — candidate retrieval only
> **Production Status:** `[Not implemented]`
> **Validation Status:** `[Not validated]`
> **Scope:** Future research and architecture specification

---

## 1. Overview

ChandraMap is a lunar image correspondence and registration research system focused on aligning imagery of the same lunar region acquired under different spatial resolutions, illumination conditions, acquisition geometries, and potentially different sensing modalities.

The current research direction begins with controlled image-pair registration and progressively investigates methods that can improve robustness and scalability.

A future large-scale deployment introduces a different problem:

> Given a query lunar image or tile, how can ChandraMap efficiently identify a small set of likely corresponding images or tiles from a potentially very large lunar image archive?

This is a **global image retrieval** problem.

FAISS is proposed as a future infrastructure component for this stage.

FAISS should **not** be interpreted as a registration method, feature detector, descriptor, geometric estimator, or correspondence verification algorithm.

Its role is to efficiently search a collection of numerical image representations and return the nearest candidate vectors.

The intended architecture is:

```text
Lunar Image Archive
        ↓
Image / Tile Representation
        ↓
Global Descriptor / Embedding
        ↓
Vector Index
        ↓
FAISS Similarity Search
        ↓
Top-K Candidate Images / Tiles
        ↓
Local Feature Matching
        ↓
Geometric Verification
        ↓
Transformation Estimation
        ↓
Registration
        ↓
Independent Evaluation
```

The central research question is therefore not:

> "Can FAISS register lunar images?"

Instead:

> **Can FAISS enable accurate, scalable, reproducible retrieval of likely corresponding lunar images or tiles so that expensive local matching and geometric registration are performed only on a manageable candidate set?**

---

## 2. Research Scope

This document defines the future research scope for integrating FAISS into ChandraMap's global retrieval architecture.

The research covers:

- global image retrieval
- tile retrieval
- vector representation
- similarity search
- exact nearest-neighbor search
- approximate nearest-neighbor search
- top-K candidate generation
- retrieval recall
- retrieval latency
- index size
- memory consumption
- candidate-set reduction
- retrieval robustness
- interaction with local feature registration
- cross-resolution retrieval
- cross-illumination retrieval
- cross-sensor retrieval
- reproducibility
- large-scale benchmarking

The research does **not** treat FAISS as a replacement for:

- SIFT
- ALIKED
- LightGlue
- LoFTR
- geometric verification
- RANSAC
- affine estimation
- homography estimation
- residual analysis
- independent check-point evaluation
- sub-pixel refinement

FAISS operates at a different stage of the overall system.

---

## 3. Problem Definition

### 3.1 Pairwise Registration

The initial ChandraMap problem can be represented as:

```text
Image A + Image B
        ↓
Correspondence
        ↓
Geometric Verification
        ↓
Transformation
        ↓
Registration
```

This is appropriate when the corresponding image pair is already known.

### 3.2 Large-Scale Retrieval

A large lunar archive changes the problem:

```text
Query Image
     ↓
Search Large Archive
     ↓
Find Potentially Related Images
     ↓
Select Top-K Candidates
     ↓
Run Expensive Registration
```

If the archive contains thousands, millions, or more image/tile representations, exhaustively comparing every query against every candidate can become computationally expensive.

A vector index can reduce the number of candidates passed to downstream registration.

### 3.3 Retrieval vs Registration

The distinction must remain explicit.

| Stage                  | Primary question                                     |
| ---------------------- | ---------------------------------------------------- |
| Global retrieval       | Which archive images/tiles are potentially relevant? |
| Local matching         | Which local structures correspond?                   |
| Geometric verification | Which correspondences are geometrically consistent?  |
| Registration           | What transformation aligns the images?               |
| Independent evaluation | How accurate is the estimated alignment?             |

FAISS belongs to the **global retrieval** stage.

---

## 4. What FAISS Is

FAISS is a library designed for efficient similarity search and clustering of dense numerical vectors.

Within ChandraMap, a global descriptor or embedding can represent each archive image or tile as a vector:

```text
Image / Tile
     ↓
Global Representation
     ↓
Vector
```

For example:

```text
Tile A → [x₁, x₂, x₃, ..., xₙ]
Tile B → [y₁, y₂, y₃, ..., yₙ]
Tile C → [z₁, z₂, z₃, ..., zₙ]
```

FAISS can then index those vectors and search for vectors that are similar to a query vector.

Conceptually:

```text
Query Vector
     ↓
FAISS Index
     ↓
Nearest Vectors
     ↓
Archive IDs
     ↓
Candidate Images / Tiles
```

The quality of retrieval therefore depends on more than the FAISS index itself.

It depends on the complete chain:

```text
Image Representation
        +
Embedding Quality
        +
Similarity Definition
        +
Index Configuration
        +
Search Parameters
        +
Top-K Selection
```

A poor representation cannot necessarily be corrected by a better index.

---

## 5. Role in ChandraMap

FAISS is proposed as a **retrieval infrastructure layer**.

It should provide:

1. efficient nearest-neighbor search
2. scalable candidate generation
3. configurable search accuracy/speed trade-offs
4. repeatable retrieval experiments
5. candidate ranking
6. retrieval benchmarking
7. integration with downstream registration

The expected architecture is:

```text
                         ┌──────────────────────┐
                         │ Lunar Image Archive  │
                         └──────────┬───────────┘
                                    │
                                    ▼
                         ┌──────────────────────┐
                         │ Image / Tile         │
                         │ Representation       │
                         └──────────┬───────────┘
                                    │
                                    ▼
                         ┌──────────────────────┐
                         │ Global Descriptor /  │
                         │ Embedding            │
                         └──────────┬───────────┘
                                    │
                                    ▼
                         ┌──────────────────────┐
                         │ FAISS Vector Index   │
                         └──────────┬───────────┘
                                    │
                              Top-K Candidates
                                    │
                                    ▼
                         ┌──────────────────────┐
                         │ Local Correspondence │
                         │ Pipeline             │
                         └──────────┬───────────┘
                                    │
                                    ▼
                         ┌──────────────────────┐
                         │ Geometric            │
                         │ Verification         │
                         └──────────┬───────────┘
                                    │
                                    ▼
                         ┌──────────────────────┐
                         │ Registration         │
                         └──────────┬───────────┘
                                    │
                                    ▼
                         ┌──────────────────────┐
                         │ Independent          │
                         │ Evaluation           │
                         └──────────────────────┘
```

---

## 6. Core Research Question

> **Can FAISS-based vector retrieval reduce the computational cost of large-scale lunar image correspondence while preserving sufficiently high retrieval recall for downstream geometric registration?**

### 6.1 Subquestions

The research should investigate:

1. Can global image representations reliably retrieve spatially corresponding lunar images?
2. How does retrieval performance change with archive size?
3. How does top-K size affect the probability of retrieving the correct candidate?
4. What representation provides useful retrieval behavior?
5. How does retrieval behave under resolution differences?
6. How does retrieval behave under illumination changes?
7. How does retrieval behave across different lunar instruments?
8. How does tile size affect retrieval?
9. How does image overlap affect retrieval?
10. How does exact search compare with approximate search?
11. What speed/memory trade-offs are introduced by approximate indexing?
12. Does higher retrieval recall translate into successful downstream registration?
13. How many candidates are required before registration success saturates?
14. Can retrieval reduce downstream local-matching computation without reducing registration reliability?
15. What failure modes occur when globally similar terrain is not the correct geographic location?
16. How should retrieval confidence be interpreted?
17. Can retrieval remain reproducible across hardware and software configurations?

---

## 7. Important Conceptual Distinction

FAISS does not solve the complete correspondence problem.

A global descriptor may identify images that are visually similar without establishing that individual pixels or local structures correspond.

For example:

```text
Global similarity
      ≠
Geometric correspondence
```

Two lunar regions may contain:

- similar crater structures
- similar illumination patterns
- similar terrain textures
- similar albedo distributions
- similar shadow structures

without being the same geographic location.

Therefore:

```text
FAISS retrieval
       ↓
Candidate generation
       ↓
Local correspondence
       ↓
Geometric verification
       ↓
Registration
```

must remain the conceptual architecture.

A retrieved candidate should not automatically be treated as a verified correspondence.

---

## 8. FAISS vs Other ChandraMap Methods

FAISS operates at a different abstraction level from the local correspondence methods already considered by ChandraMap.

| Method                 | Primary role                          | Representation            | Typical output                   | Main research stage  |
| ---------------------- | ------------------------------------- | ------------------------- | -------------------------------- | -------------------- |
| SIFT                   | Local feature detection + description | Local features            | Sparse keypoints/descriptors     | Local correspondence |
| ALIKED                 | Learned local feature extraction      | Learned local features    | Keypoints/descriptors            | Local correspondence |
| LightGlue              | Learned sparse feature matching       | Local descriptors         | Sparse matches                   | Local correspondence |
| LoFTR                  | Detector-free correspondence          | Image-pair representation | Dense/semi-dense correspondences | Local correspondence |
| FAISS                  | Vector similarity search              | Global vectors            | Ranked candidate IDs             | Global retrieval     |
| Global retrieval model | Image/tile representation             | Global embedding          | Query vector                     | Global retrieval     |

FAISS is therefore complementary to the local matching research.

---

## 9. FAISS Is Not a Global Descriptor

FAISS searches vectors.

It does not inherently determine what those vectors should represent.

The complete retrieval system therefore consists of at least two research components:

```text
Image
  ↓
Global Descriptor / Embedding Model
  ↓
Vector
  ↓
FAISS
  ↓
Nearest Neighbors
```

This distinction is essential.

A change in the embedding model can alter retrieval performance even when the FAISS index remains unchanged.

Similarly, changing the FAISS index can alter:

- search speed
- memory usage
- approximate-search behavior
- retrieval recall

without changing the underlying image representation.

Experiments must therefore separate:

1. representation effects
2. index effects
3. search-parameter effects
4. downstream registration effects

---

## 10. Candidate Global Representations

The initial research should not assume a single global representation.

Potential representation families include:

### 10.1 Classical Image Statistics

Examples may include:

- normalized intensity distributions
- low-dimensional image summaries
- downsampled representations
- gradient summaries
- structural summaries

These can provide simple baselines.

They should not be assumed to be sufficient for difficult cross-sensor retrieval.

### 10.2 Hand-Crafted Structural Representations

Potential representations include:

- edge maps
- gradient magnitude
- gradient orientation summaries
- texture descriptors
- multi-scale structural representations

These may be useful where raw intensity changes significantly across illumination or sensor conditions.

### 10.3 Learned Global Embeddings

Future research may investigate learned image embeddings designed for image retrieval.

The model choice must be treated as an independent research variable.

Documentation should record:

- model name
- model version
- checkpoint
- input dimensions
- preprocessing
- normalization
- output dimensionality
- training domain
- expected input modality
- inference framework

### 10.4 Lunar-Specific Embeddings

A later research stage may investigate embeddings trained or adapted for lunar imagery.

Potential training relationships include:

```text
OHRC ↔ OHRC
TMC-2 ↔ TMC-2
OHRC ↔ TMC-2
IIRS representation ↔ OHRC
IIRS representation ↔ TMC-2
```

Such models should only be considered after an appropriate dataset and ground-truth strategy are established.

---

## 11. Retrieval Pipeline

A complete future retrieval pipeline may be structured as follows:

```text
Archive Images
      ↓
Image Quality / Metadata Validation
      ↓
Tiling or Image Normalization
      ↓
Global Representation
      ↓
Embedding Generation
      ↓
Vector Normalization if Required
      ↓
FAISS Index Construction
      ↓
Index Persistence
      ↓
Query Image
      ↓
Query Embedding
      ↓
FAISS Search
      ↓
Top-K Candidate IDs
      ↓
Candidate Image Retrieval
      ↓
Local Matching
      ↓
Geometric Verification
      ↓
Registration
      ↓
Independent Check-Point Evaluation
```

---

## 12. Archive Index Construction

The archive-side pipeline should be separated from query-time processing.

### 12.1 Offline Stage

```text
Archive
  ↓
Preprocessing
  ↓
Embedding Extraction
  ↓
Vector Validation
  ↓
FAISS Index Construction
  ↓
Index Persistence
```

### 12.2 Query Stage

```text
Query Image
  ↓
Same Preprocessing
  ↓
Embedding Extraction
  ↓
FAISS Search
  ↓
Top-K Candidate IDs
```

### 12.3 Registration Stage

```text
Top-K Candidates
  ↓
Local Correspondence
  ↓
Geometric Verification
  ↓
Registration
```

This separation should make it possible to benchmark:

- index build time
- query time
- retrieval accuracy
- downstream registration time

independently.

---

## 13. Exact vs Approximate Search

A key FAISS research direction is the comparison between exact and approximate nearest-neighbor search.

### 13.1 Exact Search

An exact index can provide a reference retrieval result against which approximate methods can be evaluated.

Conceptually:

```text
Query
  ↓
Compare against complete archive
  ↓
Exact nearest neighbors
```

Exact search may become expensive as the archive grows.

### 13.2 Approximate Search

Approximate indexes attempt to reduce search cost.

The trade-off can be represented as:

```text
Search Efficiency
        ↕
Retrieval Accuracy
```

Approximate search must therefore be evaluated against an exact-search reference or another independently justified ground truth.

### 13.3 Research Requirement

Approximate retrieval should not be declared successful merely because it is faster.

The benchmark must quantify:

```text
Retrieval Accuracy
        +
Latency
        +
Memory
        +
Index Construction Cost
```

---

## 14. Top-K Candidate Selection

FAISS can return a ranked list of candidate vectors.

For a query image:

```text
Query
  ↓
FAISS
  ↓
Candidate 1
Candidate 2
Candidate 3
...
Candidate K
```

The value of `K` is a major research variable.

Potential evaluation values may include:

```text
K = 1
K = 5
K = 10
K = 20
K = 50
K = 100
```

The exact values should be determined by the benchmark design and archive size.

The important measurement is:

> At what candidate-set size is the correct registration candidate retrieved?

---

## 15. Retrieval Metrics

Retrieval must be evaluated independently from registration.

### 15.1 Recall@K

If the correct candidate is known:

```text
Recall@K =
Number of queries whose correct candidate
appears within the top K results
/
Total number of queries
```

This is one of the central retrieval metrics.

### 15.2 Precision of Candidate Set

When multiple valid candidates exist, candidate precision may be defined according to the ground-truth protocol.

The definition must be documented before experiments.

### 15.3 Mean Reciprocal Rank

If a single correct candidate is defined, the rank of that candidate can be measured.

This can help distinguish:

```text
Correct candidate at rank 1
```

from:

```text
Correct candidate at rank 100
```

### 15.4 Candidate Recall

For registration pipelines, an especially important question is:

> Does the candidate set contain at least one image that can successfully register to the query?

This can differ from strict geographic identity.

The benchmark must define the target precisely.

### 15.5 Retrieval Latency

Measure:

- embedding-generation time
- FAISS search time
- total query latency
- optional candidate-loading time

Do not combine these values without reporting their definitions.

### 15.6 Index Construction Time

Record:

- embedding extraction time
- index build time
- serialization time

### 15.7 Index Memory

Record:

- vector storage
- index storage
- runtime memory
- peak memory where available

### 15.8 Downstream Registration Cost

Measure the number of local registration attempts enabled by each candidate-set size.

The objective is not simply:

```text
FAISS is fast
```

but:

```text
FAISS reduces total search + registration cost
while preserving acceptable retrieval and registration performance.
```

---

## 16. Retrieval-to-Registration Metrics

Global retrieval should ultimately be evaluated in the context of the complete ChandraMap pipeline.

A useful evaluation structure is:

```text
Query
  ↓
Top-K Retrieval
  ↓
K Candidate Images
  ↓
Registration Attempts
  ↓
Successful Geometric Verification
  ↓
Independent Registration Evaluation
```

Relevant metrics include:

- retrieval Recall@K
- successful-registration@K
- number of registration attempts
- successful registration count
- independent checkpoint RMSE
- median error
- P90/P95 error
- maximum error
- spatial coverage
- registration success rate
- total processing time

A high retrieval score without successful registration does not establish that the retrieval pipeline solves the downstream task.

---

## 17. Ground Truth Requirements

Retrieval benchmarking requires a reliable definition of what constitutes a relevant candidate.

The project ground-truth design should be followed:

[`research/notes/ground-truth-design.md`](../notes/ground-truth-design.md)

Ground truth may need to distinguish:

```text
Exact same source image
        vs
Overlapping tile
        vs
Same geographic region
        vs
Registration-compatible candidate
        vs
Visually similar but geographically incorrect region
```

These are not interchangeable definitions.

### 17.1 Geographic Ground Truth

Where possible, candidate relevance should be established from trusted geographic or spatial metadata.

### 17.2 Registration-Based Ground Truth

A candidate may be considered valid if:

1. local correspondence succeeds,
2. geometric verification succeeds,
3. independent check-point error is within a predefined threshold.

This definition must be established before evaluating retrieval performance.

### 17.3 Independent Evaluation

The points used to establish or fit a transformation must not automatically be reused as independent accuracy measurements.

The same principle used throughout ChandraMap applies here:

```text
Fit / Control Data
        ≠
Independent Check Data
```

---

## 18. Tile-Based Retrieval

Large lunar archives may be represented as tiles rather than complete images.

A possible architecture is:

```text
Large Lunar Product
        ↓
Tiling
        ↓
Tile Embeddings
        ↓
FAISS Index
```

A query can then retrieve localized candidate regions.

### 18.1 Advantages to Investigate

Tile retrieval may:

- reduce image-size constraints
- improve local geographic specificity
- enable large archive indexing
- support regional search
- reduce downstream registration image size

### 18.2 Risks

Tiling introduces additional failure modes:

- boundary effects
- insufficient context
- duplicate neighboring tiles
- fragmented terrain
- ambiguous tile identity
- inconsistent tile preprocessing
- overlapping tiles
- varying tile sizes

Tile design must therefore be benchmarked rather than assumed to be optimal.

---

## 19. Tile Size as a Research Variable

Tile size can influence both embedding quality and retrieval performance.

Potential variables include:

- tile width
- tile height
- overlap
- aspect ratio
- scale
- contextual padding
- pyramid level

A larger tile may provide more geographic context but increase computational cost.

A smaller tile may improve localization but remove contextual information.

Therefore:

```text
Tile Size
    ↓
Context
    ↓
Embedding
    ↓
Retrieval
    ↓
Registration
```

should be evaluated empirically.

---

## 20. Multi-Scale Retrieval

Lunar images can differ substantially in spatial resolution.

A future retrieval system may therefore require multi-scale representations.

Potential architecture:

```text
Image
 ├── Scale 1 → Embedding
 ├── Scale 2 → Embedding
 ├── Scale 3 → Embedding
 └── Scale 4 → Embedding
             ↓
         FAISS Search
```

Alternatively, separate indexes could represent different scales.

The correct design should be determined experimentally.

Relevant project research:

[`research/notes/scale-invariance.md`](../notes/scale-invariance.md)

The presence of a multi-scale index does not automatically establish scale invariance.

---

## 21. Illumination Variation

Lunar imagery can exhibit substantial illumination differences.

Potential causes include:

- solar incidence angle
- shadow changes
- local terrain orientation
- acquisition timing
- sensor characteristics
- preprocessing differences

A global embedding that relies heavily on raw intensity may therefore produce unstable retrieval.

Potential representations may emphasize:

- terrain structure
- gradients
- edges
- shape
- multi-scale context

However, illumination normalization cannot be assumed to remove all geometric differences caused by changing shadows.

Relevant research:

[`research/notes/illumination-invariance.md`](../notes/illumination-invariance.md)

The benchmark should therefore include illumination-diverse cases where metadata or reliable grouping is available.

---

## 22. Cross-Sensor Retrieval

Cross-sensor retrieval is one of the most important future research directions.

Potential pairings include:

```text
OHRC ↔ TMC-2
OHRC ↔ IIRS-derived representation
TMC-2 ↔ IIRS-derived representation
```

The representation gap may be substantial.

Different sensors may differ in:

- spatial resolution
- spectral response
- radiometric characteristics
- acquisition geometry
- noise
- preprocessing
- dynamic range
- available channels

A global embedding trained for ordinary natural images should not automatically be assumed to preserve useful cross-sensor lunar similarity.

Cross-sensor performance therefore requires dedicated experiments.

---

## 23. IIRS Retrieval Research

IIRS introduces an additional representation problem because hyperspectral information is not necessarily directly compatible with a conventional 2D image embedding.

Potential IIRS-derived representations may include:

- selected spectral bands
- band combinations
- spectral composites
- PCA-derived components
- dimensionality-reduced representations
- gradient representations
- edge representations
- structural representations
- texture representations

The representation itself becomes a research variable.

Relevant project direction:

[`research/future/IIRS.md`](./IIRS.md)

The benchmark must record exactly how IIRS data is transformed into the representation supplied to the global embedding model.

---

## 24. Retrieval and Local Matching

FAISS should not replace local matching.

Instead:

```text
Global Retrieval
       ↓
Candidate Reduction
       ↓
Local Matching
       ↓
Geometric Verification
```

The local matching stage can continue to use candidate methods such as:

- SIFT
- ALIKED + LightGlue
- LoFTR
- other future correspondence methods

The retrieval layer should therefore be evaluated with more than one downstream matcher where appropriate.

---

## 25. Retrieval + SIFT

An initial integration experiment could use SIFT as the downstream registration baseline.

```text
Query
  ↓
Global Embedding
  ↓
FAISS Top-K
  ↓
SIFT
  ↓
Geometric Verification
  ↓
Registration
```

This provides a direct bridge to the existing V1 baseline.

Relevant baseline:

[`experiments/v1/baseline/EXP-001-sift-baseline/README.md`](../../experiments/v1/baseline/EXP-001-sift-baseline/README.md)

This experiment should measure whether retrieval can reduce the number of pairwise comparisons while preserving registration performance.

---

## 26. Retrieval + Learned Local Methods

Future experiments may replace the downstream SIFT stage with learned local correspondence systems.

Potential pipelines include:

```text
FAISS
  ↓
ALIKED + LightGlue
```

or:

```text
FAISS
  ↓
LoFTR
```

These should be treated as separate experimental configurations.

FAISS does not make these local methods equivalent.

Each combination should be benchmarked independently.

---

## 27. Global Retrieval vs Local Correspondence

Global and local methods solve different problems.

| Property               | Global Retrieval         | Local Correspondence           |
| ---------------------- | ------------------------ | ------------------------------ |
| Primary goal           | Candidate discovery      | Pixel/local correspondence     |
| Input                  | Usually whole image/tile | Image pair                     |
| Output                 | Ranked candidates        | Corresponding points           |
| Spatial precision      | Coarse                   | Fine                           |
| Geometric verification | Not sufficient by itself | Required downstream            |
| Registration           | Indirect                 | Directly supports registration |
| Large archive scaling  | Important                | Expensive if exhaustive        |
| Main metric            | Recall@K                 | Registration accuracy          |

A successful ChandraMap architecture may require both.

---

## 28. Candidate Reduction

The practical value of FAISS can be measured through candidate reduction.

Suppose an archive contains:

```text
N archive images
```

Without retrieval:

```text
N registration attempts
```

With top-K retrieval:

```text
K registration attempts
```

where:

```text
K << N
```

The key research question becomes:

> How much can K be reduced while maintaining acceptable probability of retrieving a registration-compatible candidate?

This should be measured rather than assumed.

---

## 29. Total-System Efficiency

FAISS should be evaluated as part of the complete system.

A simplified total query cost is:

```text
Total Cost
=
Embedding Cost
+
Vector Search Cost
+
Candidate Loading Cost
+
Local Matching Cost
+
Geometric Verification Cost
+
Registration Cost
```

Without retrieval:

```text
Total Cost
=
Pairwise Matching Cost × Archive Size
```

The research objective is to establish whether retrieval provides a meaningful reduction in total cost without unacceptable degradation in retrieval or registration quality.

---

## 30. Proposed Experiment Roadmap

All experiments in this section are proposed future work.

They are not evidence that the corresponding systems have already been implemented or validated.

---

### FAISS-EXP-001 — Vector Retrieval Pipeline Compatibility

**Research question**

Can ChandraMap generate valid global vectors and perform deterministic FAISS retrieval?

**Objective**

Establish the basic retrieval infrastructure.

**Inputs**

- controlled lunar image/tile dataset
- fixed image preprocessing
- fixed global representation

**Measure**

- vector dimensionality
- index construction success
- query success
- returned candidate IDs
- retrieval latency
- reproducibility

**Status:** `[Planned]`

---

### FAISS-EXP-002 — Exact Retrieval Baseline

**Research question**

What retrieval results are obtained using an exact nearest-neighbor baseline?

**Objective**

Create a reference against which approximate indexes can be evaluated.

**Metrics**

- Recall@1
- Recall@5
- Recall@10
- Recall@K
- query latency
- memory

**Status:** `[Planned]`

---

### FAISS-EXP-003 — Approximate Retrieval Comparison

**Research question**

How much retrieval efficiency can be gained from approximate indexing while preserving retrieval recall?

**Variables**

- index family
- search parameters
- K
- archive size

**Metrics**

- Recall@K
- latency
- memory
- index build time
- total query time

**Status:** `[Planned]`

---

### FAISS-EXP-004 — Archive Scaling

**Research question**

How does retrieval performance change as the archive grows?

**Archive sizes**

Potentially:

```text
Small
Medium
Large
Very Large
```

Exact sizes should be defined by the available benchmark dataset.

**Metrics**

- query latency
- index memory
- Recall@K
- build time
- downstream registration cost

**Status:** `[Planned]`

---

### FAISS-EXP-005 — Top-K Sensitivity

**Research question**

How does candidate-set size affect downstream registration success?

**Variables**

```text
K
```

**Metrics**

- Recall@K
- successful-registration@K
- registration attempts
- total runtime
- independent registration error

**Status:** `[Planned]`

---

### FAISS-EXP-006 — Tile Size Study

**Research question**

What tile configuration provides useful retrieval and downstream registration behavior?

**Variables**

- tile dimensions
- overlap
- context
- scale

**Metrics**

- retrieval recall
- candidate localization
- registration success
- spatial coverage
- compute cost

**Status:** `[Planned]`

---

### FAISS-EXP-007 — Scale Variation

**Research question**

How stable is global retrieval when query and archive imagery differ in spatial scale?

**Variables**

- scale ratio
- image resolution
- embedding preprocessing

**Metrics**

- Recall@K
- registration success
- registration error
- runtime

**Status:** `[Planned]`

---

### FAISS-EXP-008 — Illumination Variation

**Research question**

How does illumination variation affect global retrieval?

**Variables**

- illumination condition
- representation
- normalization

**Metrics**

- Recall@K
- rank distribution
- registration success
- failure rate

**Status:** `[Planned]`

---

### FAISS-EXP-009 — OHRC ↔ TMC-2 Retrieval

**Research question**

Can global retrieval identify geographically corresponding candidates across OHRC and TMC-2 imagery?

**Variables**

- sensor direction
- resolution difference
- representation
- tile scale

**Metrics**

- cross-sensor Recall@K
- downstream registration success
- independent checkpoint error
- runtime

**Status:** `[Planned]`

---

### FAISS-EXP-010 — IIRS-Derived Representation Retrieval

**Research question**

Which IIRS-derived 2D representations produce useful global retrieval behavior?

**Candidate representations**

- selected bands
- band combinations
- PCA components
- structural representations
- gradient representations
- spectral composites

**Status:** `[Planned]`

---

### FAISS-EXP-011 — FAISS + SIFT

**Research question**

Can FAISS reduce the number of SIFT registration attempts without degrading the registration benchmark?

**Pipeline**

```text
Query
  ↓
Global Embedding
  ↓
FAISS
  ↓
Top-K
  ↓
SIFT
  ↓
Geometric Verification
  ↓
Registration
```

**Metrics**

- Recall@K
- registration success
- checkpoint RMSE
- runtime
- number of pairwise registrations

**Status:** `[Planned]`

---

### FAISS-EXP-012 — FAISS + ALIKED + LightGlue

**Research question**

How does retrieval interact with a learned sparse local correspondence pipeline?

**Status:** `[Planned]`

Relevant future research:

[`research/future/ALIKED.md`](./ALIKED.md)

[`research/future/LIGHTGLUE.md`](./LIGHTGLUE.md)

---

### FAISS-EXP-013 — FAISS + LoFTR

**Research question**

How does global retrieval interact with detector-free correspondence?

**Pipeline**

```text
FAISS
  ↓
Top-K
  ↓
LoFTR
  ↓
Geometric Verification
  ↓
Registration
```

**Status:** `[Planned]`

Relevant future research:

[`research/future/LOFTR.md`](./LOFTR.md)

---

### FAISS-EXP-014 — Retrieval Failure Analysis

**Research question**

What types of scenes cause global retrieval to return incorrect candidates?

**Failure categories**

- repetitive terrain
- similar craters
- shadow similarity
- illumination similarity
- representation ambiguity
- cross-sensor appearance gap
- insufficient context
- tile boundary effects
- resolution mismatch

**Status:** `[Planned]`

---

### FAISS-EXP-015 — End-to-End Retrieval-to-Registration Benchmark

**Research question**

Does FAISS provide measurable system-level benefit for ChandraMap?

**Measure**

```text
Retrieval Accuracy
+
Registration Accuracy
+
Total Runtime
+
Memory
+
Candidate Reduction
```

**Status:** `[Planned]`

---

## 31. Benchmark Controls

FAISS experiments must use controlled comparisons.

At minimum, document:

- identical query set
- identical archive
- identical ground-truth definition
- identical image preprocessing
- identical embedding model
- identical vector dimensionality
- identical query normalization
- identical evaluation protocol
- identical downstream registration method
- identical hardware where possible

When comparing FAISS index configurations, only the intended index/search variable should change.

---

## 32. Retrieval Benchmark Dataset

A retrieval benchmark should contain explicit query/archive relationships.

Each sample should ideally record:

```text
query_id
archive_id
sensor
geographic relationship
spatial overlap
resolution
illumination metadata
representation
ground-truth relationship
registration compatibility
```

If metadata is unavailable, the missing information must be recorded as:

```text
[Not provided]
```

rather than inferred.

---

## 33. Negative Candidates

Retrieval evaluation should include meaningful negative examples.

A negative candidate may be:

- visually similar but geographically different
- nearby but non-overlapping
- same terrain type but different location
- same sensor but different geographic region
- different sensor with no geographic correspondence

This is important because global retrieval can confuse visual similarity with geographic identity.

---

## 34. Hard Negative Research

Hard negatives are especially relevant for lunar imagery.

Examples may include:

```text
Crater A
    vs
Crater B with similar morphology
```

or:

```text
Shadow pattern A
    vs
Similar shadow pattern B
```

A useful retrieval system should be evaluated against these difficult cases rather than only obvious negatives.

Hard-negative construction must be documented and reproducible.

---

## 35. Spatial Retrieval Evaluation

A candidate may be geographically close without being an exact tile match.

Therefore, retrieval evaluation may require multiple levels:

```text
Level 1 — Exact image/tile
Level 2 — Overlapping tile
Level 3 — Same geographic region
Level 4 — Registration-compatible candidate
Level 5 — Incorrect candidate
```

The benchmark must define which level is being measured.

Do not combine these categories without documenting the rule.

---

## 36. Similarity Metric Considerations

The vector similarity measure must be documented.

Potential similarity concepts include:

- Euclidean distance
- inner product
- cosine similarity through normalized vectors

The correct metric depends on the embedding representation and how it was trained or constructed.

The benchmark should explicitly record:

```text
metric
vector normalization
index type
search parameters
```

A change in similarity definition can invalidate a direct comparison if not controlled.

---

## 37. Vector Normalization

If the selected representation uses normalized vectors, normalization must occur consistently.

Conceptually:

```text
Archive Embedding
        ↓
Normalization
        ↓
FAISS Index

Query Embedding
        ↓
Same Normalization
        ↓
FAISS Search
```

Archive and query preprocessing must use the same documented procedure.

---

## 38. Index Persistence

A production-oriented future system should support persistence of the generated vector index.

The research artifact should distinguish:

```text
Raw Images
Embeddings
Index
Metadata
Configuration
Results
```

A vector index without the metadata required to interpret its vector IDs is not a complete research artifact.

The mapping should be explicit:

```text
FAISS Vector ID
        ↓
Archive Record ID
        ↓
Image / Tile
        ↓
Sensor / Metadata
```

---

## 39. Metadata Mapping

Every indexed vector should have a stable identifier.

Example conceptual mapping:

```text
Vector ID: 000123
Archive ID: OHRC_TILE_000123
Sensor: OHRC
Tile: ...
Acquisition: ...
Geographic metadata: ...
```

The exact schema is implementation-specific and should be defined separately when the retrieval system is implemented.

---

## 40. Determinism and Reproducibility

Retrieval experiments must record enough information to reproduce the index and query results.

Required information includes:

### Dataset

- dataset version
- image IDs
- tile IDs
- query IDs
- archive IDs

### Representation

- model name
- model version
- checkpoint
- preprocessing
- input dimensions
- output dimensionality
- normalization

### FAISS

- FAISS version
- index type
- similarity metric
- index parameters
- search parameters
- number of vectors
- vector dimensionality

### Runtime

- operating system
- Python version
- hardware
- CPU/GPU
- memory
- relevant software versions

### Downstream registration

- local matcher
- geometric model
- RANSAC settings
- evaluation points
- thresholds

---

## 41. Geometric Verification Remains Mandatory

Retrieval similarity must never be treated as geometric proof.

The downstream pipeline should remain:

```text
FAISS Candidate
       ↓
Local Correspondence
       ↓
Candidate Correspondences
       ↓
Geometric Verification
       ↓
Verified Inliers
       ↓
Transformation
       ↓
Independent Check Points
```

Relevant existing geometry research:

[`experiments/v1/geometry/EXP-004-affine-vs-homography/README.md`](../../experiments/v1/geometry/EXP-004-affine-vs-homography/README.md)

Residual analysis:

[`experiments/v1/geometry/EXP-005-residual-analysis/README.md`](../../experiments/v1/geometry/EXP-005-residual-analysis/README.md)

---

## 42. Transformation Estimation

FAISS does not estimate:

- translation
- similarity transformation
- affine transformation
- homography
- camera geometry
- lunar geospatial transformation

Those remain downstream responsibilities.

The transformation model should be selected and benchmarked independently.

No transformation model should be declared universally appropriate without evidence.

---

## 43. Independent Registration Evaluation

A retrieved candidate is useful only if it can support accurate registration.

The final evaluation should therefore include independent check points.

For a check point \(p_i\), a transformation \(T\) can produce:

$$
\hat{p}_i = T(p_i)
$$

with residual:

$$
r_i = \hat{p}_i - p_i^{gt}
$$

and residual magnitude:

$$
e_i = \|r_i\|
$$

Useful summary statistics include:

- RMSE
- median error
- P90
- P95
- maximum error
- spatial residual distribution

These should be calculated on independent evaluation data where possible.

---

## 44. Spatial Coverage

Retrieval can return a globally relevant image while downstream local matches remain concentrated in one small area.

Therefore, successful registration should consider:

- number of verified inliers
- inlier ratio
- spatial coverage
- distribution across the overlap
- independent checkpoint accuracy

A high number of matches clustered in one region does not automatically imply reliable global registration.

---

## 45. Retrieval Confidence

The similarity score returned by a vector index should not automatically be interpreted as:

```text
registration confidence
```

These are different quantities.

For example:

```text
High global similarity
        ≠
High geometric consistency
```

A future system may investigate whether retrieval scores can be calibrated against:

- geographic correctness
- downstream registration success
- geometric inlier ratio
- independent registration error

Such calibration is future research.

---

## 46. Failure Modes

The FAISS research must explicitly preserve and analyze failures.

### 46.1 Visually Similar Incorrect Region

Two lunar regions may produce similar global representations.

**Risk:**

```text
High similarity
+
Wrong geography
```

---

### 46.2 Weak Global Representation

The embedding may fail to capture stable terrain structure.

**Risk:**

Correct candidates receive low similarity.

---

### 46.3 Scale Mismatch

A representation may not remain stable when spatial resolution changes substantially.

**Risk:**

Correct candidate is ranked too low.

---

### 46.4 Illumination Change

Global appearance may change due to different illumination.

**Risk:**

Similarity decreases even for geographically corresponding regions.

---

### 46.5 Cross-Sensor Domain Gap

Different sensors may produce substantially different image statistics.

**Risk:**

Embedding similarity does not preserve geographic correspondence.

---

### 46.6 Repetitive Terrain

Similar craters or terrain structures may create ambiguous retrieval.

**Risk:**

Multiple incorrect candidates rank highly.

---

### 46.7 Tile Boundary Effects

A query may overlap multiple archive tiles.

**Risk:**

No individual tile contains sufficient context.

---

### 46.8 Poor Candidate K

If K is too small:

```text
Correct candidate excluded
```

If K is too large:

```text
Registration computation increases
```

---

### 46.9 Embedding Model Domain Shift

A general-purpose embedding may not represent lunar imagery appropriately.

**Risk:**

Poor retrieval despite a technically correct FAISS configuration.

---

### 46.10 False Confidence

A high vector similarity score can appear convincing while the candidate is geographically incorrect.

**Mitigation:**

Require downstream geometric verification.

---

### 46.11 Index Configuration Sensitivity

Approximate indexes may change retrieval behavior as search parameters change.

**Mitigation:**

Record and benchmark all relevant parameters.

---

### 46.12 Ground-Truth Ambiguity

A retrieval candidate may be geographically related but not identical to the designated reference.

**Mitigation:**

Define candidate relevance before evaluation.

---

## 47. Large-Scale Search Considerations

As the archive grows, the retrieval architecture should be evaluated along multiple dimensions.

### Data Scale

```text
Number of images
Number of tiles
Number of vectors
Vector dimensionality
```

### Computational Scale

```text
Embedding generation
Index construction
Search latency
Downstream matching
```

### Storage Scale

```text
Raw imagery
Embeddings
Index
Metadata
Results
```

The benchmark should report these independently.

---

## 48. Memory Considerations

Vector dimensionality directly influences storage requirements.

A simplified conceptual relationship is:

```text
Vector Storage
∝
Number of Vectors × Vector Dimensionality × Bytes per Element
```

Additional index structures can introduce further memory overhead.

Therefore, benchmark reports should include:

- number of indexed vectors
- vector dimensionality
- data type
- index size
- runtime memory
- peak memory where measurable

Exact resource requirements should not be claimed before measurement.

---

## 49. GPU and CPU Evaluation

FAISS can be evaluated under different execution environments where supported.

The benchmark should distinguish:

```text
CPU retrieval
```

from:

```text
GPU retrieval
```

and report:

- hardware
- precision
- batch size
- index configuration
- query count
- warm-up procedure
- timing methodology

Runtime comparisons across different hardware should not be presented as directly equivalent without qualification.

---

## 50. Batch Query Evaluation

Single-query latency and batch throughput represent different measurements.

The benchmark may therefore report:

### Single Query

```text
Time / query
```

### Batch Retrieval

```text
Queries / second
```

Both can be useful depending on the intended ChandraMap deployment scenario.

---

## 51. Index Build vs Query Cost

A retrieval system should distinguish one-time or infrequent costs from per-query costs.

```text
Archive Preparation
    ├── Embedding generation
    └── Index construction

Query
    ├── Query embedding
    └── FAISS search
```

A method with expensive index construction may still be useful for a static archive, while a frequently changing archive introduces a different engineering trade-off.

The intended archive-update frequency should therefore be recorded when evaluating deployment scenarios.

---

## 52. Archive Updates

Future research may need to investigate:

- adding new lunar tiles
- rebuilding indexes
- incremental updates where supported
- removing obsolete records
- metadata synchronization
- index versioning

An index should be associated with an explicit archive snapshot.

---

## 53. Versioning

A reproducible retrieval index should have a version identifier.

Conceptually:

```text
Archive Version
+
Embedding Version
+
Preprocessing Version
+
FAISS Index Configuration
=
Retrieval Index Version
```

This prevents results from becoming ambiguous when the underlying archive or embedding model changes.

---

## 54. Experiment Matrix

A future benchmark can use a matrix such as:

| Factor         | Example levels                  |
| -------------- | ------------------------------- |
| Sensor         | OHRC / TMC-2 / IIRS-derived     |
| Direction      | Same-sensor / cross-sensor      |
| Resolution     | Matched / mismatched            |
| Illumination   | Similar / different             |
| Representation | Classical / learned             |
| Index          | Exact / approximate             |
| K              | Multiple benchmark values       |
| Tile size      | Multiple benchmark values       |
| Matcher        | SIFT / ALIKED+LightGlue / LoFTR |
| Geometry       | Candidate models                |
| Evaluation     | Retrieval + registration        |

The exact levels must be determined from the available dataset.

---

## 55. Controlled Ablation Studies

FAISS research should use ablations to determine which component contributes to observed behavior.

Potential ablations:

```text
Embedding A vs Embedding B
```

```text
Exact vs Approximate Index
```

```text
K = 1 vs K = 10 vs K = 50
```

```text
Whole Image vs Tiles
```

```text
Raw Representation vs Structural Representation
```

```text
Single Scale vs Multi-Scale
```

```text
Same Sensor vs Cross Sensor
```

Each comparison should change one major variable at a time wherever practical.

---

## 56. Retrieval Baseline Without FAISS

A meaningful FAISS evaluation requires a reference.

A simple baseline may perform direct exhaustive similarity comparison over the archive:

```text
Query Vector
      ↓
Compare Against All Archive Vectors
      ↓
Rank Similarities
```

This provides a conceptual exact-search reference.

The comparison should measure:

- retrieval equivalence
- latency
- memory
- scalability

The objective is not to assume that FAISS is automatically superior, but to measure the trade-off introduced by the selected index architecture.

---

## 57. Downstream Candidate Filtering

FAISS retrieval can be combined with metadata filtering in future research.

Potential metadata constraints may include:

- sensor
- acquisition period
- geographic region
- spatial resolution
- product type
- known geographic bounds

However, metadata filtering changes the search problem and must be documented.

The benchmark should distinguish:

```text
Pure vector retrieval
```

from:

```text
Metadata-filtered vector retrieval
```

---

## 58. Hierarchical Retrieval

A future architecture may use multiple retrieval stages:

```text
Archive
  ↓
Coarse Global Retrieval
  ↓
Regional Candidate Set
  ↓
Fine Global Retrieval
  ↓
Top-K
  ↓
Local Registration
```

Potential benefits include reducing the search space before expensive fine retrieval.

Such a system should be benchmarked against a simpler single-stage retrieval pipeline.

---

## 59. Geographic Hierarchical Retrieval

Lunar imagery may eventually be organized by geographic regions.

A conceptual architecture is:

```text
Lunar Archive
     ↓
Region Retrieval
     ↓
Tile Retrieval
     ↓
Local Matching
```

This should not be assumed to be superior.

It is an architectural hypothesis requiring measurement.

---

## 60. Relationship to GLOBAL_RETRIEVAL.md

FAISS is an indexing/search infrastructure component within the broader global retrieval research direction.

Relevant research specification:

[`research/future/GLOBAL_RETRIEVAL.md`](./GLOBAL_RETRIEVAL.md)

The global retrieval document should define the broader retrieval problem, while this document focuses specifically on the vector indexing/search layer.

The separation should remain:

```text
GLOBAL_RETRIEVAL.md
        ↓
Retrieval System Research

FAISS.md
        ↓
Vector Search / Indexing Research
```

---

## 61. Relationship to RIFT / CFOG

Classical structural descriptors may provide potential image representations for retrieval experiments.

Relevant research:

[`research/future/RIFT_CFOG.md`](./RIFT_CFOG.md)

However, a descriptor designed for local matching should not automatically be treated as a suitable global embedding.

Its use for global retrieval must be experimentally justified.

---

## 62. Research Boundaries

The following are outside the immediate scope unless explicitly introduced as future experiments:

- replacing geometric verification with FAISS
- treating vector similarity as registration accuracy
- claiming geographic correctness from similarity score alone
- assuming one global embedding works across all sensors
- assuming exact retrieval guarantees successful registration
- assuming approximate search preserves recall without measurement
- assuming a specific index configuration is optimal
- treating retrieval speed as sufficient evidence of usefulness
- using undocumented external datasets as ground truth

---

## 63. Evidence Required for Adoption

FAISS should only become a ChandraMap architecture component after evidence addresses the following questions.

### Retrieval Quality

- Does the correct candidate appear within an acceptable K?
- Is retrieval stable across representative lunar scenes?
- Does retrieval work under relevant resolution differences?
- Does retrieval remain useful under illumination variation?
- Does cross-sensor retrieval work sufficiently for the intended use case?

### Registration Utility

- Does candidate retrieval improve or preserve downstream registration success?
- Does it reduce the number of registration attempts?
- Does it reduce total computation?
- Does independent registration accuracy remain acceptable?

### Engineering

- Is index construction practical?
- Is query latency acceptable?
- Is memory usage acceptable?
- Can indexes be versioned and reproduced?
- Can archive updates be handled?

### Scientific Validity

- Is the ground truth explicit?
- Are independent evaluation points used?
- Are hard negatives included?
- Are failures preserved?
- Are results reproducible?

---

## 64. Adoption Criteria

No single metric should determine adoption.

Evidence should be evaluated across:

```text
Retrieval Recall
+
Registration Success
+
Independent Registration Accuracy
+
Candidate Reduction
+
Runtime
+
Memory
+
Reproducibility
+
Failure Behavior
```

A method that improves one dimension while severely degrading another should be documented as a trade-off rather than described as universally better.

---

## 65. Suggested Status Labels

Research documentation should use explicit status labels.

| Status                       | Meaning                                                 |
| ---------------------------- | ------------------------------------------------------- |
| `[Exploratory]`              | Initial research idea                                   |
| `[Proposed]`                 | Defined future experiment                               |
| `[Planned]`                  | Scheduled for future implementation                     |
| `[Experiment Ready]`         | Dataset/protocol sufficiently defined                   |
| `[Under Evaluation]`         | Experiment currently being measured                     |
| `[Validated]`                | Evidence supports the documented claim                  |
| `[Implementation Candidate]` | Evidence supports further engineering consideration     |
| `[Implemented]`              | Implemented in the repository                           |
| `[Deferred]`                 | Deliberately postponed                                  |
| `[Rejected]`                 | Investigated and not selected for the documented reason |

This document currently remains:

> `[Planned]`

---

## 66. Reproducibility Checklist

Every completed FAISS experiment should record:

### Dataset

- [ ] Dataset version
- [ ] Query image IDs
- [ ] Archive image/tile IDs
- [ ] Ground-truth relationships
- [ ] Sensor metadata
- [ ] Resolution metadata
- [ ] Illumination metadata where available

### Preprocessing

- [ ] Image resizing
- [ ] Cropping
- [ ] Tiling
- [ ] Tile overlap
- [ ] Intensity normalization
- [ ] Channel conversion
- [ ] Representation generation
- [ ] Vector normalization

### Embedding

- [ ] Model name
- [ ] Model version
- [ ] Checkpoint
- [ ] Input size
- [ ] Output dimension
- [ ] Inference configuration

### FAISS

- [ ] FAISS version
- [ ] Index type
- [ ] Similarity metric
- [ ] Index parameters
- [ ] Search parameters
- [ ] Top-K
- [ ] Number of vectors

### Runtime

- [ ] CPU/GPU
- [ ] Hardware
- [ ] Software versions
- [ ] Batch size
- [ ] Precision
- [ ] Timing methodology

### Registration

- [ ] Local matcher
- [ ] Matching configuration
- [ ] Geometric model
- [ ] RANSAC configuration
- [ ] Independent check points
- [ ] Error thresholds

### Outputs

- [ ] Retrieval rankings
- [ ] Similarity scores
- [ ] Candidate IDs
- [ ] Registration results
- [ ] Inlier masks
- [ ] Transformation parameters
- [ ] Residuals
- [ ] Runtime
- [ ] Memory
- [ ] Failure cases

---

## 67. Expected Research Artifacts

A completed FAISS research implementation should produce reproducible artifacts such as:

```text
Experiment README
        ↓
Configuration
        ↓
Dataset Manifest
        ↓
Embedding Manifest
        ↓
FAISS Index
        ↓
Metadata Mapping
        ↓
Retrieval Results
        ↓
Registration Results
        ↓
Evaluation Tables
        ↓
Failure Analysis
        ↓
Reproducibility Manifest
```

Potential output files may include:

- retrieval rankings
- similarity scores
- candidate IDs
- Recall@K tables
- latency reports
- memory reports
- index metadata
- embedding metadata
- registration metrics
- residual plots
- qualitative retrieval visualizations
- failure-case visualizations

Exact filenames should be defined when implementation begins.

---

## 68. Recommended Result Table

A future experiment should report at least:

| Configuration | Recall@1 | Recall@5 | Recall@10 | Recall@K | Query Time | Index Size | Registration Success | Check-Point RMSE |
| ------------- | -------: | -------: | --------: | -------: | ---------: | ---------: | -------------------: | ---------------: |
| `[TBD]`       |  `[TBD]` |  `[TBD]` |   `[TBD]` |  `[TBD]` |    `[TBD]` |    `[TBD]` |              `[TBD]` |          `[TBD]` |

Values must only be populated from measured experiments.

---

## 69. Recommended End-to-End Result Table

For system-level evaluation:

| Retrieval Configuration | Archive Size |       K | Retrieval Recall | Registration Attempts | Successful Registrations | Independent RMSE | Total Runtime |
| ----------------------- | -----------: | ------: | ---------------: | --------------------: | -----------------------: | ---------------: | ------------: |
| `[TBD]`                 |      `[TBD]` | `[TBD]` |          `[TBD]` |               `[TBD]` |                  `[TBD]` |          `[TBD]` |       `[TBD]` |

This table should make the relationship between retrieval and registration explicit.

---

## 70. Failure Analysis Template

Each significant retrieval failure should document:

```text
Query:
Archive:
Sensor:
Representation:
Index:
Top-K:
Correct Candidate Rank:
Top Retrieved Candidate:
Similarity:
Ground-Truth Relationship:
Downstream Registration:
Geometric Verification:
Independent Error:
Failure Category:
Likely Cause:
Evidence:
Possible Mitigation:
```

The purpose is to identify systematic limitations rather than simply remove failed examples.

---

## 71. Scientific Interpretation Rules

The following interpretation rules should be maintained.

### Rule 1

High Recall@K does not prove accurate registration.

### Rule 2

Low retrieval recall may indicate a representation problem rather than an indexing problem.

### Rule 3

Approximate-search speed improvements must be reported together with retrieval accuracy.

### Rule 4

A large number of retrieved candidates does not establish geographic correctness.

### Rule 5

A high global similarity score is not a geometric verification result.

### Rule 6

Registration success must be evaluated independently of candidate ranking.

### Rule 7

Cross-sensor performance must be measured rather than inferred from same-sensor performance.

### Rule 8

Hard negative failures are scientifically informative and must be preserved.

### Rule 9

Index and embedding changes must be tracked independently.

### Rule 10

Unmeasured results must remain marked as `[TBD]`.

---

## 72. Potential Future Extensions

Once the basic retrieval pipeline is validated, future research may investigate:

### 72.1 Learned Lunar Retrieval Embeddings

Train or adapt global representations specifically for lunar imagery.

### 72.2 Cross-Sensor Embeddings

Learn a shared representation across:

```text
OHRC
TMC-2
IIRS-derived representations
```

### 72.3 Multi-Scale Embeddings

Represent the same region at multiple spatial scales.

### 72.4 Multi-Representation Retrieval

Combine multiple embeddings:

```text
Intensity
+
Gradient
+
Structural
+
Learned
```

### 72.5 Retrieval Fusion

Combine ranking signals from multiple retrieval systems.

### 72.6 Retrieval + Geospatial Constraints

Use geographic metadata to reduce impossible candidates.

### 72.7 Retrieval + Temporal Metadata

Where acquisition metadata supports it, investigate whether temporal information improves candidate filtering.

### 72.8 Hierarchical Search

Use:

```text
Global
→ Regional
→ Tile
→ Local
```

retrieval stages.

### 72.9 Retrieval-Aware Registration

Use retrieval information to adapt downstream registration strategy while preserving independent evaluation.

---

## 73. Potential Hybrid Architecture

A future ChandraMap system may combine several research directions:

```text
                         Lunar Archive
                              │
                              ▼
                    Global Representation
                              │
                              ▼
                         FAISS Index
                              │
                           Top-K
                              │
             ┌────────────────┼────────────────┐
             │                │                │
             ▼                ▼                ▼
           SIFT       ALIKED + LightGlue     LoFTR
             │                │                │
             └────────────────┼────────────────┘
                              ▼
                    Geometric Verification
                              │
                              ▼
                    Transformation Estimate
                              │
                              ▼
                    Independent Evaluation
```

This architecture preserves separation between:

- global retrieval
- local correspondence
- geometric verification
- registration
- evaluation

---

## 74. Relationship to V1

ChandraMap V1 establishes the controlled pairwise registration and evaluation foundation.

The FAISS research should build on those principles rather than bypass them.

In particular, V1 establishes the importance of:

- controlled experiments
- explicit transformations
- geometric verification
- residual analysis
- independent evaluation
- reproducibility
- failure preservation

FAISS introduces a new stage:

```text
Archive Candidate Discovery
```

It does not replace the established registration evaluation methodology.

---

## 75. Recommended Research Sequence

A disciplined implementation sequence is:

```text
1. Define retrieval ground truth
        ↓
2. Establish global representation baseline
        ↓
3. Establish exact-search reference
        ↓
4. Validate FAISS integration
        ↓
5. Measure Recall@K
        ↓
6. Introduce approximate indexing
        ↓
7. Measure speed/memory trade-offs
        ↓
8. Evaluate tile retrieval
        ↓
9. Evaluate scale variation
        ↓
10. Evaluate illumination variation
        ↓
11. Evaluate cross-sensor retrieval
        ↓
12. Connect retrieval to SIFT baseline
        ↓
13. Connect retrieval to learned local methods
        ↓
14. Evaluate end-to-end registration
        ↓
15. Analyze failures
        ↓
16. Determine implementation candidacy
```

This sequence should be adapted if experimental evidence indicates that an earlier assumption is invalid.

---

## 76. Implementation Boundary

This document does not prescribe a final FAISS index type.

The implementation should select the index configuration only after:

1. defining the retrieval benchmark,
2. establishing the exact-search reference,
3. measuring archive scale,
4. measuring vector dimensionality,
5. defining latency requirements,
6. defining memory constraints,
7. evaluating retrieval recall.

This avoids selecting an index architecture based solely on theoretical expectations.

---

## 77. Current Implementation Status

| Component                       | Status              |
| ------------------------------- | ------------------- |
| FAISS integration               | `[Not implemented]` |
| Global embedding pipeline       | `[Not implemented]` |
| Archive indexing pipeline       | `[Not implemented]` |
| Query retrieval pipeline        | `[Not implemented]` |
| Exact retrieval benchmark       | `[Not validated]`   |
| Approximate retrieval benchmark | `[Not validated]`   |
| Tile retrieval benchmark        | `[Not validated]`   |
| Cross-sensor retrieval          | `[Not validated]`   |
| Retrieval + SIFT                | `[Not validated]`   |
| Retrieval + ALIKED + LightGlue  | `[Not validated]`   |
| Retrieval + LoFTR               | `[Not validated]`   |
| Production integration          | `[Not implemented]` |

No performance claim should be inferred from this document.

---

## 78. Open Research Questions

The following questions remain open:

1. What global representation is sufficiently stable for lunar terrain?
2. Should the representation be classical, learned, or hybrid?
3. How much cross-resolution variation can be tolerated?
4. How much illumination variation can be tolerated?
5. What representation works across OHRC and TMC-2?
6. How should IIRS hyperspectral information be converted into a retrieval-compatible representation?
7. What top-K is sufficient for downstream registration?
8. How does archive size affect retrieval behavior?
9. Which exact-search baseline is appropriate?
10. Which approximate index provides an acceptable speed/accuracy trade-off?
11. How should tile size be selected?
12. How much overlap between tiles is necessary?
13. Can global retrieval reliably reject visually similar but geographically incorrect regions?
14. How should retrieval confidence be calibrated?
15. Can retrieval significantly reduce end-to-end registration cost?
16. Does retrieval quality remain stable across lunar terrain types?
17. How should hard negatives be constructed?
18. How should changing archives be indexed and versioned?
19. Can global retrieval and local registration be jointly optimized?
20. What evidence is sufficient to justify production integration?

---

## 79. Adoption Decision Framework

The final decision should be evidence-based rather than based on implementation convenience.

Before adopting FAISS into the main ChandraMap architecture, evaluate:

### Scientific

- [ ] Retrieval ground truth is explicit.
- [ ] Retrieval Recall@K is measured.
- [ ] Hard negatives are included.
- [ ] Cross-condition performance is evaluated.
- [ ] Cross-sensor performance is evaluated where relevant.
- [ ] Downstream registration is independently evaluated.
- [ ] Failures are documented.

### Engineering

- [ ] Index construction is reproducible.
- [ ] Index versioning is defined.
- [ ] Query latency is measured.
- [ ] Memory use is measured.
- [ ] Archive scaling is measured.
- [ ] Metadata mapping is reliable.
- [ ] Software versions are recorded.

### System-Level

- [ ] Candidate reduction is demonstrated.
- [ ] Registration computation is reduced or otherwise justified.
- [ ] Registration accuracy remains acceptable.
- [ ] End-to-end behavior is reproducible.
- [ ] Failure modes are understood sufficiently for the intended use case.

Only after these conditions are investigated should FAISS be considered an implementation candidate.

---

## 80. Related ChandraMap Research

### Core Research

[`research/README.md`](../README.md)

### Future Research Index

[`research/future/README.md`](./README.md)

### Global Retrieval

[`research/future/GLOBAL_RETRIEVAL.md`](./GLOBAL_RETRIEVAL.md)

### IIRS

[`research/future/IIRS.md`](./IIRS.md)

### ALIKED

[`research/future/ALIKED.md`](./ALIKED.md)

### LightGlue

[`research/future/LIGHTGLUE.md`](./LIGHTGLUE.md)

### LoFTR

[`research/future/LOFTR.md`](./LOFTR.md)

### RIFT / CFOG

[`research/future/RIFT_CFOG.md`](./RIFT_CFOG.md)

### Scale Invariance

[`research/notes/scale-invariance.md`](../notes/scale-invariance.md)

### Illumination Invariance

[`research/notes/illumination-invariance.md`](../notes/illumination-invariance.md)

### Ground-Truth Design

[`research/notes/ground-truth-design.md`](../notes/ground-truth-design.md)

### V1 Experiments

[`experiments/v1/README.md`](../../experiments/v1/README.md)

### SIFT Baseline

[`experiments/v1/baseline/EXP-001-sift-baseline/README.md`](../../experiments/v1/baseline/EXP-001-sift-baseline/README.md)

### Geometry Comparison

[`experiments/v1/geometry/EXP-004-affine-vs-homography/README.md`](../../experiments/v1/geometry/EXP-004-affine-vs-homography/README.md)

### Residual Analysis

[`experiments/v1/geometry/EXP-005-residual-analysis/README.md`](../../experiments/v1/geometry/EXP-005-residual-analysis/README.md)

---

## 81. References

### FAISS

Johnson, J., Douze, M., and Jégou, H.
**Billion-scale similarity search with GPUs.**
IEEE Transactions on Big Data, 2019.

This work provides foundational background for large-scale vector similarity search and the FAISS system.

### ChandraMap Research Context

The FAISS research direction should be interpreted together with the ChandraMap research documentation covering:

- lunar image registration
- scale variation
- illumination variation
- ground-truth design
- global retrieval
- local correspondence
- geometric verification
- residual analysis
- cross-sensor research

---

## 82. Final Research Position

FAISS should be treated as a **scalable vector-search infrastructure component** within a future ChandraMap global retrieval architecture.

Its role is:

```text
Large Archive
     ↓
Efficient Vector Search
     ↓
Candidate Discovery
```

Its role is not:

```text
Vector Search
     ↓
Automatic Registration
```

The complete research architecture remains:

```text
Lunar Image Archive
        ↓
Image / Tile Representation
        ↓
Global Descriptor / Embedding
        ↓
FAISS Similarity Search
        ↓
Top-K Candidate Images / Tiles
        ↓
Local Feature Matching
        ↓
Geometric Verification
        ↓
Transformation Estimation
        ↓
Independent Check-Point Evaluation
        ↓
Final Registration
```

The key evidence required is therefore not simply that FAISS can search vectors efficiently.

The research must establish whether vector retrieval can **reliably identify registration-compatible lunar candidates at a sufficiently low computational cost**, while preserving reproducibility, measurable retrieval quality, geometric validity, and independent registration accuracy.

**Current status:** `[Planned]`
**Implementation:** `[Not implemented]`
**Validation:** `[Not validated]`
