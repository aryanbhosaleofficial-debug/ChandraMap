# Control Points

**Path:** `data/ground_truth/CONTROL_POINTS.md`

This document defines the role and handling of **control points** within the ChandraMap lunar image correspondence and registration workflow.

> **Status:** The available ChandraMap project materials establish the role of reliable control points in geometric registration and clearly distinguish them from independent check/evaluation points. However, they do **not** currently define a complete machine-readable control-point schema, annotation format, coordinate-reference-system specification, point-count requirement, uncertainty model, or storage format. Those details are therefore explicitly marked **Not yet defined** or **Recommended** rather than being presented as implemented functionality.

---

## Table of Contents

- [Purpose](#purpose)
- [Scope](#scope)
- [Project Context](#project-context)
- [What Is a Control Point in ChandraMap?](#what-is-a-control-point-in-chandramap)
- [Why Control Points Are Needed](#why-control-points-are-needed)
- [Control Points in the Registration Pipeline](#control-points-in-the-registration-pipeline)
- [Control Points vs Other Point Types](#control-points-vs-other-point-types)
- [Control Points vs Ground Truth](#control-points-vs-ground-truth)
- [Control Points vs Candidate Correspondences](#control-points-vs-candidate-correspondences)
- [Control Points vs RANSAC Inliers](#control-points-vs-ransac-inliers)
- [Control Points vs Check Points](#control-points-vs-check-points)
- [Control Points vs Transformation Outputs](#control-points-vs-transformation-outputs)
- [Point Lifecycle](#point-lifecycle)
- [Source and Target Representation](#source-and-target-representation)
- [Coordinate Conventions](#coordinate-conventions)
- [Point Identity](#point-identity)
- [Provenance](#provenance)
- [Verification](#verification)
- [Spatial Distribution](#spatial-distribution)
- [Sub-Pixel Refinement](#sub-pixel-refinement)
- [Independence and Leakage Prevention](#independence-and-leakage-prevention)
- [Ground-Truth and Evaluation Design](#ground-truth-and-evaluation-design)
- [Validation and Quality Control](#validation-and-quality-control)
- [Storage and Versioning](#storage-and-versioning)
- [Reproducibility](#reproducibility)
- [Contributor Workflow](#contributor-workflow)
- [Failure Handling](#failure-handling)
- [Recommended Conceptual Schema](#recommended-conceptual-schema)
- [Current Implementation Status](#current-implementation-status)
- [Known Gaps and Limitations](#known-gaps-and-limitations)
- [Control-Point Checklist](#control-point-checklist)
- [Related Documentation](#related-documentation)
- [Source Basis](#source-basis)

---

## Purpose

Control points are a critical part of the ChandraMap registration workflow because a visually plausible alignment is not sufficient to establish that two lunar images have been correctly registered.

The project feedback defines a geometry sequence in which local matches are geometrically verified, reliable points are refined, and a final transformation is estimated:

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
REGISTERED IMAGES
```

The feedback specifically states that sub-pixel refinement should operate on **verified control points** and that flexible warping should only be applied after the control points are accurate and well distributed.

This document establishes the data-management and scientific principles that should govern those points.

---

## Scope

This document covers control points used in the context of:

- Lunar image correspondence.
- Geometric verification.
- Transformation estimation.
- Image registration.
- Sub-pixel refinement.
- Benchmark evaluation.
- Ground-truth preparation where applicable.
- Independent validation.

It does **not** define:

- a specific annotation application,
- a specific file format,
- a specific coordinate reference system,
- exact numerical tolerances,
- a required number of points,
- a specific transformation model,
- a specific RANSAC configuration,
- or a specific storage backend.

Those details are **Not yet defined** by the available ChandraMap project documentation.

---

# Project Context

ChandraMap addresses correspondence and registration between lunar images that may differ in:

- Sensor characteristics.
- Spatial scale.
- Resolution.
- Illumination.
- Viewpoint.
- Image modality.
- Geometric characteristics.

The project materials identify Chandrayaan-2 OHRC, TMC-2, and IIRS as important source imagery and LRO imagery as reference/training data.

The registration system therefore needs a reliable geometric relationship between corresponding image locations.

The project feedback emphasizes that the principal scientific outputs are not merely attractive mosaics, but reliable matched points, transformation/geolocation information, registered imagery, and measurable quality metrics.

---

# What Is a Control Point in ChandraMap?

In the available project feedback, **control points are reliable corresponding locations used to support geometric registration and transformation estimation**.

The feedback specifically states:

> “Sub-pixel refinement should refine verified control points.”

It also states that flexible warping should only be used after control points are accurate and spatially distributed.

Therefore, within the current ChandraMap terminology, a control point represents a correspondence between locations in the source and reference/target imagery that is considered sufficiently reliable for geometric processing.

Conceptually:

```text
Source Image                    Reference / Target Image
     │                                  │
     │      Corresponding location      │
     └──────────────┬───────────────────┘
                    │
                    ▼
              Control Point
```

### Important distinction

A control point is **not automatically synonymous with ground truth**.

The available project materials do not define a formal rule stating that every control point must originate from a ground-truth annotation.

They do, however, clearly distinguish reliable points used for transformation estimation from independent check points used to evaluate the transformation.

---

# Why Control Points Are Needed

A correspondence system can produce many candidate matches without producing a reliable geometric registration.

The project feedback explicitly warns that:

- matcher confidence is not proof of geometric correctness;
- RANSAC should determine which candidates become verified inliers;
- spatial distribution matters;
- evaluating a transformation on the same points used to fit it can make the result look artificially good.

Control points therefore provide the geometric foundation for:

1. Estimating a transformation.
2. Inspecting geometric consistency.
3. Refining reliable correspondences.
4. Re-estimating the final transformation.
5. Supporting registration.
6. Enabling subsequent independent evaluation.

---

# Control Points in the Registration Pipeline

The current project feedback supports the following conceptual sequence:

```text
Source / Reference Images
          │
          ▼
     Local Matching
          │
          ▼
   Candidate Matches
          │
          ▼
 RANSAC / Geometric Verification
          │
          ▼
     Verified Inliers
          │
          ▼
   Reliable Control Points
          │
          ▼
  Sub-Pixel Refinement
          │
          ▼
 Refined Control / Tie Points
          │
          ▼
   Final Transformation
          │
          ▼
      Registration
          │
          ▼
 Independent Check Points
          │
          ▼
      Evaluation
```

The exact distinction between the terms **verified inlier**, **control point**, and **sub-pixel tie point** is not formally standardized in the current repository documentation.

The operational distinction supported by the feedback is:

- Candidate matches are proposed correspondences.
- RANSAC identifies geometrically consistent inliers.
- Reliable control/tie points are refined and used for transformation estimation.
- Independent check points are reserved for evaluation.

---

# Control Points vs Other Point Types

These terms must not be casually interchanged.

| Point / artifact         | Role                                                           |      Used to fit transformation? |     Used for independent evaluation? |
| ------------------------ | -------------------------------------------------------------- | -------------------------------: | -----------------------------------: |
| Candidate correspondence | Proposed image correspondence                                  |   Potentially after verification |                        No, by itself |
| RANSAC inlier            | Candidate consistent with estimated geometric model            | Yes, depending on pipeline stage |                        No, by itself |
| Control point            | Reliable correspondence used to support geometric registration |                              Yes | No, when serving as fit/control data |
| Refined tie point        | More precisely localized reliable correspondence               |                              Yes |            No, when used for fitting |
| Ground-truth point       | Reference information for correctness                          |              Depends on protocol |   Yes, when used as evaluation truth |
| Independent check point  | Point deliberately withheld from fitting                       |                               No |                                  Yes |
| Transformation           | Estimated geometric relationship                               |                                — |   Evaluated using independent points |

The exact repository-level schema for these artifacts is **Not yet defined**.

---

# Control Points vs Ground Truth

This is one of the most important distinctions in the ChandraMap data model.

## Ground Truth

Ground truth is information regarded as the reference against which algorithmic results are evaluated.

The available materials state that challenge ground truth should be used when available. Otherwise, independently checked tie points can be retained as check points.

## Control Points

Control points are points used to establish or refine the geometric relationship between images.

They may originate from:

- verified algorithmic correspondences,
- externally established reference information,
- or another project-defined source.

The currently available documentation does **not** specify a complete provenance rule that requires all control points to originate from ground truth.

Therefore:

```text
Ground Truth
    │
    ├── May provide evaluation reference
    │
    └── May, depending on protocol, provide
        points suitable for control/fitting

Algorithmic Correspondences
    │
    └── May become reliable control points
        after geometric verification
```

These paths must not be silently conflated.

---

# Control Points vs Candidate Correspondences

Candidate correspondences are proposed matches.

For example:

```text
Source feature
      │
      ▼
Matching algorithm
      │
      ▼
Candidate correspondence
```

A candidate correspondence has not necessarily passed geometric verification.

The project explicitly recommends renaming "High Confidence Matches" to **Candidate Matches**, because a matcher confidence score does not prove geometric correctness. RANSAC/geometric verification should decide which candidates become verified inliers.

Therefore:

```text
Candidate Match ≠ Control Point
```

unless the candidate has passed the project-defined verification process and is accepted for geometric use.

---

# Control Points vs RANSAC Inliers

RANSAC is used to identify correspondences that are consistent with a geometric model.

The project feedback describes:

```text
Candidate Matches
       ↓
RANSAC + Initial Model
       ↓
Inliers
       ↓
Sub-Pixel Refinement
       ↓
Refit Final Transform
```

A RANSAC inlier is therefore a **geometrically verified correspondence** under the particular model and RANSAC procedure used.

A control point is a **role in the registration process**: a reliable point used for geometric control/transformation estimation.

In a concrete implementation, the two sets may overlap heavily or even be represented by the same records.

However:

> **The terms should not be treated as formally identical unless the implemented benchmark specification explicitly defines them that way.**

The current repository does not provide such a formal equivalence rule.

---

# Control Points vs Check Points

This distinction is explicitly supported by the project materials.

The registration feedback states:

> Do not fit and judge on exactly the same points.

It recommends using challenge ground truth where available or retaining independently checked tie points as check points and **not using them to fit the transformation**.

Therefore:

```text
                 Point Set
                    │
          ┌─────────┴─────────┐
          │                   │
          ▼                   ▼
    Control / Fit         Check / Evaluation
       Points                  Points
          │                   │
          ▼                   │
   Estimate Transform          │
          │                   │
          └─────────┬─────────┘
                    ▼
              Compare Against
              Independent Points
```

### Control points

Used to estimate or refine the transformation.

### Check points

Held out from fitting and used to measure registration accuracy.

This separation is essential for preventing overly optimistic evaluation.

---

# Control Points vs Transformation Outputs

A control point is an **input to transformation estimation**.

A transformation is the **estimated relationship produced from those inputs**.

Conceptually:

```text
Control Points
      │
      ▼
Transformation Estimation
      │
      ▼
Transformation
      │
      ▼
Registered Image
```

The transformation must not be stored or described as if it were itself ground truth.

Similarly:

```text
Control Points ≠ Transformation
Transformation ≠ Ground Truth
Registration Result ≠ Ground Truth
```

unless an explicit external reference establishes such a relationship.

---

# Point Lifecycle

A ChandraMap point can conceptually progress through the following lifecycle:

```text
1. Candidate
      │
      ▼
2. Geometrically Verified
      │
      ▼
3. Accepted for Control
      │
      ▼
4. Locally Refined
      │
      ▼
5. Used in Final Transformation
```

An independent evaluation point follows a different path:

```text
Independent Reference Point
          │
          ▼
      Check Point
          │
          ▼
Evaluate Final Transformation
```

The two paths must not be merged during evaluation.

---

# Source and Target Representation

A control point represents corresponding positions in two images.

Conceptually:

```text
Source Image                    Target / Reference Image

      P_source                         P_target
          ●                                ●
          │                                │
          └──────── Correspondence ────────┘
```

The project documentation uses source-image pixels as the primary unit for sub-pixel registration evaluation. Conversion to metres is only meaningful when the relevant GSD and projection information support that conversion.

### Current schema status

The repository does **not** currently confirm a formal field-level representation such as:

```text
source_x
source_y
target_x
target_y
```

Those names should therefore be treated as **conceptual examples only**, not as an implemented schema.

---

# Coordinate Conventions

Coordinate conventions are critical for scientific reproducibility.

However, the current ChandraMap materials do **not** formally specify:

- zero-based vs one-based pixel indexing,
- pixel-center vs pixel-corner interpretation,
- axis direction,
- row/column vs x/y storage order,
- image-origin convention,
- CRS identifier,
- projection,
- geographic coordinate format,
- or a canonical coordinate serialization.

Therefore these are:

> **Not yet defined.**

They should be formally specified before control-point data is exchanged between independent tools.

---

## Recommended Coordinate Documentation

Until a formal project schema is established, every control-point dataset should document, where applicable:

- Image identifier.
- Source image coordinate system.
- Target/reference image coordinate system.
- Pixel indexing convention.
- Axis order.
- Pixel-center interpretation.
- Map-projection information if coordinates are map-based.
- Units.
- Relevant GSD information.

These are **Recommended metadata requirements**, not claims about the current implementation.

---

# Source-Image Pixel Accuracy

The project specifically emphasizes that sub-pixel accuracy should be reported in **source-image pixels first**.

Conversion into metres should only be performed when:

- the relevant product GSD is known,
- and the projection/geospatial relationship makes that conversion meaningful.

This is important because:

```text
0.2 source pixels
```

does not represent the same physical ground distance for every sensor.

The project feedback explicitly notes this distinction for different Chandrayaan-2 sensors.

---

# Point Identity

Every persistent control point should have a stable identity.

However, the project currently does **not** define:

- a required identifier syntax,
- a UUID system,
- a naming convention,
- or a globally unique point-ID scheme.

Therefore:

> **Formal point identity rules are Not yet defined.**

### Recommended principle

A point identifier should remain stable when the point is referenced by:

- validation,
- refinement,
- transformation fitting,
- diagnostic plots,
- benchmark reports,
- or provenance records.

Do not reuse one identifier for different physical/image correspondences.

---

# Provenance

Control-point provenance is essential.

For each persisted control-point record, the project should eventually be able to establish:

```text
Where did the point originate?
          ↓
Which source image?
          ↓
Which target/reference image?
          ↓
How was the correspondence obtained?
          ↓
How was it verified?
          ↓
Was it refined?
          ↓
Was it used for fitting?
          ↓
Was it reserved for evaluation?
```

The exact provenance schema is **Not yet defined**.

---

## Provenance Categories

A future control-point dataset may need to distinguish between points originating from:

- Algorithmic matching.
- Manual/independent verification.
- Existing ground truth.
- External reference data.
- Sub-pixel refinement.

The actual annotation and verification methods used by ChandraMap are **Not confirmed**.

---

# Verification

A control point should not be considered reliable merely because:

- a descriptor matcher produced it,
- its descriptor distance is low,
- a model accepted it,
- or it appears visually plausible.

The project explicitly requires geometric verification and emphasizes RANSAC as the first robust filtering stage.

The conceptual sequence is:

```text
Candidate Correspondence
          ↓
Geometric Verification
          ↓
Reliable Inlier
          ↓
Control / Tie Point
```

The exact acceptance criteria are **Not yet defined** at the control-point file level.

---

# RANSAC and Control-Point Verification

The project feedback identifies RANSAC as a robust geometric verification mechanism.

Its role is to:

- estimate an initial geometric relationship,
- reject inconsistent candidate correspondences,
- identify geometrically consistent inliers.

The exact:

- model,
- iteration count,
- threshold,
- confidence,
- sampling configuration,
- or implementation

is **Not specified** in this document.

Do not add numerical RANSAC parameters to a control-point file unless they are defined by the benchmark or implementation.

---

# Spatial Distribution

A large number of control points is not automatically better.

The project explicitly requires spatial coverage to be measured because points concentrated around one feature can provide weak geometric control over the full overlap.

Conceptually:

```text
Poor distribution:

┌─────────────────────────┐
│                         │
│       ● ● ● ●           │
│       ● ● ● ●           │
│                         │
│                         │
└─────────────────────────┘


Better distributed:

┌─────────────────────────┐
│ ●                   ●   │
│                         │
│        ●      ●         │
│                         │
│ ●                   ●   │
└─────────────────────────┘
```

The project identifies **grid coverage** and **convex-hull coverage** as possible measures of spatial distribution.

The exact coverage implementation is governed by the benchmark specification and is not formally defined by this file.

---

# Control Points and Flexible Warping

The project feedback gives an important warning:

> A flexible warp can make an overlay look good even when the correspondences are weak.

It recommends using flexible warping only after control points are:

- accurate,
- reliable,
- and well distributed.

Therefore:

```text
Weak Control Points
        ↓
Flexible Warp
        ↓
Visually Good Overlay
        ≠
Scientifically Valid Registration
```

Control-point quality must be established before allowing a more flexible transformation to compensate for correspondence errors.

---

# Sub-Pixel Refinement

The project explicitly places sub-pixel refinement **after reliable geometric verification**.

The recommended sequence is:

```text
Candidate Matches
       ↓
RANSAC
       ↓
Verified Inliers
       ↓
Local Sub-Pixel Refinement
       ↓
Refit Final Transformation
```

The project feedback describes possible patch-based correlation/phase-based refinement or planetary registration tooling as examples, but no particular refinement implementation is established as the current ChandraMap implementation.

Therefore:

- **Sub-pixel refinement:** Specified conceptually.
- **Specific algorithm:** Not confirmed.
- **Specific implementation:** Not confirmed.
- **Numerical precision target:** Not specified.

---

# Independence and Leakage Prevention

Control points and evaluation points must be separated when evaluating transformation accuracy.

The project feedback explicitly warns against fitting and evaluating on exactly the same points because the resulting error can appear better than the actual registration quality.

### Required conceptual separation

```text
                Available Reference Points
                         │
             ┌───────────┴───────────┐
             │                       │
             ▼                       ▼
       Control / Fit             Check / Eval
          Points                    Points
             │                       │
             ▼                       │
     Estimate Transform              │
             │                       │
             └───────────┬───────────┘
                         ▼
                Evaluate Transform
```

### Leakage Rule

A point used to estimate the transformation should not also be presented as an independent check point for that same transformation.

---

# Ground-Truth and Evaluation Design

The project materials establish the following evaluation principle:

> **Do not fit and judge on exactly the same points.**

Where challenge ground truth exists, it should be used according to the benchmark's ground-truth protocol.

Where appropriate independent tie points are available, some can be held out as check points.

The registration metric identified by the project is:

**Check-point RMSE in source-image pixels**

This measures the final transformation on points that were not used to fit it.

---

## Evaluation Flow

```text
Control Points
      │
      ▼
Transformation Estimation
      │
      ▼
Final Transformation
      │
      │
      └──────────────┐
                     │
                     ▼
              Independent
              Check Points
                     │
                     ▼
             Registration Error
```

This is fundamentally different from:

```text
Control Points
      │
      ▼
Transformation
      │
      ▼
Evaluate on same Control Points
```

The second approach can produce overly optimistic fit error and should not be used as the sole evidence of registration accuracy.

---

# Ground Truth Does Not Automatically Mean Control

A ground-truth dataset may contain points used for evaluation.

Those points should not automatically become control points.

For example:

```text
Ground-Truth Dataset
        │
        ├── Points used for fitting, if the
        │   benchmark protocol permits
        │
        └── Independent check points
              used only for evaluation
```

The exact partitioning rules are **Not yet defined** in the available control-point documentation.

The benchmark's formal ground-truth protocol should take precedence once established.

---

# Validation and Quality Control

Control-point data should be validated before being used for scientific registration.

## Identity Validation

Confirm:

- Source image identity.
- Target/reference image identity.
- Point identity.
- Point role.

---

## Coordinate Validation

Verify that:

- Coordinates fall within the relevant image domain.
- Source and target coordinates correspond to the intended images.
- The coordinate convention is documented.
- Units are known.
- Any map/geospatial coordinate interpretation is documented.

The exact coordinate validation rules are **Not yet defined**.

---

## Geometric Validation

Where applicable, verify that the points:

- are consistent with the intended geometry,
- survive the defined geometric verification stage,
- do not obviously represent unrelated terrain,
- and provide meaningful spatial distribution.

---

## Independence Validation

For evaluation:

- Confirm that check points were not used for transformation fitting.
- Confirm that evaluation points were not accidentally included in the control-point fitting set.
- Preserve enough provenance to demonstrate this separation.

---

## Distribution Validation

Inspect the spatial distribution of accepted control points.

The project specifically recommends reporting spatial coverage rather than relying only on match counts.

---

# Quality Is More Important Than Point Count

The project feedback explicitly warns:

> More matches are not better if they are wrong or clustered.

Therefore, a control-point dataset should not be judged solely by the number of points it contains.

A useful control set should instead be considered in terms of:

- correctness,
- geometric consistency,
- spatial distribution,
- localization quality,
- and suitability for the intended transformation.

---

# Sensor and Scale Considerations

Control-point quality cannot be interpreted independently of the imagery from which the points were derived.

ChandraMap may compare imagery from sensors with substantially different characteristics.

The project feedback identifies:

- OHRC,
- TMC-2,
- IIRS,
- and LRO reference imagery

as important components of the system.

### Scale

The project explicitly warns that upsampling does not recover missing spatial information.

Reference imagery should be brought toward a comparable effective scale before coarse matching, with refinement only where the source contains sufficient detail.

This matters for control points because a claimed sub-pixel point should not be interpreted as evidence of physical accuracy beyond what the source imagery can support.

---

# Illumination Considerations

Lunar Sun-angle changes can alter shadow geometry, not merely image brightness.

The project therefore recommends testing terrain structure, edges, gradients, and related representations rather than assuming brightness normalization makes two lunar images equivalent.

A control point should therefore be evaluated in the context of the imagery from which it was obtained.

A point that appears visually strong under one illumination condition may require additional verification under a substantially different Sun angle.

---

# Coordinate and Physical Accuracy

ChandraMap's primary sub-pixel reporting unit is the **source-image pixel**.

Only when GSD and projection make the conversion meaningful should error be expressed in metres.

Therefore, a control-point dataset should not contain an assumed physical uncertainty in metres unless that uncertainty is supported by the actual product geometry and project protocol.

### Not specified

The following are currently not formally defined:

- Control-point physical uncertainty.
- Coordinate uncertainty model.
- Geolocation uncertainty.
- Required ground accuracy.
- Universal pixel-to-metre conversion.

---

# Versioning

Control-point data is scientific reference data and should be versioned carefully.

The project materials do not define a specific data-versioning system.

Therefore:

- **Version-control mechanism:** Not specified.
- **Point-dataset version numbering:** Not yet defined.
- **Checksum policy:** Not specified.
- **Annotation history schema:** Not specified.

### Recommended principle

A change to control-point coordinates, point membership, point roles, or provenance should be treated as a meaningful data change.

Do not silently overwrite a previously used control-point dataset.

---

# Storage

The exact storage format for control points is **Not yet defined**.

Possible future representations may include structured tabular or machine-readable formats, but no specific format should be assumed from this document.

The repository should eventually define:

- schema,
- serialization format,
- coordinate convention,
- metadata requirements,
- versioning,
- validation,
- and provenance.

Until then, `CONTROL_POINTS.md` serves as a **technical specification and terminology document**, not as evidence of an implemented control-point file schema.

---

# Recommended Conceptual Schema

The following is a **recommended conceptual model**, not an implemented ChandraMap schema.

```text
Control Point
├── Point Identity
├── Source Image Identity
├── Target / Reference Image Identity
├── Source Location
├── Target Location
├── Point Role
├── Provenance
├── Verification State
├── Refinement State
└── Dataset / Benchmark Context
```

Potential conceptual roles include:

```text
candidate
verified_inlier
control
refined_tie_point
check_point
```

These values are **illustrative role concepts**, not confirmed serialized enum values.

---

## Recommended Metadata Questions

A future formal schema should answer:

| Question                                 | Status                          |
| ---------------------------------------- | ------------------------------- |
| Which source image?                      | Required conceptually           |
| Which target/reference image?            | Required conceptually           |
| Which point?                             | Required conceptually           |
| Where is it in source image coordinates? | Required conceptually           |
| Where is it in target image coordinates? | Required conceptually           |
| What coordinate convention is used?      | Not yet defined                 |
| How was the point generated?             | Recommended                     |
| How was it verified?                     | Recommended                     |
| Was it used for fitting?                 | Required for leakage prevention |
| Is it an independent check point?        | Required for evaluation         |
| Was it sub-pixel refined?                | Recommended                     |
| Which benchmark/dataset version?         | Recommended                     |
| What is its provenance?                  | Recommended                     |

---

# Control-Point State

A future implementation may need to track point state separately from point identity.

Conceptually:

```text
Candidate
   ↓
Verified
   ↓
Accepted as Control
   ↓
Sub-Pixel Refined
   ↓
Used in Final Fit
```

An evaluation point should instead remain outside this fitting path:

```text
Independent Point
       ↓
Check Point
       ↓
Evaluation
```

The exact state machine is **Not yet implemented/defined**.

---

# Failure and Rejection

Rejected correspondences should not automatically be deleted from the scientific record.

The project feedback recommends retaining difficult cases and showing where the system fails.

A future experiment record may therefore distinguish:

```text
Candidate
   ├── Accepted
   │     └── Control / Refined
   │
   └── Rejected
         ├── Geometric inconsistency
         ├── Insufficient confidence
         ├── Poor localization
         └── Other documented reason
```

The exact rejection taxonomy is **Not yet defined**.

---

# Control Points and Residual Analysis

A transformation can appear visually successful while still exhibiting systematic geometric error.

The project feedback recommends inspecting residual vectors across the image.

If residuals change systematically from one side of the image to another, the project suggests investigating whether a local/piecewise model or sensor/geometry information is needed.

Therefore, control-point analysis should not end after obtaining a transformation.

Conceptually:

```text
Control Points
      ↓
Transformation
      ↓
Residuals
      ↓
Spatial Residual Inspection
      ↓
Model Adequacy Assessment
```

The exact residual-analysis implementation is **Not yet defined**.

---

# Transformation Models

The registration feedback describes **affine or homography** as reasonable initial models for a local, already map-projected pair, while warning that lunar terrain is not a flat surface and that one global transform may not always be sufficient.

Therefore:

- Affine transformation: documented as a possible initial model.
- Homography: documented as a possible initial model.
- Local/piecewise refinement: identified as a possible response to systematic residuals.
- Sensor geometry/DEM-assisted geometry: identified as a possible consideration.

None of these should be interpreted as a universally selected ChandraMap transformation model.

---

# Contributor Workflow

Contributors working with control points should follow this sequence.

## 1. Identify the Images

Record the source and target/reference image identities.

---

## 2. Establish the Point Role

Determine whether the point is:

- a candidate,
- verified,
- a control point,
- a refined tie point,
- or an independent check point.

Do not use ambiguous terminology.

---

## 3. Preserve Provenance

Record how the point was obtained or verified whenever the implementation supports such metadata.

---

## 4. Verify Geometry

Do not promote a candidate correspondence into a control point merely because the local appearance is convincing.

Use the project's defined geometric verification process.

The current project feedback identifies RANSAC as the baseline robust verification stage.

---

## 5. Check Spatial Distribution

Do not accept a control set solely because it contains many points.

Inspect whether the points provide meaningful coverage across the overlap.

---

## 6. Refine When Required

If sub-pixel registration is being evaluated, refinement should occur after reliable inliers have been established.

The project explicitly recommends refining verified control points and then refitting the final transformation.

---

## 7. Protect Evaluation Points

Ensure independent check points are not accidentally included in transformation fitting.

---

## 8. Record the Dataset Version

If a formal dataset version exists, record it.

If not:

> **Dataset version: Not yet defined.**

Do not invent a version identifier.

---

# Leakage Prevention Checklist

Before reporting registration accuracy:

- [ ] Control points used for fitting are identified.
- [ ] Independent check points are identified.
- [ ] Check points were not used to fit the reported transformation.
- [ ] Ground-truth information has not been replaced by model predictions.
- [ ] The same points are not being described as both fit points and independent evaluation points.
- [ ] Point provenance is available.
- [ ] The benchmark pair is known.
- [ ] The transformation was produced without using withheld evaluation information.

---

# Reproducibility

A reproducible control-point dataset should allow another researcher to understand:

```text
Which images?
     ↓
Which points?
     ↓
How obtained?
     ↓
How verified?
     ↓
Which points fitted the model?
     ↓
Which points evaluated it?
     ↓
Which transformation was estimated?
     ↓
What error was measured?
```

The project feedback recommends preserving measurable outputs including:

- match plots,
- rejected outliers,
- registered overlays,
- inlier statistics,
- and check-point error

for the initial end-to-end milestone.

---

# Benchmark Reproducibility

When comparing algorithms, the project recommends running the same test pairs through:

1. SIFT baseline.
2. A stronger matcher.
3. The full sensor-aware and multi-scale pipeline.

Control-point handling should therefore remain consistent with the benchmark protocol across methods.

A method should not receive a different evaluation-point set simply because its matching output is different.

---

# Control Points in the V1 Benchmark

The V1 benchmark architecture emphasizes:

- reliable correspondences,
- geometric verification,
- registration,
- spatial distribution,
- independent evaluation,
- and reproducibility.

The control-point layer should support these goals without changing the benchmark definition.

The exact V1 control-point file schema is **Not confirmed** in the currently available project materials.

Therefore this document should be treated as the data-level specification for control-point concepts until the benchmark's formal ground-truth protocol establishes more precise rules.

---

# Current Implementation Status

| Component                                                | Status                     |
| -------------------------------------------------------- | -------------------------- |
| Control points as reliable geometric registration points | **Documented**             |
| Control points used for transformation estimation        | **Documented**             |
| Control-point refinement after reliable verification     | **Specified conceptually** |
| RANSAC before refinement                                 | **Specified conceptually** |
| Spatially distributed control points                     | **Specified**              |
| Independent check points                                 | **Specified**              |
| Check-point evaluation                                   | **Specified**              |
| Source-pixel error as primary sub-pixel unit             | **Specified**              |
| Formal control-point schema                              | **Not yet defined**        |
| Formal point-ID scheme                                   | **Not yet defined**        |
| Formal coordinate convention                             | **Not yet defined**        |
| Formal uncertainty model                                 | **Not specified**          |
| Formal annotation workflow                               | **Not confirmed**          |
| Formal control-point file format                         | **Not confirmed**          |
| Formal versioning mechanism                              | **Not specified**          |
| Automated validation tooling                             | **Not confirmed**          |
| Automated leakage detection                              | **Not confirmed**          |
| Formal rejection taxonomy                                | **Not yet defined**        |

---

# Known Gaps and Limitations

The available project documentation does not currently establish:

### Coordinate Specification

- Pixel indexing convention.
- Pixel-center convention.
- Axis order.
- CRS.
- Projection.
- Geographic-coordinate schema.

### Point Schema

- Required fields.
- Field names.
- Point-ID syntax.
- Data types.
- Serialization format.

### Uncertainty

- Point localization uncertainty.
- Geolocation uncertainty.
- Annotation uncertainty.
- Required accuracy tolerance.

### Annotation

- Annotation software.
- Human annotation protocol.
- Independent annotator requirements.
- Verification procedure.

### Dataset Definition

- Exact control-point dataset.
- Number of control points.
- Number of image pairs.
- Required spatial distribution.
- Dataset version.

### Evaluation

- Exact train/control/check partition protocol.
- Exact RMSE implementation.
- Exact acceptance thresholds.

These gaps should be resolved by the formal benchmark/ground-truth implementation rather than guessed in this document.

---

# Important Scientific Rules

## Rule 1 — Control Does Not Automatically Mean Truth

A control point may be an algorithmically derived and geometrically verified correspondence.

Do not call it ground truth without an independent basis.

---

## Rule 2 — Candidate Does Not Mean Control

A candidate correspondence must pass the appropriate verification process before being treated as reliable geometric control.

---

## Rule 3 — Inlier Does Not Automatically Mean Independent Evaluation

A RANSAC inlier is part of model fitting and should not simultaneously serve as an independent check point for that model.

---

## Rule 4 — Check Points Must Be Held Out

Independent check points should not be used to estimate the transformation being evaluated.

---

## Rule 5 — More Points Are Not Automatically Better

Incorrect or spatially clustered points can produce weak geometric control even when their count is high.

---

## Rule 6 — Refinement Comes After Verification

Sub-pixel refinement should operate on reliable verified points rather than blindly refining every candidate correspondence.

---

## Rule 7 — Residuals Matter

A visually attractive registration does not prove that the geometric model is correct.

Inspect residual behavior and spatial distribution.

---

## Rule 8 — Preserve Provenance

A control point without known origin, image identity, and role is difficult to reproduce or audit.

---

## Rule 9 — Do Not Overclaim Physical Accuracy

Source-image pixel accuracy should be reported first.

Conversion to metres requires meaningful GSD and projection information.

---

## Rule 10 — Keep Failures

Rejected and difficult correspondences can reveal weaknesses in:

- scale handling,
- illumination handling,
- sensor modality,
- geometric modelling,
- or matching.

They should not be silently removed from scientific analysis.

---

# Control-Point Checklist

Before accepting a control-point dataset for ChandraMap:

### Identity

- [ ] Source image identified.
- [ ] Target/reference image identified.
- [ ] Point identity defined.
- [ ] Point role defined.

### Coordinates

- [ ] Source coordinates recorded.
- [ ] Target coordinates recorded.
- [ ] Coordinate convention documented.
- [ ] Units documented.
- [ ] Coordinate system documented where applicable.

### Provenance

- [ ] Point origin documented.
- [ ] Generation/annotation method documented where applicable.
- [ ] Verification state documented.
- [ ] Dataset/benchmark context documented.

### Geometry

- [ ] Candidate matches have been geometrically verified where required.
- [ ] Control points are suitable for the intended transformation.
- [ ] Spatial distribution has been inspected.
- [ ] Residual behavior has been inspected where applicable.

### Refinement

- [ ] Sub-pixel refinement is performed only after reliable verification.
- [ ] Refinement state is distinguishable from initial correspondence state.

### Evaluation

- [ ] Control points are separated from independent check points.
- [ ] Check points were not used for fitting.
- [ ] Evaluation uses the intended benchmark protocol.

### Reproducibility

- [ ] Dataset identity is recorded.
- [ ] Provenance is preserved.
- [ ] Version information is recorded when available.
- [ ] No undocumented local dependency exists.

---

# Recommended Future Control-Point Specification

Before ChandraMap scales to a larger benchmark, the repository should formally define:

1. **Control-point schema**
2. **Point-ID convention**
3. **Source/target coordinate convention**
4. **Pixel indexing convention**
5. **Pixel-center convention**
6. **Coordinate-reference-system policy**
7. **Point provenance fields**
8. **Verification-state fields**
9. **Control/check partition rules**
10. **Spatial-coverage definition**
11. **Sub-pixel refinement representation**
12. **Uncertainty representation**
13. **Versioning mechanism**
14. **Validation rules**
15. **Serialization format**
16. **Benchmark integration rules**
17. **Leakage-prevention checks**

Until these are formally implemented, they should remain explicitly marked as **Not yet defined** rather than being inferred.

---

# Related Documentation

This document should be interpreted together with the project's data and benchmark documentation, where those files are confirmed in the repository:

- `data/README.md`
- `data/interim/README.md`
- `data/ground_truth/README.md`
- `benchmarks/README.md`
- `benchmarks/v1/README.md`
- `benchmarks/v1/BENCHMARK_SPEC.md`
- `benchmarks/v1/GROUND_TRUTH_PROTOCOL.md`
- `benchmarks/v1/METRICS.md`
- `benchmarks/v1/STRESS_TESTS.md`
- `benchmarks/v1/ACCEPTANCE_CRITERIA.md`
- `benchmarks/v1/REPRODUCIBILITY.md`
- `benchmarks/baselines/README.md`
- `benchmarks/baselines/sift/README.md`
- `benchmarks/baselines/ground_truth/README.md`
- `benchmarks/baselines/expected/README.md`

The formal benchmark and ground-truth specifications should take precedence if they define a more specific control-point protocol.

---

# Source Basis

This document is grounded in the ChandraMap project materials supplied for the repository.

## `Aryan_Lunar_Image_Registration_Feedback.pdf`

The registration feedback explicitly establishes:

- RANSAC-based geometric verification.
- Reliable inliers before refinement.
- Sub-pixel refinement of verified control points.
- Final transformation estimation after refinement.
- The need for accurate and spatially distributed control points.
- The separation of transformation-fitting points from independent check points.
- Check-point RMSE as an evaluation measure.
- Source-image pixels as the primary sub-pixel error unit.

It also warns that flexible warping can hide poor correspondences and should only be used after control points are accurate and well distributed.

## `Aryan_SIH_26166_Lunar_Image_Correspondence_Feedback.pptx`

The technical feedback establishes the broader ChandraMap requirements around:

- sensor-aware processing,
- multi-scale correspondence,
- illumination changes,
- spatially distributed matches,
- geometric verification,
- sub-pixel refinement,
- and measurable evaluation.

It also explicitly distinguishes candidate matching from geometric verification and recommends retaining interpretable outputs such as match points, transformations, residuals, inlier statistics, coverage, and registered previews.

## `SIH26166 Silarlar PS.pdf`

The project problem statement identifies the relevant lunar imagery and cross-sensor nature of the correspondence task, including Chandrayaan-2 OHRC, TMC-2, IIRS, and LRO reference imagery.

---

# Final Definition

For the current ChandraMap architecture:

> **A control point is a reliable corresponding image location used to provide geometric control for transformation estimation and registration.**

The project materials distinguish this role from **independent check/evaluation points**, which should not be used to fit the transformation being evaluated.

The complete machine-readable control-point schema, coordinate convention, annotation protocol, uncertainty model, versioning mechanism, and storage format remain **Not yet defined**.

The governing principle is therefore:

```text
Candidate Correspondence
          ↓
Geometric Verification
          ↓
Reliable Control Point
          ↓
Optional Sub-Pixel Refinement
          ↓
Transformation Estimation
          ↓
Registration
          ↓
Independent Check Points
          ↓
Quantitative Evaluation
```

This separation keeps **correspondence**, **geometric control**, **ground truth**, **transformation**, and **evaluation** scientifically distinguishable and prevents a visually convincing registration from being mistaken for independently validated accuracy.
