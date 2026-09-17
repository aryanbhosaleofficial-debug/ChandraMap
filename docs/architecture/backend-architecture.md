# ChandraMap Backend Architecture

The ChandraMap backend is the **application and orchestration boundary around the reusable scientific core engine**.

Its primary role is to receive application requests, validate boundary-level inputs, resolve scientific inputs and configuration, invoke the shared ChandraMap Core Engine, preserve the resulting scientific meaning, coordinate artifacts or application state where required, and expose the result to clients.

The fundamental dependency direction is:

```text
Client / Frontend / External Consumer
                 ↓
              Backend
                 ↓
      Application Orchestration
                 ↓
       ChandraMap Core Engine
                 ↓
        Scientific Components
```

The reverse dependency must not exist:

```text
Scientific Core
      ↓
HTTP Router / Web Framework
```

The backend is **not** the registration algorithm.

It surrounds the scientific engine without duplicating it.

This document describes the logical backend/application architecture. It does not assert that every capability described here is currently implemented, nor does it prescribe a backend framework, database, queue, cache, deployment platform, or API technology.

---

## 1. Purpose

This document defines:

* the role of the backend within ChandraMap
* the boundary between application logic and scientific logic
* request handling responsibilities
* scientific input handling at application boundaries
* source/reference preservation
* configuration resolution
* Core Engine invocation
* result translation
* scientific rejection handling
* artifact responsibilities
* optional persistence responsibilities
* optional long-running execution architecture
* security and trust boundaries
* resource-management concerns
* provenance and reproducibility requirements
* interactions with frontend, CLI, benchmarks, and research workflows
* backend architectural invariants
* backend anti-patterns

This document intentionally does **not** define:

* scientific matching algorithms
* RANSAC implementation
* transformation mathematics
* metric equations
* concrete API routes
* OpenAPI schemas
* database schemas
* deployment topology
* infrastructure-as-code
* frontend architecture
* benchmark methodology

Those responsibilities belong elsewhere.

---

## 2. Architecture Status

No specific backend framework, HTTP stack, persistent database, queue, worker system, cache service, authentication system, or deployment platform is established by this document.

Therefore this file should be interpreted primarily as:

> **the logical backend/application architecture ChandraMap should preserve when backend functionality exists or evolves.**

The presence of a component in this architecture does not prove that it is currently:

* Implemented
* Tested
* Deployed
* Supported
* Benchmarked

Current implementation status must come from repository code, configuration, tests, and deployment evidence.

Target architecture must not be confused with current implementation.

---

## 3. Backend Responsibilities

The backend/application layer may own responsibilities such as:

* receiving application requests
* parsing transport-level input
* boundary validation
* authentication/authorization where actually required
* safe external-file handling
* resolving source/reference inputs
* resolving supported scientific configuration
* coordinating application use cases
* invoking the shared Core Engine
* tracking long-running work where a job architecture exists
* translating scientific results into application responses
* exposing artifact references
* coordinating application-level persistence where required
* operational logging
* resource protection
* client-facing error translation

The backend should remain **scientifically thin**.

---

## 4. What the Backend Does Not Own

The backend should not independently implement:

* SIFT
* RootSIFT
* ALIKED
* LightGlue
* LoFTR
* RANSAC
* affine estimation
* homography estimation
* tie-point refinement
* image registration mathematics
* RMSE definitions
* spatial-coverage definitions
* inlier-ratio definitions
* scientific acceptance criteria
* registration confidence calibration

These belong to the shared scientific core or its authoritative scientific configuration.

The rule is:

> **Backend orchestrates science; Core Engine performs science.**

---

## 5. Architecture at a Glance

```text
┌─────────────────────────────────────────────────────────────┐
│                  Clients / Applications                     │
│                                                             │
│   Frontend   CLI Client   External Integration   Tooling    │
└────────────────────────────┬────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────┐
│                    Backend Boundary                         │
│                                                             │
│  Transport / Interface                                     │
│  Boundary Validation                                       │
│  Safe Input Handling                                       │
│  Application Services                                      │
│  Configuration Resolution                                  │
│  Job Coordination                 [if applicable]           │
│  Resource / Security Controls                              │
└────────────────────────────┬────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────┐
│                ChandraMap Core Engine                       │
│                                                             │
│ Representation → Correspondence → Geometry                  │
│ → Registration → Evaluation → Accept / Reject              │
└────────────────────────────┬────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────┐
│               Structured Scientific Result                  │
└────────────────────────────┬────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────┐
│             Backend Result / Artifact Layer                 │
│                                                             │
│ Result Translation                                         │
│ Artifact References                                        │
│ Optional Persistence                                       │
│ Application-Level Status                                   │
└────────────────────────────┬────────────────────────────────┘
                             │
                             ▼
                        Client Result
```

The backend boundary surrounds the scientific core.

It does not replace it.

---

## 6. Backend vs Core Engine

| Responsibility                     |                         Backend |                                          Core Engine |
| ---------------------------------- | ------------------------------: | ---------------------------------------------------: |
| Request parsing                    |                             Yes |                                                   No |
| Transport serialization            |                             Yes |                                                   No |
| Boundary validation                |                             Yes |                Domain validation also occurs in core |
| Safe file handling                 |                             Yes |                       Scientific interpretation only |
| Source/reference role preservation |                        Preserve |                                    Interpret and use |
| Metadata transport                 |                Preserve/resolve |                             Interpret scientifically |
| Configuration selection            | Resolve supported configuration |                                           Execute it |
| Representation/preprocessing       |                              No |                                                  Yes |
| Feature extraction/matching        |                              No |                                                  Yes |
| RANSAC                             |                              No |                                                  Yes |
| Transform estimation               |                              No |                                                  Yes |
| Refinement                         |                              No |                                Yes, where configured |
| Registration/warp                  |                              No |                                                  Yes |
| Scientific metrics                 |                              No |                                                  Yes |
| Scientific accept/reject           |                              No |                                                  Yes |
| Response translation               |                             Yes |                                                   No |
| Job lifecycle                      |                   If applicable |                                                   No |
| Persistence                        |                   If applicable |                                                   No |
| Artifact exposure                  |                   If applicable | Generation/production may occur around core workflow |
| Benchmark aggregation              |                              No |                No; benchmark runner owns aggregation |

The Core Engine remains authoritative for scientific interpretation.

---

## 7. Dependency Direction

The preferred dependency direction is:

```text
Frontend / External Client
            ↓
      Transport Layer
            ↓
    Application Services
            ↓
   Core Engine Integration
            ↓
  ChandraMap Core Engine
            ↓
   Scientific Dependencies
```

Possible application-side adapters may sit beside application services where real requirements exist:

```text
Application Service
      ├── Artifact Adapter
      ├── Persistence Adapter
      └── Job Adapter
```

These are conceptual responsibilities, not claims that such adapters currently exist.

The scientific core must not depend upward on:

* HTTP
* route handlers
* API DTOs
* frontend components
* backend database models

---

## 8. Logical Backend Layers

A framework-neutral backend can be understood through several responsibilities.

### Transport / Interface Layer

Receives and returns application-level requests.

### Boundary Validation Layer

Checks request structure and externally supplied input before application processing.

### Application Service Layer

Coordinates a registration use case.

### Core Engine Integration

Translates application-level input into a scientific invocation and receives the scientific result.

### Result Translation Layer

Preserves scientific semantics while exposing an application-facing result.

### Artifact / Persistence Layer

Handles generated files or persistent application state only where required.

These are logical responsibilities.

They do not require one source module per layer.

---

## 9. Transport Layer

The transport layer may eventually be exposed through:

* an HTTP application
* a local service boundary
* another application interface

depending on actual repository needs.

Its responsibilities are conceptually limited to:

```text
Receive Request
      ↓
Deserialize
      ↓
Boundary Validation
      ↓
Application Service
      ↓
Serialize Result
```

It should not perform scientific registration itself.

---

### Thin Controller Principle

Where routes/controllers exist, prefer:

```text
Route / Controller
        ↓
Request Validation
        ↓
Application Service
        ↓
Core Engine
        ↓
Result Translation
```

Avoid:

```text
Route
  ↓
Open Raster
  ↓
Run SIFT
  ↓
Run RANSAC
  ↓
Compute RMSE
  ↓
Write PNG
  ↓
Return Response
```

Scientific algorithms should not live inside transport handlers.

---

## 10. Request Concepts

A registration request may conceptually identify:

* source observation or product
* reference observation or candidate region
* supported processing configuration
* optional scientific metadata
* optional evaluation/check-point information
* requested result/artifact options

These are conceptual categories.

Exact request fields belong in API/contract documentation.

---

## 11. Source and Reference Roles

The backend must preserve explicit scientific roles:

```text
SOURCE
→ observation being registered

REFERENCE
→ observation / region defining registration context
```

Avoid reducing the scientific contract to:

```text
file1
file2
```

if the distinction is then lost.

Source/reference direction affects:

* transform meaning
* coordinates
* warping
* error units
* result interpretation

The backend must not reverse these roles during translation.

---

## 12. Input Acquisition

Possible application designs may obtain scientific inputs from sources such as:

* uploaded files
* known repository/dataset references
* benchmark-defined pair identity
* previously prepared application artifacts
* connected scientific data sources

Only mechanisms that actually exist or become explicitly supported should be documented as current behavior.

The backend architecture must not assume every input arrives through file upload.

---

## 13. Boundary Validation

Backend boundary validation protects the application from malformed or unsafe external input before invoking scientific processing.

Potential conceptual checks include:

* required request information
* supported request structure
* valid identifiers
* file presence
* basic size/resource constraints
* recognized application options

Boundary validation is different from scientific validation.

---

## 14. Validation Layers

ChandraMap should distinguish at least three validation concerns.

### Transport Validation

Questions such as:

> Is required request information present?

### File / Boundary Validation

Questions such as:

> Can this external input be handled safely enough to inspect/process?

### Scientific Validation

Questions such as:

> Is this representation scientifically usable by the selected method?

The backend may handle the first two.

The Core Engine should remain authoritative for scientific/domain validation.

---

### Double Validation Is Intentional

A request passing transport validation means:

> the application request is structurally acceptable.

It does not mean:

> the scientific input is suitable for registration.

This is not unnecessary duplication.

The two layers protect different boundaries.

---

## 15. Scientific File Handling

Where the backend accepts external scientific files, they must be treated as untrusted software inputs.

Potential concerns include:

* malformed rasters
* unexpectedly large inputs
* misleading filenames
* invalid formats
* path traversal
* archive safety if archives are supported
* temporary-storage cleanup
* resource exhaustion

Detailed controls belong in security documentation and actual implementation.

---

### File Extension Is Not Proof

A filename ending in:

```text
.tif
```

does not prove that its contents form a valid or safe scientific raster.

The architecture should not rely exclusively on extensions to establish input validity.

---

### Filename Trust

User-supplied filenames should be treated as descriptive input, not trusted filesystem paths.

Backend storage and path resolution must preserve application trust boundaries.

---

## 16. Temporary Input Lifecycle

If external inputs are materialized temporarily, the conceptual lifecycle is:

```text
External Input
      ↓
Validated Temporary Representation
      ↓
Scientific Processing
      ↓
Scientific Result / Derived Artifacts
      ↓
Cleanup According to Application Policy
```

This document does not define:

* storage directories
* retention durations
* cleanup schedules

because those depend on actual implementation and deployment requirements.

---

## 17. Scientific Metadata at the Backend Boundary

The backend may receive or resolve metadata such as:

* mission
* instrument
* product identity
* GSD
* projection
* footprint
* acquisition information

The backend's responsibility is primarily to preserve or resolve known information.

It should not fabricate missing scientific metadata.

If information is unknown:

> **unknown should remain unknown.**

Scientific interpretation belongs to the Core Engine.

---

## 18. Application Service Layer

The application-service layer coordinates a concrete use case without implementing its scientific algorithms.

A registration use case may conceptually follow:

```text
Run Registration
       ↓
Resolve Source / Reference
       ↓
Resolve Supported Configuration
       ↓
Prepare Application Context
       ↓
Invoke Core Engine
       ↓
Receive Scientific Result
       ↓
Handle Application Artifacts / Persistence
       ↓
Return Application Result
```

The application service controls orchestration.

The Core Engine controls scientific computation.

---

## 19. Core Engine Integration

The backend should invoke the **same reusable scientific Core Engine** that can also support:

* benchmark runners
* CLI workflows
* research integrations
* future application interfaces

Preferred:

```text
                  Shared Core Engine
                  /              \
                 /                \
            Backend            Benchmark Runner
```

Avoid:

```text
Backend Scientific Pipeline

and separately

Benchmark Scientific Pipeline
```

with duplicated algorithms.

---

### Backend Must Not Create a Second Pipeline

The backend must not own its own versions of:

* preprocessing
* matching
* geometry
* registration
* metrics
* acceptance logic

unless those are simply calls into authoritative shared scientific components.

---

## 20. Configuration Resolution

The backend may allow clients to select from intentionally supported workflows or configurations.

Its responsibility is:

> **resolve which approved scientific configuration should run.**

The Core Engine's responsibility is:

> **execute that configuration.**

Canonical benchmark specifications remain authoritative for benchmark methodology.

---

### No Hidden Backend Defaults

Equivalent backend and direct Core Engine execution should not behave differently because the backend silently changes:

* match thresholds
* RANSAC behavior
* transform model
* preprocessing
* quality gates

Important scientific configuration should come from authoritative methodology.

---

### Client Configuration Must Be Controlled

The existence of an internal configuration parameter does not automatically mean it should be publicly client-configurable.

User-facing options should be:

* intentional
* validated
* scientifically meaningful
* compatible with supported workflows

---

## 21. Benchmark Version vs API Version

This distinction is critical.

```text
Benchmark V1
```

means a scientific research/pipeline configuration.

It does **not** mean:

```text
API Version 1
```

Benchmark V1–V4 and API versioning are separate concerns.

Do not implicitly map them together.

---

## 22. Request Lifecycle

A synchronous conceptual lifecycle is:

```text
Client
  │
  ▼
Request Boundary
  │
  ├── invalid request ─────────────→ Request Error
  │
  ▼
Boundary Validation
  │
  ▼
Application Service
  │
  ▼
Resolve Source / Reference / Configuration
  │
  ▼
Invoke Core Engine
  │
  ├── invalid scientific input ────→ Structured Invalid Result
  │
  ├── unsupported capability ──────→ Structured Unsupported Result
  │
  ├── scientific rejection ────────→ Structured Rejected Result
  │
  └── unexpected failure ──────────→ Internal / Runtime Error
  │
  ▼
Scientific Result
  │
  ▼
Optional Artifact / Persistence Handling
  │
  ▼
Result Translation
  │
  ▼
Client
```

The backend preserves scientific outcome semantics rather than collapsing them into one generic application status.

---

## 23. Scientific Result Translation

The Core Engine result is authoritative.

The backend may convert it into an application/transport representation.

Translation must preserve:

* source/reference semantics
* result status
* transform type
* transform direction
* coordinate domains
* candidate/verified distinctions
* metric values
* metric units
* rejection reasons
* provenance where exposed

---

### Translation Must Not Recompute Science

Avoid:

```text
Core Engine RMSE
        ↓
Backend Recomputes RMSE Differently
```

or:

```text
Core Result = Rejected
        ↓
Backend Renames It "Successful"
```

Transport formatting must preserve scientific meaning.

---

## 24. Contract Boundary

Where backend transport contracts exist, it can be useful to conceptually separate:

```text
Transport Request
       ↓
Application Input
       ↓
Core Engine Invocation
```

and:

```text
Core Scientific Result
       ↓
Application Result
       ↓
Transport Response
```

This separation prevents public client contracts from becoming tightly coupled to internal scientific library objects.

No specific schema framework is prescribed here.

---

## 25. Third-Party Object Leakage

Public/backend contracts should generally avoid exposing implementation-specific objects such as:

* raw NumPy arrays
* OpenCV-specific structures
* framework tensors
* matcher-library internal objects

unless deliberately serialized through a stable documented contract.

External consumers should depend on ChandraMap scientific meaning rather than one implementation library's internal representation.

---

## 26. Coordinates, Transforms, and Units

Scientific semantics must survive backend translation.

### Coordinates

If correspondences are exposed, their coordinate convention must remain clear.

Possible domains include:

* source-image `(x, y)`
* reference-image `(x, y)`
* array `(row, column)`
* projected/geospatial coordinates

Do not serialize coordinates as ambiguous unlabeled number pairs where their interpretation matters.

---

### Transforms

If a transform is externally exposed, it should conceptually preserve:

* model family
* source domain
* reference/destination domain
* direction
* numerical parameters

Avoid returning only:

```text
[ matrix values ]
```

without explaining what the matrix means.

---

### Units

Metrics should retain explicit unit semantics.

Conceptually:

```text
RMSE
=
value
+
coordinate domain
+
unit
```

not:

```text
RMSE = 0.8
```

without context.

---

### Ground Units

The backend must not silently convert:

```text
pixels
→ metres
```

without scientifically valid context supplied by the Core Engine or authoritative geospatial information.

---

### Lunar CRS

The backend must not silently assign Earth WGS84/EPSG:4326 semantics to lunar coordinates.

Longitude-convention transformations, where supported, must remain explicit.

---

## 27. Scientific Rejection vs Backend Error

Scientific rejection is expected scientific behavior.

Examples include:

* insufficient features
* insufficient useful matches
* no valid robust geometric model
* poor spatial support
* final quality below configured acceptance criteria

These conditions may mean:

```text
Software Execution
      =
Successful

Scientific Registration
      =
Rejected
```

The backend must preserve this distinction.

---

### Scientific Rejection Is Not a Server Crash

A valid request yielding:

> insufficient geometric support

should not automatically be interpreted as an unexpected backend failure.

Exact transport status codes belong in API documentation.

---

## 28. Application Failure Taxonomy

| Condition                             | Core / Scientific Meaning  | Backend Meaning                           |
| ------------------------------------- | -------------------------- | ----------------------------------------- |
| Missing/malformed request information | Core not invoked           | Request/boundary error                    |
| Unusable scientific representation    | Invalid or unsupported     | Client-visible invalid/unsupported result |
| No usable features                    | Scientific rejection       | Completed scientific attempt, rejected    |
| Geometry cannot be established        | Scientific rejection       | Completed scientific attempt, rejected    |
| Quality criteria not satisfied        | Scientific rejection       | Completed scientific attempt, rejected    |
| Required model/dependency unavailable | Dependency/runtime failure | Application/runtime failure               |
| Unexpected exception                  | Software defect            | Internal error                            |

Scientific and operational failures should not be collapsed into one category.

---

## 29. No Identity-Transform Fallback

Never design:

```text
Core Registration Failed
        ↓
Backend Inserts Identity Matrix
        ↓
Return "Success"
```

unless identity is genuinely supported by the scientific result.

Failure must remain visible.

---

## 30. No Silent Scientific Fallback

Avoid application behavior such as:

```text
Requested V1
      ↓
V1 Rejects
      ↓
Backend Silently Runs Learned Matcher
      ↓
Result Still Labelled V1
```

If a multi-method fallback workflow is ever supported, it must be explicitly defined and the actual executed method must remain traceable.

---

## 31. No Fake Confidence

Do not translate a matcher score into statements such as:

> 95% registration confidence

unless a separately defined and validated calibration method supports that interpretation.

Matcher confidence, geometric support, and calibrated registration confidence are different concepts.

---

## 32. Artifacts

Potential generated artifacts may include:

* registered preview
* candidate-match visualization
* verified-inlier visualization
* rejected-outlier visualization
* overlay
* residual visualization
* report

Artifacts support:

* debugging
* interpretation
* communication

They are not the authoritative scientific result.

---

### Result vs Artifact

```text
Structured Scientific Result
        ↓
Authoritative Science

Artifact
        ↓
Derived Representation of That Science
```

A PNG overlay must not become the sole record of registration quality.

---

### Artifact Exposure

Where the backend exposes artifacts, it may return established application references rather than embedding large scientific binaries directly in every result.

Actual artifact-serving behavior depends on implemented contracts.

---

### Artifact Storage

This architecture does not assume:

* local filesystem
* database blobs
* object storage
* cloud storage

The storage mechanism is an outer implementation decision.

---

### Artifact Security

If artifacts become externally accessible, consider:

* authorization where required
* safe path resolution
* unpredictable/safe identifiers where appropriate
* retention
* scientific metadata leakage

Detailed policy belongs in security/application documentation.

---

## 33. Persistence

A backend does not inherently require a database.

Possible application models include:

### Stateless

```text
Request
  ↓
Core Engine
  ↓
Result
  ↓
Response
```

### Temporary State

Short-lived data exists only for:

* processing
* artifacts
* long-running job state

### Persistent State

Run/result history is intentionally stored for future retrieval or analysis.

The chosen model depends on actual application requirements.

---

### Database Boundary

If persistence technology exists, it should store application/result state according to actual design.

It should not become the owner of:

* RANSAC
* RMSE
* transform estimation
* scientific acceptance

Database models are outer-layer concerns.

---

### Result Persistence

Where scientific results are persisted, preserve enough information to associate the result with:

* source
* reference
* configuration/method
* status
* artifacts
* relevant provenance

Exact schemas belong elsewhere.

---

### Source-Data Retention

Do not assume external lunar products or uploaded files should be retained permanently.

Retention should follow:

* real application needs
* provider/data policy
* storage/resource constraints

---

## 34. Caching

Scientific/backend workflows may eventually cache derived data such as:

* prepared representations
* descriptors
* image-pyramid levels
* retrieval descriptors
* reference indexes

where justified.

A cache should remain:

* rebuildable
* traceable
* non-authoritative

---

### Scientific Cache Identity

A cached derivative may depend on:

```text
Source Identity
+
Representation Configuration
+
Relevant Algorithm / Model Identity
```

A cached artifact from one methodology should not silently be reused under an incompatible methodology.

No caching technology is prescribed here.

---

## 35. Long-Running Execution

Some registration workloads may be suitable for direct/synchronous application execution.

Others may eventually justify long-running job architecture.

Potential triggers include:

* large imagery
* expensive learned inference
* GPU coordination
* multiple retrieval candidates
* many generated artifacts
* execution that outlives a normal client request
* need for status retrieval after disconnection

This does **not** imply that ChandraMap currently has a job system.

---

### Job Architecture Is Optional

Only when justified, the target lifecycle may resemble:

```text
Submit Registration
        ↓
Validate
        ↓
Create Application Job
        ↓
Execute Core Engine
        ↓
Store Scientific Result
        ↓
Application Job Completes
        ↓
Client Retrieves Result
```

No queue, scheduler, worker, or job technology is prescribed.

---

## 36. Scientific Status vs Job Status

These status dimensions must remain distinct.

Example:

```text
Application Job Status
=
Completed
```

while:

```text
Scientific Registration Status
=
Rejected
```

The job successfully executed the scientific workflow.

The scientific workflow concluded that the registration was not trustworthy.

Do not equate:

> completed

with:

> scientifically accepted.

---

### Other Possible Combinations

Conceptually:

| Application Execution | Scientific Result                        |
| --------------------- | ---------------------------------------- |
| Completed             | Accepted                                 |
| Completed             | Rejected                                 |
| Completed             | Unsupported/invalid scientific input     |
| Failed                | No authoritative final scientific result |

Exact status vocabulary belongs to actual contracts.

---

## 37. Retry Policy

Automatic retries should not blindly repeat deterministic scientific rejection.

Examples that generally should **not** be retried merely because they failed scientifically:

* insufficient features
* no geometric model
* registration rejected by quality criteria

Retries are more appropriate for transient operational failures where applicable.

No retry mechanism is assumed here.

---

## 38. Retrieval Integration

Later workflows may include global reference retrieval.

The backend may orchestrate a use case such as:

```text
Application Request
       ↓
Reference Candidate Resolution
       ↓
Candidate Region(s)
       ↓
Core Local Registration
       ↓
Scientific Result
```

Retrieval should remain a scientific capability or adjacent scientific subsystem.

It should not be implemented directly inside route/controller code.

---

### FAISS Boundary

Where FAISS is used:

```text
Global Descriptor
       ↓
Vector Index / Search
       ↓
Candidate Reference IDs
```

FAISS is not:

* a local image matcher
* RANSAC
* transform estimation
* registration evaluation

The backend may invoke a retrieval capability using FAISS.

It should not misrepresent FAISS as the registration engine.

---

## 39. Learned Models and Accelerator Boundary

Later scientific configurations may use learned methods requiring model checkpoints or accelerators.

Where relevant, application architecture may need to preserve:

* model/checkpoint identity
* availability
* trusted acquisition/source
* configuration
* runtime device context

This document does not prescribe a model registry or loading strategy.

---

### Model Loading

Heavy model initialization should be treated as a deliberate runtime concern rather than being embedded blindly in every request handler.

The correct lifecycle depends on the actual backend/framework/runtime.

---

### GPU Boundary

GPU availability must not become an implicit requirement for canonical V1 when the V1 methodology does not require it.

Advanced configurations may have different resource needs.

---

## 40. Resource Management

Scientific imagery can place significant pressure on:

* memory
* CPU
* storage
* GPU memory where relevant
* processing time

Backend architecture should therefore consider resource control proportional to exposure and workload.

Potential concerns include:

* input size
* raster dimensions
* concurrent scientific executions
* artifact volume
* model memory
* temporary storage

No numeric limits are defined here.

---

### Controlled Concurrency

If multiple expensive registrations are eventually processed concurrently, the backend may need:

* concurrency limits
* backpressure
* scheduling

The architecture should introduce such mechanisms only when actual workload requires them.

Scientific modularity alone does not justify distributed execution.

---

### Timeouts

Transport-layer timeouts should not be confused with scientific failure.

If legitimate scientific work exceeds reasonable request duration, a job-oriented application model may be more appropriate than arbitrary process termination.

---

## 41. Security and Trust Boundaries

Externally reachable backend inputs should be treated as untrusted.

Potential trust-boundary inputs include:

* uploaded scientific files
* filenames
* paths
* archives where supported
* metadata
* configuration values
* learned checkpoints
* external references

Security controls should remain proportional to the actual deployment/exposure model.

---

### Path Safety

User-controlled path values must not be allowed to escape approved input/storage boundaries.

Scientific applications are not exempt from ordinary filesystem-security requirements.

---

### Archives

If archive support ever exists, processing should account for risks such as:

* path traversal
* excessive extraction
* decompression abuse

This does not imply archive uploads are currently supported.

---

### Command Execution

Avoid architectures that interpolate untrusted client values directly into shell commands.

Prefer validated application/library interfaces where practical.

---

### Authentication / Authorization

This document does not assume:

* user accounts
* API keys
* OAuth
* JWT
* RBAC

Authentication requirements depend on actual deployment and product requirements.

---

## 42. Logging and Observability

Backend logging may capture operational context such as:

* request/run context
* processing stage
* selected workflow
* application status
* scientific status
* execution duration
* failure category

Do not log unnecessarily:

* entire images
* large numerical arrays
* secrets
* sensitive environment values

No logging framework is prescribed.

---

### Logs Are Not Scientific Results

```text
Logs
→ operational diagnostics
```

```text
Structured Core Result
→ authoritative scientific outcome
```

Do not reconstruct benchmark truth from log text when authoritative structured result data exists.

---

### Observability

Where operational monitoring becomes relevant, useful conceptual signals may include:

* request duration
* job duration
* resource failures
* scientific rejection count
* unexpected internal failures

No monitoring/tracing system is assumed.

---

## 43. Provenance and Reproducibility

The backend must not strip scientific provenance from Core Engine results.

Conceptually:

```text
Source / Reference
       +
Configuration
       +
Method / Benchmark Profile
       +
Model / Software Context where tracked
       ↓
Core Engine
       ↓
Scientific Result
```

The backend should preserve relevant identity through translation and persistence.

---

### Backend Invocation Should Not Change Science

An equivalent configuration should not become scientifically different merely because it was invoked through:

```text
HTTP / Application Backend
```

instead of:

```text
CLI / Benchmark Runner / Direct Core Invocation
```

Transport should not redefine methodology.

---

### Random Seeds

Where a scientific configuration depends on randomness, the backend/core boundary should allow reproducibility information to be preserved according to project methodology.

The backend should not expose arbitrary scientific seed controls publicly unless that is an intentionally supported capability.

---

## 44. Backend and Benchmarking

Backend serving and benchmarking serve different purposes.

### Benchmark Runner

Owns:

* controlled pair population
* repeated execution
* scientific comparison
* aggregation
* benchmark reporting

### Backend

Owns:

* application request handling
* application orchestration
* result delivery
* optional persistence/artifact access

Both should reuse the same Core Engine.

---

### Benchmark Results Must Not Depend on UI State

Canonical benchmark results should come from reproducible scientific execution.

A frontend/backend demonstration should not become the only mechanism for producing benchmark evidence.

---

### Backend-Specific Scientific Defaults

Avoid situations where:

```text
CLI V1
≠
Benchmark V1
≠
Backend V1
```

because each path secretly uses different scientific settings.

Canonical methodology should remain consistent unless intentionally labelled otherwise.

---

## 45. Backend and Frontend

The conceptual relationship is:

```text
Frontend
    ↓
Backend Contract
    ↓
Application Service
    ↓
Core Engine
    ↓
Scientific Result
    ↓
Backend Result Translation
    ↓
Frontend Visualization
```

The frontend should not become the owner of scientific state.

---

### UI-Controlled Scientific Internals

Do not expose every internal scientific threshold to a frontend merely because the parameter exists internally.

User-facing scientific options should be intentional and validated.

---

### No Frontend Recalculation

The frontend should not independently recompute authoritative:

* RMSE
* inlier ratio
* verified inliers
* transformation validity
* acceptance status

It should display scientific results supplied through the application contract.

---

## 46. Backend and CLI

Backend and CLI workflows should reuse the same:

* Core Engine
* scientific configuration
* metric definitions
* status semantics

Avoid separate:

```text
CLI Registration Logic
```

and:

```text
Backend Registration Logic
```

implementations.

Differences should be in interface/orchestration, not the science itself.

---

## 47. Backend and Research Notebooks

Research notebooks should be able to call reusable scientific components directly.

They should not be forced through a backend unless:

* the experiment specifically studies application integration
* remote execution is an intentional requirement

The backend is an application boundary, not a mandatory scientific dependency.

---

## 48. Backend and Map / Mosaic Applications

Map and mosaic capabilities should consume scientifically accepted registration results.

Conceptually:

```text
Accepted Scientific Results
        ↓
Application / Aggregation Layer
        ↓
Map / Mosaic / Visualization
```

Map/mosaic generation must not redefine whether the underlying registration is scientifically valid.

---

## 49. Backend and Scientific Data Access

Mission-data discovery/download concerns should remain separable from registration algorithms where practical.

Avoid monolithic application functions performing:

```text
Download Data
→ Decode Data
→ Preprocess
→ Match
→ RANSAC
→ Evaluate
→ Render
→ Return
```

without responsibility boundaries.

Data access and scientific registration are related but distinct concerns.

---

## 50. Application Contract Stability

External application contracts should generally evolve more deliberately than internal algorithm implementations.

Replacing:

* a matcher
* a feature extractor
* an internal numerical library

should not automatically require clients to change how they interpret:

* scientific status
* transform direction
* coordinate units
* metric meaning

Stable scientific semantics reduce unnecessary coupling.

---

## 51. Backend State Ownership

A useful conceptual state boundary is:

| Layer                     | State Responsibility                   |
| ------------------------- | -------------------------------------- |
| Scientific Core           | Temporary scientific computation state |
| Backend/Application       | Request and application lifecycle      |
| Job Layer, if any         | Long-running execution lifecycle       |
| Persistence Layer, if any | Stored result/application metadata     |
| Artifact Layer            | Generated output files                 |
| Frontend                  | Presentation/view state                |

Backend layers should not modify finalized scientific values merely because they own application state.

---

## 52. Scientific Result Immutability Principle

Once the Core Engine finalizes an authoritative scientific result, outer layers should treat its scientific values as effectively read-only.

The backend may add application metadata such as:

* storage reference
* delivery metadata
* presentation information

but should not rewrite:

* inlier masks
* transforms
* RMSE
* coverage
* scientific accept/reject status

for presentation convenience.

No language-specific immutability mechanism is prescribed.

---

## 53. Backend Testing Architecture

Backend quality requires testing different from scientific benchmarking.

### Unit Tests

May verify:

* boundary validation
* application-service behavior
* configuration resolution
* result translation
* error mapping

### Contract Tests

May verify compatibility between application request/response contracts.

### Core Integration Tests

Should verify that the backend/application layer can invoke the real Core Engine using controlled inputs.

### File-Boundary Tests

Where external files are accepted, verify malformed or unsupported input behavior.

### Failure-Mapping Tests

Verify distinction between:

* scientific rejection
* invalid input
* dependency failure
* software error

### Security-Boundary Tests

Where applicable, verify:

* path handling
* input constraints
* malformed external data handling

### End-to-End Tests

May verify the complete application boundary through Core Engine invocation and final result translation.

No testing framework is assumed by this document.

---

### Do Not Mock the Core Everywhere

Mocks can be useful for isolated application-service tests.

But integration testing should also verify:

```text
Backend/Application Boundary
          ↓
Real Core Engine
          ↓
Structured Scientific Result
```

on controlled fixture or synthetic data.

---

### Backend Tests Are Not Scientific Benchmarks

Backend tests answer:

> Does the application layer behave correctly?

Scientific benchmarks answer:

> How well does the registration methodology perform?

Neither replaces the other.

---

## 54. Scalability Principles

Potential future scalability pressures include:

* large lunar imagery
* high memory consumption
* learned-model loading
* retrieval index size
* multiple candidate registrations
* concurrent application requests
* artifact volume
* repeated preprocessing

These are possible pressure points.

No current measured ChandraMap scale limit is asserted here.

---

### Scale-Out Is Not Automatically Required

A professional backend does not automatically require:

* microservices
* Kubernetes
* distributed workers
* autoscaling
* Redis
* message queues

A modular single application can be entirely appropriate for a research system.

---

### Scientific Components Are Not Microservices

Do not create network services such as:

```text
SIFT Service
RANSAC Service
RMSE Service
```

merely because the scientific core contains separate logical responsibilities.

Code-level modularity does not imply service-level distribution.

---

## 55. When Additional Backend Infrastructure Is Justified

### Async / Job Execution

Consider when evidence shows:

* registrations exceed practical direct-request duration
* clients need status retrieval
* work must survive disconnects
* expensive accelerator resources need coordination
* concurrency needs scheduling

---

### Database

Consider when application requirements need:

* persistent run history
* result lookup
* job state
* artifact metadata
* multi-user/project state

A database is not required merely because ChandraMap has a backend.

---

### Distributed Workers

Consider only when measured workload or concurrency requirements justify them.

Scientific modularity alone is insufficient justification.

---

### Authentication

Introduce when deployment/exposure requires access control.

Do not add an elaborate identity system to a local research backend without a real requirement.

---

## 56. Backend Architectural Invariants

The following principles should remain true unless the backend architecture is deliberately revised.

1. The backend depends on the Core Engine.

2. The Core Engine does not depend on the backend.

3. Transport/controllers remain scientifically thin.

4. Scientific algorithms remain in reusable core components.

5. The backend does not implement a duplicate registration pipeline.

6. The backend does not define independent metric formulas.

7. Source and reference roles remain explicit.

8. Source→reference transform direction remains explicit.

9. Coordinate semantics survive request/result translation.

10. Metric units survive request/result translation.

11. Missing scientific metadata is not fabricated.

12. Boundary validation and scientific validation remain distinct.

13. Passing request validation does not imply scientific validity.

14. Scientific rejection is not an internal-server failure.

15. Application/job completion and registration acceptance remain separate statuses.

16. Benchmark V1–V4 are not API versions.

17. Canonical benchmark methodology is not silently changed by backend defaults.

18. The Core Engine result remains authoritative.

19. Result translation preserves scientific meaning.

20. Presentation layers do not mutate finalized scientific results.

21. Artifact files do not become authoritative scientific truth.

22. External uploaded files are treated as untrusted input.

23. User-controlled filenames and paths are not automatically trusted.

24. Scientific file handling respects resource/security boundaries.

25. No identity transform converts scientific failure into fake success.

26. Scientific fallbacks remain explicit.

27. Actual executed methodology remains traceable.

28. Matcher scores are not converted into fake registration confidence.

29. The transport layer does not independently calculate RMSE or coverage.

30. Lunar coordinates do not silently use Earth CRS defaults.

31. Longitude-convention changes remain explicit.

32. Learned checkpoint identity remains traceable where scientifically relevant.

33. GPU/accelerator infrastructure does not become mandatory for canonical V1 without a scientific requirement.

34. Caches remain rebuildable and non-authoritative.

35. Persistence technology remains an outer-layer concern.

36. Databases do not own scientific algorithms.

37. Scientific components do not depend on upload-directory layouts.

38. Job/queue architecture is introduced only when workload justifies it.

39. Deterministic scientific rejection is not blindly retried.

40. Security controls remain proportional to deployment exposure.

41. Backend tests and scientific benchmarks remain separate evidence.

42. The same scientific Core Engine can serve backend, CLI, benchmark, and research consumers.

43. Current and target backend architecture remain clearly distinguished.

44. Backend complexity grows from demonstrated application need rather than architectural appearance.

---

## 57. Backend Anti-Patterns

### Fat Controllers

Routes directly perform:

* feature extraction
* matching
* RANSAC
* warping
* metrics

This couples scientific behavior to transport code.

---

### Duplicate Scientific Pipeline

A backend-specific registration implementation exists separately from the benchmark/Core Engine implementation.

This creates:

* inconsistent fixes
* inconsistent metrics
* weaker reproducibility
* confusing scientific behavior

---

### API Version = Benchmark Version

Treating an API version as though it were Benchmark V1/V2/V3/V4 mixes unrelated versioning systems.

---

### UI-Controlled Science

A frontend can arbitrarily tune internal thresholds without explicit methodological control.

---

### Silent Scientific Fallback

A requested method fails and the backend quietly runs another method without reporting the change.

---

### Scientific Rejection as Server Crash

Expected failure to establish correspondence is treated as an unexpected application failure.

---

### Identity Transform on Failure

Failed registration becomes:

```text
identity transform
+
accepted
```

---

### Anonymous Metrics

Responses contain:

```text
rmse = 0.8
```

with no unit, coordinate domain, or metric semantics.

---

### Anonymous Transform Matrix

A matrix is returned without:

* model family
* direction
* source/reference domain

---

### Storage-Coupled Science

Scientific algorithms require backend database models or upload-directory objects.

---

### Database as Scientific Logic

Database state becomes the authoritative source of:

* registration correctness
* metric definitions
* geometric verification

rather than storing scientific results produced elsewhere.

---

### Path-Coupled Science

Core scientific functions require server-specific paths instead of meaningful scientific inputs.

---

### Job System Before Need

Queues/workers are introduced solely to make the architecture appear production-like.

---

### Infrastructure Inflation

Cloud/distributed infrastructure is added before the project has corresponding scale or operational requirements.

---

### Unbounded External Inputs

Externally exposed applications accept scientific data with no consideration for:

* resource consumption
* malformed content
* storage impact

---

### Hidden Backend Defaults

Backend invocation uses scientific settings different from canonical CLI/benchmark execution without explicit labeling.

---

### Frontend Recalculation

A browser recomputes scientific metrics instead of displaying authoritative Core Engine values.

---

### Giant JSON Artifacts

Large binary scientific rasters are embedded into every normal response when references/appropriate transfer mechanisms would be more suitable.

---

### Database as Large Scientific Array Dump

Large scientific arrays or rasters are placed into a database without a real application requirement.

---

## 58. Backend Evolution

Backend complexity should grow in response to real application needs.

A conceptual progression might be:

### Simple Application Boundary

```text
Client
  ↓
Thin Application Layer
  ↓
Core Engine
  ↓
Result
```

### Stronger Boundary Handling

Add where needed:

* stronger file validation
* artifact lifecycle
* better error translation
* resource controls

### Long-Running Execution

Introduce a job abstraction only when execution behavior justifies it.

### Persistent Application State

Introduce durable persistence only when users/workflows require:

* run history
* result lookup
* job recovery
* application entities

### Scaling Infrastructure

Add concurrency/distribution only after measured workload requires it.

This is architectural guidance, not a committed roadmap.

---

## 59. Backend Success Criteria

A strong ChandraMap backend should make it possible for an application consumer to:

* submit valid scientific inputs through a controlled boundary
* preserve source/reference identity
* preserve relevant metadata
* select an intentionally supported scientific configuration
* invoke the shared Core Engine
* receive the authoritative scientific result
* distinguish accepted, rejected, invalid, unsupported, and operational failure states
* preserve transform and coordinate meaning
* preserve metric units
* access artifacts where supported
* retain relevant provenance
* reproduce important scientific execution
* do all of this without duplicating scientific logic inside application code

Backend success should be judged by:

* correctness of orchestration
* fidelity to scientific results
* security of application boundaries
* maintainability
* reproducibility
* clear dependency direction

not by the number of backend technologies used.

---

## 60. Relationship to Other Documents

### [`system-overview.md`](./system-overview.md)

Shows the backend as one outer layer within the complete ChandraMap system architecture.

This document focuses specifically on backend/application responsibilities.

---

### [`core-engine-architecture.md`](./core-engine-architecture.md)

Defines the reusable scientific engine invoked by the backend.

The backend should depend on that scientific architecture rather than reimplementing it.

---

### [`v1-pipeline.md`](./v1-pipeline.md)

Defines canonical Benchmark V1 scientific processing.

The backend may invoke V1, but must not redefine its scientific stages.

---

### [`../project/v1-scope.md`](../project/v1-scope.md)

Defines which scientific capabilities belong to canonical Benchmark V1.

Backend configuration resolution must respect that boundary.

---

### [`../project/terminology.md`](../project/terminology.md)

Defines canonical terms including:

* source
* reference
* candidate match
* verified inlier
* transform
* rejection
* Benchmark V1

Backend contracts should preserve those meanings.

---

### [`../project/assumptions.md`](../project/assumptions.md)

Defines scientific assumptions that remain relevant when backend applications invoke the Core Engine.

---

### [`../project/limitations.md`](../project/limitations.md)

Defines scientific and methodological limitations that backend result translation must not hide.

---

### [`.ai/architecture/SYSTEM_OVERVIEW.md`](../../.ai/architecture/SYSTEM_OVERVIEW.md)

Provides deeper system-level architectural context for maintainers and AI-assisted engineering.

---

### [`.ai/architecture/PIPELINE.md`](../../.ai/architecture/PIPELINE.md)

Defines the broader scientific processing flow that backend application code must not duplicate.

---

### [`.ai/architecture/MODULE_MAP.md`](../../.ai/architecture/MODULE_MAP.md)

Owns mapping between logical responsibilities and actual repository modules.

Exact backend source paths should be documented there when verified.

---

### [`.ai/architecture/DATA_FLOW.md`](../../.ai/architecture/DATA_FLOW.md)

Defines scientific information, coordinate, provenance, transform, and result flow across system boundaries.

---

### [`.ai/development/TESTING_RULES.md`](../../.ai/development/TESTING_RULES.md)

Defines software/scientific validation principles relevant to backend integration testing.

---

### [`.ai/development/BENCHMARK_RULES.md`](../../.ai/development/BENCHMARK_RULES.md)

Defines benchmark methodology that backend invocation must not silently modify when executing canonical benchmark configurations.

---

## 61. Key Backend Rules

1. Backend is an application/orchestration layer around the scientific Core Engine.

2. Backend depends on Core Engine; Core Engine never depends on backend transport.

3. Routes/controllers remain thin.

4. Backend does not implement SIFT, learned matching, RANSAC, transforms, RMSE, coverage, or scientific acceptance logic independently.

5. Request validation and scientific validation remain separate.

6. Source and reference roles remain explicit across all boundaries.

7. Scientific metadata is preserved rather than fabricated.

8. External files are treated as untrusted inputs.

9. File extensions alone do not prove file validity.

10. User-provided filesystem paths are not automatically trusted.

11. Application services coordinate use cases rather than scientific algorithms.

12. Backend and benchmarks invoke the same reusable Core Engine.

13. The backend does not create a second registration pipeline.

14. Configuration resolution must not introduce hidden scientific defaults.

15. Benchmark V1–V4 remain scientific configurations, not API versions.

16. Scientific rejection is not a backend crash.

17. Completed application execution does not automatically mean accepted registration.

18. Core Engine results remain authoritative.

19. Backend translation preserves transform model and direction.

20. Coordinate conventions survive serialization.

21. Scientific metric units survive serialization.

22. Pixel-to-metre conversion is never silently fabricated.

23. Lunar coordinates never silently become Earth WGS84.

24. Longitude conversion remains explicit.

25. Artifacts remain distinct from scientific results.

26. Artifact storage technology is not part of scientific methodology.

27. A database is optional and application-driven.

28. Caches remain rebuildable and non-authoritative.

29. Async jobs are introduced only when workload requires them.

30. No queue or worker technology is assumed by this architecture.

31. Scientific status and job status remain separate.

32. Deterministic scientific rejection is not blindly retried.

33. FAISS remains a vector-retrieval capability, not a local registration engine.

34. Learned checkpoints remain explicit external dependencies where used.

35. GPU execution does not become a hidden canonical V1 requirement.

36. Backend resource controls remain proportional to real workload.

37. Authentication is added only when actual deployment needs it.

38. Logs do not replace structured scientific results.

39. Provenance survives backend translation.

40. Equivalent Core Engine configuration should have the same scientific meaning whether invoked by backend, CLI, benchmark runner, or research code.

41. Frontend presentation does not redefine scientific truth.

42. CLI and backend do not maintain separate scientific implementations.

43. Backend tests do not replace scientific benchmarks.

44. Scientific benchmarks do not replace backend integration tests.

45. Backend scale/distribution complexity grows only from demonstrated need.

46. Current backend implementation and target architecture must never be confused.

<!-- Source specification: :contentReference[oaicite:0]{index=0} -->
