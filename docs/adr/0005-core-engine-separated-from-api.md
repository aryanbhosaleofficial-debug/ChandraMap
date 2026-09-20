# ADR-0005: Core Engine Separated from API

- **ADR:** ADR-0005
- **Title:** Core Engine Separated from API
- **Status:** Accepted
- **Date:** `<YYYY-MM-DD>`
- **Decision Scope:** Shared architecture / V1 and later versions
- **Architecture Area:** Core Engine / Backend API Boundary
- **Sensor Scope:** Sensor-independent architectural decision
- **Supersedes:** N/A
- **Superseded By:** N/A
- **Related ADRs:** ADR-0001, ADR-0002, ADR-0003, ADR-0004
- **Related Issues:** N/A
- **Related PRs:** N/A
- **Related Experiments:** N/A
- **Related Benchmarks:** V1 benchmark architecture
- **Implementation Status:** Architecture accepted; implementation completeness is not asserted by this ADR

> **Decision:** ChandraMap's scientific/computer-vision core engine will remain independent from the HTTP/API layer.

The API may invoke ChandraMap's scientific capabilities.

The core engine must not depend on the API, HTTP request/response objects, web-framework behavior, frontend payloads, or deployment infrastructure merely to execute lunar image correspondence, registration, or evaluation.

The intended dependency direction is:

```text
Delivery Interface
        ↓
Application / Orchestration
        ↓
ChandraMap Core Engine
```

not:

```text
ChandraMap Core Engine
        ↓
HTTP / API Framework
```

The central architectural principle is:

> **The API is an adapter around ChandraMap capabilities, not the owner of ChandraMap's scientific logic.**

---

## Status

**Accepted**

`Accepted` means the architectural boundary between scientific/domain logic and HTTP/API delivery has been selected.

It does **not** mean that:

- the backend implementation is complete;
- all API endpoints already exist;
- all scientific modules have already been extracted;
- a specific API framework has been selected;
- package/module layout is final;
- deployment architecture is finalized;
- a database has been selected;
- background workers exist;
- authentication has been implemented.

This ADR defines dependency and responsibility boundaries, not completion status.

---

## Context

ChandraMap is both:

1. a scientific lunar image correspondence and registration system; and
2. a software project that may expose scientific capabilities through APIs and other user interfaces.

These responsibilities are related, but they are not the same architectural concern.

The scientific pipeline may include stages such as:

```text
Source / Reference Inputs
        ↓
Sensor-Aware Preparation
        ↓
Scale Handling
        ↓
Local Correspondence
        ↓
Geometric Verification
        ↓
Transformation Estimation
        ↓
Registration
        ↓
Independent Evaluation
        ↓
Scientific Result
```

This pipeline must support direct use by:

- benchmarks;
- research experiments;
- automated tests;
- notebooks;
- scripts;
- future command-line tools;
- offline/batch processing;
- future background workers;
- backend/API services.

The API layer has a different responsibility set, including concerns such as:

- HTTP transport;
- request parsing;
- payload validation;
- uploads;
- authentication/authorization where applicable;
- external schemas;
- API errors;
- serialization;
- HTTP status behavior;
- request logging;
- service lifecycle.

If these responsibilities are combined, the scientific implementation becomes unnecessarily coupled to its delivery mechanism.

That would make ChandraMap harder to:

- test directly;
- benchmark fairly;
- run offline;
- reproduce scientifically;
- reuse from research notebooks;
- expose through future interfaces;
- evolve without breaking scientific code.

---

## Problem

ChandraMap needs an architectural boundary that answers:

> Where should scientific image-registration logic live, and how should an API access it?

A direct prototype could place scientific processing inside HTTP route handlers:

```text
HTTP Request
        ↓
Route Handler
        ↓
Preprocessing
        ↓
SIFT
        ↓
RANSAC
        ↓
Transform
        ↓
Metrics
        ↓
JSON Response
```

This is attractive initially because it requires few abstractions.

However, it creates several long-term problems.

### Scientific workflows become transport-dependent

Running a benchmark or notebook may require constructing API-compatible requests or starting a server.

### Testing becomes unnecessarily expensive

Core algorithm tests may depend on web-framework initialization, request schemas, or transport setup.

### Scientific and API errors become conflated

A failed geometric registration is not the same thing as a malformed HTTP request.

### Runtime measurements become ambiguous

Algorithm runtime may be mixed with:

- upload time;
- parsing;
- serialization;
- network transfer;
- queueing.

### Framework replacement becomes expensive

Changing the API framework could require touching scientific algorithms.

### Multiple interfaces encourage duplication

Notebook, CLI, API, and batch workflows may each implement their own version of the pipeline.

ChandraMap therefore requires a one-way dependency boundary around its scientific engine.

---

## Decision Drivers

The major drivers for this decision are:

### Scientific reproducibility

Scientific results should be reproducible without reproducing an HTTP service environment unless the API itself is the subject of the experiment.

### Direct benchmark execution

Benchmark code must be able to invoke the registration engine directly.

### Testability

Algorithms should be testable without:

- a running server;
- a network;
- an API client;
- browser state;
- frontend state.

### Maintainability

Transport changes should not require modifications to SIFT, RANSAC, registration, or evaluation logic.

### Modular architecture

Scientific responsibilities and delivery responsibilities should remain distinct.

### Framework independence

The project should be able, in principle, to replace its web/API framework without rewriting the scientific core.

### Reuse

The same scientific implementation should be reusable from:

- API;
- notebooks;
- scripts;
- benchmarks;
- future CLI;
- future workers.

### Future worker/batch support

Long-running registration may later require asynchronous execution.

Core separation avoids moving algorithms out of endpoint handlers later.

### Clean dependency direction

Outer delivery layers may depend inward on scientific/domain logic.

Scientific/domain logic must not depend outward on delivery frameworks.

### Benchmark integrity

Core runtime and API end-to-end latency must remain distinguishable.

### Reduced duplication

Sensor preprocessing, matching, registration, and evaluation should have one canonical scientific implementation.

---

## Terminology

### Core Engine

The **core engine** is the reusable scientific/domain part of ChandraMap.

Conceptually, it may contain scientific responsibilities such as:

- sensor-aware preparation;
- scientifically justified normalization;
- scale handling;
- local feature extraction;
- local matching;
- candidate correspondence generation;
- geometric verification;
- RANSAC-related logic;
- transformation estimation;
- image registration;
- optional tie-point refinement;
- residual computation;
- metric computation;
- spatial-coverage computation;
- independent checkpoint evaluation;
- domain-level input validation;
- benchmark-relevant domain outputs.

This ADR does not require every capability to be implemented immediately.

The architectural requirement is that scientific behavior belongs on the core/domain side rather than inside transport handlers.

---

### Application / Orchestration Layer

An application/use-case layer may exist between delivery interfaces and the scientific core.

Its purpose is coordination.

Potential responsibilities include:

- selecting a workflow;
- resolving scientific configuration;
- coordinating input loading;
- invoking core stages;
- managing artifact-generation requests;
- coordinating persistence abstractions;
- handling long-running workflow state;
- returning domain-level results to adapters.

It should not become another location where scientific algorithms are duplicated.

This ADR does not mandate a particular framework, service pattern, dependency-injection library, or class structure.

---

### API / Delivery Layer

The API layer exposes ChandraMap capabilities through a network interface.

Potential responsibilities include:

- HTTP route definitions;
- request parsing;
- upload handling;
- transport/schema validation;
- authentication and authorization where applicable;
- mapping external payloads into domain inputs;
- invoking application/core operations;
- translating domain outcomes into API responses;
- serialization;
- API versioning;
- service-level logging;
- status/health behavior.

The API is an adapter around scientific capabilities.

It is not the scientific implementation itself.

---

### Domain Input and Result

A **domain input** represents scientific information required by the registration workflow independently of HTTP.

Conceptual examples include:

- source image or product;
- reference image or product;
- sensor metadata;
- GSD/scale information;
- projection metadata;
- matcher configuration;
- geometry configuration;
- refinement configuration;
- checkpoint data.

A **domain result** represents scientific output independently of API serialization.

Conceptual outputs may include:

- candidate correspondences;
- verified inliers;
- transformation model;
- registered output reference;
- residuals;
- checkpoint metrics;
- inlier statistics;
- spatial coverage;
- warnings;
- scientific failure information;
- stage timings;
- provenance metadata.

These terms are conceptual. ADR-0005 does not define concrete class names or function signatures.

---

## Relationship to Existing ADRs

### ADR-0001 — V1 Known-Overlap First

[ADR-0001](0001-v1-known-overlap-first.md) establishes that V1 begins from a known or constrained source/reference overlap.

Therefore the core registration workflow must be callable directly with such a pair:

```text
Known Source / Reference Pair
        ↓
Core Registration Workflow
        ↓
Registration Result
```

A V1 benchmark must not need to send that pair through an HTTP endpoint simply to execute the algorithm.

---

### ADR-0002 — SIFT as the V1 Local-Matching Baseline

[ADR-0002](0002-sift-as-v1-baseline.md) establishes SIFT as the V1 local-matching baseline.

Therefore SIFT-related scientific behavior belongs in reusable scientific logic.

This is an anti-pattern:

```text
API Route Handler
        ↓
Inline SIFT Detection
        ↓
Inline Descriptor Matching
        ↓
Inline RANSAC
        ↓
Response
```

The intended architecture is:

```text
API Adapter
        ↓
Registration Use Case
        ↓
Core Matching / Registration Pipeline
        ↓
SIFT Baseline
```

SIFT should remain callable from benchmarks and research code without HTTP.

---

### ADR-0003 — Independent Checkpoints for Registration Evaluation

[ADR-0003](0003-independent-checkpoints.md) establishes independent checkpoint evaluation as part of scientific registration assessment.

Checkpoint evaluation therefore belongs to reusable scientific/evaluation logic.

Conceptually:

```text
Final Transform
        +
Independent Checkpoints
        ↓
Core Evaluation Logic
        ↓
Checkpoint Metrics
```

The API may serialize these metrics.

It must not own their scientific definition or calculation.

---

### ADR-0004 — No Global Retrieval in V1

[ADR-0004](0004-no-global-retrieval-in-v1.md) establishes that global retrieval is outside V1.

The V1 core therefore must not require:

- global search infrastructure;
- FAISS infrastructure;
- vector databases;
- Top-K retrieval services;
- retrieval endpoints.

A future retrieval subsystem should be able to provide candidate regions to the same reusable registration engine:

```text
Future Retrieval
        ↓
Candidate Reference Region
        ↓
Core Registration Engine
```

Global retrieval may evolve independently without forcing the local registration engine to depend on retrieval infrastructure.

---

## Considered Options

### Option A — Separate Core Engine from API

Scientific/domain logic exists independently from HTTP.

The API calls the core through an application/use-case boundary where useful.

Conceptually:

```text
API
 ↓
Application
 ↓
Core
```

#### Advantages

- strong scientific testability;
- direct benchmark access;
- notebook reuse;
- easier future CLI support;
- API framework independence;
- clear dependency direction;
- cleaner domain errors;
- easier worker/batch evolution;
- lower scientific-code duplication;
- independent core runtime measurement;
- stronger reproducibility;
- delivery mechanisms can evolve independently.

#### Trade-offs

- requires explicit boundaries;
- requires input/output adaptation;
- may introduce mapping code;
- developers must actively prevent transport models from leaking inward;
- slightly more architectural work than putting everything in routes.

---

### Option B — Scientific Logic in API Handlers

Route handlers directly execute:

- preprocessing;
- SIFT;
- matching;
- RANSAC;
- transformation;
- evaluation;
- response generation.

Conceptually:

```text
HTTP Route
   ├── Input Parsing
   ├── Scientific Preprocessing
   ├── Matching
   ├── Geometry
   ├── Metrics
   ├── Artifact Storage
   └── Serialization
```

#### Advantages

- fewer initial files;
- simple for a disposable demonstration;
- minimal initial mapping code.

#### Trade-offs

- strong coupling to transport;
- difficult direct benchmarking;
- poor notebook reuse;
- poor CLI reuse;
- large route handlers;
- scientific and HTTP errors become mixed;
- framework replacement becomes expensive;
- testing becomes harder;
- workers/batch execution become harder;
- scientific timing becomes contaminated by service concerns;
- algorithm behavior becomes easier to duplicate accidentally.

---

### Option C — API-Centric Helper Layer

Scientific processing is extracted into helper functions, but helpers remain coupled to:

- API schema types;
- HTTP request structures;
- framework exceptions;
- transport serialization.

#### Advantages

- smaller route handlers than Option B;
- some code reuse;
- partial organization improvement.

#### Trade-offs

- dependency boundary remains weak;
- benchmark/notebook code still pulls API dependencies;
- domain representations remain unclear;
- framework migration still affects scientific code;
- transport-level models become internal architecture;
- scientific reproducibility remains unnecessarily tied to the API stack.

---

## Option Comparison

| Criterion                         | Separate Core + API | Logic in API Handlers | API-Centric Helpers |
| --------------------------------- | ------------------- | --------------------- | ------------------- |
| Direct benchmarking               | Strong              | Weak                  | Moderate            |
| Notebook reuse                    | Strong              | Weak                  | Moderate            |
| API framework independence        | Strong              | Weak                  | Weak/Moderate       |
| Scientific testability            | Strong              | Weak                  | Moderate            |
| Initial implementation simplicity | Moderate            | Strong                | Moderate            |
| Long-term maintainability         | Strong              | Weak                  | Moderate            |
| Worker/CLI reuse                  | Strong              | Weak                  | Moderate            |
| Separation of concerns            | Strong              | Weak                  | Partial             |
| Core runtime measurement          | Strong              | Weak                  | Moderate            |
| Domain error clarity              | Strong              | Weak                  | Moderate            |
| Reproducibility                   | Strong              | Weak                  | Moderate            |

This is an architectural comparison, not a numerical benchmark.

---

## Decision

**Option A — Separate Core Engine from API** is selected.

The following architectural rules are accepted:

1. API code may invoke scientific/core logic.
2. Core scientific logic must not depend on HTTP/API frameworks.
3. Core registration must execute without a web server.
4. Benchmarks must be able to invoke core scientific functionality directly.
5. API payloads should be mapped into domain-oriented inputs.
6. Domain results should be mapped into API response representations.
7. Domain errors must remain independent from HTTP status codes.
8. Scientific configuration must be conceptually separate from transport/service configuration.
9. Scientific pipeline logic must not exist only in route handlers.
10. Future non-HTTP interfaces should be able to reuse the same core implementation.
11. API latency and core algorithm runtime must remain distinct measurements.

---

## Rationale

ChandraMap is primarily a scientific image-correspondence and registration system.

HTTP is one possible delivery mechanism.

The scientific engine is expected to outlive any specific delivery technology.

The architecture should therefore make the following possible:

```text
Research Notebook
        ↓
Core Registration Engine
```

```text
Benchmark Runner
        ↓
Core Registration Engine
```

```text
Future CLI
        ↓
Application / Core
```

```text
Backend API
        ↓
Application / Core
```

```text
Future Worker
        ↓
Application / Core
```

without creating separate versions of:

- preprocessing;
- SIFT;
- RANSAC;
- registration;
- evaluation.

The purpose is not to maximize the number of architectural layers.

The purpose is to establish one high-value boundary:

> **Scientific/domain logic is independent from transport/delivery logic.**

---

## Architectural Boundary

The logical architecture is:

```text
┌─────────────────────────────────────┐
│          Delivery Interfaces        │
│                                     │
│  API     CLI     Scripts/Notebook   │
└──────────────────┬──────────────────┘
                   │
                   ▼
┌─────────────────────────────────────┐
│      Application / Use Cases        │
│                                     │
│  registration orchestration         │
│  artifact coordination              │
│  configuration selection            │
└──────────────────┬──────────────────┘
                   │
                   ▼
┌─────────────────────────────────────┐
│       ChandraMap Core Engine        │
│                                     │
│  sensor preparation                 │
│  scale handling                     │
│  local matching                     │
│  geometric verification             │
│  transformation                     │
│  refinement                         │
│  evaluation                         │
└─────────────────────────────────────┘
```

This diagram is conceptual.

It does **not** prescribe:

- exact package names;
- exact classes;
- exact modules;
- exact interface names;
- exact deployment processes.

---

## Dependency Direction

The dependency direction must remain inward toward reusable scientific/domain behavior.

### Allowed

```text
API
 ↓
Application
 ↓
Core
```

```text
CLI
 ↓
Application
 ↓
Core
```

```text
Notebook
 ↓
Core
```

```text
Research Script
 ↓
Core
```

```text
Benchmark Runner
 ↓
Core
```

### Not Allowed

```text
Core
 ↓
API
```

```text
Core
 ↓
HTTP Request
```

```text
Core
 ↓
HTTP Response
```

```text
Core Registration Logic
 ↓
Route Handler Utility
```

```text
Scientific Metric Code
 ↓
Web Framework Exception
```

### Conditional infrastructure dependencies

Infrastructure adapters may exist for concerns such as:

- file access;
- artifact storage;
- persistence;
- model loading;
- telemetry.

Where practical, scientific algorithms should depend on stable domain-facing abstractions rather than concrete delivery technologies.

However:

> **Do not add abstraction merely for architectural appearance.**

Only introduce an interface where it protects a real boundary or enables needed replacement/testing.

---

## Why the Core Must Not Import API Code

If scientific modules import API-specific code, several undesirable consequences follow.

### Core tests require web setup

Testing a transform or metric should not require configuring a web framework.

### Notebooks inherit unnecessary dependencies

Research exploration should not require API initialization.

### Benchmarks become HTTP clients

This introduces:

- serialization overhead;
- network behavior;
- server state;

into scientific measurement.

### API refactoring can alter algorithms

A transport change should not risk changing registration behavior.

### Framework migration becomes expensive

Replacing the API framework should not require rewriting SIFT, RANSAC, or checkpoint evaluation.

### Domain failures become transport failures

"Insufficient verified correspondences" is a scientific outcome.

It should not intrinsically mean a particular HTTP status code.

### Future interfaces become harder

Batch workers, scripts, CLI tools, or desktop tools should not need to emulate HTTP clients to use ChandraMap.

---

## Core Engine Responsibilities

The core/domain side owns scientific behavior.

Conceptual responsibilities include:

### Input-domain validation

Examples:

- sensor compatibility;
- representation validity;
- scientific configuration validity;
- required geometric information;
- checkpoint structure.

### Sensor-aware preparation

Scientific preprocessing for:

- OHRC;
- TMC-2;
- IIRS-derived registration representations;
- reference imagery.

### Scale handling

Preparing scientifically comparable effective image scales.

### Local correspondence

Including matcher implementations such as the V1 SIFT baseline.

### Candidate correspondence handling

Maintaining the distinction between:

- raw matcher output;
- filtered candidates;
- verified inliers.

### Geometric verification

Including RANSAC-related behavior and model consistency.

### Transformation estimation

Estimating supported registration models.

### Refinement

Where configured, refining verified tie points.

### Registration

Applying accepted geometry to generate registered outputs.

### Evaluation

Including:

- residuals;
- independent checkpoint metrics;
- spatial coverage;
- scientific success/failure interpretation.

### Scientific diagnostics

Examples:

- stage timings;
- warnings;
- inlier statistics;
- residual summaries;
- failure stage.

These responsibilities do not have to exist as one monolithic "engine" object.

The boundary is conceptual: they belong to reusable scientific/domain code.

---

## API Responsibilities

The API/delivery layer may own:

- HTTP routing;
- transport-level request validation;
- content-type handling;
- upload handling;
- authentication/authorization;
- request size controls;
- rate limiting where applicable;
- mapping external schemas into domain inputs;
- invoking application workflows;
- translating domain results into external schemas;
- translating domain failures into API responses;
- HTTP status codes;
- response serialization;
- API versioning;
- request/response logging;
- service health interfaces.

It must not become the canonical implementation location for:

- SIFT;
- RANSAC;
- transform estimation;
- sensor-specific scientific preprocessing;
- checkpoint RMSE;
- spatial-coverage metrics;
- registration algorithms;
- benchmark logic.

---

## Application / Orchestration Responsibilities

A thin application/use-case layer may coordinate workflows without owning scientific algorithms.

Potential responsibilities include:

- selecting a configured registration workflow;
- resolving input references;
- loading inputs through appropriate adapters;
- selecting scientific configuration;
- invoking the core;
- coordinating artifacts;
- calling persistence abstractions;
- managing workflow state;
- returning domain results.

Conceptually:

```text
API Request
        ↓
Request Adapter
        ↓
Application Use Case
        ↓
Core Engine
        ↓
Domain Result
        ↓
Response Adapter
        ↓
API Response
```

The orchestration layer should not become a second scientific implementation.

---

## Input and Output Boundary

External transport contracts and internal scientific structures should remain distinct.

### Input flow

```text
External API Payload
        ↓
Transport Validation
        ↓
API / Boundary Mapping
        ↓
Domain Input
        ↓
Core Engine
```

### Output flow

```text
Core Engine
        ↓
Domain Result
        ↓
API / Boundary Mapping
        ↓
Serialization
        ↓
External Response
```

This makes it possible to change an API representation without automatically changing the scientific engine.

---

## Domain Inputs

Conceptual domain inputs may include:

- source product/image;
- reference product/image;
- sensor metadata;
- GSD/effective scale;
- projection metadata;
- scientific preprocessing configuration;
- matcher configuration;
- geometric-verification configuration;
- refinement configuration;
- independent checkpoints;
- artifact-generation preferences.

Core input types should not be defined by:

- HTTP request objects;
- multipart upload objects;
- endpoint-specific models;
- frontend state;
- response serializers.

The API may translate those external representations into scientific/domain inputs.

---

## Domain Outputs

Conceptual domain outputs may include:

- candidate correspondences;
- verified inliers;
- tie/control points;
- transformation information;
- registered image/artifact references;
- residual summaries;
- checkpoint metrics;
- spatial coverage;
- stage timings;
- warnings;
- scientific failure state;
- processing metadata;
- reproducibility metadata.

The API may convert these outputs into:

- JSON;
- file responses;
- downloadable artifacts;
- UI-oriented summaries.

The core should not need to know which transport format is used.

---

## API Model Leakage

API model leakage occurs when internal scientific logic directly depends on externally versioned API models.

Anti-pattern:

```text
Core Registration Function
        ↓
Requires HTTP/API Request Object
```

or conceptually:

```text
register(HttpRequest)
```

Preferred boundary:

```text
Registration Domain Input
        ↓
Registration Engine
        ↓
Registration Domain Result
```

followed by an external adapter:

```text
HTTP Payload
        ↓
API Adapter
        ↓
Domain Input
```

No exact programming syntax or class name is mandated.

---

## Files and Image Data

ChandraMap may process large raster and image products.

Transport representation and scientific representation must therefore remain separate.

The API may receive concepts such as:

```text
Multipart Upload
Remote Reference
Job Identifier
```

The application/core side may instead work with concepts such as:

```text
Validated Product Reference
Raster / Array Representation
Sensor Metadata
Prepared Registration Input
```

The core must not need to understand:

- multipart form encoding;
- HTTP upload objects;
- browser file semantics.

---

## Error Handling Boundary

ChandraMap must distinguish **domain/scientific outcomes** from **transport/API errors**.

### Domain / Scientific Errors

Examples include:

- unsupported scientific representation;
- insufficient valid correspondences;
- geometric verification failure;
- invalid scientific configuration;
- incompatible source/reference metadata;
- transformation-estimation failure;
- refinement failure;
- independent evaluation unavailable.

These concepts should exist independently of HTTP.

### API / Transport Errors

Examples include:

- malformed request;
- missing required HTTP field;
- unsupported content type;
- authentication failure;
- authorization failure;
- request-size violation;
- rate-limit event;
- transport timeout.

### Translation boundary

Conceptually:

```text
Core Domain Outcome
        ↓
API Error / Response Mapping
        ↓
HTTP Response
```

The core must not directly return or raise HTTP status codes as its scientific error model.

---

## Scientific Failure vs API Failure

A scientifically unsuccessful registration can still result from a completely valid API request.

For example:

```text
Valid HTTP Request
        ↓
Valid Scientific Inputs
        ↓
Core Registration
        ↓
Insufficient Geometric Support
        ↓
Scientific Failure Result
```

That is different from:

```text
Malformed HTTP Request
        ↓
Transport Validation Failure
```

The architecture must preserve this distinction.

---

## Configuration Boundary

Scientific configuration and service configuration represent different concerns.

### Scientific configuration

May include:

- SIFT settings;
- matcher behavior;
- scale settings;
- preprocessing choices;
- RANSAC configuration;
- transformation model;
- refinement settings;
- checkpoint evaluation settings.

### API/service configuration

May include:

- host;
- port;
- CORS;
- request limits;
- API version;
- authentication configuration;
- worker count;
- transport timeout.

These concerns should remain conceptually separate.

Do not hide scientific benchmark defaults inside route handlers or service-level environment settings without an explicit scientific configuration path.

---

## Sensor-Aware Processing Ownership

Sensor-specific scientific preparation belongs to the scientific/domain side of the architecture.

### OHRC

High-resolution visible/panchromatic preparation belongs in reusable scientific processing.

### TMC-2

Moderate-resolution panchromatic terrain preparation belongs in reusable scientific processing.

### IIRS

Hyperspectral/infrared preparation and conversion into a defined registration-compatible 2D representation are scientific concerns.

They must not be duplicated in API handlers.

The same sensor preparation should be reusable from:

- benchmarks;
- notebooks;
- API workflows;
- future CLI tools.

---

## Matching and Geometry Ownership

ADR-0002 establishes SIFT as the V1 baseline.

The core architecture should allow matcher alternatives conceptually:

```text
Local Matcher Boundary
        │
        ├── SIFT Baseline
        ├── ALIKED + LightGlue
        ├── LoFTR
        └── Future Matcher
```

The API should not require different endpoint-level implementations for each matcher.

Similarly, geometric verification belongs inside reusable scientific logic:

```text
Candidate Correspondences
        ↓
Core Geometry Stage
        ↓
Verified Inliers
        ↓
Transform Estimation
```

not:

```text
Route Handler
        ↓
Inline RANSAC
```

---

## Evaluation Ownership

Independent checkpoint evaluation established by ADR-0003 belongs to reusable evaluation logic.

Canonical flow:

```text
Final Transform
        +
Independent Checkpoints
        ↓
Core Evaluation
        ↓
Checkpoint Metrics
```

The API may expose:

- RMSE;
- residual summaries;
- coverage;
- scientific failure information.

The API does not own their formulas.

Likewise benchmark scripts must be able to invoke the same evaluation logic directly.

---

## Artifact Handling

The scientific core may produce or describe artifacts such as:

- candidate-match visualizations;
- verified-inlier visualizations;
- registered images;
- transforms;
- residual data;
- metric records;
- benchmark metadata.

Generation and persistence should remain distinguishable.

Conceptually:

```text
Core Engine
        ↓
Domain Result / Generated Artifact
        ↓
Application / Storage Adapter
        ↓
Filesystem / External Storage / API Delivery
```

ADR-0005 does not select:

- filesystem architecture;
- object storage;
- cloud storage;
- database storage.

Scientific algorithms should not require one specific persistence technology merely to execute.

---

## Observability and Logging

Scientific diagnostics and service observability should remain distinguishable.

### Core diagnostics

May include:

- algorithm stage timings;
- feature/inlier statistics;
- residual summaries;
- scientific warnings;
- failure stage;
- resolved algorithm configuration.

### Service observability

May include:

- request identifiers;
- HTTP methods;
- endpoint latency;
- server exceptions;
- authentication events;
- response status.

The delivery layer may correlate them.

The scientific core should not depend on a specific web logging implementation.

---

## Benchmarking Requirement

> **ChandraMap scientific benchmarks must be runnable without going through the HTTP API.**

Canonical scientific benchmark flow:

```text
Benchmark Dataset
        ↓
Benchmark Runner
        ↓
Core Engine
        ↓
Scientific Result + Metrics
        ↓
Benchmark Artifact
```

Not:

```text
Benchmark Dataset
        ↓
HTTP Client
        ↓
API Server
        ↓
Core Engine
        ↓
HTTP Response
```

unless the API itself is being benchmarked.

This matters because HTTP introduces additional variables unrelated to algorithm performance.

---

## Core Runtime vs API Latency

Scientific benchmark reports must distinguish core processing runtime from API end-to-end latency.

### Core runtime

May include time spent in:

- sensor preprocessing;
- feature extraction;
- matching;
- geometric verification;
- transformation;
- refinement;
- registration;
- metric computation.

### API end-to-end latency

May additionally include:

- network transfer;
- upload handling;
- request parsing;
- queueing;
- storage;
- serialization;
- response transfer.

Therefore:

> **API latency must not be reported as algorithm runtime without qualification.**

Likewise:

> **Core algorithm runtime must not be presented as complete production request latency.**

---

## Reproducibility

Core-engine independence supports reproducibility because formal scientific runs can record scientific state without requiring transport state.

Where relevant, benchmark provenance may include:

- source/reference identifiers;
- sensor identity;
- preprocessing configuration;
- scale configuration;
- matcher configuration;
- geometric-verification configuration;
- transformation configuration;
- refinement configuration;
- checkpoint configuration;
- software/dependency versions;
- random state where applicable;
- hardware information for runtime measurements;
- result metrics;
- code revision.

API-specific configuration should not be necessary to reproduce a scientific result unless the experiment explicitly studies API/service behavior.

---

## Testability Requirements

The core scientific workflow should be testable without requiring:

- HTTP server startup;
- network access;
- browser;
- frontend;
- API authentication;
- deployment infrastructure.

Similarly, API tests should be able to validate transport contracts without reaching into scientific implementation internals unnecessarily.

The architecture should support multiple testing levels.

---

## Testing Strategy

### Core Unit Tests

Directly test scientific/domain components such as:

- transform utilities;
- metric computation;
- spatial coverage;
- scientific input validation;
- checkpoint evaluation;
- matcher-related processing.

### Core Integration Tests

Test complete scientific workflows without HTTP.

Conceptually:

```text
Known Pair
        ↓
Core Registration Workflow
        ↓
Scientific Result
```

### API Contract Tests

Test transport behavior such as:

- request validation;
- response representation;
- error translation;
- API schema behavior.

### API Integration Tests

Test:

```text
HTTP Request
        ↓
API
        ↓
Application
        ↓
Core
        ↓
API Response
```

### Benchmark Tests / Runs

Invoke the core directly.

Not every test category must exist immediately.

The architecture must permit them.

---

## Notebook, Script, and CLI Reuse

ChandraMap is a research project.

Researchers should conceptually be able to use:

```text
Notebook
        ↓
Core Registration Engine
```

without:

```text
Start API Server
        ↓
Construct HTTP Request
        ↓
Receive Serialized Response
```

Research workflows frequently need direct access to:

- intermediate correspondences;
- residuals;
- transforms;
- configuration;
- stage timings;
- visualizations.

The same principle should permit future scripts or command-line interfaces:

```text
CLI
     ┐
Script
     ├────→ Application / Core
Notebook
     ┘
```

ADR-0005 does not require a CLI to exist today.

---

## Future Worker / Job-System Compatibility

Registration may later become expensive enough to justify asynchronous/background execution.

A future architecture might conceptually use:

```text
API
        ↓
Job Submission
        ↓
Worker
        ↓
Core Engine
```

Because the core is transport-independent, a worker should be able to execute the same scientific logic without extracting algorithms from route handlers later.

ADR-0005 deliberately does not choose:

- queue technology;
- task broker;
- worker framework;
- scheduler;
- database.

---

## Security and Validation Boundary

Security concerns and scientific validation are related but distinct.

### Delivery/security validation

May include:

- authentication;
- authorization;
- CORS;
- request-size limits;
- content-type restrictions;
- malicious-upload handling;
- rate limiting.

### Scientific/domain validation

May include:

- supported sensor representation;
- valid source/reference metadata;
- acceptable transformation configuration;
- checkpoint structure;
- scientific compatibility of inputs.

The core must not assume:

> "The API validated the request, therefore the scientific input is valid."

Different entry points may bypass the API entirely.

The scientific engine must still protect its own domain invariants.

---

## Framework Replacement

A useful architecture test is:

> **Could ChandraMap replace its web framework without rewriting SIFT, geometric verification, registration, and benchmark evaluation?**

The desired answer under ADR-0005 is:

> **Yes, in principle.**

Adapters and application integration may change.

The scientific engine should remain largely unaffected.

---

## Future Interfaces

The architecture should permit future adapters such as:

```text
REST API
CLI
Notebook
Research Script
Batch Runner
Experiment Runner
Background Worker
Desktop Tool
```

to reach the same scientific implementation.

This does not claim those interfaces already exist.

---

## Anti-Patterns

### Fat Route Handlers

Avoid route handlers that directly perform:

```text
Request Parsing
        ↓
Sensor Preprocessing
        ↓
SIFT
        ↓
Matching
        ↓
RANSAC
        ↓
Transform
        ↓
Checkpoint Metrics
        ↓
Artifact Storage
        ↓
JSON Serialization
```

A route handler should primarily adapt, validate, invoke, and serialize.

---

### Core Imports Web Framework

Scientific modules must not require:

- route decorators;
- HTTP request objects;
- HTTP response objects;
- web-framework exceptions.

---

### API Schemas Become Domain Models

Do not make externally versioned API schema classes mandatory internal scientific representations.

Map them at the boundary.

---

### Benchmarks Through HTTP Only

Scientific benchmark execution must not require a running API server.

---

### Duplicate Pipelines

Do not independently implement:

```text
API Pipeline
Notebook Pipeline
CLI Pipeline
Benchmark Pipeline
```

with separate scientific logic.

They should reuse the same core behavior.

---

### HTTP Status Codes as Domain Errors

The core must not encode scientific outcomes as transport-specific status codes.

---

### API Configuration Controls Hidden Scientific Defaults

Do not allow undocumented route/service settings to silently change:

- SIFT behavior;
- RANSAC thresholds;
- scale logic;
- refinement behavior;
- checkpoint evaluation.

Scientific configuration should remain explicit.

---

### Persistence Logic Inside Algorithms

Avoid making registration algorithms directly responsible for a specific storage provider or API delivery mechanism.

---

### Over-Abstraction

Separating the core from HTTP does **not** imply that every function needs an interface, factory, repository, or service wrapper.

The architecture should stay as simple as the responsibility boundaries allow.

---

## What This Decision Establishes

ADR-0005 establishes that:

- scientific core logic is independent from HTTP/API frameworks;
- API code may invoke core scientific functionality;
- core code must not depend on API/transport code;
- scientific benchmarks must be able to call the core directly;
- transport schemas should be mapped into domain inputs;
- domain results should be mapped into API responses;
- scientific configuration and service configuration are separate concepts;
- domain/scientific errors remain independent from HTTP status codes;
- API serialization belongs outside the core;
- sensor preprocessing must not be duplicated in API handlers;
- SIFT, RANSAC, registration, and evaluation remain reusable scientific logic;
- notebooks and scripts can reuse the core directly;
- future CLI/workers can reuse the same engine;
- API latency and scientific runtime are distinct metrics.

---

## What This Decision Does Not Establish

ADR-0005 intentionally does not decide:

- exact Python package layout;
- concrete core class names;
- application service names;
- dependency-injection framework;
- API framework;
- FastAPI vs Flask vs another framework;
- endpoint paths;
- HTTP methods;
- request schema;
- response schema;
- authentication architecture;
- authorization architecture;
- user-account model;
- database technology;
- persistence pattern;
- object storage;
- worker queue;
- task broker;
- scheduler;
- API gateway;
- caching strategy;
- rate limits;
- cloud provider;
- container orchestration;
- serverless deployment;
- distributed-worker topology;
- global retrieval API;
- exact API-versioning implementation;
- frontend architecture details.

These remain separate decisions.

---

## Acceptance Criteria

No numerical performance threshold is established by this ADR.

ADR-0005 is successfully reflected in the architecture when:

- core registration can execute without starting an API server;
- benchmark code can invoke core functionality directly;
- core scientific modules do not require HTTP request/response types;
- route handlers remain focused on delivery/adaptation concerns;
- scientific configuration is not hidden inside route handlers;
- API schemas are translated at the boundary rather than becoming mandatory internal representations;
- domain errors can exist independently of HTTP status codes;
- SIFT/RANSAC/registration/evaluation logic is not duplicated across API, notebooks, scripts, and benchmarks;
- API latency can be measured separately from core scientific runtime;
- future non-HTTP adapters can reuse the core without copying algorithms.

---

## Testing and Validation Plan

### Stage 1 — Direct Core Execution

Execute one known-overlap registration without API startup.

Verify conceptually:

```text
Input Pair
        ↓
Core Engine
        ↓
Registration Result
```

This validates architectural independence.

---

### Stage 2 — Core Tests

Validate scientific functionality directly.

Relevant areas may include:

- input-domain validation;
- matching;
- geometric verification;
- transformation;
- metrics;
- checkpoint evaluation.

---

### Stage 3 — API Adapter

Validate that an API path can map:

```text
External Request
        ↓
Domain Input
        ↓
Core Engine
        ↓
Domain Result
        ↓
External Response
```

without reproducing scientific logic inside the delivery layer.

---

### Stage 4 — Core / API Consistency

Given equivalent scientific inputs and configuration:

```text
Direct Core Run
```

and:

```text
API-Driven Run
```

should invoke the same underlying scientific implementation.

Bit-for-bit identity is not universally required where nondeterminism or transport representation differs.

The scientific execution path should be shared.

---

### Stage 5 — Benchmark Independence

Confirm that benchmark automation can run without:

- API server;
- network;
- HTTP client.

---

### Stage 6 — Failure Translation

Verify that a domain failure can be:

1. produced by the core independently;
2. translated by an API adapter;
3. represented externally without requiring HTTP semantics inside the engine.

---

## Consequences

### Positive Consequences

#### Reusable scientific engine

The same scientific behavior can support research, benchmarking, services, and future interfaces.

#### Easier benchmarking

Benchmarks can execute algorithms directly.

#### Cleaner scientific tests

Tests need not initialize web infrastructure.

#### Better notebook workflows

Researchers can inspect intermediate scientific state directly.

#### API framework independence

Transport implementation can evolve without rewriting algorithms.

#### Future CLI support

A command-line adapter can reuse existing scientific logic.

#### Future worker compatibility

Background execution can invoke the same engine.

#### Clearer error semantics

Scientific failures remain distinguishable from transport failures.

#### Reduced duplication

Sensor preparation, matching, geometry, and evaluation remain canonical.

#### Better runtime measurement

Scientific processing time can be measured independently from transport overhead.

#### Lower coupling between frontend/backend changes and algorithms

Changes to delivery contracts are less likely to alter scientific behavior.

#### Clearer contribution boundaries

Contributors can distinguish scientific-core changes from API/service changes.

---

### Negative Consequences / Trade-offs

#### Mapping code is required

API schemas and domain structures may require explicit conversion.

#### Domain structures require definition

The project must define reusable scientific inputs and results sufficiently clearly.

#### Dependency discipline is required

Developers must avoid convenient shortcuts that leak web-framework types inward.

#### Some validation duplication may be intentional

Transport validity and scientific validity are not the same thing.

Both may need checks.

#### More architectural work during initial development

A direct prototype may require fewer files, but that simplicity does not scale well to ChandraMap's research use cases.

---

### Neutral Consequences

- the API remains an important project interface;
- frontend architecture may evolve independently;
- storage architecture remains unresolved;
- worker/queue architecture remains unresolved;
- deployment topology remains unresolved;
- the application layer may remain thin;
- exact package boundaries may evolve while preserving the dependency rule.

---

## Risks and Mitigations

| Risk                                               | Why It Matters                                     | Mitigation                                                                               |
| -------------------------------------------------- | -------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| Route handlers accumulate scientific logic         | Recreates tight coupling                           | Keep handlers focused on transport validation, adaptation, invocation, and serialization |
| API models leak into the core                      | Couples algorithms to external transport contracts | Map API schemas into domain-oriented inputs                                              |
| Core becomes over-abstracted                       | Adds complexity without scientific value           | Add interfaces only at real responsibility/replacement boundaries                        |
| Scientific preprocessing is duplicated             | API and benchmark behavior may diverge             | Keep canonical sensor preprocessing on the core/domain side                              |
| Benchmark behavior differs from API behavior       | Results become hard to interpret                   | Ensure both invoke the same scientific implementation                                    |
| Domain errors are converted into HTTP too early    | Core becomes transport-specific                    | Translate domain outcomes at the API boundary                                            |
| Artifact persistence enters algorithms             | Reduces reuse and portability                      | Separate scientific artifact generation from storage/delivery where practical            |
| API latency is reported as algorithm runtime       | Produces misleading benchmark claims               | Track scientific runtime and end-to-end service latency separately                       |
| Framework-specific dependencies spread inward      | Future migration becomes costly                    | Enforce inward dependency direction during review and testing                            |
| Multiple interfaces duplicate pipelines            | Scientific behavior may drift                      | Reuse the same application/core workflow                                                 |
| API validation is treated as scientific validation | Non-API callers may bypass required domain checks  | Keep domain validation inside the scientific/domain boundary                             |
| Application layer accumulates algorithms           | Separation exists only nominally                   | Keep application layer focused on coordination rather than scientific implementation     |

These are architectural risks, not claims that they have already occurred.

---

## Deferred Decisions

The following decisions remain explicitly outside ADR-0005:

- exact Python package hierarchy;
- concrete core classes;
- exact application-service names;
- interface naming;
- dependency-injection mechanism;
- API framework;
- endpoint definitions;
- request schemas;
- response schemas;
- authentication;
- authorization;
- user accounts;
- persistence implementation;
- database;
- object storage;
- worker queue;
- scheduler;
- task broker;
- API gateway;
- rate limiting;
- caching;
- deployment platform;
- container orchestration;
- distributed workers;
- global retrieval API;
- production service topology.

These may receive future ADRs where architectural significance justifies them.

---

## Relationship to Repository Documentation

ADR-0005 should be interpreted alongside ChandraMap's architecture documentation.

### ADR documentation

- [`README.md`](README.md) — ADR operating guide
- [`ADR_TEMPLATE.md`](ADR_TEMPLATE.md) — ADR template, where present
- [`0001-v1-known-overlap-first.md`](0001-v1-known-overlap-first.md) — V1 known-overlap scope
- [`0002-sift-as-v1-baseline.md`](0002-sift-as-v1-baseline.md) — SIFT V1 baseline
- [`0003-independent-checkpoints.md`](0003-independent-checkpoints.md) — independent registration evaluation
- [`0004-no-global-retrieval-in-v1.md`](0004-no-global-retrieval-in-v1.md) — V1 retrieval boundary

### Architecture documentation

- [`../architecture/system-overview.md`](../architecture/system-overview.md) — overall system architecture
- [`../architecture/v1-pipeline.md`](../architecture/v1-pipeline.md) — V1 processing flow
- [`../architecture/core-engine-architecture.md`](../architecture/core-engine-architecture.md) — scientific core architecture
- [`../architecture/backend-architecture.md`](../architecture/backend-architecture.md) — backend responsibilities
- [`../architecture/module-map.md`](../architecture/module-map.md) — module responsibility mapping
- [`../architecture/data-flow.md`](../architecture/data-flow.md) — data movement through the system
- [`../architecture/output-flow.md`](../architecture/output-flow.md) — result/artifact flow

The architecture documents describe the current system.

This ADR records **why** the core/API separation is an architectural requirement.

---

## Relationship to Future ChandraMap Versions

The separation defined here should survive algorithm evolution.

### V1

```text
Known Overlap
        ↓
SIFT Baseline
        ↓
Registration Core
```

### Later matcher evolution

```text
Improved Matcher
        ↓
Same Scientific/Core Boundary
```

### Later retrieval evolution

```text
Global Retrieval
        ↓
Candidate Reference
        ↓
Same Registration Core
```

### Later interface evolution

```text
API
CLI
Worker
Notebook
Experiment Runner
        ↓
Same Core Capabilities
```

Future scientific algorithms may change.

The architectural principle remains:

```text
Delivery Interface
        ≠
Scientific Engine
```

unless a future ADR demonstrates a stronger reason to supersede this boundary.

---

## Future Retrieval Compatibility

ADR-0004 excludes global retrieval from V1, but future versions may introduce it.

The preferred evolution is:

```text
Global Search / Retrieval
        ↓
Candidate Reference Region
        ↓
Existing Registration Use Case
        ↓
Core Registration Engine
```

Retrieval should feed the registration system.

It should not require the core registration engine to import:

- vector-index infrastructure;
- retrieval endpoints;
- search-service objects.

This preserves the local-registration architecture established in V1.

---

## Migration / Compatibility Considerations

This ADR does not assert that tightly coupled implementation currently exists.

If implementation is found to mix API and scientific responsibilities, migration should conceptually proceed as follows:

1. identify scientific logic embedded in delivery code;
2. extract reusable domain operations;
3. define domain-oriented inputs and outputs;
4. make API handlers adapt requests into those inputs;
5. make handlers invoke the extracted scientific implementation;
6. preserve intended scientific behavior;
7. add direct core tests;
8. add API contract/integration coverage;
9. remove unnecessary inward web/API dependencies.

Large migrations should preserve benchmark comparability where scientific logic is intended to remain unchanged.

If scientific outputs change during an architectural extraction that was intended to be behavior-preserving, the difference should be investigated rather than assumed harmless.

---

## Backward Compatibility

ADR-0005 is primarily an internal architectural decision rather than an external API-contract definition.

Separating the core from the API should not, by itself, require changing scientific behavior.

Likewise, it does not automatically define API backward-compatibility policy.

If API contracts later change, their compatibility/versioning policy should be addressed separately.

---

## Revisit Conditions

ADR-0005 may be revisited if:

- the scientific core intentionally becomes a separately deployed remote service;
- process boundaries become fundamental to scientific computation;
- external clients are intentionally required to access all scientific capabilities only through a service contract;
- a distributed architecture materially changes ownership of scientific computation;
- maintaining an in-process reusable core becomes impractical for validated technical reasons;
- another architecture demonstrates stronger reproducibility and maintainability without unacceptable coupling.

A future change must not erase this ADR.

If superseded:

1. retain this file;
2. change its status to `Superseded`;
3. populate `Superseded By`;
4. create a new ADR describing the new context, decision, and migration implications.

---

## Architecture Summary

The intended architecture is:

```text
                    DELIVERY
        ┌──────────────┼──────────────┐
        │              │              │
       API            CLI        Notebook/Script
        │              │              │
        └──────────────┼──────────────┘
                       ↓
             Application / Use Case
                       ↓
               ChandraMap Core
                       ↓
        Scientific Registration Result
```

The following dependency is invalid:

```text
Core Matching / Registration
        ↓
HTTP Framework
```

The architectural test is simple:

> **Can correspondence, registration, and evaluation run directly without HTTP?**

Under ADR-0005, the answer must be:

> **Yes.**

---

## References

### Internal ChandraMap Documentation

- [`README.md`](README.md) — ADR operating guide
- [`ADR_TEMPLATE.md`](ADR_TEMPLATE.md) — ADR template, where present
- [`0001-v1-known-overlap-first.md`](0001-v1-known-overlap-first.md) — V1 known-overlap-first architecture
- [`0002-sift-as-v1-baseline.md`](0002-sift-as-v1-baseline.md) — SIFT local-matching baseline
- [`0003-independent-checkpoints.md`](0003-independent-checkpoints.md) — independent registration evaluation
- [`0004-no-global-retrieval-in-v1.md`](0004-no-global-retrieval-in-v1.md) — exclusion of global retrieval from V1
- [`../architecture/system-overview.md`](../architecture/system-overview.md) — overall ChandraMap architecture
- [`../architecture/v1-pipeline.md`](../architecture/v1-pipeline.md) — V1 scientific pipeline
- [`../architecture/core-engine-architecture.md`](../architecture/core-engine-architecture.md) — core-engine responsibilities
- [`../architecture/backend-architecture.md`](../architecture/backend-architecture.md) — backend/service architecture
- [`../architecture/module-map.md`](../architecture/module-map.md) — module responsibility map
- [`../architecture/data-flow.md`](../architecture/data-flow.md) — system data flow
- [`../architecture/output-flow.md`](../architecture/output-flow.md) — result and artifact flow

### Architectural Concepts

Relevant general software-architecture principles include:

- separation of concerns;
- dependency inversion;
- ports-and-adapters style boundaries;
- transport-independent domain logic.

These concepts support the decision but do not replace ChandraMap-specific reasoning.

The purpose of ADR-0005 is not to adopt a named architecture framework wholesale.

It is to preserve one concrete project requirement:

> **ChandraMap's API exposes the engine; it does not define the engine.**

The scientific registration system must remain independently runnable, testable, benchmarkable, and reusable so ChandraMap can evolve its delivery infrastructure without rewriting its computer-vision, remote-sensing, and evaluation core.
