# Adding a Sensor

This document defines the engineering process for adding support for a new sensor to **ChandraMap**, the lunar image correspondence and registration system for SIH Problem Statement 26166.

Adding a sensor means integrating its **data model, metadata, geometry, preprocessing, representation, scale strategy, matching path, validation, benchmarking, and documentation** into the existing correspondence pipeline.

A sensor is **not considered supported merely because its image files can be opened**.

ChandraMap is intentionally sensor-aware:

> **Sensor-specific preparation happens first. Only then should the system produce a registration-friendly/common structural representation.**

The common representation exists to make stable terrain structure comparable across sensors; it does not imply that all sensors contain equivalent physical information.

---

## 1. Overview

The complete lifecycle of adding a sensor is:

```text
Sensor Definition
        ↓
Metadata Contract
        ↓
Data Loader
        ↓
Input Validation
        ↓
Sensor-Specific Preprocessing
        ↓
Registration-Friendly Representation
        ↓
Multi-Scale Integration
        ↓
Global Retrieval (when required)
        ↓
Local Matching
        ↓
Geometric Verification
        ↓
Sub-Pixel Refinement
        ↓
Evaluation
        ↓
Documentation
        ↓
Tests
        ↓
Acceptance
```

A new sensor must fit into the existing ChandraMap architecture rather than introduce an independent parallel registration system.

### Core pipeline

```text
Offline Reference Preparation
        ↓
Reference Images / Mosaic
        ↓
Tiling + Multi-Scale Representation
        ↓
Global Descriptor / Retrieval Index
        ↓
Metadata / Geospatial Index
        ↓
Searchable Reference Database
        ↓
                ┌──────────────────────────┐
                │     Online Sensor Input  │
                └────────────┬─────────────┘
                             ↓
                    Sensor Selection
                             ↓
                    Sensor-Specific
                      Preprocessing
                             ↓
                 Common Structural
                    Representation
                             ↓
               Multi-Scale Search /
                   Normalization
                             ↓
                Global Retrieval
                  (when required)
                             ↓
                  Top-K Candidates
                             ↓
                    Local Matching
                             ↓
                  Candidate Matches
                             ↓
               RANSAC / Verification
                             ↓
                 Verified Inliers
                             ↓
              Sub-Pixel Refinement
                             ↓
                  Final Transform
                             ↓
                 Registration /
                   Georeferencing
                             ↓
                   Quality Evaluation
                             ↓
                  Registered Output
                             ↓
              Optional Mosaic / Product
```

The correspondence task remains the primary output. A final lunar mosaic is a downstream demonstration and must not hide poor correspondence quality.

---

## 2. Before Adding a Sensor

Before implementing code, collect the actual technical characteristics of the sensor and its available products.

Do not implement a sensor from assumptions based only on its name or a representative image.

### Sensor identity

Record:

- Mission
- Instrument
- Official sensor name
- Internal ChandraMap sensor identifier
- Sensor type
- Imaging modality
- Whether the instrument is camera-like, spectrometer-based, radar-based, or another modality

For the current project, the primary Chandrayaan-2 source instruments are:

| Sensor | Mission       | General role                                      |
| ------ | ------------- | ------------------------------------------------- |
| OHRC   | Chandrayaan-2 | Very high-resolution visible panchromatic imagery |
| TMC-2  | Chandrayaan-2 | Panchromatic terrain imagery                      |
| IIRS   | Chandrayaan-2 | Imaging IR hyperspectral data                     |

Do not refer to these instruments as Chandrayaan-3 instruments.

### Image characteristics

Document:

- Spatial resolution
- GSD / pixel scale
- Image dimensions
- Bit depth
- Number of bands
- Spectral range where applicable
- Dynamic range
- No-data representation
- Valid-pixel representation
- Expected image orientation

Remember that pixel count and physical resolution are different properties.

### Geometry

Document, where available:

- Map projection
- Coordinate reference system
- Geographic coordinate representation
- Image footprint
- Viewing geometry
- Incidence angle
- Emission angle
- Phase angle
- Sun angle
- Acquisition geometry
- Sensor position
- Look direction
- Orthorectification state
- DEM or terrain information

### Metadata

Determine which metadata is:

- Required
- Optional
- Derived
- Product-specific
- Unavailable

Record:

- Product identifier
- Acquisition timestamp
- Calibration information
- Geolocation information
- Pixel scale
- Footprint
- Projection
- Geometry information

### Data formats

Document:

- Official product format
- Supported raw format
- Supported derived products
- Browse products
- Calibration products
- Metadata files
- Auxiliary files

The implementation should be based on the actual product specification available to the project.

---

## 3. Define the Sensor Contract

Every supported sensor should have a clear conceptual contract.

The contract describes what ChandraMap can expect from the sensor and what the sensor integration provides to the rest of the pipeline.

| Field              | Description                                          |
| ------------------ | ---------------------------------------------------- |
| `sensor_id`        | Stable ChandraMap identifier                         |
| `mission_id`       | Mission associated with the instrument               |
| `sensor_name`      | Official instrument name                             |
| `modality`         | Imaging modality                                     |
| `product_types`    | Supported product categories                         |
| `input_format`     | Supported input representation                       |
| `gsd`              | Nominal or product-specific ground sampling distance |
| `bands`            | Band/spectral information                            |
| `geometry`         | Available viewing and acquisition geometry           |
| `projection`       | Available map projection information                 |
| `metadata`         | Required and optional metadata                       |
| `preprocessing`    | Sensor-specific preparation capabilities             |
| `representation`   | Registration-friendly representation                 |
| `scale_strategy`   | Multi-scale handling                                 |
| `matching_paths`   | Supported local matching approaches                  |
| `limitations`      | Known physical/technical limitations                 |
| `supported_stages` | Pipeline stages supported by the sensor              |

### Contract requirements

A sensor contract should answer:

1. What data can be loaded?
2. What metadata can be trusted?
3. What preprocessing is required?
4. What representation reaches the common registration pipeline?
5. What spatial scales are meaningful?
6. What matching methods are applicable?
7. What outputs can be generated?
8. What capabilities are unavailable?

If the repository already contains a sensor interface, schema, configuration model, registry, or adapter contract, **use that existing architecture**.

Do not create a parallel sensor architecture merely because it is convenient for one new sensor.

### Important distinction

A sensor contract describes **capabilities**, not promises.

For example:

```text
IIRS
├── Raw hyperspectral data
├── Selected-band representation
├── PCA/composite representation
└── Structural 2D representation
```

does not mean that all representations must be supported immediately.

Unsupported capabilities should be explicit.

---

## 4. Sensor Registration

After defining the sensor contract, make the sensor known to ChandraMap.

The registration mechanism should be explicit and deterministic.

Conceptually:

```text
Input Product
     ↓
Identify Sensor
     ↓
Validate Sensor
     ↓
Resolve Configuration
     ↓
Select Sensor Path
     ↓
Run Sensor-Specific Processing
```

Sensor registration should cover:

- Sensor identifier
- Mission identifier
- Configuration
- Product types
- Capability declaration
- Input routing
- Validation
- Default preprocessing path
- Registration representation

If the repository already provides a registry, factory, dispatcher, or configuration-driven routing mechanism, extend it rather than creating another mechanism.

### Illustrative configuration

The following is conceptual and must be adapted to the repository's actual configuration system:

```yaml
sensor:
  id: example_sensor
  mission: example_mission
  modality: optical
  products:
    - example_product

  processing:
    representation: structural
    multiscale: true

  capabilities:
    geolocation: true
    illumination_metadata: true
    local_matching: true
```

This is an architectural example, not a requirement to introduce YAML if the repository uses another configuration format.

---

## 5. Data Loading

The sensor loader is responsible for converting the official product into a validated internal representation without unnecessarily discarding information.

### Loader responsibilities

The loader should handle:

- File existence
- File format validation
- Product validation
- Image extraction
- Band extraction
- Metadata extraction
- No-data handling
- Dimension validation
- Geospatial metadata extraction
- Calibration metadata extraction
- Product identification

### Metadata preservation

The loader should preserve metadata required by downstream processing.

Potential downstream consumers may need:

- Pixel scale
- GSD
- Footprint
- Projection
- Latitude/longitude
- Viewing geometry
- Illumination geometry
- Acquisition time
- Product identifier
- Calibration information

Do not load only the pixel array and silently discard the rest.

### Invalid inputs

The loader should fail explicitly for conditions such as:

```text
Unsupported product
Malformed product
Missing required image data
Invalid dimensions
Unsupported band structure
Corrupted metadata
Missing required geospatial information
```

Where metadata is optional, distinguish:

```text
AVAILABLE
```

from:

```text
UNAVAILABLE
```

Do not fabricate values to make the pipeline appear complete.

---

## 6. Sensor-Specific Preprocessing

Sensor-specific preprocessing is one of the most important parts of sensor integration.

ChandraMap must **not force every sensor through one identical preprocessing path**.

The architecture is:

```text
Raw Sensor Product
        ↓
Sensor-Specific Preparation
        ↓
Validated Physical Representation
        ↓
Common Registration Representation
```

### Camera-like sensors

For sensors such as OHRC and TMC-2, preprocessing may include:

- Use of calibrated/standard products
- No-data masking
- Light denoising
- Contrast normalization
- Dynamic-range normalization
- Edge-preserving processing
- Grayscale preparation
- Gradient computation
- Structural representation
- Map projection where appropriate
- Geometry metadata preservation

Processing should avoid destroying terrain structures that local matching depends on.

### OHRC

OHRC is very high-resolution visible panchromatic lunar imagery.

Its high spatial detail makes it suitable for fine terrain correspondence, but preprocessing should still preserve the official product's physical and geometric metadata.

The exact GSD should be taken from the applicable product/documentation rather than hardcoded from a generic statement.

### TMC-2

TMC-2 provides panchromatic terrain imagery at substantially coarser spatial resolution than OHRC.

Its broader terrain structure can provide useful correspondence with reference imagery.

Where available, map-projection and terrain information should be preserved.

### IIRS

IIRS must be treated differently.

It is an imaging IR spectrometer rather than a conventional single-band camera.

A hyperspectral cube should not automatically be passed directly into a standard 2D feature matcher.

A possible path is:

```text
IIRS Hyperspectral Cube
        ↓
Band Quality / Validity Check
        ↓
Selected Bands OR PCA / Composite
        ↓
2D Representation
        ↓
Structural Representation
        ↓
Registration Pipeline
```

Possible initial representations include:

- Selected spectral band
- Band combination
- PCA component
- Composite
- Gradient representation
- Edge/structural representation

The original spectral data should remain available separately where the product and architecture permit it.

### Important rule

> **Do not automatically force a hyperspectral cube into the same preprocessing route as a panchromatic image.**

---

## 7. Common Registration Representation

The goal of a common representation is **not** to make every sensor identical.

The goal is to preserve stable terrain information that can be compared across sensors.

Possible representations include:

- Grayscale
- Normalized intensity
- Gradients
- Edges
- Phase/structural representations
- Selected spectral bands
- PCA components
- Spectral composites
- Terrain-derived representations

Conceptually:

```text
OHRC ────────┐
             │
TMC-2 ───────┼──→ Sensor-Specific Preparation
             │              ↓
IIRS ────────┘       Registration Representation
                            ↓
                    Common Matching Path
```

The representation should be selected through experimentation.

There is no assumption that one representation is universally optimal for every:

- Sensor
- Illumination condition
- Terrain type
- Reference sensor
- Spatial scale
- Modality

A new representation should therefore be benchmarked rather than declared superior without evidence.

---

## 8. Scale and GSD Handling

Scale handling must be based on **physical information**, not only image dimensions.

### Important concepts

Distinguish between:

- Pixel dimensions
- Pixel scale
- GSD
- Physical ground resolution
- Effective spatial resolution
- Information content

### Critical rule

> **Resizing is not resolution recovery.**

Upsampling changes pixel count.

It does **not** recover missing ground detail.

Therefore:

```text
80 m/pixel source
        ↓
Resize to 1 m/pixel
        ↓
More pixels
        ≠
More physical terrain information
```

### Recommended strategy

1. Determine source GSD.
2. Determine reference GSD.
3. Build or select an appropriate reference pyramid.
4. Compare images at physically meaningful effective scales.
5. Perform coarse search at comparable scales.
6. Refine only where the source sensor contains sufficient information.
7. Report error in source-image pixels first.
8. Convert to metres only when GSD, projection, and reference truth justify the conversion.

### Example scale relationships

| Sensor  |                          Approximate scale context | Registration implication                         |
| ------- | -------------------------------------------------: | ------------------------------------------------ |
| OHRC    |            ~0.25–0.32 m/pixel depending on product | Fine terrain correspondence may be meaningful    |
| TMC-2   |                                         ~5 m/pixel | Broader terrain structure is more appropriate    |
| IIRS    |                                        ~80 m/pixel | Fine terrain claims are physically inappropriate |
| LRO NAC | Often ~0.5–2 m/pixel depending on product/geometry | Useful high-resolution reference                 |

These values are contextual rather than universal constants. Product metadata should remain authoritative.

### Example

Suppose:

```text
Source = IIRS
Source GSD ≈ 80 m/pixel

Reference = LRO NAC
Reference GSD ≈ 1 m/pixel
```

Do not:

```text
IIRS → resize to 1 m/pixel → claim 1 m spatial detail
```

Instead:

```text
LRO NAC
   ↓
Reference pyramid / downsampling
   ↓
Comparable effective scale
   ↓
Coarse correspondence
   ↓
Refinement only within physically supported limits
```

---

## 9. Illumination and Viewing Geometry

Lunar illumination is not simply a brightness-normalization problem.

A change in Sun angle can change:

- Shadow position
- Shadow length
- Shadow visibility
- Crater appearance
- Ridge appearance
- Relative local contrast

Therefore:

> **Brightness normalization does not recreate missing shadow geometry.**

### Relevant metadata

When available, preserve:

- Sun azimuth
- Sun elevation
- Incidence angle
- Emission angle
- Phase angle
- Viewing geometry
- Acquisition time

### Useful structural features

Depending on the sensor and scene, investigate:

- Gradients
- Edges
- Crater rims
- Ridge lines
- Relative geometry
- Phase-based representations
- Shadow masks

Structural information may remain more useful than raw intensity when illumination differs substantially.

### If illumination metadata exists

Use it to:

1. Describe the acquisition conditions.
2. Group or stratify benchmark pairs.
3. Investigate whether photometric correction is useful.
4. Design illumination stress tests.
5. Avoid incorrectly treating different illumination geometry as ordinary contrast variation.

### If illumination metadata is unavailable

The system should:

- Preserve that fact.
- Avoid fabricating geometry.
- Use image-based structural representations where appropriate.
- Report the missing metadata as a limitation.
- Evaluate performance on difficult illumination pairs.

### Required experiment

At minimum, investigate:

```text
Same region
   ├── Similar illumination
   └── Very different illumination
```

Report the performance difference rather than hiding the degradation.

---

## 10. Multi-Scale Integration

The new sensor must be integrated into ChandraMap's multi-scale pipeline.

The scale strategy should be driven by physical information.

### Reference pyramid

A reference dataset may be represented as:

```text
Level 0 ─ Highest resolution
   ↓
Level 1
   ↓
Level 2
   ↓
Level 3 ─ Coarse representation
```

A source sensor should enter the pyramid at a level appropriate to its effective spatial information.

### Coarse-to-fine strategy

```text
Sensor Input
     ↓
Determine Effective GSD
     ↓
Select Comparable Reference Scale
     ↓
Coarse Search
     ↓
Candidate Regions
     ↓
Finer Search
     ↓
Local Matching
     ↓
Geometric Verification
```

### Avoid unnecessary refinement

If a sensor cannot physically resolve a particular terrain feature, the pipeline should not continue refining against that feature merely because the reference image contains it.

For example, a very coarse IIRS representation should not be expected to reproduce tiny features visible in NAC or OHRC.

### Key principle

> **Compare information, not pixel count.**

---

## 11. Global Retrieval

Global retrieval is **optional**, not mandatory.

If reliable:

- Latitude/longitude
- Footprint
- Projection
- Approximate geolocation

is already available, use that information to restrict the search.

If location is unknown, global retrieval becomes more important.

### Offline reference preparation

```text
Reference Images
      ↓
Tiles
      ↓
Multi-Scale Tiles
      ↓
Global Descriptors
      ↓
FAISS Index
      +
Metadata
```

### Online processing

```text
Source Sensor Image
      ↓
Sensor-Specific Processing
      ↓
Registration Representation
      ↓
Global Descriptor
      ↓
FAISS Search
      ↓
Top-K Candidate Regions
      ↓
Local Matching
```

### Important distinction

**Global retrieval features** and **local matching features** solve different problems.

| Stage                 | Purpose                                          |
| --------------------- | ------------------------------------------------ |
| Global descriptor     | Find the likely lunar region                     |
| Local feature/matcher | Establish precise image-to-image correspondences |

Do not use local matching over the entire Moon when reliable geospatial metadata can already reduce the search area.

---

## 12. Local Matching

A new sensor must expose a registration-friendly representation compatible with the selected local matching path.

Possible approaches include:

### Baseline

```text
SIFT
  ↓
Descriptor Matching
  ↓
Candidate Matches
```

SIFT provides a simple, interpretable baseline against which more complex methods can be compared.

### Learned sparse path

```text
ALIKED
   ↓
LightGlue
   ↓
Candidate Matches
```

ALIKED produces sparse learned features and LightGlue can match those features.

Performance must be measured on lunar imagery rather than assumed from results on other domains.

### Detector-free path

```text
Source Representation
        +
Reference Representation
        ↓
      LoFTR
        ↓
Candidate Matches
```

LoFTR can be useful when repeatable keypoints are weak or terrain is relatively low-texture.

It should not be described as a feature extractor in the same sense as SIFT or ALIKED.

### Research-oriented multimodal approaches

Methods or ideas such as:

- RIFT
- CFOG-style approaches

may be investigated for difficult cross-sensor and multimodal conditions.

They should be treated as research directions unless an implementation has actually been integrated and validated.

### Evaluation principle

Do not add every matcher to one pipeline simply because it is available.

A practical comparison is:

```text
SIFT baseline
      ↓
One stronger learned path
      ↓
Compare on identical image pairs
      ↓
Evaluate hard cases
```

The selected approach should be justified by measured results.

---

## 13. Candidate Matches vs Verified Matches

ChandraMap must distinguish between **candidate matches** and **verified inliers**.

### Candidate matches

These are correspondences proposed by a matcher.

A matcher confidence score does not prove geometric correctness.

### Verified matches

These are candidate correspondences that survive geometric verification.

The correct flow is:

```text
Candidate Matches
        ↓
RANSAC
        ↓
Geometrically Verified Inliers
```

Do not label raw matcher output as "high-confidence matches" if geometric verification has not occurred.

---

## 14. Geometric Verification

Geometric verification determines whether candidate correspondences are consistent with a plausible transformation.

### Initial model

Depending on the image pair and geometry, investigate:

- Affine transformation
- Homography
- Other appropriate local models

An affine model or homography can be a reasonable first model for an already map-projected local pair.

However:

> **The Moon is not a flat poster.**

Raw imagery may contain:

- Perspective effects
- Relief displacement
- Sensor geometry
- Viewing-angle effects
- Orthorectification differences

Therefore one global transform should not automatically be assumed sufficient.

### RANSAC flow

```text
Candidate Matches
       ↓
RANSAC
       ↓
Initial Geometric Model
       ↓
Inliers
       ↓
Residual Analysis
```

Inspect:

- Number of inliers
- Inlier ratio
- Residual magnitude
- Residual distribution
- Spatial distribution of inliers

### Residual analysis

If residuals change systematically across the image, investigate:

- Local/piecewise warping
- Sensor geometry
- DEM information
- Orthorectification
- Map projection differences

Do not use a flexible warp merely to make a visual overlay appear better.

---

## 15. Sub-Pixel Refinement

Sub-pixel refinement must occur **after geometric verification**.

The correct order is:

```text
LOCAL MATCHES
      ↓
RANSAC
      ↓
VERIFIED INLIERS
      ↓
SUB-PIXEL TIE-POINT REFINEMENT
      ↓
REFIT FINAL TRANSFORM
      ↓
REGISTERED IMAGE
```

### Why this order matters

RANSAC first identifies correspondences that are geometrically consistent.

Sub-pixel refinement should then improve the coordinates of those reliable control points.

Finally, the transformation should be estimated again from the refined points.

### Possible refinement techniques

Depending on the existing repository architecture:

- Patch correlation
- Phase-based refinement
- Local intensity optimization
- Planetary registration tools
- Other validated sub-pixel tie-point methods

Do not require a particular implementation unless it is already established by the repository.

### Error reporting

The SIH correspondence requirement should be reported in **source-image pixels first**.

Convert source-pixel error to metres only when:

- GSD is known,
- Projection is appropriate,
- Geometry supports the conversion,
- Reference truth supports the interpretation.

Do not claim sub-meter accuracy for a source sensor whose spatial information does not support that claim.

---

## 16. Metadata and Georeferencing

Metadata must flow through the pipeline.

Where available, preserve:

| Metadata                | Purpose                       |
| ----------------------- | ----------------------------- |
| Source pixel scale      | Physical error interpretation |
| GSD                     | Scale handling                |
| Footprint               | Search restriction            |
| Projection              | Geospatial transformation     |
| Latitude/longitude      | Geospatial indexing           |
| Acquisition time        | Acquisition context           |
| Viewing geometry        | Registration interpretation   |
| Illumination geometry   | Sun-angle analysis            |
| Product identity        | Reproducibility               |
| Calibration information | Radiometric interpretation    |

### Metadata flow

```text
Input Product
     ↓
Metadata Extraction
     ↓
Sensor Processing
     ↓
Representation
     ↓
Matching
     ↓
Geometric Verification
     ↓
Transformation
     ↓
Georeferenced Output
```

Preprocessing must not silently discard metadata required later.

### Coordinate interpretation

Final results may be expressed in:

- Source-image pixels
- Reference-image pixels
- Projected coordinates
- Latitude/longitude
- Physical distances

Only perform conversions justified by the available geometry and reference information.

---

## 17. Output Contract

Successful sensor integration should provide interpretable correspondence outputs.

At minimum, the pipeline should expose or make available:

- Candidate matches
- Verified matches/inliers
- Final transformation
- Registered image or preview
- Confidence information where defined
- Residuals
- Inlier count
- Inlier ratio
- Spatial coverage
- Source-pixel error
- Runtime
- Failure status

### Example conceptual output

```text
CorrespondenceResult
├── candidate_matches
├── verified_inliers
├── transformation
├── residuals
├── inlier_count
├── inlier_ratio
├── spatial_coverage
├── source_pixel_error
├── ground_error_m
├── runtime
├── registered_preview
└── status
```

The final lunar mosaic is a downstream product.

The primary correspondence deliverable remains:

```text
Matched Points
+
Transformation
+
Quality Metrics
+
Registered Output
```

---

## 18. Testing Requirements

Every new sensor requires tests at multiple levels.

### Unit tests

Test:

- Sensor identification
- Configuration parsing
- Product validation
- Loader
- Metadata extraction
- Metadata preservation
- Band handling
- No-data handling
- Preprocessing
- Representation conversion
- Scale handling
- Capability declaration

### Integration tests

At minimum test:

```text
Sensor Input
    ↓
Loader
    ↓
Preprocessing
    ↓
Representation
    ↓
Matching
    ↓
Geometric Verification
```

A complete end-to-end integration test should also cover:

```text
Sensor Input
    ↓
Registration Pipeline
    ↓
Output Contract
```

### Regression tests

Adding a sensor must not change existing sensor behavior unexpectedly.

Run regression tests for:

- OHRC
- TMC-2
- IIRS
- Existing reference-image paths
- Existing configuration paths

where applicable.

### Failure tests

Test at minimum:

- Missing metadata
- Unsupported product
- Malformed image
- Invalid dimensions
- Unsupported band structure
- Missing geolocation
- Missing projection
- Extreme scale difference
- Low-feature image
- Illumination mismatch
- Invalid sensor identifier
- Corrupted input
- Unsupported product variant

A failure should be explicit and diagnosable.

---

## 19. Sensor-Specific Benchmarking

Every new sensor must have measurable benchmark evidence.

Do not treat a visually convincing overlay as sufficient evidence.

### Recommended metrics

| Metric               | Purpose                                       |
| -------------------- | --------------------------------------------- |
| Recall@1             | Retrieval success at Top-1                    |
| Recall@5             | Retrieval success within Top-5                |
| Inlier count         | Number of geometrically valid correspondences |
| Inlier ratio         | Fraction of candidates surviving verification |
| Grid coverage        | Distribution of good matches                  |
| Convex-hull coverage | Spatial extent of valid matches               |
| Check-point RMSE     | Registration accuracy on independent points   |
| Ground error         | Physical error where meaningful               |
| Runtime              | Computational cost                            |
| Failure rate         | Robustness                                    |

### Independent evaluation

Do not estimate a transformation and evaluate it only on the same points used to fit it.

Bad:

```text
Matched Points
      ↓
Fit Transform
      ↓
Evaluate Same Points
```

Prefer:

```text
Candidate / Control Points
      ↓
Fit Transform
      ↓
Independent Check Points
      ↓
Evaluate
```

Use:

- Challenge ground truth where available
- Independently checked tie points
- Held-out check points

### Spatial distribution

A large number of matches concentrated in one small crater does not necessarily indicate good registration over the whole overlap.

Measure spatial coverage.

---

## 20. Required Stress Tests

Every sensor addition should be evaluated against a controlled stress matrix.

### Easy pair

Characteristics:

- Known overlap
- Similar illumination
- Moderate scale difference
- Adequate terrain structure

Purpose:

Verify that the basic sensor integration works.

### Sun-angle stress

Same region with substantially different illumination.

Test:

```text
Similar Sun angle
        vs
Different Sun angle
```

Report the performance change.

### Scale stress

Use pairs with large GSD differences.

Verify that:

- The reference pyramid is appropriate.
- Coarse search occurs at comparable scales.
- Upsampling is not treated as resolution recovery.

### Modality stress

For example:

```text
IIRS-derived 2D representation
            ↕
Visible reference imagery
```

Measure the effect of the representation choice.

### Geometry stress

Use:

- Relief-rich terrain
- Stronger viewpoint differences
- Challenging map-projection conditions

Inspect residual structure.

### Low-feature stress

Use smooth or repetitive terrain where false matches are more likely.

### Required reporting

Results should be reported separately by sensor.

Do not hide sensor-specific failures inside one mixed aggregate score.

---

## 21. Benchmark Baseline

A new sensor should be evaluated against an established baseline where applicable.

Use the same image pairs and evaluation protocol.

A practical comparison is:

```text
1. SIFT baseline
        ↓
2. Stronger matcher alone
        ↓
3. Full sensor-aware + multi-scale pipeline
```

The purpose is to determine which architectural change produces the measured improvement.

### Keep the comparison controlled

Do not change all of the following simultaneously:

- Sensor preprocessing
- Matcher
- Reference scale
- Evaluation data
- Geometry model
- Ground truth

and then attribute the entire improvement to one change.

Where possible, change one major architectural component at a time.

---

## 22. Documentation Updates Required

Adding a sensor requires documentation updates appropriate to the implementation.

Potential documentation includes:

- Sensor documentation
- Architecture documentation
- Supported sensors list
- Data documentation
- Benchmark documentation
- Configuration documentation
- README
- Changelog
- Roadmap
- API/reference documentation
- Dataset documentation
- Experiment documentation

### Documentation rule

Do not document a sensor as fully supported if only:

- The loader exists
- A preview works
- An experimental matcher runs

Instead, clearly distinguish implementation status.

For example:

```text
Experimental
Partial Support
Supported
Production-Ready
```

The status should correspond to actual evidence.

---

## 23. Repository Changes Checklist

Use this checklist when implementing a new sensor:

- [ ] Sensor identifier added
- [ ] Mission identified
- [ ] Modality documented
- [ ] Product types documented
- [ ] Sensor metadata contract defined
- [ ] Loader implemented
- [ ] Input validation implemented
- [ ] Metadata extraction implemented
- [ ] Metadata preservation verified
- [ ] Sensor-specific preprocessing implemented
- [ ] Registration representation defined
- [ ] Scale strategy defined
- [ ] Illumination strategy evaluated
- [ ] Geospatial metadata preserved
- [ ] Sensor routing implemented
- [ ] Matching integration implemented
- [ ] Candidate matches distinguished from verified inliers
- [ ] RANSAC/geometric verification tested
- [ ] Residual analysis implemented or evaluated
- [ ] Sub-pixel refinement tested where applicable
- [ ] Final transformation generation verified
- [ ] Output contract satisfied
- [ ] Unit tests added
- [ ] Integration tests added
- [ ] Failure tests added
- [ ] Regression tests passed
- [ ] Benchmark pairs added
- [ ] Stress tests executed
- [ ] Independent evaluation performed
- [ ] Documentation updated
- [ ] Known limitations documented
- [ ] Existing sensor behavior verified
- [ ] No undocumented sensor-specific assumptions introduced

---

## 24. Pull Request Requirements

A sensor-support pull request should contain enough information for another contributor to reproduce and evaluate the integration.

### Required PR information

Include:

- Sensor name
- Mission
- Modality
- Supported products
- Input format
- Sample input description
- Metadata availability
- GSD/pixel scale
- Preprocessing description
- Registration representation
- Scale strategy
- Illumination handling
- Geospatial handling
- Matching path
- Geometric verification approach
- Sub-pixel refinement approach where applicable
- Benchmark dataset/pairs
- Benchmark results
- Stress-test results
- Known limitations
- Unit tests
- Integration tests
- Documentation changes

### Evidence requirements

Avoid unsupported statements such as:

```text
"Works perfectly."
"Fully invariant."
"Sub-pixel accurate."
"Handles all illumination conditions."
"Works at every scale."
```

Replace them with measurable evidence:

```text
Check-point RMSE:
Inlier ratio:
Spatial coverage:
Runtime:
Failure rate:
Test conditions:
Known limitations:
```

---

## 25. Acceptance Criteria

A sensor should **not** be considered fully supported merely because:

- Its image loads.
- A preview is generated.
- A matcher returns points.
- An overlay looks visually aligned.

Full sensor support requires evidence across the complete integration path.

### Required criteria

A sensor should satisfy:

1. Input validation works.
2. Metadata is correctly extracted and preserved.
3. Sensor-specific preprocessing works.
4. A documented registration representation exists.
5. Scale handling is physically meaningful.
6. Sensor routing integrates with the existing pipeline.
7. Matching integrates with the existing pipeline.
8. Geometric verification is performed.
9. Candidate matches are distinguished from verified inliers.
10. Sub-pixel refinement is performed where applicable.
11. Outputs satisfy the correspondence contract.
12. Independent evaluation is available.
13. Stress tests are documented.
14. Failure cases are documented.
15. Existing sensors do not regress.
16. Known limitations are explicitly documented.

### Support status

Use the following status categories:

| Status               | Meaning                                                                                                         |
| -------------------- | --------------------------------------------------------------------------------------------------------------- |
| **Experimental**     | Initial implementation exists and is being evaluated                                                            |
| **Partial Support**  | Core processing works but one or more required capabilities remain incomplete                                   |
| **Supported**        | Required integration, tests, and benchmark evidence are available                                               |
| **Production-Ready** | Supported implementation has passed the project's defined reliability, regression, and operational requirements |

Do not mark a sensor as fully supported without measurable evidence.

---

## 26. Example: Adding a Hypothetical Sensor

The following example is intentionally hypothetical.

It does not represent a real mission or real sensor specification.

Assume a hypothetical sensor called `Example-Sensor-A`.

### Step 1 — Sensor definition

```text
Sensor ID:
example_sensor_a

Mission:
example_mission

Modality:
optical

Product:
example_product
```

### Step 2 — Metadata contract

Collect:

```text
GSD
Projection
Footprint
Acquisition time
Viewing geometry
Illumination metadata
Product identifier
```

Unknown values remain unknown.

### Step 3 — Loader

```text
Example Product
      ↓
Format Validation
      ↓
Image Extraction
      ↓
Metadata Extraction
      ↓
Internal Sensor Representation
```

### Step 4 — Sensor-specific preprocessing

```text
Input
 ↓
No-data handling
 ↓
Calibration / normalization
 ↓
Noise handling
 ↓
Geometry preparation
 ↓
Structural representation
```

### Step 5 — Scale strategy

Determine:

```text
Source GSD
Reference GSD
       ↓
Comparable reference pyramid level
       ↓
Coarse search
```

Do not resize the source and interpret the new pixel count as recovered physical detail.

### Step 6 — Matching

Use an established baseline:

```text
Structural Representation
        ↓
SIFT
        ↓
Candidate Matches
```

Then optionally evaluate another matching path.

### Step 7 — Verification

```text
Candidate Matches
       ↓
RANSAC
       ↓
Verified Inliers
       ↓
Sub-Pixel Refinement
       ↓
Final Transform
```

### Step 8 — Evaluation

Measure:

```text
Inlier Count
Inlier Ratio
Spatial Coverage
Check-Point RMSE
Runtime
Failure Rate
```

The sensor should only receive a stronger support status after these results and their limitations are documented.

---

## 27. Common Mistakes

### 27.1 Treating every sensor as identical

Incorrect:

```text
All sensors
    ↓
Same preprocessing
    ↓
Same representation
    ↓
Same matching assumptions
```

Correct:

```text
Sensor A ──→ Sensor-specific preparation ──┐
Sensor B ──→ Sensor-specific preparation ──┼→ Common representation
Sensor C ──→ Sensor-specific preparation ──┘
```

---

### 27.2 Resizing instead of handling GSD

Incorrect:

```text
Low-resolution image
      ↓
Upsample
      ↓
Assume high-resolution information
```

Upsampling changes pixel count, not physical information.

---

### 27.3 Claiming recovered detail

A resized IIRS image does not suddenly contain the fine terrain detail present in NAC or OHRC.

Do not claim fine or sub-meter correspondence merely because the image has been resized.

---

### 27.4 Ignoring illumination geometry

Histogram normalization cannot recreate a shadow that moved because the Sun angle changed.

Test structure-aware representations instead.

---

### 27.5 Discarding metadata

Do not convert the product to an image array and discard:

- GSD
- Projection
- Footprint
- Acquisition information
- Viewing geometry
- Illumination geometry

Metadata may be essential to search, scaling, evaluation, and georeferencing.

---

### 27.6 Treating IIRS like a normal camera

IIRS is spectral data.

Do not automatically treat its full hyperspectral cube as a conventional 2D camera image.

Start by evaluating an appropriate 2D representation.

---

### 27.7 Using matcher confidence as geometric proof

A high matcher confidence does not establish that a correspondence is geometrically correct.

Use:

```text
Candidate Matches
      ↓
RANSAC
      ↓
Verified Inliers
```

---

### 27.8 Fitting and evaluating on the same points

This can make registration accuracy appear better than its independent performance.

Use held-out check points or independent ground truth.

---

### 27.9 Adding advanced matchers before establishing a baseline

A complicated pipeline without a baseline makes it difficult to identify what actually improved performance.

Start with a measurable baseline.

---

### 27.10 Reporting only visually attractive overlays

A visually convincing overlay does not establish:

- Accurate correspondence
- Good spatial coverage
- Low independent error
- Correct geometry

Report numerical metrics.

---

### 27.11 Hiding sensor-specific failures

Do not combine all sensors into one aggregate metric if one sensor fails systematically.

Report results per sensor.

---

### 27.12 Changing the common pipeline unnecessarily

Adding a sensor should normally extend the sensor integration layer.

Do not rewrite the entire registration architecture unless benchmark evidence demonstrates that an architectural change is necessary.

---

### 27.13 Duplicating existing infrastructure

If ChandraMap already has:

- A loader abstraction
- Sensor registry
- Metadata schema
- Preprocessing interface
- Matcher interface
- Benchmark framework

extend those components instead of creating duplicate versions.

---

### 27.14 Adding undocumented assumptions

Examples:

```text
"All products use the same projection."
"All sensors have grayscale data."
"All images can use the same normalization."
"All sensors have reliable geolocation."
"All scenes support one global homography."
```

These assumptions must not be introduced without evidence.

---

## 28. Design Principles

The following principles should guide every sensor integration.

### 1. Sensor-aware by design

Different instruments produce different information.

### 2. Preserve physical meaning

Do not create claims from transformations that only change image representation.

### 3. Preserve metadata

Metadata can be as important as the pixel array.

### 4. Compare information, not pixel counts

Physical scale matters more than resized dimensions.

### 5. Use common structural representations only after sensor-specific preparation

The common pipeline comes after sensor-specific handling.

### 6. Separate global retrieval from local matching

Global retrieval finds the region.

Local matching establishes detailed correspondence.

### 7. Separate candidate matches from verified inliers

Matcher output is not geometric proof.

### 8. Verify geometry before refinement

The correct order is:

```text
Matches
→ RANSAC
→ Inliers
→ Sub-Pixel Refinement
→ Final Transform
```

### 9. Evaluate using independent check points

Do not judge a transformation only on the points used to estimate it.

### 10. Measure failures, not only successes

A robust system must expose difficult cases.

### 11. Add sensors incrementally

A small measurable integration is preferable to a large unvalidated abstraction.

### 12. Do not break existing sensor paths

New sensor support must preserve existing behavior unless a deliberate, benchmarked architectural change is required.

---

## 29. Quick Developer Checklist

Use this checklist before opening a sensor-support pull request.

### Sensor

- [ ] Sensor identity documented
- [ ] Mission documented
- [ ] Modality documented
- [ ] Sensor type documented
- [ ] GSD/pixel scale documented
- [ ] Product formats documented
- [ ] Supported products documented

### Data

- [ ] Loader implemented
- [ ] Input validation implemented
- [ ] Metadata extraction implemented
- [ ] Metadata preservation verified
- [ ] Band handling implemented
- [ ] No-data handling implemented
- [ ] Invalid-input behavior tested

### Processing

- [ ] Sensor-specific preprocessing implemented
- [ ] Registration-friendly representation defined
- [ ] Representation limitations documented
- [ ] Scale strategy defined
- [ ] Illumination strategy evaluated
- [ ] Viewing geometry considered
- [ ] Projection handled
- [ ] Geospatial metadata preserved

### Retrieval

- [ ] Geospatial restriction used when reliable metadata exists
- [ ] Global retrieval path defined when needed
- [ ] Reference pyramid level identified
- [ ] Global descriptor path tested where applicable
- [ ] Top-K retrieval evaluated where applicable

### Matching

- [ ] SIFT baseline evaluated
- [ ] Selected advanced matcher evaluated where applicable
- [ ] Candidate matches recorded
- [ ] Matcher confidence not treated as geometric proof

### Verification

- [ ] RANSAC implemented/evaluated
- [ ] Geometric model documented
- [ ] Verified inliers recorded
- [ ] Residuals inspected
- [ ] Spatial coverage measured
- [ ] Local/piecewise geometry investigated where residuals require it

### Refinement

- [ ] Verified points used for refinement
- [ ] Sub-pixel refinement evaluated where applicable
- [ ] Final transform refitted after refinement
- [ ] Source-pixel error reported

### Evaluation

- [ ] Independent check points available
- [ ] Check-point RMSE measured
- [ ] Inlier count measured
- [ ] Inlier ratio measured
- [ ] Spatial coverage measured
- [ ] Ground error measured where meaningful
- [ ] Runtime measured
- [ ] Failure rate measured

### Stress Testing

- [ ] Easy pair tested
- [ ] Sun-angle stress tested
- [ ] Scale stress tested
- [ ] Modality stress tested
- [ ] Geometry stress tested
- [ ] Low-feature stress tested
- [ ] Sensor-specific failures documented

### Regression

- [ ] Existing sensor tests pass
- [ ] Existing pipelines are unaffected
- [ ] Existing configuration remains valid
- [ ] No duplicate infrastructure introduced

### Documentation

- [ ] Sensor documentation updated
- [ ] Supported-sensors documentation updated
- [ ] Architecture documentation updated where necessary
- [ ] Benchmark documentation updated
- [ ] Configuration documentation updated
- [ ] README updated where appropriate
- [ ] Changelog updated
- [ ] Known limitations documented
- [ ] Support status documented

### Pull Request

- [ ] Sensor identity included
- [ ] Product information included
- [ ] Metadata behavior described
- [ ] Preprocessing described
- [ ] Representation described
- [ ] Scale strategy described
- [ ] Matching path described
- [ ] Verification strategy described
- [ ] Benchmark results included
- [ ] Stress-test results included
- [ ] Tests included
- [ ] Reproducible evidence provided

---

## Final Rule

A new sensor is successfully integrated into ChandraMap when it can move through the existing architecture without pretending that its physical characteristics are equivalent to another sensor:

```text
Sensor Product
      ↓
Sensor Identification
      ↓
Metadata + Geometry
      ↓
Sensor-Specific Preprocessing
      ↓
Registration-Friendly Representation
      ↓
Physically Meaningful Multi-Scale Handling
      ↓
Global Retrieval (if required)
      ↓
Local Matching
      ↓
Candidate Matches
      ↓
RANSAC / Geometric Verification
      ↓
Verified Inliers
      ↓
Sub-Pixel Refinement
      ↓
Final Transformation
      ↓
Independent Evaluation
      ↓
Registered Output
```

The standard for sensor support is therefore not:

> **"Can ChandraMap open this sensor's image?"**

It is:

> **"Can ChandraMap use this sensor's information correctly, preserve its physical and geospatial meaning, establish measurable correspondence, verify the geometry, evaluate the result independently, and do so without breaking existing sensor paths?"**
