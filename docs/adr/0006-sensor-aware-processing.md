# ADR-0006: Sensor-Aware Processing

> **Decision summary:** ChandraMap will identify and prepare source/reference products according to their physical sensor and product characteristics before they enter shared correspondence and registration stages. OHRC, TMC-2, IIRS, and LRO reference products must not be forced through one blind preprocessing pipeline.

## Status

| Field                   | Value                                                                                                                                                                                                                               |
| ----------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **ADR**                 | ADR-0006                                                                                                                                                                                                                            |
| **Title**               | Sensor-Aware Processing                                                                                                                                                                                                             |
| **Status**              | Accepted                                                                                                                                                                                                                            |
| **Date**                | `<YYYY-MM-DD>`                                                                                                                                                                                                                      |
| **Decision Scope**      | V1 and shared architecture                                                                                                                                                                                                          |
| **Architecture Area**   | Sensor Processing / Registration Preparation                                                                                                                                                                                        |
| **Sensor Scope**        | OHRC, TMC-2, IIRS, LRO reference products                                                                                                                                                                                           |
| **Supersedes**          | N/A                                                                                                                                                                                                                                 |
| **Superseded By**       | N/A                                                                                                                                                                                                                                 |
| **Related ADRs**        | [ADR-0001](0001-v1-known-overlap-first.md), [ADR-0002](0002-sift-as-v1-baseline.md), [ADR-0003](0003-independent-checkpoints.md), [ADR-0004](0004-no-global-retrieval-in-v1.md), [ADR-0005](0005-core-engine-separated-from-api.md) |
| **Related Issues**      | N/A                                                                                                                                                                                                                                 |
| **Related PRs**         | N/A                                                                                                                                                                                                                                 |
| **Related Experiments** | TBD                                                                                                                                                                                                                                 |
| **Related Benchmarks**  | V1 sensor/preprocessing benchmark / TBD                                                                                                                                                                                             |

**Accepted** means ChandraMap has adopted sensor-aware routing and preparation as an architectural principle.

It does **not** mean that:

- every sensor-processing path is already implemented;
- OHRC preprocessing is finalized;
- TMC-2 preprocessing is finalized;
- a final IIRS registration representation has been selected;
- sensor-aware preprocessing has already demonstrated numerical improvement;
- every sensor currently meets a defined registration target;
- all future processing choices have been decided.

---

## Context

ChandraMap establishes correspondence and registration between lunar images that may differ substantially in:

- sensor;
- mission;
- spatial resolution;
- ground sampling distance;
- spectral modality;
- illumination;
- viewing geometry;
- map projection;
- product processing level;
- terrain information content.

The primary Chandrayaan-2 source instruments considered by the project are:

- OHRC — Orbiter High Resolution Camera;
- TMC-2 — Terrain Mapping Camera-2;
- IIRS — Imaging Infrared Spectrometer.

Reference imagery may include:

- LRO NAC;
- LRO WAC.

These inputs are not physically equivalent image products.

A registration system that treats them as interchangeable arrays risks hiding assumptions about:

- physical scale;
- spectral content;
- resolved terrain structure;
- product geometry;
- illumination;
- coordinate systems;
- available metadata.

ChandraMap therefore needs an explicit architectural boundary between:

```text
Sensor / Product Interpretation
```

and:

```text
Shared Registration
```

The decision is not that every sensor needs an entirely independent registration system.

The decision is that sensor-specific physical differences must be handled **before** shared downstream correspondence logic assumes that it has a meaningful registration representation.

> **Convert each sensor toward a useful registration representation only after sensor-specific preparation.**

---

## Problem

A universal preprocessing path such as:

```text
OHRC  → grayscale → normalize → resize → matcher
TMC-2 → grayscale → normalize → resize → matcher
IIRS  → grayscale → normalize → resize → matcher
```

is architecturally simple but scientifically weak.

It assumes, implicitly, that:

- each product begins as the same kind of imagery;
- grayscale conversion has the same meaning for each sensor;
- one radiometric operation is appropriate for all modalities;
- resizing resolves physical-scale differences;
- a full hyperspectral product can be treated like ordinary 2D panchromatic imagery;
- useful sensor metadata can be ignored once pixel arrays are loaded.

Those assumptions are not generally defensible.

For OHRC and TMC-2, a panchromatic/intensity or terrain-structure representation may be appropriate.

For IIRS, an earlier and more fundamental question exists:

> Which reproducible 2D representation of the hyperspectral product preserves terrain information useful for registration?

The architecture must leave that question explicit rather than hiding it inside a generic `to_grayscale()` operation.

A poor preprocessing architecture can:

- discard useful terrain information;
- destroy metadata required for later evaluation;
- create false impressions of spatial resolution through upsampling;
- make sensor-specific failures difficult to diagnose;
- couple research choices tightly to one matcher;
- make benchmark results irreproducible;
- break source-coordinate traceability required by independent evaluation.

---

## Decision Drivers

This decision is driven by:

- physical correctness;
- sensor-modality differences;
- hyperspectral handling;
- preservation of information content;
- correct scale interpretation;
- metadata preservation;
- scientific reproducibility;
- benchmark interpretability;
- sensor-specific evaluation;
- maintainability;
- code reuse;
- matcher independence;
- coordinate traceability;
- compatibility with independent checkpoint evaluation;
- illumination-awareness;
- avoidance of fabricated spatial detail;
- future sensor extensibility;
- separation between scientific core logic and API/frontend concerns.

---

## Sensor Reality

### OHRC

OHRC provides very high-resolution visible/panchromatic lunar imagery.

Project-level context places its spatial scale at approximately:

`~0.25–0.32 m/pixel`

depending on product and documentation.

Architectural implications include:

- very fine terrain detail may be present;
- many fine-scale features may not exist in a coarser reference;
- high nominal resolution does not guarantee easy matching;
- large scale differences can dominate correspondence behavior;
- Sun angle, viewpoint, terrain relief, projection, and reference scale remain important;
- unnecessary filtering can remove useful fine crater/ridge structure.

Specific product metadata is authoritative for an actual input.

---

### TMC-2

TMC-2 provides panchromatic terrain imagery at approximately:

`~5 m/pixel`

at project-summary level.

Architectural implications include:

- terrain structure can support regional correspondence;
- its physical scale differs substantially from OHRC and IIRS;
- scale compatibility with a high-resolution reference must be considered;
- projection and terrain information may be useful;
- illumination variation and repetitive lunar structures remain relevant.

This ADR does not claim that TMC-2 is inherently easier to register than other sensors.

---

### IIRS

IIRS is an imaging infrared hyperspectral instrument.

Its project-level spatial scale is approximately:

`~80 m/pixel`

and it spans a broad spectral range.

Architectural implications are substantial:

- the product may contain a spectral dimension rather than one ordinary grayscale plane;
- the complete cube must not be passed blindly into a standard 2D local matcher;
- a reproducible registration-oriented representation must be defined;
- many fine structures present in OHRC or NAC may be physically unresolved;
- spectral appearance may differ strongly from visible panchromatic reference imagery;
- upsampling cannot recreate missing spatial detail.

The exact number of bands, wavelengths, product organization, calibration state, and metadata must come from the actual product rather than being hard-coded by this ADR.

---

### LRO Reference Imagery

LRO reference imagery must also be treated as a physical product rather than as a generic reference array.

#### LRO NAC

LRO NAC provides high-resolution lunar imagery whose scale varies by product and acquisition geometry.

For coarse source sensors, a NAC reference may contain substantially more spatial detail than the source.

The scientifically meaningful comparison may therefore require a scale-compatible reference representation rather than enlargement of the coarse source.

#### LRO WAC

LRO WAC provides broader-scale lunar context and is not interchangeable with NAC.

Reference-product choice, projection, scale, and benchmark role remain explicit data/evaluation decisions.

---

## Terminology

### Sensor Route

A **sensor route** is the scientifically appropriate preparation path selected for a known sensor/product type before shared registration.

It does not imply an entirely separate end-to-end pipeline.

---

### Sensor-Specific Preparation

**Sensor-specific preparation** is the processing necessary to turn a sensor product into a meaningful registration representation while preserving relevant metadata and coordinate traceability.

It can include, depending on the product:

- product validation;
- calibration awareness;
- projection awareness;
- spectral representation;
- conservative radiometric preparation;
- structural representation;
- resampling with explicit coordinate transforms.

---

### Registration Representation

A **registration representation** is the prepared 2D representation supplied to the local correspondence stage together with sufficient metadata to interpret geometry, scale, and provenance.

A common registration representation does **not** imply that all sensors were processed identically.

---

### Ground Sampling Distance

Ground Sampling Distance (GSD) describes the approximate physical ground extent represented by an image pixel under the product's geometry.

It is distinct from image matrix dimensions.

Product-specific scale metadata takes precedence over approximate instrument summaries.

---

### Resampling

**Resampling** changes the sampling grid of an image representation.

Resampling may:

- make comparison at a selected scale more convenient;
- support pyramid construction;
- transform between processing grids.

Resampling does not create physical surface information that the source instrument did not resolve.

---

## Relationship to Existing ADRs

### ADR-0001 — V1 Known-Overlap First

[ADR-0001](0001-v1-known-overlap-first.md) establishes that V1 starts with source/reference pairs already known to overlap.

ADR-0006 defines how those known products are prepared before local registration.

```text
Known Source / Reference Pair
        ↓
Sensor-Aware Preparation
        ↓
Local Registration
```

Sensor-aware preparation therefore does not require whole-Moon search.

---

### ADR-0002 — SIFT as the V1 Local-Matching Baseline

[ADR-0002](0002-sift-as-v1-baseline.md) establishes SIFT as the V1 local matching baseline.

ADR-0006 establishes what happens before SIFT receives its registration representation.

```text
Sensor Product
        ↓
Sensor-Aware Preparation
        ↓
Registration-Ready 2D Representation
        ↓
SIFT Baseline
```

SIFT is downstream of sensor interpretation.

A full hyperspectral IIRS cube is therefore not passed directly to SIFT merely because SIFT is the baseline matcher.

---

### ADR-0003 — Independent Checkpoints for Registration Evaluation

[ADR-0003](0003-independent-checkpoints.md) establishes independent checkpoint evaluation.

ADR-0006 must therefore preserve enough geometry and coordinate provenance to map:

```text
Original Source Coordinates
        ↕
Prepared Processing Coordinates
        ↕
Matcher Coordinates
```

without losing the ability to express registration error in the required evaluation coordinate system.

Sensor preparation must not make source-pixel evaluation ambiguous.

---

### ADR-0004 — No Global Retrieval in V1

[ADR-0004](0004-no-global-retrieval-in-v1.md) excludes global retrieval from the V1 scope.

ADR-0006 therefore applies in V1 primarily to:

- known-overlap pairs;
- locally constrained source/reference pairs;
- metadata-constrained local registration.

Future retrieval may require sensor-aware global descriptors or search representations.

Those choices are outside ADR-0006.

---

### ADR-0005 — Core Engine Separated from API

[ADR-0005](0005-core-engine-separated-from-api.md) separates reusable scientific functionality from transport/API concerns.

Sensor-aware processing therefore belongs in the scientific/core domain.

Correct architectural direction:

```text
API / CLI / Notebook
        ↓
Core Engine
        ↓
Sensor Identification
        ↓
Sensor-Aware Processing
        ↓
Registration
```

Incorrect architectural direction:

```text
API Route
        ↓
if OHRC: scientific preprocessing A
if TMC-2: scientific preprocessing B
if IIRS: scientific preprocessing C
        ↓
Core Registration
```

Scientific sensor behavior must be reusable independently of HTTP routes or frontend state.

---

## Considered Options

### Option A — Universal Preprocessing

**Description**

Convert all source products into a common generic representation using essentially the same preprocessing sequence.

**Advantages**

- smallest initial implementation;
- fewest branches;
- low configuration complexity;
- straightforward shared code path.

**Disadvantages / Trade-offs**

- ignores sensor modality;
- is particularly weak for full hyperspectral IIRS input;
- can conceal incorrect physical assumptions;
- encourages array-size thinking instead of ground-scale reasoning;
- may discard useful sensor-specific information;
- makes failures harder to attribute;
- risks treating interpolation as recovered detail;
- reduces benchmark interpretability.

**Assessment**

Rejected as the primary architecture.

---

### Option B — Sensor-Aware Processing Before Shared Registration

**Description**

Identify the product and sensor, apply sensor-appropriate preparation, preserve relevant metadata and coordinate transforms, and then converge to a common registration boundary for downstream matching, geometry, and evaluation.

**Advantages**

- respects physical sensor differences;
- handles IIRS explicitly;
- preserves meaningful sensor information;
- supports per-sensor experimentation;
- enables clearer failure diagnosis;
- maintains shared downstream registration infrastructure;
- allows fair matcher comparison after preparation;
- preserves metadata for scale and evaluation;
- enables future sensor extension without duplicating all geometry/evaluation logic.

**Trade-offs**

- introduces additional preparation paths;
- increases configuration surface;
- requires sensor/product metadata or explicit configuration;
- requires per-sensor tests;
- increases benchmark combinations;
- requires research to determine useful IIRS representations and other preprocessing defaults.

**Assessment**

Selected.

---

### Option C — Completely Independent End-to-End Sensor Pipelines

**Description**

Maintain largely separate OHRC, TMC-2, and IIRS systems, including independent matching, geometry, output, and evaluation implementations.

**Advantages**

- maximum sensor-specific freedom;
- experimentation can proceed without a common downstream abstraction.

**Disadvantages / Trade-offs**

- high code duplication;
- duplicated geometry and evaluation semantics;
- increased risk of inconsistent benchmark behavior;
- difficult matcher comparison;
- higher maintenance burden;
- harder cross-sensor reproducibility;
- future architectural drift between sensor paths.

**Assessment**

Rejected as the default architecture.

---

## Option Comparison

| Criterion                         | Universal Preprocessing      | Sensor-Aware + Shared Core     | Fully Separate Pipelines |
| --------------------------------- | ---------------------------- | ------------------------------ | ------------------------ |
| Respects sensor modality          | Weak                         | Strong                         | Strong                   |
| IIRS suitability                  | Weak                         | Strong                         | Strong                   |
| Shared geometry/evaluation        | Strong                       | Strong                         | Weak                     |
| Initial implementation simplicity | Strong                       | Moderate                       | Weak                     |
| Long-term maintainability         | Moderate                     | Strong                         | Weak                     |
| Benchmark interpretability        | Weak                         | Strong                         | Moderate                 |
| Code duplication                  | Low                          | Low / Moderate                 | High                     |
| Matcher independence              | Moderate                     | Strong                         | Variable                 |
| Future sensor extensibility       | Moderate                     | Strong                         | Weak / Moderate          |
| Metadata preservation             | Possible but easy to neglect | Explicit architectural concern | Sensor-specific          |
| Scientific traceability           | Weak / Moderate              | Strong                         | Moderate                 |

---

## Decision

ChandraMap adopts:

> **Option B — Sensor-Aware Processing Before Shared Registration**

The intended architecture is:

```text
                  SENSOR-SPECIFIC SIDE

OHRC ──────→ OHRC Preparation ─────────┐
                                       │
TMC-2 ─────→ TMC-2 Preparation ────────┼──→ Registration-Ready
                                       │     Representation
IIRS ──────→ Spectral / 2D Preparation ┤
                                       │
LRO ───────→ Reference Preparation ────┘

                  SHARED SIDE

Registration-Ready Pair
        ↓
Scale Handling
        ↓
Local Matcher
        ↓
Candidate Correspondences
        ↓
Geometric Verification
        ↓
Optional Refinement
        ↓
Final Transform
        ↓
Independent Evaluation
```

This decision establishes that:

1. sensor/product identification happens before ordinary matching;
2. OHRC, TMC-2, and IIRS are not blindly processed through one identical path;
3. IIRS requires an explicitly defined 2D registration representation before standard 2D local matching;
4. source/reference metadata should be preserved where available;
5. scale/GSD information should remain available through processing;
6. resampling must not be described as recovered physical resolution;
7. sensor-specific paths may converge to a shared downstream registration contract;
8. preprocessing choices must eventually be justified by controlled evaluation;
9. sensor-specific results should remain separately visible where appropriate;
10. sensor-aware scientific logic belongs in the ChandraMap core/domain layer;
11. API and frontend layers do not own the scientific definition of sensor preprocessing;
12. coordinate transformations introduced during preparation must remain recoverable where required for evaluation.

---

## Rationale

ChandraMap needs both:

```text
Sensor-Specific Physical Correctness
```

and:

```text
Reusable Shared Registration Infrastructure
```

A universal preprocessing path optimizes for code simplicity by hiding physical differences.

A fully independent pipeline per sensor optimizes for freedom at the cost of duplication and inconsistent scientific semantics.

Sensor-aware preparation followed by a shared registration boundary provides the strongest balance.

It permits:

- IIRS-specific spectral representation research;
- conservative OHRC preparation;
- TMC-2-specific preparation where justified;
- reference-side scale handling;
- common matcher interfaces;
- common geometric verification;
- common transformation semantics;
- common independent evaluation;
- comparable benchmarking.

The architecture also allows processing research to evolve without redefining the entire registration system.

---

## Sensor Routing Architecture

The conceptual routing stage is:

```text
                  ┌──────── OHRC ────────┐
                  │                      │
Input + Metadata ─┼──────── TMC-2 ───────┼─→ Shared Registration Boundary
                  │                      │
                  ├──────── IIRS ────────┤
                  │                      │
                  └──── Reference ───────┘
```

Routing should be based on validated information such as:

- explicit product metadata;
- dataset manifest;
- sensor/instrument identifier;
- trusted configuration.

Fragile filename guessing is not the architectural source of truth.

If automatic sensor identification is unavailable, explicit configuration is acceptable.

Unsupported or ambiguous sensor identity should be surfaced rather than silently guessed.

---

## Metadata Preservation

Metadata should be inspected before destructive or lossy preparation.

Potentially relevant metadata includes:

- instrument;
- product identifier;
- acquisition information;
- source dimensions;
- band information;
- pixel size / GSD;
- footprint;
- latitude/longitude;
- projection;
- coordinate reference information;
- viewing geometry;
- spacecraft geometry;
- illumination geometry;
- Sun-angle information;
- product/calibration level.

Not every product is expected to contain all fields.

Available metadata may influence:

- sensor routing;
- scale preparation;
- projection decisions;
- benchmark grouping;
- reference preparation;
- error interpretation;
- result provenance.

> **Product metadata takes precedence over approximate instrument summaries.**

---

## Product Processing Level

Raw, calibrated, geometrically corrected, map-projected, and orthorectified products are not equivalent inputs.

Before preprocessing, ChandraMap should determine where possible:

- Is the product raw?
- Is it calibrated?
- Is it geometrically corrected?
- Is it map-projected?
- Is it orthorectified?
- Does it contain usable geospatial metadata?
- Is an existing standard product more appropriate than reconstructing the same correction inside ChandraMap?

Computer-vision stages should not be asked to rediscover geometry already represented reliably by an appropriate planetary product.

ADR-0006 does not define the complete calibration or map-projection workflow.

---

## OHRC Processing Principles

The OHRC route is conceptually:

```text
OHRC Product
        ↓
Validate Product + Metadata
        ↓
Use Appropriate Standard / Calibrated Product Where Available
        ↓
Preserve Geometry + Scale Information
        ↓
Light Sensor-Appropriate Preparation
        ↓
Reference-Scale Compatibility
        ↓
Registration Representation
```

Possible experiments may investigate:

- panchromatic intensity;
- conservative local contrast preparation;
- gradient representation;
- edge representation;
- structural representation;
- light denoising.

No option is selected as universally superior by this ADR.

OHRC preparation should:

- preserve crater and ridge boundaries;
- avoid unnecessary resampling;
- avoid excessive smoothing;
- preserve coordinate traceability;
- retain GSD/projection context where available;
- justify each preprocessing operation through physical reasoning or benchmark evidence.

High source resolution alone is not evidence that more preprocessing is beneficial.

---

## TMC-2 Processing Principles

The TMC-2 route is conceptually:

```text
TMC-2 Product
        ↓
Validate Product + Metadata
        ↓
Calibration / Projection Awareness
        ↓
Preserve Terrain Structure
        ↓
Reference-Scale Compatibility
        ↓
Registration Representation
```

Possible research options may include:

- panchromatic intensity;
- local contrast preparation;
- gradients;
- edges;
- structural representations;
- terrain/map-projection-aware preparation.

This ADR does not prescribe one fixed TMC-2 recipe.

The same accountability rule applies:

> **Every preprocessing step should improve matching, satisfy a known physical requirement, or be removed.**

---

## IIRS Processing Principles

IIRS requires a dedicated architecture because its product can be hyperspectral rather than a single ordinary 2D panchromatic image.

The conceptual route is:

```text
IIRS Product
        ↓
Inspect Product Type
        │
        ├── Full Hyperspectral Cube
        ├── Individual Band
        ├── Browse Product
        └── Derived Product
        ↓
Spectral / Product-Aware Preparation
        ↓
Create Reproducible 2D Registration Representation
        ↓
Preserve Large-Scale Terrain Structure
        ↓
Reference-Scale Compatibility
        ↓
Local Registration
```

The actual product format must be inspected rather than assumed.

### Selected Band

A single physically justified spectral band may be evaluated as a 2D representation.

**Potential advantages**

- simple;
- interpretable;
- low additional computational complexity.

**Limitations**

- one band may not contain the most stable terrain information;
- a universal best band is not established.

---

### PCA-Derived Representation

Spectral data may be reduced into one or more PCA-derived representations.

**Potential advantages**

- combines information across multiple bands;
- provides data-driven dimensionality reduction.

**Limitations**

- principal variance is not guaranteed to correspond to registration-stable terrain structure;
- component calculation and preprocessing must be reproducible;
- the selected component strategy is not yet decided.

---

### Spectral Composite

Several selected spectral bands may be combined.

**Potential advantages**

- can combine complementary spectral information;
- may produce useful terrain contrast.

**Limitations**

- band selection requires justification;
- arbitrary composites can create difficult-to-reproduce behavior;
- no final combination is selected here.

---

### Structural Representation

The IIRS product may be transformed into a structural representation such as gradients, edges, or another terrain-focused map.

**Potential advantages**

- may reduce dependence on absolute spectral/radiometric appearance;
- may emphasize larger-scale terrain boundaries.

**Limitations**

- coarse source resolution remains coarse;
- gradients cannot recover unresolved fine terrain;
- the representation still requires benchmark validation.

ADR-0006 intentionally does **not** choose a final IIRS representation.

That choice remains an experimental decision.

---

## No Fabricated Fine Detail

> **Preprocessing may improve representation, but it cannot recover lunar surface detail that the original sensor did not resolve.**

Conceptually:

```text
IIRS Source
~80 m/pixel
        ↓
Interpolation / Upsampling
        ↓
More Array Samples
```

does **not** mean:

```text
Recovered Fine Lunar Surface Information
```

Likewise:

```text
TMC-2 resized to NAC dimensions
```

does not become NAC-resolution imagery.

Upsampling can support:

- coordinate compatibility;
- visualization;
- algorithm-specific sampling requirements.

It cannot create new physical observations.

---

## Reference-Side Processing

Sensor-aware processing applies to reference imagery as well as source imagery.

When the reference is much finer than the source, the preferred conceptual direction is often:

```text
Coarse Source
+
Fine Reference
        ↓
Reference Pyramid / Downsampling / Scale Selection
        ↓
Comparable Effective Information Scale
        ↓
Matching
```

rather than:

```text
Coarse Source
        ↓
Large Upsampling
        ↓
Pretend Fine Information Exists
```

ADR-0006 establishes this physical principle but does not define the final reference-pyramid architecture.

Reference preparation must also preserve:

- projection context;
- coordinate transforms;
- product provenance;
- scale information.

---

## Common Registration Boundary

After sensor-specific preparation, source and reference paths converge at a shared conceptual interface:

```text
Prepared 2D Registration Representation
+
Registration Metadata
```

This ADR does not define a concrete class, Python type, JSON schema, or API contract.

The common boundary should preserve enough information for downstream stages to understand:

- representation provenance;
- sensor identity;
- physical scale;
- original/processed dimensions;
- coordinate mapping;
- projection information where available;
- resampling operations;
- representation type.

A common boundary enables shared:

- matcher interfaces;
- candidate-correspondence representation;
- geometric verification;
- transform handling;
- refinement;
- independent evaluation;
- benchmark orchestration.

---

## Common Representation Does Not Mean Identical Processing

Both of the following may ultimately produce a 2D representation:

```text
OHRC Panchromatic Product
        ↓
OHRC Preparation
        ↓
2D Registration Representation
```

and:

```text
IIRS Hyperspectral Product
        ↓
Spectral Representation
        ↓
IIRS Preparation
        ↓
2D Registration Representation
```

The shared downstream dimensionality does not make the upstream physical processing equivalent.

This distinction is fundamental to ADR-0006.

---

## Scale Handling

Sensor-aware preparation and scale handling are related but distinct responsibilities.

### Sensor Preparation

Determines:

- what the source product represents;
- which representation is scientifically meaningful;
- which metadata must be retained.

### Scale Handling

Determines:

- which effective spatial scale should be compared;
- whether a reference pyramid or downsampling is appropriate;
- how processing coordinates map back to source/reference coordinates.

The architecture must avoid treating:

```text
same width × height
```

as equivalent to:

```text
same physical ground scale
```

Two `1024 × 1024` arrays can represent radically different lunar surface extents and information content.

---

## Illumination Considerations

Lunar illumination differences affect more than image brightness.

Changes in Sun geometry can alter:

- shadow position;
- shadow length;
- shadow direction;
- local contrast;
- crater-rim appearance;
- ridge visibility;
- gradient orientation;
- visible terrain structure.

Therefore:

> **Brightness normalization and illumination robustness are not the same problem.**

Histogram equalization, local contrast normalization, or another radiometric transformation may reduce some intensity differences.

They cannot automatically align physically displaced shadows.

---

## Illumination Experiments

Potential benchmarked representations may include:

- raw intensity;
- normalized intensity;
- local contrast variants;
- gradients;
- edges;
- phase/structure-oriented representations;
- shadow-aware representations where justified.

ADR-0006 does not declare any of these approaches universally superior.

The correct scientific pattern is:

```text
Baseline Representation
        ↓
Measure on Defined Stress Pairs
        ↓
Alternative Representation
        ↓
Measure on the Same Compatible Stress Pairs
        ↓
Compare
        ↓
Keep Only Measured or Physically Required Processing
```

Negative results remain valid evidence.

---

## Sun-Angle Stress Testing

Where suitable products exist, benchmark design should compare:

```text
Same / Similar Lunar Region
+
Similar Illumination
```

against:

```text
Same / Similar Lunar Region
+
Substantially Different Illumination
```

Possible metrics may include:

- verified inlier count;
- inlier ratio;
- spatial coverage;
- independent checkpoint RMSE;
- success/failure state;
- runtime.

No numerical result is defined by this ADR.

---

## Geometry and Coordinate Traceability

Preprocessing must preserve meaningful geometry unless a transformation is intentional and recorded.

Avoid silent operations that:

- shift pixels;
- crop without retaining offsets;
- resize without recording scale transforms;
- reproject without retaining coordinate mapping;
- warp without preserving mapping information;
- alter coordinate interpretation.

The conceptual relationship is:

```text
Original Product Coordinates
        ↕
Prepared Representation Coordinates
        ↕
Matcher Coordinates
        ↕
Registration / Evaluation Coordinates
```

The system must preserve enough information to move between relevant spaces.

ADR-0006 does not prescribe the exact transform-chain data structure.

---

## Relationship to Independent Checkpoints

ADR-0003 requires meaningful independent evaluation.

The sensor-processing path must therefore preserve the ability to evaluate the final registration in a declared coordinate system.

Conceptually:

```text
Original Source Product
        ↓
Sensor-Specific Preparation
        ↓
Processing / Matcher Coordinates
        ↓
Correspondence + Transform Estimation
        ↓
Map Final Result to Evaluation Coordinates
        ↓
Independent Checkpoint Evaluation
```

If preprocessing includes:

- cropping;
- resizing;
- pyramid selection;
- reprojection;
- warping;

the corresponding transformations must remain recoverable.

A low error measured only in an undocumented intermediate processing grid is not a defensible source-pixel accuracy claim.

---

## Matcher Independence

Sensor-aware preparation must not be designed so narrowly around SIFT that meaningful comparison with later matchers becomes impossible.

The architecture should conceptually support:

```text
Sensor-Aware Preparation
        ↓
Registration Representation
        ↓
Local Matcher Interface
        │
        ├── SIFT
        ├── ALIKED + LightGlue
        ├── LoFTR
        ├── RIFT-Inspired Method
        ├── CFOG-Inspired Method
        └── Future Matcher
```

Not all candidate matchers necessarily consume exactly the same representation without adaptation.

The architectural requirement is that sensor-specific product interpretation remains distinct from the choice of matcher.

A learned matcher does not eliminate the need for scientifically valid sensor preparation.

---

## Preprocessing and Matcher Gains Must Be Separable

Suppose an improved pipeline changes:

- preprocessing;
- scale handling;
- matcher;
- transform model;
- refinement;

at the same time.

A performance change cannot then be attributed confidently to one component.

Where practical, ChandraMap should use controlled ablations.

Example:

```text
A. Minimal Preparation + SIFT

B. Sensor-Aware Preparation + SIFT

C. Minimal Preparation + Candidate Matcher

D. Sensor-Aware Preparation + Candidate Matcher
```

This helps distinguish:

- preprocessing benefit;
- matcher benefit;
- interaction effects.

No result is implied by this ADR.

---

## Benchmark and Ablation Requirements

A representative preprocessing ablation may use:

| Experiment         | Sensor Preparation              | Matcher               |
| ------------------ | ------------------------------- | --------------------- |
| Baseline           | Minimal / reference preparation | SIFT                  |
| Variant A          | Sensor-aware representation A   | SIFT                  |
| Variant B          | Sensor-aware representation B   | SIFT                  |
| Matcher comparison | Same selected preparation       | `<candidate matcher>` |

The experiment should keep other major variables fixed where practical.

For every optional preprocessing step, ask:

- What physical or algorithmic problem does this step address?
- Which sensor needs it?
- Does it preserve geometry?
- Does it preserve useful terrain structure?
- Does it preserve coordinate traceability?
- Does it improve a relevant benchmark?
- Does it create new failure modes?
- Is it reproducible?

> **Every preprocessing step should improve matching, satisfy a known physical requirement, or be removed.**

---

## Per-Sensor Reporting

Sensor behavior should remain visible in benchmark reports.

Prefer:

```text
OHRC Results
TMC-2 Results
IIRS Results
```

before relying on:

```text
Overall Aggregate
```

A single aggregate can conceal:

- strong OHRC behavior;
- weaker TMC-2 behavior;
- experimental or failing IIRS behavior.

Where an aggregate is reported, the contributing sensor populations and failure handling must remain clear.

---

## Illustrative Benchmark Matrix

A benchmark report may include a structure such as:

| Sensor | Representation        | Matcher | Inlier Ratio | Checkpoint RMSE | Coverage | Runtime |
| ------ | --------------------- | ------- | -----------: | --------------: | -------: | ------: |
| OHRC   | `<representation>`    | SIFT    |        `TBD` |           `TBD` |    `TBD` |   `TBD` |
| TMC-2  | `<representation>`    | SIFT    |        `TBD` |           `TBD` |    `TBD` |   `TBD` |
| IIRS   | `<2D representation>` | SIFT    |        `TBD` |           `TBD` |    `TBD` |   `TBD` |

This table is illustrative only.

Every real result must also state:

- benchmark/test-pair population;
- metric units;
- coordinate space where relevant;
- preprocessing configuration;
- product provenance;
- failure handling.

Do not populate placeholder values without measured evidence.

---

## Significant Preprocessing Changes Affect Comparability

Benchmark comparability can be broken by changes to:

- IIRS representation;
- selected spectral inputs;
- calibration/product level;
- normalization;
- cropping;
- scale preparation;
- reference preprocessing;
- projection handling;
- source/reference resampling.

If a significant preprocessing change occurs:

1. record the new configuration;
2. identify affected sensors;
3. rerun relevant benchmarks where comparison is needed;
4. state limitations on comparison with older results;
5. create a new ADR when the change alters architectural policy rather than only experimental configuration.

Do not silently change sensor defaults and treat old/new results as equivalent.

---

## Sensor-Aware Configuration

Configuration should support sensor-specific decisions without scattering hidden constants throughout the scientific code.

Conceptually:

```text
Common Registration Configuration
+
Sensor-Specific Preparation Configuration
```

Potential areas include:

### OHRC

- representation choice;
- conservative denoising option;
- contrast option;
- scale preparation.

### TMC-2

- representation choice;
- projection handling;
- contrast option;
- scale preparation.

### IIRS

- input product/representation type;
- band-selection strategy;
- dimensionality-reduction strategy;
- composite strategy;
- structural representation;
- normalization.

ADR-0006 does not define an exact configuration file schema.

---

## Defaults Must Be Explicit

If ChandraMap later adopts a default preparation path for a sensor, that default must be:

- documented;
- reproducible;
- version-aware;
- benchmarked where it affects scientific behavior;
- represented in configuration/provenance.

A material change such as selecting a different default IIRS representation can alter benchmark meaning.

Such changes must not be silent.

---

## Sensor Metadata Provenance

Where available, benchmark artifacts should preserve information such as:

- instrument;
- product identifier;
- product type/level;
- source dimensions;
- reference dimensions;
- source GSD;
- reference GSD;
- projection;
- footprint;
- illumination metadata;
- representation type;
- preprocessing configuration;
- resampling operations;
- crop offsets;
- coordinate-transform provenance.

No concrete values are defined by this ADR.

---

## Reproducibility Requirements

Sensor-aware experiments should preserve, where applicable:

- source product identifier;
- reference product identifier;
- sensor;
- source product type;
- reference product type;
- source GSD;
- reference GSD;
- source/reference projection information;
- illumination metadata;
- source representation;
- IIRS band/component/composite definition;
- preprocessing operations;
- preprocessing parameters;
- resize/downsample factors;
- crop coordinates;
- coordinate-transform chain;
- matcher configuration;
- geometry configuration;
- checkpoint/evaluation configuration;
- scientific version;
- software revision;
- dependency/environment information;
- hardware when runtime is reported.

A result is difficult to reproduce if the preprocessing representation is not recorded.

---

## Failure Modes

The following are expected or plausible failure categories, not claims that they have already occurred.

### OHRC

Potential issues include:

- excessive source/reference resolution difference;
- fine structure absent from the reference;
- strong shadow changes;
- viewpoint differences;
- relief-related distortion;
- over-processing that destroys useful detail.

---

### TMC-2

Potential issues include:

- illumination variation;
- scale mismatch;
- low-feature terrain;
- repetitive crater patterns;
- projection/product inconsistencies;
- insufficient structural overlap.

---

### IIRS

Potential issues include:

- inappropriate 2D representation;
- coarse spatial resolution;
- cross-modal appearance differences;
- weak local structure;
- accidental full-cube misuse;
- unstable band/component selection;
- representations tuned to favorable pairs only.

---

### Shared

Potential issues include:

- invalid or missing metadata;
- sensor misclassification;
- projection mismatch;
- excessive preprocessing;
- coordinate-traceability loss;
- incorrect scale interpretation;
- poor overlap;
- reference representation incompatible with source information content.

---

## Failure Reporting

Failures should retain enough sensor context to be diagnostically useful.

A failure record should ideally identify:

- sensor;
- source representation;
- reference representation;
- physical scale information where available;
- preprocessing route;
- matcher;
- geometric stage reached;
- available metrics;
- unavailable metrics;
- failure reason when established.

Prefer:

```text
IIRS-derived representation produced insufficient verified geometric support
for this pair.
```

over a generic message such as:

```text
matching failed
```

when the more precise state is actually known.

Do not infer an unproven root cause merely from the failure stage.

---

## Anti-Patterns

### One Blind Function for Every Sensor

```text
preprocess(image):
    grayscale
    resize
    normalize
```

with no sensor/product interpretation.

---

### Full IIRS Cube Into a Standard 2D Matcher

Passing hyperspectral data directly to a matcher that expects one 2D registration image without defining a representation.

---

### Upsampling to "Increase Resolution"

Increasing array dimensions and then claiming that new physical lunar detail has been recovered.

---

### Metadata Destruction

Reading GSD, projection, or coordinate metadata and discarding it before scale handling or evaluation.

---

### Hidden Sensor Defaults

Selecting a spectral band, normalization method, or representation implicitly without recording the choice.

---

### API-Only Sensor Logic

Implementing scientific sensor decisions solely inside an HTTP route or presentation layer.

---

### Sensor Guessing by Filename

Using filename patterns or visual appearance as the canonical sensor-identification mechanism when validated metadata or explicit configuration is available.

---

### Unmeasured Preprocessing Stack

Applying many filters because they are common computer-vision operations without isolating whether they improve lunar registration.

---

### Same Matrix Size = Same Scale

Assuming two resized images are physically comparable because their pixel dimensions match.

---

### Normalization = Sun-Angle Invariance

Treating histogram/contrast normalization as proof that shadow geometry has been solved.

---

## Testing Strategy

### Sensor Identification Tests

Verify that validated sensor/product metadata or explicit configuration selects the expected processing route.

Tests should also cover:

- unsupported sensor identity;
- ambiguous identity;
- missing required metadata;
- invalid configuration.

---

### OHRC Preparation Tests

Verify:

- accepted input handling;
- expected representation dimensionality;
- relevant metadata preservation;
- geometry/coordinate traceability;
- deterministic/reproducible preparation where applicable.

No exact preprocessing algorithm is mandated here.

---

### TMC-2 Preparation Tests

Verify:

- accepted input handling;
- representation contract;
- metadata preservation;
- coordinate traceability;
- sensor-specific path separation where required.

---

### IIRS Representation Tests

Verify that:

- full-cube handling is explicit;
- an explicit representation choice is required where necessary;
- the generated 2D representation is reproducible;
- output dimensions/metadata are correct;
- parent-product provenance is retained;
- chosen band/component/composite information is preserved;
- unsupported product forms do not silently fall through to generic grayscale handling.

---

### Cross-Sensor Contract Tests

Verify that supported sensor preparation paths produce data consumable by the shared registration pipeline without forcing downstream geometry/evaluation code to duplicate sensor parsing.

---

### Coordinate-Traceability Tests

For operations such as:

- crop;
- resize;
- pyramid selection;
- reprojection;

verify that known processing coordinates can be mapped back to their required original/evaluation coordinates.

This directly supports ADR-0003.

---

### Regression Tests

When default preprocessing or configuration changes, tests should detect unintended changes to:

- selected sensor route;
- representation provenance;
- coordinate transforms;
- serialization/provenance fields;
- benchmark configuration.

Tests do not replace scientific benchmarks.

---

## Testing and Validation Plan

### Stage 1 — Baseline OHRC / TMC-2

Start with suitable known-overlap visible/panchromatic pairs.

Use minimal, well-documented preparation:

```text
Input
→ Necessary Scale Preparation
→ SIFT
→ Geometric Verification
→ Registration
→ Independent Evaluation
```

Establish a reproducible baseline before adding preprocessing complexity.

---

### Stage 2 — Sensor-Aware Visible/Panchromatic Experiment

Introduce one justified alternative preparation at a time.

Compare on the same compatible pair population using the same downstream matcher and evaluation protocol.

---

### Stage 3 — IIRS Product Inspection

Determine the actual available product form:

- full hyperspectral cube;
- individual band;
- browse product;
- derived product;
- another documented representation.

Do not design the implementation around an assumed format.

---

### Stage 4 — Simple IIRS Representations

Evaluate simple, reproducible options first, such as:

- selected band;
- PCA-derived component;
- composite;
- structural representation.

Avoid prematurely introducing an unnecessarily complex learned spectral pipeline before simpler representations are measured.

---

### Stage 5 — Illumination Stress

Compare intensity-oriented representations against one or more structure-oriented alternatives on defined illumination-stress pairs.

Report degradation and failures honestly.

---

### Stage 6 — Matcher Independence

Verify that the preparation architecture can support:

- SIFT baseline;
- later candidate matchers;

without duplicating sensor/product interpretation in each matcher implementation.

---

## Acceptance Criteria

ADR-0006 is successfully reflected in the architecture when:

- [ ] Sensor/product identity is established before ordinary matching
- [ ] OHRC, TMC-2, and IIRS do not blindly share one identical preprocessing path
- [ ] Full IIRS cubes are not sent directly to standard 2D matchers without an explicit representation step
- [ ] Relevant sensor/product metadata remains available after preparation
- [ ] Source/reference GSD is retained where available
- [ ] Scale handling does not claim information recovery through upsampling
- [ ] Supported sensor paths produce a documented registration-ready representation
- [ ] Shared matcher/geometry/evaluation stages remain reusable where scientifically appropriate
- [ ] Coordinate transformations introduced during preparation remain traceable
- [ ] Independent checkpoint evaluation remains possible after preprocessing
- [ ] Benchmark artifacts identify sensor and registration representation
- [ ] Results can be separated by sensor
- [ ] Sensor-aware processing can execute through the core engine without requiring API logic
- [ ] Preprocessing variants can be benchmarked against a controlled baseline
- [ ] Unsupported assumptions are surfaced rather than silently guessed
- [ ] IIRS representation choices are recorded explicitly
- [ ] Significant preprocessing changes can be detected as benchmark-comparability changes

No numerical performance threshold is established by this ADR.

---

## Consequences

### Positive Consequences

The decision provides:

- stronger alignment with sensor physics;
- safer handling of hyperspectral IIRS products;
- clearer separation of representation design from matching;
- better preservation of GSD and product metadata;
- clearer failure attribution;
- more defensible benchmark interpretation;
- easier sensor-specific experimentation;
- shared reusable matching, geometry, and evaluation infrastructure;
- reduced risk of false spatial-resolution claims;
- stronger coordinate traceability;
- easier future addition of new sensors;
- matcher-independent sensor preparation;
- better visibility into per-sensor performance.

---

### Negative Consequences / Trade-offs

The decision introduces:

- additional preparation code paths;
- larger configuration surface;
- additional unit and integration testing;
- more benchmark combinations;
- ongoing IIRS representation research;
- maintenance requirements for sensor defaults;
- dependence on product metadata that may be incomplete;
- added provenance requirements;
- additional complexity in coordinate-transform tracking.

These costs are accepted because treating physically different sensors as one generic image type creates larger scientific risks.

---

### Neutral Consequences

- SIFT remains the V1 local-matching baseline downstream of preparation.
- Future matchers can reuse the same sensor-aware architecture.
- Several preprocessing choices remain experimental.
- Exact scale-selection policy remains undecided.
- Exact projection/orthorectification policy remains outside this ADR.
- Sensor-aware preparation does not imply that every sensor requires unique downstream geometry.

---

## Risks and Mitigations

| Risk                                                      | Why It Matters                                               | Mitigation                                                               |
| --------------------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------------------ |
| IIRS is treated as ordinary grayscale imagery             | Ignores hyperspectral modality and representation provenance | Require an explicit registration-friendly 2D representation              |
| Upsampling is presented as recovered detail               | Creates scientifically false resolution claims               | Preserve GSD and document resampling as sampling-grid change             |
| Excessive denoising removes terrain structure             | Can reduce repeatable crater/ridge features                  | Keep processing minimal and benchmark optional operations                |
| Contrast normalization is treated as Sun-angle invariance | Shadow geometry remains physically different                 | Run illumination stress experiments and evaluate structural alternatives |
| Sensor routes become duplicated complete pipelines        | Creates maintenance and benchmark inconsistency              | Rejoin at the shared registration boundary                               |
| Product metadata is discarded                             | Weakens scale handling and error interpretation              | Preserve relevant metadata through the processing contract               |
| Crop/resize offsets are lost                              | Invalidates source-coordinate evaluation                     | Preserve explicit coordinate-transform traceability                      |
| IIRS representation is tuned only on favorable pairs      | Can overfit the benchmark                                    | Evaluate across representative modality/scale stress cases               |
| One aggregate hides sensor failure                        | Can produce misleading project-level conclusions             | Report per-sensor results first                                          |
| Preprocessing silently changes between experiments        | Breaks reproducibility                                       | Make preprocessing configuration explicit and version-aware              |
| Sensor identification is guessed incorrectly              | Sends data through an invalid processing path                | Prefer validated metadata/manifests or explicit configuration            |
| Shared representation loses modality provenance           | Makes results difficult to interpret                         | Retain sensor/product/representation metadata downstream                 |

These are architectural risks; this ADR does not claim that they have already occurred.

---

## Deferred Decisions

ADR-0006 intentionally does not select:

- exact OHRC preprocessing pipeline;
- exact TMC-2 preprocessing pipeline;
- exact IIRS 2D representation;
- final IIRS band-selection method;
- final IIRS spectral band;
- PCA component count;
- final PCA configuration;
- composite formulation;
- hyperspectral dimensionality-reduction method;
- denoising method;
- exact filter kernels;
- contrast-enhancement method;
- histogram-normalization method;
- illumination-correction method;
- shadow-mask method;
- gradient representation;
- edge representation;
- phase-based representation;
- structural representation;
- exact map-projection workflow;
- exact orthorectification workflow;
- DEM-assisted preprocessing;
- reference-pyramid structure;
- scale-selection policy;
- exact resampling factors;
- sensor-specific matcher policy;
- learned preprocessing;
- lunar-specific learned representation;
- future global-retrieval representations;
- exact future-sensor plugin architecture;
- V2/V3/V4 sensor-processing algorithms.

These require controlled experiments, additional specifications, or future ADRs.

---

## Honest Reporting Requirements

Sensor-aware processing must be described precisely.

Avoid:

> `IIRS was enhanced to OHRC resolution.`

Prefer:

> `The IIRS-derived representation was resampled for processing; its physical spatial information remains limited by the source product.`

Avoid:

> `Normalization made the images invariant to Sun angle.`

Prefer:

> `The normalization method was evaluated for robustness to illumination differences; Sun-angle-dependent shadow geometry remains a separate source of variation.`

Avoid:

> `All sensors use the same registration image.`

Prefer:

> `Each sensor reaches a registration-ready 2D representation through a sensor-appropriate preparation path.`

Avoid:

> `The higher pixel dimensions provide more lunar detail.`

Prefer:

> `The image was resampled to a different grid; no additional physical terrain information is implied.`

---

## Relationship to Future ChandraMap Versions

The sensor-aware principle is intended to survive beyond V1.

V1 may use:

```text
Sensor-Aware Preparation
        ↓
SIFT Baseline
        ↓
Geometric Verification
        ↓
Registration
        ↓
Independent Evaluation
```

A later scientific version may use:

```text
Sensor-Aware Preparation
        ↓
Alternative Matcher
        ↓
Improved Geometry / Refinement
        ↓
Independent Evaluation
```

Future retrieval-enabled architecture may eventually introduce:

```text
Sensor-Aware Global Representation
        ↓
Retrieval
        ↓
Candidate Region
        ↓
Sensor-Aware Local Preparation
        ↓
Local Registration
```

ADR-0006 does not define the algorithms used by V2, V3, or V4.

The durable architectural property is:

> **New scientific methods may change how prepared data is matched, but they should not erase the requirement to interpret each source product according to its physical sensor characteristics.**

---

## Future Sensor Extensibility

The architecture should permit an additional scientifically appropriate lunar sensor to join through:

```text
New Sensor Product
        ↓
New Sensor-Specific Preparation
        ↓
Existing Registration Boundary
        ↓
Existing / Compatible Matcher
        ↓
Shared Geometry
        ↓
Shared Evaluation
```

Potential future research may consider additional lunar products, including other LRO or Kaguya/SELENE data where appropriate.

Their support is not established by this ADR.

New sensors should not require duplication of shared transformation or evaluation logic unless their geometry genuinely requires a different architecture.

---

## Revisit Conditions

ADR-0006 should be revisited or superseded if:

- controlled evidence demonstrates that one validated universal representation preserves the required information across all supported sensors;
- new sensor products fundamentally change preparation requirements;
- end-to-end learned preprocessing changes the architectural boundary between representation and matching;
- the intended IIRS workflow no longer operates on spectral products;
- standardized planetary processing produces a scientifically justified common product representation;
- benchmarks show that the current sensor-routing boundary adds complexity without preserving meaningful distinctions;
- future sensor geometry requires different registration contracts;
- scientific-version architecture changes the definition of the common representation boundary.

A substantial architectural change should create a new ADR that supersedes ADR-0006 rather than rewriting this accepted decision.

---

## References

### Architecture Decision Records

- [ADR index](README.md)
- [ADR template](ADR_TEMPLATE.md)
- [ADR-0001 — V1 Known-Overlap First](0001-v1-known-overlap-first.md)
- [ADR-0002 — SIFT as the V1 Local-Matching Baseline](0002-sift-as-v1-baseline.md)
- [ADR-0003 — Independent Checkpoints for Registration Evaluation](0003-independent-checkpoints.md)
- [ADR-0004 — No Global Retrieval in V1](0004-no-global-retrieval-in-v1.md)
- [ADR-0005 — Core Engine Separated from API](0005-core-engine-separated-from-api.md)

### Architecture Documentation

- [System Overview](../architecture/system-overview.md)
- [V1 Pipeline](../architecture/v1-pipeline.md)
- [Core Engine Architecture](../architecture/core-engine-architecture.md)
- [Data Flow](../architecture/data-flow.md)
- [Output Flow](../architecture/output-flow.md)

### Sensor Documentation

- [Sensor Overview](../sensors/overview.md)

Additional sensor-specific documentation should be referenced when the corresponding repository paths are established.

### Research Documentation

Relevant established research documents include:

- [Research Questions](../research/research-questions.md)
- [Known Research Limitations](../research/known-limitations.md)
- [Experiment Methodology](../research/experiment-methodology.md)

### External Reference Areas

Implementation and research should consult authoritative material where relevant, including:

- ISRO Chandrayaan-2 payload documentation;
- ISRO Chandrayaan-2 science/product documentation;
- ISSDC/PRADAN product documentation;
- LROC NAC/WAC documentation;
- USGS ISIS planetary image-processing documentation;
- official OpenCV image-processing documentation;
- primary hyperspectral image-registration literature;
- primary multimodal remote-sensing registration literature.

Exact external URLs are intentionally not fabricated in this ADR.

---

## Final Decision Statement

ChandraMap adopts the following permanent architectural principle:

```text
Input Lunar Product
        ↓
Identify Sensor / Product
        ↓
Inspect and Preserve Metadata
        ↓
Select Sensor-Appropriate Preparation
        │
        ├── OHRC Preparation
        ├── TMC-2 Preparation
        ├── IIRS Spectral / 2D Representation
        └── Reference Preparation
        ↓
Registration-Ready Representation
        ↓
Physically Meaningful Scale Handling
        ↓
Local Correspondence
        ↓
Geometric Verification
        ↓
Optional Refinement
        ↓
Final Transformation
        ↓
Independent Evaluation
```

Therefore:

> **ChandraMap does not force physically different lunar sensors through one identical preprocessing path. Each product is interpreted and prepared according to its sensor characteristics, then joined to reusable downstream registration infrastructure.**

And:

> **Sensor-aware processing does not mean inventing detail, hiding failures, or accumulating filters. Every processing step must preserve scientific meaning and ultimately justify itself through physical necessity, controlled evidence, or both.**

<!-- Source request specification: :contentReference[oaicite:0]{index=0} -->
