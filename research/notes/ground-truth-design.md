# Ground-Truth Design for ChandraMap

> **Research note:** `research/notes/ground-truth-design.md`
> **Status:** Foundational research note
> **Scope:** Ground-truth design, control/check-point separation, registration evaluation, benchmark construction, uncertainty, and reproducibility
> **Project principle:** **Build small. Measure honestly. Keep the failures.**

---

## 1. Overview

ChandraMap is a lunar image correspondence and registration system intended to establish reliable correspondences between images of the same lunar region and estimate a defensible geometric relationship between them.

A registration result is only scientifically useful if there is a credible way to determine whether the estimated alignment is actually correct.

This creates a fundamental evaluation question:

> **How do we know whether a lunar image registration result is actually correct?**

The answer cannot rely only on:

- the visual appearance of an overlay,
- the number of matches,
- the confidence score of a matcher,
- the residuals on points used to fit the transformation,
- or the apparent quality of the registered image.

ChandraMap therefore needs a distinction between:

1. **correspondences used to estimate a transformation**, and
2. **independent reference information used to evaluate that transformation**.

The supplied project feedback makes this distinction explicit: a transformation should not be fitted and judged on exactly the same points. Where challenge ground truth is available, it should be used; otherwise, independently checked tie points should be retained as check points and excluded from transformation fitting.

This note defines the conceptual framework for doing that without claiming that a final ChandraMap ground-truth dataset already exists.

---

# 2. Core Principle

The central evaluation principle is:

> **A registration result should not be judged only by the transformation estimated from the same correspondences used to fit it.**

The basic flow is:

```text
Candidate Correspondences
        ↓
Transformation Fitting
        ↓
Estimated Registration
        ↓
Independent Ground Truth
        ↓
Registration Error
```

A more complete ChandraMap evaluation flow is:

```text
Source Image + Reference Image
              ↓
       Candidate Matches
              ↓
      Geometric Verification
              ↓
        Verified Inliers
              ↓
       Transformation Fit
              ↓
        Estimated Model
              ↓
    Independent Check Points
              ↓
       Registration Error
              ↓
      Residual / Failure Analysis
```

The critical separation is:

```text
FIT
≠
EVALUATE
```

---

# 3. What Ground Truth Means in ChandraMap

In this project, **ground truth** means independently established reference information against which a correspondence or registration result can be evaluated.

Depending on the benchmark design, that reference information may take different forms.

Examples include:

- known corresponding image coordinates,
- independently checked tie points,
- challenge-provided correspondence information,
- independently established geospatial coordinates,
- trusted reference products,
- or another validated reference relationship.

The exact final ChandraMap ground-truth source is:

`[TBD]`

No existing coordinate set, checkpoint count, annotation process, or accuracy value should be assumed unless it is explicitly defined by the benchmark or source dataset.

---

# 4. Ground Truth Is Not the Same as Model-Fit Points

This distinction is fundamental.

## Model-fit points

Points used to estimate the transformation.

For example:

```text
Verified Inliers
       ↓
Fit affine / homography
       ↓
Estimated transformation
```

## Check points

Points withheld from transformation fitting and used to evaluate the estimated transformation.

```text
Independent Check Points
       ↓
Apply estimated transformation
       ↓
Compare prediction with reference
       ↓
Registration error
```

The same physical feature may be conceptually related to both sets, but the point used for evaluation must not leak into the fitting process.

---

# 5. Why Fitting and Evaluation Must Be Separated

Suppose a transformation is fitted using:

```text
P1, P2, P3, P4, P5
```

and then evaluated on:

```text
P1, P2, P3, P4, P5
```

The measured error can be artificially favorable because the transformation was explicitly optimized to explain those points.

This is not the same as asking:

> "Does this transformation correctly predict the location of an independently observed point?"

The project feedback explicitly identifies this issue and recommends independent check points for registration evaluation.

---

# 6. Control Points, Tie Points, Check Points, and Ground Truth

These terms should remain distinct.

| Term                     | ChandraMap meaning                                                |
| ------------------------ | ----------------------------------------------------------------- |
| Candidate correspondence | Proposed match from the matching stage                            |
| Verified inlier          | Candidate correspondence that survives geometric verification     |
| Control point            | Reliable correspondence used to constrain/f fit geometry          |
| Tie point                | Corresponding feature between images; may later be refined        |
| Refined tie point        | Correspondence whose coordinates have been locally refined        |
| Ground-truth point       | Independently established reference point/relationship            |
| Check point              | Ground-truth point withheld from transformation fitting           |
| Transformation           | Estimated geometric relationship between image coordinate systems |

The exact terminology used by implementation should remain consistent with the repository's eventual data schema.

---

# 7. Ground Truth vs Ground Truth Dataset

A ground-truth **concept** is not necessarily a ground-truth **dataset**.

ChandraMap may define a rigorous ground-truth protocol before the final benchmark dataset exists.

Therefore:

```text
Ground-truth design
        ↓
Specification
        ↓
Annotation / extraction process
        ↓
Validation
        ↓
Versioned ground-truth dataset
```

The current existence and final structure of a ChandraMap ground-truth dataset are:

`[TBD]`

This note defines the principles that such a dataset should follow.

---

# 8. Why Ground Truth Is Necessary

Without independent reference information, ChandraMap cannot reliably distinguish between:

```text
good-looking registration
```

and:

```text
correct registration
```

A transformation can produce an attractive overlay while still being wrong in another part of the image.

The project feedback specifically warns that a visually good overlay can still be scientifically incorrect and recommends inspecting residuals and using independent evaluation.

Ground truth provides the reference needed to measure the difference.

---

# 9. Visual Agreement Is Not Ground Truth

A registered overlay is useful for qualitative inspection.

It is not, by itself, ground truth.

For example:

```text
Source
████████████████

Reference
████████████████

Overlay
████████████████
```

may look aligned at a glance.

But local errors could remain:

```text
Correct:
●

Estimated:
×
```

with:

```text
●────×
```

representing a non-zero registration error.

Therefore visual inspection should complement, not replace, quantitative evaluation.

---

# 10. Ground Truth and the ChandraMap Pipeline

The project pipeline can be represented as:

```text
Input Images
     ↓
Sensor-Aware Preparation
     ↓
Scale Handling
     ↓
Representation
     ↓
Local Matching
     ↓
Candidate Correspondences
     ↓
RANSAC / Geometric Verification
     ↓
Verified Inliers
     ↓
Sub-Pixel Refinement
     ↓
Final Transformation
     ↓
Independent Check-Point Evaluation
     ↓
Residuals / RMSE / Coverage / Failure Analysis
```

The supplied feedback recommends the sequence:

```text
LOCAL MATCHES
      ↓
RANSAC INLIERS
      ↓
SUB-PIXEL TIE POINTS
      ↓
FINAL MODEL
      ↓
REGISTERED IMAGE
```

and places independent check-point evaluation after transformation estimation.

---

# 11. Ground Truth and Candidate Correspondences

Candidate correspondences are generated by the matching system.

They may contain:

- correct matches,
- incorrect matches,
- ambiguous matches,
- spatially clustered matches,
- matches caused by repeated terrain structures.

A matcher confidence score does not establish correctness.

The project feedback explicitly recommends calling these **candidate matches** and allowing RANSAC/geometric verification to determine which candidates become verified inliers.

Ground truth provides another, independent way to determine whether the final registration is correct.

---

# 12. Ground Truth and RANSAC Inliers

RANSAC inliers are not automatically ground truth.

They are correspondences that are consistent with the estimated geometric model under the RANSAC procedure.

Conceptually:

```text
Candidate Matches
       ↓
     RANSAC
       ↓
Verified Inliers
```

This means:

> **Geometric consistency is not necessarily independent truth.**

A wrong set of correspondences can sometimes support a plausible model, especially when:

- terrain is repetitive,
- matches are spatially clustered,
- the model is flexible,
- the overlap is limited.

Therefore RANSAC inliers can be used for fitting, while independent ground-truth/check points are used for evaluation.

---

# 13. Ground Truth and Transformation Fitting

Suppose the transformation is:

$$
\mathbf{x}' = T(\mathbf{x})
$$

where:

- \(\mathbf{x}\) is a source-image coordinate,
- \(T\) is the estimated transformation,
- \(\mathbf{x}'\) is the predicted coordinate in the reference coordinate system.

A control point provides:

$$
(\mathbf{x}_i,\mathbf{y}_i)
$$

and the transformation can be estimated from those points.

A check point provides another independently known pair:

$$
(\mathbf{x}_j,\mathbf{y}_j)
$$

The estimated transformation is then evaluated by:

$$
\hat{\mathbf{y}}_j = T(\mathbf{x}_j)
$$

and the error is:

$$
\mathbf{e}_j =
\hat{\mathbf{y}}_j-\mathbf{y}_j
$$

This is the basic mathematical distinction between model fitting and independent evaluation.

---

# 14. Ground-Truth Coordinate Systems

Ground truth can exist in different coordinate systems.

The most important distinction for ChandraMap is between:

1. **image coordinates**, and
2. **physical/geospatial coordinates**.

---

# 15. Image-Coordinate Ground Truth

Image-coordinate ground truth describes a feature using pixel coordinates.

For example:

```text
Source image:
(x_s, y_s)

Reference image:
(x_r, y_r)
```

A ground-truth correspondence therefore has the conceptual form:

```text
Source point  →  Reference point
(x_s, y_s)       (x_r, y_r)
```

This is particularly useful for evaluating image registration directly.

---

# 16. Physical / Geospatial Ground Truth

A point may also be represented using physical or geospatial coordinates, for example:

```text
latitude
longitude
```

or another appropriate planetary coordinate representation.

However, converting image coordinates into physical units requires sufficient information about:

- GSD,
- projection,
- map geometry,
- reference coordinate system,
- product generation,
- and the reliability of the underlying geospatial reference.

The project feedback explicitly recommends reporting sub-pixel error in source-image pixels first and converting to metres only when GSD and projection make that conversion meaningful.

---

# 17. Pixel Error vs Ground Error

These should not be conflated.

A registration error of:

```text
0.2 source pixels
```

is an image-space measurement.

The corresponding physical error depends on the source product.

Conceptually:

```text
pixel error
    ↓
source GSD
    ↓
projection / geometry
    ↓
physical error
```

Therefore the same pixel error can correspond to different physical distances for different sensors.

The supplied project feedback explicitly notes that 0.2 pixel on TMC-2 and 0.2 pixel on IIRS do not represent the same ground error.

---

# 18. Source-Image Pixels as the Primary Registration Unit

For ChandraMap's sub-pixel registration objective, source-image pixel coordinates should be treated as the primary reporting unit when appropriate.

A result such as:

```text
Check-point RMSE = [TBD] source pixels
```

is preferable to immediately converting the value into metres when the physical conversion is not justified.

Physical-unit reporting should be an additional measurement when the required metadata and reference geometry support it.

---

# 19. What Makes Useful Ground Truth?

A useful ground-truth reference should be:

- independently established,
- traceable to its source,
- geometrically meaningful,
- sufficiently accurate for the evaluation target,
- represented in an unambiguous coordinate system,
- associated with uncertainty where appropriate,
- spatially distributed,
- versioned,
- reproducible,
- protected against accidental leakage into model fitting.

These are design requirements, not claims that every current ChandraMap dataset already satisfies them.

---

# 20. Independence Is a Property of the Process

Calling something a "check point" does not automatically make it independent.

For example, if a point is:

1. manually selected,
2. used during transformation tuning,
3. used to choose a matcher,
4. used to tune RANSAC thresholds,
5. then called a test point,

the evaluation is no longer truly independent.

Therefore independence should be considered at the **benchmark-design level**, not merely at the file-column level.

---

# 21. Ground-Truth Creation Methods

There is no single universally correct way to create lunar registration ground truth.

Possible approaches include:

### 21.1 Challenge-provided ground truth

If the SIH benchmark supplies correspondence or evaluation truth, that should be used according to the challenge specification.

The supplied feedback explicitly recommends using challenge ground truth if available.

Current availability for the ChandraMap repository:

`[To be verified]`

---

### 21.2 Independently checked tie points

When challenge ground truth is unavailable, independently checked tie points can be retained as check points.

This is directly recommended in the project feedback.

---

### 21.3 Trusted reference products

A higher-confidence reference image/product may be used to establish corresponding locations where its geometry and accuracy are sufficiently known.

The exact reference-product protocol is:

`[TBD]`

---

### 21.4 Geospatially referenced points

If both products have trustworthy map geometry, known physical coordinates can provide another evaluation reference.

This requires validation of the relevant geospatial metadata.

---

### 21.5 Human-verified correspondences

Human annotation can be useful when automated ground truth is unavailable.

However, human annotation is not automatically exact.

It should therefore record:

- annotation procedure,
- coordinate convention,
- annotator information where appropriate,
- ambiguity,
- uncertainty,
- validation procedure.

The final ChandraMap annotation workflow is:

`[Not provided / TBD]`

---

# 22. Ground Truth Should Not Be Created From the Model Being Evaluated

A circular procedure would be:

```text
Run ChandraMap
      ↓
Select its best matches
      ↓
Call them ground truth
      ↓
Evaluate ChandraMap against them
```

This does not provide independent evidence.

Similarly:

```text
Run matcher
  ↓
Fit transformation
  ↓
Generate "ground truth"
```

creates circular validation.

Ground truth should come from an independent source or independently controlled procedure.

---

# 23. Reference Image Is Not Automatically Ground Truth

A reference image can be useful without being perfect ground truth.

For example:

```text
Reference image
      ↓
visual/geometric reference
```

does not automatically establish that every pixel coordinate is exact.

Its own:

- projection,
- orthorectification,
- geolocation,
- resolution,
- acquisition geometry,
- processing history

can influence its reliability.

Therefore a reference image should be treated as ground truth only to the degree justified by its provenance and validation.

---

# 24. Ground Truth and Reference Data

The project materials identify LRO NAC as a potential reference source and LRO WAC as an additional lunar-scale/illumination source.

However:

> **A potential reference source is not automatically a finalized ChandraMap ground-truth dataset.**

The exact reference products used for final evaluation remain:

`[TBD]`

The benchmark must document why the selected reference is sufficiently reliable for the intended evaluation.

---

# 25. Ground-Truth Correspondence Record

A recommended correspondence record could contain:

```yaml
point_id: [TBD]

source:
  image_id: [TBD]
  x: [TBD]
  y: [TBD]

reference:
  image_id: [TBD]
  x: [TBD]
  y: [TBD]

coordinate_system: image_pixels
point_type: check
source_of_truth: [TBD]

uncertainty:
  source_sigma_px: [TBD]
  reference_sigma_px: [TBD]

validation:
  method: [TBD]
  status: [TBD]

dataset_version: [TBD]
```

This is a **recommended schema**, not an assertion that this exact YAML structure is currently implemented.

---

# 26. Coordinate Convention

Ground-truth files must explicitly define the coordinate convention.

At minimum, document:

- origin location,
- x-axis direction,
- y-axis direction,
- pixel-center convention,
- units,
- image indexing convention,
- coordinate system identifier.

For example:

```text
coordinate_system: image_pixels
origin: [TBD]
pixel_center_convention: [TBD]
indexing: [TBD]
units: pixels
```

These details are especially important for sub-pixel evaluation.

---

# 27. Pixel Centers Matter

Suppose a point is reported as:

```text
x = 100.5
y = 200.5
```

That value has a different interpretation depending on whether coordinates refer to:

- pixel centers,
- pixel corners,
- zero-based indices,
- one-based indices.

Therefore the benchmark must explicitly define its convention.

Current ChandraMap convention:

`[TBD]`

This should be fixed before final benchmark measurements are reported.

---

# 28. Image Coordinates and Map Coordinates Must Not Be Mixed

A ground-truth record should never leave it ambiguous whether:

```text
x = 100
y = 200
```

means:

- image pixels,
- projected coordinates,
- latitude/longitude,
- metres,
- kilometres,
- or another coordinate system.

Every coordinate record should have an explicit coordinate-system definition.

---

# 29. Ground-Truth Uncertainty

Ground truth is not necessarily infinitely precise.

A manually identified point may have uncertainty.

A reference product may have geolocation uncertainty.

A resampled image may introduce localization uncertainty.

Therefore ground truth should support uncertainty where it is meaningful.

Conceptually:

```text
Ground-truth point
        ●
       / \
      /   \
 uncertainty region
```

The exact uncertainty model is:

`[TBD]`

---

# 30. Why Uncertainty Matters

Suppose a method produces:

```text
0.05 pixel error
```

but the ground-truth point itself can only be localized to approximately:

```text
[TBD] pixels
```

Then reporting the 0.05-pixel result as though the reference were exact would overstate the precision of the evaluation.

Therefore evaluation precision should be interpreted relative to ground-truth uncertainty.

---

# 31. Possible Uncertainty Representations

Depending on the benchmark, uncertainty could be represented as:

- scalar standard deviation,
- separate x/y uncertainty,
- covariance matrix,
- confidence interval,
- annotation tolerance,
- categorical uncertainty level.

Example:

```yaml
uncertainty:
  type: covariance
  sigma_x_px: [TBD]
  sigma_y_px: [TBD]
  covariance_xy: [TBD]
```

The final ChandraMap representation is:

`[TBD]`

---

# 32. Ground-Truth Validation

Ground truth itself requires validation.

A useful validation chain is:

```text
Ground-Truth Candidate
        ↓
Independent Review
        ↓
Geometric Consistency Check
        ↓
Metadata / Provenance Check
        ↓
Uncertainty Assessment
        ↓
Accepted Ground Truth
```

Ground truth should not become a trusted object merely because it has been placed in a file called `ground_truth.csv`.

---

# 33. Ground-Truth Quality Checks

Potential validation checks include:

### Coordinate validity

Are coordinates inside the corresponding image bounds?

### Pair consistency

Does each source point have exactly the intended reference correspondence?

### Duplicate detection

Are multiple points accidentally representing the same feature?

### Spatial distribution

Are points sufficiently distributed over the overlap?

### Visual verification

Does the correspondence identify the same physical terrain structure?

### Geometric consistency

Does the reference relationship remain plausible under the intended coordinate system?

### Provenance

Can the source of the point be traced?

### Uncertainty

Is the expected localization quality documented?

---

# 34. Spatial Coverage of Ground Truth

Ground-truth quality is not only about point accuracy.

Spatial distribution matters.

Consider:

```text
Case A

●       ●

    ●

●       ●
```

versus:

```text
Case B

●●●●●
●●●●●
```

Both may contain the same number of points.

Case B provides much less geometric coverage.

The project explicitly recommends spatial coverage metrics such as grid coverage or convex-hull coverage to determine whether good points are distributed across the overlap rather than clustered around one crater.

---

# 35. Ground Truth Coverage vs Model Coverage

Two related but distinct questions should be asked:

### Ground-truth coverage

Where are independently validated points available?

### Model-fit coverage

Where are the points used by the transformation located?

A benchmark can have excellent ground truth but still produce a poor transformation because the model-fit correspondences cluster in one region.

Therefore both should be recorded.

---

# 36. Spatially Balanced Check Points

Where the geometry allows it, independent check points should be distributed across the relevant overlap.

A conceptual grid is:

```text
+---+---+---+---+
| ● |   | ● |   |
+---+---+---+---+
|   | ● |   | ● |
+---+---+---+---+
| ● |   | ● |   |
+---+---+---+---+
|   | ● |   | ● |
+---+---+---+---+
```

The exact grid size should not be assumed.

The project feedback gives `4 × 4` grid coverage as an example metric, not as a mandatory ChandraMap ground-truth configuration.

---

# 37. Ground-Truth Splitting

Ground-truth correspondences should be divided according to their evaluation role.

A simple conceptual split is:

```text
Ground-Truth Correspondences
            │
       ┌────┴────┐
       ▼         ▼
    Control    Check
     Points    Points
       │         │
       ▼         ▼
     Fit       Evaluate
```

The exact split policy is:

`[TBD]`

It should be fixed before reporting benchmark results wherever possible.

---

# 38. Why the Split Must Be Controlled

If the split is changed after observing results, evaluation can become biased.

For example:

```text
Run experiment
     ↓
Observe failures
     ↓
Move difficult points into fitting set
     ↓
Report improved result
```

would compromise the evaluation.

Therefore the benchmark should establish the fitting/check-point assignment before final evaluation.

---

# 39. Training, Validation, and Test Concepts

If ChandraMap later includes learned models, ground truth may need additional dataset partitions:

```text
Training
Validation
Test
```

But these are not automatically required for the classical V1 pipeline.

For V1, the immediate distinction is:

```text
Transformation-fitting correspondences
vs.
Independent evaluation points
```

If learned methods are introduced, dataset leakage must be considered separately.

---

# 40. Ground Truth and Hyperparameter Tuning

Independence also applies to parameter tuning.

Suppose check-point error is used repeatedly to select:

- RANSAC threshold,
- matching ratio,
- pyramid level,
- feature detector,
- transformation model.

Then those check points are effectively becoming part of the optimization process.

A future benchmark should therefore distinguish:

```text
Development / tuning data
```

from:

```text
Final evaluation data
```

where the project scale requires it.

Exact V1 policy:

`[TBD]`

---

# 41. Ground Truth and RANSAC

RANSAC is intended to reject geometrically inconsistent candidate correspondences.

Conceptually:

```text
Candidate Matches
       ↓
Random model hypotheses
       ↓
Geometric consistency
       ↓
Verified Inliers
```

The verified inliers can be used to estimate the initial model.

They should not automatically be treated as independent ground truth.

The ground-truth/check-point layer is separate:

```text
Verified Inliers → fit
Independent Check Points → evaluate
```

---

# 42. Ground Truth and Sub-Pixel Refinement

The project feedback recommends:

```text
RANSAC
   ↓
verified inliers
   ↓
sub-pixel refinement
   ↓
refit final transformation
```

rather than attempting to refine arbitrary candidate matches.

Ground truth then evaluates the final transformation:

```text
Refined Tie Points
       ↓
Final Transformation
       ↓
Independent Check Points
       ↓
Final Error
```

This ensures that sub-pixel refinement is evaluated by independent evidence.

---

# 43. Ground Truth and Affine/Homography Models

The V1 geometry work includes affine vs homography.

A ground-truth framework should not assume beforehand that either model is universally correct.

Instead:

```text
Ground Truth
     ↓
Fit affine
     ↓
Evaluate independently

Ground Truth
     ↓
Fit homography
     ↓
Evaluate independently
```

This makes the model comparison empirical.

The project feedback describes affine/homography as reasonable first models for local, already map-projected pairs while warning that lunar terrain and raw sensor geometry can require more careful modeling.

---

# 44. Ground Truth and Flexible Warping

A flexible warp can reduce apparent registration error, but that does not automatically mean the underlying correspondences are correct.

The project feedback explicitly warns against allowing flexible warping to hide weak correspondences and recommends using it only after control points are accurate and well distributed.

Therefore:

```text
Ground truth
     ↓
Validate control-point quality
     ↓
Validate spatial coverage
     ↓
Estimate appropriate model
     ↓
Evaluate independently
```

A visually impressive warp should not be allowed to substitute for trustworthy correspondence evidence.

---

# 45. Ground Truth and Scale

Ground truth must remain meaningful across different spatial resolutions.

For example:

```text
OHRC
fine image coordinates

TMC-2
coarser image coordinates

IIRS
much coarser representation
```

A point that is precise enough for one product may not have the same physical significance for another.

The project feedback emphasizes that different sensors should retain their sensor-specific interpretation and that identical pixel errors do not necessarily imply identical ground errors.

Therefore ground-truth evaluation should preserve:

- sensor identity,
- image coordinate system,
- GSD,
- product metadata,
- physical-unit conversion assumptions.

---

# 46. Ground Truth and Illumination

Ground truth should describe the same physical terrain even when image appearance changes.

This matters because lunar Sun-angle changes can move or alter shadows.

A correspondence should not be rejected merely because:

```text
shadow appearance changed
```

if the underlying terrain feature is still the same.

Conversely, a brightness-normalized image should not be assumed to establish correct correspondence merely because its intensity statistics look similar.

The project materials explicitly distinguish illumination changes in shadow geometry from simple brightness changes.

---

# 47. Ground Truth and Sensor Modality

Cross-sensor ground truth is particularly challenging.

For example:

```text
OHRC
visible panchromatic
        ↕
IIRS
infrared hyperspectral
```

The corresponding physical structure may have substantially different image appearance.

The project therefore recommends sensor-aware preprocessing and a registration-friendly 2D representation for IIRS rather than treating the hyperspectral cube as an ordinary image.

Ground truth should describe the underlying correspondence, not assume that visual similarity must be identical.

---

# 48. Ground Truth and Geometry

Ground truth should not silently assume that the image relationship is perfectly planar.

The Moon has terrain relief.

Viewing geometry and sensor geometry may introduce spatially varying effects.

The project feedback recommends inspecting residual vectors across the image and considering local/piecewise models or available sensor/DEM geometry if systematic residual patterns remain.

Ground truth should therefore be rich enough to reveal model limitations rather than force every pair into one transformation.

---

# 49. Ground Truth Should Expose Model Failure

A good benchmark does not only reward successful transformations.

It should also reveal:

- incorrect matches,
- poor spatial coverage,
- systematic residuals,
- scale failures,
- illumination failures,
- modality failures,
- geometric-model failures,
- low-feature terrain failures.

The project explicitly recommends a stress-test matrix containing easy, Sun-angle, scale, modality, geometry, and low-feature cases.

---

# 50. Ground Truth and Registration Metrics

Ground truth supports several evaluation measurements.

The project feedback recommends:

| Stage                     | Metric                            | Purpose                                                 |
| ------------------------- | --------------------------------- | ------------------------------------------------------- |
| Global retrieval, if used | Recall@1 / Recall@5               | Whether the correct region enters candidate retrieval   |
| Local matching            | Inlier count + inlier ratio       | Quality of geometrically verified correspondences       |
| Distribution              | Grid or convex-hull coverage      | Whether good points are spatially distributed           |
| Registration              | Check-point RMSE in source pixels | Accuracy on points not used for fitting                 |
| Geospatial accuracy       | Ground error in metres            | Physical accuracy where GSD/projection/truth justify it |
| System                    | Runtime + failure rate            | Practical reliability                                   |

These metrics are explicitly identified in the supplied project feedback.

---

# 51. Reprojection Error

For a check point \(i\), define:

$$
\mathbf{e}_i =
\hat{\mathbf{p}}_i-\mathbf{p}_i
$$

where:

- \(\mathbf{p}\_i\) is the ground-truth reference coordinate,
- \(\hat{\mathbf{p}}\_i\) is the coordinate predicted by the estimated transformation.

The Euclidean error is:

$$
e_i =
\sqrt{
(\hat{x}_i-x_i)^2+
(\hat{y}_i-y_i)^2
}
$$

with units determined by the coordinate system.

For image-coordinate evaluation, this is typically reported in pixels.

---

# 52. Check-Point RMSE

For \(N\) independent check points:

$$
RMSE =
\sqrt{
\frac{1}{N}
\sum_{i=1}^{N}
e_i^2
}
$$

This provides an aggregate registration error.

However, RMSE should not be reported without describing:

- the coordinate system,
- the check-point set,
- whether points were independent,
- the sensor/product,
- the evaluation condition.

---

# 53. RMSE Is Not the Whole Evaluation

A low RMSE does not guarantee good registration everywhere.

For example:

```text
● ● ● ●
```

could all be concentrated around one crater.

A transformation may achieve low local error while being poorly constrained elsewhere.

Therefore ChandraMap should consider:

```text
RMSE
+
inlier count
+
inlier ratio
+
spatial coverage
+
residual distribution
+
failure rate
```

rather than relying on one scalar.

---

# 54. Residual Vectors

Residual vectors are useful for understanding systematic error.

Conceptually:

```text
Ground truth:   ●
Prediction:     ×

Residual:       ●──→×
```

Across an image:

```text
●→×       ●→×
    ●→×
●→×       ●→×
```

The direction and magnitude of these vectors can reveal patterns.

The project feedback specifically recommends inspecting residual vectors across the image.

---

# 55. Residual Patterns and Ground-Truth Design

Ground truth should contain enough spatial coverage to make residual patterns observable.

If all check points are clustered:

```text
+----------------+
|                |
|  ●●●           |
|  ●●●           |
|                |
+----------------+
```

then systematic errors elsewhere may remain invisible.

A better distribution is:

```text
+----------------+
| ●          ●   |
|                |
|      ●         |
|                |
| ●          ●   |
+----------------+
```

The actual distribution should depend on the overlap and available reliable points.

---

# 56. Ground Truth and Coverage Metrics

A ground-truth benchmark should ideally make it possible to measure:

### Point count

How many independent evaluation points exist?

### Spatial extent

How much of the overlap do they cover?

### Distribution

Are they clustered?

### Convex hull

What fraction of the evaluation region is covered by the points?

### Grid coverage

How many spatial cells contain valid evaluation points?

The exact benchmark metric is:

`[TBD]`

---

# 57. Ground Truth for Different Stress Cases

Ground truth should remain usable across the stress matrix.

## Easy pair

Known overlap and relatively favorable conditions.

Purpose:

> Demonstrate that the end-to-end pipeline works.

## Sun-angle stress

Same region with substantially different shadows.

Purpose:

> Measure robustness to illumination changes.

## Scale stress

Large GSD difference.

Purpose:

> Measure scale-handling behavior.

## Modality stress

IIRS-derived 2D representation versus visible reference.

Purpose:

> Measure cross-sensor representation handling.

## Geometry stress

Relief-rich or stronger viewpoint differences.

Purpose:

> Evaluate geometric-model robustness.

## Low-feature terrain

Smooth or repetitive regions.

Purpose:

> Expose false matches and failure cases.

These stress cases are explicitly recommended in the project evaluation design.

---

# 58. Ground Truth and Sensor-Specific Evaluation

The final benchmark should avoid collapsing all sensor pairs into one mixed number when their measurement properties differ substantially.

The project feedback recommends separate sensor results.

A useful structure is:

| Sensor path       | Scale | Modality | Ground-truth type |  RMSE | Coverage | Failure rate |
| ----------------- | ----- | -------- | ----------------- | ----: | -------: | -----------: |
| OHRC → reference  | [TBD] | [TBD]    | [TBD]             | [TBD] |    [TBD] |        [TBD] |
| TMC-2 → reference | [TBD] | [TBD]    | [TBD]             | [TBD] |    [TBD] |        [TBD] |
| IIRS → reference  | [TBD] | [TBD]    | [TBD]             | [TBD] |    [TBD] |        [TBD] |

The table is a reporting template, not a current result.

---

# 59. Ground Truth and Benchmark Pair Selection

A benchmark should not contain only easy examples.

If every image pair is highly similar:

```text
similar scale
similar illumination
similar modality
simple terrain
```

then a method may appear stronger than it is under the actual project problem.

A useful benchmark should deliberately include difficult conditions while preserving reliable ground truth.

---

# 60. Ground Truth and Benchmark Difficulty

Difficulty should be documented rather than inferred from a single score.

Potential attributes include:

- scale difference,
- illumination difference,
- sensor pair,
- modality difference,
- terrain complexity,
- feature density,
- overlap,
- viewing geometry,
- available metadata.

Example:

```yaml
pair_id: [TBD]

difficulty:
  scale: [TBD]
  illumination: [TBD]
  modality: [TBD]
  geometry: [TBD]
  terrain: [TBD]
```

---

# 61. Ground Truth and Metadata

Every ground-truth pair should retain relevant metadata where available.

The project feedback specifically recommends preserving:

- pixel scale,
- footprint,
- map projection,
- viewing geometry,
- lighting information.

A recommended record is:

```yaml
pair_id: [TBD]

source:
  image_id: [TBD]
  sensor: [TBD]
  gsd: [TBD]
  footprint: [TBD]
  projection: [TBD]

reference:
  image_id: [TBD]
  sensor: [TBD]
  gsd: [TBD]
  footprint: [TBD]
  projection: [TBD]

illumination:
  source: [TBD]
  reference: [TBD]

viewing_geometry:
  source: [TBD]
  reference: [TBD]
```

---

# 62. Ground-Truth Provenance

Every ground-truth record should answer:

> Where did this point come from?

Potential provenance fields:

```yaml
provenance:
  source_type: [TBD]
  source_dataset: [TBD]
  source_product: [TBD]
  source_file: [TBD]
  creation_method: [TBD]
  created_by: [TBD]
  created_at: [TBD]
  reviewed_by: [TBD]
  reviewed_at: [TBD]
```

The exact schema is `[TBD]`.

The important principle is traceability.

---

# 63. Ground-Truth Versioning

Ground truth should be versioned just like code and benchmark definitions.

A conceptual history is:

```text
ground truth v0.1
      ↓
annotation corrections
      ↓
ground truth v0.2
      ↓
validation changes
      ↓
ground truth v1.0
```

A benchmark result should identify the exact ground-truth version used.

Otherwise the same experiment can produce different results later without an obvious explanation.

---

# 64. Recommended Ground-Truth Version Record

```yaml
dataset:
  name: [TBD]
  version: [TBD]
  created_at: [TBD]
  updated_at: [TBD]

source_data:
  dataset_id: [TBD]
  product_versions: [TBD]

annotation:
  protocol_version: [TBD]

validation:
  protocol_version: [TBD]

coordinate_convention:
  version: [TBD]

notes: [TBD]
```

This is a recommended structure, not an existing project schema.

---

# 65. Immutable Benchmark Evaluation Sets

Once a final benchmark evaluation set is established, changes should be controlled.

Possible policy:

```text
Benchmark v1
   ↓
Frozen evaluation points
   ↓
Experiment A
Experiment B
Experiment C
```

If a ground-truth error is discovered:

```text
Do not silently overwrite
        ↓
Document correction
        ↓
Create new dataset version
        ↓
Record affected experiments
```

This preserves scientific traceability.

---

# 66. Ground Truth and Experiment Reproducibility

An experiment result should be reproducible from:

```text
Code version
+
Configuration
+
Input image pair
+
Ground-truth version
+
Evaluation protocol
```

Therefore an experiment record should include the ground-truth version.

Example:

```yaml
evaluation:
  ground_truth_dataset: [TBD]
  ground_truth_version: [TBD]
  checkpoint_protocol: [TBD]
```

---

# 67. Ground Truth and Results Directory

Generated evaluation results should not become the source of truth for the ground truth itself.

Conceptually:

```text
data/
  ground_truth/
        ↓
benchmark definition
        ↓
experiments/
        ↓
results/
```

Ground truth is an input to evaluation.

Results are outputs of evaluation.

The exact physical repository layout is:

`[To be verified]`

---

# 68. Ground Truth and Benchmark Specification

The benchmark should define:

- image-pair selection,
- ground-truth source,
- coordinate convention,
- fitting/check-point separation,
- uncertainty,
- metrics,
- failure definition,
- version,
- provenance.

Therefore the ground-truth design note should inform the benchmark specification rather than duplicate its implementation.

The relevant benchmark area is:

`benchmarks/`

The exact benchmark specification file is:

`[TBD]`

---

# 69. Ground Truth and Experiment README Files

This note establishes scientific principles.

Experiment files should define the actual experiment.

For example:

```text
research/notes/ground-truth-design.md
        ↓
scientific evaluation principles

experiments/v1/.../README.md
        ↓
specific experiment protocol

results/
        ↓
measured outputs
```

The ground-truth note should not become a substitute for experiment documentation.

---

# 70. Ground Truth and EXP-001

`EXP-001-sift-baseline` should establish the first measurable baseline.

The project feedback recommends a first end-to-end milestone:

```text
Known source/reference pair
        ↓
SIFT
        ↓
RANSAC
        ↓
Transformation
        ↓
Registered overlay
        ↓
Check-point error
```

with match plots, rejected outliers, inlier statistics, and check-point error saved as evidence.

Ground truth is therefore required even for the simplest baseline if the project wants to make a quantitative registration claim.

---

# 71. Ground Truth and EXP-002

For `EXP-002-scale-pyramid`, the same independent evaluation points should be used when comparing baseline and scale-aware variants, where the experiment design permits.

Conceptually:

```text
Same image pair
       │
       ├── Baseline
       │
       └── Scale-aware
              │
              ▼
       Same check-point set
              │
              ▼
          Compare error
```

This reduces the chance that differences arise merely from different evaluation samples.

---

# 72. Ground Truth and EXP-003

For gradient/structural representation experiments, ground truth should remain unchanged when the experiment is comparing representations on the same image pairs.

```text
Same ground truth
      │
      ├── grayscale
      └── gradient/structural
```

The independent evaluation reference should not change simply because the image representation changes.

---

# 73. Ground Truth and EXP-004

For affine vs homography:

```text
Same correspondences
        ↓
Affine model
        ↓
Check-point evaluation

Same correspondences
        ↓
Homography model
        ↓
Check-point evaluation
```

This helps isolate the effect of the transformation model.

If the correspondence set changes too, the experiment becomes a combined matcher + geometry comparison and should be described accordingly.

---

# 74. Ground Truth and EXP-005

Residual analysis depends directly on trustworthy check-point coordinates.

The experiment should therefore retain:

- check-point identifiers,
- observed coordinates,
- predicted coordinates,
- residual vectors,
- residual magnitude,
- spatial location.

A recommended record is:

```yaml
point_id: [TBD]
x_reference: [TBD]
y_reference: [TBD]
x_predicted: [TBD]
y_predicted: [TBD]
dx: [TBD]
dy: [TBD]
error_px: [TBD]
```

---

# 75. Ground Truth and EXP-006

Sub-pixel refinement should be evaluated against the same independent reference whenever appropriate.

Conceptually:

```text
Before refinement
       ↓
Check-point RMSE

After refinement
       ↓
Same check-point RMSE
```

The project feedback specifically recommends reporting source-pixel check-point RMSE before and after refinement.

This creates a direct quantitative test of whether refinement improves registration.

---

# 76. Ground Truth and Future Learned Methods

If ChandraMap later evaluates:

- ALIKED + LightGlue,
- LoFTR,
- RIFT-inspired approaches,
- CFOG-inspired approaches,
- other learned or structural matchers,

the same ground-truth framework should be retained.

The project feedback recommends comparing methods on the same image pairs and keeping methods only when they improve measured difficult cases.

Ground truth therefore provides a common evaluation layer across algorithm generations.

---

# 77. Ground Truth and Method Comparison

A fair comparison can be represented as:

```text
                SAME IMAGE PAIRS
                       │
              SAME GROUND TRUTH
                       │
        ┌──────────────┼──────────────┐
        ▼              ▼              ▼
     SIFT          Learned A       Learned B
        │              │              │
        ▼              ▼              ▼
    Transform      Transform       Transform
        │              │              │
        └──────────────┼──────────────┘
                       ▼
              SAME CHECK POINTS
                       │
                       ▼
                 SAME METRICS
```

This is substantially more informative than comparing methods on different data.

---

# 78. Ground Truth and Failure Definition

A benchmark should define what counts as a failed registration.

Possible failure criteria include:

- no valid transformation,
- insufficient verified inliers,
- insufficient spatial coverage,
- check-point error above a defined threshold,
- invalid output,
- runtime failure.

The exact ChandraMap failure thresholds are:

`[TBD]`

They should not be invented before the benchmark is defined.

---

# 79. Avoiding Selection Bias

Ground truth should not be constructed only for examples where the pipeline is expected to succeed.

A strong benchmark should retain difficult cases.

For example:

```text
Successful cases
+
Scale failures
+
Illumination failures
+
Modality failures
+
Geometry failures
+
Low-feature failures
```

The project explicitly emphasizes showing what works, where it fails, and what the pipeline improves.

---

# 80. Ground Truth and Negative Cases

Not every pair necessarily needs to have a successful registration.

A benchmark may include difficult or invalid cases to test failure detection.

However, whether ChandraMap V1 includes explicit negative pairs is:

`[TBD]`

If negative cases are introduced, their ground-truth semantics must be documented separately.

---

# 81. Ground Truth and Partial Overlap

Lunar images may not have complete overlap.

Ground truth should therefore distinguish:

```text
image extent
```

from:

```text
valid overlap region
```

A point outside the true overlap should not be treated as a valid registration checkpoint.

Potential metadata:

```yaml
overlap:
  type: [TBD]
  geometry: [TBD]
  confidence: [TBD]
```

---

# 82. Ground Truth and Border Effects

Features near image boundaries may be less reliable because:

- descriptor windows can be truncated,
- resampling can affect neighborhoods,
- geometric transformations may move points outside the image,
- ground-truth correspondence may not be fully observable.

Therefore benchmark design should document any border-handling policy.

Current policy:

`[TBD]`

---

# 83. Ground Truth and Occlusion / Shadow Changes

On the Moon, shadows can change substantially with Sun angle.

A ground-truth point should refer to the same physical terrain feature even if its intensity or shadow appearance changes.

However, if a feature is no longer reliably identifiable in one image, the point may become unsuitable for that specific correspondence evaluation.

This distinction should be documented:

```text
physical correspondence exists
        ≠
feature is visually measurable with equal confidence
```

---

# 84. Ground Truth and Feature Ambiguity

Some terrain structures may be ambiguous.

For example:

```text
Crater A ≈ Crater B
```

A ground-truth annotation should not silently mark an ambiguous point as exact.

Possible statuses include:

```text
valid
ambiguous
uncertain
rejected
```

The final annotation status vocabulary is:

`[TBD]`

---

# 85. Ground Truth Review Process

A recommended review workflow is:

```text
Candidate point
     ↓
Primary annotation/review
     ↓
Independent verification
     ↓
Disagreement check
     ↓
Accept / reject / mark uncertain
```

The exact number of reviewers is:

`[TBD]`

The important principle is that important benchmark truth should have a documented validation process.

---

# 86. Ground Truth Quality Levels

A useful internal classification could distinguish:

| Level                 | Meaning                                       |
| --------------------- | --------------------------------------------- |
| Unverified            | Candidate point not yet independently checked |
| Reviewed              | Human or procedural review completed          |
| Independently checked | Verified by an independent process/person     |
| Benchmark accepted    | Meets the benchmark's inclusion criteria      |
| Rejected              | Not sufficiently reliable                     |

This is a recommended framework.

It is not an existing ChandraMap status schema unless implemented elsewhere.

---

# 87. Ground Truth and Confidence Scores

Confidence can be useful as metadata.

But confidence should not replace truth.

For example:

```text
confidence = 0.98
```

does not by itself establish that a point is correct.

Confidence should be accompanied by:

- how it was calculated,
- what it means,
- what source supports it.

The project feedback similarly warns against decorative confidence scores and recommends actual RMSE, inlier ratio, coverage, and runtime measurements.

---

# 88. Ground Truth and Synthetic Data

The SIH problem materials mention synthetic lunar augmentations involving:

- Sun angle,
- rotation,
- scale,
- contrast.

Synthetic transformations can provide useful controlled experiments because the applied transformation is known.

However:

> Synthetic truth is not automatically equivalent to real lunar ground truth.

A synthetic benchmark can establish whether an algorithm behaves correctly under a known transformation.

It does not necessarily establish performance on independently acquired lunar images with real:

- sensor differences,
- illumination geometry,
- terrain relief,
- acquisition artifacts,
- processing differences.

---

# 89. Synthetic Ground Truth

For a synthetic experiment:

```text
Original image
      ↓
Known transformation
      ↓
Synthetic image
```

the transformation itself can provide reference truth.

For example:

```text
T_known
```

can be compared with:

```text
T_estimated
```

This is valuable for controlled algorithm verification.

The exact synthetic benchmark design is:

`[TBD]`

---

# 90. Real vs Synthetic Evaluation

A strong benchmark architecture may eventually contain:

```text
Synthetic validation
        +
Real lunar evaluation
```

Synthetic tests can answer:

> "Does the implementation recover a known transformation?"

Real lunar tests can answer:

> "Does the system establish reliable correspondence under actual lunar imaging differences?"

These questions should not be conflated.

---

# 91. Ground Truth and Data Leakage

Ground-truth leakage can occur if:

- evaluation coordinates are used during tuning,
- test pairs are repeatedly inspected during development,
- test annotations are used to select methods,
- benchmark points influence preprocessing decisions,
- the same points are used for fitting and evaluation.

A robust benchmark should minimize these pathways.

---

# 92. Ground Truth and Development Workflow

A practical development workflow can use:

```text
Development pairs
     ↓
Tune / debug
     ↓
Validation pairs
     ↓
Select configuration
     ↓
Frozen test pairs
     ↓
Final evaluation
```

Whether this full three-way split is required for V1 remains:

`[TBD]`

For the immediate V1 milestone, the critical requirement is at least to separate transformation-fitting points from independent check points.

---

# 93. Ground Truth and Reproducibility

A reproducible ground-truth dataset requires more than coordinates.

It should preserve:

- source image identity,
- reference image identity,
- source product/version,
- reference product/version,
- coordinate convention,
- ground-truth source,
- annotation protocol,
- validation status,
- uncertainty,
- dataset version.

A future researcher should be able to answer:

> "Why is this point considered ground truth?"

without asking the original author.

---

# 94. Recommended Ground-Truth Manifest

A project-level manifest could contain:

```yaml
ground_truth:
  dataset_name: [TBD]
  version: [TBD]

  coordinate_convention:
    type: [TBD]
    origin: [TBD]
    pixel_center: [TBD]
    indexing: [TBD]

  provenance:
    source_dataset: [TBD]
    source_products: [TBD]
    creation_protocol: [TBD]
    validation_protocol: [TBD]

  evaluation:
    fitting_policy: [TBD]
    check_point_policy: [TBD]
    uncertainty_policy: [TBD]

  integrity:
    checksum: [TBD]
    release_date: [TBD]
```

This is a recommended design, not an existing ChandraMap file format.

---

# 95. Ground Truth and Checksums

When practical, versioned ground-truth files should have integrity identifiers such as checksums.

This allows a result to identify the exact ground-truth artifact used.

Conceptually:

```text
ground_truth_v1
      ↓
checksum
      ↓
experiment result
```

The exact repository mechanism is:

`[TBD]`

---

# 96. Ground Truth and Access Restrictions

If a challenge provides ground truth under restricted terms, the repository should not assume that the full annotations can be committed publicly.

The benchmark should document:

- whether redistribution is permitted,
- whether only derived metrics can be published,
- whether coordinates can be stored,
- whether access requires external credentials.

Current ChandraMap ground-truth licensing/access policy:

`[TBD]`

---

# 97. Copyright and Data Provenance

Ground truth derived from external imagery should preserve appropriate attribution and licensing information.

The repository should distinguish:

```text
source data
```

from:

```text
ChandraMap-derived annotations
```

and document the relationship.

The exact licensing requirements depend on the source datasets and should be verified before redistribution.

---

# 98. Ground Truth and Benchmark Integrity

Benchmark integrity requires that:

1. the reference source is documented;
2. the coordinate convention is explicit;
3. the fitting/check-point split is controlled;
4. uncertainty is documented;
5. the evaluation set is versioned;
6. the test set is not silently modified;
7. experiment results identify the ground-truth version;
8. corrections create traceable versions.

---

# 99. Recommended Ground-Truth File Structure

The exact repository structure is not yet established.

A possible future organization is:

```text
data/
└── ground_truth/
    ├── README.md
    ├── manifests/
    │   └── [TBD]
    ├── correspondences/
    │   └── [TBD]
    ├── checkpoints/
    │   └── [TBD]
    └── versions/
        └── [TBD]
```

This should only be adopted if it matches the final repository architecture.

It should not be interpreted as an existing directory structure.

---

# 100. Recommended Ground-Truth README Contents

If a dedicated ground-truth dataset is later created, its README should document:

```text
1. Purpose
2. Dataset scope
3. Source products
4. Image-pair selection
5. Coordinate convention
6. Annotation protocol
7. Validation protocol
8. Uncertainty
9. Fitting/check-point split
10. Versioning
11. Licensing
12. Known limitations
13. Example record
14. Changelog
```

This research note defines the scientific principles behind those sections.

---

# 101. Ground Truth and Benchmark Traceability

A useful traceability chain is:

```text
Source Product
      ↓
Image Pair
      ↓
Ground-Truth Record
      ↓
Ground-Truth Version
      ↓
Benchmark Case
      ↓
Experiment
      ↓
Measured Result
      ↓
Analysis
```

For example:

```text
Image Pair: [TBD]
Ground Truth: GT-[TBD]
Benchmark Case: CASE-[TBD]
Experiment: EXP-[TBD]
Result: RESULT-[TBD]
```

The exact identifiers are not yet established.

---

# 102. Ground Truth and Research Questions

Ground truth should support specific research questions rather than existing as an isolated dataset.

Examples:

### Scale

> Does a multi-scale strategy improve registration under large GSD differences?

Ground truth provides the independent points needed to measure the answer.

### Illumination

> Does structural representation improve correspondence under different Sun angles?

Ground truth determines whether the resulting registration is actually correct.

### Geometry

> Does affine or homography better explain the correspondence under the tested conditions?

Ground truth provides independent residual measurements.

### Sub-pixel refinement

> Does refinement reduce independent registration error?

Ground truth provides the reference for before/after evaluation.

---

# 103. Ground Truth and Scientific Claims

Every quantitative project claim should be traceable to:

```text
claim
  ↓
experiment
  ↓
result
  ↓
ground-truth version
  ↓
source data
```

For example:

```text
"RMSE decreased"
```

should identify:

- which experiment,
- which image pairs,
- which check points,
- which coordinate system,
- which ground-truth version,
- which configuration.

This makes the result auditable.

---

# 104. Ground Truth and Negative Findings

Ground truth is equally important when a method fails.

For example:

```text
Scale pyramid
      ↓
More candidates
      ↓
No RMSE improvement
```

is meaningful only if the RMSE is independently measured.

Likewise:

```text
Matcher B
      ↓
More inliers
      ↓
Worse check-point RMSE
```

can reveal that match count alone was misleading.

The project principle is to retain such failures rather than hide them.

---

# 105. Ground Truth and Method Selection

Methods should be selected based on measured behavior.

The project feedback recommends:

```text
SIFT baseline
      ↓
Test stronger method
      ↓
Same image pairs
      ↓
Same benchmark
      ↓
Compare metrics
```

The ground-truth framework provides the common evaluation layer.

A method should not be selected merely because its paper reports strong performance on another dataset.

---

# 106. Ground Truth and External Literature

Published literature can establish:

- known methods,
- known evaluation practices,
- known limitations,
- benchmark design principles.

It cannot automatically provide ChandraMap ground truth.

The project must still establish its own evaluation reference for its own data.

Therefore:

```text
Literature
    ↓
Methodological guidance
    ↓
ChandraMap ground-truth design
    ↓
ChandraMap experiment
    ↓
ChandraMap result
```

---

# 107. Ground Truth and the Physical Moon

The ultimate reference is the physical lunar terrain.

But ChandraMap does not directly observe the physical terrain without measurement uncertainty.

Instead:

```text
Physical lunar terrain
        ↓
Sensor measurement
        ↓
Image product
        ↓
Coordinate model
        ↓
Ground-truth representation
```

Each stage can introduce uncertainty.

Therefore "ground truth" in this project should be understood as **validated reference information for the evaluation task**, not philosophical absolute truth.

---

# 108. Ground Truth Limitations

Important limitations may include:

## 108.1 Reference-product uncertainty

The reference itself may contain geometric uncertainty.

## 108.2 Annotation uncertainty

Human-selected points may not have exact coordinates.

## 108.3 Resolution limits

A coarse sensor may not represent the feature precisely.

## 108.4 Illumination differences

The same terrain may look substantially different.

## 108.5 Terrain relief

A global transformation may not explain all local geometry.

## 108.6 Projection differences

Different map projections can introduce coordinate differences.

## 108.7 Sensor geometry

Raw or minimally processed imagery may contain geometry not captured by a simple transformation.

---

# 109. What to Do When Reliable Ground Truth Is Unavailable

The project feedback provides an important fallback:

> If challenge ground truth is unavailable, retain independently checked tie points as check points and do not use them to fit the transform.

This should be treated as a controlled evaluation fallback, not as permission to call arbitrary matches "ground truth."

If even independently checked points cannot be established reliably, the benchmark should explicitly mark the evaluation limitation.

---

# 110. What Not to Do

Do not:

- call all RANSAC inliers ground truth;
- evaluate only on points used for fitting;
- create ground truth from the method being evaluated;
- silently change check points after seeing results;
- report ground error without meaningful GSD/projection/truth;
- assume a reference image is perfect truth;
- hide uncertainty;
- mix coordinate systems;
- silently change pixel conventions;
- discard difficult cases because they reduce the score;
- overwrite benchmark truth without versioning;
- use visual alignment as the only evaluation;
- report unsupported accuracy values.

---

# 111. Ground-Truth Design Checklist

Before declaring a benchmark evaluation valid, verify:

### Source

- [ ] Source image IDs are recorded.
- [ ] Reference image IDs are recorded.
- [ ] Product versions are known where applicable.
- [ ] Relevant sensor metadata is preserved.

### Coordinates

- [ ] Coordinate system is explicitly defined.
- [ ] Pixel convention is defined.
- [ ] Units are defined.
- [ ] Coordinate origin/indexing are documented.

### Ground truth

- [ ] Source of ground truth is documented.
- [ ] Creation process is documented.
- [ ] Validation process is documented.
- [ ] Uncertainty is documented where appropriate.
- [ ] Ground-truth version is recorded.

### Fitting/evaluation

- [ ] Fitting points are identified.
- [ ] Check points are identified.
- [ ] Check points are not used to fit the final transform.
- [ ] Evaluation points were not used improperly for tuning.

### Coverage

- [ ] Check points cover the relevant overlap.
- [ ] Clustering is understood.
- [ ] Spatial coverage is reported.

### Metrics

- [ ] Inlier count is reported.
- [ ] Inlier ratio is reported.
- [ ] Spatial coverage is reported.
- [ ] Check-point RMSE is reported.
- [ ] Ground error is reported only when meaningful.
- [ ] Failure rate is reported where appropriate.
- [ ] Runtime is recorded where relevant.

### Reproducibility

- [ ] Code version is recorded.
- [ ] Configuration is recorded.
- [ ] Ground-truth version is recorded.
- [ ] Input data version is recorded.
- [ ] Result artifacts are traceable.

---

# 112. Recommended Ground-Truth Record Template

```markdown
## Ground-Truth Case

### Identity

- Case ID: [TBD]
- Dataset version: [TBD]
- Source image ID: [TBD]
- Reference image ID: [TBD]

### Sensors

- Source sensor: [TBD]
- Reference sensor: [TBD]

### Image Metadata

- Source dimensions: [TBD]
- Reference dimensions: [TBD]
- Source GSD: [TBD]
- Reference GSD: [TBD]
- Projection: [TBD]
- Footprint: [TBD]

### Ground-Truth Source

- Source type: [TBD]
- Source dataset/product: [TBD]
- Creation method: [TBD]
- Validation method: [TBD]

### Coordinate Convention

- Coordinate system: [TBD]
- Units: [TBD]
- Origin: [TBD]
- Pixel-center convention: [TBD]
- Indexing convention: [TBD]

### Point Sets

- Fitting/control points: [TBD]
- Independent check points: [TBD]

### Uncertainty

- Model: [TBD]
- Source-point uncertainty: [TBD]
- Reference-point uncertainty: [TBD]

### Coverage

- Valid overlap: [TBD]
- Spatial distribution: [TBD]
- Coverage metric: [TBD]

### Limitations

- [TBD]
```

---

# 113. Recommended Check-Point Record

```markdown
## Check Point

- Point ID: [TBD]
- Case ID: [TBD]

### Source Coordinate

- x: [TBD]
- y: [TBD]

### Reference Coordinate

- x: [TBD]
- y: [TBD]

### Coordinate System

- Type: image pixels
- Convention: [TBD]

### Ground-Truth Provenance

- Source: [TBD]
- Validation: [TBD]
- Reviewer/status: [TBD]

### Uncertainty

- x uncertainty: [TBD]
- y uncertainty: [TBD]
- covariance: [TBD]

### Evaluation Role

- Used for fitting: NO
- Used for final evaluation: YES
```

---

# 114. Recommended Evaluation Record

```markdown
## Registration Evaluation

### Experiment

- Experiment ID: [TBD]
- Configuration ID: [TBD]

### Ground Truth

- Dataset: [TBD]
- Version: [TBD]
- Check-point set: [TBD]

### Transformation

- Model: [TBD]
- Fitting points: [TBD]
- RANSAC: [TBD]
- Refinement: [TBD]

### Results

- Candidate matches: [TBD]
- Verified inliers: [TBD]
- Inlier ratio: [TBD]
- Spatial coverage: [TBD]
- Check-point RMSE: [TBD] px
- Ground error: [TBD] m
- Runtime: [TBD]
- Failure status: [TBD]

### Residual Analysis

- Mean residual: [TBD]
- Maximum residual: [TBD]
- Residual pattern: [TBD]
- Known limitations: [TBD]
```

---

# 115. Recommended Ground-Truth Matrix

A project-level matrix can connect ground truth to experiments:

| Ground-Truth Case | Sensor Pair | Stress Type  | Fitting Points | Check Points | Ground-Truth Version | Experiment | Result  |
| ----------------- | ----------- | ------------ | -------------: | -----------: | -------------------- | ---------- | ------- |
| `[TBD]`           | `[TBD]`     | Easy         |        `[TBD]` |      `[TBD]` | `[TBD]`              | EXP-001    | `[TBD]` |
| `[TBD]`           | `[TBD]`     | Scale        |        `[TBD]` |      `[TBD]` | `[TBD]`              | EXP-002    | `[TBD]` |
| `[TBD]`           | `[TBD]`     | Illumination |        `[TBD]` |      `[TBD]` | `[TBD]`              | EXP-003    | `[TBD]` |
| `[TBD]`           | `[TBD]`     | Geometry     |        `[TBD]` |      `[TBD]` | `[TBD]`              | EXP-004    | `[TBD]` |
| `[TBD]`           | `[TBD]`     | Residual     |        `[TBD]` |      `[TBD]` | `[TBD]`              | EXP-005    | `[TBD]` |
| `[TBD]`           | `[TBD]`     | Sub-pixel    |        `[TBD]` |      `[TBD]` | `[TBD]`              | EXP-006    | `[TBD]` |

---

# 116. V1 Ground-Truth Strategy

A conservative V1 strategy is:

```text
1. Select one known source/reference pair.
2. Establish independently checked correspondence points.
3. Separate fitting/control points from check points.
4. Run the SIFT baseline.
5. Fit the initial geometric model with RANSAC.
6. Evaluate on independent check points.
7. Record RMSE, inliers and spatial coverage.
8. Preserve the exact points and metadata used.
9. Repeat the same evaluation for later V1 variants.
10. Keep difficult and failed cases.
```

This aligns with the project's recommendation to establish one measurable end-to-end result before expanding to the entire Moon.

---

# 117. First Ground-Truth Milestone

The first useful milestone is not:

> "Create a huge lunar ground-truth database."

It is:

```text
One known image pair
        ↓
Reliable independent reference points
        ↓
SIFT
        ↓
RANSAC
        ↓
Transformation
        ↓
Independent check-point evaluation
        ↓
Numerical error
```

Once this works, the evaluation framework can be expanded.

This matches the project's build philosophy of obtaining one measurable end-to-end result before scaling the system.

---

# 118. Ground Truth for V1 vs Future Versions

## V1

Focus on:

- reliable image-coordinate correspondences,
- fitting/check-point separation,
- source-pixel error,
- spatial coverage,
- clear provenance,
- small controlled benchmark.

## Future benchmark versions

Potential extensions include:

- larger sensor diversity,
- more illumination conditions,
- larger scale ranges,
- geospatial ground truth,
- DEM/sensor-geometry references,
- uncertainty-aware evaluation,
- more terrain classes,
- frozen test sets,
- learned-model evaluation,
- broader lunar geographic coverage.

The exact version roadmap is:

`[TBD]`

---

# 119. Ground Truth and Benchmark Evolution

A benchmark should evolve without destroying historical comparability.

Conceptually:

```text
Benchmark V1
    ↓
Lessons / corrections
    ↓
Benchmark V2
    ↓
Expanded conditions
    ↓
Benchmark V3
```

Results from different versions should not automatically be treated as directly comparable.

Every published result should identify:

```text
benchmark version
+
ground-truth version
+
experiment version
```

---

# 120. Recommended ChandraMap Traceability Chain

The complete scientific chain should be:

```text
External / Mission Data
        ↓
Image Pair
        ↓
Ground-Truth Reference
        ↓
Benchmark Case
        ↓
Research Question
        ↓
Experiment
        ↓
Transformation
        ↓
Independent Evaluation
        ↓
Measured Result
        ↓
Residual / Failure Analysis
        ↓
Research Finding
```

This connects the ground-truth design to the broader ChandraMap research structure.

---

# 121. Known vs Unknown

| Question                                                     | Current status                   |
| ------------------------------------------------------------ | -------------------------------- |
| Should fitting and evaluation points be separated?           | Established project requirement  |
| Should independent check points be used?                     | Established project requirement  |
| Should challenge ground truth be used if available?          | Recommended                      |
| Should source-pixel RMSE be reported?                        | Established evaluation direction |
| Should ground error require meaningful GSD/projection/truth? | Established                      |
| Should spatial coverage be measured?                         | Established evaluation direction |
| Should sensor-specific evaluation be retained?               | Established project direction    |
| Exact ChandraMap ground-truth dataset                        | `[TBD]`                          |
| Exact checkpoint count                                       | `[TBD]`                          |
| Exact fitting/check-point split                              | `[TBD]`                          |
| Exact annotation protocol                                    | `[TBD]`                          |
| Exact coordinate convention                                  | `[TBD]`                          |
| Exact uncertainty model                                      | `[TBD]`                          |
| Exact benchmark versioning scheme                            | `[TBD]`                          |
| Exact ground-truth source for every sensor pair              | `[TBD]`                          |
| Exact physical accuracy of reference products                | `[TBD]`                          |

---

# 122. Core Rules

For ChandraMap ground truth:

1. **Ground truth must be independent of the model being evaluated.**
2. **Do not fit and evaluate on exactly the same points.**
3. **RANSAC inliers are not automatically ground truth.**
4. **Candidate-match confidence is not proof of correctness.**
5. **Reference imagery is not automatically perfect truth.**
6. **Define coordinate conventions explicitly.**
7. **Report source-pixel error before physical conversion where appropriate.**
8. **Only report ground error when GSD, projection, and reference truth justify it.**
9. **Record uncertainty where meaningful.**
10. **Preserve spatial coverage information.**
11. **Version ground truth.**
12. **Record provenance.**
13. **Keep difficult cases.**
14. **Do not silently change evaluation points after seeing results.**
15. **Use the same evaluation cases when comparing methods.**
16. **Keep sensor-specific behavior visible.**
17. **Separate synthetic truth from real lunar ground truth.**
18. **Do not invent coordinates, checkpoint counts, or accuracy values.**

---

# 123. Final Scientific Position

Ground truth is the foundation that turns ChandraMap registration from a visually demonstrated system into a quantitatively testable research system.

The essential separation is:

```text
Correspondences used to fit
        ≠
Correspondences used to evaluate
```

A trustworthy ChandraMap evaluation should therefore follow:

```text
Candidate Correspondences
        ↓
Geometric Verification
        ↓
Control / Fitting Points
        ↓
Transformation
        ↓
Independent Ground-Truth Check Points
        ↓
Source-Pixel Registration Error
        ↓
Spatial Residual Analysis
        ↓
Bounded Scientific Conclusion
```

Ground truth should be:

- independently established,
- traceable,
- coordinate-explicit,
- uncertainty-aware where appropriate,
- spatially meaningful,
- versioned,
- reproducible.

It should support the project's core evaluation outputs:

- verified inliers,
- inlier ratio,
- spatial coverage,
- independent check-point RMSE,
- meaningful geospatial error where justified,
- runtime,
- failure rate.

Most importantly:

> **A low error on points used to fit a transformation does not prove accurate registration.**

The stronger evidence comes from applying the estimated transformation to independently established reference points and measuring how accurately the model predicts them.

For ChandraMap V1, the practical objective is therefore not to build an enormous ground-truth system immediately. It is to establish one trustworthy, independently evaluated source/reference pair, document its provenance and coordinate convention, separate fitting from evaluation, and produce a reproducible numerical registration error.

From there, the same ground-truth framework can support the scale, illumination, modality, geometry, residual, and sub-pixel experiments that form the foundation of the broader ChandraMap research program.

**Build small. Measure honestly. Keep the failures.**

---

# 124. Related ChandraMap Documentation

## Research

- `research/README.md`
- `research/literature/README.md`
- `research/notes/`
- `research/notes/scale-invariance.md`

## V1 Experiments

- `experiments/v1/README.md`
- `experiments/v1/baseline/EXP-001-sift-baseline/README.md`
- `experiments/v1/preprocessing/EXP-002-scale-pyramid/README.md`
- `experiments/v1/preprocessing/EXP-003-gradient-representation/README.md`
- `experiments/v1/geometry/EXP-004-affine-vs-homography/README.md`
- `experiments/v1/geometry/EXP-005-residual-analysis/README.md`
- `experiments/v1/refinement/EXP-006-subpixel-refinement/README.md`

## Experiment Template

- `experiments/templates/EXPERIMENT_TEMPLATE.md`

## Benchmarking

- `benchmarks/`

## Data and Results

- `data/`
- `results/`

The exact ground-truth dataset path and final benchmark schema remain `[TBD]`.

---

# 125. Source Basis

This research note is grounded in the ChandraMap project materials and repository context supplied for this project, including:

- `SIH26166 Silarlar PS.pdf`
- `Aryan_SIH_26166_Lunar_Image_Correspondence_Feedback.pptx`
- `Aryan_Lunar_Image_Registration_Feedback.pdf`
- ChandraMap V1 experiment structure and terminology supplied in the project context

The supplied technical feedback explicitly establishes the importance of independent check points, the separation of fitting and evaluation, source-pixel RMSE, spatial coverage, ground-error qualification, stress-test design, and preservation of failure cases.

The project materials also establish that the first measurable milestone should be an end-to-end source/reference registration with numerical error measured on independent check points.

Specific ChandraMap ground-truth coordinates, checkpoint counts, annotation procedures, uncertainty values, dataset versions, and final benchmark definitions are intentionally marked `[TBD]` because they are not established by the supplied materials.
