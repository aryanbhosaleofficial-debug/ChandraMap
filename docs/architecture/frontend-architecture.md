# ChandraMap Frontend Architecture

The ChandraMap frontend is the **human-facing scientific visualization and interaction layer** around the backend/application boundary and the reusable ChandraMap scientific core.

Its primary responsibility is to help users:

- provide or select scientific inputs
- choose supported workflows
- understand processing state
- inspect correspondence evidence
- inspect registration results
- interpret scientific metrics
- understand accepted and rejected registrations
- explore generated artifacts
- inspect metadata and provenance
- use downstream map or mosaic views where those capabilities exist

The fundamental architectural principle is:

> **The frontend consumes scientific truth; it does not create scientific truth.**

Conceptually:

```text
ChandraMap Scientific Core
            ↓
Authoritative Scientific Result
            ↓
Backend / Application Contract
            ↓
Frontend Data Layer
            ↓
View Models / Presentation State
            ↓
Scientific Visualization
```

Never:

```text
Frontend
   ↓
Recalculate Scientific Result
   ↓
Declare Registration Correct
```

This document describes the **logical frontend architecture ChandraMap should preserve as the project evolves**.

It does not claim that every visualization, page, viewer, interaction, or frontend capability described here is currently implemented.

---

## 1. Purpose

This document defines:

- the role of the ChandraMap frontend
- the boundary between frontend, backend, and scientific core
- frontend responsibility for input and workflow interaction
- scientific-result consumption
- candidate/inlier/outlier visualization
- registered-result visualization
- metric presentation
- coordinate and geospatial presentation
- result/rejection/error states
- artifact viewing
- benchmark-result presentation
- downstream map/mosaic boundaries
- client-side state ownership
- performance considerations for large scientific imagery
- accessibility expectations
- frontend security boundaries
- reproducibility considerations
- frontend testing expectations
- architectural invariants and anti-patterns

This document intentionally does **not** define:

- scientific matching algorithms
- RANSAC
- transform estimation
- RMSE mathematics
- spatial-coverage formulas
- backend endpoints
- request/response schemas
- application routes
- page names
- concrete component filenames
- frontend framework
- state-management library
- charting library
- map library
- build tooling
- deployment configuration

Those details belong in their respective implementation, API, design, or deployment documentation.

---

## 2. Architecture Status

No specific frontend framework, state-management library, image-viewer library, charting system, map library, routing system, or build system is established by this document.

Therefore this file primarily describes a:

> **target logical frontend architecture**

unless individual capabilities are independently verified from repository implementation.

A documented capability must not automatically be described as:

- Implemented
- Tested
- Supported
- Deployed
- Complete

Current frontend status should be determined from:

- actual application source
- package/configuration files
- tests
- backend contracts
- working application behavior

Target architecture must not be presented as current implementation.

---

## 3. Frontend Responsibilities

The frontend may own responsibilities such as:

- user interaction
- input selection
- supported workflow selection
- request construction
- request submission
- application/request status presentation
- scientific-result presentation
- image viewing
- correspondence visualization
- registration visualization
- metric formatting
- metadata/provenance presentation
- artifact viewing
- benchmark-result visualization
- client-side view state
- accessibility
- responsive layout
- presentation-specific performance optimization

The frontend should remain:

- scientifically faithful
- framework-independent at the architectural level
- loosely coupled to individual matcher implementations
- dependent on stable application contracts rather than internal scientific libraries

---

## 4. What the Frontend Must Not Own

The frontend must not become the authoritative implementation of:

- scientific preprocessing
- feature extraction
- SIFT
- RootSIFT
- ALIKED
- LightGlue
- LoFTR
- candidate-match generation
- RANSAC
- verified-inlier classification
- affine estimation
- homography estimation
- sub-pixel refinement
- registration warping as scientific truth
- RMSE
- spatial coverage
- scientific quality gates
- scientific accept/reject decisions
- calibrated registration confidence

The rule is:

> **Frontend presents science; backend/core execute science.**

---

## 5. Responsibility Boundary

| Responsibility                 |             Frontend |                           Backend / Core |
| ------------------------------ | -------------------: | ---------------------------------------: |
| Input selection UI             |                  Yes |                                 Validate |
| Request construction           |                  Yes |                        Receive/interpret |
| Frontend form validation       |                  Yes |                               Revalidate |
| Source/reference display       |                  Yes |                       Preserve semantics |
| Scientific preprocessing       |                   No |                                     Core |
| Feature extraction             |                   No |                                     Core |
| Local matching                 |                   No |                                     Core |
| RANSAC                         |                   No |                                     Core |
| Verified-inlier classification |         Display only |                                     Core |
| Transform estimation           |                   No |                                     Core |
| RMSE / coverage                |         Display only |                                     Core |
| Scientific accept/reject       |   Display faithfully |                                     Core |
| Application/job status         |              Display |                                  Backend |
| Candidate/inlier visualization |                  Yes |                 Classification from core |
| Registered preview             |                  Yes |      Scientific output from core/backend |
| Map interaction                | Yes, where supported | Geospatial truth from authoritative data |
| Artifact viewing               |                  Yes |    Backend/application exposes artifacts |
| View preferences               |                  Yes |                                       No |
| Scientific provenance          |              Present |                    Backend/core preserve |

---

## 6. Architecture at a Glance

```text
┌──────────────────────────────────────────────────────────────┐
│                     ChandraMap UI                            │
│                                                              │
│  Input / Dataset Selection                                  │
│  Workflow Selection                                         │
│  Processing Status                                          │
│  Result Inspection                                          │
│  Scientific Visualization                                   │
│  Map / Mosaic Views                  [downstream if present] │
└──────────────────────────────┬───────────────────────────────┘
                               │
                               ▼
┌──────────────────────────────────────────────────────────────┐
│              Frontend Application Layer                     │
│                                                              │
│  Request State                                              │
│  Result State                                               │
│  View Models                                                │
│  Artifact State                                             │
│  UI Preferences                                             │
│  Backend Data Access                                        │
└──────────────────────────────┬───────────────────────────────┘
                               │
                               ▼
┌──────────────────────────────────────────────────────────────┐
│                  Backend / Application                      │
└──────────────────────────────┬───────────────────────────────┘
                               │
                               ▼
┌──────────────────────────────────────────────────────────────┐
│             ChandraMap Scientific Core                      │
│                                                              │
│ Correspondence → Geometry → Registration → Evaluation      │
└──────────────────────────────────────────────────────────────┘
```

Scientific results move upward.

Scientific algorithms do not move into the frontend.

---

## 7. Frontend, Backend, and Core Boundaries

### Scientific Core

Owns:

- scientific representations
- preprocessing
- correspondence
- geometric verification
- transforms
- registration
- evaluation
- scientific acceptance/rejection

### Backend / Application Layer

Owns:

- request/application boundary
- application orchestration
- input resolution
- configuration resolution
- Core Engine invocation
- job lifecycle where applicable
- artifact access
- result translation

### Frontend

Owns:

- interaction
- presentation
- view state
- result inspection
- scientific visualization
- supported workflow submission

The intended dependency direction is:

```text
Frontend
    ↓
Backend / Application Contract
    ↓
Core Engine
    ↓
Scientific Components
```

The scientific core must not depend on frontend state or UI framework behavior.

---

## 8. Frontend Application Structure

A framework-neutral frontend can be understood through several logical responsibilities.

### Presentation Layer

Responsible for:

- layout
- labels
- tables
- controls
- status indicators
- responsive presentation
- accessibility

It should not fetch scientific data directly from arbitrary locations or implement scientific calculations.

---

### Feature / Workflow Layer

Coordinates user-facing capabilities such as:

- registration input
- processing/result inspection
- correspondence inspection
- benchmark comparison
- downstream map/mosaic exploration where available

These are conceptual capabilities, not claims about current pages/routes.

---

### Application State Layer

Owns client-side state such as:

- request state
- remote result state
- view state
- selection state
- UI preferences

It must keep scientific-result data separate from mutable presentation state.

---

### API / Data Access Layer

Responsible for backend communication.

Conceptually:

```text
UI Feature
    ↓
Frontend Data Access
    ↓
Backend Contract
```

Transport assumptions should not be duplicated throughout every visualization component.

---

### Scientific Visualization Layer

Displays authoritative scientific information such as:

- images
- candidate correspondences
- verified inliers
- outliers
- transforms
- metrics
- residuals
- registered previews
- artifacts

It must not recompute scientific truth.

---

### Shared UI Layer

May contain reusable, non-scientific presentation primitives.

Scientific meaning should not be hidden inside generic UI components.

---

## 9. Input Workflow

A user-facing registration workflow may conceptually allow the user to provide or select:

- source observation
- reference observation
- supported workflow/configuration
- optional scientifically meaningful context where allowed

Exact form fields depend on actual application contracts.

---

### Source

The observation being registered.

The UI should consistently label it:

> **Source**

---

### Reference

The observation or known region defining the target registration frame.

The UI should consistently label it:

> **Reference**

Avoid using only:

- Image A
- Image B

when source/reference direction matters scientifically.

---

### Visual Distinction

The interface should make source/reference roles difficult to confuse.

Useful approaches may include:

- persistent labels
- dedicated panels
- product identity
- sensor metadata
- clear headings

Do not rely only on color.

---

## 10. Frontend Validation

Frontend validation improves user experience.

It may identify obvious problems such as:

- missing required selection
- incomplete form state
- malformed user-entered values
- incompatible visible option combinations

However:

> **Frontend validation is never the authoritative security or scientific validation boundary.**

The backend and scientific core must revalidate input according to their responsibilities.

---

### Never Trust Client Validation

The backend must never assume:

> the frontend already checked it.

Client-side validation can be bypassed or become stale.

---

## 11. Workflow and Benchmark Selection

If the application exposes Benchmark V1–V4, they must be presented as:

> **Benchmark / Research Configurations**

not software releases.

Conceptually:

```text
Benchmark V1 — Classical Baseline
Benchmark V2 — Sensor / Scale Research
Benchmark V3 — Advanced Matching / Retrieval Research
Benchmark V4 — Advanced Robustness Research
```

Exact labels should remain consistent with authoritative benchmark documentation.

---

### Benchmark Version Is Not API Version

Never confuse:

```text
Benchmark V1
```

with:

```text
API v1
```

or:

```text
Application Version 1
```

These are separate concepts.

---

### Later Does Not Mean Better

The UI should not automatically label:

```text
V4 = Best
```

A later benchmark version may:

- improve some metrics
- worsen others
- require more compute
- perform differently across stress categories

Measured evidence determines interpretation.

---

## 12. V1 Frontend Semantics

When canonical Benchmark V1 is shown, the UI should preserve its actual scientific scope:

- known-overlap
- classical baseline
- local registration
- SIFT-based classical correspondence
- geometric verification
- affine/homography baseline geometry
- scientific evaluation
- accept/reject

The interface should not imply that V1 includes:

- whole-Moon retrieval
- FAISS
- LightGlue
- LoFTR
- native full-cube IIRS matching
- DEM-aware registration
- advanced local geometry

unless the authoritative V1 scope changes.

---

## 13. Scientific Configuration UI

The frontend should not expose every internal scientific parameter merely because it exists.

Internal parameters may include things such as:

- feature thresholds
- matching thresholds
- RANSAC tolerances
- acceptance gates

Exposing arbitrary controls can:

- reduce reproducibility
- encourage visual tuning
- undermine benchmark comparability

Supported configuration should come from intentionally designed application workflows.

---

## 14. Request and Processing State

Frontend application state is different from scientific result state.

Conceptual request/application states may include:

- idle
- preparing
- submitting
- processing
- loading result
- completed
- request failure

Actual vocabulary should follow implemented backend contracts.

This document does not define a required job-state enum.

---

### Loading State

Scientific processing may take noticeable time.

The interface should communicate that work is ongoing rather than appearing frozen.

---

### No Fake Progress

Do not display:

```text
73% complete
```

unless progress has a real authoritative basis.

If only execution state is known, prefer honest states such as:

```text
Processing…
```

If authoritative stage progress is available, stage-based presentation may be used.

---

## 15. Scientific Result State

Scientific state is separate from request state.

Depending on the authoritative scientific result contract, a completed attempt may conceptually be:

- Accepted
- Rejected
- Invalid
- Unsupported

Unexpected software failure is another category.

Critical example:

```text
Application Processing:
Completed

Scientific Registration:
Rejected

Reason:
Insufficient geometric support
```

This is not contradictory.

The application worked correctly and the scientific method abstained.

---

## 16. Result Overview

A scientific-result view should prioritize information approximately in this order:

1. scientific registration status
2. source/reference identity
3. method or benchmark configuration
4. primary quality evidence
5. correspondence and geometry diagnostics
6. registered visualization
7. secondary runtime or debugging information
8. provenance/configuration details

The interface should not put a visually attractive overlay above the actual scientific status.

---

## 17. Scientific Result Data Flow

```text
Scientific Core
      ↓
Structured Scientific Result
      ↓
Backend Contract
      ↓
Frontend Data Layer
      ↓
View Model
      ↓
┌──────────────────────────────────────┐
│ Scientific Status                    │
│ Metrics                              │
│ Candidate / Inlier Visualization     │
│ Registration Preview                 │
│ Metadata / Provenance                │
│ Artifacts                            │
└──────────────────────────────────────┘
```

The transformation:

```text
Structured Result
→ View Model
```

may change formatting.

It must not change scientific meaning.

---

## 18. Image Viewer Architecture

Lunar imagery may require richer inspection than an ordinary static image element.

Potential viewer concerns include:

- zoom
- pan
- fit-to-view
- large-image rendering
- image-space coordinate inspection
- overlays
- synchronized comparison
- correspondence inspection

These are architectural concerns, not claims that every viewer capability currently exists.

---

### Source / Reference Viewing

A scientific workspace may present source and reference imagery separately.

If synchronized navigation is supported, that synchronization should not imply that the two observations:

- have equal GSD
- share the same projection
- have identical dimensions
- are already registered

The viewer synchronizes presentation, not scientific geometry.

---

## 19. Correspondence Visualization

The UI should reflect the actual scientific correspondence lifecycle.

```text
Candidate Matches
       ↓
Geometric Verification
       ↓
Verified Inliers
+
Rejected Outliers
```

Do not reduce this into one generic visual state called:

> Matches

when the scientific distinction matters.

---

### Candidate Matches

Candidate matches are unverified proposals produced before geometric verification.

The frontend should not label them:

- correct
- verified
- ground truth

unless an independent truth source explicitly provides that classification.

---

### Verified Inliers

Verified inliers are candidate correspondences judged geometrically consistent with the selected model.

They should be visually distinguishable from candidate matches.

They must not be presented as independent ground truth.

---

### Outliers

Rejected candidates can contain useful diagnostic information.

Where scientifically useful, the UI should allow them to remain visible or inspectable.

Do not hide outliers solely to make a visualization look cleaner.

---

### Accessible Distinction

Candidate/inlier/outlier states should not depend only on color.

Where practical, combine:

- labels
- line styles
- marker styles
- legends
- text descriptions

with color.

---

## 20. Registration Visualization

A result interface may present scientific outputs such as:

- registered source preview
- reference image
- source/reference overlay
- before/after comparison
- difference visualization where defined

These are **diagnostic and explanatory views**.

They are not independent scientific accuracy evidence.

---

### Pretty Overlay Is Not Proof

Never communicate:

```text
Looks Aligned
=
Scientifically Correct
```

A visually convincing result may still have:

- systematic residual error
- poor support
- weak coverage
- incorrect correspondence
- insufficient independent truth

Scientific status and metrics should remain visible alongside registration imagery.

---

### Client-Side Blending

Simple visual blending may occur client-side when appropriate.

However, it must remain presentation-only.

The frontend must not create a scientifically different transform or warp merely to improve appearance.

---

## 21. Metric and Scientific Quality Presentation

The frontend should display only metrics supplied through authoritative scientific results.

Potential metric families include:

- candidate-match count
- verified-inlier count
- inlier ratio
- spatial coverage
- fit residual
- RMSE
- independent check-point RMSE
- retrieval Recall@K
- runtime
- scientific status

Only metrics actually available for the result should be shown.

---

### Do Not Recalculate Metrics

Avoid:

```text
Backend RMSE
     ↓
Frontend Recomputes RMSE
     ↓
Different Value
```

The browser should not implement a separate scientific metric definition.

---

## 22. Metric Units

Units are part of the scientific value.

Prefer:

```text
RMSE: 0.82 source-image px
```

over:

```text
RMSE: 0.82
```

The frontend must preserve units provided by the authoritative result.

---

### Source vs Reference Pixels

Do not collapse:

```text
source-image px
```

and:

```text
reference-image px
```

into generic:

```text
pixels
```

when their physical meaning differs.

---

### Ground Error

Display metre-level error only when the authoritative scientific result provides a scientifically valid ground-unit metric.

Do not compute:

```text
pixel error
×
approximate sensor resolution
```

inside the browser to invent ground accuracy.

---

### Sub-Pixel

`Sub-pixel` means:

> less than one pixel in the specified coordinate system.

Do not automatically present it as:

> sub-metre.

---

## 23. Fit Residual vs Independent Accuracy

If both are available, distinguish them clearly.

### Fit Residual

Measured on points used to estimate/finalize the transform.

### Independent Check-Point Error

Measured using independent held-out evaluation points.

Avoid grouping both under a vague label such as:

> Accuracy

without explanation.

---

## 24. Spatial Coverage

Where coverage is available, the frontend should display the authoritative metric/result.

It may visualize concepts such as:

- point distribution
- grid occupancy
- convex-hull region

only when those quantities have been scientifically defined and provided.

Do not invent a browser-only coverage metric.

---

## 25. Matcher Confidence

If matcher-specific scores are exposed, label them according to their actual meaning.

For example:

> matcher score

or:

> matcher confidence

Do not convert those values into:

```text
Registration Confidence: 96%
```

unless a separately validated calibration method defines that interpretation.

---

## 26. Combined Quality Scores

The frontend must not independently combine:

- RMSE
- inlier ratio
- coverage
- candidate count

into an undocumented overall score.

If an authoritative project-level quality score is ever introduced, the UI should display it according to its documented scientific definition.

---

## 27. Coordinate Presentation

Coordinate semantics are part of scientific meaning.

The frontend may encounter:

- image `(x, y)`
- array `(row, column)`
- source-image coordinates
- reference-image coordinates
- projected/geospatial coordinates

These must remain distinguishable.

---

### `(x, y)` vs `(row, column)`

Do not silently treat:

```text
(x, y)
```

and:

```text
(row, column)
```

as the same ordering.

If the backend contract defines `(x, y)`, frontend conversion must preserve that meaning.

---

### Coordinate Labels

Avoid ambiguous displays such as:

```text
X: 123
Y: 456
```

when the image/context is unclear.

Prefer context such as:

```text
Source image (x, y)
```

or:

```text
Reference image (x, y)
```

where necessary.

---

## 28. Image-Space vs Geospatial Coordinates

Image coordinates answer:

> Where is this point inside the raster?

Geospatial coordinates answer:

> Where does this point correspond on the Moon?

They are not interchangeable.

The frontend should not format one coordinate type as though it were the other.

---

## 29. Lunar CRS and Longitude

When geospatial information is available, preserve relevant context such as:

- lunar CRS
- projection
- longitude convention

Do not silently label lunar coordinates using Earth WGS84/EPSG:4326 defaults.

---

### Longitude Convention

Different products may use:

- positive east
- positive west
- `0–360°`
- `-180–180°`

If longitude is shown and convention matters, the active convention should remain clear.

Do not silently convert it merely for display convenience unless the conversion is explicitly defined and traceable.

---

## 30. Transform Presentation

If a transform is exposed to users or researchers, enough context should remain visible to interpret it.

Potential information includes:

- transform model
- source → reference direction
- coordinate domain
- parameter or matrix representation
- transform validity/status

Avoid presenting an anonymous numerical matrix as though its meaning is self-evident.

---

## 31. Metadata and Provenance

A metadata or result context view may expose scientifically useful information such as:

- mission
- instrument
- product identity
- GSD
- projection
- footprint
- derived representation
- benchmark/method configuration

only where those values are actually available.

---

### Unknown Metadata

Prefer explicit states such as:

- Unknown
- Unavailable
- Not provided

rather than inserting guessed values.

---

### Provenance

Where useful, the UI should help users understand the relationship:

```text
Source
+
Reference
+
Method / Benchmark
+
Scientific Result
```

Advanced research views may also expose configuration or model/checkpoint identity where the backend provides it.

---

## 32. Rejection and Failure UX

Rejected registrations should have a first-class result experience.

Do not make them disappear because no accepted registration exists.

A rejection view may explain structured reasons such as:

- insufficient usable features
- insufficient candidate matches
- no valid geometric model
- insufficient verified support
- poor spatial coverage
- scientific quality criteria not satisfied

Only use reasons actually supplied by the authoritative result.

---

### Do Not Invent Failure Causes

If the backend/core reports:

```text
insufficient geometric support
```

the frontend should not infer:

> Sun angle caused the failure.

unless that causal attribution was actually produced by the scientific system.

---

### Distinguish Application Failure

A scientific rejection is different from:

- network failure
- malformed request
- unsupported input
- unexpected backend error

Do not display all of them as:

> Something went wrong.

---

## 33. Empty States

Before a result exists, the interface should show meaningful empty states instead of:

- blank charts
- empty metric cards
- fake zeros
- placeholders that resemble scientific results

For example, an unavailable metric should not default visually to:

```text
RMSE: 0
```

because zero has scientific meaning.

---

## 34. Artifacts

The frontend may display or provide access to generated artifacts such as:

- match visualization
- inlier/outlier visualization
- registered preview
- overlay
- residual plot
- benchmark report

Only artifacts actually exposed by the backend/application result should be shown.

---

### Artifact Is Not Result

```text
Structured Scientific Result
→ authoritative scientific information

Artifact
→ visualization or derived file
```

A PNG overlay does not replace:

- transform information
- metrics
- status
- provenance

---

### Artifact Loading

Large artifacts should be loaded intentionally.

Potential architectural strategies include:

- preview-first loading
- lazy loading
- on-demand inspection

No framework-specific mechanism is prescribed.

---

## 35. Artifact-Based vs Data-Driven Visualization

Two visualization approaches may be useful.

### Artifact-Based

The backend provides an already rendered artifact.

Advantages:

- simpler client rendering
- reproducible static diagnostic output

### Data-Driven

The backend provides structured scientific data and the frontend renders an interactive overlay.

Advantages:

- zoom-aware interaction
- filtering
- inspection

Either approach can be valid.

The frontend must preserve scientific classification in both cases.

---

### Data-Driven Rule

If the frontend draws correspondence lines itself:

```text
candidate
inlier
outlier
```

classification must still come from the authoritative scientific result.

The browser must not independently run geometric verification to determine those classes.

---

## 36. Benchmark Visualization

Where benchmark results are presented, the UI may eventually show:

- per-pair status
- metric distributions
- stress-category summaries
- V1–V4 measurements
- success/rejection rates
- runtime information

Canonical aggregation must come from the authoritative benchmark/result layer.

The browser should not independently redefine benchmark statistics.

---

### Filtered Views

Frontend filtering may change:

> what the user is looking at.

It must not change:

> canonical benchmark truth.

If a visible subset differs from the full benchmark population, the UI should make that distinction understandable.

---

### No Client-Side Benchmark Gaming

Do not silently remove:

- failed pairs
- rejected registrations
- difficult cases

from aggregate presentation merely because the user applied a convenience filter.

---

## 37. Map Architecture

Map functionality is downstream of scientifically valid registration/geospatial information.

Conceptually:

```text
Accepted / Valid Registration
          ↓
Valid Lunar Geospatial Context
          ↓
Map Visualization
```

The map is not required for the registration core to be scientifically valid.

---

### Map Responsibilities

Where supported, a lunar map may visualize:

- scientific product footprints
- registered locations
- candidate regions
- accepted registration products
- contextual lunar layers

Only scientifically available coordinates should be displayed.

Do not invent coordinates to populate a map.

---

### Lunar Map Is Not an Earth Map

Earth-specific assumptions must not be applied automatically.

Map architecture may need to account for:

- lunar body model
- lunar projection
- longitude convention
- lunar tiling assumptions

No map library is prescribed by this document.

---

### 3D Globe

A lunar globe may be a useful downstream visualization.

It is not required for:

- correspondence
- geometric verification
- registration
- scientific evaluation

No current globe implementation is implied.

---

## 38. Mosaic Architecture

Mosaic visualization is downstream from accepted registration results.

Conceptually:

```text
Accepted Registrations
        ↓
Mosaic Composition
        ↓
Visualization
```

A visually seamless mosaic must not hide:

- rejected registrations
- weak geometric support
- local misregistration
- uncertain alignment

Mosaic appearance does not redefine scientific validity.

---

## 39. Client-Side State Architecture

Frontend state should be separated by responsibility.

### Remote / Server State

Backend-provided information such as:

- application/run state
- scientific result
- artifact metadata

### Form / Request State

User selections before submission.

### View State

Examples include:

- zoom
- pan
- selected visualization
- visible overlay
- expanded panel
- table filter

### Local Preferences

Optional display preferences that do not change scientific methodology.

---

### Authoritative vs Derived State

Authoritative:

```text
Backend Scientific Result
```

Derived presentation state may include:

- formatted text
- chart-ready values
- selected correspondence subset for inspection
- visible layers

Derived view state must not modify scientific values.

---

### Result Status Is Not View State

If the scientific result says:

```text
Rejected
```

a local UI action must never change it to:

```text
Accepted
```

The user may change the visualization, not scientific history.

---

## 40. Backend Data Access

The frontend should centralize backend communication sufficiently to keep transport details out of scientific visualization components.

Preferred conceptual direction:

```text
Feature / Page
      ↓
Application / View Model
      ↓
Data Access Layer
      ↓
Backend Contract
```

Avoid every visual component independently:

- constructing network requests
- interpreting raw transport errors
- understanding backend implementation details

---

## 41. Response Translation and View Models

Frontend view models may:

- format labels
- prepare presentation structures
- format units
- organize result sections
- map status to accessible presentation

They must not:

- recalculate RMSE
- reclassify inliers
- reverse transform direction
- invent missing metadata
- convert rejection to success
- alter scientific thresholds

---

## 42. Contract Stability

The frontend should depend on stable ChandraMap application/scientific semantics rather than internal matcher objects.

For example, changing the internal matcher from one method to another should not require rewriting every result view if both methods still produce:

- candidate correspondences
- verified geometry
- transform information
- metrics
- scientific status

This reduces coupling between research iteration and UI architecture.

---

## 43. Large-Image Performance

Scientific lunar imagery can be large.

Potential browser pressure points include:

- image decoding
- memory usage
- image dimensions
- zoom rendering
- overlay rendering
- network transfer
- large artifact loading

No current performance limit is asserted here.

---

### Large Correspondence Sets

Large numbers of correspondence lines may be expensive to render naively.

Depending on actual measured needs, optimized drawing mechanisms may eventually be appropriate.

Do not assume a particular rendering technology or library.

---

### Viewer Tiling

Large scientific visual products may eventually benefit from display-oriented tiles or image pyramids.

Important distinction:

```text
Visualization Tile
→ optimized for rendering
```

versus:

```text
Retrieval Tile
→ scientific candidate-search unit
```

The two concepts must not be assumed to use identical grids or semantics.

---

### Native Hyperspectral Data

The ordinary frontend should not require loading a full native hyperspectral IIRS cube merely to show a registration result.

Where applicable, it can consume an appropriate:

- preview
- selected representation
- scientifically defined derived 2D view

---

## 44. IIRS Presentation

Where IIRS-derived imagery is shown, preserve representation meaning where scientifically important.

Examples may include:

- selected spectral band
- PCA-derived component
- structural representation

Avoid labeling every derived representation simply as:

> grayscale IIRS

when that hides how the image was produced.

---

## 45. Accessibility

Scientific visualization should remain understandable without relying exclusively on visual color distinctions.

Important architectural principles include:

- keyboard-accessible controls
- meaningful labels
- logical heading structure
- readable status text
- adequate contrast
- accessible tables where practical
- status represented in text as well as appearance
- legends for scientific visualization

Formal accessibility-compliance claims require actual evaluation and should not be invented.

---

### Scientific Color Semantics

If candidate, inlier, and outlier states use different colors, also provide other cues where practical.

Possible additional cues include:

- labels
- marker shape
- line style
- legend
- text summary

Scientific interpretation should not depend solely on color perception.

---

## 46. Responsive Design

Lunar image inspection may naturally benefit from a large desktop workspace.

Large dual-image comparison can require substantial screen area.

That does not mean the entire application should become unusable on smaller displays.

A realistic goal is:

- navigation remains usable
- scientific status remains readable
- key result summaries remain accessible
- advanced image inspection may be desktop-oriented where justified

Do not claim complete mobile analysis parity unless it is intentionally designed and tested.

---

## 47. Frontend Security Boundaries

Frontend code processes untrusted information from:

- users
- backend metadata
- filenames
- result strings
- external resources
- artifact references

Do not assume scientific metadata is automatically safe for direct HTML rendering.

---

### Untrusted Text

Avoid rendering arbitrary metadata or backend text as trusted executable HTML.

---

### Secrets

Browser-delivered code must not contain private:

- API keys
- service credentials
- private tokens

Secrets belong on trusted server-side boundaries.

---

### File Preview

If local file previews exist, they should use browser-safe mechanisms appropriate to supported formats.

The frontend must not execute arbitrary uploaded content.

---

### External Resources

Externally supplied artifact/resource references should not automatically be trusted.

They should be consumed according to established backend/application contracts.

---

## 48. Reproducibility

The frontend should not create hidden scientific configuration that exists only inside UI state.

A run triggered through the frontend should remain scientifically traceable to the same kinds of information used by other execution paths, such as:

- source
- reference
- method/configuration
- scientific result

where those are provided by the backend.

---

### UI Execution vs Direct Execution

An equivalent scientific configuration should have the same methodological meaning whether invoked through:

- frontend/backend
- CLI
- benchmark runner
- direct research integration

The frontend is an invocation interface.

It must not redefine the method.

---

### Manual Review

If future research workflows permit human annotation or review, keep:

```text
Machine Result
+
Human Annotation
```

rather than silently replacing the original machine result.

Human intervention must remain traceable.

---

## 49. Notifications and Messaging

User-facing messages should accurately distinguish scientific and application outcomes.

Good:

> Registration completed but was rejected because the scientific result reported insufficient geometric support.

Avoid:

> Registration crashed.

when the engine executed correctly and intentionally rejected the result.

---

### Avoid Marketing Claims

Do not use unsupported result labels such as:

- Perfect Match
- AI Verified
- 100% Accurate
- Guaranteed Alignment
- Ultra-Precise

Scientific interfaces should communicate evidence, not hype.

---

## 50. Frontend Testing Architecture

Frontend testing should verify presentation and application correctness without pretending to replace scientific benchmarking.

### Unit Tests

May validate:

- value formatting
- view-model logic
- state transitions
- unit/status presentation

### Component Tests

May validate:

- result rendering
- metric presentation
- candidate/inlier distinction
- metadata states
- rejection presentation

### Contract Tests

May validate compatibility with backend application contracts.

### Interaction Tests

May validate:

- input flow
- request submission
- result inspection
- loading/rejection/error behavior

### Accessibility Tests

May verify important interactions/status communication where supported by actual project tooling.

### End-to-End Tests

May verify:

```text
User Request
      ↓
Backend
      ↓
Scientific Result
      ↓
Frontend Presentation
```

No particular testing framework is assumed.

---

## 51. Important Scientific UI Test Cases

Useful conceptual frontend scenarios include:

- accepted registration
- rejected registration
- invalid scientific input
- unsupported representation
- zero candidate matches
- result with candidate matches but no accepted geometry
- result with no independent check points
- source-image pixel metric
- reference-image pixel metric
- valid ground-unit metric
- candidate vs verified-inlier visualization
- outlier visualization
- unavailable metadata
- processing state
- backend/internal failure

These scenarios test UI correctness.

They are not actual ChandraMap benchmark results.

---

### Synthetic Fixture Values

UI tests may need synthetic values.

Those values should remain clearly test data.

Do not publish them as measured scientific performance.

---

## 52. Dependency Direction

Preferred frontend dependency direction:

```text
Features / Pages
      ↓
Application / View Models
      ↓
API / Data Access
      ↓
Backend Contracts
```

Scientific visualization consumes structured data:

```text
Structured Scientific Result
            ↓
Scientific Visualization
```

Shared reusable UI may be consumed by features:

```text
Shared UI
   ↑
Features
```

Avoid structures where:

```text
Shared UI
   ↓
Backend Internals
   ↓
Scientific Algorithms
```

---

## 53. No Browser-Side Scientific Reimplementation

If the Core Engine is implemented outside the browser, the frontend must not duplicate scientific algorithms merely for presentation convenience.

Avoid browser-side reimplementations of:

- SIFT
- RANSAC
- RMSE
- transform acceptance
- benchmark aggregation

unless an explicit scientifically equivalent shared-browser architecture is deliberately designed and documented.

No such architecture is assumed here.

---

## 54. Architectural Invariants

The following principles should remain true unless the frontend architecture is deliberately revised.

1. The frontend never becomes the authoritative scientific engine.

2. The frontend consumes scientific results from the backend/Core Engine.

3. Backend/core scientific results remain authoritative.

4. Source and reference roles remain explicit.

5. Source/reference identity survives visualization.

6. Frontend validation is not authoritative backend/scientific validation.

7. Candidate matches remain distinct from verified inliers.

8. Rejected outliers remain distinct where visualized.

9. RANSAC inliers are not labeled independent ground truth.

10. Matcher scores are not automatically registration confidence.

11. Registered previews are not presented as accuracy proof.

12. Scientific status remains visible alongside visual outputs.

13. Metric values retain their units.

14. Source-image and reference-image pixel metrics remain distinguishable.

15. Ground metres are shown only when provided by authoritative scientific evaluation.

16. The frontend does not invent pixel-to-ground conversions.

17. Sub-pixel is not presented as automatically sub-metre.

18. Fit residuals remain distinct from independent check-point error.

19. Spatial coverage is displayed from authoritative scientific data.

20. The browser does not independently calculate canonical scientific metrics.

21. Scientific rejection is a valid result.

22. Scientific rejection is not presented as an application crash.

23. Request/job state remains distinct from scientific result state.

24. Benchmark V1–V4 remain benchmark/research configurations.

25. Benchmark versions are not API/application versions.

26. Later benchmark versions are not automatically labeled superior.

27. UI filtering does not change canonical benchmark truth.

28. Scientific result values cannot be mutated by view state.

29. View state remains separate from remote scientific state.

30. `(x, y)` and `(row, column)` semantics remain explicit.

31. Source and reference coordinate spaces remain distinct.

32. Image-space and geospatial coordinates remain distinct.

33. Lunar CRS context is preserved where provided.

34. Earth WGS84 is never silently substituted for lunar spatial context.

35. Longitude convention remains explicit where scientifically relevant.

36. Unknown metadata remains unknown.

37. Artifacts remain distinct from structured scientific results.

38. Map functionality remains downstream of registration.

39. Mosaic functionality remains downstream of accepted scientific results.

40. Visualization tiles are not assumed to equal retrieval tiles.

41. Frontend-specific hidden thresholds do not alter canonical scientific methods.

42. The frontend does not silently resubmit rejected methods using stronger algorithms while preserving the original method label.

43. Public scientific statements do not use fake confidence percentages.

44. Frontend test fixtures are not presented as benchmark evidence.

45. Accessibility does not rely on color alone.

46. External/user-provided content is treated as untrusted presentation input.

47. Browser-delivered code contains no private server credentials.

48. Backend transport assumptions remain separated from reusable visual components where practical.

49. Scientific result semantics remain stable even if the underlying matcher changes.

50. Current and target frontend architecture remain clearly distinguished.

---

## 55. Frontend Anti-Patterns

### Scientific Logic in Components

A UI component computes:

- RANSAC
- RMSE
- inlier classification
- acceptance status

instead of displaying authoritative results.

---

### Frontend as a Second Engine

The browser reimplements the scientific registration pipeline.

This creates inconsistent scientific behavior between:

- benchmark runner
- backend
- browser

---

### Anonymous Accuracy

Displaying:

```text
Accuracy: 95%
```

without defining:

- metric
- population
- unit
- calibration

---

### Candidate Equals Correct Match

Candidate correspondences are presented as verified physical truth before geometric verification.

---

### Pretty Overlay Equals Success

The UI treats visual alignment as stronger evidence than authoritative status and metrics.

---

### V4 Equals Best

The frontend automatically labels the later benchmark version as superior.

---

### API Version Equals Benchmark Version

Application/API versioning and benchmark research versioning are mixed together.

---

### Earth Map Defaults

Lunar geospatial data is interpreted through Earth CRS assumptions.

---

### Hidden Parameter Tuning

UI controls alter canonical scientific thresholds without preserving the resulting configuration.

---

### Fake Progress

A timer drives a percentage that does not represent actual backend progress.

---

### Scientific Rejection as Error Toast

A valid rejected registration is displayed as though the application crashed.

---

### Dropped Units

The backend provides:

```text
0.8 source-image px
```

and the frontend displays:

```text
0.8
```

---

### Recomputed Metrics

Frontend code calculates a second definition of RMSE or spatial coverage.

---

### Mutable Scientific Result

Users can directly edit canonical:

- RMSE
- transform
- inlier status
- scientific acceptance

without creating a separate annotation or derived record.

---

### Color-Only Science

The meaning of candidate/inlier/outlier classifications is conveyed only through color.

---

### Giant Feature Component

One component is responsible for:

- input handling
- networking
- scientific interpretation
- image rendering
- charting
- map rendering
- application state

with no coherent responsibility boundaries.

---

### Direct Transport Logic Everywhere

Each visual component independently constructs backend requests and interprets transport failures.

---

### Framework-Coupled Scientific Meaning

Scientific status depends on component lifecycle or UI-library behavior rather than backend/core results.

---

### Client-Side Benchmark Gaming

Failed/rejected pairs are silently removed before displaying benchmark aggregate results.

---

### Frontend-Only Quality Score

The browser creates its own composite quality number from unrelated scientific metrics.

---

### Secret in Browser Code

Private service credentials are bundled into publicly delivered frontend code.

---

## 56. Performance and Scalability Principles

Frontend architecture should be prepared to scale primarily along dimensions such as:

- image size
- number of candidate correspondences
- number of verified inliers
- artifact count
- benchmark-result count
- downstream map layers

No current scalability claim is made by this document.

Potential optimization should respond to measured problems rather than anticipated architectural prestige.

---

### Avoid Premature Complexity

Do not introduce without demonstrated need:

- micro-frontends
- multiple competing global state systems
- large event-bus architectures
- client-side databases
- plugin marketplaces

A clear modular frontend application is a professional architecture.

---

## 57. Architecture Evolution

Frontend complexity should follow stable scientific/application contracts.

A conceptual evolution may be:

### Stage A — Scientific Result Viewer

Focus on:

- source/reference context
- status
- metrics
- registered result

### Stage B — Interactive Registration Inspection

Add richer:

- candidate/inlier/outlier inspection
- synchronized image exploration
- diagnostic artifacts

### Stage C — Benchmark Analysis

Add:

- method comparisons
- distributions
- stress-category views
- failure inspection

### Stage D — Map / Mosaic Exploration

Add downstream geospatial visualization when scientific geolocation/registration is sufficiently trustworthy.

### Stage E — Advanced Research Interfaces

Add richer experimental inspection only where real research needs justify it.

This progression is conceptual architecture guidance.

It is not a committed roadmap.

---

## 58. When to Add Advanced Visualization

Additional visualization complexity is justified when it improves:

- scientific interpretation
- debugging
- failure analysis
- research usability
- communication

It should not be introduced merely for visual spectacle.

---

## 59. When to Add Map or 3D Features

Map, mosaic, or 3D visualization should be introduced when:

- scientifically meaningful geospatial results exist
- coordinate semantics are trustworthy
- real user/research needs justify the interface

These features should not block development or validation of the registration core.

---

## 60. Frontend Success Criteria

A strong ChandraMap frontend should allow a user to:

- understand which source and reference observations are being compared
- select an intentionally supported scientific workflow
- submit a registration request
- understand application/processing state
- distinguish application status from scientific status
- understand accepted vs rejected registration
- inspect candidate correspondences
- inspect verified inliers and outliers
- inspect the registered result
- read quality metrics with correct units
- distinguish fit residual from independent error
- understand missing metadata
- inspect provenance where useful
- inspect artifacts where supported
- explore downstream map/mosaic outputs where scientifically valid
- do all of this without the frontend redefining scientific truth

Frontend quality should be judged by:

- scientific fidelity
- clarity
- accessibility
- usability
- maintainability
- performance
- stable application boundaries

not by the number of UI libraries or visual effects used.

---

## 61. Relationship with Other Documents

### [`system-overview.md`](./system-overview.md)

Defines where the frontend sits within the wider ChandraMap system.

This file focuses specifically on frontend responsibilities and scientific-result consumption.

---

### [`core-engine-architecture.md`](./core-engine-architecture.md)

Defines the scientific engine that owns:

- correspondence
- geometry
- registration
- evaluation
- scientific status

The frontend consumes those results.

---

### [`backend-architecture.md`](./backend-architecture.md)

Defines the application/server boundary that:

- accepts requests
- invokes the Core Engine
- translates results
- exposes artifacts

The frontend communicates through that application boundary rather than becoming part of the scientific engine.

---

### [`v1-pipeline.md`](./v1-pipeline.md)

Defines canonical Benchmark V1 scientific processing.

The frontend may visualize V1 inputs, progress, and results but must not implement the V1 pipeline itself.

---

### [`../project/v1-scope.md`](../project/v1-scope.md)

Defines what Benchmark V1 includes and excludes.

Frontend labels and workflow presentation must respect that scope.

---

### [`../project/terminology.md`](../project/terminology.md)

Defines canonical human-facing terms such as:

- source
- reference
- candidate match
- verified inlier
- registered preview
- Benchmark V1

Frontend wording should remain consistent with these definitions.

---

### [`../project/assumptions.md`](../project/assumptions.md)

Defines scientific assumptions underlying ChandraMap processing.

Frontend presentation should not hide those assumptions when they materially affect interpretation.

---

### [`../project/limitations.md`](../project/limitations.md)

Defines scientific and methodological limitations.

Frontend result presentation should communicate relevant limitations honestly rather than masking them with presentation polish.

---

### [`.ai/architecture/MODULE_MAP.md`](../../.ai/architecture/MODULE_MAP.md)

Owns actual repository/module responsibility mapping.

Exact frontend implementation paths belong there when verified.

---

### [`.ai/architecture/DATA_FLOW.md`](../../.ai/architecture/DATA_FLOW.md)

Defines detailed information, coordinate, result, and provenance movement across system boundaries.

This frontend architecture defines how the client-side layer should consume that information.

---

### [`.ai/development/TESTING_RULES.md`](../../.ai/development/TESTING_RULES.md)

Defines broader software/scientific testing principles relevant to frontend/backend integration and result presentation.

---

## 62. Key Frontend Rules

1. Frontend is a consumer of scientific results.

2. Frontend never becomes the authoritative registration engine.

3. Backend/Core Engine remains authoritative for scientific computation.

4. Source and reference roles remain explicit.

5. Frontend validation is not authoritative backend/scientific validation.

6. Benchmark V1–V4 are research configurations, not software releases.

7. Benchmark V1 is not an API version.

8. V4 is not automatically the best configuration.

9. Request/application state remains separate from scientific result state.

10. Scientific rejection is presented as a valid result rather than an application crash.

11. Fake progress percentages are prohibited.

12. Candidate matches remain distinct from verified inliers.

13. Verified inliers are not labeled independent ground truth.

14. Outliers remain distinguishable where shown.

15. Visual overlays do not prove scientific accuracy.

16. Registered previews remain downstream of authoritative geometry.

17. Scientific metrics are displayed rather than independently recomputed.

18. Metric units are preserved.

19. Source-image and reference-image pixel metrics remain distinct.

20. Metre-level error is shown only when scientifically supplied.

21. Sub-pixel is not automatically labelled sub-metre.

22. Fit residual and independent check-point accuracy remain separate.

23. Matcher scores are not converted into fake registration-confidence percentages.

24. No frontend-only combined scientific quality score is invented.

25. Coordinate conventions remain explicit.

26. `(x, y)` and `(row, column)` are not silently swapped.

27. Image coordinates remain distinct from lunar geospatial coordinates.

28. Lunar CRS context is preserved.

29. Earth WGS84 is never silently substituted.

30. Longitude convention remains explicit where relevant.

31. Unknown metadata remains unknown.

32. Scientific artifacts remain distinct from authoritative result data.

33. Map/mosaic functionality remains downstream.

34. Visualization tiling and retrieval tiling remain separate concepts.

35. Scientific result state remains separate from mutable view state.

36. Backend communication is centralized conceptually rather than scattered across scientific components.

37. Transport details do not define scientific meaning.

38. Frontend code does not contain private backend credentials.

39. Untrusted metadata/content is rendered safely.

40. Accessibility does not rely on color alone.

41. Responsive design is realistic about large scientific-image workflows.

42. Test-fixture numbers are not presented as actual benchmark results.

43. UI filters do not modify canonical benchmark results.

44. Manual annotation remains separate from original machine results.

45. Hidden frontend threshold tuning must not alter canonical benchmark methodology.

46. Current and target frontend architecture remain explicitly distinguishable.

<!-- Source specification: :contentReference[oaicite:0]{index=0} -->
