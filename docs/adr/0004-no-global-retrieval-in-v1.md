# ADR-0004: No Global Retrieval in V1

> **Decision summary:** ChandraMap V1 will not implement or require whole-Moon/global image retrieval. V1 accepts a known-overlap or externally constrained reference region and concentrates on local correspondence, geometric verification, registration, and independent evaluation. Global retrieval is deferred to a later independently benchmarkable architecture.

---

## Metadata

- **ADR:** ADR-0004
- **Title:** No Global Retrieval in V1
- **Status:** Accepted
- **Date:** `<YYYY-MM-DD>`
- **Decision Scope:** V1
- **Architecture Area:** Global Retrieval / Candidate Selection Boundary
- **Sensor Scope:** OHRC, TMC-2, IIRS, LRO reference imagery
- **Supersedes:** N/A
- **Superseded By:** N/A
- **Related ADRs:** ADR-0001, ADR-0002, ADR-0003
- **Related Issues:** N/A
- **Related PRs:** N/A
- **Related Experiments:** TBD
- **Related Benchmarks:** V1 registration benchmark; future retrieval benchmark TBD

---

## Status

**Accepted**

For ChandraMap V1, global lunar image retrieval is intentionally outside the canonical architecture.

`Accepted` in this ADR means:

- V1 does not require whole-Moon candidate discovery;
- V1 does not require global image embeddings;
- V1 does not require FAISS or another vector-search system;
- V1 does not require a global reference index;
- V1 does not require retrieval-specific metrics;
- known-overlap and metadata-constrained candidate regions are permitted;
- local multi-scale processing remains permitted;
- retrieval experiments may exist outside the canonical V1 path.

It does **not** mean:

- global localization is permanently rejected;
- FAISS is forbidden from ChandraMap;
- retrieval research cannot occur during V1 development;
- metadata will always be available;
- later scientific versions cannot search a global lunar corpus.

---

## Context

ChandraMap is a lunar image correspondence and registration system intended to establish reliable geometric relationships between observations of the same lunar surface region captured under different:

- instruments;
- spatial resolutions;
- Ground Sample Distances (GSDs);
- Sun angles;
- shadow geometries;
- viewing geometries;
- sensor modalities;
- radiometric conditions;
- product/projection states.

Relevant Chandrayaan-2 source instruments include:

- **OHRC** — Orbiter High Resolution Camera;
- **TMC-2** — Terrain Mapping Camera-2;
- **IIRS** — Imaging Infrared Spectrometer.

Reference imagery may include:

- **LRO NAC**;
- **LRO WAC**.

Approximate instrument-level context includes:

| Instrument | Approximate Context                                   | Architectural Relevance                       |
| ---------- | ----------------------------------------------------- | --------------------------------------------- |
| OHRC       | ~0.25–0.32 m/pixel depending on product/documentation | Very high-detail visible/panchromatic imagery |
| TMC-2      | ~5 m/pixel                                            | Broader panchromatic terrain structure        |
| IIRS       | ~80 m/pixel; hyperspectral/infrared                   | Cross-modal representation problem            |
| LRO NAC    | High-resolution lunar imagery; product scale varies   | Fine reference imagery                        |
| LRO WAC    | Broader-scale lunar imagery                           | Regional/global context                       |

These values are contextual rather than universal product specifications. Product metadata should govern specific experiments.

The sensors above must not be treated as equivalent ordinary grayscale cameras.

---

## Problem

ChandraMap contains two separate technical problems.

### Problem A — Local Correspondence and Registration

Given source and reference imagery already known or expected to overlap:

> **Where are the corresponding image locations, and what geometric transformation aligns the observations?**

Conceptually:

```text
Known / Constrained Candidate Region
        ↓
Sensor-Aware Preparation
        ↓
Scale Handling
        ↓
Local Correspondence
        ↓
Geometric Verification
        ↓
Registration
        ↓
Independent Evaluation
```

### Problem B — Global Retrieval / Localization

Given a source image whose correct reference location is not already known:

> **Which lunar region should be passed to the local correspondence pipeline?**

Conceptually:

```text
Unknown-Location Source
        ↓
Global Image Representation
        ↓
Search Large Lunar Reference Corpus
        ↓
Rank Candidate Regions
        ↓
Top-K Candidates
        ↓
Local Registration
```

Problem B eventually depends on Problem A.

If ChandraMap retrieves the correct region but cannot reliably register it, the end-to-end system still fails.

ADR-0004 therefore keeps the two problems architecturally and scientifically separate.

---

## Decision Drivers

This decision is driven by the following requirements.

### Scientific measurability

V1 must establish whether ChandraMap can perform measurable local registration before search complexity is added.

### Failure isolation

A failed V1 result should be diagnosable in terms of:

- preprocessing;
- physical scale preparation;
- correspondence generation;
- match filtering;
- geometric verification;
- transform estimation;
- refinement;
- registration;
- evaluation.

It should not initially require the additional question:

> Was the reference candidate itself wrong?

### Benchmark clarity

Registration metrics and retrieval metrics answer different scientific questions.

The V1 benchmark should evaluate registration, not mix retrieval quality with registration quality.

### Reproducibility

A known or constrained reference region produces a more controlled first benchmark than an evolving global retrieval system.

### Architectural modularity

A future retrieval engine should be able to produce a candidate reference region and pass it into the same local-registration core.

### Scope control

Whole-Moon retrieval introduces substantial additional infrastructure and research uncertainty that is not required to prove the local correspondence problem.

### Single-maintainer practicality

ChandraMap is currently a focused open-source research project. V1 should remain small enough to understand, reproduce, benchmark, and maintain.

---

## Terminology

### Local Registration

Local registration begins after a plausible source/reference overlap has already been identified.

It answers:

> **How do these two overlapping observations align?**

Typical responsibilities include:

- preprocessing;
- scale preparation;
- local feature/correspondence generation;
- match filtering;
- robust geometric verification;
- transformation estimation;
- optional tie-point refinement;
- final transformation refitting;
- registration;
- independent evaluation.

---

### Global Retrieval

For ChandraMap, **global retrieval** means receiving a source observation whose correct lunar reference location is not already known and searching a large reference corpus for likely candidate regions.

Conceptually:

```text
Unknown Source Image
        ↓
Global Descriptor
        ↓
Large Reference Search
        ↓
Ranked Candidate Regions
        ↓
Top-K Candidate Set
```

Global retrieval identifies **where to look**.

Local registration determines **how the images align**.

---

### Metadata-Constrained Candidate Selection

Metadata-constrained candidate selection uses reliable existing information such as:

- latitude;
- longitude;
- image footprint;
- map projection;
- spacecraft/product geometry;
- approximate region;
- known product association.

Conceptually:

```text
Source Product
        ↓
Reliable Coordinates / Footprint
        ↓
Restrict Reference Region
        ↓
Local Registration
```

This is allowed in V1.

It is **not** classified as whole-Moon global image retrieval.

---

### Candidate Region Provider

A **candidate region provider** is the conceptual upstream mechanism that supplies a possible reference region to the local-registration pipeline.

In V1 it may be:

```text
Known Benchmark Pair
        ↓
Candidate Region Provider
        ↓
Local Registration
```

or:

```text
Metadata Constraint
        ↓
Candidate Region Provider
        ↓
Local Registration
```

A future architecture may use:

```text
Global Retrieval Engine
        ↓
Candidate Region Provider
        ↓
Local Registration
```

No concrete software interface is defined by this ADR.

---

## Relationship to Existing ADRs

### ADR-0001 — V1 Known-Overlap First

[ADR-0001](0001-v1-known-overlap-first.md) answers:

> **Where does V1 begin?**

Its answer is:

> **With known-overlap source/reference imagery.**

ADR-0004 answers the corresponding negative architectural question:

> **Does canonical V1 include a whole-Moon/global candidate-retrieval subsystem?**

Its answer is:

> **No.**

The distinction is:

```text
ADR-0001
Defines the positive V1 input boundary
        ↓
Known / Constrained Overlap
        ↓
ADR-0004
Defines what remains outside that boundary
        ↓
No Global Retrieval Requirement
```

ADR-0001 and ADR-0004 are therefore complementary rather than duplicates.

---

### ADR-0002 — SIFT as the V1 Baseline

[ADR-0002](0002-sift-as-v1-baseline.md) establishes the classical local-matching baseline.

Conceptually:

```text
Prepared Known-Overlap Pair
        ↓
SIFT
        ↓
Descriptor Matching
        ↓
Candidate Matches
        ↓
Geometric Verification
```

ADR-0004 clarifies what supplies that pair.

The V1 SIFT baseline receives:

```text
Known / Externally Constrained Candidate Pair
```

not:

```text
Automatically Retrieved Whole-Moon Candidate
```

Therefore the V1 matcher benchmark measures local correspondence behavior rather than global retrieval quality.

---

### ADR-0003 — Independent Checkpoints

[ADR-0003](0003-independent-checkpoints.md) establishes independent checkpoint evaluation.

Conceptually:

```text
Fit Tie Points
        ↓
Estimate Transform

Independent Checkpoints
        ↓
Evaluate Final Transform
```

ADR-0004 prevents global-retrieval errors from contaminating the first registration benchmark.

The V1 scientific question is:

```text
Given the Correct / Constrained Region
        ↓
How Well Does Registration Perform?
```

A future retrieval benchmark asks:

```text
Given an Unknown Location
        ↓
Can the System Find the Correct Region?
```

These are separate questions with separate metric families.

---

## Considered Options

### Option A — No Global Retrieval in V1

#### Description

Canonical V1 receives known-overlap or metadata-constrained candidate regions and focuses exclusively on local registration and evaluation.

#### Advantages

- smaller architecture;
- lower implementation complexity;
- clearer scientific scope;
- easier debugging;
- cleaner failure attribution;
- easier benchmark interpretation;
- lower storage and compute requirements;
- no premature global-descriptor decision;
- no premature index architecture;
- avoids retrieval errors contaminating registration evaluation;
- supports reproducibility;
- keeps sensor-specific registration research central;
- creates a reusable local-registration core.

#### Trade-offs

- V1 cannot globally localize an arbitrary unknown lunar image;
- V1 does not produce Recall@K results;
- whole-Moon scalability remains untested;
- retrieval integration remains future work.

---

### Option B — Add Global Retrieval to V1

#### Description

Build global lunar candidate retrieval and local registration together in the first scientific version.

#### Advantages

- broader end-to-end localization capability;
- potential support for unknown-location imagery;
- starts whole-Moon architecture earlier.

#### Trade-offs

- combines two independent research problems;
- requires reference-corpus preparation;
- requires global-descriptor selection;
- requires index infrastructure;
- requires retrieval benchmark design;
- introduces additional failure modes;
- increases compute/storage requirements;
- makes debugging harder;
- weakens interpretation of local-registration metrics;
- risks spending V1 effort on retrieval infrastructure before registration is trustworthy.

---

### Option C — Optional Retrieval Inside V1

#### Description

Include retrieval implementation within V1 but allow known-overlap execution as another mode.

#### Advantages

- enables early retrieval experimentation;
- supports broader demonstrations;
- may reduce later integration work.

#### Trade-offs

- makes V1 architecture substantially larger;
- weakens the canonical V1 boundary;
- introduces dependencies unnecessary for the baseline;
- creates ambiguity about which path defines V1;
- requires maintaining and validating retrieval infrastructure;
- risks coupling local registration to experimental search logic.

Experimental retrieval work may occur during V1 development, but it must remain outside the canonical V1 architecture.

---

## Decision

**Selected option: Option A — No Global Retrieval in V1**

ChandraMap V1 will:

- operate on known-overlap or externally constrained candidate regions;
- allow metadata-assisted candidate restriction;
- allow local tiling and local image pyramids;
- perform sensor-aware preparation;
- perform physically meaningful scale handling;
- perform local correspondence;
- perform geometric verification;
- estimate and refine transformations;
- produce registered outputs;
- perform independent evaluation where truth is available;
- record candidate-region provenance.

ChandraMap V1 will not require:

- whole-Moon candidate discovery;
- global descriptor generation;
- learned global lunar embeddings;
- FAISS;
- another ANN/vector retrieval system;
- global reference vector databases;
- Top-K whole-Moon ranking;
- retrieval-specific acceptance metrics;
- a production retrieval service.

> **ChandraMap should prove that it can correctly register the right lunar region before requiring itself to find that region across the entire Moon.**

---

## Rationale

Local lunar registration is already technically difficult because the input observations may differ in:

- sensor modality;
- physical scale;
- illumination;
- shadows;
- viewing geometry;
- radiometry;
- projection;
- terrain information content.

Adding global retrieval immediately would combine these problems with:

- reference tiling;
- corpus preparation;
- global descriptor research;
- vector indexing;
- candidate ranking;
- retrieval-scale selection;
- candidate deduplication;
- index versioning;
- retrieval evaluation.

An unsuccessful end-to-end result could then be caused by:

```text
Global Descriptor
OR
Reference Representation
OR
Indexing
OR
Wrong Candidate
OR
Sensor Preprocessing
OR
Scale Handling
OR
Local Correspondence
OR
Geometric Verification
OR
Transformation
OR
Refinement
OR
Evaluation
```

V1 intentionally removes the first set of uncertainties.

Its primary scientific question remains:

> **Given the correct or appropriately constrained lunar region, can ChandraMap establish reliable local correspondence and produce measurable registration?**

---

## V1 Architectural Boundary

The canonical V1 architecture begins after candidate-region identification.

```text
Input Source Image
        +
Known / Constrained Reference Region
        ↓
Input Validation
        ↓
Sensor-Aware Preparation
        ↓
Physical Scale Preparation
        ↓
Local Correspondence
        ↓
Candidate Matches
        ↓
Geometric Verification
        ↓
Verified Inliers
        ↓
Optional Tie-Point Refinement
        ↓
Final Transform Refit
        ↓
Registered Output
        ↓
Independent Evaluation
```

The following is outside canonical V1:

```text
Unknown Source Location
        ↓
Search Global Lunar Corpus
        ↓
Rank Candidate Regions
```

---

## Allowed V1 Candidate Sources

V1 may receive candidate regions through:

- known-overlap benchmark pairs;
- manually selected reference regions for controlled research;
- dataset-provided pair associations;
- product footprints;
- latitude/longitude metadata;
- projection metadata;
- predefined reference crops;
- external validated pairing;
- another non-retrieval source that constrains overlap.

V1 may also use:

- reference image pyramids inside the candidate region;
- local multi-scale matching;
- local reference tiling;
- local coarse-to-fine search;
- sensor-aware preprocessing;
- independent checkpoint evaluation.

---

## Explicitly Deferred Retrieval Capabilities

The following are outside canonical V1:

- whole-Moon candidate discovery;
- global image embedding generation;
- lunar reference-vector databases;
- FAISS integration as a V1 dependency;
- approximate-nearest-neighbor whole-Moon search;
- Top-K global candidate ranking;
- global retrieval reranking;
- global retrieval APIs;
- whole-Moon tile-index construction;
- global retrieval model training;
- retrieval-specific infrastructure;
- retrieval-specific benchmark acceptance.

These capabilities are deferred rather than rejected.

---

## Metadata-First Principle

> **When valid metadata already narrows the lunar location, ChandraMap should use it rather than intentionally discarding useful information and solving a harder global-retrieval problem unnecessarily.**

This is considered good engineering and good scientific scope control.

Permitted V1 path:

```text
Known Pair
        ↓
Local Registration
```

Permitted V1 path:

```text
Reliable Metadata
        ↓
Constrained Reference Region
        ↓
Local Registration
```

Not required in V1:

```text
Unknown Location
        ↓
Search Entire Lunar Corpus
        ↓
Top-K Candidates
        ↓
Local Registration
```

Metadata use must still be documented so results are reported honestly.

---

## Global Descriptors vs Local Correspondence

Global descriptors and local correspondence methods solve different problems.

### Global Descriptor

A global descriptor represents an image or tile as a compact vector suitable for large-corpus similarity search.

It answers:

> **Which reference region is likely to be relevant?**

Conceptually:

```text
Source Image
        ↓
Global Descriptor
        ↓
Candidate Reference Region
```

### Local Features / Local Matcher

Local matching produces point-level relationships used for geometric verification and registration.

It answers:

> **Which exact image locations correspond?**

Conceptually:

```text
Source + Candidate Region
        ↓
Local Correspondences
        ↓
Geometry
        ↓
Registration
```

A future end-to-end system may combine both:

```text
Global Retrieval
        ↓
Candidate Region
        ↓
Local Registration
```

but they must remain conceptually and metrically separate.

---

## FAISS Clarification

FAISS, if adopted by a later retrieval architecture, would provide vector similarity search/indexing.

FAISS does **not** itself:

- extract lunar image features;
- create global visual descriptors;
- detect local keypoints;
- generate local correspondences;
- perform SIFT;
- perform LightGlue matching;
- perform LoFTR matching;
- run RANSAC;
- estimate affine transforms;
- estimate homographies;
- refine tie points;
- register imagery;
- calculate registration accuracy.

A future reference-side architecture would require:

```text
Reference Tile
        ↓
Global Descriptor Method
        ↓
Descriptor Vector
        ↓
Vector Index
```

A query would require:

```text
Source Image
        ↓
Same Compatible Descriptor Method
        ↓
Query Vector
        ↓
Vector Search
        ↓
Nearest Reference Vectors
        ↓
Candidate Lunar Regions
```

The global descriptor method remains **TBD**.

FAISS is not required by V1.

---

## Local Multi-Scale Processing vs Global Retrieval

Multi-scale processing is not equivalent to global retrieval.

V1 may use:

```text
Known Reference Region
        ↓
Reference Pyramid
        ↓
Comparable Effective Scale
        ↓
Local Matching
```

This solves a local scale-matching problem.

Global retrieval instead performs:

```text
Unknown Region
        ↓
Search Across Large Reference Corpus
```

These are different architectural operations.

---

## Local Tiling vs Global Retrieval

Tiling itself is also not global retrieval.

A known candidate region may be divided into local tiles for:

- memory management;
- image-size control;
- scale handling;
- coarse-to-fine processing;
- overlap management.

This remains inside V1 if the larger candidate region was already known or constrained.

The boundary is crossed when tiles across a broad/global reference corpus are searched to identify an otherwise unknown lunar region.

---

## Candidate Provider Interface Boundary

The preferred conceptual dependency direction is:

```text
Candidate Region Provider
        ↓
Local Registration Core
```

V1:

```text
Known Pair / Metadata Constraint
        ↓
Candidate Region Provider
        ↓
Local Registration Core
```

Future:

```text
Global Retrieval Engine
        ↓
Candidate Region Provider
        ↓
Local Registration Core
```

The local-registration core should ideally not need to know whether the candidate originated from:

- a benchmark manifest;
- a manually prepared pair;
- product metadata;
- geographic footprint intersection;
- future vector retrieval;
- another retrieval algorithm.

The local pipeline's responsibility is to determine whether the supplied candidate can be geometrically aligned.

This reduces coupling and preserves V1 reuse.

---

## Why the Local Pipeline Must Not Depend on Candidate Discovery

Coupling local matching to candidate-discovery logic would make it harder to:

- benchmark registration independently;
- replace a future retrieval method;
- compare metadata-based and image-based candidate selection;
- debug local failures;
- reuse V1 from future versions;
- perform controlled known-overlap experiments.

The local-registration pipeline therefore conceptually consumes:

```text
Source
+
Candidate Reference
```

rather than:

```text
Source
+
Global Retrieval Implementation Details
```

---

## V1 Benchmark Impact

ADR-0004 defines an important benchmark boundary.

### V1 Registration Metrics May Include

Where defined by the benchmark/evaluation specification:

- candidate correspondence count;
- filtered candidate count;
- verified inlier count;
- inlier ratio;
- fit/reprojection residual;
- independent checkpoint RMSE;
- source-image pixel error;
- spatial coverage;
- runtime;
- scientific success/failure;
- sensor/category-specific registration behavior.

No numerical thresholds are defined by this ADR.

### Metrics Not Required by V1

V1 does not require:

- Recall@1;
- Recall@5;
- Recall@K;
- mean reciprocal rank;
- global candidate precision;
- whole-Moon retrieval latency;
- global index build time;
- global index memory;
- reference-index size.

These belong to a future retrieval benchmark.

---

## Why Recall@K Is Deferred

Retrieval-specific metrics become meaningful only when a genuine retrieval problem exists.

For example:

**Recall@1** asks whether the correct reference region appears as the highest-ranked retrieved candidate.

**Recall@5** asks whether the correct reference region appears somewhere among the first five retrieved candidates.

**Recall@K** generalizes this question to a benchmark-defined value of \(K\).

These metrics answer:

> **Did retrieval find the correct region?**

They do not answer:

> **How accurately did that region register?**

No Recall@K value is claimed or required by this ADR.

---

## Failure Attribution

A major objective of ADR-0004 is to make V1 failures interpretable.

A canonical V1 failure should be attributable to areas such as:

- input validation;
- sensor preprocessing;
- physical scale handling;
- local correspondence;
- match filtering;
- geometric verification;
- transformation estimation;
- refinement;
- registration;
- independent evaluation.

The first V1 investigation should not need to ask:

```text
Was the retrieved reference region wrong?
```

Retrieval failure becomes a separate category only when a later retrieval subsystem is formally introduced.

---

## Sensor-Specific Retrieval Complexity

Future retrieval is not expected to be sensor-neutral automatically.

### OHRC

OHRC provides very high-detail visible/panchromatic imagery.

A future retrieval representation may need to bridge a major scale difference between OHRC and coarser reference representations.

### TMC-2

TMC-2 provides approximately 5 m/pixel panchromatic terrain imagery in the general project context.

Its useful retrieval representation and operating scale may differ substantially from OHRC.

### IIRS

IIRS provides much coarser hyperspectral/infrared information.

A future retrieval architecture must determine how to convert the spectral product into a representation suitable for global search.

Potential choices remain unresolved.

ADR-0004 deliberately does not select:

- a universal global descriptor;
- a cross-sensor embedding;
- an IIRS retrieval representation.

---

## Reference-Side Complexity

Whole-Moon retrieval would require separate decisions concerning matters such as:

- LRO NAC vs WAC corpus design;
- map projection;
- lunar tiling scheme;
- tile dimensions;
- tile overlap;
- scale hierarchy;
- reference pyramid construction;
- descriptor generation;
- descriptor storage;
- tile metadata;
- polar regions;
- candidate ranking;
- spatial deduplication;
- reference updates;
- index regeneration;
- versioning;
- storage requirements.

None of these decisions are required to prove V1 local registration.

They are intentionally deferred.

---

## Reference Pyramids Remain Allowed

ADR-0004 does not prohibit V1 reference pyramids.

A V1 workflow may use:

```text
Known Reference Region
        ↓
Pyramid Level 0
Pyramid Level 1
Pyramid Level 2
...
        ↓
Comparable Effective Scale
        ↓
Local Matching
```

A pyramid within an already selected region addresses the **scale** problem.

It does not constitute whole-Moon retrieval.

---

## Retrieval Experiments During V1 Development

Experimental retrieval research may occur while V1 is being developed.

Such experiments must:

- remain explicitly experimental;
- not become a mandatory V1 dependency;
- not modify V1 acceptance criteria;
- not silently alter the canonical V1 pipeline;
- use separate retrieval evaluation;
- preserve the known-overlap registration baseline.

This allows research exploration without contaminating the architectural boundary of V1.

---

## What This ADR Decides

ADR-0004 establishes that:

- global retrieval is excluded from canonical V1;
- V1 does not require whole-Moon candidate discovery;
- known-overlap candidate selection is allowed;
- metadata-constrained candidate selection is allowed;
- local image pyramids are allowed;
- local tiling is allowed;
- local multi-scale search is allowed;
- retrieval metrics are not V1 registration metrics;
- retrieval infrastructure is not a V1 dependency;
- FAISS is not required by V1;
- candidate provenance must be preserved;
- local registration should remain independent of candidate-discovery mechanism;
- future retrieval should feed candidates into the existing registration architecture where practical;
- future global retrieval requires separate architectural and benchmark decisions.

---

## What This ADR Does Not Decide

ADR-0004 does not decide:

- whether FAISS will ultimately be adopted;
- which global descriptor will be used;
- whether a learned descriptor is required;
- whole-Moon tiling strategy;
- tile dimensions;
- tile overlap;
- descriptor dimensionality;
- approximate-nearest-neighbor index type;
- Top-K value;
- candidate reranking policy;
- geospatial deduplication policy;
- index persistence format;
- index versioning;
- whole-Moon database architecture;
- NAC/WAC corpus strategy;
- cross-scale retrieval strategy;
- IIRS global-search representation;
- cross-sensor embedding architecture;
- retrieval hardware requirements;
- retrieval API;
- retrieval service architecture;
- retrieval caching;
- distributed search;
- future scientific-version retrieval architecture.

All remain deferred.

---

## Non-Goals

This ADR does not:

- reject global lunar localization as a project goal;
- claim global search is unnecessary;
- claim reliable metadata will always exist;
- define future retrieval algorithms;
- define a production planetary search engine;
- define a complete lunar tiling standard;
- select FAISS;
- select a neural retrieval model;
- select a global descriptor;
- establish Recall@K thresholds;
- establish retrieval-speed targets;
- define V2, V3, or V4 retrieval architecture;
- prohibit experimental global-retrieval research.

---

## V1 vs Future Architecture

### V1 — Accepted Architecture

```text
Source Image
        +
Known / Metadata-Constrained Reference Region
        ↓
Sensor-Aware Preparation
        ↓
Physical Scale Handling
        ↓
Local Correspondence
        ↓
Candidate Matches
        ↓
RANSAC / Geometric Verification
        ↓
Verified Inliers
        ↓
Optional Refinement
        ↓
Final Transform
        ↓
Registered Output
        ↓
Independent Evaluation
```

### Future Retrieval-Enabled Architecture — Conceptual, Not Implemented

```text
Unknown Source Image
        ↓
Global Descriptor
        ↓
Reference Search / Vector Index
        ↓
Top-K Candidate Regions
        ↓
Existing Local Registration Core
        ↓
Geometric Verification
        ↓
Validated Registration Candidate
```

The second diagram represents deferred architecture, not current implementation status.

---

## Reproducibility and Candidate Provenance

Because V1 receives a known or constrained reference region, formal results should record how that region was obtained.

Possible provenance concepts include:

- known benchmark pair;
- manually defined overlap;
- dataset-provided pairing;
- product footprint;
- coordinate constraint;
- predefined reference crop;
- another externally constrained source.

Where applicable, benchmark provenance should preserve:

- source identifier;
- reference identifier;
- pair identity;
- overlap/candidate provenance;
- relevant metadata used;
- crop information;
- scale/pyramid configuration;
- local matcher configuration;
- geometric-verification configuration;
- evaluation configuration;
- scientific version;
- benchmark version;
- code revision.

This ADR does not define a final serialization schema.

Conceptually, a future result may include information equivalent to:

```text
candidate_source: known_overlap
```

or:

```text
candidate_source: metadata_constrained
```

The exact field name/value remains repository-defined.

---

## Honest Reporting Requirements

V1 results must not imply that a known candidate region was discovered globally when it was supplied in advance.

Incorrect description:

> ChandraMap found this lunar region anywhere on the Moon and registered it.

when the region was manually or metadata-selected.

Appropriate V1 wording is closer to:

> **Given a known or externally constrained overlapping lunar region, ChandraMap establishes local correspondences, verifies them geometrically, estimates the registration, and evaluates the result.**

Similarly, a local registration benchmark must not be described as a whole-Moon localization benchmark.

This reporting distinction is part of scientific reproducibility.

---

## Testing and Validation Plan

### Stage 1 — Known Pair

Run conceptually:

```text
Known Source
+
Known Reference
        ↓
Local Registration
```

Verify that:

- no global descriptor is required;
- no vector index is required;
- no global candidate ranking is required;
- local registration produces a result or explicit scientific failure.

---

### Stage 2 — Metadata-Constrained Pair

Where reliable metadata exists:

```text
Metadata
        ↓
Restrict Candidate Region
        ↓
Local Registration
```

Verify that candidate restriction remains separate from image-based global retrieval.

---

### Stage 3 — Local Reference Pyramid

Within a known reference region:

```text
Known Region
        ↓
Reference Pyramid
        ↓
Comparable Scale
        ↓
Local Registration
```

Verify that multi-scale local processing does not require corpus-wide search.

---

### Stage 4 — Candidate Provider Boundary

Validate that local registration can conceptually consume a reference candidate without depending on how the candidate was selected.

The local scientific logic should remain reusable across:

- manual pairing;
- metadata pairing;
- future retrieval.

---

### Stage 5 — Future Retrieval Integration

**Not a V1 acceptance criterion.**

If global retrieval is later implemented:

```text
Global Retrieval
        ↓
Candidate Region
        ↓
Existing Local Registration Core
```

validate that retrieval can feed the established registration system without redefining its fundamental evaluation semantics.

---

## Acceptance Criteria

ADR-0004 is considered implemented when:

- [ ] canonical V1 operates without a global retrieval subsystem;
- [ ] known-overlap reference regions can be supplied cleanly;
- [ ] metadata-constrained candidate regions can be supplied where available;
- [ ] local registration does not require FAISS;
- [ ] local registration does not require global descriptors;
- [ ] local registration does not require a reference-vector database;
- [ ] local image pyramids remain usable;
- [ ] local tiling remains usable;
- [ ] multi-scale registration remains usable;
- [ ] result provenance identifies how the candidate region was obtained;
- [ ] V1 benchmark reporting does not imply global candidate discovery;
- [ ] V1 metrics remain registration-focused;
- [ ] retrieval-specific metrics remain outside V1 acceptance;
- [ ] future candidate providers can conceptually feed the same registration core;
- [ ] documentation clearly distinguishes local registration from global retrieval.

No numeric accuracy or runtime threshold is defined by this ADR.

---

## Consequences

### Positive Consequences

#### Smaller V1 architecture

The first scientific version requires fewer subsystems.

#### Clearer scientific question

V1 asks whether the correct lunar region can be registered, not whether the correct region can also be discovered globally.

#### Better failure attribution

A failed result can be investigated without first validating retrieval.

#### Cleaner benchmark interpretation

Registration metrics remain independent from retrieval ranking.

#### Lower infrastructure requirements

V1 does not require a whole-Moon descriptor/index pipeline.

#### Better reproducibility

Known or constrained candidate regions make the first benchmark easier to repeat.

#### Reusable local core

Later retrieval architectures can reuse the V1 registration pipeline.

#### Fewer hidden dependencies

The baseline does not depend on an immature retrieval subsystem.

#### More research capacity for sensor differences

Development effort can focus on:

- cross-scale matching;
- illumination variation;
- IIRS representation;
- geometric verification;
- transform quality;
- evaluation.

---

### Negative Consequences / Trade-offs

#### No arbitrary-image whole-Moon localization

Canonical V1 cannot accept an entirely unknown-location source and be expected to discover its lunar location globally.

#### Retrieval benchmarks are deferred

V1 does not produce formal Recall@K retrieval results.

#### Whole-Moon scalability remains unproven

V1 does not demonstrate planetary-scale reference search.

#### Retrieval infrastructure remains future work

Index preparation, descriptor generation, and candidate ranking must still be designed later.

#### Some demonstrations require prepared candidates

Users or benchmark definitions must supply or constrain the reference region.

---

### Neutral Consequences

- metadata may still restrict search;
- local image pyramids remain allowed;
- local tiling remains allowed;
- retrieval research may proceed experimentally;
- future retrieval requires a dedicated ADR or equivalent architectural decision;
- V1 remains useful after retrieval is introduced because the registration core remains independently benchmarkable.

---

## Risks and Mitigations

| Risk                                                                         | Why It Matters                                                | Mitigation                                                     |
| ---------------------------------------------------------------------------- | ------------------------------------------------------------- | -------------------------------------------------------------- |
| V1 is presented as whole-Moon localization                                   | Overstates actual capability                                  | Record candidate provenance and use precise reporting language |
| Metadata-constrained selection is confused with global retrieval             | Blurs subsystem boundaries                                    | Define both concepts explicitly                                |
| Future retrieval becomes tightly coupled to local registration               | Makes methods harder to replace or benchmark independently    | Preserve the candidate-provider → local-registration boundary  |
| Retrieval code silently becomes a V1 dependency                              | Expands the baseline and weakens reproducibility              | Keep retrieval infrastructure outside canonical V1             |
| Known-overlap pairs are too easy                                             | May hide illumination, scale, or modality weaknesses          | Use controlled known-overlap stress cases                      |
| Failed pairs are blamed on candidate selection despite known correct overlap | Weakens failure diagnosis                                     | Preserve pair/candidate provenance                             |
| Future candidate-retrieval failures are confused with local matcher failures | Obscures scientific attribution                               | Maintain separate retrieval and registration result semantics  |
| Reference pyramid is mistakenly classified as global retrieval               | Could prohibit legitimate scale handling                      | Explicitly distinguish local multi-scale processing            |
| Manual reference selection is reported as automatic discovery                | Damages research credibility                                  | Require honest candidate-source reporting                      |
| Future global retrieval assumes one representation works for every sensor    | OHRC, TMC-2, and IIRS have very different information content | Benchmark sensor-specific/global representations separately    |

These are architectural risks, not claims that the failures have already occurred.

---

## Deferred Decisions

The following retrieval decisions remain explicitly deferred:

- whole-Moon tiling;
- tile dimensions;
- tile overlap;
- tile scale hierarchy;
- reference-corpus projection;
- reference-source selection;
- global image representation;
- classical vs learned global descriptor;
- descriptor dimensionality;
- descriptor normalization;
- vector-index library;
- FAISS adoption;
- FAISS configuration;
- ANN index structure;
- Top-K candidate count;
- candidate reranking;
- geographic deduplication;
- cross-scale retrieval;
- cross-sensor retrieval;
- IIRS retrieval representation;
- global cross-modal embedding;
- offline index-generation pipeline;
- online retrieval API;
- retrieval service architecture;
- index persistence;
- index versioning;
- cache architecture;
- distributed search;
- retrieval benchmarks;
- retrieval acceptance thresholds;
- index memory targets;
- index construction-time targets;
- global-search latency targets.

No defaults are implied.

---

## Relationship to Future ChandraMap Versions

The intended architectural evolution is:

### V1

```text
Known / Constrained Region
        ↓
Local Registration
        ↓
Independent Evaluation
```

### Later Retrieval-Enabled Version

```text
Unknown Source
        ↓
Global / Regional Retrieval
        ↓
Candidate Region
        ↓
Existing Local Registration Core
        ↓
Registration Evaluation
```

The later system adds an upstream capability.

It should not require V1's local-registration science to be redefined.

Where scientifically compatible, future versions should still be able to run the known-overlap registration benchmark to preserve backward comparison.

No exact later version number or retrieval algorithm is selected by this ADR.

---

## Future Retrieval Benchmark

When global retrieval becomes a formal subsystem, it should receive a dedicated benchmark specification.

Potential evaluation dimensions may include:

- Recall@1;
- Recall@5;
- Recall@K;
- candidate ranking behavior;
- retrieval failure rate;
- retrieval latency;
- index build cost;
- index memory/storage;
- sensor-specific retrieval behavior.

These are examples of metric families, not final benchmark requirements.

No:

- target value;
- threshold;
- Top-K definition;
- latency target;
- index size;
- memory limit

is defined by ADR-0004.

A future ADR and evaluation specification should establish those details.

---

## Revisit Conditions

ADR-0004 should be revisited when one or more of the following occurs:

- V1 local registration becomes sufficiently stable that retrieval is the primary next research problem;
- unknown-location localization becomes a formal project requirement;
- benchmark rules explicitly require global search;
- reliable geospatial metadata is unavailable for the intended operating mode;
- a later scientific version formally introduces whole-Moon search;
- a validated global retrieval prototype is ready for architectural integration;
- a retrieval benchmark has been defined independently from registration benchmarking.

Revisiting this decision must not erase the historical reasoning recorded here.

A later ADR should:

1. reference ADR-0004;
2. explain the changed context;
3. define the new retrieval architecture;
4. define its relationship to the local registration core;
5. define retrieval-specific benchmarking;
6. mark ADR-0004 as `Superseded` if the canonical architecture has genuinely changed.

---

## References

### Architecture Decision Records

- [ADR Index](README.md)
- [ADR Template](ADR_TEMPLATE.md)
- [ADR-0001 — V1 Known-Overlap First](0001-v1-known-overlap-first.md)
- [ADR-0002 — SIFT as the V1 Baseline](0002-sift-as-v1-baseline.md)
- [ADR-0003 — Independent Checkpoints](0003-independent-checkpoints.md)

### Project Documentation

- [Project Goals](../project/goals.md)
- [Project Non-Goals](../project/non-goals.md)
- [V1 Scope](../project/v1-scope.md)
- [Project Terminology](../project/terminology.md)
- [Project Assumptions](../project/assumptions.md)
- [Project Limitations](../project/limitations.md)

### Architecture Documentation

- [System Overview](../architecture/system-overview.md)
- [V1 Pipeline](../architecture/v1-pipeline.md)
- [Core Engine Architecture](../architecture/core-engine-architecture.md)
- [Data Flow](../architecture/data-flow.md)
- [Output Flow](../architecture/output-flow.md)

### Research Documentation

- [Research Overview](../research/README.md)
- [Baseline](../research/baseline.md)
- [Research Questions](../research/research-questions.md)
- [Research Assumptions](../research/assumptions.md)
- [Experiment Methodology](../research/experiment-methodology.md)
- [Research References](../research/references.md)

### Development and Evaluation

- [Benchmarking Guide](../development/benchmarking.md)
- [Naming Conventions](../development/naming-conventions.md)
- [Testing](../development/testing.md)

### External Reference Categories

Relevant future retrieval work should consult authoritative sources for:

- FAISS vector-search documentation;
- ISRO Chandrayaan-2 product and sensor documentation;
- ISSDC / PRADAN product metadata;
- LROC / LRO product documentation;
- planetary image-search literature;
- remote-sensing image-retrieval literature.

Selection of a specific retrieval technique remains deferred.

---

## Final Decision Statement

> **ChandraMap V1 will not implement or require whole-Moon/global candidate retrieval. V1 will operate on known-overlap or externally constrained reference regions and will focus on local correspondence, geometric verification, registration, and independent evaluation.**

The canonical V1 path is:

```text
Known / Metadata-Constrained Region
        ↓
Local Correspondence
        ↓
Geometric Verification
        ↓
Registration
        ↓
Independent Evaluation
```

It is **not**:

```text
Unknown Source
        ↓
Search Entire Moon
        ↓
Retrieve Candidate
        ↓
Registration
```

The future architectural direction remains:

```text
Future Retrieval System
        ↓
Candidate Region
        ↓
Existing Local Registration Core
```

ADR-0004 therefore preserves a deliberate research sequence:

```text
First:
Can ChandraMap correctly register the right lunar region?

Then:
Can ChandraMap also find the right region in a large lunar reference corpus?
```

Global retrieval is deferred, not rejected. The local-registration baseline remains the scientific foundation on which future retrieval-enabled ChandraMap versions should build.

<!-- Source request: :contentReference[oaicite:0]{index=0} -->
