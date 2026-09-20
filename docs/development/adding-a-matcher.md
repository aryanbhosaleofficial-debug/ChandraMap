````markdown
# Adding a Matcher

This document defines the engineering process for adding a new local matching method to **ChandraMap**, the lunar image correspondence and registration system for **SIH Problem Statement 26166**.

A matcher is responsible for proposing correspondences between a prepared **source representation** and a **reference representation**. It is not responsible for deciding whether those correspondences are geometrically correct.

The core principle is:

> **A matcher produces candidate correspondences. Geometric verification determines which candidates are valid.**

ChandraMap therefore keeps **local matching**, **geometric verification**, **sub-pixel refinement**, and **final transformation estimation** as separate stages.

---

## 1. Purpose

Adding a matcher should extend the existing matching architecture without creating a second registration pipeline.

The intended flow is:

```text
Source Sensor
      ↓
Sensor-Specific Preprocessing
      ↓
Registration-Friendly Representation
      ↓
Multi-Scale Preparation
      ↓
┌─────────────────────────────┐
│       LOCAL MATCHING        │
│                             │
│  Path A: SIFT               │
│  Path B: ALIKED + LightGlue │
│  Path C: LoFTR              │
│  Path D: Research Matcher   │
└──────────────┬──────────────┘
               ↓
        Candidate Matches
               ↓
        Geometric Verification
               ↓
             RANSAC
               ↓
        Verified Inliers
               ↓
      Sub-Pixel Refinement
               ↓
      Final Transformation
               ↓
        Registered Output
               ↓
         Evaluation
```
````

The matcher must therefore fit into the existing pipeline rather than bypass it.

---

# 2. Matcher vs Feature Extractor vs Verifier

One of the most important architectural rules in ChandraMap is to keep these concepts separate.

## Feature extractor

A feature extractor identifies image locations and/or produces descriptors.

Examples:

- SIFT
- ALIKED

Conceptually:

```text
Image
  ↓
Keypoints
  +
Descriptors
```

## Descriptor matcher

A descriptor matcher compares descriptors between two images.

For example:

```text
SIFT
 ↓
Keypoints + Descriptors
 ↓
Descriptor Matching
 ↓
Candidate Matches
```

## Learned sparse pipeline

A method such as ALIKED + LightGlue consists of two components:

```text
Image A ──→ ALIKED ──→ Features A ──┐
                                     ├──→ LightGlue → Matches
Image B ──→ ALIKED ──→ Features B ──┘
```

ALIKED is the feature extractor.

LightGlue is the matcher.

## Detector-free matcher

LoFTR is different.

It directly estimates correspondences between image pairs using a coarse-to-fine matching approach.

Conceptually:

```text
Image A ──┐
          ├──→ LoFTR ──→ Candidate Correspondences
Image B ──┘
```

It should therefore not be placed inside a generic "feature extraction" box alongside SIFT.

## Geometric verifier

RANSAC is not a matcher.

It evaluates whether candidate correspondences are consistent with a geometric model.

```text
Candidate Matches
        ↓
      RANSAC
        ↓
Verified Inliers
```

This distinction must remain explicit throughout the repository.

---

# 3. Current Matching Strategy

ChandraMap should maintain a small number of clearly defined matching paths rather than combining every available algorithm into one pipeline.

The current conceptual comparison is:

| Path | Components                       | Purpose                       |
| ---- | -------------------------------- | ----------------------------- |
| A    | SIFT + descriptor matching       | Explainable baseline          |
| B    | ALIKED + LightGlue               | Learned sparse matching       |
| C    | LoFTR                            | Detector-free matching        |
| D    | RIFT/CFOG-style research methods | Multimodal research direction |

The project feedback specifically recommends starting with SIFT and then testing one stronger learned path on the same image pairs.

Do not assume the newest or most complex matcher is automatically better for lunar imagery.

Pretrained terrestrial models are not automatically robust to lunar imagery, and extreme scale or illumination differences can still cause failures.

---

# 4. Before Adding a Matcher

Before implementing a new matcher, document its actual characteristics.

Collect:

- Method name
- Paper/reference
- Official implementation
- License
- Required dependencies
- Required hardware
- Input image requirements
- Input resolution requirements
- Expected image normalization
- Color/grayscale requirements
- Feature representation
- Descriptor representation
- Maximum image dimensions
- Matching strategy
- Confidence output
- Number of returned correspondences
- Runtime characteristics
- GPU requirements
- Known limitations
- Known domain assumptions

Do not implement a matcher simply because it performs well on a benchmark unrelated to lunar imagery.

---

# 5. Define the Matcher Contract

Every matcher should expose a consistent internal contract.

Conceptually:

```text
Matcher
├── name
├── version
├── configuration
├── capabilities
├── prepare()
├── match()
└── metadata
```

A matcher should accept prepared representations rather than raw mission products.

### Conceptual interface

```python
class Matcher:
    name: str

    def match(
        self,
        source,
        reference,
        source_metadata=None,
        reference_metadata=None,
    ):
        ...
```

The exact interface must follow the existing ChandraMap implementation.

Do not introduce a new interface if the repository already has an established matcher abstraction.

---

# 6. Input Contract

The matcher should clearly define what it expects.

Possible inputs include:

```text
Source image
Reference image
Source mask
Reference mask
Source metadata
Reference metadata
Scale information
Sensor information
```

However, not every matcher needs all of these.

The contract should distinguish:

### Required

Information without which the matcher cannot operate.

### Optional

Information that improves processing but is not required.

### Unsupported

Information that the matcher cannot use.

### Example

```text
Input:
    source_image
    reference_image

Optional:
    source_mask
    reference_mask
    sensor_id
    scale_metadata

Output:
    candidate_matches
    matcher_confidence
```

Do not make every matcher accept unnecessary parameters just to make interfaces look identical.

---

# 7. Raw Sensor Data Must Not Bypass Preprocessing

A matcher should normally receive a registration-friendly representation rather than an untouched mission product.

The intended architecture is:

```text
Raw Sensor Product
        ↓
Sensor Identification
        ↓
Sensor-Specific Processing
        ↓
Registration-Friendly Representation
        ↓
Matcher
```

Not:

```text
Raw OHRC / TMC-2 / IIRS
        ↓
Universal Matcher
```

Different sensors provide different physical information.

The project feedback explicitly recommends keeping sensor-specific preparation and only then producing a common structural representation.

---

# 8. Matcher Input Representations

The matcher may receive different representations depending on the sensor and experiment.

Possible representations include:

- Raw grayscale
- Normalized grayscale
- Gradient image
- Edge map
- Structural representation
- Selected IIRS spectral band
- PCA component
- Spectral composite
- Multi-scale representation

The matcher implementation should not silently change the representation.

Record the representation used for every benchmark.

For example:

```text
Matcher:
    SIFT

Source representation:
    gradient

Reference representation:
    gradient

Scale:
    reference pyramid level 3
```

This makes experiments reproducible.

---

# 9. Multi-Scale Requirements

A matcher must not assume that the two input images have identical physical resolution.

This is particularly important for ChandraMap because OHRC, TMC-2, IIRS and lunar reference products can have substantially different spatial scales.

The matching system should compare images at physically meaningful scales.

```text
Source GSD
     +
Reference GSD
     ↓
Comparable Scale
     ↓
Matcher
```

### Do not solve scale differences by arbitrary resizing

Incorrect:

```text
80 m/pixel image
       ↓
Upsample
       ↓
1 m/pixel image
       ↓
Claim 1 m detail
```

Upsampling changes pixel count, not physical information.

The reference side should instead be downsampled or represented through a scale pyramid when necessary.

The project feedback explicitly recommends using a reference pyramid or downsampling the higher-resolution side before coarse matching.

---

# 10. Matcher Registration

The matcher should be registered through the repository's existing matcher registry/factory mechanism.

Conceptually:

```text
Matcher Registry
│
├── sift
├── aliked_lightglue
├── loftr
└── new_matcher
```

Configuration may then select the matcher:

```yaml
matcher:
  name: new_matcher
  parameters:
    threshold: ...
```

The exact configuration format must follow the existing ChandraMap architecture.

Do not create matcher-specific hardcoded routing throughout the codebase.

---

# 11. Configuration

Matcher configuration should be explicit.

Potential configuration fields include:

```yaml
matcher:
  name: example_matcher

  device: cuda

  parameters:
    confidence_threshold: 0.5
    max_matches: 5000
```

These are examples only.

Only expose parameters that actually exist in the implementation.

Avoid configuration such as:

```yaml
magic_quality: 0.95
```

unless that parameter has a clearly defined mathematical or algorithmic meaning.

---

# 12. Device Handling

If the matcher supports CPU and GPU execution, device selection should be explicit.

For example:

```text
device:
    cpu
    cuda
```

The implementation should:

- Detect available hardware.
- Fail clearly when a required device is unavailable.
- Avoid silently falling back to a much slower mode without reporting it.
- Record the execution device in benchmark metadata.

Benchmark results should distinguish:

```text
Matcher
Model version
Device
Precision
Image size
Runtime
```

A GPU runtime should not be compared directly to a CPU runtime without clearly reporting the difference.

---

# 13. Determinism

If the matcher or preprocessing uses randomness, determine whether deterministic execution is possible.

Where supported:

```text
Random seed
+
Deterministic settings
```

should be recorded.

If deterministic execution is not guaranteed, document it.

This matters because repeated executions may produce different candidate matches.

---

# 14. Candidate Match Output

The matcher should return **candidate correspondences**, not verified matches.

Conceptually:

```text
CandidateMatch
├── source_point
├── reference_point
├── confidence
└── optional metadata
```

For example:

```text
source_point:
    (x1, y1)

reference_point:
    (x2, y2)

confidence:
    0.87
```

The exact schema must follow the repository's common correspondence representation.

### Important

A confidence score means:

> The matcher considers this correspondence likely according to its own scoring mechanism.

It does **not** mean:

> The correspondence is geometrically correct.

The feedback explicitly recommends calling these outputs **Candidate Matches** and allowing RANSAC/geometric verification to determine verified inliers.

---

# 15. Confidence Scores

If a matcher produces confidence values, preserve them.

Possible uses:

- Thresholding
- Ranking
- Debugging
- Benchmark analysis
- Visualization

But do not compare confidence values across different matchers as though they were calibrated probabilities unless calibration has actually been demonstrated.

For example:

```text
SIFT ratio score
≠
LightGlue confidence
≠
LoFTR confidence
```

These values may have completely different meanings.

---

# 16. Filtering Candidate Matches

A matcher may perform initial filtering.

Possible filters include:

- Descriptor ratio test
- Mutual/cross-check matching
- Confidence threshold
- Duplicate removal
- Invalid-point removal
- Mask filtering
- Image-boundary validation

For example:

```text
Raw Descriptor Matches
        ↓
Ratio Test
        ↓
Cross Check
        ↓
Candidate Matches
```

The filtering rules should be documented.

Do not hide major filtering logic inside a matcher implementation without recording it in benchmark configuration.

---

# 17. Geometric Verification Must Remain Separate

The matcher must not silently perform the entire registration task.

The expected flow is:

```text
MATCHER
   ↓
Candidate Matches
   ↓
RANSAC
   ↓
Initial Geometric Model
   ↓
Verified Inliers
```

The feedback explicitly identifies this separation as necessary because matcher confidence does not establish geometric correctness.

### Matcher responsibilities

The matcher should:

- Find candidate correspondences.
- Provide optional confidence.
- Provide diagnostic information.

### Verifier responsibilities

The verifier should:

- Estimate the geometric model.
- Reject outliers.
- Produce verified inliers.
- Calculate residuals.
- Evaluate spatial distribution.

Do not merge these responsibilities simply because it makes the implementation shorter.

---

# 18. RANSAC Integration

The matcher output should be compatible with the existing geometric verification stage.

Conceptually:

```text
Matcher
   ↓
Candidate Points
   ↓
RANSAC
   ↓
Affine / Homography / Appropriate Model
   ↓
Inliers
```

An affine transformation or homography can be a reasonable starting model for local, already map-projected imagery.

However, lunar terrain is not inherently planar, and raw imagery may contain sensor/viewing geometry effects.

The matcher documentation should therefore not claim:

```text
"Homography guarantees correct lunar registration."
```

Instead:

```text
"Homography is used as an initial local geometric model where appropriate."
```

---

# 19. Sub-Pixel Refinement

The matcher should not be responsible for final sub-pixel refinement unless it explicitly provides a validated refinement mechanism.

The standard ChandraMap flow is:

```text
Candidate Matches
       ↓
RANSAC
       ↓
Verified Inliers
       ↓
Sub-Pixel Tie-Point Refinement
       ↓
Refit Final Transform
```

This ordering is important.

The project feedback specifically recommends refining verified inliers and then refitting the final transformation.

Do not describe raw matcher confidence or coordinate precision as final registration accuracy.

---

# 20. Matcher-Specific Pipeline

Each matcher should have a clearly defined execution path.

## Path A — SIFT

```text
Source Image
     ↓
SIFT Keypoints
     ↓
SIFT Descriptors
     ↓
Descriptor Matching
     ↓
Ratio / Cross-Check Filtering
     ↓
Candidate Matches
     ↓
RANSAC
```

SIFT should remain the primary explainable baseline.

It provides a reference number against which more complex methods can be evaluated.

---

# 21. ALIKED + LightGlue

The architecture should be represented as:

```text
Source Image
     ↓
ALIKED
     ↓
Keypoints + Descriptors
     ↓
LightGlue
     ↑
Keypoints + Descriptors
     ↑
ALIKED
     ↑
Reference Image
```

Or more simply:

```text
Source ──→ ALIKED ──→ Features ──┐
                                  ├──→ LightGlue
Reference ─→ ALIKED ─→ Features ─┘
                                      ↓
                               Candidate Matches
```

Do not call ALIKED itself the matcher.

Do not call LightGlue a generic feature extractor.

The feedback specifically identifies ALIKED as the sparse feature extractor and LightGlue as the matcher.

### Important limitation

A pretrained learned matcher should not automatically be described as lunar-invariant.

Benchmark it on:

- Lunar imagery
- Different sensors
- Different Sun angles
- Different scales
- Difficult terrain

---

# 22. LoFTR

LoFTR should be treated as a detector-free matching path.

Conceptually:

```text
Source Image
      +
Reference Image
      ↓
    LoFTR
      ↓
Coarse-to-Fine Correspondences
      ↓
Candidate Matches
      ↓
RANSAC
```

Do not represent LoFTR as:

```text
Image
 ↓
Feature Extractor
 ↓
Descriptor
```

when documenting the architecture.

LoFTR can be useful where repeatable keypoints are weak or terrain is low-texture, but domain shift and extreme scale differences remain potential failure modes.

---

# 23. RIFT / CFOG-Style Methods

RIFT and CFOG-style approaches can be evaluated as research directions for difficult multimodal cases.

They may be relevant when:

- Sensor intensities differ strongly.
- Illumination changes are significant.
- Structural information is more stable than raw intensity.
- Cross-sensor correspondence is difficult.

However, they should not automatically be presented as drop-in replacements.

Implementation cost and actual benchmark performance must be considered.

The project feedback identifies these approaches as research baselines/directions rather than mandatory components.

---

# 24. Sensor-Aware Matcher Selection

The matcher may behave differently for different sensors.

Therefore, do not assume:

```text
One Matcher
   ↓
OHRC
TMC-2
IIRS
```

will produce equivalent results.

A better conceptual architecture is:

```text
                 ┌──→ OHRC Representation ──→ Matcher
Sensor Input ────┼──→ TMC-2 Representation ─→ Matcher
                 └──→ IIRS Representation ──→ Matcher
```

The same matcher can still be tested across sensors.

But the representation entering the matcher must be documented.

### Example

```text
OHRC
  ↓
Grayscale / structural
  ↓
SIFT

IIRS
  ↓
PCA / selected band / structural map
  ↓
SIFT
```

This does not mean SIFT is necessarily optimal for both.

It means the experiment is controlled.

---

# 25. Illumination Stress

The matcher must be evaluated under lunar illumination changes.

A brightness normalization step does not recreate different shadow geometry.

The same crater can have substantially different visual structure under different Sun angles.

The project feedback recommends comparing similar-illumination and very-different-illumination pairs directly.

At minimum evaluate:

```text
Case A
Similar Sun angle
      ↓
Matcher performance

Case B
Different Sun angle
      ↓
Matcher performance
```

Report the difference.

Do not describe a matcher as illumination-invariant unless experiments actually establish that property.

---

# 26. Modality Stress

For cross-sensor cases, evaluate the representation and matcher together.

Example:

```text
IIRS
 ↓
Selected Band / PCA / Composite
 ↓
Structural Representation
 ↓
Matcher
 ↓
Visible Reference
```

Compare multiple representations when useful:

```text
IIRS raw selected band
        vs
IIRS PCA
        vs
IIRS structural representation
```

Use the same benchmark pairs and evaluation protocol.

---

# 27. Scale Stress

A new matcher must be tested under scale differences.

For example:

```text
Source:
coarse lunar image

Reference:
high-resolution lunar image
```

Do not simply resize both images until their dimensions match and call the result scale-invariant.

Instead:

```text
Source GSD
     ↓
Reference Pyramid
     ↓
Comparable Effective Scale
     ↓
Matcher
```

The feedback recommends testing the benefit of coarse-to-fine scale handling directly.

---

# 28. Low-Feature Terrain

A matcher that performs well on crater-rich imagery may still fail on:

- Smooth terrain
- Repetitive terrain
- Low-texture regions
- Shadow-heavy regions

Create explicit low-feature test cases.

Measure:

- Candidate count
- Verified inliers
- Inlier ratio
- Spatial coverage
- Failure rate
- Runtime

Do not discard these cases merely because they make the benchmark harder.

---

# 29. Spatial Distribution

More matches do not automatically mean better registration.

Consider:

```text
Case A

● ● ● ●
● ● ● ●
● ● ● ●
● ● ● ●
```

versus:

```text
Case B

●●●●●●●
●
●
●
```

The second case may contain many matches but poor spatial coverage.

ChandraMap should therefore measure the distribution of verified inliers.

Possible metrics:

- Grid coverage
- Convex-hull coverage
- Spatial entropy
- Bounding-box coverage

The project feedback specifically recommends grid or convex-hull coverage.

---

# 30. Matcher Benchmarking

Every new matcher must be benchmarked against the existing baseline.

At minimum:

```text
Same image pairs
       ↓
 ┌──────────────┐
 │ SIFT         │
 └──────────────┘
       ↓
Metrics

Same image pairs
       ↓
 ┌──────────────┐
 │ New Matcher  │
 └──────────────┘
       ↓
Metrics
```

Do not compare matchers using different image pairs.

---

# 31. Required Metrics

At minimum measure:

| Metric                | Meaning                                 |
| --------------------- | --------------------------------------- |
| Candidate match count | Number of proposed correspondences      |
| Verified inlier count | Number surviving geometric verification |
| Inlier ratio          | Verified / candidate matches            |
| Grid coverage         | Spatial distribution                    |
| Convex-hull coverage  | Spatial extent                          |
| Check-point RMSE      | Independent registration error          |
| Ground error          | Physical error when meaningful          |
| Runtime               | Execution cost                          |
| Failure rate          | Robustness                              |

These metrics align with the project's evaluation framework.

---

# 32. Independent Evaluation

Do not evaluate the matcher only using the same points used to estimate the transformation.

Incorrect:

```text
Matcher
  ↓
RANSAC
  ↓
Inliers
  ↓
Fit Transform
  ↓
Evaluate Same Inliers
```

Preferred:

```text
Matcher
  ↓
RANSAC
  ↓
Verified Control Points
  ↓
Fit Transform
  ↓
Independent Check Points
  ↓
Evaluate
```

Otherwise the reported error can look better than actual registration quality.

---

# 33. Source-Pixel Accuracy

ChandraMap should report registration error in **source-image pixels first**.

For example:

```text
Check-point RMSE:
0.32 source pixels
```

Only convert this to metres when:

- Source GSD is known.
- Projection is appropriate.
- Reference truth supports the conversion.
- The physical interpretation is valid.

A pixel error has different physical meanings for different sensors.

For example:

```text
0.2 pixel on a fine-resolution sensor
≠
0.2 pixel on an ~80 m/pixel sensor
```

The project feedback explicitly highlights this distinction.

---

# 34. Runtime Benchmarking

Measure runtime consistently.

Record:

```text
Matcher
Model version
Device
Image dimensions
Number of features
Number of matches
Runtime
Memory where useful
```

Separate:

```text
Feature extraction time
+
Matching time
+
Post-processing time
```

when the implementation makes this possible.

For detector-free methods such as LoFTR, report the complete matching runtime.

Do not compare only one component of one matcher against the complete pipeline of another.

---

# 35. Failure Handling

A matcher must fail explicitly.

Possible failures:

```text
No features detected
No candidate matches
Insufficient candidate matches
Invalid image
Unsupported dimensions
Invalid image dtype
Missing dependency
Missing model weights
GPU unavailable
Out-of-memory
Numerical failure
```

The system should return a structured failure state where appropriate.

Example:

```text
MatcherResult
├── status: FAILED
├── reason: INSUFFICIENT_MATCHES
├── candidate_count: 3
└── diagnostics: ...
```

Do not silently return an empty list and pretend that the matcher succeeded.

---

# 36. Minimum Match Requirements

The matcher may expose a minimum candidate count, but geometric verification should remain responsible for determining whether the points form a valid transformation.

For example:

```text
candidate_count < minimum_required
        ↓
MATCHING_INSUFFICIENT
```

However:

```text
candidate_count = 500
```

does not mean the registration is valid.

Those 500 points may all be incorrect or spatially clustered.

---

# 37. Masks and No-Data Areas

If the input contains invalid regions, the matcher should avoid generating correspondences there.

Possible masks:

```text
Valid Image Mask
Shadow Mask
No-Data Mask
Cloud/Artifact Mask
Border Mask
```

The exact masks depend on the sensor.

If masks are supported, benchmark with and without them where relevant.

Do not silently interpret no-data values as valid image content.

---

# 38. Image Borders

Matchers can produce unstable features near image boundaries.

The implementation should define whether:

- Border features are allowed.
- A border exclusion margin is applied.
- Masks are used.

If a border margin exists, make it configurable and document it.

---

# 39. Large Images

Lunar imagery can be large.

A matcher should not assume that an entire full-resolution lunar product can always be loaded into GPU memory.

Possible strategy:

```text
Large Image
     ↓
Candidate Region / Tile
     ↓
Matcher
```

or:

```text
Large Image
     ↓
Overlapping Windows
     ↓
Local Matching
     ↓
Merge Correspondences
```

If tiling is implemented, document:

- Tile size
- Overlap
- Coordinate conversion
- Duplicate-match handling
- Boundary handling

Do not silently crop the image.

---

# 40. Coordinate Systems

Matcher outputs must clearly identify the coordinate system.

For example:

```text
source point:
    pixel coordinates

reference point:
    pixel coordinates
```

Do not return:

```text
x = 12.5
y = 20.3
```

without defining what those coordinates represent.

Possible coordinate systems:

- Pixel coordinates
- Normalized image coordinates
- Projected coordinates
- Geographic coordinates

The matcher should normally operate in image coordinates unless there is a strong architectural reason otherwise.

---

# 41. Coordinate Convention

Define:

```text
Origin:
    top-left

x:
    image columns

y:
    image rows
```

or whatever convention the repository already uses.

Be consistent across:

- Matcher
- RANSAC
- Refinement
- Evaluation
- Visualization

A coordinate convention mismatch can produce apparently plausible but incorrect registration.

---

# 42. Visualization

Every matcher should have a debugging visualization where practical.

Useful visualizations include:

### Candidate matches

```text
Source                 Reference

●───────────────●
●────────────────●
●──────────●
```

### Verified matches

```text
Source                 Reference

●───────────────●
                  X
●────────────────●
```

### Rejected matches

Use a separate visualization for rejected outliers.

### Spatial coverage

Display the locations of verified inliers across the source image.

This makes it easier to detect:

- Feature clustering
- Border effects
- Wrong-region matches
- Repeated terrain confusion
- Geometry problems

---

# 43. Debug Artifacts

For benchmark runs, consider saving:

```text
candidate_matches.json
verified_inliers.json
match_visualization.png
inlier_visualization.png
residual_vectors.png
registered_preview.png
metrics.json
configuration.yaml
```

The exact artifact structure should follow the existing benchmark architecture.

The goal is reproducibility and diagnosis, not generating unnecessary files.

---

# 44. Unit Tests

Every matcher integration must have unit tests.

Test:

- Matcher registration
- Configuration parsing
- Input validation
- Output schema
- Coordinate format
- Empty input
- Invalid image
- Small image
- No-feature case
- Insufficient-match case
- Confidence handling
- Device handling
- Deterministic behavior where applicable

---

# 45. Integration Tests

At minimum:

```text
Prepared Source
      ↓
Prepared Reference
      ↓
Matcher
      ↓
Candidate Matches
      ↓
RANSAC
      ↓
Verified Inliers
```

The test should verify that the matcher output can be consumed by the existing geometric verification stage.

A stronger end-to-end test is:

```text
Sensor Input
      ↓
Preprocessing
      ↓
Representation
      ↓
Matcher
      ↓
RANSAC
      ↓
Sub-Pixel Refinement
      ↓
Final Transform
```

---

# 46. Regression Tests

Adding a matcher must not change the behavior of existing matchers.

Run existing matcher tests:

```text
SIFT
ALIKED + LightGlue
LoFTR
```

where implemented.

Verify:

- Existing configurations remain valid.
- Existing benchmark scripts still run.
- Existing output schemas remain compatible.
- Existing sensor paths are unaffected.

---

# 47. Matcher Benchmark Dataset

Use a fixed benchmark set for matcher comparison.

The dataset should contain:

- Known overlapping pairs
- Different lunar regions
- Different illumination conditions
- Different scales
- Different sensors
- Difficult terrain
- Low-feature terrain

Avoid changing the benchmark set every time a new matcher is added.

Otherwise results cannot be compared fairly.

---

# 48. Benchmark Matrix

A useful benchmark matrix is:

| Test                | SIFT | New Matcher | Full Pipeline |
| ------------------- | ---: | ----------: | ------------: |
| Easy pair           |    ✓ |           ✓ |             ✓ |
| Sun-angle stress    |    ✓ |           ✓ |             ✓ |
| Scale stress        |    ✓ |           ✓ |             ✓ |
| Modality stress     |    ✓ |           ✓ |             ✓ |
| Geometry stress     |    ✓ |           ✓ |             ✓ |
| Low-feature terrain |    ✓ |           ✓ |             ✓ |

The project feedback recommends comparing the same image pairs using a SIFT baseline, a stronger matcher, and the full sensor-aware/multi-scale pipeline.

---

# 49. Do Not Add Decorative Scores

Do not introduce unsupported scores such as:

```text
Matcher Quality: 92%
Reliability: ★★★★★
Lunar Robustness: 95%
```

unless those numbers have a documented measurement methodology.

The project feedback specifically warns against presenting unmeasured percentages or star ratings as experimental results.

Use measurable metrics instead:

```text
Candidate matches: 842
Verified inliers: 317
Inlier ratio: 37.6%
Grid coverage: 13/16
Check-point RMSE: 0.42 px
Runtime: 1.83 s
```

These numbers must come from actual experiments.

---

# 50. Matcher Selection

ChandraMap should not automatically select a matcher because it has the highest number of candidate matches.

Selection should consider:

- Verified inlier count
- Inlier ratio
- Spatial coverage
- Independent RMSE
- Failure rate
- Runtime
- Memory requirements
- Sensor compatibility
- Illumination robustness
- Scale robustness
- Modality robustness

A matcher producing 5,000 incorrect matches is worse for registration than one producing 200 geometrically valid, well-distributed matches.

Do not reduce matcher selection to one metric.

---

# 51. Recommended Development Order

When introducing a new matcher, follow this order.

### Step 1 — Establish baseline

Run:

```text
SIFT
  ↓
RANSAC
  ↓
Transform
  ↓
Metrics
```

### Step 2 — Implement matcher adapter

```text
New Matcher
    ↓
Common Matcher Interface
```

### Step 3 — Validate candidate output

Check:

- Point coordinates
- Count
- Confidence
- Image bounds
- Data types

### Step 4 — Connect to RANSAC

```text
New Matcher
     ↓
Candidate Matches
     ↓
Existing RANSAC
```

### Step 5 — Evaluate same image pairs

Do not change the dataset.

### Step 6 — Add difficult cases

Test:

- Illumination
- Scale
- Modality
- Geometry
- Low-feature terrain

### Step 7 — Evaluate independent registration error

Use held-out check points.

### Step 8 — Optimize only after correctness

Only after the matcher is producing reliable results should you optimize:

- GPU execution
- Batching
- Tiling
- Memory
- Caching
- Parallel execution

---

# 52. Pull Request Requirements

A matcher pull request should contain:

### Implementation

- [ ] Matcher implementation
- [ ] Matcher adapter/interface
- [ ] Configuration
- [ ] Registry entry
- [ ] Dependency declaration
- [ ] Model-weight handling where applicable
- [ ] Device handling

### Input

- [ ] Input requirements documented
- [ ] Supported representations documented
- [ ] Image-size constraints documented
- [ ] Mask behavior documented
- [ ] Scale assumptions documented

### Output

- [ ] Candidate-match schema documented
- [ ] Confidence semantics documented
- [ ] Coordinate convention documented
- [ ] Failure states documented

### Geometry

- [ ] Existing RANSAC integration verified
- [ ] Verified inliers distinguished from candidates
- [ ] Residual analysis tested
- [ ] Sub-pixel refinement remains a separate stage

### Evaluation

- [ ] SIFT baseline included
- [ ] Same benchmark pairs used
- [ ] Candidate count measured
- [ ] Inlier count measured
- [ ] Inlier ratio measured
- [ ] Spatial coverage measured
- [ ] Independent RMSE measured
- [ ] Runtime measured
- [ ] Failure rate measured

### Stress tests

- [ ] Easy pair
- [ ] Sun-angle stress
- [ ] Scale stress
- [ ] Modality stress
- [ ] Geometry stress
- [ ] Low-feature stress

### Documentation

- [ ] Matcher documentation updated
- [ ] Architecture documentation updated if required
- [ ] Benchmark documentation updated
- [ ] Configuration documentation updated
- [ ] Changelog updated
- [ ] Known limitations documented

---

# 53. Common Mistakes

## 53.1 Calling every algorithm a feature extractor

Incorrect:

```text
Feature Extraction
├── SIFT
├── ORB
├── ALIKED
└── LoFTR
```

This hides important architectural differences.

Prefer:

```text
LOCAL MATCHING

Path A
SIFT

Path B
ALIKED + LightGlue

Path C
LoFTR
```

---

## 53.2 Calling candidate matches "verified matches"

Incorrect:

```text
Matcher
 ↓
High Confidence Matches
```

Prefer:

```text
Matcher
 ↓
Candidate Matches
 ↓
RANSAC
 ↓
Verified Inliers
```

---

## 53.3 Using matcher confidence as registration accuracy

Incorrect:

```text
Confidence = 0.95
↓
Registration accuracy = 95%
```

These are not equivalent.

---

## 53.4 Comparing different image pairs

Incorrect:

```text
SIFT:
easy dataset

New Matcher:
hard dataset
```

The comparison is invalid.

Use the same pairs.

---

## 53.5 Changing preprocessing between benchmark runs without documenting it

If SIFT receives raw grayscale while the new matcher receives a heavily engineered structural representation, the experiment is not simply a matcher comparison.

Record the complete processing configuration.

---

## 53.6 Ignoring scale

A matcher can fail because the images are at incompatible physical scales.

Do not immediately conclude that the matcher itself is weak.

Investigate:

```text
GSD
↓
Reference pyramid
↓
Effective scale
↓
Matcher
```

---

## 53.7 Assuming pretrained models are lunar-invariant

A learned matcher trained on terrestrial imagery may encounter significant domain shift on lunar imagery.

Measure it.

---

## 53.8 Measuring only match count

More matches are not necessarily better.

Always examine:

```text
Matches
+
Inliers
+
Inlier Ratio
+
Coverage
+
Independent RMSE
```

---

## 53.9 Evaluating on fitting points

Do not fit a transformation and report the error on exactly those points as the only accuracy measurement.

Use independent check points.

---

## 53.10 Letting the matcher perform hidden warping

A matcher should not secretly apply an aggressive geometric warp that makes the final overlay look good.

The geometric model belongs in the verification/registration stages.

---

## 53.11 Optimizing before establishing correctness

Do not spend time on:

- TensorRT
- CUDA kernels
- aggressive batching
- model quantization
- distributed matching

before establishing that the matcher produces reliable correspondences.

First obtain measurable correctness.

---

## 53.12 Adding every available matcher

There is no benefit in maintaining ten matchers simply because ten algorithms exist.

A smaller benchmarked set is easier to:

- Maintain
- Explain
- Test
- Compare
- Reproduce

The project feedback explicitly recommends starting with SIFT and testing one stronger learned path rather than running every algorithm in one pipeline.

---

# 54. Example: Adding a New Matcher

Assume a hypothetical matcher named:

```text
ExampleMatcher
```

### Step 1 — Register it

```text
Matcher Registry
        ↓
example_matcher
```

### Step 2 — Define configuration

```yaml
matcher:
  name: example_matcher

  parameters:
    threshold: 0.5
```

### Step 3 — Implement adapter

```text
ChandraMap Representation
        ↓
ExampleMatcher Adapter
        ↓
ExampleMatcher
        ↓
Raw Correspondences
```

### Step 4 — Normalize output

Convert output into the common representation:

```text
CandidateMatch
├── source_point
├── reference_point
└── confidence
```

### Step 5 — Connect existing verification

```text
Candidate Matches
       ↓
Existing RANSAC
       ↓
Verified Inliers
```

Do not implement a second RANSAC system unless there is a demonstrated architectural reason.

### Step 6 — Evaluate

Run:

```text
ExampleMatcher
       +
SIFT
```

on exactly the same benchmark pairs.

### Step 7 — Stress test

Evaluate:

```text
Easy
Sun-angle
Scale
Modality
Geometry
Low-feature
```

### Step 8 — Record results

Example structure:

```text
Matcher:
ExampleMatcher

Candidate matches:
...

Verified inliers:
...

Inlier ratio:
...

Grid coverage:
...

Check-point RMSE:
...

Runtime:
...

Failure rate:
...
```

Only real benchmark measurements should populate these fields.

---

# 55. Acceptance Criteria

A matcher should not be considered integrated merely because it executes successfully.

The matcher must satisfy:

1. It follows the existing matcher abstraction.
2. It has documented input requirements.
3. It has documented output semantics.
4. It returns candidate correspondences using the common schema.
5. Confidence values are clearly defined where available.
6. It integrates with existing geometric verification.
7. It does not silently perform unapproved geometric correction.
8. It handles invalid inputs explicitly.
9. It has unit tests.
10. It has integration tests.
11. It has regression coverage where applicable.
12. It is benchmarked against SIFT.
13. It uses the same benchmark pairs for comparison.
14. It is evaluated under difficult lunar conditions.
15. Independent check-point evaluation is available.
16. Runtime is measured.
17. Failure cases are documented.
18. Known limitations are documented.
19. Documentation is updated.
20. No unsupported accuracy or robustness claims are introduced.

---

# 56. Matcher Support Status

Use explicit support levels.

| Status               | Meaning                                                                                |
| -------------------- | -------------------------------------------------------------------------------------- |
| **Experimental**     | Initial implementation exists and is being investigated                                |
| **Benchmarking**     | Implementation is stable enough for controlled comparison                              |
| **Supported**        | Integration, tests, and benchmark evidence are complete                                |
| **Production-Ready** | Supported matcher meets the project's defined operational and reliability requirements |

Do not mark a matcher as supported simply because it has produced a visually convincing result.

---

# 57. Recommended ChandraMap Matcher Architecture

The intended architecture is:

```text
                    SENSOR INPUT
                         │
                         ▼
             SENSOR-SPECIFIC PROCESSING
                         │
                         ▼
             COMMON STRUCTURAL / IMAGE
                   REPRESENTATION
                         │
                         ▼
                MULTI-SCALE PREPARATION
                         │
             ┌───────────┼───────────┐
             │           │           │
             ▼           ▼           ▼
           SIFT     ALIKED+LG      LoFTR
             │           │           │
             └───────────┼───────────┘
                         │
                         ▼
                CANDIDATE MATCHES
                         │
                         ▼
                    RANSAC / MODEL
                    VERIFICATION
                         │
                         ▼
                  VERIFIED INLIERS
                         │
                         ▼
               SUB-PIXEL REFINEMENT
                         │
                         ▼
                 REFIT FINAL MODEL
                         │
                         ▼
                REGISTERED OUTPUT
                         │
                         ▼
                     METRICS
```

This keeps the architecture modular:

```text
Sensor
   ↓
Representation
   ↓
Matcher
   ↓
Verifier
   ↓
Refiner
   ↓
Registration
   ↓
Evaluation
```

Each stage can therefore be tested independently.

---

# 58. Final Engineering Rules

When adding a matcher to ChandraMap, follow these rules:

### Rule 1 — Start with the baseline

SIFT provides the initial comparison point.

### Rule 2 — Keep algorithm roles explicit

Do not mix feature extraction, matching, verification, and refinement into one undocumented component.

### Rule 3 — Return candidate matches

Do not call matcher output verified matches.

### Rule 4 — Let geometry verify correspondence

RANSAC or another appropriate geometric verification stage determines whether candidates are consistent.

### Rule 5 — Refine only verified points

The standard sequence is:

```text
Candidate Matches
→ RANSAC
→ Inliers
→ Sub-Pixel Refinement
→ Final Transform
```

### Rule 6 — Compare at meaningful physical scales

Do not confuse resizing with resolution recovery.

### Rule 7 — Test lunar conditions

At minimum investigate:

- Illumination
- Scale
- Modality
- Geometry
- Low-feature terrain

### Rule 8 — Use independent evaluation

Do not judge the transformation only on points used to estimate it.

### Rule 9 — Measure spatial coverage

A large cluster of matches in one region is not equivalent to well-distributed correspondence.

### Rule 10 — Measure before claiming improvement

Use:

- Inlier count
- Inlier ratio
- Coverage
- Check-point RMSE
- Runtime
- Failure rate

### Rule 11 — Do not assume terrestrial models are lunar-invariant

Validate learned methods on actual lunar data.

### Rule 12 — Do not add complexity without evidence

A matcher earns its place by improving measured performance on relevant cases.

---

# 59. Final Matcher Integration Flow

The complete ChandraMap matcher lifecycle is:

```text
New Matcher Proposal
        ↓
Algorithm / License / Dependency Review
        ↓
Define Matcher Contract
        ↓
Implement Adapter
        ↓
Register Matcher
        ↓
Validate Input Representation
        ↓
Run Candidate Matching
        ↓
Normalize Candidate Output
        ↓
RANSAC / Geometric Verification
        ↓
Verified Inliers
        ↓
Spatial Coverage Analysis
        ↓
Sub-Pixel Refinement
        ↓
Refit Final Transformation
        ↓
Independent Check-Point Evaluation
        ↓
Benchmark Against SIFT
        ↓
Stress Testing
        ↓
Runtime + Failure Analysis
        ↓
Documentation
        ↓
Tests
        ↓
Support Status
```

The standard for adding a matcher is therefore not:

> **"Can the algorithm find some matching points?"**

It is:

> **"Can the algorithm produce reproducible, well-defined candidate correspondences that integrate cleanly with ChandraMap's geometric verification and refinement pipeline, and does controlled evaluation demonstrate useful behavior on the lunar conditions for which it is intended?"**

```

```
