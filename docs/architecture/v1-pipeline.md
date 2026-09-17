# ChandraMap Benchmark V1 Pipeline

ChandraMap Benchmark V1 is the project's **classical, known-overlap lunar image-registration baseline**.

It processes one source/reference pair from validated 2D inputs through classical local feature extraction, candidate matching, robust geometric verification, transformation estimation, registration, evaluation, and an explicit accept/reject decision.

The canonical scientific flow is:

```text
Known Source Image
        +
Known Overlapping Reference
        ↓
Input Validation
        ↓
Minimal 2D Preparation
        ↓
SIFT Feature Extraction
        ↓
Descriptor Matching
        ↓
Candidate Filtering
        ↓
RANSAC / Geometric Verification
        ↓
Verified Inliers
        ↓
Coverage / Residual Analysis
        ↓
Final Affine / Homography
        ↓
Registration / Warp
        ↓
Final Evaluation
        ↓
Accept / Reject
        ↓
Structured Result + Diagnostics
```

The key V1 principle is:

> **Keep the baseline simple enough to understand, reproduce, diagnose, and compare against later benchmark configurations.**

V1 must not silently absorb V2, V3, or V4 capabilities merely to rescue difficult image pairs.

---

## 1. Purpose

This document defines the **ordered scientific processing flow for one canonical Benchmark V1 registration case**.

It answers:

- what enters V1
- what V1 assumes
- which processing stages execute
- what each stage consumes and produces conceptually
- where candidate matches become geometrically verified inliers
- when the transformation is estimated
- when geometry is checked
- when warping is permitted
- how the final registration is evaluated
- when the pipeline should reject instead of continue
- how scientific rejection differs from invalid input or software failure
- which diagnostic artifacts may be generated
- what is deliberately excluded from V1
- how V1 limitations motivate later benchmark versions

This document describes **processing order**.

It does not define:

- concrete Python functions
- repository module paths
- classes
- schemas
- configuration keys
- metric equations
- numerical thresholds
- benchmark results

The authoritative V1 scope remains [`../project/v1-scope.md`](../project/v1-scope.md) together with the canonical [`.ai/context/V1_SCOPE.md`](../../.ai/context/V1_SCOPE.md).

---

## 2. V1 Pipeline in One Sentence

> **Given a valid source lunar image and the already-known overlapping reference region, V1 uses a minimal classical local-feature workflow to propose correspondences, verify them geometrically, estimate a source-to-reference transform, register the source, evaluate the final geometry, and explicitly accept or reject the result.**

---

## 3. Scope and Assumptions

### 3.1 Known Overlap

Canonical V1 begins **after global reference discovery**.

The correct approximate overlapping reference region is already supplied.

Therefore V1 does not need to answer:

> Where on the Moon does this source image belong?

It answers:

> Given the correct overlapping region, can a classical local-registration method align the two observations reliably?

Conceptually:

```text
Source Image
      +
Known Reference Region
        ↓
Local Registration
```

not:

```text
Source Image
      ↓
Whole-Moon Search
      ↓
Candidate Retrieval
      ↓
Local Registration
```

---

### 3.2 Source and Reference Roles

V1 must preserve two distinct roles:

**Source**
The lunar observation being registered.

**Reference**
The known overlapping image or region that defines the target registration context.

Avoid ambiguous scientific descriptions such as:

```text
image1
image2
```

when direction matters.

---

### 3.3 Canonical Transform Direction

The conceptual transformation direction is:

```text
source coordinates
        ↓
     transform
        ↓
reference coordinates
```

Any implementation that uses the inverse convention internally must keep the actual direction explicit and consistent during:

- estimation
- point transformation
- warping
- evaluation
- reporting

A transformation matrix without direction is incomplete scientific information.

---

### 3.4 Registration-Ready 2D Inputs

Canonical V1 operates on suitable 2D image representations.

Typical conceptual inputs are:

- panchromatic lunar imagery
- grayscale-compatible 2D scientific imagery
- controlled benchmark crops
- explicitly derived 2D representations where permitted

V1 does not imply that every native mission product can be sent directly to the local matcher.

---

### 3.5 Optional Scientific Context

Where available, a V1 pair may also carry:

- source/reference product identity
- sensor identity
- GSD
- masks or no-data information
- projection/geospatial metadata
- provenance identifiers
- independent evaluation check points

V1 must not fabricate missing metadata simply because later stages would benefit from it.

---

## 4. End-to-End Pipeline

```text
┌─────────────────────────────────────────────────┐
│ Source Image + Known Overlapping Reference      │
└────────────────────────┬────────────────────────┘
                         │
                         ▼
                 Pair Initialization
                         │
                         ▼
                  Input Validation
                  │              │
             valid│              │invalid
                  ▼              └────────→ INVALID INPUT
          Minimal 2D Preparation
                  │
                  ▼
             SIFT Features
             │           │
      usable │           │none / insufficient
             ▼           └───────────────→ REJECT
       Descriptor Matching
                  │
                  ▼
          Candidate Matches
             │           │
      enough │           │insufficient
             ▼           └───────────────→ REJECT
          Candidate Filter
                  │
                  ▼
          Geometry Point Pairs
                  │
                  ▼
                RANSAC
          ┌───────┴──────────┐
          │                  │
 valid geometry          no valid model
          │                  │
          ▼                  └───────────→ REJECT
     Verified Inliers
          │
          ├────────→ Spatial Coverage
          │
          ├────────→ Residual Analysis
          │
          ▼
      Final V1 Transform
          │
          ▼
    Registration / Warp
          │
          ▼
      Final Evaluation
          │
       ┌──┴────────┐
       │           │
    ACCEPT       REJECT
       │           │
       └─────┬─────┘
             ▼
      Structured Result
             +
     Diagnostic Artifacts
```

If a future authoritative V1 definition explicitly includes point refinement, the refinement stage must occur only after geometric verification and must be followed by final transform refitting.

Canonical V1 should not silently add such refinement when it is outside the approved baseline.

---

## 5. Pipeline Stage Summary

| Stage                      | Purpose                                         | Primary Conceptual Output      |
| -------------------------- | ----------------------------------------------- | ------------------------------ |
| Pair initialization        | Establish source/reference and V1 context       | Initialized pair/run context   |
| Input validation           | Ensure the inputs can be processed meaningfully | Validated pair                 |
| Representation preparation | Produce minimal matcher-ready 2D images         | Prepared source/reference      |
| Feature extraction         | Detect local structure                          | Keypoints + descriptors        |
| Descriptor matching        | Propose local correspondences                   | Candidate matches              |
| Candidate filtering        | Remove weak/ambiguous proposals                 | Filtered candidates            |
| Point preparation          | Build aligned point pairs for geometry          | Source/reference point pairs   |
| RANSAC                     | Robustly verify geometric consistency           | Initial geometry + inlier mask |
| Geometry validation        | Reject invalid or degenerate geometry           | Validated initial geometry     |
| Inlier extraction          | Separate verified support from outliers         | Verified inlier set            |
| Coverage analysis          | Measure spatial support                         | Coverage evidence              |
| Residual analysis          | Measure model fit                               | Residual evidence              |
| Final transform            | Establish authoritative V1 geometry             | Source→reference transform     |
| Registration / warp        | Apply geometry                                  | Registered output              |
| Evaluation                 | Measure final result                            | Quality/accuracy evidence      |
| Decision                   | Accept or reject scientifically                 | Final status                   |
| Result packaging           | Preserve scientific outcome                     | Structured result              |
| Diagnostics                | Support interpretation                          | Optional artifacts             |

---

## 6. Stage 0 — Pair Initialization

### Purpose

Initialize one V1 registration case without performing scientific matching yet.

Conceptual responsibilities include:

- identify the source
- identify the reference
- preserve source/reference roles
- associate the pair with its benchmark/run context
- resolve the active canonical V1 methodology
- preserve available provenance
- establish transform direction

The pipeline should know at this point:

```text
SOURCE
→ observation being registered

REFERENCE
→ known overlapping target region
```

No feature matching should begin while those roles are ambiguous.

---

### Configuration Boundary

Canonical V1 parameters should come from the project's actual benchmark/configuration mechanism.

This document does not assume:

- a particular configuration file format
- environment variables
- a specific config library
- command-line flags

Configuration selects the methodology.

Scientific stages execute it.

---

## 7. Stage 1 — Input Validation

### Purpose

Reject structurally invalid or unusable inputs before expensive scientific processing.

Potential checks include:

- input can be read
- image is non-empty
- dimensions are valid
- expected 2D representation is present
- numeric values are usable
- finite values are available where required
- masks/no-data are structurally valid where used
- source/reference identity is known
- required context for the selected methodology exists

---

### Valid Path

```text
Valid Source
     +
Valid Reference
     ↓
Continue
```

---

### Invalid Input Path

Examples may include:

- unreadable input
- empty image
- unsupported representation
- invalid dimensions
- structurally incompatible mask

Conceptually:

```text
Invalid Input
     ↓
Stop Processing
     ↓
Explicit Invalid Status / Failure Context
```

Do not continue until a later feature extractor fails obscurely.

---

### Invalid Input vs Scientific Rejection

These are different.

**Invalid Input**

> The pipeline cannot meaningfully interpret or process what it received.

**Scientific Rejection**

> The input was valid, but V1 could not establish sufficiently reliable registration evidence.

The distinction must remain visible.

---

## 8. Stage 2 — Minimal Representation Preparation

### Purpose

Prepare both valid inputs for the classical local-feature baseline.

V1 preprocessing should remain:

- minimal
- generic
- deterministic where practical
- reproducible
- easy to explain

Potential operations, only where scientifically appropriate, may include:

- grayscale-compatible representation
- datatype preparation
- valid-data masking
- finite-value handling
- basic intensity normalization
- deterministic preparation required by the active SIFT-based configuration

This document does not define exact normalization mathematics.

---

### Preprocessing Boundary

Canonical V1 preprocessing should not become:

- sensor-specific optimization
- pair-specific enhancement
- automatic complex routing
- advanced illumination modeling

Those are later research concerns unless explicitly added to the authoritative V1 definition.

---

### No Hidden Pair-Specific Processing

Avoid methodology such as:

```text
Pair A
→ special contrast settings

Pair B
→ different hidden settings

Pair C
→ another manually chosen filter
```

after examining benchmark outcomes.

Canonical preprocessing must be reproducible.

---

## 9. IIRS Boundary

Native IIRS data is hyperspectral / imaging-infrared data.

A full hyperspectral observation must not be treated blindly as an ordinary grayscale image.

Canonical V1 expects a registration-ready 2D representation.

If a controlled V1 experiment uses an externally prepared deterministic IIRS-derived representation, it should be described as:

> **derived 2D registration input**

not:

> **native full-IIRS registration support**

Conceptually:

```text
Native IIRS Cube
      ↓
Externally Defined / Approved 2D Representation
      ↓
V1 Local Registration
```

Advanced questions such as:

- band selection
- PCA/component selection
- spectral representation optimization
- modality-specific preprocessing

belong primarily to later benchmark research.

---

## 10. Stage 3 — Minimal Scale Preparation Boundary

Canonical V1 intentionally avoids advanced automatic scale search.

If basic resizing is required by the approved V1 methodology or for technical compatibility, it must be described as:

> resampling

rather than:

> physical resolution recovery.

Conceptually:

```text
Coarse Image
    ↓
Upsampling
    ↓
More Raster Samples
```

does not mean:

```text
More Physical Lunar Detail
```

V1 must not silently introduce:

- GSD-driven reference-pyramid search
- automatic scale candidate optimization
- complex multi-scale routing

Those belong primarily to Benchmark V2.

If canonical V1 does not define an explicit scale-preparation step, this boundary should simply remain inactive.

---

## 11. Stage 4 — SIFT Feature Extraction

### Purpose

Detect local image structure and compute classical local descriptors independently in the source and reference images.

Conceptually:

```text
Prepared Source
      ↓
     SIFT
      ↓
Source Keypoints
+
Source Descriptors
```

and:

```text
Prepared Reference
      ↓
     SIFT
      ↓
Reference Keypoints
+
Reference Descriptors
```

---

### SIFT Role

SIFT provides:

- local keypoints
- local descriptors

SIFT does not:

- establish final physical truth
- perform RANSAC
- evaluate final registration accuracy
- decide success

---

### RootSIFT Boundary

RootSIFT is a transformation/normalization of SIFT descriptors.

It is not a separate keypoint detector.

If an approved canonical V1 configuration explicitly uses RootSIFT, it should be identified as a distinct named configuration.

Do not silently describe:

```text
SIFT
```

and:

```text
RootSIFT
```

as the same benchmark method.

SIFT remains the primary V1 classical baseline unless the authoritative V1 specification explicitly defines another approved configuration.

---

### No-Feature Branch

If either source or reference produces no usable keypoints/descriptors:

```text
No Usable Features
       ↓
Cannot Form Candidate Correspondences
       ↓
Scientific Rejection
```

The pipeline must not fabricate descriptors or matches.

---

## 12. Stage 5 — Descriptor Matching

### Purpose

Compare source and reference local descriptors and propose possible correspondences.

Classical matching policy may conceptually use mechanisms such as:

- nearest-neighbor matching
- KNN matching
- ratio filtering
- mutual/cross consistency

The exact canonical strategy belongs to the active V1 configuration.

This document does not invent:

- matcher implementation
- ratio value
- neighbor count
- descriptor-distance threshold

---

### Matcher Output

The output is:

> **candidate matches**

A candidate match is a hypothesis.

It is not yet:

- a verified inlier
- a final tie point
- independent ground truth
- proof of correct registration

Conceptually:

```text
Source Feature
      ↔
Reference Feature
```

---

### Critical Semantic Boundary

Never interpret:

```text
Descriptor Similarity
```

as:

```text
Verified Physical Lunar Correspondence
```

Geometric verification must occur first.

---

## 13. Stage 6 — Candidate Filtering

### Purpose

Remove weak or ambiguous descriptor associations before robust geometric estimation.

The active filtering policy should be:

- fixed by the methodology
- reproducible
- applied consistently
- independent of final evaluation truth

---

### Hidden Tuning Is Not Canonical V1

Avoid:

```text
Inspect final RMSE
      ↓
Change match filter
      ↓
Run again
      ↓
Choose whichever looks best
```

for individual benchmark pairs.

Such behavior invalidates a controlled baseline unless explicitly defined as a separate diagnostic/oracle experiment.

---

### Insufficient-Candidate Branch

If too few candidate correspondences remain to estimate the selected geometric model:

```text
Insufficient Candidate Support
             ↓
      Scientific Rejection
```

The pipeline should not call robust geometry with an impossible point configuration and treat the predictable failure as an unexpected software problem.

No numerical minimum is defined here.

---

## 14. Stage 7 — Geometry Point Preparation

### Purpose

Convert filtered feature matches into aligned source/reference point pairs for geometric estimation.

Conceptually:

```text
Candidate Match i
      ↓
Source Point i
      ↔
Reference Point i
```

The relationship must remain exact.

---

### Pairing Invariant

Candidate ordering and point ordering must remain aligned.

For example:

```text
candidate i
↔ source point i
↔ reference point i
```

must remain true after any:

- filtering
- sorting
- slicing
- format conversion

A stale or reordered mapping can silently corrupt geometry.

---

### Coordinate Convention

Implementations must distinguish:

```text
(x, y)
```

from:

```text
(row, column)
```

These conventions are not automatically interchangeable.

Likewise, source-image coordinates and reference-image coordinates must remain distinct.

---

## 15. Stage 8 — RANSAC / Geometric Verification

### Purpose

Estimate an initial geometric relationship while separating model-consistent candidate matches from geometric outliers.

Conceptually:

```text
Filtered Candidate Matches
            ↓
          RANSAC
            ↓
Initial Transform
       +
Inlier Mask
       ↓
Verified Inliers
       +
Rejected Outliers
```

---

### RANSAC Role

RANSAC performs:

> robust geometric estimation.

It does not perform:

- keypoint detection
- descriptor computation
- descriptor matching
- retrieval
- independent accuracy evaluation

---

### RANSAC Inlier Meaning

A RANSAC inlier means:

> **the candidate is consistent with the selected model under the configured geometric criterion.**

It does not automatically mean:

> **the point pair has been independently verified as the same physical lunar location.**

Therefore:

```text
RANSAC Inlier
≠
Ground Truth
```

---

### Inlier-Mask Integrity

The inlier mask must refer to the exact candidate list used by geometric estimation.

Conceptually:

```text
Candidate i
    ↔
Inlier Mask i
```

If candidate order changes, mask alignment must remain correct.

---

## 16. RANSAC Failure Branches

Several scientific outcomes are possible.

### No Valid Model

The candidate correspondence set does not support a usable transformation.

Result:

> reject.

---

### Insufficient Verified Support

A numerical transform may exist but insufficient reliable support exists according to the benchmark policy.

Result:

> reject.

---

### Degenerate Geometry

The point configuration does not constrain the selected model reliably.

Possible conceptual causes include:

- too few points
- severe clustering
- nearly collinear arrangements
- unstable geometry

Result:

> reject.

---

### Numerical / Invalid Transform

The estimator produces unusable or non-finite geometry.

Result:

> reject or classify as invalid according to the actual system policy.

Do not continue to warping with an invalid transform.

---

## 17. Stage 9 — Initial Geometry Validation

After robust estimation, the geometry should be checked before any registration output is generated.

Conceptual checks include:

- transform exists
- transform values are finite
- expected transform form is valid
- transform direction is correct
- enough model-consistent support exists
- point geometry is not obviously degenerate
- source/reference coordinate pairing remains correct

No numerical thresholds are defined here.

---

### Geometry Validation Is Not Final Accuracy Evaluation

This stage asks:

> Is the estimated geometry structurally usable?

It does not yet answer:

> How accurate is the final registration independently?

Those responsibilities remain separate.

---

## 18. V1 Transform Model

Canonical V1 may use a baseline global 2D transform such as:

- affine
- homography

according to the approved V1 methodology.

The active model must be explicit.

---

### Affine Role

An affine transform can represent planar combinations of:

- translation
- rotation
- scale
- shear

within its modeling assumptions.

It is a baseline approximation.

---

### Homography Role

A homography provides a more flexible planar projective relationship.

It remains a planar model.

It must not be presented as a universal model for:

- arbitrary lunar relief
- all viewing geometry
- full 3D terrain

---

### No Oracle Model Selection

Do not:

```text
Fit Affine
Fit Homography
Check Final Ground Truth
Choose the Lower RMSE
```

unless the experiment explicitly defines oracle selection.

Canonical transform policy must be established independently of final benchmark truth.

---

## 19. Stage 10 — Verified Inlier Extraction

### Purpose

Use the robust-estimation result to separate:

```text
Verified / Model-Consistent Inliers
```

from:

```text
Rejected Outliers
```

Only the verified inlier set should drive subsequent model-support analysis.

---

### Candidate Count vs Inlier Count

Keep these values distinct.

```text
Candidate Match Count
        ≥
Verified Inlier Count
```

They measure different stages of the process.

Do not overwrite the candidate population with the inlier population and later report both as though they were independent values.

---

## 20. Stage 11 — Spatial Coverage Analysis

### Purpose

Measure whether the verified inliers provide useful spatial support across the relevant image/overlap region.

Possible project metrics may include:

- grid coverage
- convex-hull coverage

Use whichever definition is authoritative elsewhere.

This document does not define the formula.

---

### Why Coverage Matters

Consider:

```text
Many Inliers
Concentrated Around One Small Crater
```

versus:

```text
Fewer Inliers
Distributed Across the Shared Region
```

Both may have similar match counts, but they may constrain global geometry very differently.

Therefore:

> **inlier count alone is not enough to characterize geometric support.**

---

### Coverage Is Supporting Evidence

Coverage itself is not proof of physical correctness.

It should be interpreted alongside:

- transform validity
- residuals
- independent evaluation where available

---

## 21. Stage 12 — Residual and Geometric Quality Analysis

### Purpose

Measure how well the verified points agree with the estimated geometric model.

Potential diagnostics may include:

- correspondence residuals
- fit-point RMSE
- residual summaries
- geometry diagnostics

Exact metric formulas belong elsewhere.

---

### Fit Residual Warning

Residuals computed using points that helped estimate the transform are:

> **fit-quality measures**

They are not automatically:

> **independent registration accuracy**

Conceptually:

```text
Fit Points
    ↓
Estimate Transform
    ↓
Evaluate Same Points
    ↓
Fit Residual
```

is scientifically different from:

```text
Independent Check Points
        ↓
Evaluate Final Transform
        ↓
Independent Accuracy
```

---

## 22. Stage 13 — Refinement Boundary

Canonical V1 normally remains a simple classical baseline.

If the authoritative V1 scope excludes advanced sub-pixel refinement, the V1 path should proceed conceptually as:

```text
Verified Inliers
      ↓
Coverage / Residual Analysis
      ↓
Final V1 Geometry
```

Do not silently add refinement merely because it improves residuals.

---

### If Refinement Is Explicitly Added to Canonical V1

Only if the authoritative V1 definition explicitly permits it, use the scientifically correct order:

```text
Candidate Matches
      ↓
Geometric Verification
      ↓
Verified Inliers
      ↓
Sub-Pixel / Tie-Point Refinement
      ↓
Final Transform Refit
```

Do not refine arbitrary unverified candidate matches first.

---

### Critical Refit Rule

If point coordinates change:

> **the authoritative transform must be re-estimated from the final accepted point coordinates.**

Do not:

```text
Refine Tie Points
      ↓
Keep Old Transform
      ↓
Call It Final
```

---

## 23. Stage 14 — Final Transform

### Purpose

Establish the authoritative geometric relationship used for V1 registration and final evaluation.

The final transform should conceptually preserve:

- model type
- source→reference direction
- source coordinate space
- reference coordinate space
- verified support

In a V1 configuration without additional refinement, the final geometry follows the approved robust-estimation/refit policy defined by the actual methodology.

This document does not invent an extra transform-refit operation where the authoritative implementation does not define one.

---

### Bare Matrix Is Not Enough Conceptually

This:

```text
[ ... matrix values ... ]
```

is not a complete scientific description without:

- model
- direction
- coordinate semantics

---

## 24. Stage 15 — Registration / Warp

### Purpose

Apply the final source→reference geometry to produce an aligned representation.

Potential outputs include:

- transformed source coordinates
- registered source image
- registered preview
- overlap visualization

Exact raster formats or interpolation policies are implementation details outside this document.

---

### Correct Order

```text
Correspondence
      ↓
Geometry
      ↓
Final Transform
      ↓
Warp
```

not:

```text
Warp
  ↓
Manually Adjust Until It Looks Good
```

---

### Warp Success Is Not Scientific Success

This is a critical V1 principle:

> **A library can successfully warp an image using an incorrect transformation.**

Therefore:

```text
Warp Completed
≠
Registration Proven Accurate
```

The final transformation must still be scientifically evaluated.

---

## 25. Stage 16 — Independent Evaluation

Where trusted independent check points exist, evaluate the **final transform** using them.

Conceptually:

```text
Independent Source Check Points
             ↓
       Final Transform
             ↓
 Predicted Reference Locations
             ↓
Trusted Reference Check Points
             ↓
      Independent Error
```

---

### Evaluate the Final Geometry

If geometry changes during any earlier processing, evaluation must use the final accepted transform.

Do not report final accuracy from an obsolete preliminary transform.

---

### No Independent Check Points

If independent check points are unavailable, V1 may still report appropriate evidence such as:

- candidate count
- verified-inlier count
- inlier ratio
- fit residuals
- spatial coverage
- transform status
- diagnostic visualizations
- success/rejection under the available benchmark policy

However, it must not claim:

> **independent registration accuracy**

when no independent truth was evaluated.

---

## 26. Error Units

Every error value must identify its measurement domain.

Prefer:

- `source-image px`
- `reference-image px`
- metres only when scientifically valid

Avoid:

```text
RMSE = 0.8
```

without units and context.

---

### Source-Image Pixel Error

If the canonical evaluation expresses error in source-image pixels, that domain must remain explicit.

For example:

```text
0.8 source-image px
```

---

### Reference-Image Pixel Error

Reference-image pixel error may have a different physical meaning if the source and reference have different GSD.

The two domains are not interchangeable.

---

### Ground Error

Convert image-space error to metres only when:

- spatial information is valid
- the coordinate interpretation is correct
- the metric definition supports the conversion
- the conversion is scientifically meaningful

Do not treat a broad approximate instrument GSD as exact ground truth.

---

### Sub-Pixel vs Sub-Metre

If:

```text
error < 1 image pixel
```

then the result may be described as:

> **sub-pixel in that image coordinate system**

It does not automatically imply:

> **sub-metre on the lunar surface**

---

## 27. Stage 17 — Accept / Reject Decision

### Purpose

Determine whether the available scientific evidence is sufficient to accept the V1 registration under the active benchmark policy.

Potential evidence may include:

- valid transformation
- sufficient candidate support
- sufficient verified support
- useful spatial coverage
- acceptable residual behavior
- independent error where required and available

No universal thresholds are defined here.

---

### Acceptance Must Use Final Evidence

Do not accept a V1 result solely because:

- many descriptor matches exist
- matcher confidence is high
- RANSAC returned a model
- a transformation matrix exists
- a warp completed
- the overlay looks good

Acceptance should consider the complete defined V1 evidence.

---

### Rejection Is Valid

Potential scientific rejection reasons include:

- insufficient features
- missing descriptors
- insufficient candidate matches
- no valid geometric model
- insufficient verified inliers
- degenerate geometry
- poor spatial support
- invalid transform
- evaluation quality below the benchmark criterion

A rejected V1 result can be scientifically correct.

---

### No Identity Fallback

Never convert registration failure into:

```text
Identity Transform
+
Success
```

unless identity is genuinely supported by the estimated geometry.

---

### No Silent Method Fallback

Do not silently switch from canonical V1 matching to:

- learned matching
- another advanced matcher
- retrieval
- DEM correction

after failure while still labeling the result canonical V1.

A fallback, if ever introduced into a methodology, must be explicit and reproducible.

---

## 28. Stage 18 — Structured Scientific Result

One V1 attempt should conceptually produce one coherent scientific outcome.

Relevant categories may include:

- final status
- source/reference identity
- benchmark/pair context
- candidate-match count
- verified-inlier count
- inlier ratio
- transform model
- transform direction
- coverage evidence
- residual/error metrics
- evaluation units
- rejection/failure reason
- runtime where measured
- diagnostic-artifact references

This is a conceptual result model.

No concrete class, field names, or serialized schema are defined here.

---

## 29. Stage 19 — Diagnostic Artifacts

Optional V1 diagnostics may include:

- source-keypoint visualization
- reference-keypoint visualization
- candidate-match visualization
- inlier/outlier visualization
- coverage visualization
- registered preview
- source/reference overlay
- residual plot

These artifacts help:

- inspect behavior
- debug failures
- communicate results
- analyze difficult pairs

They are not authoritative scientific truth.

---

### Diagnostic Artifacts Must Not Override Results

A visually convincing overlay must not override:

- invalid geometry
- poor coverage
- insufficient verified support
- unacceptable independent error
- rejected status

Programmatically generated scientific metrics and status remain authoritative.

---

## 30. Stage 20 — Result Persistence and Reporting

Where the repository provides a persistence/reporting mechanism, a scientifically important V1 result should remain traceable to relevant context such as:

- pair identity
- V1 methodology/configuration
- code revision where tracked
- metric definitions
- generated artifacts

This document does not prescribe:

- a database
- file format
- manifest schema
- object store
- reporting framework

Persistence is an outer concern around the scientific result.

---

## 31. Decision Flow

| Condition                           | V1 Response                          |
| ----------------------------------- | ------------------------------------ |
| Structurally invalid input          | Stop as invalid input                |
| Unsupported 2D representation       | Stop as invalid/unsupported          |
| No usable features/descriptors      | Scientific rejection                 |
| Too few candidate matches           | Scientific rejection                 |
| No valid robust geometric model     | Scientific rejection                 |
| Degenerate geometry                 | Scientific rejection                 |
| Insufficient verified support       | Scientific rejection                 |
| Poor spatial coverage               | Reject according to benchmark policy |
| Invalid/non-finite transform        | Reject / invalid geometry            |
| Valid final transform               | Continue to registration/evaluation  |
| Independent quality insufficient    | Reject according to benchmark policy |
| Available evidence satisfies policy | Accept                               |

The benchmark/configuration defines the applicable criteria.

This document intentionally does not invent numerical thresholds.

---

## 32. Scientific State Transitions

V1 should preserve the semantic progression:

```text
Keypoints / Descriptors
        ↓
Candidate Matches
        ↓
Filtered Candidate Matches
        ↓
Model-Consistent Verified Inliers
        ↓
Final Geometric Support
        ↓
Final Transform
        ↓
Registered Output
        ↓
Evaluated Registration
        ↓
Accepted or Rejected Result
```

Each transition has meaning.

Do not collapse all intermediate states into one generic term such as `matches`.

---

### Candidate Matches Are Not Ground Truth

```text
Descriptor Match
≠
Correct Lunar Correspondence
```

---

### RANSAC Inliers Are Not Ground Truth

```text
RANSAC Inlier
=
Model-Consistent Candidate
```

not:

```text
RANSAC Inlier
=
Independent Physical Truth
```

---

## 33. Fit vs Independent Evaluation

Where independent check points exist:

```text
Verified Fit Points
       ↓
Estimate / Finalize Transform
       ↓
Final Transform
       │
       └────────────────────┐
                            ▼
                Independent Check Points
                            ↓
                     Accuracy Metrics
```

This separation protects the distinction between:

- fitting consistency
- independent registration accuracy

---

## 34. Failure and Rejection Taxonomy

### Input Failure

The pair cannot be meaningfully processed.

Examples:

- unreadable input
- unsupported representation
- malformed image structure

---

### Feature Failure

The inputs are valid, but usable local features cannot be extracted.

Examples:

- no usable keypoints
- no descriptors
- insufficient distinctive structure

This is normally a scientific limitation/rejection, not automatically a software defect.

---

### Matching Failure

Features exist, but too few useful candidate correspondences remain.

Result:

> scientific rejection.

---

### Geometric Failure

Candidate matches exist, but robust geometry cannot establish trustworthy support.

Examples:

- no valid model
- too few verified inliers
- degenerate point layout
- invalid transformation

---

### Quality Rejection

A valid-looking geometric model exists, but the complete evidence does not satisfy the active acceptance policy.

Potential reasons include:

- poor coverage
- unacceptable residual behavior
- inadequate independent evaluation

---

### Software / Runtime Failure

An unexpected implementation or execution problem occurs.

Examples conceptually include:

- incorrect indexing
- unexpected shape failure
- unhandled exception
- resource failure

This is not normal V1 scientific performance.

---

## 35. Scientific Failure vs Software Bug

A difficult lunar pair rejected because V1 cannot establish reliable correspondences may indicate:

> **the classical baseline is insufficient under those conditions.**

That does not automatically mean:

> **the implementation is broken.**

Conversely, defects such as:

- reversed source/reference direction
- stale inlier masks
- incorrect coordinate conversion
- wrong metric implementation

are software defects.

They must not be reported as scientific limitations of V1.

---

## 36. Pipeline Invariants

The following must remain true across the V1 pipeline.

1. Source and reference identity remain explicit.

2. Source→reference transform direction remains explicit.

3. `(x, y)` and `(row, column)` are not silently mixed.

4. Source-image and reference-image coordinate spaces remain distinct.

5. Candidate matches preserve source/reference pairing.

6. Candidate-match count is greater than or equal to verified-inlier count.

7. Inlier masks remain aligned with the exact candidate population used by geometry.

8. Candidate matches are not reported as verified inliers.

9. Verified inliers are model-consistent evidence, not independent ground truth.

10. RANSAC is used for robust geometry rather than local descriptor matching.

11. Geometry is validated before registration/warping.

12. Spatial coverage is considered where defined by benchmark policy.

13. Fit residuals are not labeled as independent accuracy.

14. Advanced refinement is not silently introduced into canonical V1.

15. If verified point coordinates are refined, the final transform is refit.

16. Final evaluation uses the final transform.

17. Every reported error includes a meaningful unit/domain.

18. Source-pixel and reference-pixel errors are not silently interchanged.

19. Ground metres are reported only through scientifically valid conversion.

20. Sub-pixel does not automatically mean sub-metre.

21. Warp success does not imply scientific acceptance.

22. Scientific rejection does not silently become successful identity registration.

23. V1 does not silently invoke global retrieval.

24. V1 does not silently invoke learned local matchers.

25. V1 does not silently invoke DEM/local terrain correction.

26. Benchmark truth does not drive hidden per-pair tuning.

27. Diagnostic visualizations cannot overwrite authoritative metrics or status.

---

## 37. Pair-Level vs Benchmark-Level Responsibility

The V1 pipeline processes **one registration pair**.

Conceptually:

```text
Source + Reference
       ↓
   V1 Pipeline
       ↓
Pair-Level Result
```

The benchmark layer manages multiple pair executions.

Conceptually:

```text
Benchmark Definition / Manifest
            ↓
      Pair 1 ─┐
      Pair 2 ─┼──→ V1 Pipeline ──→ Pair Results
      Pair N ─┘
            ↓
 Benchmark Aggregation / Reporting
```

---

### Pair-Level Evidence

Potential pair-level information includes:

- candidate count
- inlier count
- inlier ratio
- spatial coverage
- residual/error values
- transform status
- runtime where measured
- accept/reject status

---

### Benchmark-Level Evidence

Potential benchmark-level aggregation includes:

- success/rejection rates
- aggregate error statistics
- stress-category summaries
- runtime summaries

Exact aggregation definitions belong in benchmark/metric documentation.

---

### No Result Cherry-Picking

Rejected V1 pairs remain part of benchmark interpretation.

Do not evaluate the baseline only on successful registrations while silently discarding difficult failures.

---

## 38. Reproducibility

A V1 result should conceptually be reproducible from:

```text
Source / Reference Pair
        +
Canonical V1 Configuration
        +
Code Revision
        +
Random Seed where relevant
        +
Metric Definitions
        ↓
V1 Result
```

This document does not prescribe a concrete run-manifest schema.

---

### RANSAC Randomness

If robust estimation uses randomness:

- control seeds where practical
- record them where supported and scientifically relevant
- do not promise universal bit-for-bit reproducibility across every platform

Reproducibility and exact numerical identity are not always the same thing.

---

### No Hidden Local State

Canonical V1 should not depend on undocumented:

- notebook state
- developer-local files
- manual point edits
- unrecorded configuration changes

---

### No Per-Pair Hidden Tuning

Canonical V1 must not operate like:

```text
Pair A
→ parameter set A chosen after seeing final truth

Pair B
→ parameter set B chosen after seeing final truth

Pair C
→ different transform chosen after seeing final truth
```

unless explicitly identified as a separate oracle or diagnostic study.

---

### No Manual Visual Correction

Canonical automated V1 should not be:

```text
Run Matcher
     ↓
Manually Drag Correspondences
     ↓
Adjust Transform
     ↓
Report Automated Result
```

Manual annotation may be valid for:

- ground-truth creation
- diagnostic analysis
- separate assisted workflows

but it must not be hidden inside the automated baseline.

---

## 39. Testing Touchpoints

This pipeline should remain testable at important stage boundaries.

| Stage                      | Example Testing Concern                                                 |
| -------------------------- | ----------------------------------------------------------------------- |
| Validation                 | Invalid/unreadable input is rejected explicitly                         |
| Representation preparation | Output semantics remain correct                                         |
| Feature extraction         | Empty-feature cases are handled                                         |
| Matching                   | Candidate index relationships remain valid                              |
| Filtering                  | Candidate/source/reference alignment remains intact                     |
| Geometry                   | Known synthetic transforms can be recovered under controlled conditions |
| Inlier mask                | Mask remains aligned with candidates                                    |
| Coverage                   | Controlled point layouts produce expected qualitative behavior          |
| Transform direction        | Source→reference semantics remain correct                               |
| Warp                       | Geometry is applied in the correct direction                            |
| Evaluation                 | Controlled errors produce expected metric behavior                      |
| Failure handling           | Scientific rejection does not become fake success                       |

Detailed testing policy belongs in [`.ai/development/TESTING_RULES.md`](../../.ai/development/TESTING_RULES.md).

---

### Synthetic vs Real Data

Synthetic or mathematically controlled data is useful for verifying:

- coordinate semantics
- transform direction
- geometry
- error calculations
- failure behavior

Real lunar pairs are required for:

- domain validation
- correspondence research
- benchmark evidence

One does not replace the other.

---

## 40. Explicit V1 Exclusions

Canonical V1 normally excludes:

- whole-Moon localization
- global image retrieval
- global descriptors
- FAISS
- vector indexes
- Top-K candidate search
- learned global retrieval
- ALIKED
- LightGlue
- LoFTR
- SuperPoint
- SuperGlue
- other advanced learned local matching as canonical V1
- native full hyperspectral IIRS processing
- automatic IIRS band-selection research
- advanced sensor-aware routing
- advanced GSD-driven scale search
- automatic reference-pyramid optimization
- advanced multi-scale optimization
- DEM-aware correction
- local/piecewise warping
- dense optical flow as canonical V1 geometry
- confidence calibration
- adaptive matcher selection
- bundle adjustment
- map UI as part of scientific registration
- mosaic generation as a V1 core output

If authoritative V1 scope changes, the scope documents take precedence.

---

## 41. Why the Exclusions Matter

V1 is valuable only if it remains a stable reference point.

If difficult cases are silently rescued using:

- V2 scale logic
- V3 learned correspondence
- V3 retrieval
- V4 refinement
- V4 terrain-aware geometry

while retaining the V1 label, then later benchmark comparisons become meaningless.

The baseline should expose its limitations honestly.

---

## 42. Known V1 Limitations

V1 intentionally uses simple classical methodology.

It may therefore struggle with:

- extreme source/reference GSD differences
- severe illumination changes
- cross-modality appearance
- repetitive crater terrain
- low-feature lunar terrain
- small or partial overlap
- strong terrain relief
- geometry poorly represented by one global affine/homography

A rejection under those conditions may be useful baseline evidence.

It does not automatically indicate an implementation defect.

See [`../project/limitations.md`](../project/limitations.md) for broader scientific limitations.

---

## 43. V1 Limitation Handoff

V1 exists partly to reveal which later capabilities are worth investigating.

### Benchmark V2

May investigate improvements such as:

- sensor-aware preparation
- GSD-aware scale handling
- reference pyramids
- stronger structural representations
- improved derived 2D representations where appropriate

These are later-version research concerns, not hidden V1 dependencies.

---

### Benchmark V3

May investigate:

- advanced or learned local correspondence
- remote-sensing correspondence methods
- optional global retrieval
- global descriptors
- Top-K reference candidates

The local geometry/evaluation responsibilities should remain shared where appropriate.

---

### Benchmark V4

May investigate:

- advanced tie-point refinement
- local/piecewise geometry
- DEM/terrain-aware correction
- uncertainty
- confidence calibration
- stronger rejection logic
- scalable retrieval

These research directions should be measured against simpler configurations rather than backported silently into V1.

---

## 44. No Advanced-Feature Rescue

If canonical V1 fails a difficult case:

```text
V1 Fails
```

the normal benchmark response is:

```text
Record V1 Failure
      ↓
Analyze Why
      ↓
Evaluate Later Method Separately
```

not:

```text
V1 Fails
      ↓
Run LightGlue / Retrieval / DEM Logic
      ↓
Call Result "V1"
```

A baseline must remain a baseline.

---

## 45. V1 Status Language

Keep documentation and implementation status distinct.

| Status               | Meaning                                                     |
| -------------------- | ----------------------------------------------------------- |
| **Specified**        | The V1 methodology/pipeline is documented                   |
| **Implemented**      | The required code exists                                    |
| **Tested**           | Relevant tests were actually executed                       |
| **Domain Validated** | Suitable real lunar data was actually processed             |
| **Benchmarked**      | The canonical V1 scientific benchmark was actually executed |

The existence of this document proves only that the pipeline is **specified**.

It does not prove implementation, testing, lunar validation, or benchmark completion.

---

## 46. Key V1 Pipeline Rules

1. V1 starts from a known overlapping source/reference pair.

2. Global reference discovery has already occurred outside V1.

3. Source and reference roles remain explicit.

4. The source→reference transform direction remains explicit.

5. Input validation occurs before local correspondence processing.

6. Invalid input is different from scientific rejection.

7. V1 representation preparation remains minimal and generic.

8. Native IIRS hyperspectral data is not blindly treated as grayscale.

9. A derived IIRS 2D representation must remain identified as derived.

10. Upsampling does not create new physical terrain detail.

11. Advanced scale search is not a hidden V1 capability.

12. SIFT provides local keypoints and descriptors.

13. RootSIFT, where approved, is a descriptor transformation rather than another detector.

14. Descriptor matching produces candidate matches.

15. Candidate matches are hypotheses, not trusted physical truth.

16. Candidate filtering precedes robust geometry.

17. Source/reference candidate pairing must remain aligned.

18. `(x, y)` and `(row, column)` must not be silently mixed.

19. RANSAC performs robust geometric verification.

20. RANSAC does not create descriptor matches.

21. RANSAC inliers are model-consistent, not independent ground truth.

22. Geometry must be validated before warping.

23. Affine/homography are baseline planar models.

24. Candidate count and verified-inlier count remain separate.

25. Verified inliers should be assessed for useful spatial support.

26. Fit residuals do not automatically measure independent accuracy.

27. Advanced refinement is excluded unless canonical V1 explicitly includes it.

28. If verified tie-point coordinates change, the final transform must be refit.

29. Final evaluation must use the final transform.

30. Warping occurs only after valid final geometry exists.

31. Warp completion does not prove registration accuracy.

32. Independent check points should be used where available for independent accuracy.

33. Missing independent truth limits the claims that can be made.

34. Every reported error requires explicit units and coordinate context.

35. Source-image and reference-image pixel errors are not interchangeable.

36. Ground metres require a scientifically valid conversion.

37. Sub-pixel does not automatically mean sub-metre.

38. Acceptance uses the defined quality evidence, not matcher score alone.

39. RANSAC success alone is not enough for final acceptance.

40. A good-looking overlay is not a scientific success criterion.

41. Rejection is a valid scientific outcome.

42. Identity-transform fallback must not convert failure into fake success.

43. Silent matcher fallback is not canonical V1 behavior.

44. V1 does not include global retrieval.

45. V1 does not include FAISS.

46. V1 does not include learned local matching.

47. V1 does not include advanced sensor-specific routing.

48. V1 does not include DEM/local terrain correction.

49. V1 ends at pair-level scientific result and diagnostics.

50. Mosaic and map generation remain downstream.

51. Pair-level results remain separate from benchmark aggregation.

52. Rejected V1 pairs remain visible in benchmark interpretation.

53. Diagnostic artifacts do not override authoritative metrics/status.

54. Hidden per-pair tuning is not allowed in canonical evaluation.

55. Manual visual correction is not part of canonical automated V1.

56. Benchmark V1 is a research configuration, not software `v1.0.0`.

---

## 47. Related Documents

- [`../project/overview.md`](../project/overview.md) — overall ChandraMap project introduction
- [`../project/problem-statement.md`](../project/problem-statement.md) — scientific problem ChandraMap addresses
- [`../project/goals.md`](../project/goals.md) — intended project outcomes
- [`../project/non-goals.md`](../project/non-goals.md) — project boundaries
- [`../project/assumptions.md`](../project/assumptions.md) — assumptions underlying processing and evaluation
- [`../project/limitations.md`](../project/limitations.md) — scientific and methodological limitations
- [`../project/terminology.md`](../project/terminology.md) — canonical human-facing terminology
- [`../project/v1-scope.md`](../project/v1-scope.md) — human-facing canonical V1 scope
- [`./system-overview.md`](./system-overview.md) — high-level system responsibility boundaries
- [`.ai/context/V1_SCOPE.md`](../../.ai/context/V1_SCOPE.md) — canonical detailed V1 contract
- [`.ai/tasks/V1_IMPLEMENTATION.md`](../../.ai/tasks/V1_IMPLEMENTATION.md) — V1 implementation guidance
- [`.ai/architecture/PIPELINE.md`](../../.ai/architecture/PIPELINE.md) — broader ChandraMap processing pipeline
- [`.ai/architecture/SYSTEM_OVERVIEW.md`](../../.ai/architecture/SYSTEM_OVERVIEW.md) — canonical technical architecture context
- [`.ai/architecture/DATA_FLOW.md`](../../.ai/architecture/DATA_FLOW.md) — scientific data and result movement
- [`.ai/architecture/MODULE_MAP.md`](../../.ai/architecture/MODULE_MAP.md) — repository responsibility mapping
- [`.ai/development/BENCHMARK_RULES.md`](../../.ai/development/BENCHMARK_RULES.md) — benchmark methodology and comparability rules
- [`.ai/development/TESTING_RULES.md`](../../.ai/development/TESTING_RULES.md) — software/scientific testing expectations

Benchmark V1 should remain simple enough to understand completely, strict enough to expose unreliable geometry, and stable enough to serve as the reference point for measuring later ChandraMap research improvements.

<!-- Source specification: :contentReference[oaicite:0]{index=0} -->
