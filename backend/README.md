# Backend

> **ChandraMap backend documentation**
> **Status:** `[Planned / Partially Defined]`
> **Implementation status:** `[Not fully provided]`
> **Research integration:** `[Planned]`

The ChandraMap backend is the software layer responsible for supporting the computational and application-facing parts of the lunar image correspondence and registration system.

ChandraMap is not a generic image-processing web application. Its backend exists to connect application interfaces with a scientific computer-vision pipeline involving:

- lunar image ingestion
- sensor-aware preprocessing
- multi-scale image handling
- image correspondence
- geometric verification
- transformation estimation
- registration
- residual analysis
- quantitative evaluation
- future global retrieval
- reproducible experiment execution
- research-result presentation

The backend should therefore preserve the distinction between **application infrastructure**, **research algorithms**, and **scientific evaluation**.

The supplied project material emphasizes that the core deliverable is reliable correspondence and registration with measurable outputs rather than only a visual mosaic. The recommended implementation path begins with a small, measurable end-to-end pair before expanding toward retrieval, stronger matchers, additional sensors, and larger-scale processing.

---

## 1. Backend Purpose

The backend provides the application and orchestration layer around ChandraMap's research pipeline.

At a conceptual level:

```text
                         ChandraMap
                             │
              ┌──────────────┴──────────────┐
              │                             │
              ▼                             ▼
          Frontend                       Backend
                                            │
                  ┌─────────────────────────┼─────────────────────────┐
                  │                         │                         │
                  ▼                         ▼                         ▼
              API Layer              Processing Layer          Data / Metadata
                  │                         │                         │
                  ▼                         ▼                         ▼
             Requests                 Registration Jobs          Inputs / Results
                                            │
                                            ▼
                                  Computer Vision Pipeline
                                            │
                    ┌───────────────────────┼───────────────────────┐
                    │                       │                       │
                    ▼                       ▼                       ▼
               Preprocessing          Correspondence          Geometry
                    │                       │                       │
                    └───────────────────────┼───────────────────────┘
                                            │
                                            ▼
                                      Evaluation
                                            │
                                            ▼
                                       Results
```

**Important:** The architecture above is a conceptual architecture unless corresponding implementation files are present in the repository. The supplied project material does not provide enough evidence to claim a specific backend framework, database, queue system, endpoint implementation, or deployment platform.

---

## 2. What the Backend Is Responsible For

The backend should provide a controlled boundary between the application and the research pipeline.

Its responsibilities can include:

### Application responsibilities

- accepting supported requests
- validating request parameters
- validating image and metadata inputs
- initiating processing
- tracking processing state
- returning structured results
- exposing experiment outputs
- handling errors consistently

### Computer-vision orchestration responsibilities

- selecting the appropriate processing route
- passing images and metadata to research components
- coordinating preprocessing
- invoking correspondence methods
- invoking geometric verification
- invoking registration
- collecting evaluation outputs

### Research infrastructure responsibilities

- preserving experiment configuration
- recording method configuration
- exposing reproducible results
- separating candidate matches from verified inliers
- preserving failure information
- storing or referencing experiment artifacts

### Metadata responsibilities

Where metadata exists, the backend should preserve relevant information such as:

- sensor
- image/product identity
- pixel scale/GSD
- image dimensions
- geographic information
- footprint
- map projection
- illumination information
- viewing geometry
- preprocessing configuration

The project feedback specifically emphasizes preserving pixel scale, footprint, projection, and lighting/viewing metadata where available.

---

## 3. What the Backend Is Not

The backend should not be confused with the research algorithms themselves.

It is not inherently:

- SIFT
- ALIKED
- LightGlue
- LoFTR
- RIFT
- CFOG
- FAISS
- RANSAC
- a homography estimator
- an affine estimator
- a sub-pixel refinement algorithm
- a DEM
- a lunar map
- a registration algorithm by itself

Instead, the backend provides the software boundary through which such components can eventually be orchestrated.

A useful conceptual separation is:

```text
Backend
   │
   ├── API / orchestration
   ├── validation
   ├── configuration
   ├── job lifecycle
   ├── metadata
   └── result handling
             │
             ▼
       Research Pipeline
             │
             ├── preprocessing
             ├── local correspondence
             ├── geometric verification
             ├── registration
             └── evaluation
```

---

## 4. Current Backend vs Research Pipeline

This distinction is important throughout the repository.

### Current Backend

The exact backend implementation details are **not fully specified in the supplied repository context**.

Therefore, the following must remain `[TBD]` until the actual backend source files establish them:

- backend framework
- application entry point
- endpoint names
- request schemas
- response schemas
- database technology
- job queue
- worker architecture
- authentication
- deployment platform
- container configuration
- storage implementation

No specific framework or service should be assumed without repository evidence.

### Research Pipeline

The research pipeline contains the computer-vision methodology being developed and evaluated.

Relevant research directions include:

- SIFT baseline
- reference-image scale pyramid
- gradient/structural representations
- affine vs homography
- residual analysis
- sub-pixel refinement
- global retrieval
- FAISS
- ALIKED
- LightGlue
- LoFTR
- RIFT/CFOG
- IIRS representations
- DEM-aware registration

### Future Backend Integration

Future research components may be exposed through backend services after they have been independently implemented and benchmarked.

They should not be presented as production backend capabilities before that evidence exists.

---

## 5. Scientific Pipeline Context

The backend must support the actual scientific flow rather than treating registration as a single opaque operation.

A conceptual end-to-end pipeline is:

```text
Input Images
      ↓
Sensor Identification / Metadata
      ↓
Sensor-Aware Preprocessing
      ↓
Multi-Scale Representation
      ↓
Candidate Search / Local Matching
      ↓
Candidate Correspondences
      ↓
Geometric Verification
      ↓
Verified Inliers
      ↓
Transformation Estimation
      ↓
Optional Sub-Pixel Refinement
      ↓
Final Transformation
      ↓
Independent Evaluation
      ↓
Registered Output + Metrics
```

The project feedback specifically recommends the sequence:

```text
LOCAL MATCHES
      ↓
RANSAC
      ↓
INLIERS
      ↓
SUB-PIXEL TIE POINTS
      ↓
FINAL MODEL
      ↓
REGISTERED IMAGE
```

and warns against treating matcher confidence as proof of geometric correctness.

---

## 6. Backend Data Flow

A conceptual backend request flow can be represented as:

```text
Client
  │
  ▼
API Request
  │
  ▼
Input Validation
  │
  ▼
Metadata Validation
  │
  ▼
Processing Configuration
  │
  ▼
Registration Pipeline
  │
  ├── Preprocessing
  ├── Retrieval / Matching
  ├── Geometric Verification
  ├── Transformation
  ├── Refinement
  └── Evaluation
  │
  ▼
Result Assembly
  │
  ├── Correspondences
  ├── Inliers
  ├── Transformation
  ├── Residuals
  ├── Coverage
  ├── Metrics
  └── Registered Preview
  │
  ▼
Structured Response
```

The backend should preserve enough information for the frontend and research workflow to distinguish:

```text
Candidate Matches
        ↓
Verified Inliers
        ↓
Independent Evaluation
```

These are scientifically different outputs.

---

## 7. Sensor-Aware Backend Design

The backend should not assume that OHRC, TMC-2, and IIRS are interchangeable image sources.

The supplied project feedback explicitly recommends separate sensor-aware paths because these inputs have different spatial and sensing characteristics.

Conceptually:

```text
                    Input
                      │
          ┌───────────┼───────────┐
          │           │           │
          ▼           ▼           ▼
        OHRC        TMC-2        IIRS
          │           │           │
          ▼           ▼           ▼
      Sensor-Specific Preparation
          │           │           │
          └───────────┼───────────┘
                      ▼
             Common Registration
                  Interface
```

The exact implementation of those routes is `[TBD]` unless established by backend source code.

---

## 8. OHRC Processing Context

OHRC imagery is high-detail visible panchromatic imagery.

The backend should preserve relevant product information such as:

- source product
- image dimensions
- pixel scale
- projection
- footprint
- acquisition metadata
- viewing/illumination metadata when available

Fine registration should not be assumed merely because the imagery has high spatial resolution.

The challenge product metadata should remain authoritative for the actual pixel scale used in a benchmark.

---

## 9. TMC-2 Processing Context

TMC-2 provides a different spatial scale from OHRC.

The backend should therefore avoid silently treating the two products as having identical pixel geometry.

Relevant metadata should remain attached to the processing request.

Potential future processing may include:

- scale-aware preparation
- reference-image downsampling
- structural representation
- geometric metadata use
- DEM-aware processing where supported

The exact implementation remains `[TBD]`.

---

## 10. IIRS Processing Context

IIRS should not automatically be treated as an ordinary 2D camera image.

The project feedback recommends first determining the available IIRS product representation and then testing simple 2D representations such as:

- selected bands
- PCA
- composites
- structural representations

rather than immediately treating the entire hyperspectral cube as a conventional image-matching input.

The backend should therefore allow the representation choice to remain explicit.

Conceptually:

```text
IIRS Product
     ↓
Representation Selection
     ├── Selected Band
     ├── PCA / Reduced Representation
     ├── Composite
     └── Structural Representation
              ↓
        Registration Input
```

This is a research configuration, not a claim that these routes are currently implemented.

---

## 11. Scale-Aware Processing

Scale is a first-class backend input whenever registration depends on imagery with substantially different ground sampling distances.

The backend should preserve:

- source pixel scale
- reference pixel scale
- scale ratio where known
- selected processing scale
- pyramid level
- resampling configuration

The project guidance explicitly states that upsampling does not recover missing spatial detail and recommends comparing imagery at physically meaningful scales before fine alignment.

A conceptual flow is:

```text
Source Image
     │
     ├── Pixel Scale
     │
     ▼
Reference Image
     │
     ├── Pixel Scale
     │
     ▼
Scale Comparison
     │
     ▼
Reference Pyramid / Multi-Scale Search
     │
     ▼
Fine Matching
```

---

## 12. Illumination-Aware Processing

Lunar illumination changes can alter shadows and apparent terrain structure.

The backend should therefore treat illumination handling as a configurable research stage rather than assuming normalization solves the problem.

Possible research configurations include:

- raw grayscale
- contrast normalization
- gradient representation
- edge representation
- structural representation
- other explicitly documented photometric processing

The project feedback emphasizes that brightness normalization cannot undo geometric shadow changes caused by different Sun angles.

The backend should preserve the selected preprocessing configuration in the result metadata.

---

## 13. Global Retrieval Integration

Global retrieval is optional and should not automatically be inserted into every request.

The project feedback recommends using reliable geographic metadata to restrict the search when available and using image-based global retrieval when location information is unavailable or insufficient.

Conceptually:

```text
Input
  │
  ▼
Reliable Geographic Metadata?
  │
  ├── Yes ──► Restrict Candidate Search
  │
  └── No ───► Global Retrieval
                    │
                    ▼
                 Top-K
                    │
                    ▼
             Local Matching
```

Future retrieval infrastructure may include FAISS.

FAISS should remain a candidate-search/indexing component rather than a registration algorithm.

---

## 14. FAISS Backend Integration

A future FAISS-enabled backend may follow:

```text
Query Image
     ↓
Global Descriptor
     ↓
FAISS Index
     ↓
Top-K Candidate IDs
     ↓
Candidate Metadata
     ↓
Local Matching
     ↓
Geometric Verification
     ↓
Registration
```

The backend may eventually expose:

- retrieval configuration
- candidate list
- similarity scores
- candidate metadata
- downstream registration status

However, these interfaces are:

> `[Planned]`

unless corresponding backend implementation exists.

The project feedback explicitly separates global descriptor generation, offline reference indexing, metadata, and online Top-K retrieval from the later local matching stage.

---

## 15. Local Correspondence Integration

The backend may eventually support multiple research paths.

### Path A — SIFT

```text
Image Pair
   ↓
SIFT
   ↓
Descriptor Matching
   ↓
Candidate Matches
```

### Path B — ALIKED + LightGlue

```text
Image Pair
   ↓
ALIKED
   ↓
Sparse Features
   ↓
LightGlue
   ↓
Candidate Matches
```

### Path C — LoFTR

```text
Image Pair
   ↓
LoFTR
   ↓
Candidate Correspondences
```

These paths should not be merged conceptually into a single "feature extraction" step.

The project feedback specifically distinguishes SIFT, ALIKED + LightGlue, and LoFTR as different local matching paths.

The backend should therefore represent the selected research method explicitly.

---

## 16. Candidate Matches vs Verified Inliers

This distinction must be preserved in backend interfaces.

### Candidate Matches

Produced by a correspondence method.

```text
Candidate Matches
```

These are hypotheses.

### Verified Inliers

Produced after geometric verification.

```text
Candidate Matches
       ↓
RANSAC / Geometric Verification
       ↓
Verified Inliers
```

A backend response should not label all matcher outputs as "correct matches".

The project feedback explicitly recommends renaming "High Confidence Matches" to "Candidate Matches" and letting geometric verification determine verified inliers.

---

## 17. Geometric Verification

Geometric verification is a central stage in the backend-supported pipeline.

Conceptually:

```text
Candidate Correspondences
          ↓
      RANSAC
          ↓
     Initial Model
          ↓
       Inliers
          ↓
  Transformation Model
```

Potential transformation models include:

- similarity
- affine
- homography
- other future models where scientifically justified

The exact model should be selected through experiment rather than hard-coded as universally correct.

---

## 18. Registration Geometry

The backend should not assume that one global homography always describes lunar image geometry.

The project feedback notes that the Moon is not a flat plane and that residual variation may indicate the need for local/piecewise warping or sensor/DEM geometry.

A conceptual progression is:

```text
Initial Correspondences
        ↓
Simple Geometric Model
        ↓
Residual Analysis
        ↓
Does the model explain the residuals?
        │
        ├── Yes ──► Continue
        │
        └── No ───► Investigate
                     Local / Piecewise /
                     Sensor / DEM Geometry
```

This should be represented as an experimental decision rather than an automatic assumption.

---

## 19. Sub-Pixel Refinement

Sub-pixel refinement is downstream of verified correspondences.

The recommended order is:

```text
Candidate Matches
      ↓
RANSAC
      ↓
Verified Inliers
      ↓
Sub-Pixel Tie-Point Refinement
      ↓
Final Transformation Refit
```

The backend should preserve the distinction between:

- initial correspondence coordinates
- refined coordinates
- initial transformation
- final transformation

Sub-pixel refinement should not be applied blindly to all candidate matches.

---

## 20. Evaluation Outputs

The backend should expose scientifically meaningful outputs rather than only an image overlay.

Potential result fields include:

### Correspondence

- candidate match count
- verified inlier count
- inlier ratio
- match coordinates
- inlier mask

### Spatial distribution

- coverage
- grid coverage
- convex-hull coverage
- spatial distribution

### Geometry

- transformation model
- transformation parameters
- residual vectors
- residual magnitudes

### Accuracy

- independent check-point RMSE
- median error
- P90/P95 error
- maximum error
- ground error where scientifically meaningful

### System

- runtime
- processing status
- failure state
- configuration
- method used

The supplied feedback identifies inlier count, inlier ratio, spatial coverage, independent check-point RMSE, ground error when meaningful, runtime, and failure rate as useful evaluation dimensions.

---

## 21. Independent Evaluation

The backend should not calculate registration quality only from points used to fit the transformation.

A scientifically safer flow is:

```text
Control / Fit Points
        ↓
Transformation Estimation
        ↓
Independent Check Points
        ↓
Registration Error
```

The project feedback explicitly warns against fitting and judging on exactly the same points.

The backend result schema should therefore distinguish:

- fitting/control data
- verification/inlier data
- independent evaluation data

where the experiment provides them.

---

## 22. Backend Inputs

The exact request schema is `[TBD]`.

A future registration request may conceptually contain:

```text
Registration Request
├── source image
├── reference image / candidate source
├── source metadata
├── reference metadata
├── sensor information
├── preprocessing configuration
├── scale configuration
├── matching method
├── matching parameters
├── geometric model
├── verification parameters
├── refinement configuration
└── evaluation configuration
```

These fields are architectural concepts, not claims that the current API already implements them.

---

## 23. Backend Outputs

A future registration response may conceptually contain:

```text
Registration Result
├── status
├── method
├── candidate matches
├── verified inliers
├── inlier ratio
├── spatial coverage
├── transformation model
├── transformation parameters
├── residual statistics
├── independent check-point metrics
├── runtime
├── failure information
└── registered output reference
```

The actual schema is `[TBD]` unless defined in backend source code.

---

## 24. Error Handling

Backend errors should distinguish between different failure classes.

### Input Errors

Examples:

- unsupported image
- missing required metadata
- invalid dimensions
- malformed configuration
- incompatible sensor representation

### Processing Errors

Examples:

- preprocessing failure
- feature extraction failure
- matching failure
- geometric verification failure
- insufficient inliers
- transformation estimation failure

### Evaluation Errors

Examples:

- missing ground truth
- insufficient independent check points
- invalid evaluation configuration

### Infrastructure Errors

Examples:

- storage failure
- worker failure
- unavailable model
- unavailable retrieval index

The actual error schema remains `[TBD]`.

---

## 25. Failure Is a Valid Research Outcome

The backend should not convert every failed registration into an apparently successful response.

A failure should remain observable.

Conceptually:

```text
Processing
    │
    ├── Success
    │
    └── Failure
          ├── No candidates
          ├── No reliable matches
          ├── Insufficient inliers
          ├── Geometry failure
          ├── Evaluation unavailable
          └── Runtime / infrastructure failure
```

This is particularly important because the project is benchmark-driven.

The research guidance explicitly recommends preserving failures and analyzing difficult cases rather than hiding them behind confidence scores.

---

## 26. Configuration

The exact backend configuration system is `[TBD]`.

Configuration should eventually separate:

### Application configuration

- environment
- logging
- storage
- API settings

### Processing configuration

- sensor route
- preprocessing
- scale
- matching method
- geometric model
- refinement

### Research configuration

- experiment ID
- benchmark version
- evaluation thresholds
- ground-truth configuration

### Model configuration

- model/checkpoint
- model version
- inference settings

Configuration should not be hidden inside application code.

---

## 27. Reproducibility

A backend request that launches a scientific computation should ideally preserve the configuration required to reproduce it.

A conceptual processing record is:

```text
Run
├── experiment ID
├── dataset ID
├── source ID
├── reference ID
├── sensor
├── preprocessing
├── scale
├── method
├── model/checkpoint
├── geometric configuration
├── refinement configuration
├── evaluation configuration
├── software version
└── runtime environment
```

The exact persistence mechanism is `[TBD]`.

---

## 28. Backend and Experiments

The backend should not replace the experiment documentation system.

Experiments remain responsible for documenting:

- research question
- hypothesis
- variables
- controls
- methodology
- metrics
- results
- interpretation
- failure analysis

The backend can execute or expose experiment-related processing, but experiment documentation remains part of the research layer.

Relevant experiment structure includes:

```text
experiments/
├── templates/
└── v1/
    ├── baseline/
    ├── preprocessing/
    ├── geometry/
    └── refinement/
```

The exact currently implemented backend-to-experiment integration is `[TBD]`.

---

## 29. Backend and Benchmarking

The backend should support benchmark execution without changing the scientific evaluation rules.

A benchmark run should preserve:

```text
Dataset
   ↓
Fixed Configuration
   ↓
Processing
   ↓
Metrics
   ↓
Results
```

Comparisons should use the same image pairs and evaluation protocol wherever possible.

The project feedback recommends running the same test pairs through the SIFT baseline, stronger local methods, and the full sensor-aware/multi-scale pipeline to identify which component actually produces improvements.

---

## 30. Stress-Test Support

The backend should eventually support the project's stress-test matrix.

### Easy Pair

Known overlap, similar illumination, moderate scale difference.

### Sun-Angle Stress

Same region with substantially different shadows.

### Scale Stress

Large ground-scale difference.

### Modality Stress

IIRS-derived 2D representation against visible imagery.

### Geometry Stress

Relief-rich terrain or stronger viewpoint difference.

### Low-Feature Stress

Smooth or repetitive terrain where false matches are likely.

These categories come from the supplied project feedback and should be treated as benchmark scenarios rather than assumed production capabilities.

---

## 31. Backend API Design Principles

When APIs are implemented, they should follow these principles.

### Explicit

Requests should identify the processing configuration.

### Reproducible

The backend should make it possible to identify how a result was produced.

### Scientific

Responses should contain measurable outputs rather than only visual status.

### Observable

Failures and intermediate states should remain visible.

### Versioned

Research methods and result schemas should be versionable.

### Sensor-aware

The API should not assume all lunar imagery follows the same preprocessing route.

### Extensible

Future methods should be addable without rewriting the entire interface.

---

## 32. Conceptual API Boundary

The following is a conceptual interface, not a claim that these endpoints currently exist:

```text
POST   /...
       Submit registration request

GET    /...
       Retrieve processing status

GET    /...
       Retrieve registration result

GET    /...
       Retrieve experiment / benchmark result

GET    /...
       Retrieve available methods/configuration
```

Exact endpoint paths, HTTP methods, schemas, authentication, and versioning are:

> `[TBD]`

They should be documented only after implementation establishes them.

---

## 33. Suggested Result Object

A future result object could conceptually be structured as:

```text
result
├── run_id
├── status
├── method
├── source
├── reference
├── metadata
├── candidate_matches
├── verified_inliers
├── geometry
│   ├── model
│   ├── parameters
│   └── residuals
├── coverage
├── evaluation
│   ├── rmse
│   ├── median
│   ├── p90
│   ├── p95
│   └── max
├── runtime
├── artifacts
└── failure
```

This is an architectural proposal only.

---

## 34. Data Management

The backend should avoid treating raw research data as ordinary application state.

A conceptual separation is:

```text
data/
    Raw / source datasets

results/
    Experiment outputs

backend/
    Application and processing infrastructure

experiments/
    Experimental definitions

research/
    Scientific rationale and future methods
```

The exact storage policy is `[TBD]`.

Large lunar datasets should not be committed to Git merely because the backend can access them.

---

## 35. Metadata Preservation

Metadata is scientifically important.

Where available, the backend should preserve:

- image/product ID
- sensor
- dimensions
- GSD/pixel scale
- footprint
- latitude/longitude information
- projection
- acquisition time
- illumination geometry
- viewing geometry
- processing level
- source provenance

Missing metadata should be represented explicitly.

Do not fabricate metadata from image appearance.

---

## 36. Backend and Geospatial Information

The backend may eventually interact with geospatial information such as:

- image footprints
- coordinate reference systems
- map projections
- geographic bounds
- DEM information
- pixel-to-ground transformations

However, the exact geospatial library or database is not established in the supplied repository context.

Therefore:

> **Geospatial backend implementation:** `[TBD]`

The backend should preserve the distinction between:

```text
Image Pixel Coordinates
```

and:

```text
Ground / Geographic Coordinates
```

---

## 37. Pixel Accuracy

The project's primary sub-pixel accuracy reporting should use source-image pixels first.

Conversion to metres should only occur when:

- GSD is known
- projection is appropriate
- the reference geometry supports the conversion
- the evaluation definition makes the conversion meaningful

The supplied feedback explicitly recommends reporting source-image pixel error first and only converting to metres when ground scale and projection make it meaningful.

---

## 38. Runtime Measurement

Runtime should be treated as a measured metric.

The backend should eventually distinguish:

```text
Total Runtime
├── Input / decoding
├── Preprocessing
├── Retrieval
├── Feature extraction
├── Matching
├── Geometric verification
├── Refinement
├── Evaluation
└── Output generation
```

This allows future optimization without confusing algorithmic runtime with API overhead.

Exact instrumentation is `[TBD]`.

---

## 39. Logging

A future backend should provide structured logs sufficient to diagnose:

- request lifecycle
- selected sensor
- selected method
- experiment ID
- processing stage
- runtime
- failure stage
- output location

Sensitive or unnecessary data should not be logged.

The exact logging framework is `[TBD]`.

---

## 40. Observability

Research workloads benefit from stage-level observability.

A conceptual processing trace is:

```text
RUN START
   ↓
INPUT VALIDATED
   ↓
PREPROCESSING
   ↓
MATCHING
   ↓
GEOMETRIC VERIFICATION
   ↓
REGISTRATION
   ↓
EVALUATION
   ↓
RESULT SAVED
   ↓
RUN COMPLETE
```

A failed run should identify the stage at which it failed.

---

## 41. Testing Strategy

Backend testing should occur at multiple levels.

### Unit Tests

Test:

- input validation
- configuration parsing
- metadata validation
- result formatting
- error handling
- individual service utilities

### Integration Tests

Test:

- API-to-processing integration
- image loading
- pipeline orchestration
- result persistence
- artifact generation

### Research Pipeline Tests

Test:

- known image pair processing
- deterministic configuration
- geometric verification behavior
- metric computation

### Regression Tests

Ensure that backend changes do not silently change scientific outputs.

The exact testing framework is `[TBD]`.

---

## 42. Scientific Regression Testing

Scientific software requires more than API-level tests.

A future regression test may use a controlled image pair and verify that:

- processing completes
- candidate matches are returned
- geometric verification executes
- transformation output is valid
- evaluation metrics are produced
- failure behavior remains consistent

Numerical tolerances must be defined based on the experiment.

Tests should not assert invented benchmark values.

---

## 43. Reproducibility Testing

Where deterministic processing is expected, tests should verify reproducibility under a fixed configuration.

Relevant variables include:

- dataset
- preprocessing
- model
- model checkpoint
- random seed where applicable
- hardware
- numerical precision
- geometric configuration

If exact bitwise reproducibility is not possible, the expected tolerance should be documented.

---

## 44. Security and Input Validation

Although ChandraMap is a scientific system, the backend should still treat uploaded or externally referenced data as untrusted input.

Future validation should cover:

- file type
- file size
- image dimensions
- malformed files
- unsupported formats
- unsafe paths
- resource exhaustion
- invalid parameters

The exact security architecture is `[TBD]`.

---

## 45. Resource Management

Image registration can be computationally expensive.

A future backend must account for:

- CPU usage
- GPU usage
- memory
- image dimensions
- batch size
- concurrent processing
- processing timeouts
- temporary storage

The exact resource-management strategy is `[TBD]`.

Do not claim GPU acceleration, worker queues, or distributed execution unless implemented.

---

## 46. Long-Running Processing

Registration may eventually become a long-running backend task.

A conceptual architecture could be:

```text
Request
   ↓
Job Creation
   ↓
Processing Worker
   ↓
Pipeline
   ↓
Result
```

The exact queue or worker technology is not provided and therefore remains:

> `[TBD]`

A synchronous API should not be assumed to remain appropriate for all future research workloads.

---

## 47. Frontend Relationship

The backend should expose scientifically meaningful information to the frontend.

A future frontend may display:

- input images
- retrieved candidates
- candidate matches
- rejected matches
- verified inliers
- transformation
- residuals
- spatial coverage
- registration overlay
- evaluation metrics
- runtime
- failure state

The supplied project feedback specifically recommends a UI that shows inputs, candidates, accepted/rejected matches, metrics, and registered overlay, while keeping the UI after the core measurable pipeline works.

The frontend should not become the source of scientific truth.

Backend and experiment artifacts should remain authoritative.

---

## 48. Backend-to-Frontend Data Principle

The backend should return structured information rather than forcing the frontend to infer scientific results from images.

Prefer:

```text
{
  match_count: ...,
  inlier_count: ...,
  inlier_ratio: ...,
  coverage: ...,
  rmse: ...,
  transformation: ...
}
```

over requiring the frontend to calculate these values from rendered images.

The exact schema is `[TBD]`.

---

## 49. Research Method Selection

Future backend configuration may expose a method selector such as:

```text
method
├── sift
├── aliked_lightglue
├── loftr
└── [future methods]
```

This is conceptual only.

Methods should become selectable through the backend only after their implementation and experiment definitions exist.

The backend should not expose research methods that are merely documented as future ideas.

---

## 50. Future Global Retrieval

The backend may eventually support:

```text
Query Image
    ↓
Metadata / Geographic Filtering
    ↓
Global Descriptor
    ↓
FAISS
    ↓
Top-K Candidates
    ↓
Local Registration
```

The project roadmap recommends proving a small retrieval database with tens or hundreds of reference tiles before attempting a large-scale lunar archive.

Therefore, backend integration should follow the research progression rather than starting with a full-Moon architecture.

---

## 51. Future Research Components

Potential future backend integrations include:

| Component               | Backend role                      | Current status    |
| ----------------------- | --------------------------------- | ----------------- |
| SIFT                    | Baseline local correspondence     | Research baseline |
| Scale pyramid           | Multi-scale preprocessing/search  | Research          |
| Gradient representation | Structural preprocessing          | Research          |
| Affine                  | Transformation option             | Research          |
| Homography              | Transformation option             | Research          |
| Residual analysis       | Evaluation                        | Research          |
| Sub-pixel refinement    | Final correspondence refinement   | Research          |
| FAISS                   | Global candidate retrieval        | `[Planned]`       |
| ALIKED                  | Learned local feature extraction  | `[Planned]`       |
| LightGlue               | Learned sparse matching           | `[Planned]`       |
| LoFTR                   | Detector-free correspondence      | `[Planned]`       |
| RIFT/CFOG               | Multimodal/structural research    | `[Planned]`       |
| IIRS representation     | Sensor-specific input preparation | `[Planned]`       |
| DEM-aware registration  | Geometry enhancement              | `[Planned]`       |

These statuses do not imply that all components are implemented in the backend.

---

## 52. Backend Architecture Evolution

The backend should evolve incrementally.

### Stage 1 — Minimal End-to-End Processing

```text
API
 ↓
Known Pair
 ↓
SIFT
 ↓
RANSAC
 ↓
Transform
 ↓
Metrics
```

### Stage 2 — Scale and Sensor Handling

```text
API
 ↓
Sensor Route
 ↓
Preprocessing
 ↓
Scale Handling
 ↓
SIFT
 ↓
Geometry
 ↓
Metrics
```

### Stage 3 — Retrieval

```text
API
 ↓
Metadata / Retrieval
 ↓
Global Descriptor
 ↓
FAISS
 ↓
Top-K
 ↓
Local Matching
```

### Stage 4 — Stronger Research Methods

```text
API
 ↓
Method Selection
 ↓
SIFT / ALIKED+LightGlue / LoFTR / ...
 ↓
Geometry
 ↓
Evaluation
```

### Stage 5 — Advanced Research

```text
API
 ↓
Sensor-Aware Pipeline
 ↓
Global Retrieval
 ↓
Multi-Scale Local Correspondence
 ↓
Geometric / DEM-Aware Processing
 ↓
Sub-Pixel Refinement
 ↓
Independent Evaluation
```

These are architectural stages, not claims that all stages are currently implemented.

---

## 53. Current Implementation Boundaries

The supplied project material supports the research architecture but does not provide enough evidence to document specific backend implementation details.

Therefore, the following are intentionally unspecified:

| Area                                   | Status           |
| -------------------------------------- | ---------------- |
| Backend framework                      | `[Not provided]` |
| Entry point                            | `[Not provided]` |
| API routes                             | `[Not provided]` |
| Request schemas                        | `[Not provided]` |
| Response schemas                       | `[Not provided]` |
| Database                               | `[Not provided]` |
| ORM                                    | `[Not provided]` |
| Job queue                              | `[Not provided]` |
| Worker system                          | `[Not provided]` |
| Authentication                         | `[Not provided]` |
| Object storage                         | `[Not provided]` |
| Containerization                       | `[Not provided]` |
| Deployment                             | `[Not provided]` |
| Monitoring                             | `[Not provided]` |
| Backend-specific environment variables | `[Not provided]` |

These fields should be replaced with verified repository information when the corresponding implementation exists.

---

## 54. Development Setup

The exact backend development commands are not provided in the available project context.

Therefore, no framework-specific installation command is intentionally invented here.

Before documenting setup, verify the actual backend files and identify:

1. runtime language
2. package manager
3. dependency manifest
4. application entry point
5. environment configuration
6. test command
7. development server command
8. optional service dependencies

### Current Setup Status

```text
Backend runtime: [TBD]
Package manager: [TBD]
Dependency file: [TBD]
Environment file: [TBD]
Development command: [TBD]
Test command: [TBD]
```

Once implemented, this section should become the authoritative local-development guide.

---

## 55. Environment Variables

The actual environment-variable list is not provided.

Do not create placeholder variables that imply implementation.

Future documentation should record:

| Variable | Purpose | Required | Default | Status  |
| -------- | ------- | -------: | ------- | ------- |
| `[TBD]`  | `[TBD]` |  `[TBD]` | `[TBD]` | `[TBD]` |

Secrets must never be committed to Git.

Use a documented example environment file only when the actual application requires one.

---

## 56. Local Development Workflow

Once the backend implementation is established, the recommended workflow should be:

```text
Clone Repository
      ↓
Install Backend Dependencies
      ↓
Configure Environment
      ↓
Verify Test Suite
      ↓
Start Backend
      ↓
Run Controlled Registration
      ↓
Inspect Metrics / Artifacts
      ↓
Run Backend Tests
```

For research development:

```text
Modify Pipeline
      ↓
Run Experiment
      ↓
Save Configuration
      ↓
Save Results
      ↓
Compare Against Baseline
      ↓
Document Findings
```

---

## 57. Testing Workflow

A complete backend contribution should eventually follow:

```text
Code Change
   ↓
Unit Tests
   ↓
Integration Tests
   ↓
Scientific Regression Tests
   ↓
Experiment Validation
   ↓
Documentation Update
```

Not every research experiment needs to become a permanent backend integration.

---

## 58. Backend Contribution Guidelines

Contributors should:

- keep backend code modular
- avoid embedding research assumptions in API code
- keep configurations explicit
- preserve metadata
- preserve failures
- avoid hard-coded paths
- avoid hard-coded experiment results
- document new interfaces
- add tests for backend behavior
- document research changes separately
- avoid claiming unmeasured accuracy
- keep research and production status explicit

---

## 59. Code and Research Separation

A useful repository boundary is:

```text
backend/
    Application / service infrastructure

src/
    Reusable processing implementation

experiments/
    Experiment definitions and evaluations

research/
    Scientific documentation and future directions

tests/
    Software and scientific regression tests

results/
    Measured outputs
```

The exact responsibilities of `src/` and `backend/` must follow the actual repository implementation.

If the repository currently uses a different separation, that actual structure should take precedence.

---

## 60. API Versioning

If a public API is introduced, its versioning strategy should be explicit.

Potential conceptual structure:

```text
API
├── v1
├── future versions
└── compatibility policy
```

This is `[Planned]`.

Do not document a versioned API until the actual route structure is implemented.

---

## 61. Backward Compatibility

Research APIs can change rapidly.

When an interface becomes stable enough for frontend or external use, changes should document:

- schema changes
- deprecated fields
- method changes
- result changes
- migration requirements

Scientific result compatibility should also be considered.

A change that alters numerical outputs may be scientifically significant even if the HTTP schema remains unchanged.

---

## 62. Provenance

Every scientifically meaningful backend result should eventually be traceable to:

```text
Input
  ↓
Configuration
  ↓
Method
  ↓
Software Version
  ↓
Processing
  ↓
Result
```

This is particularly important when comparing research methods.

A result without provenance is difficult to reproduce or interpret.

---

## 63. Result Artifacts

The backend may eventually produce or reference:

- candidate match visualization
- verified inlier visualization
- registered overlay
- transformation matrix
- match coordinates
- inlier mask
- residual vectors
- coverage metrics
- check-point errors
- runtime logs
- configuration
- metadata

The actual artifact storage format is `[TBD]`.

---

## 64. Registered Image Is Not the Only Output

A visually aligned image should not be treated as sufficient evidence.

The backend should preserve measurable outputs such as:

```text
Registered Image
+
Correspondences
+
Verified Inliers
+
Transformation
+
Residuals
+
Coverage
+
Independent Error
+
Runtime
```

The project documentation explicitly emphasizes that the correspondence problem and quantitative outputs are more important than presenting only a visually attractive mosaic.

---

## 65. Backend Limitations

The backend currently has the following documentation limitations because the exact implementation details were not provided:

- framework is not established here
- API schema is not established here
- deployment architecture is not established here
- storage architecture is not established here
- worker architecture is not established here
- authentication is not established here
- exact module structure is not established here
- exact commands are not established here

These are documentation gaps, not claims that the project cannot support them.

They should be replaced with verified implementation details when the backend source is available.

---

## 66. Known Scientific Constraints

The backend should preserve the following scientific constraints.

### Resizing Does Not Recover Missing Detail

Upsampling should not be represented as creating new spatial information.

### More Matches Do Not Necessarily Mean Better Registration

Matches should be evaluated for geometric validity and spatial distribution.

### Global Similarity Is Not Geometric Proof

Retrieval must be followed by local correspondence and geometric verification.

### Pretrained Models Are Not Automatically Lunar-Invariant

Learned methods should be evaluated on the actual lunar benchmark.

### A Single Global Warp May Be Insufficient

Residual patterns should be inspected.

### Ground Error Is Not Always Meaningful

Physical-unit error requires appropriate GSD and projection information.

These principles are directly aligned with the supplied technical feedback.

---

## 67. Recommended Backend Research Order

The backend should support the research roadmap incrementally.

```text
1. Establish one known image pair
        ↓
2. Run SIFT baseline
        ↓
3. Add RANSAC and transformation
        ↓
4. Add measurable evaluation
        ↓
5. Add scale handling
        ↓
6. Add sensor-aware preprocessing
        ↓
7. Add retrieval when required
        ↓
8. Add FAISS candidate search
        ↓
9. Benchmark stronger local matchers
        ↓
10. Add sub-pixel refinement
        ↓
11. Add additional sensors
        ↓
12. Investigate DEM-aware processing
```

The project feedback explicitly recommends proving one complete measurable pair before expanding toward retrieval and additional methods.

---

## 68. Recommended First Backend Milestone

The first meaningful backend milestone should be:

```text
Input Source Image
        ↓
Input Reference Image
        ↓
SIFT
        ↓
Candidate Matches
        ↓
RANSAC
        ↓
Verified Inliers
        ↓
Transformation
        ↓
Registered Overlay
        ↓
Independent Check-Point Error
```

The expected evidence should include:

- match visualization
- rejected outliers
- registered overlay
- inlier statistics
- spatial coverage
- independent check-point error
- runtime

This follows the supplied project build-order guidance.

---

## 69. Future Backend Integration Matrix

| Research Component      | Backend Integration      | Expected Role                   | Status                 |
| ----------------------- | ------------------------ | ------------------------------- | ---------------------- |
| SIFT                    | Processing service       | Baseline correspondence         | `[Research Baseline]`  |
| Scale Pyramid           | Preprocessing service    | Multi-scale search              | `[Planned]`            |
| Gradient Representation | Preprocessing service    | Structure-focused matching      | `[Planned]`            |
| RANSAC                  | Geometry service         | Outlier rejection               | `[Planned / Research]` |
| Affine                  | Geometry service         | Transformation candidate        | `[Planned / Research]` |
| Homography              | Geometry service         | Transformation candidate        | `[Planned / Research]` |
| Residual Analysis       | Evaluation service       | Registration diagnostics        | `[Planned]`            |
| Sub-Pixel Refinement    | Refinement service       | Tie-point refinement            | `[Planned]`            |
| FAISS                   | Retrieval service        | Top-K candidate discovery       | `[Planned]`            |
| ALIKED                  | Feature service          | Learned local features          | `[Planned]`            |
| LightGlue               | Matching service         | Sparse learned matching         | `[Planned]`            |
| LoFTR                   | Correspondence service   | Detector-free correspondence    | `[Planned]`            |
| RIFT/CFOG               | Research processing path | Multimodal/structural matching  | `[Planned]`            |
| IIRS Representation     | Sensor processing path   | Hyperspectral-to-2D preparation | `[Planned]`            |
| DEM-Aware Registration  | Geometry service         | Geometry-aware refinement       | `[Planned]`            |

---

## 70. Definition of Done for Backend Features

A new backend capability should not be considered complete merely because an endpoint returns a response.

A research-facing feature should satisfy:

### Software

- [ ] implementation exists
- [ ] input validation exists
- [ ] output schema is documented
- [ ] errors are handled
- [ ] tests exist

### Scientific

- [ ] method is documented
- [ ] configuration is reproducible
- [ ] benchmark conditions are defined
- [ ] metrics are defined
- [ ] failure cases are preserved

### Documentation

- [ ] backend README updated
- [ ] experiment documentation updated
- [ ] research documentation updated where relevant
- [ ] configuration documented
- [ ] output artifacts documented

### Reproducibility

- [ ] versions recorded
- [ ] dataset identified
- [ ] configuration saved
- [ ] results reproducible within documented tolerances

---

## 71. Backend Quality Checklist

Before considering the backend production-ready:

### Architecture

- [ ] Responsibilities are clearly separated.
- [ ] Research code is not unnecessarily coupled to API code.
- [ ] Processing components have explicit interfaces.
- [ ] Configuration is explicit.

### Scientific correctness

- [ ] Candidate matches are distinguished from verified inliers.
- [ ] Geometric verification is preserved.
- [ ] Independent evaluation is supported.
- [ ] Metadata is preserved.
- [ ] Failures are retained.

### Software quality

- [ ] Tests are present.
- [ ] Errors are structured.
- [ ] Logging is adequate.
- [ ] Resource use is controlled.
- [ ] Configuration is documented.

### Reproducibility

- [ ] Runs have identifiable configurations.
- [ ] Dataset versions are recorded.
- [ ] Model versions are recorded.
- [ ] Experiment IDs are traceable.
- [ ] Results are reproducible.

### Documentation

- [ ] API documentation is current.
- [ ] Setup instructions are current.
- [ ] Research interfaces are documented.
- [ ] Known limitations are explicit.

---

## 72. Repository Integration

The backend should remain connected to the broader ChandraMap repository.

Relevant areas include:

```text
backend/
experiments/
research/
data/
tests/
results/
```

The exact presence and structure of additional directories such as `configs/` and `scripts/` should follow the actual repository.

Do not introduce undocumented backend-specific directories solely for architectural appearance.

---

## 73. Documentation Maintenance

This README should be updated when any of the following changes:

- backend framework
- API routes
- processing architecture
- storage
- configuration
- deployment
- research integration
- result schema
- testing workflow
- experiment execution
- model integration
- retrieval integration

Research documents should continue to describe scientific methodology independently from backend implementation details.

---

## 74. Implementation Status

| Area                          | Status                |
| ----------------------------- | --------------------- |
| Backend README                | `[Documented]`        |
| Backend architecture          | `[Partially Defined]` |
| Exact framework               | `[Not provided]`      |
| Exact API                     | `[Not provided]`      |
| Image-processing integration  | `[Not provided]`      |
| SIFT backend integration      | `[Not provided]`      |
| FAISS integration             | `[Planned]`           |
| ALIKED integration            | `[Planned]`           |
| LightGlue integration         | `[Planned]`           |
| LoFTR integration             | `[Planned]`           |
| IIRS backend path             | `[Planned]`           |
| DEM-aware backend path        | `[Planned]`           |
| Automated benchmark execution | `[TBD]`               |
| Production deployment         | `[Not provided]`      |

---

## 75. What Should Not Be Claimed

The backend README must not claim:

- that FAISS is already implemented unless code proves it
- that ALIKED is already integrated unless code proves it
- that LightGlue is already integrated unless code proves it
- that LoFTR is already integrated unless code proves it
- that IIRS processing is complete unless code proves it
- that a database exists unless code/configuration proves it
- that a queue exists unless implementation proves it
- that an API endpoint exists without source evidence
- that registration accuracy has been validated without benchmark evidence
- that a particular method is superior without controlled comparison
- that a visual overlay proves registration accuracy
- that a particular hardware configuration is supported without testing

---

## 76. Engineering Principles

The backend should follow these project principles:

### Measure Before Optimizing

Performance improvements should be demonstrated with measurements.

### Research Before Production

A research method should be independently evaluated before becoming a production backend capability.

### Preserve Scientific Traceability

Every result should be traceable to its inputs and configuration.

### Keep Interfaces Explicit

Methods and configurations should not be hidden behind ambiguous generic endpoints.

### Keep Sensor Differences Visible

OHRC, TMC-2, and IIRS should not be silently forced into identical processing assumptions.

### Preserve Failures

Failures are part of the benchmark.

### Keep Evaluation Independent

Transformation fitting and independent accuracy evaluation should remain separate.

### Build Incrementally

One measurable end-to-end result is more valuable than a large unvalidated architecture.

---

## 77. Future Architecture

The long-term conceptual architecture may evolve toward:

```text
                              ChandraMap
                                  │
                    ┌─────────────┴─────────────┐
                    │                           │
                    ▼                           ▼
                Frontend                    Backend
                                                │
                    ┌───────────────────────────┼───────────────────────────┐
                    │                           │                           │
                    ▼                           ▼                           ▼
              API / Control              Retrieval Layer             Processing Layer
                    │                           │                           │
                    │                     Global Descriptor               │
                    │                           │                         Sensor Routing
                    │                           ▼                           │
                    │                        FAISS                          │
                    │                           │                           │
                    │                         Top-K                         │
                    │                           │                           │
                    └───────────────────────────┼───────────────────────────┘
                                                ▼
                                      Local Correspondence
                                                │
                              ┌─────────────────┼─────────────────┐
                              │                 │                 │
                              ▼                 ▼                 ▼
                            SIFT        ALIKED + LightGlue       LoFTR
                              │                 │                 │
                              └─────────────────┼─────────────────┘
                                                ▼
                                      Geometric Verification
                                                │
                                                ▼
                                      Transformation Model
                                                │
                                                ▼
                                       Residual Analysis
                                                │
                                                ▼
                                       Sub-Pixel Refinement
                                                │
                                                ▼
                                       Final Registration
                                                │
                                                ▼
                                      Independent Evaluation
                                                │
                                                ▼
                                             Results
```

This is a future architecture concept, not a statement that all components currently exist.

---

## 78. Final Architecture Principle

The backend should make the complete ChandraMap workflow understandable and measurable:

```text
INPUT
  ↓
VALIDATION
  ↓
SENSOR-AWARE PREPARATION
  ↓
SCALE-AWARE SEARCH
  ↓
GLOBAL RETRIEVAL IF REQUIRED
  ↓
LOCAL CORRESPONDENCE
  ↓
CANDIDATE MATCHES
  ↓
GEOMETRIC VERIFICATION
  ↓
VERIFIED INLIERS
  ↓
TRANSFORMATION
  ↓
OPTIONAL SUB-PIXEL REFINEMENT
  ↓
FINAL MODEL
  ↓
INDEPENDENT EVALUATION
  ↓
REGISTERED OUTPUT
  ↓
SCIENTIFIC METRICS
```

The backend should expose this workflow without hiding the scientific distinctions between retrieval, correspondence, geometric verification, registration, and evaluation.

---

## 79. Summary

ChandraMap's backend is intended to serve as the software infrastructure connecting the application layer with a research-grade lunar image correspondence and registration pipeline.

Its most important responsibilities are:

- structured input handling
- sensor-aware processing orchestration
- configuration management
- research-method integration
- correspondence and registration execution
- geometric verification
- result handling
- quantitative evaluation
- failure reporting
- reproducibility
- future global retrieval integration

The backend should remain deliberately separated from the scientific claims made by individual algorithms.

The core system should continue to follow the project's central research principle:

```text
Make lunar data comparable
        ↓
Search at an appropriate scale
        ↓
Find candidate correspondences
        ↓
Verify geometry
        ↓
Refine when justified
        ↓
Measure independent error
        ↓
Preserve failures
```

The supplied project feedback recommends building this capability incrementally: first prove one real source/reference pair end-to-end, then add scale handling, retrieval, stronger matchers, refinement, and additional sensors.

**Backend status:** `[Partially Defined / Implementation Details TBD]`

**Research integration status:** `[Planned]`

**Production readiness:** `[Not established]`
